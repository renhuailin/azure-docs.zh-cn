---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017658"
---
| 机制 | 范围 |限制 | 支持的权限级别 |
|---|---|---|---|
| Azure RBAC | 存储帐户、容器。 <br>在订阅或资源组级别跨资源 Azure 角色分配。 | 2000订阅中的 Azure 角色分配 | Azure 角色 (内置或自定义)  |
| ACL| 目录、文件 |每个文件和每个目录有 32 个 ACL 条目（实际上是 28 个 ACL 条目）。 访问 ACL 和默认 ACL 都有自己的 32 个 ACL 条目的限制。 |ACL 权限|
