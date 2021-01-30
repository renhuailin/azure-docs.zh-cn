---
title: 使用 RMAN 和 Azure 存储在 Azure Linux VM 上备份 Oracle Database 19c 数据库
description: 了解如何将 Oracle Database 19c 数据库备份到 Azure 云存储。
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 695f151e6d6cc0a677942f60c751567da0cfca7c
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063784"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>使用 Azure 存储在 Azure Linux VM 上备份和恢复 Oracle Database 19c 数据库

本文演示如何使用 Azure 存储作为媒体来备份和还原在 Azure VM 上运行的 Oracle 数据库。 使用 SMB 协议将使用 Oracle RMAN 的数据库备份到装载到 VM 的 Azure 文件存储。 对备份介质使用 Azure 存储非常经济高效。 但对于非常大的数据库，Azure 备份提供了更好的解决方案。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- 若要执行备份和恢复过程，必须首先创建一个 Oracle Database 19c 的已安装实例的 Linux VM。 当前用于创建 VM 的 Marketplace 映像是  **oracle： oracle-数据库-19-3： oracle-0904：最新版本**。 按照 [oracle 创建数据库快速入门](./oracle-database-quick-create.md) 中的步骤创建 oracle 数据库来完成本教程。

## <a name="prepare-the-database-environment"></a>准备数据库环境

1. 若要与 VM 建立安全外壳 (SSH) 会话，请使用以下命令。 将 IP 地址和主机名组合替换为 VM 的 `publicIpAddress` 值。
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. 切换到 **_root_* _ 用户：
 
   ```bash
   sudo su -
   ```
    
3. 将 oracle 用户添加到 _*_ /etc/sudoers_ * _ 文件中：

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. 此步骤假设你有一个在名为 _vmoracle19c * 的 VM 上运行的 Oracle 实例 (测试) 。

   将用户切换到 *oracle* 用户：

   ```bash
   sudo su - oracle
   ```
    
5. 在连接之前，需要设置环境变量 ORACLE_SID：
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   还应将 ORACLE_SID 变量添加到 `oracle` 用户文件， `.bashrc` 以便以后使用以下命令登录：

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. 如果 Oracle 侦听器尚未运行，则启动它：
    
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

7.   (FRA) 位置创建快速恢复区域：

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  连接到数据库：

    ```bash
    sqlplus / as sysdba
    ```

9.  如果数据库尚未运行，则启动它：

    ```bash
    SQL> startup
    ```

10. 为快速恢复区域设置数据库环境变量：

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. 请确保数据库处于存档日志模式，以启用联机备份。
    首先检查日志存档状态：

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    如果在 NOARCHIVELOG 模式下，请在 sqlplus 中运行以下命令：

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

## <a name="back-up-to-azure-files"></a>备份到 Azure 文件

若要备份到 Azure 文件，请完成以下步骤：

1. 设置 Azure 文件存储。
1. 将 Azure 存储文件共享装载到 VM。
1. 备份数据库。
1. 还原和恢复数据库。

### <a name="set-up-azure-file-storage"></a>设置 Azure 文件存储

在此步骤中，你将使用 Oracle 恢复管理器将 Oracle 数据库备份 (RMAN) 备份到 Azure 文件存储。 Azure 文件共享是位于云中的完全托管文件共享。 它们可以使用服务器消息块 (SMB) 协议或网络文件系统 (NFS) 协议进行访问。 此步骤介绍如何创建使用 SMB 协议装载到 VM 的文件共享。 有关如何使用 NFS 进行装载的信息，请参阅 [使用 nfs 3.0 协议装载 Blob 存储](../../../storage/blobs/network-file-system-protocol-support-how-to.md)。

装载 Azure 文件时，我们将使用 `cache=none` 禁用文件共享数据的缓存。 若要确保在共享中创建的文件归 oracle 用户所有，也可以设置 `uid=oracle` 和 `gid=oinstall` 选项。 

# <a name="portal"></a>[门户](#tab/azure-portal)

首先，设置存储帐户。

1. 在 Azure 门户中配置文件存储

    在 Azure 门户中，选择 "***+ 创建资源**"，搜索并选择 _*_存储帐户_*_
    
    ![存储帐户添加页](./media/oracle-backup-recovery/storage-1.png)
    
2. 在 "创建存储帐户" 页中，选择现有的资源组 _*_rg-oracle_*_，将存储帐户命名为 _*_oracbkup1_*_ ，然后选择 " _*_存储 V2" (generalpurpose V2)_*_ 帐户类型。 将复制更改为 _*_本地冗余存储 (LRS)_*_ 并将性能设置为 _*_Standard_*_。 确保将 "位置" 设置为与资源组中的所有其他资源相同的区域。 
    
    ![存储帐户添加页](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. 单击 " _*_高级_*_ " 选项卡，在 "Azure 文件" 下，将 _*_大文件共享_*_ 设置为 " _*_已启用_*_"。 依次单击“查看 + 创建”、“创建”。
    
    ![存储帐户添加页](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. 创建存储帐户后，请前往资源并选择 " _*_文件共享_"*_
    
    ![存储帐户添加页](./media/oracle-backup-recovery/file-storage-3.png)
    
5. 单击 " _*_ + 文件 share_ *_"，然后在 "*_新建文件共享_"*边栏选项卡中，为文件共享 _*_orabkup1_*_ 命名。将 _*_Quota_*_ 设置为 _*_10240_*_ GiB，并检查 _*_事务优化_*_ 作为层。配额反映文件共享可以增长到的上限。由于我们使用的是标准存储，因此 PAYG 和未预配资源，因此将其设置为 10 TiB 不会产生超出你使用范围的成本。如果备份策略需要更多的存储空间，则必须将配额设置为适当的级别以保存所有备份。  完成 "新建文件共享" 边栏选项卡后，单击*"_创建_"。
    
    ![存储帐户添加页](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. 创建后，单击 "文件共享设置" 页上的 " _*_orabkup1_*_ "。 
    单击 " _*_连接_*_ " 选项卡以打开 "连接" 边栏选项卡，然后单击 " _*_Linux_*_ " 选项卡。复制提供的命令，以便使用 SMB 协议装载文件共享。 
    
    ![存储帐户添加页](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要设置存储帐户和文件共享，请在 Azure CLI 中运行以下命令。

1. 在与 VM 相同的资源组和位置中创建存储帐户：
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. 在存储帐户中创建配额为 10 TiB 的文件共享：

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. 在将文件共享装载到 VM 时，检索存储帐户主密钥 (key1) ：

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>将 Azure 存储文件共享装载到 VM

1. 创建装入点：

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. 设置凭据：

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   `<Your Storage Account Key1>`在运行以下命令之前，替换之前检索到的存储帐户密钥：
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

4. 将装载添加到/etc/fstab：

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

   然后，可能不会在 Linux 主机上安装 CIFS 包。 
   
   若要检查是否安装了 CIS，请运行以下命令：

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   如果该命令未返回任何输出，请使用以下命令安装 CIFS 包：

   ```bash 
   sudo yum install cifs-utils
   ```

   现在，请重新运行上述装载命令以装载 Azure 文件存储。

6. 请检查文件共享是否已正确装入以下命令：

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

1. 将 RMAN 配置为备份到 Azure 文件装入点：

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. 由于 Azure 标准文件共享的最大文件大小为 1 TiB，因此，我们会将 RMAN 备份部分的大小限制为 1 TiB。  (请注意，高级文件共享的最大文件大小限制为 4 TiB。 有关详细信息，请参阅 [Azure 文件可伸缩性和性能目标](../../../storage/files/storage-files-scale-targets.md)。 ) 

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. 确认配置更改详细信息：

    ```bash
    RMAN> show all;
    ```

4. 现在运行备份。 以下命令将采用压缩格式的 backupset 进行完整数据库备份，包括存档日志文件：   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

你现在已使用 Oracle RMAN 备份了数据库，并在 Azure 文件存储中进行备份。 此方法的优点是，可以利用 RMAN 的功能，同时将备份存储在可从其他 Vm 访问的 Azure 文件存储中，这在需要克隆数据库时很有用。  
    
使用 RMAN 和用于数据库备份的 Azure 文件存储具有很多优势时，备份和还原时间会与数据库的大小相关联，因此对于非常大的数据库，这些操作可能需要相当长的时间。  使用 Azure 备份应用程序一致性 VM 备份的一种替代备份机制，使用快照技术来执行备份，这种备份的优点是非常快，而不考虑数据库大小。 与恢复服务保管库集成，可在 Azure 云存储中安全存储 Oracle Database 备份，可从其他 Vm 和 Azure 区域访问。 

### <a name="restore-and-recover-the-database"></a>还原和恢复数据库

1.  关闭 Oracle 实例：

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  删除数据文件和备份：

    ```bash
    cd /u02/oradata/TEST
    rm -f _.dbf
    ```

3. 以下命令使用 RMAN 还原缺少的数据文件并恢复数据库：

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


现在已完成在 Azure Linux VM 上 Oracle Database 19c 数据库的备份和恢复。

## <a name="delete-the-vm"></a>删除 VM

如果不再需要 VM，可以使用以下命令删除资源组、VM 和所有相关的资源：

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>后续步骤

[教程：创建具有高可用性的 VM](../../linux/create-cli-complete.md)

[浏览 VM 部署 Azure CLI 示例](../../linux/cli-samples.md)
