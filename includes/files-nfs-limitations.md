---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b708e80bf335ba8bdce074285857a6f8b77b972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103439156"
---
在预览版阶段，NFS 具有以下限制：

- NFS 4.1 目前只支持[协议规范](https://tools.ietf.org/html/rfc5661)中的大部分功能。 有些功能（如所有类型的委托和回叫、锁定升级和降级、Kerberos 身份验证和加密）不受支持。
- 如果大多数请求是以元数据为中心的，那么，与读取/写入/更新操作相比，延迟将会更加严重。
- 必须创建新的存储帐户才能创建 NFS 共享。
- 只支持管理平面 REST API。 数据平面 REST API 不可用，这意味着存储资源管理器之类的工具将无法用于 NFS 共享，你也无法在 Azure 门户中浏览 NFS 共享数据。
- 目前不支持 AzCopy。
- 只适用于高级层。
- NFS 共享只接受数字 UID/GID。 为了避免客户端发送字母数字 UID/GID，应禁用 ID 映射。
- 在使用专用链接时，只能从单个 VM 上的一个存储帐户装载共享。 尝试从其他存储帐户装载共享将会失败。
- 最好依赖于分配到主要组的权限。 由于一个已知的 bug，分配到非主要组用户的权限有时可能会导致访问被拒绝。

### <a name="azure-storage-features-not-yet-supported"></a>尚不支持 Azure 存储功能

另外，以下 Azure 文件存储功能也不可与 NFS 共享配合使用：

- 基于标识的身份验证
- Azure 备份支持
- 快照
- 软删除
- 完全支持传输中加密（有关详细信息，请参阅 [NFS 安全性](../articles/storage/files/storage-files-compare-protocols.md#security)）
- Azure 文件同步（只适用于 NFS 4.1 不支持的 Windows 客户端）
