---
title: Azure 德国的安全和标识服务 | Microsoft Docs
description: 本文将比较 Azure 德国的安全和标识服务。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 1d66a1f9a1fd2aea17d28d52cbcaa102ec3cb6cc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117028955"
---
# <a name="azure-germany-security-and-identity-services"></a>Azure 德国的安全和标识服务

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="key-vault"></a>Key Vault
有关 Azure Key Vault 服务以及如何使用该服务的详细信息，请参阅[Key Vault 全球文档](../key-vault/index.yml)。

Key Vault 已在 Azure 德国中正式发布。 由于在全球 Azure 中没有扩展，因此只能通过 PowerShell 和 CLI 使用 Key Vault。

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory 为在 Microsoft Azure 中运行的信息系统提供标识和访问功能。 使用目录服务、安全组和组策略，可以帮助控制使用 Azure Active Directory 的计算机的访问和安全策略。 可以使用帐户和安全组，以及 Azure 基于角色的访问控制 (Azure RBAC) 来帮助管理对信息系统的访问。 

Azure Active Directory 已在 Azure 德国中正式发布。

### <a name="variations"></a>变体

* Azure 德国中的 Azure Active Directory 与全球 Azure 中的 Azure Active Directory 是完全分离的。 
* 客户无法使用 Microsoft 帐户登录 Azure 德国。
* Azure 德国的登录后缀是 onmicrosoft.de（而不是全球 Azure 中的 onmicrosoft.com） 。
* 客户需要单独的订阅才能使用 Azure 德国。
* Azure 德国中的客户无法访问需要全球 Azure 中的订阅或标识的资源。
* 全球 Azure 中的客户无法访问需要 Azure 德国中的订阅或标识的资源。
* 只能在一个云环境中添加/验证其他域。
 
> [!NOTE]
> 目前尚无法通过 Azure 德国内部的这两个租户为其他租户的用户分配权限。


## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Azure 德国博客](/archive/blogs/azuregermany/)。