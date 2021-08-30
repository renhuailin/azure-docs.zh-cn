---
title: 应用程序网关中的常见密钥保管库错误
titleSuffix: Azure Application Gateway
description: 本文可帮助确定并解决与 Key Vault 相关的问题，以便应用程序网关正常运行。
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: 9be1caf74a7eeaa225c2025767eeef86053c17a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748799"
---
# <a name="common-key-vault-errors-in-application-gateway"></a>应用程序网关中的常见密钥保管库错误

本故障排除指南有助于详细了解 Key Vault 错误代码及其原因，以及导致问题的相关 Key Vault 资源。 其中还包括解决此类错误配置的分步指南。

> [!NOTE]
> 应用程序网关中的 Key Vault 诊断日志每四小时生成一次。 因此，在某些情况下，如果修复配置后诊断仍显示错误，则可能需要等待日志刷新。

> [!TIP]
> 建议使用无版本的机密标识符。 这样，如果 Key Vault 中有更新的版本可用，应用程序网关将自动轮换证书。  无版本的机密 URI 示例 `https://myvault.vault.azure.net/secrets/mysecret/`。

### <a name="list-of-error-codes-and-their-details"></a>错误代码及其详细信息列表

[comment]: # (错误代码 1)
#### <a name="1-error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>**1) 错误代码：** UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**说明：** 用户分配的托管标识没有 GET 权限。 

**解决方法：** 配置 Key Vault 的访问策略，向关联的用户分配的托管标识授予对机密的 GET 权限。 
1. 导航到 Azure 门户中链接的 Key Vault
2. 打开“访问策略”边栏选项卡
3. 为“权限”模型选择“Vault 访问策略”
4. 为给定用户分配的托管标识选择机密的“Get”权限
5. 保存配置


:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text="用户分配的标识没有 Key Vault 的 Get 权限。":::

有关 Key Vault 访问策略的完整指南，请参阅这篇[文章](../key-vault/general/assign-access-policy-portal.md)
</br></br>



[comment]: # (错误代码 2)
#### <a name="2-error-code-secretdisabled"></a>**2) 错误代码：** SecretDisabled 

**说明：** Key Vault 中已禁用关联的证书。 

**解决方法：** 重新启用当前用于应用程序网关的证书版本。
1. 导航到 Azure 门户中链接的 Key Vault
2. 打开“证书”边栏选项卡
3. 依次单击所需的证书名称和禁用的版本
4. 使用管理页面上的开关启用该证书版本

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="重新启用机密。":::
</br></br>


[comment]: # (错误代码 3)
#### <a name="3-error-code-secretdeletedfromkeyvault"></a>**3) 错误代码：** SecretDeletedFromKeyVault 

**说明：** Key Vault 中已删除关联的证书。 

**解决方法：** 可以使用 Key Vault 中的软删除恢复功能恢复其中已删除的证书对象。 恢复已删除的证书： 
1. 导航到 Azure 门户中链接的 Key Vault
2. 打开“证书”边栏选项卡
3. 使用“托管的已删除证书”选项卡恢复已删除的证书。

但如果证书对象已永久删除，则需新建证书并使用新的证书详细信息更新应用程序网关。 通过 Azure CLI 或 Azure PowerShell 进行配置时，建议使用无版本机密标识符 URI，以允许实例检索证书的更新版本（如果存在）。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="恢复 Key Vault 中已删除的证书。":::
</br></br>


[comment]: # (错误代码 4)
#### <a name="4-error-code-userassignedmanagedidentitynotfound"></a>**4) 错误代码：** UserAssignedManagedIdentityNotFound 

**说明：** 已删除关联的用户分配的托管标识。 

**解决方法：** 请按照以下指南解决此问题。
1. 在同一资源组下重新创建与之前同名的托管标识。 可以参阅资源活动日志了解详细信息。 
2. 创建后，至少在应用程序网关 - 访问控制 (IAM) 下为该新托管标识分配读者角色。
3. 最后，导航到所需的 Key Vault 资源并设置其访问策略，为此新托管标识授予 GET 机密权限。 

[详细信息](./key-vault-certs.md#how-integration-works)
</br></br>

[comment]: # (错误代码 5)
#### <a name="5-error-code-keyvaulthasrestrictedaccess"></a>**5) 错误代码：** KeyVaultHasRestrictedAccess

**说明：** Key Vault 的受限网络设置。 

**解决方法：** 你将在启用 Key Vault 防火墙以限制访问时遇到此问题。 仍可通过以下方式在 Key Vault 的受限网络中配置应用程序网关。
1. 在 Key Vault 的“网络”边栏选项卡下
2. 在“防火墙和虚拟网络”选项卡中选择“专用终结点和选定网络”
3. 最后，选择“是”以允许受信任的服务绕过 Key Vault 的防火墙。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Key Vault 访问受限。":::
</br></br>


[comment]: # (错误代码 6)
#### <a name="6-error-code-keyvaultsoftdeleted"></a>**6) 错误代码：** KeyVaultSoftDeleted 

**说明：** 关联的 Key Vault 处于软删除状态。 

**解决方法：** 恢复软删除的 Key Vault 非常容易。 在 Azure 门户中，转到 Key Vault 服务页面。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="搜索 Key Vault 服务。":::
</br></br>
单击“托管的已删除 Vault”选项卡。在其中可以找到并恢复已删除的 Key Vault 资源。
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="使用软删除恢复已删除的 Key Vault。":::
</br></br>


[comment]: # (错误代码 7)
#### <a name="7-error-code-customerkeyvaultsubscriptiondisabled"></a>**7) 错误代码：** CustomerKeyVaultSubscriptionDisabled 

**说明：** 已禁用 Key Vault 的订阅。 

**解决方法：** Azure 订阅可能因各种原因被禁用。 请参阅关于[重新激活已禁用的 Azure 订阅](../cost-management-billing/manage/subscription-disabled.md)的指南并执行必要的操作。
</br></br>



