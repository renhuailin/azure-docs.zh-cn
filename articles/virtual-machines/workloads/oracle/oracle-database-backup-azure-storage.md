---
title: 使用 RMAN 和 Azure 文件存储在 Azure Linux VM 上备份 Oracle Database 19c 数据库
description: 了解如何将 Oracle Database 19c 数据库备份到 Azure 文件存储。
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: f30a7fcbc99f6a47574d101e3792d992dc2c1af8
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260050"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-files"></a>使用 Azure 文件存储在 Azure Linux VM 上备份和恢复 Oracle Database 19c 数据库

**适用于：** :heavy_check_mark: Linux VM 

本文演示如何使用 Azure 文件存储作为介质来备份和还原在 Azure VM 上运行的 Oracle 数据库。 你将使用 Oracle RMAN 将数据库备份到 Azure 文件共享，该文件共享使用 SMB 协议装载到 VM。 使用 Azure 文件存储作为备份介质非常经济高效。 但对于非常大的数据库，Azure 备份提供了更好的解决方案。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- 要执行备份和恢复过程，首先必须创建已安装 Oracle Database 19c 实例的 Linux VM。 当前用于创建 VM 的市场映像为 Oracle:oracle-database-19-3:oracle-database-19-0904:latest。 请按照 [Oracle 创建数据库快速入门](./oracle-database-quick-create.md)中的步骤操作，创建 Oracle 数据库来完成本教程。

## <a name="prepare-the-database-environment"></a>准备数据库环境

1. 若要与 VM 建立安全外壳 (SSH) 会话，请使用以下命令。 将 IP 地址和主机名组合替换为 VM 的 `publicIpAddress` 值。
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. 切换到根用户：
 
   ```bash
   sudo su -
   ```
    
3. 将 oracle 用户添加到 /etc/sudoers 文件中：

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. 为了更好地执行此步骤，请确保你已有在名为“vmoracle19c”的 VM 上运行的 Oracle 实例（“test”）。

   切换到 oracle 用户：

   ```bash
   sudo su - oracle
   ```
    
5. 在连接之前，需要设置环境变量 ORACLE_SID：
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   你还应使用以下命令将 ORACLE_SID 变量添加到 `oracle` 用户 `.bashrc` 文件以供将来登录：

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. 启动 Oracle 侦听器（如果它还没有运行的话）：
    
   ```bash
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

7.  创建快速恢复区域 (FRA) 位置：

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  连接到数据库：

    ```bash
    sqlplus / as sysdba
    ```

9.  启动数据库（如果它还没有运行的话）：

    ```bash
    SQL> startup
    ```

10. 设置快速恢复区域的数据库环境变量：

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. 确保数据库处于存档日志模式，以启用联机备份。
    首先，检查日志存档状态：

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    如果处于 NOARCHIVELOG 模式，请在 sqlplus 中运行以下命令：

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. 创建一个表来测试备份和还原操作：

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>备份到 Azure 文件存储

若要备份到 Azure 文件存储，请完成以下步骤：

1. 设置 Azure 文件存储。
1. 将 Azure 文件共享装载到 VM。
1. 备份数据库。
1. 还原并恢复数据库。

### <a name="set-up-azure-files"></a>设置 Azure 文件存储

在此步骤中，你将使用 Oracle 恢复管理器 (RMAN) 将 Oracle 数据库备份到 Azure 文件存储。 Azure 文件共享是位于云中的完全托管文件共享。 可以使用服务器消息块 (SMB) 协议或网络文件系统 (NFS) 协议来访问它们。 此步骤介绍如何创建使用 SMB 协议装载到 VM 的文件共享。 有关如何使用 NFS 进行装载的信息，请参阅[使用 NFS 3.0 协议装载 Blob 存储](../../../storage/blobs/network-file-system-protocol-support-how-to.md)。

装载 Azure 文件存储时，使用 `cache=none` 来禁用文件共享数据的缓存。 若要确保在共享中创建的文件归 oracle 用户所有，也可以设置 `uid=oracle` 和 `gid=oinstall` 选项。 

# <a name="portal"></a>[Portal](#tab/azure-portal)

首先，设置存储帐户。

1. 在 Azure 门户中，选择“+ 创建资源”，然后搜索并选择“存储帐户”*
    
    ![显示创建资源和选择存储帐户的位置的屏幕截图。](./media/oracle-backup-recovery/storage-1.png)
    
2. 在“创建存储帐户”页中，选择现有的资源组 rg-oracle，将存储帐户命名为 oracbkup1，然后选择“存储 V2 (generalpurpose v2)”作为“帐户类型” 。 将“复制”更改为“本地冗余存储(LRS)”，并将“性能”设置为“标准” 。 请确保将“位置”设置为与资源组中其他所有资源相同的区域。 
    
    ![显示在何处选择现有资源组的屏幕截图。](./media/oracle-backup-recovery/file-storage-1.png)
   
3. 单击“高级”选项卡，然后在 Azure 文件存储下，将“大文件共享”设置为“已启用” 。 依次单击“查看 + 创建”、“创建”。
    
    ![显示在何处将大文件共享设置为“已启用”的屏幕截图。](./media/oracle-backup-recovery/file-storage-2.png)
    
4. 创建存储帐户后，请前往资源并选择“文件共享”
    
    ![显示在何处选择“文件共享”的屏幕截图。](./media/oracle-backup-recovery/file-storage-3.png)
    
5. 单击“+ 文件共享”，然后在“新建文件共享”边栏选项卡中，将文件共享命名为“orabkup1” 。 将“配额”设置为 10240 GiB，并勾选“经优化的事务”作为层  。 配额反映文件共享可以增长到的上限。 由于我们使用的是标准存储，未预配 PAYG 和资源，因此将其设置为 10 TiB 不会产生超出你使用范围的成本。 如果备份策略需要更多的存储空间，则必须将配额设置为适当的级别以保存所有备份。   填写完“新建文件共享”边栏选项卡后，单击“创建”。
    
    ![显示在何处添加新文件共享的屏幕截图。](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. 创建后，在“文件共享设置”页上单击“orabkup1”。 
    单击“连接”选项卡打开“连接”边栏选项卡，然后单击“Linux”选项卡。使用 SMB 协议复制提供用于装载文件共享的命令。 
    
    ![存储帐户添加页面](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要设置存储帐户和文件共享，请在 Azure CLI 中运行以下命令。

1. 在 VM 所在的资源组和位置中创建存储帐户：
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. 在存储帐户中创建配额为 10 TiB 的文件共享：

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. 在将文件共享装载到 VM 时，检索存储帐户主密钥 (key1)：

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>将 Azure 存储文件共享装载到 VM

1. 创建装载点：

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. 设置凭据：

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   在运行以下命令之前，用 `<Your Storage Account Key1>` 替换之前检索到的存储帐户密钥：
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. 更改凭据文件的权限：

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. 将装载添加到 /etc/fstab：

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. 运行命令，使用 SMB 协议装载 Azure 文件存储：

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   如果收到类似于以下内容的错误：

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   则可能 Linux 主机上未安装 CIFS 包。 
   
   若要检查是否已安装 CIS，请运行以下命令：

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   如果该命令未返回任何输出，请使用以下命令安装 CIFS 包：

   ```bash 
   sudo yum install cifs-utils
   ```

   现在，请重新运行上述装载命令以装载 Azure 文件存储。

6. 使用以下命令检查文件共享是否已正确装载：

   ```bash
   df -h
   ```

   输出应如下所示：

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>备份数据库

在本部分中，我们将使用 Oracle 恢复管理器 (RMAN) 对数据库和存档日志进行完整备份，并将备份作为备份集写入先前装载的 Azure 文件共享。 

1. 将 RMAN 配置为备份到 Azure 文件存储装入点：

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. 由于 Azure 标准文件共享的最大文件大小为 1 TiB，我们将 RMAN 备份部分的大小限制为 1 TiB。 （请注意，高级文件共享的最大文件大小限制为 4 TiB。 有关详细信息，请参阅 [Azure 文件存储可伸缩性和性能目标](../../../storage/files/storage-files-scale-targets.md)。）

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. 确认配置更改详细信息：

    ```bash
    RMAN> show all;
    ```

4. 现在，运行备份。 下面的命令将创建完整数据库备份（包括存档日志文件）作为压缩格式的备份集：   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

你现在已使用 Oracle RMAN 联机备份了数据库，并且备份位于 Azure 文件存储中。 此方法的优点是，可以利用 RMAN 的功能，同时将备份存储在可从其他 VM 访问的 Azure 文件存储中，这在需要克隆数据库时很有用。  
    
尽管使用 RMAN 和 Azure 文件存储进行数据库备份具有诸多优势，但备份和还原时间与数据库的大小相关，因此对于非常大的数据库，这些操作可能需要相当长的时间。 另一种备份机制是使用 Azure 备份应用程序一致性 VM 备份，它使用快照技术来执行备份，其优点是无论数据库大小如何，都可以进行非常快速的备份。 与恢复服务保管库集成，可以将 Oracle Database 备份安全地存储在可从其他 VM 和 Azure 区域访问的 Azure 云存储中。 

### <a name="restore-and-recover-the-database"></a>还原并恢复数据库

1.  关闭 Oracle 实例：

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  删除数据库数据文件：

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. 下面的命令使用 RMAN 来还原缺失的数据文件并恢复数据库：

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. 检查数据库内容是否已完全恢复：

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
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
