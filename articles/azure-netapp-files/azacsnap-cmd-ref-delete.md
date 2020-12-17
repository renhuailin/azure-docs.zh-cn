---
title: 使用 Azure NetApp 文件 Azure 应用程序一致性快照工具删除 |Microsoft Docs
description: 提供有关运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的 "删除" 命令的指南。
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632590"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 应用程序一致性快照工具删除 (预览版) 

本文提供了有关运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的 "删除" 命令的指南。

## <a name="introduction"></a>简介

可以通过命令删除卷快照和数据库目录条目 `azacsnap -c delete` 。

> [!IMPORTANT]
> 由于可能会干扰快照复制，因此，在运行此命令之前创建的快照不会被删除。

## <a name="command-options"></a>命令选项

`-c delete`命令包含以下选项：

- `--delete hana` 与选项一起使用时 `--hanasid <SID>` ， `--hanabackupid <HANA backup id>` 将从与条件匹配的 SAP HANA 备份目录中删除条目。

- `--delete storage` 与选项一起使用时， `--snapshot <snapshot name>` 将从后端存储系统中删除快照。

- `--delete sync` 与选项一起使用 `--hanasid <SID>` 时 `--hanabackupid <HANA backup id>` ，从的备份目录获取存储快照名称 `<HANA backup id>` ，然后删除备份目录中的条目 _以及_ 包含命名快照的任何卷的快照。

- `--delete sync` 当与一起使用时 `--snapshot <snapshot name>` ，将检查的备份目录中的任何条目 `<snapshot name>` ，获取 SAP HANA 备份 ID 并同时从包含命名快照的任何卷删除备份目录中的条目 _和_ 快照。

- `[--force]` (可选 *的) 谨慎使用*。  此操作将强制删除，而不提示确认。

- `[--configfile <config filename>]` 是允许自定义配置文件名称的可选参数。

### <a name="delete-a-snapshot-using-sync-option"></a>使用选项删除快照 `sync`

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> 检查备份目录中是否存在 SAP HANA 备份 ID 157979797979 的任何条目，获取存储快照名称，并从包含命名快照的所有卷中删除备份目录中的条目和快照。

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> 在备份目录中检查名为 hana_hourly .2020-01-22 _2358 的快照的任何条目，获取 SAP HANA 备份 ID，并从包含命名快照的任何卷删除备份目录中的条目和快照。

### <a name="delete-a-snapshot-using-hana-option"></a>使用选项删除快照 `hana`

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> 从 SID H80 的备份目录中删除 SAP HANA 备份 ID 157979797979。

### <a name="delete-a-snapshot-using-storage-option"></a>使用选项删除快照 `storage`

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> 从包含名为 hana_hourly .2020;-22 _2358 的快照的任何卷删除快照。

**使用选项的输出 `--delete storage`**

系统要求用户确认删除。

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

通过使用可选参数，可以避免用户确认，如下所示 `--force` ：

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>后续步骤

- [获取快照详细信息](azacsnap-cmd-ref-details.md)
- [进行备份](azacsnap-cmd-ref-backup.md)
