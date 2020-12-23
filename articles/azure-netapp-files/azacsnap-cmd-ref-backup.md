---
title: 使用适用于 Azure NetApp 文件 Azure 应用程序一致性快照工具进行备份 |Microsoft Docs
description: 提供有关运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的 backup 命令的指南。
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
ms.openlocfilehash: 1051859d514c77bad1aa5f14becc2218a923df44
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632649"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 应用程序一致的快照工具 (预览来备份) 

本文提供了有关运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的 backup 命令的指南。

## <a name="introduction"></a>简介

基于存储快照的备份是使用命令运行的 `azacsnap -c backup` 。  此命令对数据卷上的数据库一致性存储快照执行业务流程，并在其他卷上执行无任何数据库一致性设置) 的存储快照 (。  

对于数据卷 `azacsnap` ，将为存储快照准备数据库，然后将存储快照用于所有配置的卷，最后，它将建议数据库快照已完成。  它还将管理记录快照备份活动 (的任何数据库条目，例如 SAP HANA 备份目录) 。

## <a name="command-options"></a>命令选项

此 `-c backup` 命令采用以下参数：

- `--volume=` 卷到快照的类型，此参数可能包含 `data` 或 `other`
  - `data` 在配置文件的 dataVolume stanza 中对卷进行快照。
  - `other` 在配置文件的 otherVolume stanza 中对卷进行快照。
  
  > [!NOTE]
  > 通过创建一个单独的配置文件，将启动卷作为 otherVolume，可以使用 `boot` 完全不同的计划来执行快照 (例如，每日) 。

- `--prefix=` 快照名称的客户快照前缀。 此参数有两个用途。 首先用途是为快照分组提供唯一的名称。 其次，确定 `--retention` 为指定保留的存储快照数 `--prefix` 。

    > [!IMPORTANT]
    > 仅允许使用字母数字 ( "A-z，a-z，0-9" ) ，下划线 ( "_" ) 和破折号 ( "-" ) 字符。

- `--retention` 定义要保留的快照数 `--prefix` 。 为此创建了新快照后，将删除任何其他快照 `--prefix` 。

- `--trim` 适用于 SAP HANA v2 和更高版本，此选项保留备份目录以及磁盘目录和日志备份。 备份目录中保留的条目数取决于 `--retention` 上面的选项，并 `--prefix` 从备份目录以及相关的物理日志备份中删除已定义前缀 () 的旧条目。 它还会删除早于最早的非日志备份条目的任何日志备份条目。 此操作有助于防止日志备份占用所有可用磁盘空间。

  > [!NOTE]
  > 下面的示例命令将保留9个存储快照，并确保对备份目录进行持续修整，使其与保留的9个存储快照匹配。

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` 一个可选参数，用于定义用于与 SAP HANA 通信的加密方法（ `openssl` 或） `commoncrypto` 。 如果已定义，则该 `azacsnap -c backup` 命令需要在同一目录中找到两个文件，这些文件必须在相应的 SID 后命名。 请参阅 [使用 SSL 与 SAP HANA 通信](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)。 下面的示例采用一个 `hana` 带有前缀的类型快照 `hana_TEST` ，并 `9` 使用 SSL () 使它们与 SAP HANA 通信 `openssl` 。

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` 是允许自定义配置文件名称的可选参数。

## <a name="snapshot-backups-are-fast"></a>快照备份速度快

快照备份的持续时间与卷大小无关，其中 10 TB 的卷在与 10 GB 卷相同的大致时间内进行了拍摄。  

影响总体执行时间的主要因素包括：要进行快照的卷数，以及参数 (中的任何更改， `--retention` 减少可在) 删除多余快照时增加执行时间。

在上面的示例配置中， (适用于 **Azure 大型实例**) ，两个卷的快照需要不到5秒的时间才能完成。 对于 **Azure NetApp 文件**，两个卷的快照大约需要60秒。

> [!NOTE]
> 如果 `--retention` 从以前的时间中显著降低了 `azacsnap` (例如，从 `--retention 50` 到 `--retention 5`) ，则所用时间将随着 `azacsnap` 需要删除额外的快照而增加。

## <a name="example-with-data-parameter"></a>带参数的示例 `data`

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

此命令不会输出到控制台，但会写入日志文件、结果文件和 `/var/log/messages` 。

*日志文件* 由命令名称 +-c 选项 + 配置文件名组成。 默认情况下， `-c backup` 使用默认配置文件名的运行的日志文件名 `azacsnap-backup-azacsnap.log` 。

*结果* 文件与日志文件具有相同的基名称， `.result` 其后缀为，例如， `azacsnap-backup-azacsnap.result` 包含以下输出：

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

`/var/log/messages`文件包含与文件相同的输出 `.result` 。 请参阅以下示例 (以 root) 运行：

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>带参数的示例 `other`

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

此命令不会输出到控制台，只会写入日志文件。  它 _不_ 会写入结果文件或 `/var/log/messages` 。

*日志文件* 由命令名称 +-c 选项 + 配置文件名组成。 默认情况下， `-c backup` 使用默认配置文件名的运行的日志文件名 `azacsnap-backup-azacsnap.log` 。

## <a name="example-with-other-parameter-to-backup-host-os"></a>`other` (备份主机 OS 的参数的示例) 

> [!NOTE]
> 使用另一个配置文件 (`--configfile bootVol.json` 仅包含启动卷) 。

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

此命令不会输出到控制台，只会写入日志文件。  它 _不_ 会写入结果文件或 `/var/log/messages` 。

在此示例中， *日志文件* 的名称为 `azacsnap-backup-bootVol.log` 。

> [!NOTE]
> 日志文件名称由 " (命令名称- (`-c` 选项) - (配置文件名) " 组成。  例如，如果 `-c backup` 将选项与日志文件名称一起使用 `h80.json` ，则将调用日志文件 `azacsnap-backup-h80.log` 。  如果使用 `-c test` 具有相同配置文件的选项，则将调用日志文件 `azacsnap-test-h80.log` 。

- HANA 大型实例类型：在 `TYPEI` HANA 大型实例单元上有两个有效值，或 `TYPEII` 依赖于。
- 请参阅 [适用于 HANA 大型实例的 sku](/azure/virtual-machines/workloads/sap/hana-available-skus) ，以确认可用的 sku。

## <a name="next-steps"></a>后续步骤

- [获取快照详细信息](azacsnap-cmd-ref-details.md)
- [删除快照](azacsnap-cmd-ref-delete.md)
