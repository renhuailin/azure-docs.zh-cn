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
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111381"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Azure 应用程序一致性快照工具（预览版）的发行说明

此页列出了对 AzAcSnap 所做的重大更改，这些更改的目的是提供新功能或解决缺陷问题。

## <a name="march-2021"></a>2021 年 3 月

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 预览版（内部版本：20210318.30771）

AzAcSnap v 5.0 预览版（内部版本：20210318.30771）已发布，并包括以下修复和改进内容：

- 无需将 AZACSNAP 用户添加到 SAP HANA 租户 DB，请参阅[启用与 SAP HANA 的通信](azacsnap-installation.md#enable-communication-with-sap-hana)部分。
- 让用户能使用已配置手动 QOS 的卷进行[还原](azacsnap-cmd-ref-restore.md)的修补程序。
- 添加了互斥控制来限制 Azure 大型实例的 SSH 连接。
- 修复安装程序以处理带有空格和其他相关问题的路径名。
- 为准备支持其他数据库服务器，请将可选参数“--hanasid”更改为“--dbsid”。

下载[最新版本](https://aka.ms/azacsnapdownload)的安装程序并查看如何[开始操作](azacsnap-get-started.md)。

## <a name="next-steps"></a>后续步骤

- [Azure 应用程序一致性快照工具入门](azacsnap-get-started.md)
