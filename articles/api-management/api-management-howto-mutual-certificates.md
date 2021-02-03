---
title: 使用客户端证书身份验证保护 API 管理后端
titleSuffix: Azure API Management
description: 了解如何在 Azure API 管理中使用客户端证书身份验证管理客户端证书和安全后端服务。
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492804"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>在 Azure API 管理中使用客户端证书身份验证保护后端服务

API 管理允许使用客户端证书保护对 API 后端服务的访问。 本指南演示如何使用 Azure 门户管理 Azure API 管理服务实例中的证书。 它还说明了如何将 API 配置为使用证书来访问后端服务。

你还可以使用 [Api 管理 REST API](/rest/api/apimanagement/2020-06-01-preview/certificate)管理 api 管理证书。

## <a name="certificate-options"></a>证书选项

API 管理提供了两个选项来管理用于保护对后端服务的访问的证书：

* 引用在[Azure Key Vault](../key-vault/general/overview.md)管理的证书 
* 直接在 API 管理中添加证书文件

建议使用 key vault 证书，因为它有助于提高 API 管理安全性：

* 存储在密钥保管库中的证书可以在服务之间重复使用
* 精细 [访问策略](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) 可应用到密钥保管库中存储的证书
* 在密钥保管库中更新的证书会在 API 管理中自动轮替。 更新密钥保管库中的后，API 管理中的证书会在4小时内更新。 你还可以使用 Azure 门户或通过管理 REST API 手动刷新证书。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* 如果尚未创建 API 管理服务实例，请参阅 [创建 Api 管理服务实例][Create an API Management service instance]。
* 应将后端服务配置为进行客户端证书身份验证。 若要在 Azure App Service 中配置证书身份验证，请参阅 [此文][to configure certificate authentication in Azure WebSites refer to this article]。 
* 需要有权访问 Azure 密钥保管库中用于管理的证书和密码，或上载到 API 管理服务。 证书必须采用 **PFX** 格式。 允许使用自签名证书。

### <a name="prerequisites-for-key-vault-integration"></a>密钥保管库集成的先决条件

1. 有关创建密钥保管库的步骤，请参阅[快速入门：使用 Azure 门户创建密钥保管库](../key-vault/general/quick-create-portal.md)。
1. 在 API 管理实例中启用系统分配的或用户分配的 [托管标识](api-management-howto-use-managed-service-identity.md) 。
1. 将 [密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md) 分配给具有从保管库获取和列出证书的权限的托管标识。 添加策略的步骤：
    1. 在门户中导航到你的密钥保管库。
    1. 选择 " **设置" > 访问策略 > "+ 添加访问策略**"。
    1. 选择 " **证书权限**"，然后选择 " **获取** " 和 " **列表**"。
    1. 在 " **选择主体**" 中，选择托管标识的资源名称。 如果你使用系统分配的标识，则主体为你的 API 管理实例的名称。
1. 创建证书或将其导入到密钥保管库。 请参阅 [快速入门：使用 Azure 门户设置和检索 Azure Key Vault 中的证书](../key-vault/certificates/quick-create-portal.md)。

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>添加密钥保管库证书

请参阅 [密钥保管库集成的先决条件](#prerequisites-for-key-vault-integration)。

> [!CAUTION]
> 在 API 管理中使用密钥保管库证书时，请注意不要删除证书、密钥保管库或用于访问密钥保管库的托管标识。

若要将 key vault 证书添加到 API 管理：

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在 " **安全性**" 下，选择 " **证书**"。
1. 选择 "**证书**  >  **+ 添加**"。
1. 在 " **Id**" 中，输入所选的名称。
1. 在 " **证书**" 中，选择 **Key vault**。
1. 输入密钥保管库证书的标识符，或选择 " **选择** " 以从密钥保管库中选择一个证书。
    > [!IMPORTANT]
    > 如果自己输入密钥保管库证书标识符，请确保它不包含版本信息。 否则，在密钥保管库中的更新后，证书不会在 API 管理中自动轮替。
1. 在 " **客户端标识**" 中，选择系统分配的或现有的用户分配的托管标识。 了解如何 [在 API 管理服务中添加或修改托管标识](api-management-howto-use-managed-service-identity.md)。
    > [!NOTE]
    > 标识需要权限才能从密钥保管库获取和列出证书。 如果尚未配置对密钥保管库的访问权限，API 管理会提示你，使其可以使用所需的权限自动配置标识。
1. 选择 **添加** 。

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="添加密钥保管库证书":::

## <a name="upload-a-certificate"></a>上传证书

将客户端证书上传到 API 管理： 

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在 " **安全性**" 下，选择 " **证书**"。
1. 选择 "**证书**  >  **+ 添加**"。
1. 在 " **Id**" 中，输入所选的名称。
1. 在 " **证书**" 中，选择 " **自定义**"。
1. 浏览以选择证书 .pfx 文件并输入其密码。
1. 选择 **添加** 。

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="上传客户端证书":::

上载证书后，它将显示在 " **证书** " 窗口中。 如果有多个证书，请记下所需证书的指纹，以便将 API 配置为使用客户端证书进行 [网关身份验证](#configure-an-api-to-use-client-certificate-for-gateway-authentication)。

> [!NOTE]
> 若要在使用自签名证书时关闭证书链验证，请按照本文后面的 [自签名证书](#self-signed-certificates)中所述的步骤进行操作。

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>将 API 配置为使用客户端证书进行网关身份验证

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在 " **api**" 下选择 " **api**"。
1. 从列表中选择一个 API。 
2. 在 " **设计** " 选项卡中，选择 " **后端** " 部分中的编辑器图标。
3. 在 " **网关凭据**" 中，选择 " **客户端证书** " 并从下拉列表中选择证书。
1. 选择“保存”。

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="使用客户端证书进行网关身份验证":::

> [!CAUTION]
> 此更改立即生效，对该 API 的操作的调用将使用证书在后端服务器上进行身份验证。

> [!TIP]
> 为 API 的后端服务指定网关身份验证的证书时，该证书会成为该 API 的策略的一部分，并且可以在策略编辑器中查看。

## <a name="self-signed-certificates"></a>自签名证书

如果你使用的是自签名证书，则将需要禁用证书链验证，以便 API 管理与后端系统进行通信。 否则，它将返回 500 错误代码。 若要对此进行配置，可以将 [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (用于现有后端的) 或 [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) () PowerShell cmdlet，并将 `-SkipCertificateChainValidation` 参数设置为 `True` 。

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>删除客户端证书

若要删除证书，请选择该证书，然后从上下文菜单中选择 "**删除** **" () "。**

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="删除证书":::

> [!IMPORTANT]
> 如果证书被任何策略引用，则会显示警告屏幕。 若要删除证书，必须首先从配置为使用该证书的任何策略中将其删除。

## <a name="next-steps"></a>后续步骤

* [如何使用 API 管理中的客户端证书身份验证确保 API 安全](api-management-howto-mutual-certificates-for-clients.md)
* 了解 [API 管理中的策略](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

