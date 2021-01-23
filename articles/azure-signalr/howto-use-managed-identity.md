---
title: Azure SignalR 服务中的托管标识
description: 了解托管标识在 Azure SignalR 服务中的工作原理，以及如何在无服务器方案中使用托管标识。
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731578"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Azure SignalR 服务的托管标识

本文介绍了如何为 Azure SignalR 服务创建托管标识，以及如何在无服务器方案中使用它。

> [!Important] 
> Azure SignalR 服务只能支持一个托管标识。 这意味着，你只能添加系统分配的标识或用户分配的标识。 

## <a name="add-a-system-assigned-identity"></a>添加系统分配的标识

若要在 Azure 门户中设置托管标识，需要首先创建一个 Azure SignalR 服务实例，然后启用该功能。

1. 像往常一样在门户中创建 Azure SignalR 服务实例。 在门户中浏览到它。

2. 选择“标识”。

4. 在“系统分配”选项卡中，将“状态”切换为“启用”  。 选择“保存” 。

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="在门户中添加系统分配的标识":::

## <a name="add-a-user-assigned-identity"></a>添加用户分配的标识

若要使用用户分配的标识创建 Azure SignalR 服务实例，需要先创建该标识，然后将其资源标识符添加到你的服务。

1. 根据[这些说明](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)创建用户分配的托管标识资源。

2. 像往常一样在门户中创建 Azure SignalR 服务实例。 在门户中浏览到它。

3. 选择“标识”。

4. 在“用户分配”选项卡上，选择“添加”。

5. 搜索之前创建的标识并选择它。 选择“添加”  。

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="在门户中添加用户分配的标识":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>在无服务器方案中使用托管标识

Azure SignalR 服务是一种完全托管的服务，因此你不能使用托管标识来手动获取令牌， 而只能让 Azure SignalR 服务使用你设置的托管标识来获取访问令牌。 然后，该服务将访问令牌设置为无服务器方案的上游请求中的 `Authorization` 标头。

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>在上游设置中启用托管标识身份验证

1. 添加系统分配的标识或用户分配的标识。

2. 添加一个上游设置，然后单击任何星号进入如下所示的详细页面。
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="pre-msi-setting":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. 在托管标识身份验证设置中，对于“资源”，你可以指定目标资源。 资源将成为获取的访问令牌中的 `aud` 声明，可在上游终结点中用作验证的一部分。 资源可以是下列值之一：
    - 空
    - 服务主体的应用程序（客户端）ID
    - 服务主体的应用程序 ID URI
    - [Azure 服务的资源 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > 如果你在服务中亲自验证访问令牌，则可选择任何一种资源格式。 只需要确保“身份验证”设置中的“资源”值与验证一致即可。 如果对数据平面使用 Azure 基于角色的访问控制 (Azure RBAC)，则必须使用服务提供商请求的资源。

### <a name="validate-access-tokens"></a>验证访问令牌

`Authorization` 标头中的令牌是 [Microsoft 标识平台访问令牌](../active-directory/develop/access-tokens.md#validating-tokens)。

若要验证访问令牌，你的应用还应当验证受众和签名令牌。 这些需要根据 OpenID 发现文档中的值进行验证。 有关示例，请参阅[文档的独立于租户的版本](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

Azure Active Directory (Azure AD) 中间件具有用于验证访问令牌的内置功能。 你可以浏览我们的[示例](../active-directory/develop/sample-v2-code.md)来查找所选语言的示例。

我们提供了库和代码示例，用以演示如何轻松处理令牌验证。 还有多个可用于 JSON Web 令牌 (JWT) 验证的开源合作伙伴库。 几乎针对每种平台和语言都提供了至少一个选项。 有关 Azure AD 身份验证库和代码示例的详细信息，请参阅 [Microsoft 标识平台身份验证库](../active-directory/develop/reference-v2-libraries.md)。

#### <a name="authentication-in-function-app"></a>函数应用中的身份验证

无需代码即可轻松有效地在函数应用中设置访问令牌验证。

1. 在“身份验证/授权”页中，将“应用服务身份验证”切换为“开”  。

2. 在“请求未经身份验证时需执行的操作”中，选择“使用 Azure Active Directory 登录” 。

3. 在“身份验证提供程序”中，单击“Azure Active Directory”

4. 在新页中。 选择“快速”和“创建新的 AD 应用”，然后单击“确定”:::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="函数 AAD":::  

5. 导航到 SignalR 服务并按照[步骤](howto-use-managed-identity.md#add-a-system-assigned-identity)添加系统分配的标识或用户分配的标识。

6. 在 SignalR 服务中进入“上游设置”，然后选择“使用托管标识”和“从现有应用程序中选择”  。 选择之前创建的应用程序。

完成这些设置后，函数应用将拒绝标头中没有访问令牌的请求。

## <a name="use-a-managed-identity-for-key-vault-reference"></a>使用托管标识实现 Key Vault 引用

SignalR 服务可以使用托管标识访问 Key Vault 以获取机密。

1. 为 Azure SignalR 服务添加系统分配的标识或用户分配的标识。

2. 在 Key Vault 的访问策略中授予对托管标识的机密读取权限。 请参阅[使用 Azure 门户分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

目前，此功能可用于以下方案：

- [在上游 URL 模式中引用机密](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>后续步骤

- [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)