---
title: 如何保护守护程序应用程序
titleSuffix: Azure Maps
description: 使用 Azure 门户管理身份验证以配置受信任的守护程序应用程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895641"
---
# <a name="secure-a-daemon-application"></a>保护守护程序应用程序

以下指南适用于在受信任的安全环境中托管的后台进程、计时器和作业。 示例包括 Azure Web 作业、Azure 函数应用、Windows 服务和任何其他可靠的后台服务。

> [!Tip]
> Microsoft 建议为生产应用程序实现 Azure Active Directory (Azure AD) 和 Azure 基于角色的访问控制 (Azure RBAC)。 有关这些概念的概述，请参阅 [Azure Maps 身份验证](./azure-maps-authentication.md)。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>方案：共享密钥身份验证

创建 Azure Maps 帐户后，将生成主密钥和辅助密钥。 当[使用共享密钥身份验证以调用 Azure Maps](./azure-maps-authentication.md#shared-key-authentication) 时，建议使用主密钥作为订阅密钥。 在滚动密钥更改等方案下，可以使用辅助密钥。 有关详细信息，请参阅 [Azure Maps 中的身份验证](./azure-maps-authentication.md)。

### <a name="securely-store-shared-key"></a>安全存储共享密钥

主密钥和辅助密钥允许针对 Maps 帐户向所有 API 授权。 应用程序应将密钥存储在安全存储中（如 Azure 密钥保管库）。 应用程序必须以 Azure 密钥保管库机密的形式检索共享密钥，以避免在应用程序配置中以纯文本格式存储共享密钥。 若要了解如何配置 Azure 密钥保管库，请参阅 [Azure 密钥保管库开发人员指南](../key-vault/general/developers-guide.md)。

以下步骤概述了此过程：

1. 创建 Azure 密钥保管库。
2. 通过创建应用注册或托管标识来创建 Azure AD 服务主体，创建的主体负责访问 Azure 密钥保管库。
3. 向服务主体分配对 Azure 密钥机密 `Get` 权限的访问权限。
4. 为开发人员临时分配对机密 `Set` 权限的访问权限。
5. 在密钥保管库机密中设置共享密钥，并引用密钥 ID 作为守护程序应用程序的配置，然后删除机密 `Set` 权限。
6. 在守护程序应用程序中实现 Azure AD 身份验证，以从 Azure 密钥保管库检索共享密钥机密。
7. 使用共享密钥创建 Azure Maps REST API 请求。

> [!Tip]
> 如果应用在 Azure 环境中托管，则应实现托管标识，以降低为向 Azure 密钥保管库进行身份验证而管理机密的成本和复杂性。 请参阅以下 Azure 密钥保管库[教程以通过托管标识进行连接](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)。

守护程序应用程序负责从安全存储检索共享密钥。 Azure 密钥保管库的实现要求通过 Azure AD 进行身份验证以访问机密。 相反，我们鼓励将 Azure AD 身份验证定向到 Azure Maps，因为使用共享密钥身份验证会增加复杂性和操作要求。

> [!IMPORTANT]
> 为了简化密钥重新生成，建议应用程序一次使用一个密钥。 应用程序随后可以重新生成未使用的密钥，并将重新生成的新密钥部署到安全机密存储，例如 Azure 密钥保管库。

## <a name="scenario-azure-ad-role-based-access-control"></a>方案：Azure AD 基于角色的访问控制

创建 Azure Maps 帐户后，Azure Maps `x-ms-client-id` 值就会出现在 Azure 门户身份验证详细信息页中。 此值表示将用于 REST API 请求的帐户。 此值应存储在应用程序配置中，并在发出 HTTP 请求之前进行检索。 此方案的目标是使守护程序应用程序能够对 Azure AD 进行身份验证，并调用 Azure Maps REST API。

> [!Tip]
> 建议在 Azure 虚拟机、虚拟机规模集或应用服务上进行托管，以实现托管标识组件的好处。

### <a name="daemon-hosted-on-azure-resources"></a>在 Azure 资源上托管的守护程序

在 Azure 资源上运行时，请配置 Azure 托管标识，以实现低成本、最少的凭据管理工作。 

请参阅[托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)，使应用程序可以访问托管标识。

托管标识好处：

* Azure 系统管理的 X509 证书公钥加密身份验证。
* 使用 X509 证书（而不是客户端密码）保护 Azure AD 安全性。
* Azure 管理并续订与托管标识资源关联的所有证书。
* 无需使用安全机密存储服务（如Azure 密钥保管库），从而简化了凭据操作管理。 

### <a name="daemon-hosted-on-non-azure-resources"></a>在非 Azure 资源上托管的守护程序

在非 Azure 环境中运行时，托管标识不可用。 因此，必须通过 Azure AD 应用程序注册为守护程序应用程序配置服务主体。

1. 在 Azure 门户的 Azure 服务列表中，依次选择“Azure Active Directory” > “应用注册” > “新注册”。    

    > [!div class="mx-imgBorder"]
    > ![应用注册](./media/how-to-manage-authentication/app-registration.png)

2. 如果已注册应用，请继续执行下一步。 如果尚未注册应用，请输入“名称”，选择“支持帐户类型”，然后选择“注册”  。  

    > [!div class="mx-imgBorder"]
    > ![应用注册详细信息](./media/how-to-manage-authentication/app-create.png)

3. 若要将委托的 API 权限分配给 Azure Maps，请访问应用程序。 然后在“应用注册”下，选择“API 权限” > “添加权限”。 在“我的组织使用的 API”下，搜索并选择“Azure Maps” 。

    > [!div class="mx-imgBorder"]
    > ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

4. 选中“访问 Azure Maps”旁边的复选框，然后选择“添加权限” 。

    > [!div class="mx-imgBorder"]
    > ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

5. 完成以下步骤以创建客户端密码或配置证书。

    * 如果应用程序使用服务器或应用程序身份验证，则在应用注册页上，转到“证书和机密”。 然后上传公钥证书或是通过选择“新客户端密码”来创建密码。

        > [!div class="mx-imgBorder"]
        > ![创建客户端机密](./media/how-to-manage-authentication/app-keys.png)

    * 选择“添加”后，复制机密并将它安全地存储在诸如 Azure 密钥保管库此类的服务中。 查看 [Azure 密钥保管库开发人员指南](../key-vault/general/developers-guide.md)以安全地存储证书或机密。 你会使用此机密从 Azure AD 获取令牌。

        > [!div class="mx-imgBorder"]
        > ![添加客户端密码](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>为守护程序应用程序授予对 Azure Maps 的基于角色的访问权限

通过将创建的托管标识或服务主体分配给一个或多个 Azure Maps 角色定义，来授予 Azure 基于角色的访问控制 (Azure RBAC)。 若要查看可用于 Azure Maps 的 Azure 角色定义，请转到“访问控制(IAM)”。 选择“角色”，然后搜索以“Azure Maps”开头的角色。 这些 Azure Maps 角色是可以向其授予访问权限的角色。

> [!div class="mx-imgBorder"]
> ![查看可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. 转到你的 Azure Maps 帐户。 选择“访问控制(IAM)” > “角色分配”。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure RBAC 授予访问权限](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在“角色分配”选项卡上，“添加”角色分配 。 

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示角色分配，其中选择了“添加”。](./media/how-to-manage-authentication/add-role-assignment.png)

3. 选择内置 Azure Maps 角色定义，例如“Azure Maps 数据读取器”或“Azure Maps 数据参与者” 。 在“将访问权限分配到”下，选择“Azure AD 用户、组或服务主体”或是具有“用户分配的托管标识” / “系统分配的托管标识”的托管标识   。 选择主体。 再选择“保存”。

    > [!div class="mx-imgBorder"]
    > ![如何添加角色分配](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. 可以在角色分配选项卡上确认已应用了角色分配。

## <a name="request-token-with-managed-identity"></a>使用托管标识请求令牌

为托管资源配置托管标识后，使用 Azure SDK 或 REST API 获取 Azure Maps 的令牌，请参阅有关[获取访问令牌](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)的详细信息。 按照指南，预期会返回一个访问令牌，该令牌可用于 REST API 请求。

## <a name="request-token-with-application-registration"></a>使用应用程序注册请求令牌

注册应用并将它与 Azure Maps 关联后，可以请求访问令牌。

* Azure AD 资源 ID `https://atlas.microsoft.com/`
* Azure AD 应用 ID
* Azure AD 租户 ID
* Azure AD 应用注册客户端密码

请求：

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

响应：

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

有关更多详细示例，请参阅 [Azure AD 的身份验证方案](../active-directory/develop/authentication-vs-authorization.md)。

## <a name="next-steps"></a>后续步骤

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

探索演示如何将 Azure AD 与 Azure Maps 集成的示例：
> [!div class="nextstepaction"]
> [Azure Maps 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)