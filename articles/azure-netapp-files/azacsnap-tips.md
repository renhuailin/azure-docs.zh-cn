---
title: 有关使用 Azure NetApp 文件的 Azure 应用程序一致性快照工具的提示和技巧 | Microsoft Docs
description: 提供有关使用可与 Azure NetApp 文件一起使用 Azure 应用程序一致性快照工具的提示和技巧。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/04/2021
ms.author: phjensen
ms.openlocfilehash: 6650554c92f42a8b5c25a26be5f4ea41947105e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733400"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool"></a>有关使用 Azure 应用程序一致性快照工具的提示和技巧

本文提供了在使用 AzAcSnap 时可能会有所帮助的提示和技巧。

## <a name="limit-service-principal-permissions"></a>限制服务主体权限

可能需要限制 AzAcSnap 服务主体的范围。  查看 [Azure RBAC 文档](../role-based-access-control/index.yml)以详细了解 Azure 资源的精细访问管理。  

下面是具有使 AzAcSnap 正常运行所需的最少必需操作的示例角色定义。

```azurecli
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

若要使还原选项成功工作，AzAcSnap 服务主体还需要能够创建卷。  在这种情况下，角色定义需要附加操作，因此完整服务主体应类似于以下示例。

```azurecli
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>手动拍摄快照

在执行任何备份命令 (`azacsnap -c backup`) 之前，请通过运行测试命令来检查配置并验证是否成功执行了这些命令。  这些测试的正确执行证明 `azacsnap` 可以与 Azure 大型实例或 Azure NetApp 文件系统上的已安装 SAP HANA 数据库和 SAP HANA 基础存储系统通信 。

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

随后若要获取手动数据库快照备份，请运行以下命令：

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>设置自动快照备份

Unix/Linux 系统上的常见做法是使用 `cron` 自动在在系统上运行命令。 快照工具的标准做法是设置用户的 `crontab`。

下面是供用户 `azacsnap` 自动拍摄快照的 `crontab` 示例。

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

上面 crontab 的说明。

- `MAILTO=""`：使用空值可在执行 crontab 条目时防止 cron 自动尝试向用户发送电子邮件，因为它可能最终会出现在本地用户的邮件文件中。
- crontab 条目的速记时间版本不言自明：
  - `@monthly` = 每月运行一次，即“0 0 1 * *”。
  - `@weekly`  = 每周运行一次，即“0 0 * * 0”。
  - `@daily`   = 每天运行一次，即“0 0 * * *”。
  - `@hourly`  = 每小时运行一次，即“0 * * * *”。
- 前五列用于指定时间，请参阅下面的列示例：
  - `0,15,30,45`：每 15 分钟
  - `0-23`：每小时
  - `*`：每天
  - `*`：每月
  - `*`：一周的每一天
- 要执行的命令行包含在括号“()”中
  - `. /home/azacsnap/.profile` = 拉取用户的 .profile 以设置其环境，包括 $PATH 等。
  - `cd /home/azacsnap/bin` = 将执行目录更改为配置文件所处的位置“/home/azacsnap/bin”。
  - `azacsnap -c .....` = 要运行的完整 azacsnap 命令，包括所有选项。

此处进一步说明了 cron 和 crontab 文件的格式：<https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> 用户负责监视 cron 作业，以确保成功生成快照。

## <a name="monitor-the-snapshots"></a>监视快照

应监视以下条件以确保系统正常：

1. 可用磁盘空间。 快照会缓慢占用磁盘空间，因为较旧磁盘块会保留在快照中。
    1. 若要帮助自动进行磁盘空间管理，请使用 `--retention` 和 `--trim` 选项自动清理旧快照和数据库日志文件。
1. 快照工具的成功执行
    1. 检查 `*.result` 文件，了解 `azacsnap` 的最新运行是成功还是失败。
    1. 在 `/var/log/messages` 中检查 `azacsnap` 命令的输出。
1. 通过定期将快照还原到另一个系统来保持快照的一致性。

> [!NOTE]
> 若要列出快照详细信息，请执行命令 `azacsnap -c details`。

## <a name="delete-a-snapshot"></a>删除快照

若要删除快照，请使用命令 `azacsnap -c delete`。 无法从操作系统级别删除快照。 必须使用正确的命令 (`azacsnap -c delete`) 才能删除存储快照。

> [!IMPORTANT]
> 删除快照时请保持警惕。 删除后，无法恢复已删除的快照。

## <a name="restore-a-snapshot"></a>还原快照

可以将存储卷快照还原到新卷 (`-c restore --restore snaptovol`)。  对于 Azure 大型实例，可以将卷还原到快照 (`-c restore --restore revertvolume`)。

> [!NOTE]
> 未提供数据库恢复命令。

可以将快照复制回 SAP HANA 数据区域，但是在进行复制时 (`cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*`)，不得运行 SAP HANA。

对于 Azure 大型实例，可以通过打开服务请求联系 Microsoft 操作团队，要求从现有可用快照还原所需快照。 可以从 Azure 门户打开服务请求：<https://portal.azure.com>

如果决定执行灾难恢复故障转移，则 DR 站点上的 `azacsnap -c restore --restore revertvolume` 命令会自动提供最新（`/hana/data` 和 `/hana/logbackups`）卷快照以便可以进行 SAP HANA 恢复。 请谨慎使用此命令，因为它会中断生产与 DR 站点之间的复制。

## <a name="set-up-snapshots-for-boot-volumes-only"></a>仅为“引导”卷设置快照

> [!IMPORTANT]
> 此操作仅适用于 Azure 大型实例。

在某些情况下，客户已具有保护 SAP HANA 的工具，只需要配置“引导”卷快照。  在这种情况下，只需完成以下步骤。

1. 完成安装先决条件的步骤 1-4。
1. 启用与存储的通信。
1. 下载并运行安装程序以安装快照工具。
1. 完成快照工具的设置。
1. 获取要添加到 azacsnap 配置文件的卷的列表，在此示例中，存储用户名为 `cl25h50backup`，存储 IP 地址为 `10.1.1.10` 
    ```bash
    ssh cl25h50backup@10.1.1.10 "volume show -volume *boot*"
    ```
    ```output
    Last login time: 7/20/2021 23:54:03
    Vserver   Volume       Aggregate    State      Type       Size  Available Used%
    --------- ------------ ------------ ---------- ---- ---------- ---------- -----
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v51_vol aggr_n01_ssd online RW 150GB 57.24GB  61%
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v52_vol aggr_n01_ssd online RW 150GB 81.06GB  45%
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v53_vol aggr_n01_ssd online RW 150GB 79.56GB  46%
    3 entries were displayed.
    ```
    > [!NOTE] 
    > 在此示例中，此主机是 3 节点横向扩展系统的一部分，并且所有 3 个引导卷都可以从此主机中查看。  这意味着所有 3 个引导卷都可以通过此主机进行快照，并且在下一步中应将所有 3 个卷添加到配置文件中。

1. 按如下所示创建新配置文件。 引导卷详细信息必须处于 OtherVolume stanza 中：
    ```bash
    azacsnap -c configure --configuration new --configfile BootVolume.json
    ```
    ```output
    Building new config file
    Add comment to config file (blank entry to exit adding comments): Boot only config file.
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: y
    HANA SID (for example, H80): X
    HANA Instance Number (for example, 00): X
    HANA HDB User Store Key (for example, `hdbuserstore List`): X
    HANA Server's Address (hostname or IP address): X
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: y
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v51_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v52_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v53_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. 检查配置文件，请参阅以下示例：

    使用 `cat` 命令显示配置文件的内容：

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v51_vol"
                  },
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v52_vol"
                  },
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v53_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. 测试引导卷备份

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. 检查它是否列出，请注意添加了 `--snapshotfilter` 选项以限制返回的快照列表。

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    命令输出：
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t250_sles_boot_sollabams07v51_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v51_vol, , , Size used by Snapshots, 1.31GB
    #1, t250_sles_boot_sollabams07v52_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v52_vol, , , Size used by Snapshots, 1.31GB
    #1, t250_sles_boot_sollabams07v53_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v53_vol, , , Size used by Snapshots, 1.31GB
    ```

1. （可选）使用 `crontab` 设置自动快照备份，或设置适合运行 `azacsnap` 备份命令的适用计划程序。

> [!NOTE]
> 无需设置与 SAP HANA 的通信。

## <a name="restore-a-boot-snapshot"></a>还原“引导”快照

> [!IMPORTANT]
> 此操作仅适用于 Azure 大型实例。
> 服务器会还原到拍摄快照时的时间点。

可以按如下所示恢复“引导”快照：

1. 客户需要关闭服务器。
1. 服务器关闭后，客户需要打开包含要还原的计算机 ID 和快照的服务请求。
    > 客户可以从 Azure 门户打开服务请求：<https://portal.azure.com>
1. Microsoft 会使用指定计算机 ID 和快照还原操作系统 LUN，然后启动服务器。
1. 客户随后需要确认服务器是否已启动且正常。

还原后无需执行附加步骤。

## <a name="key-facts-to-know-about-snapshots"></a>需要了解的有关快照的关键事实

存储卷快照的关键属性：

- 快照位置：可以在卷内的虚拟目录 (`.snapshot`) 中找到快照。  对于 Azure 大型实例，请参阅以下示例：
  - 数据库：`/hana/data/<SID>/mnt00001/.snapshot`
  - 共享：`/hana/shared/<SID>/.snapshot`
  - 日志：`/hana/logbackups/<SID>/.snapshot`
  - 引导：HLI 的引导快照在操作系统级别不可见，但可以使用 `azacsnap -c details` 列出。

  > [!NOTE]
  >  `.snapshot` 是只读隐藏虚拟文件夹，提供对快照的只读访问。

- 最大快照：硬件可以对每个卷支持最多 250 个快照。 快照命令会根据命令行上设置的保留期，为前缀保留最大数量的快照，如果快照超出要保留的最大数量，则会删除最早快照。
- 快照名称：快照名称包含客户提供的前缀标签。
- 快照大小：取决于数据库级别的大小/更改。
- 日志文件位置：命令生成的日志文件会输出到 JSON 配置文件中定义的文件夹，这在默认情况下是在其下运行命令的子文件夹（例如 `./logs`）。

## <a name="next-steps"></a>后续步骤

- [故障排除](azacsnap-troubleshoot.md)
