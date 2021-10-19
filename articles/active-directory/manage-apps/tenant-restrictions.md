---
title: 使用租户限制管理对 SaaS 应用的访问
description: 如何使用租户限制来根据用户的 Azure AD 租户管理可访问应用的用户。
titleSuffix: Azure AD
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/30/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 029328313ac58a5177f5a5e3c75b86fdc3587b43
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613731"
---
# <a name="restrict-access-to-a-tenant-in-azure-active-directory"></a>在 Azure Active Directory 中限制对租户的访问权限

重视安全的大型组织都希望迁移到 Microsoft 365 等云服务，但需要知道，其用户只能访问已批准的资源。 在传统上，公司在想要管理访问权限时，会限制域名或 IP 地址。 如果软件即服务 (SaaS) 应用在公有云中托管并在类似于 [outlook.office.com](https://outlook.office.com/) 和 [login.microsoftonline.com](https://login.microsoftonline.com/) 的共享域名中运行，则这种做法会失败。 阻止这些地址会导致用户完全无法访问 Web 上的 Outlook，而不只是将他们能够访问的内容局限于批准的标识和资源。

为了解决这个难题，Azure Active Directory (Azure AD) 解决方案推出了一项称作“租户限制”的功能。 借助租户限制，组织可以根据应用程序用于单一登录的 Azure AD 租户来控制对 SaaS 云应用程序的访问。 例如，你可能希望允许用户访问你所在组织的 Microsoft 365 应用程序，同时阻止他们访问其他组织中这些应用程序的实例。  

借助租户限制，组织可以指定其用户有权访问的租户的列表。 然后，Azure AD 只会授予对这些允许的租户的访问权限。

本文将重点介绍 Microsoft 365 的租户限制，但该功能可防止所有应用将用户发送到 Azure AD 进行单一登录。 如果用于 SaaS 应用的 Azure AD 租户与 Microsoft 365 使用的租户不同，请确保允许所有必需的租户（例如，在 B2B 协作方案中）。 有关 SaaS 云应用的详细信息，请参阅 [Active Directory 市场](https://azuremarketplace.microsoft.com/marketplace/apps)。

此外，租户限制功能现在支持[阻止使用所有 Microsoft 使用者应用程序](#blocking-consumer-applications)（MSA 应用），例如 OneDrive、Hotmail 和 Xbox.com。  因此，将对 `login.live.com` 终结点使用单独的标头，请参阅本文档末尾部分了解详细信息。

## <a name="how-it-works"></a>工作原理

总体解决方案由以下组件构成：

1. **Azure AD**：如果指定了 `Restrict-Access-To-Tenants: <permitted tenant list>` 标头，Azure AD 只会针对允许的租户颁发安全令牌。

2. **本地代理服务器基础结构**：此基础结构是能够进行传输层安全性 (TLS) 检查的代理设备。 必须将代理配置为在发往 Azure AD 的流量中插入包含允许租户列表的标头。

3. **客户端软件**：为了支持租户限制，客户端软件必须直接从 Azure AD 请求令牌，使代理基础结构能够截获流量。 基于浏览器的 Microsoft 365 应用程序目前支持租户限制，使用 OAuth 2.0 等新式身份验证的 Office 客户端也支持租户限制。

4. **新式身份验证**：云服务必须使用新式身份验证来使用租户限制，阻止对所有不允许租户的访问。 必须将 Microsoft 365 云服务配置为默认使用新式身份验证协议。 有关 Microsoft 365 对新式身份验证支持的最新信息，请参阅[更新的 Office 365 新式身份验证](/microsoft-365/enterprise/modern-auth-for-office-2013-and-2016)。

下图演示了大致的流量流。 租户限制仅要求针对发往 Azure AD 的流量执行 TLS 检查，对于发往 Microsoft 365 云服务的流量，不需要执行此检查。 这种区别很重要，因为 Azure AD 的身份验证流量通常比 SaaS 应用程序（如 Exchange Online 和 SharePoint Online）的流量要小得多。

![租户限制流量流 - 示意图](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>设置租户限制

需要完成两个步骤才能开始使用租户限制。 首选，确保客户端能够连接到正确的地址。 其次，配置代理基础结构。

### <a name="urls-and-ip-addresses"></a>URL 和 IP 地址

要使用租户限制，客户端必须能够连接到以下 Azure AD URL 进行身份验证：[login.microsoftonline.com](https://login.microsoftonline.com/)、[login.microsoft.com](https://login.microsoft.com/) 和 [login.windows.net](https://login.windows.net/)。 此外，要访问 Office 365，客户端还必须能够连接到 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中定义的完全限定域名 (FQDN)、URL 和 IP 地址。

### <a name="proxy-configuration-and-requirements"></a>代理配置和要求

需要完成以下配置，以便通过代理基础结构启用租户限制。 本指南的内容是通用的，有关具体的实施步骤，请参阅代理供应商的文档。

#### <a name="prerequisites"></a>先决条件

- 代理必须能够使用 FQDN/URL 执行 TLS 截获、HTTP 标头插入和目标筛选。

- 客户端必须信任代理提供的用于 TLS 通信的证书链。 例如，如果使用了来自内部[公钥基础结构 (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) 的证书，则必须信任内部根证书颁发机构颁发的证书。

- 使用租户限制需要 Azure AD Premium 1 许可证。

#### <a name="configuration"></a>配置

对于 login.microsoftonline.com、login.microsoft.com 和 login.windows.net 的每个传入请求，请插入两个 HTTP 标头：Restrict-Access-To-Tenants 和 Restrict-Access-Context 。

> [!NOTE]
> 不要在代理配置的 `*.login.microsoftonline.com` 下包含子域。 这样做会加入 device.login.microsoftonline.com，并会干扰设备注册和基于设备的条件访问方案中使用的客户端证书身份验证。 配置代理服务器，将 device.login.microsoftonline.com 排除在 TLS 中断并检查和标头注入外。

这些标头应包含以下元素：

- 对于 Restrict-Access-To-Tenants，使用 \<permitted tenant list\> 值，即希望允许用户访问的租户的逗号分隔列表。 已注册到某个租户的任何域都可用于在此列表中标识该租户，以及目录 ID 本身。 以描述租户的所有三种方式为例，允许 Contoso、Fabrikam 和 Microsoft 的名称/值对如下所示：`Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- 对于 Restrict-Access-Context，应使用单个目录 ID 的值，用于声明哪个租户将要设置租户限制。 例如，要将 Contoso 声明为设置租户限制策略的租户，名称/值对如下所示：`Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`。  在此位置，必须使用自己的目录 ID 才能获取这些身份验证的日志。

> [!TIP]
> 可在 [Azure Active Directory 门户](https://aad.portal.azure.com/)中找到该目录 ID。 以管理员身份登录，选择“Azure Active Directory”，再选择“属性”即可。 
>
> 要验证目录 ID 或域名是否引用了同一个租户，请使用该 ID 或域代替 URL `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` 中的 \<tenant\>。  如果域和 ID 的结果相同，则指同一租户。

为了防止用户插入其自己的包含未批准租户的 HTTP 标头，代理需要替换 Restrict-Access-To-Tenants 标头（如果传入的请求中已提供此标头）。

必须强制客户端针对发往 login.microsoftonline.com、login.microsoft.com 和 login.windows.net 的所有请求使用代理。 例如，如果使用 PAC 文件来指示客户端使用代理，则不应该允许最终用户编辑或禁用这些 PAC 文件。

## <a name="the-user-experience"></a>用户体验

本部分介绍最终用户和管理员的体验。

### <a name="end-user-experience"></a>最终用户体验

例如，某个用户身处 Contoso 网络，但该用户正在尝试访问 Fabrikam 的某个共享 SaaS 应用程序的实例（如 Outlook Online）。 如果 Fabrikam 是 Contoso 实例的不允许租户，则该用户会看到“拒绝访问”消息，这表明用户正在尝试访问组织内部 IT 部门未批准的资源。

![2021 年 4 月的租户限制错误消息](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>管理员体验

尽管租户限制的配置是在企业代理基础结构中完成的，但管理员也可以直接在 Azure 门户中访问租户限制报告。 查看报告：

1. 登录到 [Azure Active Directory 门户](https://aad.portal.azure.com/)。 随即显示“Azure Active Directory 管理中心”仪表板。

2. 在左窗格中选择“Azure Active Directory”。 随即显示 Azure Active Directory 概述页面。

3. 在“概述”页上，选择“租户限制”。

Restricted-Access-Context 租户的管理员可以使用此报告来查看由于租户限制策略而被阻止的登录，包括使用的标识和目标目录 ID。 如果设置限制的租户是登录的用户租户或资源租户，则会包含登录。

当 Restricted-Access-Context 租户之外的其他租户中的用户登录时，该报告可能包含有限的信息，例如目标目录 ID。 在这种情况下，用户身份信息（例如，名称和用户主体名称）将被过滤，以保护其他租户中的用户数据（“{已删除 PII}@domain.com”或根据需要使用 00000000-0000-0000-0000-000000000000 代替用户名和对象 ID）。

与使用 Azure 门户中的其他报告时一样，可以使用筛选器来指定报告的范围。 可以根据特定的时间间隔、用户、应用程序、客户端或状态进行筛选。 如果选择“列”按钮，则可以选择通过任意组合以下字段来显示数据：

- 用户 - 此字段可以删除个人数据，它将被设置为 `00000000-0000-0000-0000-000000000000`。
- **应用程序**
- **Status**
- **Date**
- 日期 (UTC) - 其中 UTC 是协调世界时
- **IP 地址**
- **客户端**
- 用户名 - 此字段可以删除个人数据，它将被设置为 `{PII Removed}@domain.com`
- **位置**
- **目标租户 ID**

## <a name="microsoft-365-support"></a>Microsoft 365 支持

Microsoft 365 应用程序必须满足两个条件才能完全支持租户限制：

1. 使用的客户端支持新式身份验证。
2. 已启用新式身份验证作为云服务的默认身份验证协议。

有关哪些 Office 客户端目前支持新式身份验证的最新信息，请参阅 [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)（更新的 Office 365 新式身份验证）。 该页面还包含有关如何在特定的 Exchange Online 和 Skype for Business Online 租户中启用新式身份验证的说明文档链接。 SharePoint Online 已默认启用新式身份验证。 Teams 仅支持新式身份验证，不支持旧式身份验证，因此免验证不适用于 Teams。 

Microsoft 365 基于浏览器的应用程序（Office 门户、Yammer、SharePoint 网站、Outlook 网页版等）目前支持租户限制。 胖客户端（Outlook、Skype for Business、Word、Excel、PowerPoint 等）仅在使用新式身份验证时才可强制实施租户限制。  

支持新式身份验证的 Outlook 和 Skype for Business 客户端仍可针对未启用新式身份验证的租户使用旧版协议，从而有效绕过租户限制。 如果在身份验证期间访问 login.microsoftonline.com、login.microsoft.com 或 login.windows.net，使用旧版协议的应用程序可能会被租户限制阻止。

对于 Windows 上的 Outlook，客户可能选择实施限制来阻止最终用户将未经批准的邮件帐户添加到其配置文件。 有关示例，请参阅[阻止添加非默认 Exchange 帐户](https://gpsearch.azurewebsites.net/default.aspx?ref=1)组策略设置。

## <a name="testing"></a>测试

在整个组织中实施租户限制之前如果想要试用，可采取两种做法：使用 Fiddler 等工具基于主机实施租户限制，或者分阶段推出代理设置。

### <a name="fiddler-for-a-host-based-approach"></a>基于主机的实施方法所用的 Fiddler

Fiddler 是一个免费 Web 调试代理，可用于捕获和修改 HTTP/HTTPS 流量，包括插入 HTTP 标头。 要配置 Fiddler 来测试租户限制，请执行以下步骤：

1. [下载并安装 Fiddler](https://www.telerik.com/fiddler)。

2. 根据 [Fiddler 的帮助文档](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)，配置 Fiddler 来解密 HTTPS 流量。

3. 配置 Fiddler，使用自定义规则插入 *Restrict-Access-To-Tenants* 和 *Restrict-Access-Context* 标头：

   1. 在 Fiddler Web 调试器工具中选择“规则”菜单，并选择“自定义规则...”  打开 CustomRules 文件。

   2. 将以下行添加到 `OnBeforeRequest` 函数的开头。 将 \<List of tenant identifiers\> 替换为已注册到租户的域（例如 `contoso.onmicrosoft.com`）。 将 \<directory ID\> 替换为租户的 Azure AD GUID 标识符。  必须包含正确的 GUID 标识符，租户中才能显示日志。

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   如果需要允许多个租户，请使用逗号分隔租户名称。 例如：

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. 保存并关闭 CustomRules 文件。

配置 Fiddler 后，可转到“文件”菜单并选择“捕获流量”来捕获流量。 

### <a name="staged-rollout-of-proxy-settings"></a>分阶段部署代理设置

根据代理基础结构的功能，有时可以分阶段向用户部署设置。 请考虑下面几个高级选项：

1. 使用 PAC 文件将测试用户指向测试代理基础结构，普通用户可继续使用生产代理基础结构。
2. 某些代理服务器可以使用组来支持不同的配置。

有关具体细节，请参阅代理服务器文档。

## <a name="blocking-consumer-applications"></a>阻止使用者应用程序

Microsoft 中支持使用者帐户和组织帐户的应用程序（如 [OneDrive](https://onedrive.live.com/) 或 [Microsoft Learn](/learn/)）有时可能会托管在同一个 URL 上。  这意味着，因工作目的必须访问该 URL 的用户也可以因个人用途而访问该 URL，这可能违反你的操作准则。

有些组织尝试通过阻止 `login.live.com` 来阻止个人帐户进行身份验证，由此修复此问题。  这样做有几个缺点：

1. 阻止 `login.live.com` 会阻止在 B2B 来宾方案中使用个人帐户，从而侵扰访客和协作。
1. [Autopilot 要求使用 `login.live.com`](/mem/autopilot/networking-requirements) 进行部署。 当 `login.live.com` 被阻止时，Intune 和 Autopilot 方案可能会失败。
1. 依赖于 login.live.com 服务获取设备 ID 进行的组织遥测和 Windows 更新[将停止工作](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are)。

### <a name="configuration-for-consumer-apps"></a>使用者应用的配置

当 `Restrict-Access-To-Tenants` 标头充当允许列表时，Microsoft 帐户 (MSA) 块将作为拒绝信号，告诉 Microsoft 帐户平台不允许用户登录到使用者应用程序。 要发送此信号，请将 `sec-Restrict-Tenant-Access-Policy` 标头注入到使用[上述](#proxy-configuration-and-requirements)相同公司代理或防火墙访问 `login.live.com` 的流量中。 此标头的值必须为 `restrict-msa`。 如果该标头存在且使用者应用尝试直接登录用户，登录将被阻止。

目前，由于 login.live.com 与 Azure AD 分开托管，[管理日志](#admin-experience)中不显示对使用者应用程序的身份验证。

### <a name="what-the-header-does-and-does-not-block"></a>标头阻止和不会阻止的项目

`restrict-msa` 策略会阻止使用使用者应用程序，但允许通过多种其他类型的流量和身份验证：

1. 面向设备的无用户流量。  这包括面向 Autopilot、Windows 更新和组织遥测的流量。
1. 使用者帐户的 B2B 身份验证。 [受邀与租户协作](../external-identities/redemption-experience.md#invitation-redemption-flow)的 Microsoft 帐户的用户将通过身份验证登录 login.live.com，以便访问资源租户。
    1. 此访问权限由 `Restrict-Access-To-Tenants` 标头进行控制，以允许或拒绝对该资源租户的访问。
1. 许多 Azure 应用和 Office.com 都使用“传递”身份验证，对此应用使用 Azure AD 在使用者上下文中登录使用者用户。
    1. 此访问权限也由 `Restrict-Access-To-Tenants` 标头进行控制，以允许或拒绝对该特殊“传递”租户 (`f8cdef31-a31e-4b4a-93e4-5f571e91255a`) 的访问。  如果此租户不在允许域的 `Restrict-Access-To-Tenants` 列表中，Azure AD 将阻止使用者帐户登录到这些应用。

## <a name="next-steps"></a>后续步骤

- 阅读 [Updated Office 365 modern authentication](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)（更新的 Office 365 新式身份验证）
- 查看 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
