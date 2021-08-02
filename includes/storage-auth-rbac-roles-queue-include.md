---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a93e85114c46f6e0ce252bb4059836d62fadf20f
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904366"
---
Azure RBAC 提供了多个内置角色，用于使用 Azure AD 和 OAuth 授予对队列数据的访问权限。 某些角色可在 Azure 存储中提供数据资源权限，例如：

- [存储队列数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用来授予对 Azure 队列的读取/写入/删除权限。
- [存储队列数据读取者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用来授予对 Azure 队列的只读权限。
- [存储队列数据消息处理者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用来对 Azure 存储队列中的消息授予扫视、检索和删除权限。
- [存储队列数据消息发送者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用来对 Azure 存储队列中的消息授予添加权限。

若要了解如何列出 Azure RBAC 角色及其权限，请参阅[列出 Azure 角色定义](../articles/role-based-access-control/role-definitions-list.md)。

只有为数据访问明确定义的角色才允许安全主体访问队列数据。 内置角色（例如“所有者”、“参与者”和“存储帐户参与者”）允许安全主体管理存储帐户，但不通过 Azure AD 提供对该帐户内的队列数据的访问权限。 但是，如果角色包括 Microsoft.Storage/storageAccounts/listKeys/action，则分配了该角色的用户可以使用帐户访问密钥通过共享密钥授权来访问存储帐户中的数据。 有关详细信息，请参阅[选择如何在 Azure 门户中授予对 blob 数据的访问权限](../articles/storage/queues/authorize-data-operations-portal.md)。

要详细了解数据服务和管理服务的 Azure 存储的 Azure 内置角色，请参阅 [Azure RBAC 的 Azure 内置角色](../articles/role-based-access-control/built-in-roles.md#storage)的“存储”部分。 此外，若要了解 Azure 中提供权限的不同类型的角色，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!IMPORTANT]
> Azure 角色分配最多需要 30 分钟时间来进行传播。
