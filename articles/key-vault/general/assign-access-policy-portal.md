---
title: 分配 Azure Key Vault 访问策略（门户）
description: 如何使用 Azure 门户将 Key Vault 访问策略分配到安全主体或应用程序标识。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 6258de3e6f81ef25c4f515c956662be13eb5f1e2
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136483"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>使用 Azure 门户分配 Key Vault 访问策略

Key Vault 访问策略确定给定的安全主体（即用户、应用程序或用户组）是否可以对 Key Vault [机密](../secrets/index.yml)、[密钥](../keys/index.yml)和[证书](../certificates/index.yml)执行不同的操作。 可以使用 Azure 门户（本文）、[Azure CLI](assign-access-policy-cli.md) 或 [Azure PowerShell](assign-access-policy-powershell.md) 来分配访问策略。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

若要详细了解如何通过 Azure 门户在 Azure Active Directory 中创建组，请参阅[创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>分配访问策略

1.  在 [Azure 门户](https://portal.azure.com)中，导航到 Key Vault 资源。 

1.  在“设置”下，选择“访问策略”，然后选择“添加访问策略”  ：

    ![选择“访问策略”，选择“添加角色分配”](../media/authentication/assign-policy-portal-01.png)

1.  在“证书权限”、“密钥权限”和“机密权限”下选择所需要的权限  。 也可以选择包含常见权限组合的模板：

    ![指定访问策略权限](../media/authentication/assign-policy-portal-02.png)

1. 在“选择主体”下，选择“未选择任何项”链接，以打开“主体”选择窗格  。 在搜索字段中输入用户、应用或服务主体的名称，选择相应的结果，然后选择“选择”。

    ![为该访问策略选择安全主体](../media/authentication/assign-policy-portal-03.png)

    如果使用的是应用的托管标识，请搜索并选择该应用本身的名称。 有关安全主体的详细信息，请参阅 [Key Vault 身份验证](authentication.md)。
 
1.  返回“添加访问策略”窗格，选择“添加”以保存该访问策略 。

    ![添加分配了安全主体的访问策略](../media/authentication/assign-policy-portal-04.png)

1. 返回“访问策略”页，验证“当前访问策略”下是否已列出你的访问策略，然后选择“保存”  。 访问策略在保存之后才会应用。

    ![保存访问策略更改](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 安全性](security-features.md)
- [Azure Key Vault 开发人员指南](developers-guide.md)
