---
title: 使用 Azure NetApp 文件的 Azure 应用程序一致的快照工具进行备份 | Microsoft Docs
description: 提供有关运行可用于 Azure NetApp 文件的 Azure 应用程序一致的快照工具的备份命令的指南。
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
ms.openlocfilehash: 5fd588cc9ff36f4213d62ee47ce296e9eadfc40e
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296750"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool"></a>使用 Azure 应用程序一致的快照工具进行备份

本文提供有关运行可用于 Azure NetApp 文件的 Azure 应用程序一致的快照工具的备份命令的指南。

## <a name="introduction"></a>简介

使用 `azacsnap -c backup` 命令运行基于存储快照的备份。  此命令对数据卷上的数据库一致的存储快照和其他卷上的存储快照（没有任何数据库一致性设置）执行业务流程。  

对于数据卷，`azacsnap` 将为存储快照准备数据库，然后将存储快照用于所有配置的卷，最后，它将通知数据库快照已完成。  它还将管理记录快照备份活动的任何数据库条目（例如 SAP HANA 备份目录）。

## <a name="command-options"></a>命令选项

`-c backup` 命令使用以下自变量：

- `--volume=` 进行快照的卷类型，此参数可能包含 `data` 或 `other`
  - `data` 在配置文件的 dataVolume stanza 中对卷进行快照拍摄。
  - `other` 在配置文件的 otherVolume stanza 中对卷进行快照拍摄。
  
  > [!NOTE]
  > 通过创建一个将引导卷作为 otherVolume 的单独的配置文件，可以按照完全不同的时间安排（例如，每日）来执行 `boot` 快照。

- `--prefix=` 快照名称的客户快照前缀。 该参数具有两个用途。 首先，为快照分组提供唯一的名称。 其次，确定为指定 `--prefix` 保留的存储快照 `--retention` 数。

    > [!IMPORTANT]
    > 仅允许字母数字 (“A-Z,a-z,0-9”)、下划线 (“_”) 和破折号 (“-”) 字符。

- `--retention` 定义的 `--prefix` 的要保留的快照数。 为该 `--prefix` 创建了新快照后，将删除任何其他快照。

- `--trim` 用于 SAP HANA v2 和更高版本，此选项保留备份目录以及磁盘目录和日志备份。 备份目录中保留的条目数取决于上面的 `--retention` 选项，并从备份目录以及相关的物理日志备份中删除已定义前缀 (`--prefix`) 的旧条目。 它还会删除早于最早非日志备份条目的任何日志备份条目。 此操作有助于防止日志备份占用所有可用磁盘空间。

  > [!NOTE]
  > 下面的示例命令将保留 9 个存储快照，并确保对备份目录进行持续修整，使其与保留的 9 个存储快照匹配。

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` 一个可选参数，定义用于与 SAP HANA 通信的加密方法， `openssl` 或 `commoncrypto`。 如果定义了一种加密方法，则 `azacsnap -c backup` 命令期望在同一目录中找到两个文件，这些文件必须根据对应的 SID 来命名。 请参阅[使用 SSL 与 SAP HANA 通信](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)。 下面的示例采用一个带有前缀 `hana_TEST` 的 `hana` 类型快照，并保留它们中的 `9` 个使用 SSL (`openssl`) 与 SAP HANA 通信。

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` 是一个可选参数，可用于自定义配置文件名称。

## <a name="snapshot-backups-are-fast"></a>快照备份速度快

快照备份的持续时间与卷大小无关，其中 10 TB 的卷在与 10 GB 卷相同的大致时间内进行了快照拍摄。  

影响总体执行时间的主要因素包括：要进行快照拍摄的卷数，以及 `--retention` 参数的任何更改（其中卷数的减少由于删除多余快照而增加了执行时间）。

在上面的示例配置中，（用于 **Azure 大型实例**），两个卷的快照需要不到 5 秒的时间完成。 对于 **Azure NetApp 文件**，两个卷的快照大约需要 60 秒。

> [!NOTE]
> 如果 `--retention` 从 `azacsnap` 运行的先前时间显著降低（例如，从 `--retention 50` 到 `--retention 5`），则所用时间将随着 `azacsnap` 需要删除额外的快照而增加。

## <a name="example-with-data-parameter"></a>`data` 参数的示例

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

此命令不会输出到控制台，但会写入日志文件、结果文件和 `/var/log/messages`。

在本示例中，“日志文件”名为 `azacsnap-backup-azacsnap.log`（请参阅[日志文件](#log-files)）

运行包含 `--volume data` 选项的 `-c backup` 时，还会生成一个结果文件，该文件将允许快速检查备份结果。  “结果”文件与日志文件具有相同的基本名，`.result` 为其后缀。

在本示例中，“结果文件”名为 `azacsnap-backup-azacsnap.result`，该文件包含以下输出：

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

`/var/log/messages` 文件包含与 `.result` 文件相同的输出。 请参阅以下示例（以 root 身份运行）：

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

## <a name="example-with-other-parameter"></a>`other` 参数的示例

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

此命令不会输出到控制台，但仅会写入日志文件。  它“不”会写入结果文件或 `/var/log/messages`。

在本示例中，“日志文件”名为 `azacsnap-backup-azacsnap.log`（请参阅[日志文件](#log-files)）。

## <a name="example-with-other-parameter-to-backup-host-os"></a>`other` 参数的示例（备份主机 OS）

> [!NOTE]
> 使用另一个配置文件 (`--configfile bootVol.json`)，其仅包含引导卷。

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

> [!IMPORTANT]
> 对于 Azure 大型实例，引导卷的配置文件卷参数在主机操作系统级别可能不可见。
> 此值可由 Microsoft Operations 提供。

此命令不会输出到控制台，但仅会写入日志文件。  它“不”会写入结果文件或 `/var/log/messages`。

在本示例中，“日志文件”名为 `azacsnap-backup-bootVol.log`（请参阅[日志文件](#log-files)）。

## <a name="log-files"></a>日志文件

日志文件由“（命令名称）-（`-c` 选项）-（配置文件名）”这些内容构成。  例如，如果运行 `azacsnap -c backup --configfile h80.json --retention 5 --prefix one-off` 命令，则日志文件叫做 `azacsnap-backup-h80.log`。  或者，如果将 `-c test` 选项与相同的配置文件（例如 `azacsnap -c test --configfile h80.json`）一起使用，则日志文件叫作 `azacsnap-test-h80.log`。

## <a name="next-steps"></a>后续步骤

- [获取快照详细信息](azacsnap-cmd-ref-details.md)
- [删除快照](azacsnap-cmd-ref-delete.md)
