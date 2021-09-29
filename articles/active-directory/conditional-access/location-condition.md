---
title: Azure Active Directory 条件访问中的位置条件
description: 使用位置条件根据用户位置控制访问。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, olhuan
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 09dbcf3557dd45d005a61f9aed99e29abbc684e6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626411"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>在条件访问策略中使用位置条件 

如[概述文章](overview.md)中所述，条件访问策略归根到底就是一个 if-then 语句，该语句用于组合信号、做出决策和强制实施组织策略。 可以引入到决策过程中的信号之一是位置。

![概念性条件信号加上要实施的决策](./media/location-condition/conditional-access-signal-decision-enforcement.png)

组织可以将此位置用于常见任务，例如： 

- 要求用户在企业网络外部访问服务时执行多重身份验证。
- 阻止特定国家或地区的用户访问服务。

位置是由客户端提供给 Azure Active Directory 的公共 IP 地址或 Microsoft Authenticator 应用提供的 GPS 坐标来确定。 默认情况下，条件访问策略应用于所有 IPv4 和 IPv6 地址。 

## <a name="named-locations"></a>命名位置

位置在 Azure 门户中的“Azure Active Directory” > “安全性” > “条件访问” > “命名位置”下指定。 这些命名网络位置可能包括组织的总部网络范围、VPN 网络范围或你希望阻止的范围等位置。 命名位置可以通过 IPv4/IPv6 地址范围或国家或地区来定义。 

![Azure 门户中的命名位置](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>IP 地址范围

若要按 IPv4/IPv6 地址范围定义命名位置，需要提供： 

- 位置的名称
- 一个或多个 IP 范围
- （可选）“标记为受信任位置”

![Azure 门户中的新 IP 位置](./media/location-condition/new-trusted-location.png)

IPv4/IPv6 地址范围定义的命名位置受到以下限制： 

- 配置最多 195 个命名位置
- 为每个命名位置配置最多 2000 个 IP 范围
- 支持 IPv4 和 IPv6 范围
- 无法配置专用 IP 范围
- 包含在某范围内的 IP 地址的数量受限。 定义 IP 范围时只允许使用大于 /8 的 CIDR 掩码。 

#### <a name="trusted-locations"></a>受信任位置

管理员可以将 IP 地址范围定义的位置指定为受信任的命名位置。 

从受信任的命名位置登录可以提高 Azure AD 标识保护风险计算的准确性，从而在用户从标记为受信任的位置进行身份验证时降低用户的登录风险。 此外，受信任的命名位置可以作为条件访问策略中的目标。 例如，你可能需要[将多重身份验证注册限制到受信任的位置](howto-conditional-access-policy-registration.md)。 

### <a name="countries"></a>国家/地区

组织可以通过 IP 地址或 GPS 坐标确定国家/地区位置。 

若要国家/地区定义命名位置，需要提供： 

- 位置的名称
- 选择按 IP 地址或 GPS 坐标确定位置
- 添加一个或多个国家/地区
- （可选）选择“包括未知国家/地区”

![Azure 门户中作为位置的国家/地区](./media/location-condition/new-named-location-country-region.png)

如果选择“按 IP 地址确定位置（仅 IPv4）”，系统将收集用户登录的设备的 IP 地址。 当用户登录时，Azure AD 会将用户的 IPv4 地址解析为国家或地区，并定期更新映射。 组织可以使用由国家/地区定义的命名位置阻止来自他们未开展业务的国家/地区的流量。 

> [!NOTE]
> 来自 IPv6 地址的登录无法映射到国家或地区，因此被视为未知区域。 只有 IPv4 地址才能映射到国家或地区。

如果选择“按 GPS 坐标确定位置（预览）”，用户将需要在其移动设备上安装 Microsoft Authenticator 应用。 系统每隔一小时会联系用户的 Microsoft Authenticator 应用，以收集用户的移动设备的 GPS 位置。

首次要求用户从 Microsoft Authenticator 共享其位置时，用户会在应用中收到通知。 用户需要打开应用并授予位置权限。 

接下来的 24 小时内，如果用户仍在访问资源并授予应用在后台运行的权限，则每小时以静默方式共享一次设备的位置。 24 小时后，用户必须打开应用，并批准通知。 每次用户分享其 GPS 位置时，该应用都会进行越狱检测（使用与 Intune MAM SDK 相同的逻辑）。 如果设备已越狱，则位置不被视为有效，并且不会授予用户访问权。 

在“仅报告”模式下具有基于 GPS 的命名位置的条件访问策略会提示用户共享其 GPS 位置，但不会阻止用户登录。

> [!IMPORTANT]
> 用户可能会每小时收到提示，让他们知道 Azure AD 正在 Authenticator 应用中检查其位置。 预览只应用于保护可接受此行为的非常敏感的应用，或者需要将访问限制于特定国家/地区的情况。

#### <a name="include-unknown-countriesregions"></a>包括未知国家/地区

某些 IP 地址（包括所有 IPv6 地址）未映射到特定的国家或地区。 要捕获这些 IP 位置，请在定义地理位置时选中“包括未知国家/地区”框。 使用此选项可以选择这些 IP 地址是否应包含在命名位置中。 如果使用命名位置的策略需要应用到未知位置，则使用此设置。

### <a name="configure-mfa-trusted-ips"></a>配置 MFA 受信任的 IP

还可以在[多重身份验证服务设置](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)中配置 IP 地址范围，用于表示组织的本地 Intranet。 使用此功能最多可以配置 50 个 IP 地址范围。 IP 地址范围采用 CIDR 格式。 有关详细信息，请参阅[受信任的 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips)。  

如果已配置“受信任的 IP”，这些 IP 将作为“MFA 受信任的 IP”显示在位置条件的位置列表中。

#### <a name="skipping-multi-factor-authentication"></a>跳过多重身份验证

在多重身份验证服务设置页中，可以通过选择“跳过多重身份验证以适用于我的 Intranet 上的联合用户发出的请求”，来标识企业 Intranet 用户。 此设置指示 AD FS 颁发的内部企业网络声明应受信任，并且应该用于将用户标识为位于企业网络中。 有关详细信息，请参阅[使用条件访问启用受信任的 IP 功能](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)。

选中此选项之后，“MFA 受信任的 IP”（包括命名位置）将应用到已选择此选项的所有策略。

对于会话生存期较长的移动和桌面应用程序，将定期重新评估条件访问。 默认设置是一小时评估一次。 如果只在初始身份验证时才颁发内部企业网络声明，则 Azure AD 可能没有受信任的 IP 范围列表。 在这种情况下，更难以确定用户是否仍在企业网络中：

1. 检查用户的 IP 地址是否在某个受信任的 IP 范围内。
1. 检查用户 IP 地址的前三个八位字节是否匹配初始身份验证 IP 地址的前三个八位字节。 当内部企业网络声明最初是初次颁发且用户位置已经过验证时，IP 地址将与初始身份验证进行比较。

如果这两个步骤均失败，则将用户视为不再位于受信任的 IP 中。

## <a name="location-condition-in-policy"></a>策略中的位置条件

配置位置条件时，可以区分：

- 任何位置
- 所有受信任的位置
- 选定的位置

### <a name="any-location"></a>任何位置

默认情况下，选择“任何位置”会将策略应用到所有 IP 地址，即 Internet 上的所有地址。 此设置不限于已配置为命名位置的 IP 地址。 选择“任何位置”时，仍可从策略中排除特定位置。 例如，可将策略应用到除可信位置以外的所有位置，以将作用域设置为除企业网络以外的所有位置。

### <a name="all-trusted-locations"></a>所有受信任的位置

此选项将应用到：

- 已标记为可信位置的所有位置
- MFA 受信任的 IP（如果已配置）

### <a name="selected-locations"></a>选定的位置

使用此选项可以选择一个或多个命名位置。 对于要应用此设置的策略，用户需要从任一选定位置建立连接。 “选择”时，将会打开显示命名网络列表的命名网络选择控件。 此列表还显示网络位置是否已标记为可信。 名为“MFA 受信任的 IP”的命名位置用于包含可在多重身份验证服务设置页中配置的 IP 设置。

## <a name="ipv6-traffic"></a>IPv6 流量

默认情况下，条件访问策略将应用于所有 IPv6 流量。 如果不想对特定 IPv6 范围强制实施策略，可以从条件访问策略中排除特定的 IPv6 地址范围。 例如，如果不想强制执行针对企业网络使用的策略，且企业网络托管在公共 IPv6 范围内。

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>在 Azure AD 登录活动报告中标识 IPv6 流量

可以通过转到 [Azure AD 登录活动报告](../reports-monitoring/concept-sign-ins.md)来发现租户中的 IPv6 流量。 打开活动报告后，添加“IP 地址”列。 可通过此列标识 IPv6 流量。

还可以通过单击报告中的行，然后转到登录活动详细信息中的“位置”选项卡来查找客户端 IP。 

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>我的租户什么时候会有 IPv6 流量？

Azure Active Directory (Azure AD) 当前不支持使用 IPv6 的直接网络连接。 但在某些情况下，身份验证流量是通过其他服务代理的。 在这些情况下，将在策略评估期间使用 IPv6 地址。

通过代理发送到 Azure AD 的大多数 IPv6 流量来自 Microsoft Exchange Online。 如果可行，Exchange 会首选 IPv6 连接。 **因此，如果你有针对特定 IPv4 范围配置的 Exchange 条件访问策略，需要确保你还添加了组织的 IPv6 范围。** 如果不包含 IPv6 范围，会导致在以下两种情况下出现意外行为：

- 使用邮件客户端通过旧版身份验证连接到 Exchange Online 时，Azure AD 可能会收到 IPv6 地址。 初始身份验证请求转到 Exchange，然后通过代理发送到 Azure AD。
- 当在浏览器中使用 Outlook Web Access (OWA) 时，它将定期验证是否仍然满足所有条件访问策略。 此检查用于发现用户可能已从允许的 IP 地址移动到新位置（例如街上的咖啡店）的情况。 在这种情况下，如果使用的是 IPv6 地址并且 IPv6 地址不在配置的范围内，用户的会话可能会中断，并被定向回 Azure AD 进行重新验证。 

另外，如果使用的是 Azure VNet，会收到来自 IPv6 地址的流量。 如果有条件访问策略阻止了 VNet 流量，请检查 Azure AD 登录日志。 识别流量后，就可以获取正在使用的 IPv6 地址，并将其从策略中排除。 

> [!NOTE]
> 如果要为单个地址指定 IP CIDR 范围，请应用 /128 位掩码。 如果 IPv6 地址为 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a，并想从地址范围中排除该单一地址，应使用 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128。

## <a name="what-you-should-know"></a>要点

### <a name="when-is-a-location-evaluated"></a>何时评估位置？

条件访问策略的评估时机：

- 当用户最初登录到 Web 应用、移动应用或桌面应用程序时。
- 当使用新式身份验证的移动应用或桌面应用程序使用刷新令牌来获取新的访问令牌时。 默认情况下此检查一小时进行一次。

对于使用新式身份验证的移动应用和桌面应用程序，此检查意味着，在更改网络位置的一小时内会检测到位置更改。 对于不使用新式身份验证的移动应用和桌面应用程序，此策略将应用于每个令牌请求。 请求的频率可能会因应用程序而异。 同样，对于 Web 应用程序，此策略在初始登录时应用，并适合用于 Web 应用程序的会话生存期。 由于不同应用程序的会话生存期不同，因此策略评估间隔的时间也会有所不同。 每次应用程序请求新的登录令牌时，就会应用此策略。

默认情况下，Azure AD 每小时颁发一个令牌。 在移出企业网络后的一小时内，将使用新式身份验证对应用程序实施该策略。

### <a name="user-ip-address"></a>用户 IP 地址

在策略评估中使用的 IP 地址是用户的公共 IP 地址。 对于专用网络中的设备，此 IP 地址不是 Intranet 中用户设备的客户端 IP，而是专用网络连接到公共 Internet 时使用的地址。

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>批量上传和下载命名位置

创建或更新命名位置时，若要进行批量更新，可以上传或下载含 IP 范围的 CSV 文件。 上传过程会将列表中的 IP 范围替换为该文件中的那些范围。 该文件的每行包含一个采用 CIDR 格式的 IP 地址范围。

### <a name="cloud-proxies-and-vpns"></a>云代理和 VPN

使用云托管代理或 VPN 解决方案时，Azure AD 在评估策略时使用的 IP 地址是该代理的 IP 地址。 不会使用包含用户公共 IP 地址的 X-Forwarded-For (XFF) 标头，因为没有任何机制验证该地址是否来自受信任的源，该标头可能提供了一种用于伪造 IP 地址的方法。

当云代理准备就绪时，可以使用一个策略来要求设备加入混合 Azure AD，或使用来自 AD FS 的公司网络内部的声明。

### <a name="api-support-and-powershell"></a>API 支持和 PowerShell

命名位置的 Graph API 预览版本可用。有关详细信息，请参阅 [namedLocation API](/graph/api/resources/namedlocation)。

## <a name="next-steps"></a>后续步骤

- 使用位置配置条件访问策略时，请参阅[条件访问：按位置阻止访问](howto-conditional-access-policy-location.md)。
