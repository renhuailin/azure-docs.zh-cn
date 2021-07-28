---
title: 使用 Azure NetApp 文件的 Azure 应用程序一致性快照工具进行删除 | Microsoft Docs
description: 提供的指南介绍了如何运行可与 Azure NetApp 文件配合使用的 Azure 应用程序一致性快照工具的删除命令。
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
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: d02f0e8ff3d8d6da37e0e457cea2e4878ff1a2b0
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930004"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool"></a>使用 Azure 应用程序一致性快照工具进行删除

本文提供的指南介绍了如何运行可与 Azure NetApp 文件配合使用的 Azure 应用程序一致性快照工具的删除命令。

## <a name="introduction"></a>简介

可以通过 `azacsnap -c delete` 命令来删除卷快照和数据库目录条目。

> [!IMPORTANT]
> 不应删除在运行此命令前 10 分钟以内创建的快照，因为那样可能会干扰快照复制。

## <a name="command-options"></a>命令选项

`-c delete` 命令的选项如下：

- `--delete hana` 在与选项 `--dbsid <SID>` 和 `--hanabackupid <HANA backup id>` 配合使用时，会从 SAP HANA 备份目录删除符合条件的条目。

- `--delete storage` 在与选项 `--snapshot <snapshot name>` 配合使用时，会从后端存储系统删除快照。

- `--delete sync` 在与选项 `--dbsid <SID>` 和 `--hanabackupid <HANA backup id>` 配合使用时，会从 `<HANA backup id>` 的备份目录获取存储快照名称，然后删除备份目录中的条目，并从任何包含命名快照的卷中删除快照。

- `--delete sync` 在与 `--snapshot <snapshot name>` 配合使用时，会检查 `<snapshot name>` 的备份目录中是否有任何条目，获取 SAP HANA 备份 ID，并删除备份目录中的条目以及任何包含命名快照的卷中的快照。

- `[--force]`（可选）请谨慎使用。  此操作会强制执行删除，不提示你进行确认。

- `[--configfile <config filename>]` 是可选参数，可用于自定义配置文件名称。

### <a name="delete-a-snapshot-using-sync-option"></a>使用 `sync` 选项删除快照

```bash
azacsnap -c delete --delete sync --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> 针对 ID 为 157979797979 的 SAP HANA 备份检查备份目录中是否有任何条目，获取存储快照名称，并删除备份目录中的条目以及所有包含命名快照的卷中的快照。

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> 针对名为“hana_hourly.2020-01-22_2358”的快照检查备份目录中是否有任何条目，获取 SAP HANA 备份 ID，并删除备份目录中的条目以及所有包含命名快照的卷中的快照。

### <a name="delete-a-snapshot-using-hana-option"></a>使用 `hana` 选项删除快照

```bash
azacsnap -c delete --delete hana --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> 从 SID H80 的备份目录中删除 SAP HANA 备份 ID 157979797979。

### <a name="delete-a-snapshot-using-storage-option"></a>使用 `storage` 选项删除快照

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> 从任何包含名为“hana_hourly.2020-01-22_2358”的快照的卷中删除快照。

使用 `--delete storage` 选项的输出

系统会要求用户确认删除。

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

使用可选的 `--force` 参数可以省去用户确认的步骤，如下所示：

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
