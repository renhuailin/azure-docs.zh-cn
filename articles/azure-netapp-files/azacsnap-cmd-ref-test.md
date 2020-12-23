---
title: 测试 Azure 应用程序适用于 Azure NetApp 文件的一致快照工具 |Microsoft Docs
description: 说明如何运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的测试命令。
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632575"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>测试 Azure 应用程序一致性快照工具 (预览版) 

本文介绍如何运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的测试命令。

## <a name="introduction"></a>简介

使用命令来测试配置 `azacsnap -c test` 。

## <a name="command-options"></a>命令选项

`-c test`命令包含以下选项：

- `--test hana`  测试与 SAP HANA 实例的连接。

- `--test storage` 通过在所有配置的卷上创建一个临时存储快照 `data` ，然后删除它们来测试与基础存储接口的通信。 

- `--test all` 将 `hana` 按顺序执行和 `storage` 测试。

- `[--configfile <config filename>]` 是允许自定义配置文件名称的可选参数。

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>检查与 SAP HANA 的连接 `azacsnap -c test --test hana`

此命令检查配置文件中所有 HANA 实例的 HANA 连接。 它使用 HDBuserstore 连接到 SYSTEMDB 并获取 SID 信息。

对于 SSL，此命令可以采用以下可选参数：

- `--ssl=` 强制与数据库建立加密连接，并定义用于与 SAP HANA 通信的加密方法（或） `openssl` `commoncrypto` 。 如果定义了此命令，则此命令需要在同一目录中找到两个文件，这些文件必须在相应的 SID 后命名。 请参阅 [使用 SSL 与 SAP HANA 通信](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)。

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>命令的输出 `azacsnap -c test --test hana`

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>检查与存储的连接 `azacsnap -c test --test storage`

此 `azacsnap` 命令将为所有配置的数据卷拍摄快照，以验证它是否具有对每个 SAP HANA 实例的卷的正确访问权限。 为每个数据卷创建并删除临时快照，以便验证每个文件系统的快照访问。

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>命令的输出 `azacsnap -c test --test storage`

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> 对于 Azure 大型实例， `azacsnap -c test --test storage` 命令推断存储生成和 B-HLI SKU。  然后，它将根据此信息提供有关配置 "启动" 快照的指导 (查看以输出) 开头的行 `Action:` 。

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>后续步骤

- [快照备份与 Azure 应用程序一致性快照工具](azacsnap-cmd-ref-backup.md)
