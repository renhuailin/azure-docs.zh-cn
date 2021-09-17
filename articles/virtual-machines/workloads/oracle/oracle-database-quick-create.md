---
title: 在 Azure VM 上创建 Oracle 数据库 | Microsoft Docs
description: 在 Azure 环境中快速创建并运行 Oracle Database 12c 数据库。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: 2854cb45f83e46fc556f57247ab4aa28bd7f74c0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696908"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>在 Azure VM 上创建 Oracle 数据库

适用于：:heavy_check_mark: Linux VM 

本指南详述了如何使用 Azure CLI 通过从 [Oracle 市场库映像](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview)部署 Azure 虚拟机来创建 Oracle 19c 数据库。 部署服务器后，若要配置 Oracle 数据库，请先通过 SSH 进行连接。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 eastus 位置创建名为 rg-oracle 的资源组 。

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>创建虚拟机

若要创建虚拟机 (VM)，请使用 [az vm create](/cli/azure/vm) 命令。 

以下示例创建一个名为 `vmoracle19c` 的 VM。 此外，它还在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息。 请记下 `publicIpAddress` 的值。 到时需要使用此地址来访问 VM。

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>为 Oracle 数据文件和 FRA 创建并附加新磁盘

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 64 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>打开用于连接的端口
在此任务中，必须通过设置用于保护 VM 的 Azure 网络安全组，为数据库侦听器和 EM Express 配置一些要使用的外部终结点。 

1. 若要打开用来远程访问 Oracle 数据库的终结点，请创建一个网络安全组规则，如下所示：
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. 若要打开用来远程访问 Oracle EM Express 的终结点，请使用 az network nsg rule create 创建一个网络安全组，如下所示：
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. 如果需要，使用 az network public-ip show 获取 VM 的公共 IP 地址，如下所示：

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>准备 VM 环境

1. 连接到 VM

   若要与 VM 建立 SSH 会话，请使用以下命令。 请将 IP 地址替换为 VM 的 `publicIpAddress` 值。

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. 切换到 root 用户

   ```bash
   sudo su -
   ```

3. 检查上次创建的磁盘设备，我们将对其进行格式化以用于保存 Oracle 数据文件

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   输出将类似于下面：
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. 对该设备进行格式化。 
   以 root 用户身份在设备上分开运行 
   
   首先，创建磁盘标签：
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   然后，创建跨越整个磁盘的主分区：
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   最后，通过打印设备的元数据来检查设备的详细信息：
   ```bash
   parted /dev/sdc print
   ```
   输出应如下所示：
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. 在设备分区上创建一个文件系统

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. 创建装入点
   ```bash
   mkdir /u02
   ```

7. 装载磁盘

   ```bash
   mount /dev/sdc1 /u02
   ```

8. 更改对于装入点的权限

   ```bash
   chmod 777 /u02
   ```

9. 将装载添加到 /etc/fstab 文件。 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. 使用公共 IP 和主机名更新 /etc/hosts 文件。

    更改公共 IP 和 VM 名称以反映你的实际值：
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. 更新主机名文件
    
    使用以下命令将 VM 的域名添加到 /etc/hostname 文件中。 这假定你已在 eastus 区域中创建了资源组和 VM：
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. 打开防火墙端口
    
    由于 SELinux 对于市场映像是默认启用的，因此我们需要为数据库侦听端口 1521 和企业管理器快速端口 5502 打开防火墙以接受流量。 以 root 用户身份运行以下命令：

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>创建数据库

该 Oracle 软件已安装在市场映像中。 如下所述创建一个示例数据库。 

1.  切换到 oracle 用户：

    ```bash
    sudo su - oracle
    ```
2. 启动数据库侦听器

   ```bash
   lsnrctl start
   ```
   输出与下面类似：
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. 为 Oracle 数据文件创建一个数据目录：

   ```bash
   mkdir /u02/oradata
   ```
    

3.  运行数据库创建助手：

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname oratest1 \
       -sid oratest1 \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    创建数据库需要几分钟的时间。

    你将看到如下所示的输出：

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/oratest1.
       Database Information:
       Global Database Name:oratest1
       System Identifier(SID):oratest1
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/oratest1/oratest1.log" for further details.
    ```

4. 设置 Oracle 变量

    在连接之前，需要设置环境变量 ORACLE_SID：

    ```bash
        export ORACLE_SID=oratest1
    ```

    你还应使用以下命令将 ORACLE_SID 变量添加到 `oracle` 用户 `.bashrc` 文件以供将来登录：

    ```bash
    echo "export ORACLE_SID=oratest1" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express 连接

若要获取可以用来浏览数据库的 GUI 管理工具，请设置 Oracle EM Express。 若要连接到 Oracle EM Express，必须先在 Oracle 中设置端口。 

1. 使用 sqlplus 连接到数据库：

    ```bash
    sqlplus sys as sysdba
    ```

2. 在连接后，为 EM Express 设置端口 5502

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  从浏览器连接 EM Express。 确保浏览器与 EM Express 兼容（需要安装 Flash）： 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    可以使用 **SYS** 帐户登录，并选中“以 sysdba 身份”复选框。 使用你在安装期间设置的密码 **OraPasswd1**。 

    ![Oracle OEM Express 登录页面的屏幕截图](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>自动执行数据库启动和关闭

默认情况下，当重启 VM 时，Oracle 数据库不会自动启动。 若要将 Oracle 数据库设置为自动启动，请首先以 root 身份登录。 然后，创建并更新一些系统文件。

1. 以 root 身份登录

    ```bash
    sudo su -
    ```

2.  运行以下命令，将 `/etc/oratab` 文件中的自动启动标志从 `N` 更改为 `Y`：

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  创建名为 `/etc/init.d/dbora` 的文件并粘贴下列内容：

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  使用 *chmod* 更改对文件的权限，如下所示：

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  创建用于启动和关闭的符号链接，如下所示：

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  若要测试所做的更改，请重新启动 VM：

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>清理资源

当在 Azure 中了解完首个 Oracle 数据库，且不再需要 VM 时，可以使用 [az group delete](/cli/azure/group) 命令来删除资源组、VM 和一切相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 中通过 [Oracle 备份策略](./oracle-database-backup-strategies.md)保护数据库

了解其他 [Azure 上的 Oracle 解决方案](./oracle-overview.md)。 

尝试[安装和配置 Oracle Automated Storage Management](configure-oracle-asm.md) 教程。
