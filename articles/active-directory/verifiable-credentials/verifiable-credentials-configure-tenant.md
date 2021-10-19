---
title: 教程 - 为 Azure AD 可验证凭据（预览版）配置租户
description: 本教程介绍如何配置租户来使其受到支持
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 10/08/2021
ms.openlocfilehash: e28a210a4f80540da9aa8f08ca443c355705b469
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730727"
---
# <a name="configure-your-tenant-for-azure-ad-verifiable-credentials-preview"></a>为 Azure AD 可验证凭据（预览版）配置租户

通过 Azure Active Directory (Azure AD) 可验证凭据服务，可颁发和验证凭据。 若要能够颁发和/或验证凭据，请按照本教程中的指南操作。

在本教程中，我们将完成配置 Azure AD 租户所需的步骤，以便它可使用 Azure AD 可验证凭据。

在本文中，学习如何：

> [!div class="checklist"]
>
> - 设置服务主体
> - 创建 Azure Key Vault
> - 在 Azure AD 中注册应用程序
> - 设置可验证凭据预览版服务

下图说明了 Azure AD 可验证凭据体系结构和你配置的组件。

![此图说明了 Azure AD 可验证凭据体系结构。](media/verifiable-credentials-configure-tenant/verifiable-credentials-architecture.png)

## <a name="prerequisites"></a>先决条件

- 如果还没有 Azure 订阅，请[免费创建一个](https://azure.microsoft.comfree/?WT.mc_id=A261C142F)。
- 在租户中注册 [Azure Active Directory Premium 版本](../../active-directory/fundamentals/active-directory-get-started-premium.md)订阅。
- 要配置的目录的[全局管理员](../../active-directory/roles/permissions-reference.md#global-administrator)权限。
- 已安装 [PowerShell](/powershell/scripting/install/installing-powershell) 7.0.6 LTS-x64、PowerShell 7.1.3-x64 或更高版本。

## <a name="step-1-set-up-a-service-principal"></a>步骤 1。 设置服务主体

为请求服务 API 创建服务主体。 服务 API 是用于颁发或验证 Azure AD 可验证凭据的 Microsoft 服务。

若要创建服务主体：

1. 运行以下 PowerShell 命令。 这些命令将安装 AzureAD 模块（如果尚未安装），并导入 [AzureAD 模块](/powershell/azure/install-az-ps#installation)。 如果安装模块时遇到任何问题，请参阅[安装 Azure Az PowerShell 模块](/powershell/azure/install-az-ps#installation)一文。

    ```powershell
    if ((Get-Module -ListAvailable -Name "AzureAD") -eq $null) {  Install-Module "AzureAD" -Scope CurrentUser }  Import-Module AzureAD
    ```

1. 运行以下 PowerShell 命令来连接到 Azure AD 租户。 将 \<*your-tenant-ID*> 替换为 [Azure AD 租户 ID](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)。

    ```powershell
    Connect-AzureAD -TenantId <your-tenant-ID>
    ```

1. 在同一 PowerShell 会话中运行以下命令。 AppId“bbb94529-53a3-4be5-a069-7eaf2712b826”指的是可验证凭据 Microsoft 服务。

    ```powershell
    New-AzureADServicePrincipal -AppId "bbb94529-53a3-4be5-a069-7eaf2712b826" -DisplayName "Verifiable Credential Request Service" 
    ```

## <a name="step-2-create-a-key-vault"></a>步骤 2. 创建密钥保管库

[Azure Key Vault](/azure/key-vault/general/basic-concepts) 是一种云服务，可实现对机密和密钥的安全存储和访问。 可验证凭据服务将公钥和私钥存储在 Azure Key Vault 中。 这些密钥用来对凭据进行签名和验证。

如果没有 Azure Key Vault 实例，请在 Azure 门户上按照以下步骤创建密钥保管库。

>[!NOTE]
>默认情况下，仅创建密钥保管库的帐户具有访问权限。 可验证凭据服务需要访问密钥保管库。 该密钥保管库必须配置了一个访问策略，它允许配置期间使用的帐户创建密钥、删除密钥（如果你选择退出），并具有签名权限来创建可验证凭据的域绑定  。 如果在测试时使用的是同一帐户，请修改默认政策，除了授予保管库创建者默认访问权限以外，授予帐户 **登录** 权限。

### <a name="step-21-set-access-policies-for-the-key-vault"></a>步骤 2.1 为密钥保管库设置访问策略

加入 Azure 可验证凭据服务后，该服务会生成一组用于提供消息安全性的密钥。 这些密钥存储在密钥保管库中。 无论是颁发还是验证，密钥集仍然用于签名、更新和恢复可验证凭据。

密钥保管库[访问策略](/azure/key-vault/general/assign-access-policy)定义了给定的安全主体是否可对密钥保管库机密和密钥执行操作。 在 Azure Key Vault 中，为 Azure AD 可验证凭据服务的管理员帐户以及你创建的请求服务 API 主体设置访问策略。

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到用于本教程的密钥保管库。

1. 在“设置”下，选择“访问策略” 。

1. 在“添加访问策略”页的“用户”下，选择用于执行本教程操作的帐户 。

1. 对于“密钥权限”，请验证是否已选择以下权限。 创建、删除和签名 。 默认情况下，“创建”和“删除”已启用。  “签名”应该是唯一需要更新的密钥权限。
    
    以下屏幕截图显示如何配置管理员访问策略。

    ![屏幕截图显示如何配置管理员访问策略](media/verifiable-credentials-configure-tenant/set-key-vault-admin-access-policy.png)

1. 若要保存更改，请选择“保存”。

1. 选择“+ 添加访问策略”，向可验证凭据请求服务的服务主体添加权限。

1. 在“添加访问策略”页中

    1. 对于“密钥权限”，依次选择“获取”和“签名”  。

    1. 对于“机密权限”，选择“获取” 。

    1. 对于“选择主体”，选择“可验证凭据请求服务” 。

    1. 选择“添加”。  
        
       以下屏幕截图演示如何为可验证凭据请求服务添加访问策略。 
    
       ![屏幕截图演示如何为可验证凭据请求服务添加访问策略。](media/verifiable-credentials-configure-tenant/set-key-vault-service-principal-access-policy.png)

1. 选择“保存”，保存你创建的新策略。

## <a name="step-3-register-an-application-in-azure-ad"></a>步骤 3. 在 Azure AD 中注册应用程序

Azure AD 可验证凭据请求服务需要能够获取用于颁发和验证程序的访问令牌。 若要获取访问令牌，请注册 Web 应用程序，并授予在上一步中设置的 API 可验证凭据请求服务的 API 权限。

1. 使用管理帐户登录到 [Azure 门户](https://portal.azure.com/)。

1. 如果有权访问多个租户，请选择“目录 + 订阅”:::image type="icon" source="media/verifiable-credentials-configure-tenant/portal-directory-subscription-filter.png" border="false"::: 图标。 然后，搜索并选择“Azure Active Directory”。

1. 在“管理”下，选择“应用注册”，然后选择“新建注册”  。  

   ![屏幕截图显示如何选择新的应用程序注册。](media/verifiable-credentials-configure-tenant/register-azure-ad-app.png)

1. 输入应用程序的显示名称。 例如“verifiable-credentials-app”。

1. 对于“支持的帐户类型”，选择“仅此组织目录中的帐户(仅默认目录 - 单租户)” 。

1. 选择“注册”以创建应用程序。
  
   以下屏幕截图显示如何注册应用程序。

   ![屏幕截图显示如何注册可验证凭据应用。](media/verifiable-credentials-configure-tenant/register-azure-ad-app-properties.png)

### <a name="31-grant-permissions-to-get-access-tokens"></a>3.1 授予获取访问令牌的权限

在此步骤中，将向[步骤 1](#step-1-set-up-a-service-principal) 中创建的可验证凭据请求服务主体授予权限。

若要添加所需权限，请执行以下步骤：

1. 停留在 verifiable-credentials-app 应用程序详细信息页面上。 选择“API 权限”，然后选择“添加权限”。
    
    ![屏幕截图显示如何将权限添加到可验证凭据应用。](media/verifiable-credentials-configure-tenant/add-app-api-permissions.png)

1. 选择“我的组织使用的 API”。

1. 搜索之前创建的服务主体“可验证凭据请求服务”并将其选中。
    
    ![屏幕截图显示如何选择服务主体。](media/verifiable-credentials-configure-tenant/add-app-api-permissions-select-service-principal.png)

1. 选择“应用程序权限”，然后展开 VerifiableCredential.Create.All 。

    ![屏幕截图显示如何选择所需的权限。](media/verifiable-credentials-configure-tenant/add-app-api-permissions-verifiable-credentials.png)

1. 选择“添加权限”  。

1. 选择“为 \<your tenant name\> 授予管理员同意”。

## <a name="step-4-set-up-verifiable-credentials-preview-service"></a>步骤 4. 设置可验证凭据预览版服务

Azure AD 可验证凭据服务使用无缝去中心化标识解决方案来保护组织。 对于颁发者，Azure AD 提供一项服务供他们进行自定义和用来颁发自己的可验证凭据。 对于验证者，可验证凭据服务提供一项免费使用的 REST API 服务，让你在应用和服务中能够轻松请求和接受可验证凭据。

若要设置 Azure AD 可验证凭据，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，搜索“可验证凭据”。 然后，选择“可验证凭据(预览版)”。

1. 从左侧菜单中，选择“入门”。

1. 提供以下信息来设置组织：

    1. “组织名称”：输入名称，以便在可验证凭据中引用业务。 此值不面向客户。

    1. **域**：输入的域将添加到 DID 文档中的服务终结点。 域将你的 DID 绑定到用户可能了解的你业务中的内容。 Microsoft Authenticator 和其他数字钱包使用该信息来验证你的 DID 是否与你的域相关联。 如果钱包可以验证 DID，则会显示已验证的符号。 如果钱包无法验证 DID，则会通知用户凭据是由它无法验证的组织颁发的。   
            
        >[!IMPORTANT]
        > 域不能重定向。 否则，“DID”和“域”将无法链接。 请确保为域使用 HTTPS（例如 <https://contoso.com>）。

    1. **密钥保管库**：输入之前创建的密钥保管库的名称。

1. 选择“保存并创建凭据”。  
    
    以下屏幕截图显示如何设置可验证凭据：  
    
    ![屏幕截图显示如何设置可验证凭据。](media/verifiable-credentials-configure-tenant/verifiable-credentials-getting-started.png)

## <a name="next-steps"></a>后续步骤

- [了解如何从 Web 应用程序颁发 Azure AD 可验证凭据](verifiable-credentials-configure-issuer.md)。
- [了解如何验证 Azure AD 可验证凭据](verifiable-credentials-configure-verifier.md)。
