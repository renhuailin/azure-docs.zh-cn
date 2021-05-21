---
title: 使用适用于 Azure AD 的 PingAccess 应用程序代理进行基于标头的身份验证
description: 使用 PingAccess 和应用代理发布应用程序，以支持基于标头的身份验证。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9a9ce7b2462978660db3ee2693933ae4fa451ec3
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108224469"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>使用应用程序代理和 PingAccess 通过基于标头的身份验证进行单一登录

Azure Active Directory (Azure AD) 应用程序代理与 PingAccess 合作，让你的 Azure AD 客户可访问更多应用程序。 除了集成的[基于标头的单一登录](application-proxy-configure-single-sign-on-with-headers.md)，PingAccess 还提供另一个选项。

## <a name="whats-pingaccess-for-azure-ad"></a>什么是 PingAccess for Azure AD？

通过 PingAccess for Azure AD，可使用户能够访问和单一登录 (SSO) 到使用标头进行身份验证的应用程序。 应用程序代理将这些应用程序视为与其他任何应用程序一样，它使用 Azure AD 对访问进行身份验证，然后通过连接器服务传递流量。 PingAccess 驻留在应用程序的前面，可将 Azure AD 提供的访问令牌转换为标头。 然后，应用程序接收采用可读格式的身份验证令牌。

用户登录后使用企业应用程序时，感觉不到任何差异。 他们仍可在任何位置的任何设备上工作。 应用程序代理连接器会将远程流量定向到所有应用（不管其身份验证类型如何），因此它们仍会自动均衡负载。

## <a name="how-do-i-get-access"></a>如何获取访问权限？

由于本方案是通过 Azure Active Directory 与 PingAccess 之间的合作实现的，因此需要获取这两款服务的许可证。 但是，Azure Active Directory Premium 订阅包含一个 PingAccess 基本许可证，可涵盖多达 20 个应用程序。 如果需要发布 20 个以上基于标头的应用程序，可以再从 PingAccess 购买一个许可证。

有关详细信息，请参阅 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="publish-your-application-in-azure"></a>在 Azure 中发布应用程序

本文面向首次使用此方案发布应用程序的人员。 除了详细介绍发布步骤以外，本文还指导你开始使用应用程序代理和 PingAccess。 如果已配置这两款服务，但只是想要重温发布步骤，可跳至[使用应用程序代理将应用程序添加到 Azure AD](#add-your-application-to-azure-ad-with-application-proxy) 部分。

> [!NOTE]
> 由于本方案是 Azure AD 与 PingAccess 之间的合作成果，某些说明已在 Ping Identity 站点上提供。

### <a name="install-an-application-proxy-connector"></a>安装应用程序代理连接器

如果已启用应用程序代理，并且已安装连接器，可跳过此部分转到[使用应用程序代理将应用程序添加到 Azure AD](#add-your-application-to-azure-ad-with-application-proxy)。

应用程序代理连接器是一项 Windows Server 服务，可将来自远程员工的流量定向到已发布的应用程序。 有关更详细的安装说明，请查看[教程：在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理远程访问](../app-proxy/application-proxy-add-on-premises-application.md)。

1. 以应用程序管理员身份登录到 [Azure Active Directory 门户](https://aad.portal.azure.com/)。 此时会显示“Azure Active Directory 管理中心”页。
1. 选择“Azure Active Directory” > “应用程序代理” > “下载连接器服务”  。 此时将显示“应用程序代理连接器下载”页面。

   ![应用程序代理连接器下载](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. 按照安装说明进行操作。

下载连接器时应会自动为目录启用应用程序代理，但如果没有，可选择“启用应用程序代理”。

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>使用应用程序代理将应用程序添加到 Azure AD

需要在 Azure 门户中执行两项操作。 首先，需要使用应用程序代理发布应用程序。 然后，需要收集应用程序的一些信息，以便在执行 PingAccess 步骤期间使用。

#### <a name="publish-your-application"></a>发布应用程序

首先需要发布应用程序。 操作涉及：

- 将本地应用程序添加到 Azure AD
- 分配用户来测试应用程序并选择基于标头的 SSO
- 设置应用程序的重定向 URL
- 为用户和其他应用程序授予使用本地应用程序的权限

若要发布自己的本地应用程序：

1. 以应用程序管理员身份登录到 [Azure Active Directory 门户](https://aad.portal.azure.com/)（如果在上一部分中未登录）。
1. 选择“企业应用程序” > “新应用程序” > “添加本地应用程序”  。 这会显示“添加自己的本地应用程序”页面。

   ![添加自己的本地应用程序](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 在必填字段中填写新应用程序的相关信息。 参考以下指导完成设置。

   > [!NOTE]
   > 有关此步骤的更详细演练，请查看[将本地应用添加到 Azure AD](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。

   1. **内部 URL**：通常提供在企业网络中时可将你转到应用登录页的 URL。 对于此方案，连接器需要将 PingAccess 代理视为应用程序的首页。 使用此格式：`https://<host name of your PingAccess server>:<port>`。 默认情况下端口为 3000，但可以在 PingAccess 中对其进行配置。

      > [!WARNING]
      > 对于此类型的单一登录，内部 URL 必须使用 `https`，不能使用 `http`。 此外，配置应用程序时存在约束，即两个应用不得具有相同的内部 URL，因为这会允许应用代理保留应用程序之间的差异。

   1. **预身份验证方法**：选择“Azure Active Directory”。
   1. **转换标头中的 URL**：选择“否”。

   > [!NOTE]
   > 如果这是第一个应用程序，则从使用端口 3000 开始，如果更改了 PingAccess 配置，则返回以更新此设置。 对于后续应用程序，端口将需要与你在 PingAccess 中配置的侦听器匹配。 详细了解 [PingAccess 中的侦听器](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)。

1. 选择 **添加** 。 此时将显示新应用程序的概述页面。

现在分配用户来测试应用程序，并选择基于标头的单一登录：

1. 从应用程序边栏中，选择“用户和组” > “添加用户” > “用户和组(已选择 \<Number> 个)”  。 此时会显示用户和组的列表供你选择。

   ![显示用户和组的列表](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. 选择要测试应用程序的用户，然后选中“选择”。 确保此测试帐户有权访问本地应用程序。
1. 选择“分配”。
1. 从应用程序边栏中，选择“单一登录” > “基于标头” 。

   > [!TIP]
   > 如果这是第一次使用基于标头的单一登录，则需安装 PingAccess。 为了确保 Azure 订阅与 PingAccess 安装自动关联，请使用此单一登录页上的链接下载 PingAccess。 可以现在就打开下载站点，也可以稍后返回到此页面。

   ![显示“基于标头的登录”屏幕和 PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. 选择“保存”。

然后，确保将重定向 URL 设置为外部 URL：

1. 在“Azure Active Directory 管理中心”边栏中，选择“Azure Active Directory” > “应用注册”  。 此时会显示应用程序列表。
1. 选择自己的应用程序。
1. 选择“重定向 URI”旁边的链接，其中显示为 Web 和公共客户端设置的重定向 URI 的数目。 此时会显示“\<application name> - 身份验证”页面。
1. 检查“重定向 URI”列表中是否有前面分配给应用程序的外部 URL。 如果没有，现在请使用重定向 URI 类型“Web”，然后选择“保存”来添加外部 URL 。

除了外部 URL，还应将外部 URL 上 Azure Active Directory 的授权终结点添加到重定向 URI 列表中。

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

最后，设置本地应用程序，使用户拥有读取访问权限，而其他应用程序具有读/写访问权限：

1. 从应用程序的“应用注册”边栏中，选择“API 权限” > “添加权限” > “Microsoft API” > “Microsoft Graph”    。 此时会显示 Microsoft Graph 的“请求 API 权限”页面，其中包含 Windows Azure Active Directory 的 API 。

   ![显示“请求 API 权限”页面](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. 选择“委托权限” > “用户” > “User.Read”  。
1. 选择“应用程序权限” > “应用程序” > “Application.ReadWrite.All”  。
1. 选择“添加权限”。
1. 在“API 权限”页面中，选择“为 \<your directory name> 授予管理员同意” 。

#### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步骤的信息

要通过 PingAccess 设置应用程序，需要收集以下三部分信息（所有 GUID）：

| Azure AD 名称字段 | PingAccess 名称字段 | 数据格式 |
| --- | --- | --- |
| **应用程序(客户端) ID** | **客户端 ID** | GUID |
| **目录（租户）ID** | **颁发者** | GUID |
| `PingAccess key` | **客户端机密** | 随机字符串 |

若要收集此信息：

1. 在“Azure Active Directory 管理中心”边栏中，选择“Azure Active Directory” > “应用注册”  。 此时会显示应用程序列表。
1. 选择自己的应用程序。 此时会显示应用程序的“应用注册”页面。

   ![应用程序的注册概述](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. 在“应用程序(客户端) ID”值旁边，选择“复制到剪贴板”图标，然后复制并保存 。 稍后将此值指定为 PingAccess 的客户端 ID。
1. 在“目录(租户) ID”值旁边，也选择“复制到剪贴板”，然后复制并保存 。 稍后将此值指定为 PingAccess 的颁发者。
1. 从应用程序的“应用注册”边栏中，选择“证书和机密” > “新建客户端密码”  。 此时会显示“添加客户端密码”页面。

   ![显示“添加客户端密码”页面](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. 在“说明”中，键入 `PingAccess key`。
1. 在“过期时间”下，选择设置 PingAccess 密钥的方式：“1 年”、“2 年”或“从不”   。
1. 选择 **添加** 。 此时客户端密码表中会显示 PingAccess 密钥，并在“值”字段中自动填充一个随机字符串。
1. 在 PingAccess 密钥的“值”字段旁边，选择“复制到剪贴板”图标，然后复制并保存 。 稍后将此值指定为 PingAccess 的客户端密码。

**更新 `acceptMappedClaims` 字段：**

1. 以应用程序管理员身份登录到 [Azure Active Directory 门户](https://aad.portal.azure.com/)。
1. 选择“Azure Active Directory” > “应用注册”。  此时会显示应用程序列表。
1. 选择自己的应用程序。
1. 从应用程序的“应用注册”页面的边栏中，选择“清单” 。 此时将显示应用程序注册的清单 JSON 代码。
1. 搜索 `acceptMappedClaims` 字段，并将值更改为 `True`。
1. 选择“保存”。

### <a name="use-of-optional-claims-optional"></a>使用可选声明（可选）

通过可选声明，可添加每个用户和租户具有的标准但不默认包含的声明。 可修改应用程序清单来配置应用程序的可选声明。 有关详细信息，请查看[“了解 Azure AD 应用程序清单”一文](../develop/reference-app-manifest.md)

例如，将电子邮件地址包含到 PingAccess 将使用的 access_token 中：

```json
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>使用声明映射策略（可选）

为 AzureAD 中不存在的属性[声明映射策略（预览）](../develop/reference-claims-mapping-policy-type.md#claims-mapping-policy-properties)。 通过声明映射，可添加 ADFS 或用户对象支持的其他自定义声明，来将旧的本地应用迁移到云

若要让应用程序使用自定义声明并包括其他字段，请确保还[创建了自定义声明映射策略并将其分配给应用程序](../develop/active-directory-claims-mapping.md)。

> [!NOTE]
> 若要使用自定义声明，还必须定义自定义策略并将其分配给应用程序。 此策略应包括所有必需的自定义属性。
>
> 可通过 PowerShell 或 Microsoft Graph 执行策略定义和分配。 如果在 PowerShell 中执行这些操作，可能需要先使用 `New-AzureADPolicy`，然后使用 `Add-AzureADServicePrincipalPolicy` 将新策略分配给应用程序。 有关详细信息，请查看[声明映射策略分配](../develop/active-directory-claims-mapping.md)。

示例：
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>使 PingAccess 能够使用自定义声明

可选择性地使 PingAccess 能够使用自定义声明，但如果希望应用程序使用其他声明，则需要此选项。

在以下步骤中配置 PingAccess 时，要创建的 Web 会话（“设置”->“访问”->“Web 会话”）必须取消选择“请求配置文件”，并将“刷新用户属性”设置为“否”  

## <a name="download-pingaccess-and-configure-your-application"></a>下载 PingAccess 并配置应用程序

完成 Azure Active Directory 的所有设置步骤后，可以开始配置 PingAccess。

有关此方案的 PingAccess 部分的详细步骤，可查看 Ping Identity 文档。 按照 Ping Identity 网站上[为 Azure AD 配置 PingAccess 来保护使用 Microsoft Azure AD 应用程序代理发布的应用程序](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)中的说明进行操作，并下载[最新版本的 PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?)。

这些步骤将帮助你安装 PingAccess 并设置 PingAccess 帐户（如果还没有帐户）。 然后，若要创建 Azure AD OpenID Connect (OIDC) 连接，请使用从 Azure AD 门户复制的“目录(租户) ID”值设置令牌提供程序。 接下来，若要在 PingAccess 上创建 Web 会话，请使用应用程序（客户端）ID 和 `PingAccess key` 值。 接下来，可以设置标识映射并创建虚拟主机、站点和应用程序。

### <a name="test-your-application"></a>测试应用程序

完成所有这些步骤后，应用程序应该会启动并运行。 若要测试应用程序，请打开浏览器,并导航到在 Azure 中发布该应用程序时创建的外部 URL。 使用分配给应用程序的测试帐户登录。

## <a name="next-steps"></a>后续步骤

- [为 Azure AD 配置 PingAccess 来保护使用 Microsoft Azure AD 应用程序代理发布的应用程序](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)