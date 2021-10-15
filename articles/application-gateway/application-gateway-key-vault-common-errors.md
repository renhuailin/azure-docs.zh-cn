---
title: 应用程序网关中的常见密钥保管库错误
titleSuffix: Azure Application Gateway
description: 本文列出了与密钥保管库相关的问题，并帮助你解决这些问题，以便顺利操作应用程序网关。
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: cfcacf84b5a35862c3ef1423ed58ff4d5d7f3142
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272313"
---
# <a name="common-key-vault-errors-in-azure-application-gateway"></a>Azure 应用程序网关中的常见密钥保管库错误

本文帮助你了解你可能会遇到的密钥保管库错误代码的详细信息，包括导致这些错误的原因。 本文还包含解决此类错误配置的步骤。

> [!TIP]
> 使用不指定版本的机密标识符。 这样，如果 Azure 密钥保管库中有更新的版本可用，Azure 应用程序网关将自动轮换证书。 不包含版本的机密 URI 的示例：`https://myvault.vault.azure.net/secrets/mysecret/`。

## <a name="list-of-error-codes-and-their-details"></a>错误代码及其详细信息列表

以下部分包含了你可能会遇到的各种错误。 可以在 Azure 顾问中找到详细信息，并使用本故障排除文章来解决问题。 有关详细信息，请参阅[使用 Azure 门户针对新建议创建 Azure 顾问警报](../advisor/advisor-alerts-portal.md)。

> [!NOTE]
> Azure 应用程序网关每隔四小时生成密钥保管库诊断日志。 如果在修复配置后诊断仍显示错误，你可能需要等待日志刷新。

[comment]: # (错误代码 1)
### <a name="error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>错误代码：UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

说明：关联的用户分配的托管标识没有“Get”权限。 

解决方法：配置密钥保管库的访问策略，向用户分配的托管标识授予对机密的此权限。 
1. 在 Azure 门户中转到链接的密钥保管库。
1. 打开“访问策略”窗格。
1. 对于“权限模型”，请选择“保管库访问策略” 。
1. 在“机密管理操作”下，选择“Get”权限 。
1. 选择“保存”  。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text="显示如何解决“Get”权限错误的屏幕截图。":::

有关详细信息，请参阅[使用 Azure 门户分配密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md)。

[comment]: # (错误代码 2)
### <a name="error-code-secretdisabled"></a>错误代码：SecretDisabled 

说明：已在密钥保管库中禁用关联的证书。 

**解决方法：** 重新启用当前用于应用程序网关的证书版本。
1. 在 Azure 门户中转到链接的密钥保管库。
1. 打开“证书”窗格。
1. 选择所需的证书名称，然后选择已禁用的版本。
1. 在管理页上，使用切换开关启用该证书版本。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="显示如何重新启用机密的屏幕截图。":::

[comment]: # (错误代码 3)
### <a name="error-code-secretdeletedfromkeyvault"></a>错误代码：SecretDeletedFromKeyVault 

说明：已从密钥保管库中删除关联的证书。 

解决方法：若要恢复已删除的证书，请执行以下操作： 
1. 在 Azure 门户中转到链接的密钥保管库。
1. 打开“证书”窗格。
1. 使用“托管的已删除证书”选项卡恢复已删除的证书。

但如果证书对象已永久删除，则需要创建新证书，并使用新的证书详细信息更新应用程序网关。 通过 Azure CLI 或 Azure PowerShell 进行配置时，请使用不包含版本的机密标识符 URI。 此选项允许实例检索证书的续订版本（如果存在）。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="显示如何恢复密钥保管库中已删除的证书的屏幕截图。":::

[comment]: # (错误代码 4)
### <a name="error-code-userassignedmanagedidentitynotfound"></a>错误代码：UserAssignedManagedIdentityNotFound 

说明：已删除关联的用户分配的托管标识。 

解决方法：若要再次使用该标识，请执行以下操作：
1. 在同一资源组下重新创建与之前同名的托管标识。 资源活动日志包含了更多详细信息。 
1. 创建标识后，转到“应用程序网关 - 访问控制(IAM)”。 至少为该标识分配“读取者”角色。
1. 最后，转到所需的密钥保管库资源并设置其访问策略，为此新托管标识授予“Get”机密权限。 

有关详细信息，请参阅[集成的工作原理](./key-vault-certs.md#how-integration-works)。

[comment]: # (错误代码 5)
### <a name="error-code-keyvaulthasrestrictedaccess"></a>错误代码：KeyVaultHasRestrictedAccess

说明：密钥保管库存在受限网络设置。 

解决方法：启用密钥保管库防火墙以限制访问时，你将遇到此错误。 仍可执行以下步骤，在密钥保管库的受限网络中配置应用程序网关：
1. 在密钥保管库中打开“网络”窗格。
1. 选择“防火墙和虚拟网络”选项卡，然后选择“专用终结点和选定网络” 。
1. 然后使用“虚拟网络”添加应用程序网关的虚拟网络和子网。 在此过程中，还可以通过选中“Microsoft.KeyVault”服务终结点复选框来对其进行配置。
1. 最后，选择“是”以允许受信任的服务绕过 Key Vault 的防火墙。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="显示如何解决受限网络错误的屏幕截图。":::

[comment]: # (错误代码 6)
### <a name="error-code-keyvaultsoftdeleted"></a>错误代码：KeyVaultSoftDeleted 

说明：关联的密钥保管库处于软删除状态。 

解决方法：在 Azure 门户中搜索“密钥保管库”。 在“服务”下，选择“密钥保管库” 。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="显示如何搜索密钥保管库服务的屏幕截图。":::

选择“托管的已删除保管库”。 在此处可以查找并恢复已删除的密钥保管库资源。
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="显示如何恢复已删除的密钥保管库的屏幕截图。":::

[comment]: # (错误代码 7)
### <a name="error-code-customerkeyvaultsubscriptiondisabled"></a>错误代码：CustomerKeyVaultSubscriptionDisabled 

说明：已禁用密钥保管库的订阅。 

解决方法：你的 Azure 订阅可能出于各种原因而被禁用。 若要采取必要的措施来解决此问题，请参阅[重新激活已禁用的 Azure 订阅](../cost-management-billing/manage/subscription-disabled.md)。

## <a name="next-steps"></a>后续步骤

在你继续使用应用程序网关的过程中，以下故障排除文章可能有所帮助：

- [Azure 应用程序网关资源运行状况概述](resource-health-overview.md)
- [排查 Azure 应用程序网关会话相关性问题](how-to-troubleshoot-application-gateway-session-affinity-issues.md)
