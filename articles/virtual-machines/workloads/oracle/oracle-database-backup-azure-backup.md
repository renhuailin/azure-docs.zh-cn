---
title: 使用 Azure 备份在 Azure Linux VM 上备份和恢复 Oracle Database 19c 数据库
description: 了解如何使用 Azure 备份服务来备份和恢复 Oracle Database 19c 数据库。
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 90f86a198ad36c2961f77336092d863953ee45ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673890"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>使用 Azure 备份在 Azure Linux VM 上备份和恢复 Oracle Database 19c 数据库

本文展示了如何使用 Azure 备份来创建 VM 磁盘的磁盘快照，其中包括数据库文件和快速恢复区域。 使用 Azure 备份时，可以创建适合作为备份的完整磁盘快照，这些快照存储在[恢复服务保管库](../../../backup/backup-azure-recovery-services-vault-overview.md)中。  Azure 备份还提供了应用程序一致性备份，确保还原数据不需要额外的修复。 还原应用程序一致型数据可减少还原时间，因此可快速恢复到运行状态。

> [!div class="checklist"]
>
> * 使用应用程序一致性备份来备份数据库
> * 从恢复点还原和恢复数据库
> * 从恢复点还原 VM

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

若要执行备份和恢复过程，必须先创建已安装 Oracle Database 19c 实例的 Linux VM。 当前用于创建 VM 的市场映像为 Oracle:oracle-database-19-3:oracle-database-19-0904:latest。 请按照 [Oracle 创建数据库快速入门](./oracle-database-quick-create.md)中的步骤操作，以创建 Oracle 数据库来完成本教程。


## <a name="prepare-the-environment"></a>准备环境

若要准备环境，请完成以下步骤：

1. 连接到 VM。
1. 准备数据库。

### <a name="connect-to-the-vm"></a>连接到 VM

1. 若要与 VM 建立安全外壳 (SSH) 会话，请使用以下命令。 将 IP 地址和主机名组合替换为 VM 的 `<publicIpAddress>` 值。
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. 切换到根用户：

   ```bash
   sudo su -
   ```
    
1. 将 oracle 用户添加到 /etc/sudoers 文件中：

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>准备数据库

为了更好地执行此步骤，请确保你已有在名为“vmoracle19c”的 VM 上运行的 Oracle 实例（“test”）。

1. 切换到 oracle 用户：
 
   ```bash
    sudo su - oracle
    ```
    
2. 在连接之前，需要设置环境变量 ORACLE_SID：
    
    ```bash
    export ORACLE_SID=test;
    ```

    你还应使用以下命令将 ORACLE_SID 变量添加到 `oracle` 用户 `.bashrc` 文件以供将来登录：

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. 启动 Oracle 侦听器（如果它还没有运行的话）：

    ```output
    $ lsnrctl start
    ```

    输出应类似于以下示例：

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

4.  创建快速恢复区域 (FRA) 位置：

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  连接到数据库：

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  启动数据库（如果它还没有运行的话）：

    ```bash
    SQL> startup
    ```

7.  设置快速恢复区域的数据库环境变量：

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  确保数据库处于存档日志模式，以启用联机备份。

    首先，检查日志存档状态：

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    如果处于 NOARCHIVELOG 模式，请运行以下命令：

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  创建一个表来测试备份和还原操作：

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. 将 RMAN 配置为备份到 VM 磁盘上的快速恢复区域：

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. 确认配置更改详细信息：

    ```bash
    RMAN> show all;
    ```    

12.  现在，运行备份。 下面的命令将创建完整数据库备份（包括存档日志文件）作为压缩格式的备份集：

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup-preview"></a>使用 Azure 备份（预览）

Azure 备份服务提供简单、安全且经济高效的解决方案来备份数据，并从 Microsoft Azure 云恢复数据。 Azure 备份提供独立且隔离的备份，可以防范原始数据的意外破坏。 备份存储在提供恢复点内置管理的恢复服务保管库中。 配置和可伸缩性很简单，备份经过优化，可以轻松地根据需要还原。

Azure 备份服务为 Oracle、MySQL、Mongo DB 和 PostGreSQL 等多种应用程序提供了[框架](../../../backup/backup-azure-linux-app-consistent.md)来实现 Windows 和 Linux VM 备份期间的应用程序一致性。 这包括在创建磁盘快照之前调用前脚本（以静止应用程序），并在快照完成后调用后脚本（用于取消冻结应用程序的命令），以使应用程序恢复到正常模式。 虽然 GitHub 上提供了示例前脚本和后脚本，但这些脚本的创建和维护仍由你负责。

现在，Azure 备份提供了增强的前脚本和后脚本框架（目前处于预览阶段），即 Azure 备份服务为选定的应用程序提供打包的前脚本和后脚本。 Azure 备份用户只需要对应用程序进行命名，Azure VM 备份就会自动调用相关的前脚本和后脚本。 打包的前脚本和后脚本将由 Azure 备份团队维护，这样就可以向用户保证这些脚本的支持、所有权和有效性。 目前，增强型框架支持的应用程序为 Oracle 和 MySQL。

在此部分中，你将使用 Azure 备份增强型框架为正在运行的 VM 和 Oracle 数据库创建应用程序一致性快照。 当 Azure 备份创建 VM 磁盘的快照时，数据库被置于备份模式，以便进行事务一致性联机备份。 此快照是存储的完整副本，而不是增量快照或写入时复制快照，因此它是还原数据库的有效介质。 使用 Azure 备份应用程序一致性快照的优点是，无论数据库多大，都可以极快速地创建快照，且快照在创建后就可用于还原操作，而无需等待被传输到恢复服务保管库中。

若要使用 Azure 备份来备份数据库，请完成以下步骤：

1. 为应用程序一致性备份准备环境。
1. 设置应用程序一致性备份。
1. 触发 VM 的应用程序一致性备份。

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>为应用程序一致性备份准备环境

1. 切换到根用户：

   ```bash
   sudo su -
   ```

1. 新建备份用户：

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. 创建备份用户环境：

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. 为新的备份用户设置外部身份验证。 备份用户需要能够使用外部身份验证来访问数据库，以免受到密码质询。

   首先，切换回 oracle 用户：

   ```bash
   su - oracle
   ```

   使用 sqlplus 来登录数据库，并检查外部身份验证的默认设置：
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   输出应如下面的示例所示： 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   现在，创建在外部进行身份验证的数据库用户 azbackup，并授予 sysbackup 特权：
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > 如果在运行 `GRANT` 语句时看到错误 `ORA-46953: The password file is not in the 12.2 format.`，请按以下步骤操作，将 orapwd 文件迁移到 12.2 格式：
   >
   > 1. 退出 sqlplus。
   > 1. 将旧格式的密码文件迁移到新名称。
   > 1. 迁移密码文件。
   > 1. 删除旧文件。
   > 1. 运行下面的命令：
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. 在 sqlplus 中重新运行 `GRANT` 操作。
   >
   
4. 创建存储过程，以将备份消息记录到数据库警报日志中：

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>设置应用程序一致性备份  

1. 切换到根用户：

   ```bash
   sudo su -
   ```

2. 检查是否有“etc/azure”文件夹。 如果没有，请创建应用程序一致性备份工作目录：

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. 在此文件夹中，检查是否有“workload.conf”。 如果没有，请在 /etc/azure 目录中创建包含以下内容的文件 workload.conf，这些内容必须以 `[workload]` 开头。 如果文件已存在，则只需编辑字段，使其与以下内容匹配即可。 否则，下面的命令将创建此文件并填充内容：

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>触发 VM 的应用程序一致性备份

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  在 Azure 门户中，转到资源组 rg-oracle，然后单击虚拟机 vmoracle19c。

2.  在“备份”边栏选项卡上，在资源组 rg-oracle 中新建名为 myVault 的恢复服务保管库。
    对于“选择备份策略”，选择“(新) DailyPolicy”。 若要更改备份频率或保持期，请改为选择“新建策略”。

    ![恢复服务保管库“添加”页](./media/oracle-backup-recovery/recovery-service-01.png)

3.  若要继续操作，请单击“启用备份”。

    > [!IMPORTANT]
    > 在单击“启用备份”后，备份过程将不会在计划时间到期前启动。 若要设置即时备份，请完成下一步。

4. 在资源组页上，单击新创建的恢复服务保管库 myVault。 提示：可能需要刷新页面才能看到它。

5.  在“myVault - 备份项”边栏选项卡中，在“备份项计数”下选择备份项计数。

    ![恢复服务保管库“myVault 详细信息”页](./media/oracle-backup-recovery/recovery-service-02.png)

6.  在“备份项(Azure 虚拟机)”边栏选项卡中，单击页面右侧的省略号 (...) 按钮，然后单击“立即备份”。

    ![恢复服务保管库“立即备份”命令](./media/oracle-backup-recovery/recovery-service-03.png)

7.  接受默认的“保留备份截止日期”值，然后单击“确定”按钮。 等待备份过程结束。 

    若要查看备份作业的状态，请单击“备份作业”。

    ![恢复服务保管库“作业”页](./media/oracle-backup-recovery/recovery-service-04.png)

    下图中显示了备份作业的状态：

    ![显示有状态的恢复服务保管库作业页](./media/oracle-backup-recovery/recovery-service-05.png)
    
    请注意，虽然执行快照只需要几秒钟，但将它传输到存储库可能需要一些时间，并且备份作业在传输完成之前不会完成。

8. 对于应用程序一致性备份，请解决日志文件中的所有错误。 日志文件位于 /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 创建恢复服务保管库：

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. 为 VM 启用备份保护：

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. 触发立即运行备份，而不是等待备份按默认计划（凌晨 5 点 UTC）触发： 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   可以使用 `az backup job list` 和 `az backup job show` 来监视备份作业的进度。

---

## <a name="recovery"></a>恢复

若要恢复数据库，请完成以下步骤：

1. 删除数据库文件。
1. 从恢复服务保管库生成还原脚本。
1. 装载还原点。
1. 执行恢复。

### <a name="remove-the-database-files"></a>删除数据库文件 

本文的稍后部分中将介绍如何测试恢复过程。 能够测试恢复过程之前，需要删除数据库文件。

1.  关闭 Oracle 实例：

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  删除数据文件和备份：

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>从恢复服务保管库生成还原脚本

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 Azure 门户中，搜索并选择“myVault”恢复服务保管库项。

    ![恢复服务保管库 myVault 备份项](./media/oracle-backup-recovery/recovery-service-06.png)

2. 在“概述”边栏选项卡上，依次选择“备份项”和“Azure 虚拟机”，其中应列出非零的“备份项计数”。

    ![恢复服务保管库 Azure 虚拟机备份项计数](./media/oracle-backup-recovery/recovery-service-07.png)

3. 在“备份项”（“Azure 虚拟机”）页上，你的 VM vmoracle19c 列出。 单击右边的省略号，以打开菜单并选择“文件恢复”。

    ![恢复服务保管库“文件恢复”页的屏幕快照](./media/oracle-backup-recovery/recovery-service-08.png)

4. 在“文件恢复(预览)”窗格中，单击“下载脚本”。 然后，将下载的文件 (.py) 保存到客户端计算机上的文件夹中。 为运行脚本而生成密码。 将密码复制到文件中，以供稍后使用。 

    ![下载脚本文件保存选项](./media/oracle-backup-recovery/recovery-service-09.png)

5. 将 .py 文件复制到 VM 中。

    以下示例演示如何使用安全复制 (scp) 命令将文件移动到 VM。 还可先将内容复制到剪贴板，再粘贴到 VM 上设置的新文件中。

    > [!IMPORTANT]
    > 在以下示例中，请确保更新 IP 地址和文件夹值。 这些值必须映射到保存文件的文件夹。
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要列出 VM 的恢复点，请使用 az backup recoverypoint list。 在本示例中，我们为在 myRecoveryServicesVault 中受保护的名为 myVM 的 VM 选择最近的恢复点：

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

若要获取将恢复点连接或装载到 VM 的脚本，请使用 az backup restore files mount-rp 命令。 下面的示例可为在 myRecoveryServicesVault 中受保护的名为 myVM 的 VM 获取脚本。

将 myRecoveryPointName 替换为你在前一个命令中获取的恢复点的名称：

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

下载该脚本并显示密码，如下面的示例中所示：

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

将 .py 文件复制到 VM 中。

以下示例演示如何使用安全复制 (scp) 命令将文件移动到 VM。 还可先将内容复制到剪贴板，再粘贴到 VM 上设置的新文件中。

> [!IMPORTANT]
> 在以下示例中，请确保更新 IP 地址和文件夹值。 这些值必须映射到保存文件的文件夹。
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>装载还原点

1. 创建还原装入点，并将脚本复制到其中。

    在下面的示例中，创建 /restore 目录，以便将快照装载到其中，将文件移动到此目录中，然后将文件更改为被根用户所有的可执行文件。

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    现在，执行脚本来还原备份。 系统会要求你提供在 Azure 门户中生成的密码。 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    以下示例显示运行上一脚本后会看到什么。 当系统提示继续时，请输入“Y”。

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. 确认访问已安装的卷。

    若要退出，请输入“q”，然后搜索已安装的卷。 若要创建已添加卷的列表，请在命令提示符处输入“df -h”。
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>执行恢复

1. 将缺失的备份文件复制回快速恢复区域：

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. 下面的命令使用 RMAN 来还原缺失的数据文件并恢复数据库：

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. 检查数据库内容是否已完全恢复：

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. 卸载还原点。

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    在 Azure 门户中的“文件恢复(预览)”边栏选项卡上，单击“卸载磁盘”。

    ![卸载磁盘命令](./media/oracle-backup-recovery/recovery-service-10.png)
    
    也可以通过使用 -clean 选项再次运行 python 脚本，以卸载恢复卷。

## <a name="restore-the-entire-vm"></a>还原整个 VM

可以还原整个 VM，而不是从恢复服务保管库还原已删除文件。

若要还原整个 VM，请完成以下步骤：

1. 停止并删除 vmoracle19c。
1. 恢复 VM。
1. 设置公共 IP 地址。
1. 连接到 VM。
1. 启动数据库来装载阶段并执行恢复。

### <a name="stop-and-delete-vmoracle19c"></a>停止并删除 vmoracle19c

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 Azure 门户中，转到 vmoracle19c 虚拟机，然后选择“停止”。

1. 当虚拟机不再运行时，依次选择“删除”和“是”。

   ![保管库“删除”命令](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 停止并解除分配 VM：

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. 删除 VM。 出现提示时输入“Y”：

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>恢复 VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 Azure 门户中创建用于暂存的存储帐户。

   1. 在 Azure 门户中，选择“+ 创建资源”，然后搜索并选择“存储帐户”。
    
      ![展示了资源创建位置的屏幕截图。](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. 在“创建存储帐户”页中，选择现有的资源组 rg-oracle，将存储帐户命名为 oracrestore，然后选择“存储 V2 (generalpurpose V2)”作为“帐户类型”。 将“复制”更改为“本地冗余存储(LRS)”，并将“性能”设置为“标准”。 请确保“位置”被设置为与资源组中的其他所有资源相同的区域。 
    
      ![存储帐户添加页面](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. 依次单击“查看 + 创建”、“创建”。

2. 在 Azure 门户中，搜索并单击“myVault”恢复服务保管库项。

    ![恢复服务保管库 myVault 备份项](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  在“概述”边栏选项卡上，依次选择“备份项”和“Azure 虚拟机”，其中应列出非零的“备份项计数”。

    ![恢复服务保管库 Azure 虚拟机备份项计数](./media/oracle-backup-recovery/recovery-service-07.png)

4.  在“备份项”（“Azure 虚拟机”）页上，你的 VM vmoracle19c 列出。 单击 VM 名称。

    ![恢复 VM 页](./media/oracle-backup-recovery/recover-vm-02.png)

5.  在“vmoracle19c”边栏选项卡上，选择“一致性”类型为“应用程序一致性”的还原点，然后单击右边的省略号 (...) 来打开菜单。  在菜单中，单击“还原 VM”。

    ![还原 VM 命令](./media/oracle-backup-recovery/recover-vm-03.png)

6.  在“还原虚拟机”边栏选项卡中，依次选择“新建”和“新建虚拟机”。 输入虚拟机名称“vmoracle19c”，并选择 VNet“vmoracle19cVNET”，子网会根据你选择的 VNet 自动填充。 “还原 VM”进程要求 Azure 存储帐户在同一个资源组和区域中。 可以选择之前创建的存储帐户“orarestore”。

    ![还原配置值](./media/oracle-backup-recovery/recover-vm-04.png)

7.  要还原 VM，请单击“还原”按钮。

8.  若要查看还原过程的状态，请依次单击“作业”和“备份作业”。

    ![备份作业状态命令](./media/oracle-backup-recovery/recover-vm-05.png)

    单击处于“正在进行”状态的还原操作，以显示还原进程的状态：

    ![还原过程的状态](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要创建存储帐户和文件共享，请在 Azure CLI 中运行以下命令。

1. 在 VM 所在的资源组和位置中创建存储帐户：

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. 检索可用恢复点的列表。 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. 将恢复点还原到存储帐户。 将 `<myRecoveryPointName>` 替换为在上一步中生成的列表中的恢复点：

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. 检索还原作业详细信息。 下面的命令获取已触发的还原作业的更多详细信息，包括检索模板 URI 所需的作业名称。 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   输出类似于此 `(Note down the name of the restore job)`：

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. 检索用于重新创建 VM 的 URI 的详细信息。 将 `<RestoreJobName>` 替换为上一步中的还原作业名称。

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   输出如下所示：

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   列出了模板 Blob URI 末尾的模板名称（在此示例中为 `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json`）和 Blob 容器名称（在此示例中为 `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2`）。 

   在下面的命令中使用这些值来分配变量，为创建 VM 做准备。 为存储容器生成持续时间为 30 分钟的 SAS 密钥。  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   现在，部署模板来创建 VM。

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   系统会提示你提供 VM 名称。

---

### <a name="set-the-public-ip-address"></a>设置公共 IP 地址

在 VM 恢复后，应将原始 IP 地址重新分配给新 VM。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  在 Azure 门户中，转到虚拟机 vmoracle19c。 你会注意到，它已经被分配了新的公共 IP 和 NIC（类似于 vmoracle19c-nic-XXXXXXXXXXXX），但没有 DNS 地址。 在原始 VM 被删除后，它的公共 IP 和 NIC 被保留，后续步骤会将它们重新附加到新 VM。

    ![公共 IP 地址列表](./media/oracle-backup-recovery/create-ip-01.png)

2.  停止 VM

    ![创建 IP 地址](./media/oracle-backup-recovery/create-ip-02.png)

3.  转到“网络”

    ![关联 IP 地址](./media/oracle-backup-recovery/create-ip-03.png)

4.  单击“附加网络接口”，选择原始公共 IP 地址仍关联到的原始 NIC **vmoracle19cVMNic，然后单击“确定”

    ![选择资源类型和 NIC 值](./media/oracle-backup-recovery/create-ip-04.png)

5.  现在，必须拆离使用 VM 还原操作创建的 NIC，因为它被配置为主接口。 单击“拆离网络接口”，选择类似于 vmoracle19c-nic-XXXXXXXXXXXX 的新 NIC，然后单击“确定”

    ![展示了“拆离网络接口”选择位置的屏幕截图。](./media/oracle-backup-recovery/create-ip-05.png)
    
    重新创建的 VM 现在有与原始 IP 地址和网络安全组规则关联的原始 NIC
    
    ![IP 地址值](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  返回到“概述”，然后单击“开始” 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 停止并解除分配 VM：

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. 列出还原生成的当前 VM NIC

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   输出如下所示，其中将还原生成的 NIC 名称列为 `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. 附加名为 `<VMName>VMNic`（在此示例中为 `vmoracle19cVMNic`）的原始 NIC。 原始公共 IP 地址仍附加到此 NIC，并将在重新附加 NIC 时还原到 VM。 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. 拆离还原生成的 NIC

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. 启动 VM：

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>连接到 VM

要连接到 VM，请使用以下脚本：

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>启动数据库来装载阶段并执行恢复

1. 你可能会发现实例正在运行，因为自动启动尝试在 VM 启动后启动数据库。 但是，数据库需要恢复，而且很可能只处于装载阶段，因此先运行预备关机。

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. 检查数据库内容是否已恢复：

    ```bash
    SQL> select * from scott.scott_table;
    ```

现已完成在 Azure Linux VM 上备份和恢复 Oracle Database 19c 数据库。

## <a name="delete-the-vm"></a>删除 VM

如果不再需要 VM，可以使用以下命令删除资源组、VM 和所有相关的资源：

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>后续步骤

[教程：创建具有高可用性的 VM](../../linux/create-cli-complete.md)

[浏览 VM 部署 Azure CLI 示例](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)