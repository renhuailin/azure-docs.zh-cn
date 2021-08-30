---
title: 使用 Connect Health 在 Azure AD 中进行 AD FS 登录 | Microsoft Docs
description: 本文档介绍如何将 AD FS 登录与 Azure AD Connect Health 登录报告相集成。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 890bf61eb98218df53a6aa66c9e8562881b8d2df
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895591"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>使用 Connect Health 在 Azure AD 中进行 AD FS 登录 - 预览

现在，可以使用 Connect Health 将 AD FS 登录集成到 Azure Active Directory 登录报告中。 [Azure AD 登录报告](../reports-monitoring/concept-all-sign-ins.md)包含有关用户、应用程序和受管理资源何时登录到 Azure AD 并访问资源的信息。 

Connect Health for AD FS 代理会关联 AD FS 中的多个事件 ID（具体取决于服务器版本），以便在请求失败时提供有关请求的信息和错误详细信息。 此信息将关联到 Azure AD 登录报告架构，并显示在 Azure AD 登录报告 UX 中。 除报告以外，还会连同 AD FS 数据以及新的 Azure Monitor 工作簿模板一起提供新的 Log Analytics 流。 可以使用并修改该模板，以便对 AD FS 帐户锁定、错误的密码尝试和出现意外登录尝试高峰等场景进行深入分析。

## <a name="prerequisites"></a>先决条件
* Azure AD Connect Health for AD FS 已安装并已升级到最新版本（3.1.95.0 或更高版本）。
* 用于查看 Azure AD 登录的全局管理员或报告读取者角色

## <a name="what-data-is-displayed-in-the-report"></a>报告中显示哪些数据？
可用的数据反映了为 Azure AD 登录过程提供的数据。将根据登录类型（Azure AD 或 AD FS）提供五个包含信息的选项卡。 Connect Health 关联 AD FS 中的事件（具体取决于服务器版本），并将其与 AD FS 架构相匹配。 



#### <a name="user-sign-ins"></a>用户登录 
登录边栏选项卡中的每个选项卡显示以下默认值：
* 登录日期
* 请求 ID
* 用户名或用户 ID
* 登录的状态
* 用于登录的设备的 IP 地址
* 登录标识符

#### <a name="authentication-method-information"></a>身份验证方法信息
身份验证选项卡中可能会显示以下值。身份验证方法取自 AD FS 审核日志。

|身份验证方法|说明|
|-----|-----|
|窗体|用户名/密码身份验证|
|Windows|Windows 集成身份验证|
|证书|使用智能卡/VirtualSmart 证书进行身份验证|
|WindowsHelloForBusiness|此字段适用于通过 Windows Hello 企业版进行的身份验证。 （Microsoft Passport 身份验证）|
|设备 | 如果选择“设备身份验证”作为 Intranet/Extranet 中的“主要”身份验证并执行设备身份验证，则会显示此方法。  在这种情况下没有单独的用户身份验证。| 
|联合|AD FS 未执行身份验证，而是将其发送到了第三方标识提供者|
|SSO |如果使用了单一登录令牌，则会显示此字段。 如果 SSO 包含 MFA，该字段将显示为“多重”|
|多重|如果单一登录令牌包含 MFA 并使用了该 MFA 方法进行身份验证，则该字段将显示为“多重”|
|Azure MFA|Azure MFA 已选作 AD FS 中的附加身份验证提供程序并已用于身份验证|
|ADFSExternalAuthenticationProvider|此字段指示是否已注册第三方身份验证提供程序并将其用于身份验证|


#### <a name="ad-fs-additional-details"></a>AD FS 更多详细信息
将提供 AD FS 登录的以下详细信息：
* 服务器名称
* IP 链
* 协议

### <a name="enabling-log-analytics-and-azure-monitor"></a>启用 Log Analytics 和 Azure Monitor
可为 AD FS 登录启用 Log Analytics，并可将 Log Analytics 与 Log Analytics 集成的任何其他组件（例如 Sentinel）配合使用。

> [!NOTE] 
> AD FS 登录可能会显著提高 Log Analytics 成本，具体取决于组织中的 AD FS 登录量。 若要启用和禁用 Log Analytics，请选中流对应的复选框。

若要为该功能启用 Log Analytics，请导航到“Log Analytics”边栏选项卡并选择“ADFSSignIns”流。 做出此项选择将允许 AD FS 登录流入 Log Analytics。

若要访问更新的 Azure Monitor 工作簿模板，请导航到“Azure Monitor 模板”并选择“登录”工作簿。
有关工作簿的详细信息，请访问 [Azure Monitor 工作簿](https://aka.ms/adfssigninspreview)。




### <a name="frequently-asked-questions"></a>常见问题
***我可以看到哪些类型的登录？***
登录报告支持通过 O-Auth、WS-Fed、SAML 和 WS-Trust 协议登录。 

***登录报告中如何显示不同类型的登录？***
如果执行了无缝 SSO 登录，将为该登录显示一行，其中包含一个相关 ID。
如果执行了单重身份验证，将在两行中填充同一相关 ID，但还会在两行中填充两种不同的身份验证方法（例如 Forms、SSO）。
如果执行了多重身份验证，则会显示三行，其中包含共享的相关 ID 和三种对应的身份验证方法（例如 Forms、AzureMFA、多重）。 此特定示例中的身份验证方法是“多重”，其中显示 SSO 包含 MFA。

***在报告中可以看到哪些错误？***
有关在登录报告和说明中填充的 AD FS 相关错误完整列表，请访问 [AD FS 帮助 - 错误代码参考](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)

***我在登录信息的“用户”部分看到了“00000000-0000-0000-0000-000000000000”。这是什么意思？***
如果登录失败并且尝试的 UPN 与现有 UPN 不匹配，则“用户”、“用户名”和“用户 ID”字段将显示“00000000-0000-0000-0000-000000000000”，并会在“登录标识符”中填充用户输入的尝试值。 在这种情况下，尝试登录的用户并不存在。

***如何将本地事件关联到 Azure AD 登录报告？***
Azure AD Connect Health for AD FS 代理会关联 AD FS 中的事件 ID（具体取决于服务器版本）。 这些事件将在 AD FS 服务器的安全日志中提供。 

***某些 AD FS 登录操作的应用程序 ID/名称中为何显示 NotSet 或 NotApplicable？***
对于 OAuth 登录操作，AD FS 登录报表中的“应用程序 ID”字段会显示 OAuth ID。在 WS-Fed、WS-Trust 登录方案中，“应用程序 ID”将是 NotSet 或 NotApplicable，“资源 ID”字段中将显示“资源 ID”和“信赖方”标识符。

为什么我看到“资源 ID”和“资源名称”字段为“未设置”？
在某些错误情况下，例如“用户名和密码错误”和基于 WSTrust 的失败登录中，“ResourceId/名称”字段将设置为“未设置”。

***报告功能预览版是否存在任何其他已知问题？***
报告功能存在这样一个已知问题：不管登录方法是什么，“基本信息”选项卡中的“身份验证要求”字段中都会填充用于 AD FS 登录的单重身份验证值。 此外，“身份验证详细信息”选项卡将在“要求”字段下显示“主要或次要”，我们正在开发修复程序来区分“主要”或“次要”身份验证类型。


## <a name="related-links"></a>相关链接
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health 代理安装](how-to-connect-health-agent-install.md)
* [有风险的 IP 报告](how-to-connect-health-adfs-risky-ip.md)
