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
ms.openlocfilehash: fd6826370a0292190fc2534585fdb08f7982f447
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698696"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>使用 Azure 备份在 Azure Linux VM 上备份和恢复 Oracle Database 19c 数据库

适用于：:heavy_check_mark: Linux VM 

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

1. [连接到 VM](#connect-to-the-vm)。
1. [设置 Azure 文件存储](#setup-azure-files-storage-for-the-oracle-archived-redo-log-files)
1. [准备数据库](#prepare-the-databases)。

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

### <a name="setup-azure-files-storage-for-the-oracle-archived-redo-log-files"></a>为 Oracle 存档的重做日志文件设置 Azure 文件存储

Oracle 数据库存档重做日志文件在数据库恢复中发挥着重要作用，因为其中存储了从过去所创建的数据库快照进行前滚所需的已提交事务。 在存档日志模式下，当联机重做日志文件已满并转存至其他位置时，数据库将存档这些文件。 当数据库丢失时，需要结合备份使用这些文件来实现时间点恢复。  
   
Oracle 提供将重做日志文件存档至不同位置的功能，行业最佳做法建议将其中的至少一个目标指定为远程存储，使其独立于主机存储并受独立快照的保护。 Azure 文件存储非常适合用于满足这些要求。

Azure 文件存储文件共享是可以通过 SMB 或 NFS（预览版）协议作为普通文件系统组件附加到 Linux 或 Windows VM 的存储。 
   
若要使用 SMB 3.0 协议（建议）在 Linux 上设置 Azure 文件存储文件共享作为存档日志存储，请按照[有关在 Linux 中使用 Azure 文件存储的操作指南](../../../storage/files/storage-how-to-use-files-linux.md)进行操作。 完成设置后，请返回到本指南并完成所有剩余步骤。

### <a name="prepare-the-databases"></a>准备数据库

此步骤假定你已按照 [Oracle 创建数据库快速入门](./oracle-database-quick-create.md)进行操作并且你有一个在名为 `vmoracle19c` 的 VM 上运行的名为 `oratest1` 的 Oracle 实例，同时你在 shell 会话中使用标准 Oracle“oraenv”脚本，该脚本依赖标准 Oracle 配置文件“/etc/oratab”来设置环境变量。

为 VM 上的每个数据库执行以下步骤：

1. 切换到 oracle 用户：
 
   ```bash
    sudo su - oracle
    ```
    
1. 在连接之前，你需要通过运行 `oraenv` 脚本来设置环境变量 ORACLE_SID，该脚本会提示你输入 ORACLE_SID 名称：
    
    ```bash
    $ . oraenv
    ```

1.   将 Azure 文件存储共享添加为另一数据库存档日志文件目标
     
     此步骤假定你在 Linux VM 上的某个位置（例如，在名为 `/backup` 的装入点目录下）配置并装载了一个 Azure 文件存储共享。

     参照以下示例，为 VM 上安装的每个数据库创建以数据库 SID 命名的子目录。
     
     在此示例中，装入点名称为 `/backup`，SID 为 `oratest1`，因此，我们将创建子目录 `/backup/oratest1` 并将所有权更改到 oracle 用户。 请将 /backup/SID 替换为你的装入点名称和数据库 SID。 

     ```bash
     sudo mkdir /backup/oratest1
     sudo chown oracle:oinstall /backup/oratest1
     ```
    
1. 连接到数据库：
    
   ```bash
   sqlplus / as sysdba
   ```

1.  启动数据库（如果它尚未运行）。 
   
    ```bash
    SQL> startup
    ```
   
1. 将数据库的第一个存档日志目标设置为你之前创建的文件共享目录：

   ```bash
   SQL> alter system set log_archive_dest_1='LOCATION=/backup/oratest1' scope=both;
   ```

1. 为数据库定义恢复点目标 (RPO)。

    为了 RPO 保持稳定，必须考虑到联机重做日志文件的存档频率。 存档日志生成频率由以下因素控制：
    - 联机重做日志文件的大小。 当联机日志文件已满足时，其内容将会转存，该文件将会存档。 联机日志文件越大，其填满时间就越长，这会降低存档生成频率。
    - ARCHIVE_LAG_TARGET 参数的设置控制当前联机日志文件必须转存并存档之前允许的最大秒数。 

    为了最大程度地降低转存和存档以及执行检查点操作的频率，Oracle 联机重做日志文件的大小通常很大（1024M、4096M、8192M 等）。 在繁忙的数据库环境中，日志仍可能每隔几秒或几分钟就要转存并存档一次，但在较不活跃的数据库中，可能需要在保存几个小时或几天的日志之后才会将最近的事务存档，这会大大降低存档频率。 因此，建议设置 ARCHIVE_LAG_TARGET 以确保 RPO 保持稳定。 明智的 ARCHIVE_LAG_TARGET 设置值为 5 分钟（300 秒），这可以确保任何数据库恢复操作都可以恢复到故障后 5 分钟或更短时间内的状态。

    若要设置 ARCHIVE_LAG_TARGET，请运行以下命令：

    ```bash 
    SQL> alter system set archive_lag_target=300 scope=both;
    ```

    若要更好地了解如何在 Azure 中部署 RPO 为零的高可用性 Oracle 数据库，请参阅 [Oracle 数据库的参考体系结构](./oracle-reference-architecture.md)。

1.  确保数据库处于存档日志模式，以启用联机备份。

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
1. 创建一个表来测试备份和还原操作：
   ```bash
   SQL> create user scott identified by tiger quota 100M on users;
   SQL> grant create session, create table to scott;
   SQL> connect scott/tiger
   SQL> create table scott_table(col1 number, col2 varchar2(50));
   SQL> insert into scott_table VALUES(1,'Line 1');
   SQL> commit;
   SQL> quit
   ```
## <a name="using-azure-backup"></a>使用 Azure 备份 

Azure 备份服务提供简单、安全且经济高效的解决方案来备份数据，并从 Microsoft Azure 云恢复数据。 Azure 备份提供独立且隔离的备份，可以防范原始数据的意外破坏。 备份存储在提供恢复点内置管理的恢复服务保管库中。 配置和可伸缩性很简单，备份经过优化，可以轻松地根据需要还原。

Azure 备份服务为 Oracle 和 MySQL 等各种应用程序提供了[框架](../../../backup/backup-azure-linux-app-consistent.md)来实现 Windows 和 Linux VM 备份期间的应用程序一致性。 这包括在创建磁盘快照之前调用前脚本（以使应用程序静止），以及在快照完成后调用后脚本（用于取消冻结应用程序）。 

该框架现在已增强，用于选定应用程序（例如 Oracle）的已打包的前脚本和后脚本由 Azure 备份服务提供，并已预加载到 Linux 映像上，因此你无需安装任何东西。 Azure 备份用户只需对应用程序进行命名，然后 Azure VM 备份就会自动调用相关的前脚本和后脚本。 打包的前脚本和后脚本将由 Azure 备份团队维护，这样就可以向用户保证这些脚本的支持、所有权和有效性。 目前，增强型框架支持的应用程序为 Oracle 和 MySQL。

> [!Note]
> 每次执行备份时，增强的框架都会在 VM 上安装的所有 Oracle 数据库上运行前脚本和后脚本。 
>
> workload.conf 文件中的参数 `configuration_path` 指向 Oracle /etc/oratab 文件（或遵循 oratab 语法的一个用户定义的文件）的位置。 有关详细信息，请参阅[设置应用程序一致性备份](#set-up-application-consistent-backups)。
> 
> Azure 备份将为 configuration_path 指向的文件中列出的每个数据库运行备份前和备份后脚本，但以 # 开头的行（被视为注释）或以 +ASM 开头的行（Oracle 自动存储管理实例）例外。
> 
> Azure 备份增强型框架会对在 ARCHIVELOG 模式下运行的 Oracle 数据库进行在线备份。 前脚本和后脚本使用 ALTER DATABASE BEGIN/END BACKUP 命令来实现应用程序一致性。 
>
> 在快照开始之前，必须完全关闭处于 NOARCHIVELOG 模式的数据库，使数据库备份保持一致。


在本部分，我们将使用 Azure 备份框架为正在运行的 VM 和 Oracle 数据库创建应用程序一致性快照。 当 Azure 备份创建 VM 磁盘的快照时，数据库会被置于备份模式，以便进行事务一致性联机备份。 此快照是存储的完整副本，而不是增量快照或写入时复制快照，因此它是还原数据库的有效介质。 使用 Azure 备份应用程序一致性快照的优点是，无论数据库多大，都可以极快速地创建快照，且快照在创建后就可用于还原操作，无需等待快照传输到恢复服务保管库中。

若要使用 Azure 备份来备份数据库，请完成以下步骤：

1. [为应用程序一致性备份准备环境](#prepare-the-environment-for-an-application-consistent-backup)。
1. [设置应用程序一致性备份](#set-up-application-consistent-backups)。
1. [触发 VM 的应用程序一致性备份](#trigger-an-application-consistent-backup-of-the-vm)。

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>为应用程序一致性备份准备环境

> [!Note] 
> Oracle 数据库通过作业角色分离使用最低特权来提供职责分离。 这是通过将独立的操作系统组与独立的数据库管理角色相关联来实现的。 然后，可以根据操作系统用户在操作系统组中的成员身份来为其授予不同的数据库特权。 
>
> `SYSBACKUP` 数据库角色（通用名称为 OSBACKUPDBA）用于提供受限的特权以便在数据库中执行备份操作，Azure 备份需要此角色。
>
> 在 Oracle 安装过程中，建议与 SYSBACKUP 角色关联的操作系统组名称为 `backupdba`，但也可以使用任何名称，因此首先需要确定表示 Oracle SYSBACKUP 角色的操作系统组的名称。

1. 切换到 oracle 用户：
   ```bash
   sudo su - oracle
   ```

1. 设置 Oracle 环境：
   ```bash
   export ORACLE_SID=oratest1
   export ORAENV_ASK=NO
   . oraenv
   ```

1. 确定表示 Oracle SYSBACKUP 角色的操作系统组的名称：
   ```bash
   grep "define SS_BKP" $ORACLE_HOME/rdbms/lib/config.c
   ```
   输出类似于以下内容： 
   ```output
   #define SS_BKP_GRP "backupdba"
   ```

   在输出中，括在双引号中的值（在本示例中为 `backupdba`）是 Oracle SYSBACKUP 角色在外部对其进行身份验证的 Linux 操作系统组的名称。 记下此值。

1. 通过运行以下命令来验证该操作系统组是否存在。 请将 \<group name\> 替换为上述命令返回的值（不包括引号）：
   ```bash
   grep <group name> /etc/group
   ```
   输出将如下所述（本示例中使用了 `backupdba`）： 
   ```output
   backupdba:x:54324:oracle
   ```

   > [!IMPORTANT] 
   > 如果输出与步骤 3 中检索到的 Oracle 操作系统组值不匹配，则需要创建表示 Oracle SYSBACKUP 角色的操作系统组。 请将 `<group name>` 替换为在步骤 3 中检索到的组名称：
   >   ```bash
   >   sudo groupadd <group name>
   >   ```

1. 创建属于在先前步骤中验证或创建的操作系统组的新备份用户 `azbackup`。 请将 \<group name\> 替换验证的组的名称：

   ```bash
   sudo useradd -g <group name> azbackup
   ```

1. 为新的备份用户设置外部身份验证。 

   备份用户 `azbackup` 需要能够使用外部身份验证来访问数据库，以免受到密码质询。 为此，必须创建一个通过 `azbackup` 在外部进行身份验证的数据库用户。 数据库使用你需要查找的用户名的前缀。

   针对 VM 上安装的每个数据库执行以下步骤：
 
   使用 sqlplus 来登录数据库，并检查外部身份验证的默认设置：
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   输出应如以下示例所示，其中显示了 `ops$` 作为数据库用户名前缀： 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   创建数据库用户 ops$azbackup 以在外部对 `azbackup` 用户进行身份验证，然后授予 sysbackup 特权：
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > 如果在运行 `GRANT` 语句时收到错误 `ORA-46953: The password file is not in the 12.2 format.`，请按照以下步骤将 orapwd 文件迁移到 12.2 格式。请注意，需要对 VM 上的每个 Oracle 数据库执行此操作：
   >
   > 1. 退出 sqlplus。
   > 1. 将旧格式的密码文件迁移到新名称。
   > 1. 迁移密码文件。
   > 1. 删除旧文件。
   > 1. 运行以下命令：
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapworatest1 $ORACLE_HOME/dbs/orapworatest1.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapworatest1 input_file=$ORACLE_HOME/dbs/orapworatest1.tmp
   >    rm $ORACLE_HOME/dbs/orapworatest1.tmp
   >    ```
   >
   > 1. 在 sqlplus 中重新运行 `GRANT` 操作。
   >
   
1. 创建存储过程，以将备份消息记录到数据库警报日志中：

   针对 VM 上安装的每个数据库执行以下步骤：

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

1. 首先切换到 root 用户：
   ```bash
   sudo su -
   ```

1. 检查是否有“etc/azure”文件夹。 如果没有，请创建应用程序一致性备份工作目录：

   ```bash
   if [ ! -d "/etc/azure" ]; then
      mkdir /etc/azure
   fi
   ```

1. 在此文件夹中，检查是否有“workload.conf”。 如果没有，请在 /etc/azure 目录中创建包含以下内容的文件 workload.conf，这些内容必须以 `[workload]` 开头。 如果文件已存在，则只需编辑字段，使其与以下内容匹配即可。 否则，下面的命令将创建此文件并填充内容：

   ```bash
   echo "[workload]
   workload_name = oracle
   configuration_path = /etc/oratab
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

   > [!IMPORTANT]
   > workload.conf 使用的格式如下：
   > * Azure 备份使用参数 workload_name 来确定数据库工作负载类型。 在本例中，设置为 oracle 可使 Azure 备份针对 Oracle 数据库运行正确前后一致性命令。
   > * 参数 timeout 指示每个数据库完成存储快照所需的最长时间（以秒为单位）。
   > * 参数 linux_user 指示由 Azure 备份用来运行数据库静止操作的 Linux 用户帐户。 前面你已创建此 `azbackup` 用户。
   > * 参数 configuration_path 指示 VM 上某个文本文件的绝对路径名称，该文件中的每行列出了 VM 上运行的一个数据库实例。 此文件通常是在数据库安装过程中由 Oracle 生成的 `/etc/oratab`，但也可能是使用你选择的任何名称的任何文件，但不管怎样，它必须遵守以下格式规则：
   >   * 其中每个字段以冒号字符 `:` 分隔的文本文件
   >   * 每行中的第一个字段是 ORACLE_SID 的名称
   >   * 每行中的第二个字段是该 ORACLE_SID 的 ORACLE_HOME 的绝对路径名称
   >   * 前两个字段之后的所有文本将被忽略
   >   * 如果行以井号字符 `#` 开头，则将整行视为注释，因此会将其忽略
   >   * 如果第一个字段包含表示自动存储管理实例的值 `+ASM`，则会将其忽略。 


### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>触发 VM 的应用程序一致性备份

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  在 Azure 门户中，转到资源组 rg-oracle，然后单击虚拟机 vmoracle19c。

1.  在“备份”边栏选项卡上，在资源组 rg-oracle 中新建名为 myVault 的恢复服务保管库。
    对于“选择备份策略”，选择“(新) DailyPolicy”。 若要更改备份频率或保持期，请改为选择“新建策略”。

    ![恢复服务保管库“添加”页](./media/oracle-backup-recovery/recovery-service-01.png)

1.  若要继续操作，请单击“启用备份”。

    > [!IMPORTANT]
    > 在单击“启用备份”后，备份过程将不会在计划时间到期前启动。 若要设置即时备份，请完成下一步。

1. 在资源组页上，单击新创建的恢复服务保管库 myVault。 提示：可能需要刷新页面才能看到它。

1.  在“myVault - 备份项”边栏选项卡中，在“备份项计数”下选择备份项计数。

    ![恢复服务保管库“myVault 详细信息”页](./media/oracle-backup-recovery/recovery-service-02.png)

1.  在“备份项(Azure 虚拟机)”边栏选项卡中，单击页面右侧的省略号 (...) 按钮，然后单击“立即备份”。

    ![恢复服务保管库“立即备份”命令](./media/oracle-backup-recovery/recovery-service-03.png)

1.  接受默认的“保留备份截止日期”值，然后单击“确定”按钮。 等待备份过程结束。 

    若要查看备份作业的状态，请单击“备份作业”。

    ![恢复服务保管库“作业”页](./media/oracle-backup-recovery/recovery-service-04.png)

    下图中显示了备份作业的状态：

    ![显示有状态的恢复服务保管库作业页](./media/oracle-backup-recovery/recovery-service-05.png)
    
    请注意，虽然执行快照只需要几秒钟，但将它传输到存储库可能需要一些时间，并且备份作业在传输完成之前不会完成。

1. 对于应用程序一致性备份，请解决日志文件中的所有错误。 日志文件位于 /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 创建恢复服务保管库：

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

1. 为 VM 启用备份保护：

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

1. 触发立即运行备份，而不是等待备份按默认计划（凌晨 5 点 UTC）触发： 

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

1. [删除数据库文件](#remove-the-database-files)。
1. [从恢复服务保管库生成还原脚本](#generate-a-restore-script-from-the-recovery-services-vault)。
1. [装载还原点](#mount-the-restore-point)。
1. [执行恢复](#perform-recovery)。

### <a name="remove-the-database-files"></a>删除数据库文件 

本文的稍后部分中将介绍如何测试恢复过程。 能够测试恢复过程之前，需要删除数据库文件。

1.  切换回到 oracle 用户：
    ```bash
    su - oracle
    ```

1. 关闭 Oracle 实例：

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

1.  删除数据库数据文件和控制文件以模拟故障：

    ```bash
    cd /u02/oradata/ORATEST1
    rm -f *.dbf *.ctl
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>从恢复服务保管库生成还原脚本

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 Azure 门户中，搜索并选择“myVault”恢复服务保管库项。

    ![恢复服务保管库 myVault 备份项](./media/oracle-backup-recovery/recovery-service-06.png)

1. 在“概述”边栏选项卡上，依次选择“备份项”和“Azure 虚拟机”，其中应列出非零的“备份项计数”。

    ![恢复服务保管库 Azure 虚拟机备份项计数](./media/oracle-backup-recovery/recovery-service-07.png)

1. 在“备份项”（“Azure 虚拟机”）页上，你的 VM vmoracle19c 列出。 单击右边的省略号，以打开菜单并选择“文件恢复”。

    ![恢复服务保管库“文件恢复”页的屏幕快照](./media/oracle-backup-recovery/recovery-service-08.png)

1. 在“文件恢复(预览)”窗格中，单击“下载脚本”。 然后，将下载的文件 (.py) 保存到客户端计算机上的文件夹中。 为运行脚本而生成密码。 将密码复制到文件中，以供稍后使用。 

    ![下载脚本文件保存选项](./media/oracle-backup-recovery/recovery-service-09.png)

1. 将 .py 文件复制到 VM 中。

    以下示例演示如何使用安全复制 (scp) 命令将文件移动到 VM。 还可先将内容复制到剪贴板，再粘贴到 VM 上设置的新文件中。

    > [!IMPORTANT]
    > 在以下示例中，请确保更新 IP 地址和文件夹值。 这些值必须映射到保存文件的文件夹。
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要列出 VM 的恢复点，请使用 az backup recoverypoint list。 在此示例中，我们选择了在名为 myVault 的恢复服务保管库中受保护的、名为 vmoracle19c 的 VM 的最近恢复点：

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

若要获取将恢复点连接或装载到 VM 的脚本，请使用 az backup restore files mount-rp 命令。 以下示例获取在名为 myVault 的恢复服务保管库中受保护的、名为 vmoracle19c 的 VM 的脚本。

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

1. 切换到根用户：
   ```bash
   sudo su -
   ``````
1. 创建还原装入点，并将脚本复制到其中。

    在下面的示例中，创建 /restore 目录，以便将快照装载到其中，将文件移动到此目录中，然后将文件更改为被根用户所有的可执行文件。

    ```bash 
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

1. 确认访问已安装的卷。

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
为 VM 上的每个数据库执行以下步骤：

1. 将缺少的数据库文件还原到其所在位置：

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/oradata/ORATEST1
    cp * /u02/oradata/ORATEST1
    cd /u02/oradata/ORATEST1
    chown -R oracle:oinstall *
    ```
1. 切换回到 oracle 用户
   ```bash
   sudo su - oracle
   ```
1. 启动数据库实例并装载要读取的控制文件：
   ```bash
   sqlplus / as sysdba
   SQL> startup mount
   SQL> quit
   ```

1. 使用 sysbackup 连接到数据库：
   ```bash
   sqlplus / as sysbackup
   ```
1. 启动自动数据库恢复：

   ```bash
   SQL> recover automatic database until cancel using backup controlfile;
   ```
   > [!IMPORTANT]
   > 请注意，必须指定 USING BACKUP CONTROLFILE 语法，以告知 RECOVER AUTOMATIC DATABASE 命令不应在已还原的数据库控制文件中记录的 Oracle 系统更改编号 (SCN) 处停止恢复。 已还原的数据库控制文件是附带了数据库其余内容的快照，其中存储的 SCN 来自快照的时间点。 在此时间点之后可能记录了事务，我们想要恢复到最后一个已提交至数据库的事务的时间点。

   恢复成功完成后，你将看到消息 `Media recovery complete`。 但是，在使用 BACKUP CONTROLFILE 子句时，recover 命令将忽略联机日志文件，并且可能需要当前联机重做日志中的更改才能完成时间点恢复。 在这种情况下，你可能会看到如下所示的消息：

   ```output
   SQL> recover automatic database until cancel using backup controlfile;
   ORA-00279: change 2172930 generated at 04/08/2021 12:27:06 needed for thread 1
   ORA-00289: suggestion :
   /u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc
   ORA-00280: change 2172930 for thread 1 is in sequence #13
   ORA-00278: log file
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc' no
   longer needed for this recovery
   ORA-00308: cannot open archived log
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc'
   ORA-27037: unable to obtain file status
   Linux-x86_64 Error: 2: No such file or directory
   Additional information: 7

   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   ```
   
   > [!IMPORTANT]
   > 请注意，如果当前联机重做日志已丢失或损坏，因此不可使用，则此时可以取消恢复。 

   若要更正此问题，可以确定当前未存档哪个联机日志，然后在提示符下提供完全限定的文件名。


   打开新的 SSH 连接 
   ```bash
   ssh azureuser@<IP Address>
   ```
   切换到 oracle 用户并设置 Oracle SID
   ```bash
   sudo su - oracle
   export ORACLE_SID=oratest1
   ```
   
   连接到数据库并运行以下查询来查找联机日志文件 
   ```bash
   sqlplus / as sysdba
   SQL> column member format a45
   SQL> set linesize 500  
   SQL> select l.SEQUENCE#, to_char(l.FIRST_CHANGE#,'999999999999999') as CHK_CHANGE, l.group#, l.archived, l.status, f.member
   from v$log l, v$logfile f
   where l.group# = f.group#;
   ```

   输出将如下所示。 
   ```output
   SEQUENCE#  CHK_CHANGE           GROUP# ARC STATUS            MEMBER
   ---------- ---------------- ---------- --- ---------------- ---------------------------------------------
           13          2172929          1 NO  CURRENT          /u02/oradata/ORATEST1/redo01.log
           12          2151934          3 YES INACTIVE         /u02/oradata/ORATEST1/redo03.log
           11          2071784          2 YES INACTIVE         /u02/oradata/ORATEST1/redo02.log
   ```
   复制当前联机日志的日志文件路径和文件名，在本示例中为 `/u02/oradata/ORATEST1/redo01.log`。 切换回到运行 recover 命令的 SSH 会话，输入日志文件信息，然后按回车键：

   ```bash
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   ```

   应会看到日志文件已应用，且恢复已完成。 输入 CANCEL 退出 recover 命令：
   ```output
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   Log applied.
   Media recovery complete.
   ```

1. 打开数据库
   
   > [!IMPORTANT]
   > 当 RECOVER 命令使用 USING BACKUP CONTROLFILE 选项时，需要指定 RESETLOGS 选项。 RESETLOGS 通过将重做历史记录重置回到开始时间来创建数据库的新具体化实例，因为无法确定在恢复过程中跳过了多少个以前的数据库具体化实例。

   ```bash
   SQL> alter database open resetlogs;
   ```

   
1. 检查数据库内容是否已完全恢复：

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

1. 卸载还原点。

   成功恢复 VM 上的所有数据库后，便可以卸载还原点。 可以使用 `unmount` 命令在 VM 上完成此操作，也可以在 Azure 门户中通过“文件恢复”边栏选项卡来这样做。 也可以通过使用 -clean 选项再次运行 python 脚本，以卸载恢复卷。

   在 VM 中使用 unmount：
   ```bash
   sudo umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    在 Azure 门户中的“文件恢复(预览)”边栏选项卡上，单击“卸载磁盘”。

    ![卸载磁盘命令](./media/oracle-backup-recovery/recovery-service-10.png)
    


## <a name="restore-the-entire-vm"></a>还原整个 VM

可以还原整个 VM，而不是从恢复服务保管库还原已删除文件。

若要还原整个 VM，请完成以下步骤：

1. [停止并删除 VM](#stop-and-delete-the-vm)。
1. [恢复 VM](#recover-the-vm)。
1. [设置公共 IP 地址](#set-the-public-ip-address)。
1. [执行数据库恢复](#perform-database-recovery)。

### <a name="stop-and-delete-the-vm"></a>停止并删除 VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 Azure 门户中，转到 vmoracle19c 虚拟机，然后选择“停止”。

1. 当虚拟机不再运行时，依次选择“删除”和“是”。

   ![保管库“删除”命令](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 停止并解除分配 VM：

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

1. 删除 VM。 出现提示时输入“Y”：

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

1. 在 Azure 门户中，搜索并单击“myVault”恢复服务保管库项。

    ![恢复服务保管库 myVault 备份项](./media/oracle-backup-recovery/recovery-service-06.png)
    
1.  在“概述”边栏选项卡上，依次选择“备份项”和“Azure 虚拟机”，其中应列出非零的“备份项计数”。

    ![恢复服务保管库 Azure 虚拟机备份项计数](./media/oracle-backup-recovery/recovery-service-07.png)

1.  在“备份项”（“Azure 虚拟机”）页上，你的 VM vmoracle19c 列出。 单击 VM 名称。

    ![恢复 VM 页](./media/oracle-backup-recovery/recover-vm-02.png)

1.  在“vmoracle19c”边栏选项卡上，选择“一致性”类型为“应用程序一致性”的还原点，然后单击右边的省略号 (...) 来打开菜单。  在菜单中，单击“还原 VM”。

    ![还原 VM 命令](./media/oracle-backup-recovery/recover-vm-03.png)

1.  在“还原虚拟机”边栏选项卡中，依次选择“新建”和“新建虚拟机”。 输入虚拟机名称“vmoracle19c”，并选择 VNet“vmoracle19cVNET”，子网会根据你选择的 VNet 自动填充。 “还原 VM”进程要求 Azure 存储帐户在同一个资源组和区域中。 可以选择之前创建的存储帐户“orarestore”。

    ![还原配置值](./media/oracle-backup-recovery/recover-vm-04.png)

1.  要还原 VM，请单击“还原”按钮。

1.  若要查看还原过程的状态，请依次单击“作业”和“备份作业”。

    ![备份作业状态命令](./media/oracle-backup-recovery/recover-vm-05.png)

    单击处于“正在进行”状态的还原操作，以显示还原进程的状态：

    ![还原过程的状态](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要创建存储帐户和文件共享，请在 Azure CLI 中运行以下命令。

1. 在 VM 所在的资源组和位置中创建存储帐户：

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

1. 检索可用恢复点的列表。 

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

1. 将恢复点还原到存储帐户。 将 `<myRecoveryPointName>` 替换为在上一步中生成的列表中的恢复点：

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

1. 检索还原作业详细信息。 下面的命令获取已触发的还原作业的更多详细信息，包括检索模板 URI 所需的作业名称。 

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

1. 检索用于重新创建 VM 的 URI 的详细信息。 将 `<RestoreJobName>` 替换为上一步中的还原作业名称。

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

1.  停止 VM

    ![创建 IP 地址](./media/oracle-backup-recovery/create-ip-02.png)

1.  转到“网络”

    ![关联 IP 地址](./media/oracle-backup-recovery/create-ip-03.png)

1.  单击“附加网络接口”，选择原始公共 IP 地址仍关联到的原始 NIC **vmoracle19cVMNic，然后单击“确定”

    ![选择资源类型和 NIC 值](./media/oracle-backup-recovery/create-ip-04.png)

1.  现在，必须拆离使用 VM 还原操作创建的 NIC，因为它被配置为主接口。 单击“拆离网络接口”，选择类似于 vmoracle19c-nic-XXXXXXXXXXXX 的新 NIC，然后单击“确定”

    ![展示了“拆离网络接口”选择位置的屏幕截图。](./media/oracle-backup-recovery/create-ip-05.png)
    
    重新创建的 VM 现在有与原始 IP 地址和网络安全组规则关联的原始 NIC
    
    ![IP 地址值](./media/oracle-backup-recovery/create-ip-06.png)
    
1.  返回到“概述”，然后单击“开始” 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 停止并解除分配 VM：

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

1. 列出还原生成的当前 VM NIC

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

1. 附加名为 `<VMName>VMNic`（在此示例中为 `vmoracle19cVMNic`）的原始 NIC。 原始公共 IP 地址仍附加到此 NIC，并将在重新附加 NIC 时还原到 VM。 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

1. 拆离还原生成的 NIC

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```
1. 启动 VM：

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="perform-database-recovery"></a>执行数据库恢复
首先，重新连接到 VM：

```azurecli
ssh azureuser@<publicIpAddress>
```

还原整个 VM 后，务必通过在 VM 上的每个数据库上执行以下步骤来恢复每个数据库：

1. 你可能会发现实例正在运行，因为自动启动尝试在 VM 启动后启动数据库。 但是，数据库需要恢复，而且很可能只处于装载阶段，因此将先运行预备性关机，然后进入开始装载阶段。

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    ```
    
1. 执行数据库恢复
   > [!IMPORTANT]
   > 请注意，必须指定 USING BACKUP CONTROLFILE 语法，以告知 RECOVER AUTOMATIC DATABASE 命令不应在已还原的数据库控制文件中记录的 Oracle 系统更改编号 (SCN) 处停止恢复。 已还原的数据库控制文件是附带了数据库其余内容的快照，其中存储的 SCN 来自快照的时间点。 在此时间点之后可能记录了事务，我们想要恢复到最后一个已提交至数据库的事务的时间点。
    
    ```bash
    SQL> recover automatic database using backup controlfile until cancel;
    ```
   当最后一个可用的存档日志文件已应用后，键入 `CANCEL` 来结束恢复。

1. 打开数据库
   > [!IMPORTANT]
   > 当 RECOVER 命令使用 USING BACKUP CONTROLFILE 选项时，需要指定 RESETLOGS 选项。 RESETLOGS 通过将重做历史记录重置回到开始时间来创建数据库的新具体化实例，因为无法确定在恢复过程中跳过了多少个以前的数据库具体化实例。
   
    ```bash 
    SQL> alter database open resetlogs;
    ```
   

1. 检查数据库内容是否已恢复：

    ```bash
    SQL> select * from scott.scott_table;
    ```

现已完成在 Azure Linux VM 上备份和恢复 Oracle Database 19c 数据库。

可以在 Oracle 文档中找到有关 Oracle 命令和概念的详细信息，其中包括：

   * [对整个数据库执行 Oracle 用户管理的备份](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-database-backups.html#GUID-65C5E03A-E906-47EB-92AF-6DC273DBD0A8)
   * [对用户管理的数据库执行完整的恢复](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-66D07694-533F-4E3A-BA83-DD461B68DB56)
   * [Oracle STARTUP 命令](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/STARTUP.html#GUID-275013B7-CAE2-4619-9A0F-40DB71B61FE8)
   * [Oracle RECOVER 命令](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-54B59888-8683-4CD9-B144-B0BB68887572)
   * [Oracle ALTER DATABASE 命令](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ALTER-DATABASE.html#GUID-8069872F-E680-4511-ADD8-A4E30AF67986)
   * [Oracle LOG_ARCHIVE_DEST_n 参数](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/LOG_ARCHIVE_DEST_n.html#GUID-10BD97BF-6295-4E85-A1A3-854E15E05A44)
   * [Oracle ARCHIVE_LAG_TARGET 参数](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ARCHIVE_LAG_TARGET.html#GUID-405D335F-5549-4E02-AFB9-434A24465F0B)


## <a name="delete-the-vm"></a>删除 VM

如果不再需要该 VM，可使用以下命令删除资源组、该 VM 和所有相关资源：

1. 禁用保管库中的备份的软删除

    ```azurecli
    az backup vault backup-properties set --name myVault --resource-group rg-oracle --soft-delete-feature-state disable
    ```

1. 停止 VM 保护并删除备份

    ```azurecli
    az backup protection disable --resource-group rg-oracle --vault-name myVault --container-name vmoracle19c --item-name vmoracle19c --delete-backup-data true --yes
    ```

1. 删除包含所有资源的资源组

    ```azurecli
    az group delete --name rg-oracle
    ```

## <a name="next-steps"></a>后续步骤

[教程：创建具有高可用性的 VM](../../linux/create-cli-complete.md)

[浏览 VM 部署 Azure CLI 示例](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
