---
title: 使用适用于 Azure NetApp 文件 Azure 应用程序一致性快照工具的提示和技巧 |Microsoft Docs
description: 提供使用适用于 Azure NetApp 文件的 Azure 应用程序一致快照工具的提示和技巧。
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
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 86d7b1a6d361ebbc0d8419d184f9a11d390a37f1
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803181"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>有关使用 Azure 应用程序一致性快照工具的提示和技巧 (预览版) 

本文提供了一些提示和技巧，在使用 AzAcSnap 时可能会有所帮助。

## <a name="limit-service-principal-permissions"></a>限制服务主体权限

可能需要限制 AzAcSnap 服务主体的作用域。  查看 [AZURE RBAC 文档](https://docs.microsoft.com/azure/role-based-access-control/) ，详细了解 azure 资源的精细访问管理。  

下面是使用 AzAcSnap 所需的最少所需操作所需的角色定义的示例。

```bash
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

## <a name="take-snapshots-manually"></a>手动拍摄快照

在执行 () 的任何备份命令之前 `azacsnap -c backup` ，请通过运行测试命令并验证是否已成功执行这些命令来检查配置。  正确执行这些测试 `azacsnap` 可与 **Azure 大型实例** 或 **azure NetApp 文件** 系统上已安装的 SAP HANA 数据库和 SAP HANA 的基础存储系统通信。

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

然后，若要获取手动数据库快照备份，请运行以下命令：

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>设置自动快照备份

Unix/Linux 系统常见的做法是使用 Unix/Linux 系统 `cron` 在系统上自动运行命令。 快照工具的标准做法是设置用户的 `crontab` 。

`crontab` `azacsnap` 下面是用于自动执行快照的用户的示例。

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

上述 crontab 的说明。

- `MAILTO=""`：通过使用空值，可以在执行 crontab 条目时防止 cron 自动尝试向用户发送电子邮件，因为它可能最终会出现在本地用户的邮件文件中。
- Crontab 条目的速记时间版本是一目了然的：
  - `@monthly` = 每月运行一次，即 "0 0 1 * *"。
  - `@weekly`  = 每周运行一次，即 "0 0 * * 0"。
  - `@daily`   = 一天运行一次，即 "0 0 * * *"。
  - `@hourly`  = 每小时运行一次，即 "0 * * * *"。
- 前五列用于指定时间，请参阅下面的列示例：
  - `0,15,30,45`：每15分钟
  - `0-23`：每小时
  - `*` ：每天
  - `*` ：每月
  - `*` ：每周的某一天
- 要执行的命令行包含在括号 " ( # A1" 中
  - `. /home/azacsnap/.profile` = 请求用户的. 配置文件以设置其环境，包括 $PATH 等。
  - `cd /home/azacsnap/bin` = 更改执行目录到配置文件所在位置的 "/home/azacsnap/bin"。
  - `azacsnap -c .....` = 要运行的完整 azacsnap 命令，包括所有选项。

下面是有关 cron 和 crontab 文件格式的进一步说明： <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> 用户负责监视 cron 作业，以确保成功生成快照。

## <a name="monitor-the-snapshots"></a>监视快照

应监视以下条件以确保系统正常：

1. 可用磁盘空间。 快照会慢慢地消耗磁盘空间，因为保留旧的磁盘块将保留在快照中。
    1. 若要帮助自动执行磁盘空间管理，请使用 `--retention` 和 `--trim` 选项来自动清理旧快照和数据库日志文件。
1. 快照工具的成功执行
    1. 检查 `*.result` 文件以了解最新运行的是否成功 `azacsnap` 。
    1. 检查 `/var/log/messages` 命令的输出 `azacsnap` 。
1. 快照的一致性：将它们定期还原到另一个系统。

> [!NOTE]
> 若要列出快照详细信息，请执行命令 `azacsnap -c details` 。

## <a name="delete-a-snapshot"></a>删除快照

若要删除快照，请执行命令 `azacsnap -c delete` 。 无法从操作系统级别删除快照。 必须使用正确的命令 (`azacsnap -c delete`) 才能删除存储快照。

> [!IMPORTANT]
> 请在删除快照时小心。 删除后， **无法** 恢复已删除的快照。

## <a name="restore-a-snapshot"></a>还原快照

可以将存储卷快照还原到 () 的新卷 `-c restore --restore snaptovol` 。  对于 Azure 大型实例，可以将卷还原到 () 的快照 `-c restore --restore revertvolume` 。

> [!NOTE]
> **未** 提供数据库恢复命令。

可以将快照复制回 SAP HANA 的数据区域，但是 () 时，SAP HANA 不得运行 `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` 。

对于 Azure 大型实例，你可以通过打开服务请求从现有可用快照还原所需的快照，联系 Microsoft 运营团队。 可以从 Azure 门户中打开服务请求： <https://portal.azure.com>

如果决定执行灾难恢复故障转移， `azacsnap -c restore --restore revertvolume` DR 站点上的命令将自动提供最新 (`/hana/data` ，并 `/hana/logbackups`) 卷快照以允许 SAP HANA 恢复。 请小心使用此命令，因为它会破坏生产和灾难恢复站点之间的复制。

## <a name="set-up-snapshots-for-boot-volumes-only"></a>仅为 "启动" 卷设置快照

> [!IMPORTANT]
> 此操作仅适用于 Azure 大型实例。

在某些情况下，客户已经具有保护 SAP HANA 的工具，并且只需要配置 "启动" 卷快照。  在这种情况下，将简化任务，应采取以下步骤。

1. 完成安装先决条件的步骤1-4。
1. 启用与存储的通信。
1. 下载运行安装程序以安装快照工具。
1. 完成快照工具的设置。
1. 创建新的配置文件，如下所示。 启动卷详细信息必须在 OtherVolume stanza (用户条目中以 <span style="color:red">红色</span>) ：
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
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
      "version": "5.0 Preview",
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
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
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

1. 测试启动卷备份

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. 选中它，请注意添加了 `--snapshotfilter` 限制返回的快照列表的选项。

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    命令输出：
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. 设置自动快照备份。

> [!NOTE]
> 不需要设置与 SAP HANA 的通信。

## <a name="restore-a-boot-snapshot"></a>还原 "启动" 快照

> [!IMPORTANT]
> 此操作适用于 Azure 大型实例只能。
> 在拍摄快照时，服务器将还原到该点。

"启动" 快照可以恢复，如下所示：

1. 客户需要关闭服务器。
1. 关闭服务器后，客户将需要打开包含要还原的计算机 ID 和快照的服务请求。
    > 客户可以从 Azure 门户中打开服务请求： <https://portal.azure.com>
1. Microsoft 将使用指定的计算机 ID 和快照还原操作系统 LUN，然后启动服务器。
1. 然后，客户将需要确认服务器已启动并且运行正常。

还原后，不需要执行其他步骤。

## <a name="key-facts-to-know-about-snapshots"></a>了解快照的关键事实

存储卷快照的关键属性：

- **快照的位置**：快照可以在卷中 () 中找到 `.snapshot` 。  请参阅下面的 **Azure 大型实例** 示例：
  - 数据 `/hana/data/<SID>/mnt00001/.snapshot`
  - 共享 `/hana/shared/<SID>/.snapshot`
  - 登录 `/hana/logbackups/<SID>/.snapshot`
  - 启动： B-HLI 的启动快照在操作系统级别中 **不可见** ，但可以使用来列出 `azacsnap -c details` 。

  > [!NOTE]
  >  `.snapshot` 是一个只读 *虚拟* 文件夹，提供对快照的只读访问。

- **最大快照：** 硬件可以承受每个卷最多250快照。 Snapshot 命令将根据命令行的保留集，为前缀保留最大数量的快照，如果快照超出了要保留的最大数目，则会删除最早的快照。
- **快照名称：** 快照名称包含客户提供的前缀标签。
- **快照的大小：** 取决于数据库级别的大小/更改。
- **日志文件位置：** 由命令生成的日志文件输出到 JSON 配置文件中定义的文件夹，默认情况下，该命令是在其下运行命令的子文件夹 (例如 `./logs`) 。

## <a name="next-steps"></a>后续步骤

- [故障排除](azacsnap-troubleshoot.md)
