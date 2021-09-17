---
title: 从 Azure MFA 服务器迁移到 Azure 多重身份验证 - Azure Active Directory
description: 有关从本地 Azure MFA 服务器迁移到 Azure 多重身份验证的分步指南
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4415e7ce86d2beb9e2903f23d0b6fa9ac7d3ec04
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597831"
---
# <a name="migrate-from-azure-mfa-server-to-azure-multi-factor-authentication"></a>从 Azure MFA 服务器迁移到 Azure 多重身份验证

多重身份验证 (MFA) 为基础结构和资产免受恶意行动者的入侵发挥了重要作用。 Azure MFA 服务器不适用于新部署且即将弃用。 使用 MFA 服务器的客户应改用基于云的 Azure Active Directory (Azure AD) 多重身份验证。 本文档假设你有一个混合环境，在其中：

- 你要使用 MFA 服务器实现 MFA。
- 你正在将 Azure AD 上的联合身份验证与 Active Directory 联合身份验证服务 (AD FS) 或其他标识提供者联合身份验证产品配合使用。
  - 尽管本文所述内容的范围限于 AD FS，但类似的步骤也适用于其他标识提供者。
- MFA 服务器已与 AD FS 集成。 
- 可能有应用程序正在使用 AD FS 进行身份验证。

你的迁移有多种可能的最终状态，具体取决于你的目标。

| <br> | 目标：仅停用 MFA 服务器 | 目标：停用 MFA 服务器并迁移到 Azure AD 身份验证 | 目标：停用 MFA 服务器和 AD FS |
|------|------------------------------------|-------------------------------------------------------------------|-----------------------------------------|
|MFA 提供程序 | 将 MFA 提供程序从 MFA 服务器更改为 Azure AD MFA。 | 将 MFA 提供程序从 MFA 服务器更改为 Azure AD MFA。 |   将 MFA 提供程序从 MFA 服务器更改为 Azure AD MFA。 |
|用户身份验证  |继续使用联合身份验证进行 Azure AD 身份验证。 | 迁移到提供密码哈希同步（首选）或直通身份验证以及无缝单一登录的 Azure AD。| 迁移到提供密码哈希同步（首选）或直通身份验证以及无缝单一登录的 Azure AD。 |
|应用程序身份验证 | 继续对应用程序使用 AD FS 身份验证。 | 继续对应用程序使用 AD FS 身份验证。 | 在迁移到 Azure MFA 之前将应用迁移到 Azure AD。 |

如果可能，请将 MFA 和用户身份验证都迁移到 Azure。 有关分步指南，请参阅[迁移到 Azure AD MFA 和 Azure AD 用户身份验证](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)。 

如果无法迁移用户身份验证，请参阅有关[通过联合迁移到 Azure AD MFA](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md) 的分步指南。

## <a name="prerequisites"></a>必备条件

- AD FS 环境（如果在迁移 MFA 之前未将所有应用迁移到 Azure AD，则必须创建此环境）
  - 升级到 AD FS for Windows Server 2019，场行为级别 (FBL) 4。 这样就可以根据组成员身份选择身份验证提供程序，使用户过渡更加顺畅。 尽管在 AD FS for Windows Server 2016 FBL 3 上可以进行迁移，但这种迁移对于用户而言不是很顺畅。 在迁移过程中，在迁移完成之前，系统会提示用户选择身份验证提供程序（MFA 服务器或 Azure MFA）。 
- 权限
  - Active Directory 中有权为 Azure AD MFA 配置 AD FS 场的企业管理员角色
  - Azure AD 中有权使用 Azure AD PowerShell 执行 Azure AD 配置的全局管理员角色


## <a name="considerations-for-all-migration-paths"></a>所有迁移路径的注意事项

从 MFA 服务器到 Azure AD MFA 的迁移过程不只是涉及到移动已注册的 MFA 电话号码。 Microsoft 的 MFA 服务器可与多个系统集成，你必须评估这些系统如何使用 MFA 服务器，以了解与 Azure AD MFA 集成的最佳方式。 

### <a name="migrating-mfa-user-information"></a>迁移 MFA 用户信息

分批移动用户的常见方式包括按区域、部门或角色（例如管理员）移动用户。 无论选择哪种策略，都请确保从测试组和试点组开始以迭代方式移动用户，并制定回滚计划。 

尽管可以迁移用户的已注册 MFA 电话号码和硬件令牌，但无法迁移设备注册，例如用户的 Microsoft Authenticator 应用设置。 用户需要在 Authenticator 应用中注册和添加新帐户，并删除旧帐户。 

为了帮助用户区分新添加的帐户与链接到 MFA 服务器的旧帐户，请确保 MFA 服务器上移动应用的帐户名以一种能够区分这两个帐户的方式命名。 例如，在 MFA 服务器上的移动应用下显示的帐户名已重命名为“本地 MFA 服务器”。 在下一次向用户发送推送通知时，Authenticator 应用中的帐户名将会更改。 

迁移电话号码还可能导致迁移过时的号码，并很有可能会使用户保持使用基于电话的 MFA，而不是设置更安全的方法，例如，在无密码模式下使用 Microsoft Authenticator。 因此，无论你选择哪种迁移路径，我们都建议让所有用户注册[组合安全性信息](howto-registration-mfa-sspr-combined.md)。


#### <a name="migrating-hardware-security-keys"></a>迁移硬件安全密钥

Azure AD 提供 OATH 硬件令牌支持。 若要将令牌从 MFA 服务器迁移到 Azure AD MFA，[必须使用 CSV 文件（通常称为“种子文件”）将令牌上传到 Azure AD 中](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)。 种子文件包含机密密钥和令牌序列号，以及将令牌上传到 Azure AD 所需的其他必要信息。 

如果你不再拥有包含机密密钥的种子文件，则无法从 MFA 服务器导出机密密钥。 如果你不再有权访问机密密钥，请与硬件供应商联系以请求支持。

可以使用 MFA 服务器 Web 服务 SDK 导出分配到给定用户的任何 OATH 令牌的序列号。 将此信息与种子文件一起使用，IT 管理员可将令牌导入 Azure AD，并基于序列号将 OATH 令牌分配给指定的用户。 在导入时，还需要联系用户，让其提供设备中的 OTP 信息以完成注册。 请参阅 MFA 服务器上的多重身份验证服务器帮助文件中的“GetUserInfo”>“userSettings”>“OathTokenSerialNumber”主题。 


### <a name="additional-migrations"></a>其他迁移方案

决定从 MFA 服务器迁移到 Azure MFA 会打开其他迁移方案的大门。 如何完成其他迁移方案取决于多种因素，具体包括：

- 为用户使用 Azure AD 身份验证的意愿
- 将应用程序迁移到 Azure AD 的意愿

由于 MFA 服务器与应用程序和用户身份验证深度集成，因此，可以考虑在 MFA 迁移过程中将这两项功能都迁移到 Azure，并最终停用 AD FS。 

我们的建议： 

- 使用 Azure AD 进行身份验证，因为它可以实现更可靠的安全性和治理
- 如果可能，将应用程序迁移到 Azure AD

若要选择最适合你的组织的用户身份验证方法，请参阅[为 Azure AD 混合标识解决方案选择适当的身份验证方法](../hybrid/choose-ad-authn.md)。 我们建议使用密码哈希同步 (PHS)。

### <a name="passwordless-authentication"></a>无密码身份验证

在注册用户以使用 Microsoft Authenticator 作为第二个因素的过程中，我们建议在用户注册过程中启用无密码手机登录。 有关详细信息（包括 FIDO 和 Windows Hello 企业版等其他无密码方法），请访问[规划使用 Azure AD 的无密码身份验证部署](howto-authentication-passwordless-deployment.md#plan-for-and-deploy-the-microsoft-authenticator-app)。

### <a name="microsoft-identity-manager-self-service-password-reset"></a>Microsoft Identity Manager 自助式密码重置 

在执行密码重置流的过程中，Microsoft Identity Manager (MIM) SSPR 可以使用 MFA 服务器调用一次性短信密码。 无法将 MIM 配置为使用 Azure MFA。 建议评估是否要将 SSPR 服务迁移到 Azure AD SSPR。

凭借用户注册的机会，可以让 Azure MFA 使用组合注册体验来注册 Azure AD SSPR。


### <a name="radius-clients-and-azure-ad-mfa"></a>RADIUS 客户端和 Azure AD MFA

MFA 服务器支持通过 RADIUS 来为支持该协议的应用程序和网络设备调用 MFA。 如果将 RADIUS 与 MFA 服务器配合使用，我们建议将客户端应用程序迁移到新式协议，例如 SAML、Open ID Connect，或 Azure AD 上的 OAuth。 如果无法更新应用程序，可以部署带有 Azure MFA 的网络策略服务器 (NPS) 扩展。 该网络策略服务器 (NPS) 扩展充当基于 RADIUS 的应用程序与 Azure AD MFA 之间的适配器，提供身份验证的第二因素。 这样，便可以将 RADIUS 客户端迁移到 Azure MFA 并停用 MFA 服务器。

#### <a name="important-considerations"></a>重要注意事项

为 RADIUS 客户端使用 NPS 时存在一些限制，我们建议评估所有 RADIUS 客户端，确定是否可将其升级到新式身份验证协议。 有关支持的产品版本及其功能，请咨询服务提供商。 

- NPS 扩展不使用 Azure AD 条件访问策略。 如果继续使用 RADIUS，并且使用 NPS 扩展，则转到 NPS 的所有身份验证请求都会要求用户执行 MFA。
- 用户必须先注册 Azure AD MFA 才能使用 NPS 扩展。 否则，该扩展无法对用户进行身份验证，从而可能导致用户呼叫支持人员。
- 当 NPS 扩展调用 MFA 时，MFA 请求将发送到用户的默认 MFA 方法。 
  - 由于登录是在第三方应用程序中发生的，用户不太可能会看到指出需要执行 MFA 并且已将请求发送到其设备的视觉通知。
  - 在处理 MFA 要求期间，用户必须能够访问其默认身份验证方法才能解决 MFA 要求。 他们无法选择替代方法。 即使租户身份验证方法和 MFA 策略中已禁用默认身份验证方法，也将使用该方法。
  - 用户可以在“安全信息”页 (aka.ms/mysecurityinfo) 中更改其默认 MFA 方法。
- RADIUS 客户端的可用 MFA 方法由发送 RADIUS 访问请求的客户端系统控制。
  - 只能在支持 RADIUS 访问质询响应的系统中使用要求用户在输入密码后提供用户输入的 MFA 方法。 输入方法可能包括 OTP、硬件 OATH 令牌或 Microsoft Authenticator 应用程序。
  - 某些系统可能会将可用 MFA 方法限制为 Microsoft Authenticator 推送通知和电话呼叫。


>[!NOTE]
>在 RADIUS 客户端与 NPS 系统之间使用的密码加密算法以及客户端可以使用的输入方法会影响哪些身份验证方法可供使用。 有关详细信息，请参阅[确定用户可以使用的身份验证方法](howto-mfa-nps-extension.md)。 

常见的 RADIUS 客户端集成包括[远程桌面网关](howto-mfa-nps-extension-rdg.md)和 [VPN 服务器](howto-mfa-nps-extension-vpn.md)等应用程序。 其他应用程序可能包括：

- Citrix Gateway
  - [Citrix Gateway](https://docs.citrix.com/en-us/citrix-gateway) 支持 RADIUS 和 NPS 扩展集成，以及 SAML 集成。
- Cisco VPN
  - Cisco VPN 支持 RADIUS 以及[用于 SSO 的 SAML 身份验证](../saas-apps/cisco-anyconnect.md)。
  - 从 RADIUS 身份验证迁移到 SAML 后，无需部署 NPS 扩展即可集成 Cisco VPN。
- 所有 VPN
  - 如果可能，我们建议将 VPN 作为 SAML 应用进行联合。 这样便可以使用条件访问。 有关详细信息，请参阅[已集成到 Azure AD 的 VPN 供应商列表](../manage-apps/secure-hybrid-access.md#secure-hybrid-access-through-azure-ad-partner-integrations)应用库。


### <a name="resources-for-deploying-nps"></a>有关部署 NPS 的资源

- [添加新的 NPS 基础结构](/windows-server/networking/technologies/nps/nps-top)
- [NPS 部署最佳做法](https://www.youtube.com/watch?v=qV9wddunpCY)
- [Azure MFA NPS 扩展运行状况检查脚本](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)
- [将现有的 NPS 基础结构与 Azure AD MFA 集成](howto-mfa-nps-extension-vpn.md)

## <a name="next-steps"></a>后续步骤

- [通过联合迁移到 Azure AD MFA](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md)
- [迁移到 Azure AD MFA 和 Azure AD 用户身份验证](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)


