---
title: 发生系统标识泄露后借助 Microsoft 和 Azure 安全性资源进行恢复 | Microsoft Docs
description: 了解如何使用 Microsoft 365 Defender、Azure Sentinel、Azure Active Directory、Azure 安全中心与 Microsoft 建议等 Microsoft 和 Azure 安全资源来保护系统，使其免受类似于 2020 年 12 月 Nobelium 攻击 (Solorigate) 之类的系统标识泄露攻击。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: 965de895cbe7e030ff009ba11a0947048ce8b4c1
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252605"
---
# <a name="recovering-from-systemic-identity-compromise"></a>从系统标识泄露中恢复

本文介绍在组织遭受系统标识泄露攻击（例如 2020 年 12 月发生的 [Nobelium](https://aka.ms/solorigate) 攻击）后进行恢复时可用的 Microsoft 资源及相关建议。

本文的内容是根据 Microsoft 检测和响应团队（简称 DART，致力于应对泄露事件并帮助客户在遇到网络问题时进行复原）提供的指导编写的。 有关 DART 团队提供的更多指导，请参阅其 [Microsoft 安全博客系列文章](https://www.microsoft.com/security/blog/microsoft-detection-and-response-team-dart-blog-series/)。

许多组织已过渡到基于云的方法，以增强其标识和访问管理的安全性。 但是，你的组织可能还有一些本地系统在发挥作用，并且使用混合体系结构的各种方法。 本文将明确说明系统标识攻击对云、本地和混合系统造成的影响，并提供适用于所有这些环境的建议和参考。

> [!IMPORTANT]
> 这些信息按原样提供并构成通用指导；要最终决定如何将此指导应用于你的 IT 环境和租户，必须考虑到你的独特环境和需求，而每个客户最能确定自己的独特环境和需求。
>

## <a name="about-systemic-identity-compromise"></a>关于系统标识泄露

当攻击者在组织标识基础结构的管理方面成功获得了立足点时，即表示组织遭到了系统标识泄露攻击。

如果你的组织遭遇了这种情况，则你需要与攻击者对抗以保护环境，使其免受进一步的损害。

- 获得了环境标识基础结构管理控制权的攻击者可以利用这种控制权来创建、修改或删除该环境中的标识和标识权限。

    发生本地泄露时，如果受信任的 SAML 令牌签名证书未存储在 [HSM](../../key-vault/keys/hsm-protected-keys.md) 中，则攻击行为将包括对该受信任 SAML 令牌签名证书的访问。

- 然后，攻击者可以使用该证书来伪造 SAML 令牌，以冒充组织中的任何现有用户和帐户，而无需获得对帐户凭据的访问权限，也不会留下任何痕迹。

- 高特权帐户访问权限也可用于向现有应用程序添加由攻击者控制的凭据，使攻击者能够使用这些权限在不被发现的情况下访问你的系统，例如调用 API。

## <a name="responding-to-the-attack"></a>对攻击做出响应


对系统标识泄露做出的响应应该包括下图和下表中所示的步骤：

:::image type="content" source="media/recover-from-identity-compromise/recover-identity-compromise.png" alt-text="从标识泄露中恢复的步骤。":::


|步骤  |说明  |
|---------|---------|
|建立安全通信     |  遭遇系统标识泄露的组织必须假定所有通信均受到影响。 在采取任何恢复措施之前，必须确保负责调查和响应工作的重要团队成员[能够安全地进行通信](#establish-secure-communications)。 <br><br>第一步必须是确保通信安全，以便能够在攻击者尚不知情的情况下继续采取措施。|
|调查环境   | 在确保核心调查团队的通信安全后，可以开始寻找初始接入点和持久性方法。 [识别泄露迹象](#identify-indications-of-compromise)，然后寻找初始接入点和持久性方法。 同时，在采取恢复措施期间开始[建立持续性的监视操作](#establish-continuous-monitoring)。        |
|改善安全状况     | 按照最佳做法建议来[启用安全特性和功能](#improve-security-posture)，以持续改善系统安全性。  <br><br>确保随着时间的推移和安全形势的变化，[持续监视](#establish-continuous-monitoring)工作不会中断。    |
|夺回/保留控制权     |  必须夺回攻击者对你环境的管理控制权。 在夺回控制权并刷新系统的安全状况后，确保[修正或阻止](#remediate-and-retain-administrative-control)所有可能的持久性方法和新的初始访问攻击。       |
|     |         |

## <a name="establish-secure-communications"></a>建立安全通信

在开始做出响应之前，必须确保能够安全通信且不会受到攻击者的窃听。 确保隔离与事件相关的任何通信，这样攻击者便无从知道你正在进行调查，并对你的响应措施感到措手不及。

例如：

1. 要进行初始的一对一通信和小组通信，可以使用 PSTN 呼叫、未连接到企业基础结构的会议桥，以及端到端的加密消息传送解决方案。

    除非已通过安全通道进行验证，否则应将这些框架外部的通信视为已泄露且不受信任。

2. 完成这些初始对话后，可以创建一个与组织的生产租户隔离的全新 Microsoft 365 租户。 仅为需要参与响应的关键人员创建帐户。

如果你确实创建了一个新的 Microsoft 365 租户，请务必遵循适用于该租户的所有最佳做法，尤其是对于管理帐户和权限。 限制管理权限，不要信任外部应用程序或供应商。

> [!IMPORTANT]
> 务必不要在可能已泄露的现有电子邮件帐户中传达有关新租户的信息。 

有关详细信息，请参阅[有关安全使用 Microsoft 365 的最佳做法](https://www.microsoft.com/security/blog/2019/01/10/best-practices-for-securely-using-microsoft-365-the-cis-microsoft-365-foundations-benchmark-now-available/)。

## <a name="identify-indications-of-compromise"></a>识别泄露迹象

建议客户遵循系统提供商（包括 Microsoft 和任何合作伙伴）提供的最新指示，实施提供的任何新检测和保护措施，并识别已发布的泄露事件 (IOC)。

检查以下 Microsoft 安全产品的更新，并实施所有建议的更改：

- [Azure Sentinel](../../sentinel/index.yml)
- [Microsoft 365 安全解决方案和服务](/microsoft-365/security/)
- [Windows 10 Enterprise Security](/windows/security/)
- [Microsoft Cloud App Security](/cloud-app-security/)

实施新的更新有助于识别任何早期攻击活动，并防止系统将来遭到攻击。 请记住，IOC 列表可能并不详尽，可能会随着调查的继续而不断补充。

因此，我们建议还要采取以下措施：

- 确保已应用 [Azure 安全基准文档](/security/benchmark/azure/)，并通过 [Azure 安全中心](../../security-center/index.yml)监视符合性。

- 将威胁情报源整合到 SIEM 中（例如，通过在 [Azure Sentinel](../../sentinel/understand-threat-intelligence.md) 中配置 Microsoft 365 数据连接器）。

有关详细信息，请参阅 Microsoft 安全文档：

- [Microsoft 安全文档](/security/)
- [Azure 安全文档](../index.yml)

## <a name="investigate-your-environment"></a>调查环境

一旦事件响应者和关键人员有了一个安全的协作位置，你便可以开始调查已泄露的环境。

需要在找到每种异常行为的根源与快速采取行动来阻止攻击者进一步展开活动之间取得平衡。 要成功进行修正，需要当时就尽可能全面了解攻击者使用的初始进入方法和持久性方法。 在调查过程中遗漏任何持久性方法都可能导致攻击者持续获得访问权限，从而可能发生再次泄露。

此时，可以执行风险分析以确定所要采取的措施的优先级。 有关详细信息，请参阅：

- [数据中心威胁、漏洞和风险评估](/compliance/assurance/assurance-threat-vulnerability-risk-assessment)
- [使用威胁分析跟踪和应对新兴威胁](/microsoft-365/security/defender-endpoint/threat-analytics)
- [威胁和漏洞管理](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)

Microsoft 的安全服务提供了大量资源以用于详尽调查。 以下部分介绍了建议的首要措施。


> [!NOTE]
> 如果你发现列出的一个或多个日志记录源当前不是你的安全计划的一部分，我们建议尽快配置这些源以启用检测和将来的日志审查。
>
> 确保配置日志保留期，以支持组织的长远调查目标。 根据法律、法规或保险目的的需求保留证据。
>

### <a name="investigate-and-review-cloud-environment-logs"></a>调查并审查云环境日志

调查并审查云环境日志，以找出可疑的操作和攻击者入侵迹象。 例如，检查以下日志：

- [统一审核日志 (UAL)](/powershell/module/exchange/search-unifiedauditlog)
- [Azure Active Directory (Azure AD) 日志](../../active-directory/reports-monitoring/overview-monitoring.md)
- [Microsoft Exchange 本地日志](/exchange/mail-flow/transport-logs/transport-logs)
- 来自 [VPN 网关](../../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)等组件的 VPN 日志
- 工程系统日志
- 防病毒和终结点检测日志

### <a name="review-endpoint-audit-logs"></a>审查终结点审核日志

审查终结点审核日志以找出本地更改，例如以下类型的操作：

- 组成员资格更改
- 新用户帐户创建
- Active Directory 中的委托

请特别留意随其他典型的泄露或活动迹象一起发生的以上任何更改。

### <a name="review-administrative-rights-in-your-environments"></a>审查环境中的管理权限

审查云和本地环境中的管理权限。 例如：

|环境  |说明  |
|---------|---------|
|所有云环境    |       - 审查云中的任何特权访问权限，删除任何不必要的权限<br>    - 实施 Privileged Identity Management (PIM)<br>    - 设置条件访问策略以在强化期间限制管理访问权限      |
|所有本地环境     |       - 审查本地的特权访问权限，删除不必要的权限<br>   - 降低内置组的成员资格<br>    - 验证 Active Directory 委托<br>    - 强化第 0 层环境，限制有权访问第 0 层资产的人员      |
|所有企业应用程序     | 审查允许执行以下任一操作的委托权限和同意授予： <br><br>  - 修改特权用户和角色 <br>- 读取或访问所有邮箱 <br>- 代表其他用户发送或转发电子邮件 <br>- 访问所有 OneDrive 或 SharePoint 网站内容 <br>- 添加可读取/写入目录的服务主体      |
|Microsoft 365 环境     |审查 Microsoft 365 环境的访问权限和配置设置，包括： <br>- SharePoint Online 共享 <br>- Microsoft Teams <br>- PowerApps <br>- Microsoft OneDrive for Business          |
| 审查环境中的用户帐户   |- 审查并删除不再需要的来宾用户帐户。 <br>- 审查委托的电子邮件配置、邮箱文件夹权限、ActiveSync 移动设备注册、收件箱规则和 Outlook 网页版选项。 <br>- 审查 ApplicationImpersonation 权限，尽量减少旧式身份验证的使用。 <br>- 验证是否强制执行 MFA，以及所有用户的 MFA 和自助式密码重置 (SSPR) 联系信息是否正确。         |
|     |         |

## <a name="establish-continuous-monitoring"></a>建立持续监视

攻击者行为检测包括多种方法，具体取决于可供组织用来应对攻击的安全工具。

例如，Microsoft 安全服务可能会提供与攻击相关的特定资源和指导，如以下部分中所述。

> [!IMPORTANT]
> 如果你在调查中发现了有人通过系统入侵获得管理权限，从而使其能够访问你组织的全局管理员帐户和/或受信任 SAML 令牌签名证书的证据，我们建议采取措施来进行[修正并夺回管理控制权](#remediate-and-retain-administrative-control)。
>

### <a name="monitoring-with-azure-sentinel"></a>使用 Azure Sentinel 进行监视

Azure Sentinel 提供许多内置资源来帮助你进行调查，例如搜寻工作簿和分析规则可帮助检测相关环境区域中的攻击。

有关详细信息，请参阅：

- [可视化并分析环境](../../sentinel/get-visibility.md)
- [直接检测威胁](../../sentinel/detect-threats-built-in.md)。

### <a name="monitoring-with-microsoft-365-defender"></a>使用 Microsoft 365 Defender 进行监视

建议查看“Microsoft 365 Defender for Endpoint”和“Microsoft Defender 防病毒”，其中提供了与攻击相关的具体指导。

查看 Microsoft 安全中心（例如 Microsoft 365 Defender、Microsoft 365 Defender for Identity 和 Microsoft Cloud App Security）的其他检测、搜寻查询和威胁分析报告示例。 为确保全面监视，请务必在所有域控制器以及 ADFS 服务器上安装 [Microsoft Defender for Identity 代理](/defender-for-identity/install-step4)。

有关详细信息，请参阅：

- [使用威胁分析跟踪和应对新兴威胁](/windows/security/threat-protection/microsoft-defender-atp/threat-analytics)
- [了解威胁分析中的分析报告](/microsoft-365/security/defender/threat-analytics-analyst-reports)

### <a name="monitoring-with-azure-active-directory"></a>使用 Azure Active Directory 进行监视

Azure Active Directory 登录日志可以显示是否正确使用了多重身份验证。 可以直接从 Azure 门户中的“Azure Active Directory”区域访问登录日志，也可以使用 Get-AzureADAuditSignInLogs cmdlet 或者在 Azure Sentinel 的“日志”区域中查看这些日志 。

例如，可以搜索或筛选结果来确定“MFA 结果”字段是否包含“令牌中的声明满足 MFA 要求”值 。 如果你的组织使用 ADFS，而记录的声明未包含在 ADFS 配置中，则这些声明可能指示出现了攻击者活动。

进一步搜索或筛选结果，以排除其他干扰信息。 例如，可以仅包含来自联合域的结果。 如果发现了可疑登录，请根据 IP 地址、用户帐户等条件进一步向下钻取。

下表描述了在调查中使用 Azure Active directory 日志的其他方法：

|方法  |说明  |
|---------|---------|
|分析风险登录事件     |  Azure Active Directory 及其标识保护平台可以生成与攻击者生成的 SAML 令牌的使用相关联的风险事件。 <br><br>这些事件可能标记为不熟悉的属性、匿名 IP 地址、不可能的行程等  。 <br><br>建议仔细分析与拥有管理特权的帐户关联的所有风险事件，包括已自动消除或修正的任何风险事件。 例如，可能会由于用户通过了 MFA 而自动修正风险事件或匿名 IP 地址。 <br><br>确保使用 [ADFS Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)，使所有身份验证事件在 Azure AD 中可见。 |
|检测域身份验证属性     |  攻击者操控域身份验证策略的任何企图将记录在 Azure Active Directory 审核日志中，并反映在统一审核日志中。 <br><br> 例如，可以审查统一审核日志、Azure AD 审核日志和/或 SIEM 环境中与“设置域身份验证”关联的任何事件，以验证列出的所有活动是否符合预期和计划。   |
|检测 OAuth 应用程序的凭据     |  已获得特权帐户控制权的攻击者可以搜索能够访问组织中任何用户电子邮件的应用程序，然后将攻击者控制的凭据添加到该应用程序。 <br><br>例如，你可以搜索与攻击者行为相一致的以下任何活动： <br>- 添加或更新服务主体凭据 <br>- 更新应用程序证书和机密 <br>- 向用户添加应用角色分配授予 <br>- 添加 Oauth2PermissionGrant |
|检测应用程序的电子邮件访问     |  搜索环境中应用程序对电子邮件的访问。 例如，使用 [Microsoft 365 高级审核功能](/microsoft-365/compliance/mailitemsaccessed-forensics-investigations)来调查已泄露的帐户。 |
|检测服务主体的非交互式登录     | Azure Active Directory 登录报告提供有关任何使用服务主体凭据的非交互式登录的详细信息。  例如，可以使用登录报告来查找可用于调查的宝贵数据，如攻击者在访问电子邮件应用程序时使用的 IP 地址。        |
|     |         |


## <a name="improve-security-posture"></a>改善安全状况

如果系统中发生了安全事件，我们建议根据当前安全策略和优先级来反映此问题。

既然组织现在面临新的威胁，事件响应者往往需要提供有关组织应优先考虑哪些投资的建议。

除了本文中所述的建议外，我们建议优先关注能够对攻击者在利用漏洞后采取的手法做出响应，以及弥补使攻击者能够得逞的常见安全状况差距的方面。

以下部分列出了有关改善一般安全状况和标识安全状况的建议。

### <a name="improve-general-security-posture"></a>改善一般安全状况

建议采取以下措施来确保一般安全状况正常：

- 查看 [Microsoft 安全评分](/microsoft-365/security/mtp/microsoft-secure-score)，了解针对所用 Microsoft 产品和服务自定义的安全基本建议。

- 确保组织中已部署 [Microsoft 365 Defender for Endpoint](/microsoft-365/security/defender/microsoft-365-defender) 和 [Azure Sentinel](../../sentinel/overview.md) 等 EDR 和 SIEM 解决方案。

- 查看 Microsoft 的[企业访问模型](/security/compass/privileged-access-access-model)。

### <a name="improve-identity-security-posture"></a>改善标识安全状况

建议采取以下措施来确保标识相关的安全状况正常：

- 查看 Microsoft 的[保护标识基础结构的五个步骤](steps-secure-identity.md)，并根据标识体系结构确定适当的步骤优先级。

- [考虑迁移到身份验证策略的 Azure AD 安全默认值](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)。

- 在组织中消除旧式身份验证的使用（如果系统或应用程序仍然需要它）。 有关详细信息，请参阅[使用条件访问来阻止 Azure AD 的旧式身份验证](../../active-directory/conditional-access/block-legacy-authentication.md)。

    > [!NOTE]
    > Exchange 团队计划在 2021 年下半年[禁用 EAS、EWS、POP、IMAP 和 RPS 协议的基本身份验证](https://developer.microsoft.com/en-us/office/blogs/deferred-end-of-support-date-for-basic-authentication-in-exchange-online/)。
    >
    > 特此澄清，安全默认值和身份验证策略是独立的，但提供互补的功能。
    >
    > 我们建议客户使用身份验证策略来关闭一部分 Exchange Online 协议的基本身份验证，或者在大型组织中逐渐关闭基本身份验证。
    >

- 将 ADFS 基础结构和 AD Connect 基础结构视为第 0 层资产。

- 限制对系统进行本地管理访问（包括用于运行 ADFS 服务的帐户）。

    运行 ADFS 的帐户所需的最低特权是“作为服务登录”用户权限分配。

- 使用远程桌面的 Windows 防火墙策略来限制受限用户和受限 IP 地址范围进行管理访问。

    建议设置第 0 层 Jumpbox 或相应的系统。

- 阻止从环境中的任何位置对系统进行各种入站 SMB 访问。 有关详细信息，请参阅[超越边缘：如何保护 Windows 中的 SMB 流量](https://techcommunity.microsoft.com/t5/itops-talk-blog/beyond-the-edge-how-to-secure-smb-traffic-in-windows/ba-p/1447159)。 我们还建议将 Windows 防火墙日志流式传输到 SIEM，以进行历史记录监视和主动监视。

- 如果使用的是服务帐户并且你的环境支持服务帐户，请从服务帐户迁移到组托管服务帐户 (gMSA)。 如果无法迁移到 gMSA，请将服务帐户的密码轮换为复杂密码。

- 确保在 ADFS 系统上启用详细日志记录。 例如，运行以下命令：

    ```powershell
    Set-AdfsProperties -AuditLevel verbose
    Restart-Service -Name adfssrv
    Auditpol.exe /set /subcategory:”Application Generated” /failure:enable /success:enable
    ```

## <a name="remediate-and-retain-administrative-control"></a>修正和保留管理控制权

如果你在调查中发现，攻击者取得了组织云或本地环境中的管理控制权，则必须夺回控制权，并确保攻击者无法持久展开攻击。

本部分提供了在制定管理控制权恢复计划时可以考虑的方法和步骤。

> [!IMPORTANT]
> 需要在组织中执行的确切步骤取决于你在调查中发现的持久活动，以及你对调查全面性以及是否发现了所有可能入口和持久性方法的自信程度。
>
> 确保采取的所有措施是在从[干净的源](/security/compass/privileged-access-access-model)构建的受信任设备上执行的。 例如，使用全新的[特权访问工作站](/security/compass/privileged-access-deployment)。
>

以下部分提供了以下类型的有关修正和保留管理控制权的建议：

- 删除当前服务器上的信任
- 轮换 SAML 令牌签名证书，或根据需要替换 ADFS 服务器
- 针对云或本地环境的特定修正活动

### <a name="remove-trust-on-your-current-servers"></a>删除当前服务器上的信任

如果你的组织失去了对令牌签名证书或联合信任的控制，则最可靠的方法就是删除信任，并在本地修正时切换到云控制的标识。

删除信任并切换到云控制的标识需要经过精心的规划，并深入了解隔离标识所造成的业务运营影响。 有关详细信息，请参阅[防范 Microsoft 365 遭受本地攻击](../../active-directory/fundamentals/protect-m365-from-on-premises-attacks.md)。

### <a name="rotate-your-saml-token-signing-certificate"></a>轮换 SAML 令牌签名证书

如果你的组织在本地恢复管理控制权时决定不[删除信任](#remove-trust-on-your-current-servers)，则必须在本地夺回管理控制权并能够阻断攻击者再次访问签名证书后，轮换 SAML 令牌签名证书。

轮换令牌签名证书一次还是会允许前一个令牌签名证书生效。 仍然允许以前的证书生效是一项内置功能，目的是为了正常进行证书轮换，它为组织提供一段宽限期，以便在证书过期之前更新任何信赖方信任。

如果发生了攻击，则你肯定不希望攻击者保留访问权限。 请务必使用以下步骤来确保攻击者无法伪造域的令牌。

> [!CAUTION]
> 此过程的最后一步是将用户从其手机、当前 Web 邮件会话以及使用关联令牌和刷新令牌的任何其他项中注销。
>

> [!TIP]
> 在 ADFS 环境中执行这些步骤会创建主要证书和辅助证书，并在 5 天的默认期限后自动将辅助证书提升为主要证书。
>
> 如果你有信赖方信任，这种提升可能会在初始 ADFS 环境更改 5 天后生效，应在计划中考虑到这种情况。 也可以通过第三次替换主要证书，再次使用 Urgent 标志并删除辅助证书或关闭自动证书轮换来解决此问题。
>

完全轮换令牌签名证书，并防止攻击者伪造新令牌

1. 检查并确保 AutoCertificateRollover 参数设置为 True ：

    ``` powershell
    Get-AdfsProperties | FL AutoCert*, Certificate*
    ```
    如果 AutoCertificateRollover 未设置为 True，请按如下所示设置值 ：

    ``` powershell
    Set-ADFSProperties -AutoCertificateRollover $true
    ```

1. 连接到 Microsoft Online Service：

    ``` powershell
    Connect-MsolService
    ```

1. 运行以下命令，并记下本地和云令牌签名证书指纹与到期日期：

    ``` powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

    例如：

    ```powershell
    ...
    [Not Before]
        12/9/2020 7:57:13 PM

    [Not After]
        12/9/2021 7:57:13 PM

    [Thumbprint]
        3UD1JG5MEFHSBW7HEPF6D98EI8AHNTY22XPQWJFK6
    ```

1. 使用 Urgent 开关替换主要令牌签名证书。 此命令会导致 ADFS 立即替换主要证书，而不会将其设为辅助证书：

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing -Urgent
    ```

1. 在不使用 Urgent 开关的情况下创建一个辅助令牌签名证书。 在与 Azure 云同步之前，可通过此命令创建两个本地令牌签名证书。

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing
    ```

1. 使用本地主要证书和辅助证书更新云环境，以立即删除云发布的令牌签名证书。

    ```powershell
    Update-MsolFederatedDomain -DomainName <domain>
    ```

    > [!IMPORTANT]
    > 如果使用此方法执行此步骤，则旧令牌签名证书可能仍然能够对用户进行身份验证。

1. 为确保正确执行这些步骤，请验证前面在步骤 3 中显示的证书现在是否已删除：

    ```powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

1. 通过 PowerShell 撤销刷新令牌，以防止使用旧令牌进行访问。 

    有关详细信息，请参阅：

    - [在 Azure Active Directory 中撤销用户访问](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell 文档](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



### <a name="replace-your-adfs-servers"></a>替换 ADFS 服务器

如果你不想要[轮换 SAML 令牌签名证书](#rotate-your-saml-token-signing-certificate)，而是决定将 ADFS 服务器替换为干净的系统，则需要从环境中删除现有的 ADFS 服务器，然后构建一个新服务器。 

有关详细信息，请参阅[删除配置](../../active-directory/cloud-sync/how-to-configure.md#remove-a-configuration)。 

### <a name="cloud-remediation-activities"></a>云修正活动

除了本文前面列出的建议以外，我们还建议对云环境执行以下活动：

|活动  |说明  |
|---------|---------|
|**重置密码**     |   重置任何[紧急帐户](../../active-directory/roles/security-emergency-access.md)的密码，并将紧急帐户的数量减少至所需的绝对最小值。    |
|限制特权访问帐户     |    确保拥有访问特权的服务和用户帐户是仅限云的帐户，而不要使用已同步或联合到 Azure Active Directory 的本地帐户。  |
|强制执行 MFA     | 强制要求租户中所有已提升权限的用户执行多重身份验证 (MFA)。 建议强制要求租户中的所有用户执行 MFA。       |
|限制管理访问权限     |    实施 [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) (PIM) 和条件访问以限制管理访问权限。  <br><br>对于 Microsoft 365 用户，请实施 [Privileged Access Management](https://techcommunity.microsoft.com/t5/microsoft-security-and/privileged-access-management-in-office-365-is-now-generally/ba-p/261751) (PAM) 以限制访问敏感功能，例如电子数据展示、全局管理、帐户管理等。    |
|审查/降低委派权限和同意授予     |  审查并降低所有允许使用以下任何功能的企业应用程序委托权限或[同意授予](/graph/auth-limit-mailbox-access)： <br><br>- 修改特权用户和角色 <br>- 阅读、发送电子邮件或访问所有邮箱 <br>- 访问 OneDrive、Teams 或 SharePoint 内容 <br>- 添加可读取/写入目录的服务主体 <br>- 应用程序权限与委托访问权限       |
|     |         |

### <a name="on-premises-remediation-activities"></a>本地修正活动

除了本文前面列出的建议以外，我们还建议对本地环境执行以下活动：

|活动  |说明  |
|---------|---------|
|重建受影响的系统     |   重新在调查过程中发现的被攻击者入侵的系统。      |
|删除不必要的管理员用户     |   从“域管理员员”、“备份操作员”和“企业管理员”组中删除不必要的成员。 有关详细信息，请参阅[保护特权访问](/security/compass/overview)。 |
|重置特权帐户的密码     |  重置环境中所有特权帐户的密码。 <br><br>注意：特权帐户并不局限于内置组，它们还可以属于拥有委托访问权限，可以进行服务器管理、工作站管理或访问环境中其他区域的组。      |
|重置 krbtgt 帐户     | 使用 [New-KrbtgtKeys](https://github.com/microsoft/New-KrbtgtKeys.ps1/blob/master/New-KrbtgtKeys.ps1) 脚本重置 krbtgt 帐户两次。 <br><br>注意：如果使用只读域控制器，则需要分别针对读写域控制器和只读域控制器运行该脚本。        |
|计划系统重启     |   在验证系统上不存在或未保留攻击者创建的持久性机制后，计划系统重启以帮助删除常驻内存的恶意软件。 |
|重置 DSRM 密码     |  将每个域控制器的 DSRM（目录服务还原模式）密码重置为独特且复杂的密码。       |
|     |         |

### <a name="remediate-or-block-persistence-discovered-during-investigation"></a>修正或阻止在调查过程中发现的持久性机制

调查是一个迭代过程，你既要考虑到组织在识别异常后予以修正的需求，同时又要考虑到这种修正是否会让攻击者知晓你正在进行检测，从而使他们有时间做出反应。

例如，知道你正在进行检测的攻击者可能会改变手法或创建更多的持久性机制。

请确保修正你在早期调查阶段识别到的任何持久性手法。

### <a name="remediate-user-and-service-account-access"></a>修正用户和服务帐户访问权限

除了上面列出的建议措施之外，我们还建议考虑执行以下步骤来修正并还原用户帐户：

- 强制实施基于受信任设备的条件访问。 如果可能，我们建议根据组织的要求强制实施基于位置的条件访问。

- 在解除可能已泄露的任何用户帐户后重置密码。 此外，确保实施中期计划，以重置目录中所有帐户的凭据。

- 在轮换凭据后立即撤销刷新令牌。

    有关详细信息，请参阅：

    - [在 Azure Active Directory 中紧急撤销用户访问权限](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell 文档](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



## <a name="next-steps"></a>后续步骤

- 选择顶部导航栏中的“帮助”(?) 按钮，从 Microsoft 产品（包括 Microsoft 365 安全中心、Microsoft 365 安全与合规中心和 Microsoft Defender 安全中心）内部获取帮助  。

- 如需部署帮助，请通过 [FastTrack](https://fasttrack.microsoft.com) 联系我们

- 如果你有产品支持相关的需求，请在 https://support.microsoft.com/contactus 上提交 Microsoft 支持案例。

    > [!IMPORTANT]
    > 如果你认为自己遭到入侵并需要通过事件响应获得帮助，请提交“严重性 A”Microsoft 支持案例。
    >
