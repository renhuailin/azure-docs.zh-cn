---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017658"
---
| 机制 | 范围 |限制 | 支持的权限级别 |
|---|---|---|---|
| Azure RBAC | 存储帐户、容器。 <br>在订阅或资源组级别进行跨资源 Azure 角色分配。 | 一个订阅中 2000 个 Azure 角色分配 | Azure 角色（内置或自定义） |
| ACL| 目录、文件 |每个文件和每个目录有 32 个 ACL 条目（实际上是 28 个 ACL 条目）。 访问 ACL 和默认 ACL 都有自己的 32 个 ACL 条目的限制。 |ACL 权限|
