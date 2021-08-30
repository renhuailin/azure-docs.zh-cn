---
title: 如何在 Microsoft Azure Maps 中保护守护程序应用程序
titleSuffix: Azure Maps
description: 本文介绍如何在 Microsoft Azure Maps 的受信任且安全的环境中托管守护程序应用程序，例如后台进程、计时器和作业。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philema
custom.ms: subject-rbac-steps
ms.openlocfilehash: b888dccbd7ce5cbf948b2da5494dd554b2d80649
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112541763"
---
# <a name="secure-a-daemon-application"></a>保护守护程序应用程序

本文介绍如何在 Microsoft Azure Maps 的受信任且安全的环境中托管守护程序应用程序。

下面是守护程序应用程序的示例：

- Azure Web 作业
- Azure 函数应用
- Windows 服务
- 正在运行的可靠后台服务

## <a name="view-azure-maps-authentication-details"></a>查看 Azure Maps 身份验证详细信息

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

>[!IMPORTANT]
>对于生产应用程序，建议实现 Azure AD 和 Azure 基于角色的访问控制 (Azure RBAC)。 有关 Azure AD 概念的概述，请参阅 [Azure Maps 身份验证](azure-maps-authentication.md)。

## <a name="scenario-shared-key-authentication-with-azure-key-vault"></a>方案：使用 Azure 密钥保管库进行共享密钥身份验证

使用共享密钥身份验证的应用程序应将密钥存储在安全存储中。 此方案说明如何安全地将应用程序密钥作为机密存储在 Azure 密钥保管库中。 应用程序可以检索 Azure 密钥保管库机密形式的共享密钥，而不是将共享密钥存储在应用程序配置中。 为了简化密钥重新生成，建议让应用程序一次使用一个密钥。 然后，应用程序可以重新生成未使用的密钥，并将重新生成的密钥部署到 Azure 密钥保管库，同时仍可使用一个密钥保持当前连接。 若要了解如何配置 Azure 密钥保管库，请参阅 [Azure 密钥保管库开发人员指南](../key-vault/general/developers-guide.md)。

>[!IMPORTANT]
>此方案通过 Azure 密钥保管库间接访问 Azure Active Directory。 但是，建议直接使用 Azure AD 身份验证。 直接使用 Azure AD 可以避免使用共享密钥身份验证和设置密钥保管库，无需应对更高的复杂性与操作要求。

以下步骤概述了此过程：

1. [创建 Azure 密钥保管库](../key-vault/general/quick-create-portal.md)。
2. 通过创建应用注册或托管标识来创建 [Azure AD 服务主体](../active-directory/fundamentals/service-accounts-principal.md)。 创建的主体负责访问 Azure 密钥保管库。
3. 向服务主体分配对 Azure 密钥机密 `get` 权限的访问权限。 有关如何设置权限的详细信息，请参阅[使用 Azure 门户分配密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md)。
4. 为开发人员临时分配对机密 `set` 权限的访问权限。
5. 在密钥保管库机密中设置共享密钥，并引用机密 ID 作为守护程序应用程序的配置。
6. 删除机密 `set` 权限。
7. 若要从 Azure 密钥保管库检索共享密钥机密，请在守护程序应用程序中实现 Azure Active Directory 身份验证。
8. 使用共享密钥创建 Azure Maps REST API 请求。
现在，守护程序应用程序可以从密钥保管库检索共享密钥。

> [!TIP]
> 如果应用托管在 Azure 环境中，我们建议使用托管标识来降低管理身份验证机密的成本和复杂性。 若要了解如何设置托管标识，请参阅[教程：使用托管标识将密钥保管库连接到 .NET 中的 Azure Web 应用](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)。

## <a name="scenario-azure-ad-role-based-access-control"></a>方案：Azure AD 基于角色的访问控制

创建 Azure Maps 帐户后，Azure Maps `Client ID` 值就会出现在 Azure 门户身份验证详细信息页中。 此值表示要用于 REST API 请求的帐户。 此值应存储在应用程序配置中，并在发出 HTTP 请求之前进行检索。 此方案的目标是使守护程序应用程序能够向 Azure AD 进行身份验证，并调用 Azure Maps REST API。

> [!TIP]
>为了发挥托管标识组件的优势，我们建议在 Azure 虚拟机、虚拟机规模集或应用服务上进行托管。

### <a name="host-a-daemon-on-azure-resources"></a>在 Azure 资源上托管守护程序

在 Azure 资源上运行时，可以配置 Azure 管理的标识，以降低成本并最大程度地减少凭据管理工作量。

要使应用程序能够访问托管标识，请参阅[托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)。

托管标识的一些优势如下：

- Azure 系统管理的 X509 证书公钥加密身份验证。
- 使用 X509 证书（而不是客户端密码）保护 Azure AD 安全性。
- Azure 管理并续订与托管标识资源关联的所有证书。
- 由于托管标识不需要 Azure 密钥保管库之类的安全机密存储服务，因此简化了凭据操作管理。

### <a name="host-a-daemon-on-non-azure-resources"></a>在非 Azure 资源上托管守护程序

在非 Azure 环境中运行时，托管标识不可用。 因此，必须通过 Azure AD 应用程序注册为守护程序应用程序配置服务主体。

#### <a name="create-new-application-registration"></a>创建新的应用程序注册

如果已创建应用程序注册，请转到[分配委托的 API 权限](#assign-delegated-api-permissions)。

若要创建新的应用程序注册，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“Azure Active Directory” 。

3. 在左侧窗格中的“管理”下，选择“应用注册” 。

4. 选择“+ 新建注册”选项卡。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/app-registration.png" alt-text="查看应用注册。":::

5. 输入名称，然后选择一个支持帐户类型 。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-create.png" alt-text="创建应用注册。":::

6. 选择“注册”  。  

#### <a name="assign-delegated-api-permissions"></a>分配委托的 API 权限

若要将委托的 API 权限分配给 Azure Maps，请执行以下操作：

1. 如果尚未登录到 [Azure 门户](https://portal.azure.com)，请先登录。

2. 选择“Azure Active Directory” 。

3. 在左侧窗格中的“管理”下，选择“应用注册” 。

4. 选择自己的应用程序。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-select.png" alt-text="选择“应用注册”。":::

5. 在左侧窗格中的“管理”下，选择“API 权限” 。

6. 选择“添加权限”。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-add-permissions.png" alt-text="添加应用权限。":::

7. 选择“我的组织使用的 API”选项卡。

8. 在搜索框中输入“Azure Maps”。

9. 选择“Azure Maps”。

   :::image type="content" border="true" source="./media/how-to-manage-authentication/app-permissions.png" alt-text="请求应用权限。":::

10. 选中“访问 Azure Maps”复选框。

11. 选择“添加权限”。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/select-app-permissions.png" alt-text="选择应用 API 权限。":::

#### <a name="create-a-client-secret-or-configure-certificate"></a>创建客户端机密或配置证书

若要在应用程序中实现基于服务器或应用程序的身份验证，可选择以下两个选项之一：

- 上传公钥证书。
- 创建客户端机密。

##### <a name="upload-a-public-key-certificate"></a>上传公钥证书

若要上传公钥证书，请执行以下操作：

1. 在左侧窗格中的“管理”下，选择“证书和机密” 。

2. 选择“上传证书”。 
   :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate.png" alt-text="上传证书。":::

3. 在文本框的右侧，选择文件图标。

4. 选择 .crt、.cer 或 .pem 文件，然后选择“添加”  。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate-file.png" alt-text="上传证书文件。":::

##### <a name="create-a-client-secret"></a>创建客户端机密

若要创建客户端机密，请执行以下操作：

1. 在左侧窗格中的“管理”下，选择“证书和机密” 。

2. 选择“+ 新建客户端机密”。

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret.png" alt-text="新建客户端机密。":::

3. 输入客户端机密的说明。

4. 选择 **添加**。

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret-add.png" alt-text="添加新客户端机密。":::

5. 复制该机密并将其安全地存储在 Azure 密钥保管库之类的服务中。 在本文的[使用托管标识请求令牌](#request-a-token-with-managed-identity)部分，我们也要使用该机密。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/copy-client-secret.png" alt-text="复制客户端机密。":::

     >[!IMPORTANT]
     >若要安全地存储证书或机密，请参阅 [Azure 密钥保管库开发人员指南](../key-vault/general/developers-guide.md)。 你会使用此机密从 Azure AD 获取令牌。

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

### <a name="request-a-token-with-managed-identity"></a>使用托管标识请求令牌

为托管资源配置托管标识后，可以使用 Azure SDK 或 REST API 获取 Azure Maps 的令牌。 若要了解如何获取访问令牌，请参阅[获取访问令牌](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

### <a name="request-token-with-application-registration"></a>使用应用程序注册请求令牌

注册应用并将它与 Azure Maps 关联后，需要请求访问令牌。

若要获取访问令牌，请执行以下操作：

1. 如果尚未登录到 [Azure 门户](https://portal.azure.com)，请先登录。

2. 选择“Azure Active Directory” 。

3. 在左侧窗格中的“管理”下，选择“应用注册” 。

4. 选择自己的应用程序。

5. 此时应会显示“概述”页。 复制“应用程序(客户端) ID”和“目录(租户) ID”。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/get-token-params.png" alt-text="复制令牌参数。":::

我们将使用 [Postman](https://www.postman.com/) 应用程序创建令牌请求，但你也可以使用其他 API 开发环境。

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 为请求输入一个请求名称，例如“POST 令牌请求”。

4. 选择“POST”HTTP 方法。

5. 在地址栏中输入以下 URL（请将 `<Tenant ID>` 和 `<Client ID>` 分别替换为前面复制的“目录(租户) ID”和“应用程序(客户端) ID”，并将 `<Client Secret>` 替换为你的客户端机密）：

    ```http
    https://login.microsoftonline.com/<Tenant ID>/oauth2/v2.0/token?response_type=token&grant_type=client_credentials&client_id=<Client ID>&client_secret=<Client Secret>%3D&scope=api%3A%2F%2Fazmaps.fundamentals%2F.default
    ```

6. 选择“发送” 

7. 此时应会看到以下 JSON 响应：

```json
{
    "token_type": "Bearer",
    "expires_in": 86399,
    "ext_expires_in": 86399,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFq..."
}
```

有关身份验证流的详细信息，请参阅 [Microsoft 标识平台上的 OAuth 2.0 客户端凭据流](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)

## <a name="next-steps"></a>后续步骤

有关更多详细示例：
> [!div class="nextstepaction"]
> [Azure AD 的身份验证方案](../active-directory/develop/authentication-vs-authorization.md)

查找 Azure Maps 帐户的 API 使用指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示如何将 Azure AD 与 Azure Maps 集成的示例：
> [!div class="nextstepaction"]
> [Azure Maps 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
