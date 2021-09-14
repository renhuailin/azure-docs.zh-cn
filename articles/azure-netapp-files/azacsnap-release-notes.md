---
title: 适用于 Azure NetApp 文件的 Azure 应用程序一致性快照工具的发行说明 | Microsoft Docs
description: 提供可与 Azure NetApp 文件配合使用的 Azure 应用程序一致快照工具的发行说明。
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
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: phjensen
ms.openlocfilehash: 0a2a110b483d7f5f5618e5679e4479ebf1d313b8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438113"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Azure 应用程序一致性快照工具（预览版）的发行说明

此页列出了对 AzAcSnap 所做的重大更改，这些更改的目的是提供新功能或解决缺陷问题。

## <a name="aug-2021"></a>2021 年 8 月

### <a name="azacsnap-v502-build_2021082719086---patch-update-to-v501"></a>AzAcSnap v5.0.2 (Build_20210827.19086) - 对 v5.0.1 的补丁更新

AzAcSnap v5.0.2 (Build_20210827.19086) 作为 v5.0 分支的补丁更新提供，具有以下修复和改进：

- 忽略 `ssh` 255 退出代码。  在某些情况下，当未发生错误或执行失败时，用于与 Azure 大型实例上的存储通信的 `ssh` 命令将发出退出代码 255（请参阅 `man ssh`“退出状态”）- 随后 AzAcSnap 会捕获此错误并中止。  在此更新中，除了执行传统的退出代码检查外，还会执行附加验证来验证执行是否正常，这包括分析 `ssh` STDOUT 和 STDERR 中的错误。
- 修复安装程序 hdbuserstore 源路径检查。  安装程序会检查用户正在安装的 hdbuserstore 是否存在错误的源目录 - 现已修复为检查 `~/.hdb`。  这适用于在安装 `azacsnap` 之前为 `root` 用户预配置了 hdbuserstore 的系统（例如 Azure 大型实例）。
- 安装程序现在会显示它安装/提取的版本（如果不结合任何参数运行安装程序）。

下载[最新版本](https://aka.ms/azacsnapinstaller)的安装程序并查看如何[开始操作](azacsnap-get-started.md)。

## <a name="may-2021"></a>May-2021

### <a name="azacsnap-v501-build-2021052414837---patch-update-to-v50"></a>AzAcSnap v5.0.1（内部版本：20210524.14837）- 对 v5.0 的补丁更新

AzAcSnap v5.0.1（内部版本：20210524.14837）作为 v5.0 分支的补丁更新提供，具有以下修复和改进：

- 改进了退出代码处理。  在某些情况下，即使执行失败，也会发出退出代码 0（零），而此类情况下退出代码本应为非零值。  退出代码现在只会在成功运行 `azacsnap` 到结束的情况下为零。只要运行失败，退出代码就会变为非零值。  另外，AzAcSnap 的内部错误处理已扩展为捕获和发出由 AzAcSnap 运行的外部命令的退出代码（例如 hdbsql、ssh），前提是这些命令是失败的原因。

## <a name="april-2021"></a>2021 年 4 月

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0（版本编号：20210421.6349) - GA 发布版本（2021 年 4 月 21 日）

AzAcSnap v5.0（版本编号：20210421.6349）已公开发布，此版本包含以下修复和改进：

- Hdbsql 重试超时（等待 SAP HANA 的响应）自动设置为“savePointAbortWaitSeconds”的一半，以避免争用条件。  可以直接在 JSON 配置文件中修改“savePointAbortWaitSeconds”的设置，并且该设置的最小值必须为 600 秒。

## <a name="march-2021"></a>2021 年 3 月

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 预览版（内部版本：20210318.30771）

AzAcSnap v 5.0 预览版（内部版本：20210318.30771）已发布，并包括以下修复和改进内容：

- 无需将 AZACSNAP 用户添加到 SAP HANA 租户 DB，请参阅[启用与 SAP HANA 的通信](azacsnap-installation.md#enable-communication-with-sap-hana)部分。
- 让用户能使用已配置手动 QOS 的卷进行[还原](azacsnap-cmd-ref-restore.md)的修补程序。
- 添加了互斥控制来限制 Azure 大型实例的 SSH 连接。
- 修复安装程序以处理带有空格和其他相关问题的路径名。
- 为准备支持其他数据库服务器，请将可选参数“--hanasid”更改为“--dbsid”。

## <a name="next-steps"></a>后续步骤

- [Azure 应用程序一致性快照工具入门](azacsnap-get-started.md)
