---
title: Azure AD 中的连续访问评估
description: 使用 Azure AD 中的连续访问评估，更快地响应用户状态的更改
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18cc593e3aa1f50dcdaeaea32d7ac584f8bd4a24
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354063"
---
# <a name="continuous-access-evaluation"></a>连续访问评估

令牌过期和刷新是业界的一种标准机制。 当客户端应用程序（如 Outlook）连接到服务（如 Exchange Online）时，API 请求通过 OAuth 2.0 访问令牌得到授权。 默认情况下，访问令牌的有效期为一小时，超过此时间后，客户端会重定向回 Azure AD 来刷新这些令牌。 该刷新期间为重新评估用户访问策略提供了机会。 例如，我们可能会选择不刷新令牌，原因是存在条件访问策略，或是用户在目录中已被禁用。 

对用户条件发生变化之后、强制实施策略更改之前的这段滞后时间，客户表达了忧虑。 Azure AD 已经尝试了降低令牌寿命的“生硬”方法，但发现这种方法会降低用户体验和可靠性，而不会消除风险。

对策略冲突或安全问题的及时响应实际上需要令牌颁发者 (Azure AD) 和信赖方（令牌提供到的应用）之间进行“对话”。 这种双向对话提供了两项重要功能。 信赖方可以查看属性更改（例如网络位置），并通知令牌颁发者。 通过此对话，令牌颁发者也可通知信赖方由于帐户泄露、禁用或其他问题而停止采用给定用户的令牌。 此对话的机制是连续访问评估 (CAE)。 虽然我们的目标是近乎实时地做出响应，但由于事件传播时间的原因，延迟可能会长达 15 分钟。

连续访问评估的初始实现侧重于 Exchange、Teams 和 SharePoint Online。

若要准备应用程序以使用 CAE，请参阅[如何在应用程序中使用启用了连续访问评估的 API](../develop/app-resilience-continuous-access-evaluation.md)。

### <a name="key-benefits"></a>主要优点

- 用户终止或密码更改/重置：用户会话吊销将近乎实时地强制执行。
- 网络位置更改：条件访问位置策略将近乎实时地强制执行。
- 可以使用条件访问位置策略阻止将令牌导出到受信任网络外部的计算机。

## <a name="scenarios"></a>方案 

可以通过两种方案进行连续访问评估、关键事件评估和条件访问策略评估。

### <a name="critical-event-evaluation"></a>关键事件评估

连续访问评估是通过允许服务（例如 Exchange Online、SharePoint Online 和 Teams）订阅关键 Azure AD 事件来实现的。 然后，可以近乎实时地评估和强制实施这些事件。 关键事件评估不依赖于条件访问策略，因此可在任何租户中使用。 当前评估以下事件：

- 用户帐户已删除或禁用
- 用户的密码已更改或已重置
- 是否为用户启用了多重身份验证
- 管理员显式撤销用户的所有刷新令牌
- Azure Active Directory 标识保护检测到用户风险程度高

此过程会导致用户在关键事件发生后的数分钟内失去对 Microsoft 365 客户端应用中的组织 SharePoint Online 文件、电子邮件、日历或任务和 Teams 的访问权限。 

> [!NOTE] 
> Teams 和 SharePoint Online 不支持用户风险事件。

### <a name="conditional-access-policy-evaluation-preview"></a>条件访问策略评估（预览版）

Exchange Online、SharePoint Online、Teams 和 MS Graph 能够同步要在服务本身中评估的关键条件访问策略。

此过程会导致用户在网络位置发生更改后立即失去对 Microsoft 365 客户端应用或 SharePoint Online 中的组织文件、电子邮件、日历或任务的访问权限。

> [!NOTE]
> 并非所有应用和资源提供程序组合都受支持。 请参阅下表。 Office 指的是 Word、Excel 和 PowerPoint。

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 支持 | 支持 | 支持 | 支持 | 支持 |
| **Exchange Online** | 支持 | 支持 | 支持 | 支持 | 支持 |

| | Office Web 应用 | Office Win32 应用 | Office for iOS | Office for Android | Office for Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 不支持 | 支持 | 支持 | 支持 | 支持 |
| **Exchange Online** | 不支持 | 支持 | 支持 | 支持 | 支持 |

| | OneDrive web | OneDrive Win32 | OneDrive iOS | OneDrive Android | OneDrive Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 支持 | 支持 | 支持 | 支持 | 支持 |

| | Teams Web | Teams Win32 | Teams iOS | Teams Android | Teams Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Teams 服务 | 支持 | 支持 | 支持 | 支持 | 支持 |
| **SharePoint Online** | 支持 | 支持 | 支持 | 支持 | 支持 |
| **Exchange Online** | 支持 | 支持 | 支持 | 支持 | 支持 |

## <a name="client-capabilities"></a>客户端功能

### <a name="client-side-claim-challenge"></a>客户端声明质询

在进行连续访问评估之前，只要访问令牌未过期，客户端就会从其缓存中重播访问令牌。 我们通过 CAE 引入了一种新用例 - 即使令牌没有过期，资源提供程序也可以拒绝令牌。 为了通知客户端绕过其缓存（即使在缓存的令牌尚未过期的情况下），我们引入了一种称为“声明质询”的机制，用于指示令牌已被拒绝，并需要由 Azure AD 颁发一个新的访问令牌。 CAE 要求客户端更新以理解声明质询。 以下应用程序的最新版本支持声明质询：

| | Web | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | 支持 | 支持 | 支持 | 支持 | 支持 |
| **Teams** | 支持 | 支持 | 支持 | 支持 | 支持 |
| **Office** | 不支持 | 支持 | 支持 | 支持 | 支持 |
| **OneDrive** | 支持 | 支持 | 支持 | 支持 | 支持 |

### <a name="token-lifetime"></a>令牌生存期

由于风险和策略是实时评估的，协商连续访问评估感知会话的客户端将不再依赖于静态访问令牌生存期策略。 此项更改意味着，协商 CAE 感知会话的客户端将不遵守可配置的令牌生存期策略。

在 CAE 会话中，令牌生存期增加，可以长时间生存，最长可达 28 小时。 吊销是由关键事件和策略评估驱动的，并非可以随时吊销。 此更改提高了应用程序的稳定性，而不会影响安全状况。 

如果你未使用支持 CAE 的客户端，则默认访问令牌生存期将保持为 1 小时。 仅当你已使用[可配置的令牌生存期 (CTL)](../develop/active-directory-configurable-token-lifetimes.md) 预览版功能配置了访问令牌生存期时，默认生存期才会更改。

## <a name="example-flow-diagrams"></a>示例流示意图

### <a name="user-revocation-event-flow"></a>用户吊销事件流

![用户吊销事件流](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. 支持 CAE 的客户端向 Azure AD 提供凭据或刷新令牌，要求获得某个资源的访问令牌。
1. 访问令牌与其他项目一起返回到客户端。
1. 管理员显式地[撤销用户的所有刷新令牌](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)。 吊销事件将从 Azure AD 发送到资源提供程序。
1. 向资源提供程序提供访问令牌。 资源提供程序评估令牌的有效性，并检查用户是否存在任何吊销事件。 资源提供程序使用此信息来决定是否授予对资源的访问权限。
1. 在这种情况下，资源提供程序会拒绝访问，并将 401+ 声明质询发送回客户端。
1. 支持 CAE 的客户端理解 401+ 声明质询。 它绕过缓存并返回到步骤 1，将其刷新令牌和声明质询一起发送回 Azure AD。 然后在此情况下，Azure AD 将重新评估所有条件，并提示用户重新进行身份验证。

### <a name="user-condition-change-flow-preview"></a>用户条件更改流（预览版）

在下面的示例中，条件访问管理员配置了一个基于位置的条件访问策略，仅允许来自特定 IP 范围的访问：

![用户条件事件流](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. 支持 CAE 的客户端向 Azure AD 提供凭据或刷新令牌，要求获得某个资源的访问令牌。
1. Azure AD 会评估所有条件访问策略，看用户和客户端是否满足条件。
1. 访问令牌与其他项目一起返回到客户端。
1. 用户从允许的 IP 范围移出
1. 客户端从允许的 IP 范围之外向资源提供程序提供访问令牌。
1. 资源提供程序评估令牌的有效性，并检查从 Azure AD 同步的位置策略。
1. 在这种情况下，资源提供程序会拒绝访问，并将 401+ 声明质询发送回客户端。 客户端将受到质询，因为它并非来自允许的 IP 范围。
1. 支持 CAE 的客户端理解 401+ 声明质询。 它绕过缓存并返回到步骤 1，将其刷新令牌和声明质询一起发送回 Azure AD。 在这种情况下，Azure AD 会重新评估所有条件并拒绝访问。

## <a name="enable-or-disable-cae-preview"></a>启用或禁用 CAE（预览版）

1. 以条件访问管理员、安全管理员或全局管理员的身份登录到 **Azure 门户**
1. 浏览到“Azure Active Directory” > “安全性” > “连续访问评估”  。
1. 选择“启用预览版”。
1. 选择“保存”。

在此页上，你可以选择对将受预览版限制的用户和组进行限制。

> [!NOTE]
> 可以通过 [continuousAccessEvaluationPolicy ](/graph/api/continuousaccessevaluationpolicy-get?view=graph-rest-beta&preserve-view=true&tabs=http#request-body) 查询 Microsoft Graph，验证租户中 CAE 的配置。 HTTP 200 响应和关联的响应正文指示租户中是启用还是禁用了 CAE。 如果 Microsoft Graph 返回 HTTP 404 响应，则未配置 CAE。

![在 Azure 门户中启用 CAE 预览版](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

### <a name="available-options"></a>可用选项

组织可以使用多个选项来启用 CAE。

1. 在 CAE 正式发布后，将默认已选中的“正式发布后自动启用”保持选中状态可以启用该功能。
1. 选择“启用预览版”的客户可立即从新功能中获益，且无需在正式版发布后进行任何更改。 
1. 选择“禁用预览版”的客户可以按照其组织的步调从容启用 CAE。 在正式版发布后，此设置将保留为“已禁用”。

## <a name="limitations"></a>限制

### <a name="group-membership-and-policy-update-effective-time"></a>组成员身份和策略更新的有效时间

管理员对条件访问策略和组成员身份所做的更改最多可能需要一天才会生效。 存在这种延迟的原因是需要在 Azure AD 与资源提供程序（例如 Exchange Online 和 SharePoint Online）之间进行复制。 已针对策略更新进行了一些优化，将延迟缩短到了两小时。 但是，这还未涵盖所有方案。  

需要立即将条件访问策略或组成员身份更改应用于某些用户时，可以采取两种做法。 

- 运行 [revoke-azureaduserallrefreshtoken PowerShell 命令](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)以撤销指定用户的所有刷新令牌。
- 在 Azure 门户中的用户配置文件页上选择“撤销会话”，以撤销用户的会话，确保立即应用更新的策略。

### <a name="ip-address-variation"></a>IP 地址变体

标识提供者和资源提供程序可能会看到不同的 IP 地址。 这种不匹配情况可能是由于以下原因而造成的：

- 组织中的网络代理实施
- 标识提供者和资源提供程序之间错误的 IPv4/IPv6 配置
 
示例：

- 标识提供者看到了客户端的一个 IP 地址。
- 资源提供程序通过代理后看到了客户端的另一个 IP 地址。
- 标识提供者所看到的 IP 地址是策略中允许的 IP 范围的一部分，但来自资源提供程序的 IP 地址不是。

为了避免这些情况导致无限循环，Azure AD 将颁发有效期为一小时的 CAE 令牌，但不会强制更改客户端位置。 在这种情况下，与传统的一小时令牌相比，安全性将得到提高，因为除了客户端位置更改事件之外，我们还评估[其他事件](#critical-event-evaluation)。

### <a name="supported-location-policies"></a>支持的位置策略

CAE 只能识别[基于 IP 的命名位置](../conditional-access/location-condition.md#ip-address-ranges)。 CAE 无法识别其他位置条件，例如 [MFA 信任的 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) 或基于国家/地区的位置。 如果用户来自 MFA 信任的 IP、受信任的位置（其中包含受 MFA 信任的 IP）或国家/地区位置，则在该用户移到其他位置之后，将不会强制执行 CAE。 在这种情况下，Azure AD 将颁发有效期为 1 小时的访问令牌，而不执行即时 IP 强制检查。 

> [!IMPORTANT]
> 在配置连续访问评估的位置时，请仅使用[基于 IP 的条件访问位置条件](../conditional-access/location-condition.md)并配置所有 IP 地址（包括 IPv4 和 IPv6），这些地址可通过标识提供者和资源提供程序查看。 不要使用国家/地区位置条件，也不要使用 Azure AD 多重身份验证的服务设置页中提供的受信任的 IP 功能。

### <a name="office-and-web-account-manager-settings"></a>Office 和 Web 帐户管理器设置

| Office 更新通道 | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| 半年企业频道 | 如果设置为 enabled 或 1，则不支持 CAE。 | 如果设置为 enabled 或 1，则不支持 CAE。 |
| 当前频道 <br> or <br> 每月企业频道 | 无论采用哪种设置，都支持 CAE | 无论采用哪种设置，都支持 CAE |

有关 Office 更新通道的说明，请参阅 [Microsoft 365 应用的更新通道概述](/deployoffice/overview-update-channels)。 建议组织不要禁用 Web 帐户管理器 (WAM)。

### <a name="coauthoring-in-office-apps"></a>Office 应用中的共同创作

当多个用户同时针对某个文档进行协作时，CAE 可能不会根据用户撤销或策略更改事件立即撤销用户对文档的访问权限。 在这种情况下，用户会在执行以下操作或经过以下时间后完全失去访问权限： 

- 关闭文档
- 关闭 Office 应用
- 10 小时后

若要缩短这一时间，SharePoint 管理员可以通过[在 SharePoint Online 中配置网络位置策略](/sharepoint/control-access-based-on-network-location)，来减少存储在 SharePoint Online 和 OneDrive for Business 中的文档的共同创作会话的最大生存期。 更改此配置后，共同创作会话的最长生存期将缩短到 15 分钟，你可以使用 SharePoint Online PowerShell 命令“[Set-SPOTenant –IPAddressWACTokenLifetime](/powershell/module/sharepoint-online/set-spotenant)”进一步对其进行调整。

### <a name="enable-after-a-user-is-disabled"></a>在禁用用户后启用

如果在禁用用户权限后再将其启用，则需要经过一段延迟时间，才会在下游 Microsoft 服务中将帐户识别为已启用。

- SharePoint Online 和 Teams 通常有 15 分钟的延迟。 
- Exchange Online 通常有 35-40 分钟的延迟。 

### <a name="push-notifications"></a>推送通知

在发布推送通知之前不会评估 IP 地址策略。 存在这种情况的原因是，推送通知是出站的，并且没有要用于评估的关联 IP 地址。 如果用户通过单击进入该推送通知（例如，在 Outlook 电子邮件中），则仍会强制实施 CAE IP 地址策略，然后才能显示电子邮件。 推送通知显示消息预览，后者不受 IP 地址策略保护。 所有其他 CAE 检查将在发送推送通知之前执行。 如果删除了用户或设备的访问权限，则强制实施将在记录的时间段内发生。 

## <a name="faqs"></a>常见问题解答

### <a name="how-will-cae-work-with-sign-in-frequency"></a>如何将 CAE 与登录频率一起使用？

无论是否使用 CAE，登录频率都会得到遵循。

## <a name="next-steps"></a>后续步骤

- [如何在应用程序中使用启用了连续访问评估的 API](../develop/app-resilience-continuous-access-evaluation.md)
- [声明质询、声明请求和客户端功能](../develop/claims-challenge.md)
- [监视连续访问评估并排查其问题](howto-continuous-access-evaluation-troubleshoot.md)
