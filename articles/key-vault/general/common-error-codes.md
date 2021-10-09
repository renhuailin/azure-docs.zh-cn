---
title: Azure Key Vault 的常见错误代码 |Microsoft Docs
description: Azure Key Vault 的常见错误代码
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: bd66619c65dd39e32a11b81096d4b763430fc16c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553672"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Azure Key Vault 的常见错误代码

Azure 密钥保管库上的操作可能返回下表中列出的错误代码

| 错误代码 | 用户消息 |
|--|--|
| VaultAlreadyExists |  由于指定名称已在使用中，因此尝试创建具有该名称的新密钥保管库失败。 如果最近删除了具有此名称的密钥保管库，则该密钥保管库可能仍处于软删除状态。 你可以在[此处](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault)验证它是否处于软删除状态 |
| VaultNameNotValid |  保管库名称应为由 3 到 24 个字符构成的字符串，并且只能包含数字 (0-9)、字母（a-z、A-Z）和连字符 (-) |
| AccessDenied |  你可能在访问策略中缺少执行该操作的权限。 |
| ForbiddenByFirewall |  客户端地址未获授权，调用方不是受信任的服务。 |
| ConflictError |  你请求对同一项进行多个操作。  |
| RegionNotSupported |  此资源不支持指定的 azure 区域。 |
| SkuNotSupported |  此资源不支持指定的 SKU 类型。 |
| ResourceNotFound |  未找到指定的 azure 资源。 |
| ResourceGroupNotFound | 未找到指定的 azure 资源组。 |
| CertificateExpired |  检查证书的到期日期和有效期。 |


## <a name="next-steps"></a>后续步骤

- 参阅 [Azure Key Vault 开发人员指南](developers-guide.md)
- 了解有关[向密钥保管库进行身份验证](authentication.md)的详细信息
