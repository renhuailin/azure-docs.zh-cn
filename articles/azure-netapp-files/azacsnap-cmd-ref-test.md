---
title: 测试适用于 Azure NetApp 文件的 Azure 应用程序一致快照工具 | Microsoft Docs
description: 说明如何运行可用于 Azure NetApp 文件的 Azure 应用程序一致性快照工具的测试命令。
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
ms.date: 08/04/2021
ms.author: phjensen
ms.openlocfilehash: eb41e1ebda2e5a14bc2987dded8948221ee93452
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729236"
---
# <a name="test-azure-application-consistent-snapshot-tool"></a>测试 Azure 应用程序一致性快照工具

本文介绍如何运行可用于 Azure NetApp 文件的 Azure 应用程序一致性快照工具的测试命令。

## <a name="introduction"></a>简介

使用 `azacsnap -c test` 命令可完成配置的测试。

## <a name="command-options"></a>命令选项

`-c test` 命令的选项如下：

- `--test hana` 测试与 SAP HANA 实例的连接。

- `--test storage` 会在所有配置的 `data` 卷上创建一个临时存储快照，然后再删除这些快照，以此来测试与基础存储接口的通信。 

- `--test all` 将按顺序执行 `hana` 和 `storage` 测试。

- `[--configfile <config filename>]` 是一个可选参数，可用于自定义配置文件名称。

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>检查与 SAP HANA `azacsnap -c test --test hana` 的连接

该命令可检查配置文件中所有 HANA 实例的 HANA 连接。 该命令使用 HDBuserstore 连接到 SYSTEMDB 并获取 SID 信息。

对于 SSL，该命令可以采用以下可选参数：

- `--ssl=` 强制与数据库建立加密连接，并定义用于与 SAP HANA 通信的加密方法：`openssl` 或 `commoncrypto`。 如果定义了一种加密方法，则该命令需要在同一目录中找到两个文件，这些文件必须根据对应的 SID 来命名。 请参阅[使用 SSL 与 SAP HANA 通信](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)。

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>`azacsnap -c test --test hana` 命令的输出

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>检查与存储 `azacsnap -c test --test storage` 的连接

`azacsnap` 命令将为所有配置的数据卷生成快照，以验证它是否具有对每个 SAP HANA 实例的卷的正确访问权限。 为每个数据卷创建一个临时快照，然后再删除，以此来验证每个文件系统的快照访问权限。

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>`azacsnap -c test --test storage` 命令的输出

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

## <a name="next-steps"></a>后续步骤

- [通过 Azure 应用程序一致性快照工具进行快照备份](azacsnap-cmd-ref-backup.md)
