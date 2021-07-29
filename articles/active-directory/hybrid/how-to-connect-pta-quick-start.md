---
title: Azure AD 直通身份验证 - 快速入门 | Microsoft Docs
description: 本文介绍如何开始使用 Azure Active Directory (Azure AD) 直通身份验证。
services: active-directory
keywords: Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c5e71522d53bd4e528b2a5a106a4dcc344df3ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732850"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Azure Active Directory 直通身份验证：快速入门

## <a name="deploy-azure-ad-pass-through-authentication"></a>部署 Azure AD 直通身份验证

借助 Azure Active Directory (Azure AD) 直通身份验证，你的用户可使用同一密码登录到本地应用程序和基于云的应用程序。 直通身份验证通过直接针对本地 Active Directory 验证用户密码来使其登录。

>[!IMPORTANT]
>如果要从 AD FS（或其他联合技术）迁移到传递身份验证，强烈建议按照[此处](https://aka.ms/adfstoPTADPDownload)发布的详细部署指南进行操作。

>[!NOTE]
>如果是在 Azure 政府云中部署直通身份验证，请查看[针对 Azure 政府的混合标识注意事项](./reference-connect-government-cloud.md)。

按照以下说明在租户上部署直通身份验证：

## <a name="step-1-check-the-prerequisites"></a>步骤 1：检查先决条件

请确保符合以下先决条件：

>[!IMPORTANT]
>从安全角度来看，管理员应该将运行 PTA 代理的服务器视为域控制器。  应按照[保护域控制器免受攻击](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)中所述的同样方式，对 PTA 代理服务器进行强化

### <a name="in-the-azure-active-directory-admin-center"></a>在 Azure Active Directory 管理中心中

1. 在 Azure AD 租户中创建仅限云的全局管理员帐户。 这样一来，就可以在本地服务出现故障或不可用时管理租户的配置。 了解如何[添加仅限云的全局管理员帐户](../fundamentals/add-users-azure-active-directory.md)。 完成此步骤至关重要，可确保自己不被锁定在租户外部。
2. 在 Azure AD 租户中添加一个或多个[自定义域名](../fundamentals/add-custom-domain.md)。 用户可以使用其中一个域名登录。

### <a name="in-your-on-premises-environment"></a>在本地环境中

1. 标识运行 Windows Server 2012 R2 或更高版本的服务器，以在其上运行 Azure AD Connect。 如果尚未启用，请[在服务器上启用 TLS 1.2](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect)。 将该服务器添加到与需要验证其密码的用户同一 Active Directory 林中。 应注意，不支持在 Windows Server Core 版本上安装直通身份验证代理。 
2. 在上一步中标识的服务器上安装[最新版 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。 如果已在运行 Azure AD Connect，请确保其版本为 1.1.750.0 或更高版本。

    >[!NOTE]
    >Azure AD Connect 版本 1.1.557.0、1.1.558.0、1.1.561.0 和 1.1.614.0 具有密码哈希同步相关问题。 如果不打算将密码哈希同步与直通身份验证结合使用，请参阅 [Azure AD Connect 发行说明](./reference-connect-version-history.md)了解详细信息。

3. 确定可以运行独立身份验证代理的一个或多个其他服务器（在启用 TLS 1.2 的情况下运行 Windows Server 2012 R2 或更高版本）。 需要这些额外的服务器来确保登录请求的高可用性。 将这些服务器添加到需要验证其密码的用户所在的同一 Active Directory 林中。

    >[!IMPORTANT]
    >在生产环境中，我们建议你在租户上至少运行 3 个身份验证代理。 系统限制每位租户最多安装 40 个身份验证代理。 最佳做法是将运行身份验证代理的所有服务器视为第 0 层系统（请参阅[参考](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)）。

4. 如果服务器和 Azure AD 之间存在防火墙，请配置以下项：
   - 确保身份验证代理可以通过以下端口向 Azure AD 提出“出站”请求：

     | 端口号 | 用途 |
     | --- | --- |
     | **80** | 下载证书吊销列表 (CRL) 的同时验证 TLS/SSL 证书 |
     | **443** | 处理与服务的所有出站通信 |
     | **8080**（可选） | 如果端口 443 不可用，身份验证代理每隔十分钟通过端口 8080 报告其状态。 此状态显示在 Azure AD 门户上。 用户登录不会使用端口 8080。 |
     
     如果防火墙根据原始用户强制实施规则，请打开这些端口以允许来自作为网络服务运行的 Windows 服务的流量。
   - 如果防火墙或代理允许向加入允许列表中添加 DNS 条目，请添加与 **\*.msappproxy.net** 和 **\*.servicebus.windows.net** 的连接。 否则，请允许访问每周更新的 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。
   - 避免在 Azure Passthrough 代理与 Azure 终结点之间，对出站 TLS 通信进行各类形式的内联检查和终止。 
   - 如果有传出 HTTP 代理，请确保此 URL (autologon.microsoftazuread-sso.com) 被列入允许列表中。 应显式指定此 URL，因为可能不接受通配符。 
   - 身份验证代理首次注册需要访问 login.windows.net 和 login.microsoftonline.net。 另外，还请为这些 URL 打开防火墙。
    - 对于证书验证，请取消阻止以下 URL：**crl3.digicert.com:80**、**crl4.digicert.com:80**、**ocsp.digicert.com:80**、**www\.d-trust.net:80**、**root-c3-ca2-2009.ocsp.d-trust.net:80**、**crl.microsoft.com:80**、**oneocsp.microsoft.com:80** 和 **ocsp.msocsp.com:80**。 由于这些 URL 与其他 Microsoft 产品一起用于证书验证，因此可能已取消阻止这些 URL。

### <a name="azure-government-cloud-prerequisite"></a>Azure 政府云的先决条件
在根据步骤 2 通过 Azure AD Connect 启用直通身份验证之前，请先从 Azure 门户下载最新版本的 PTA 代理。  需确保代理版本为 **1.5.1742.0.** 或更高版本。  若要验证代理，请参阅[升级身份验证代理](how-to-connect-pta-upgrade-preview-authentication-agents.md)

下载最新版本的代理后，请继续执行以下说明，通过 Azure AD Connect 配置直通身份验证。

## <a name="step-2-enable-the-feature"></a>步骤 2：启用功能

启用通过 [Azure AD Connect](whatis-hybrid-identity.md) 进行直通身份验证。

>[!IMPORTANT]
>可在 Azure AD Connect 主服务器或暂存服务器上启用直通身份验证。 强烈建议从主服务器启用。 如果将来要设置 Azure AD Connect 暂存服务器，**必须** 继续选择直通身份验证作为登录选项；选择另一个选项将在租户上 **禁用** 直通身份验证并覆盖主服务器中的设置。

若是首次安装 Azure AD Connect，请选择[自定义安装路径](how-to-connect-install-custom.md)。 在“用户登录”页面，选择“直通身份验证”作为“登录方法”。 成功完成上述步骤后，将在 Azure AD Connect 所在的同一服务器上安装直通身份验证代理。 此外，还会在租户中启用直通身份验证功能。

![Azure AD Connect：用户登录](./media/how-to-connect-pta-quick-start/sso3.png)

如果已安装 Azure AD Connect（使用[快速安装](how-to-connect-install-express.md)或[自定义安装](how-to-connect-install-custom.md)路径），请在 Azure AD Connect 上选择“更改用户登录”任务，然后选择“下一步”。 然后选择“直通身份验证”作为登录方法。 成功完成上述步骤后，将在 Azure AD Connect 所在的同一服务器上安装直通身份验证代理，并在租户中启用该功能。

![Azure AD Connect：更改用户登录](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>直通身份验证是租户级功能。 启用此功能将影响租户内所有托管域中的用户登录方式。 如果你从 Active Directory 联合身份验证服务 (AD FS) 切换到直通身份验证，你应等待至少 12 小时以后再关闭 AD FS 基础结构。 此等待时间是为了确保用户在转换期间可以保持登录到 Exchange ActiveSync。 有关从 AD FS 迁移到直通身份验证的更多帮助，请查看我们在[此处](https://aka.ms/adfstoptadpdownload)发布的详细部署计划。

## <a name="step-3-test-the-feature"></a>步骤 3：测试功能

按照这些说明验证是否已正确启用直通身份验证：

1. 使用租户的全局管理员凭据登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 在左窗格中选择“Azure Active Directory”。
3. 选择“Azure AD Connect”。
4. 验证“直通身份验证”功能是否显示为“已启用”。
5. 选择“直通身份验证”。 “直通身份验证”窗格列出了已安装身份验证代理的服务器。

![Azure Active Directory 管理中心：Azure AD Connect 窗格](./media/how-to-connect-pta-quick-start/pta7.png)

![Azure Active Directory 管理中心：直通身份验证窗格](./media/how-to-connect-pta-quick-start/pta8.png)

在此阶段，租户中所有托管域的用户都可以使用直通身份验证登录。 但是，联合域中的用户将继续使用 AD FS 或以前配置的其他联合身份验证提供程序登录。 如果将联合域转换为托管域，则该域中的所有用户将自动开始使用直通身份验证登录。 直通身份验证功能不会影响仅限云的用户。

## <a name="step-4-ensure-high-availability"></a>步骤 4： 确保高可用性

如果计划在生产环境中部署直通身份验证，则应安装其他独立身份验证代理。 在没有运行 Azure AD Connect 的服务器上安装这些身份验证代理。 此设置可提供用户登录请求的高可用性。

>[!IMPORTANT]
>在生产环境中，我们建议你在租户上至少运行 3 个身份验证代理。 系统限制每位租户最多安装 40 个身份验证代理。 最佳做法是将运行身份验证代理的所有服务器视为第 0 层系统（请参阅[参考](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)）。

安装多个直通身份验证代理可以确保高可用性，但不会在身份验证代理之间实现确定性负载均衡。 若要确定租户所需的身份验证代理数，请考虑租户上预期出现的登录请求峰值和平均负载。 作为基准，单一身份验证代理可在标准的 4 核 CPU、16 GB RAM 服务器上每秒处理 300 到 400 个身份验证。

若要估算网络流量，请使用以下大小调整指南：
- 每个请求的有效负载大小为 (0.5K + 1K * num_of_agents) 个字节，即从 Azure AD 传输到身份验证代理的数据。 此处，“num_of_agents”表示在租户上注册的身份验证代理的数量。
- 每个响应的有效负载大小为 1K 字节，即从身份验证代理传输到 Azure AD 的数据。

对于大多数客户而言，总共三个身份验证代理足以满足高可用性和大容量需求。 应在域控制器附近安装身份验证代理以改善登录延迟。

若要开始，请按照以下说明下载身份验证代理软件：

1. 若要下载最新版身份验证代理（版本 1.5.193.0 或更高版本），请使用租户的全局管理员凭据登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 在左窗格中选择“Azure Active Directory”。
3. 依次选择“Azure AD Connect”、“直通身份验证”和“下载代理”。
4. 选择“接受条款并下载”按钮。

![Azure Active Directory 管理中心：“下载身份验证代理”按钮](./media/how-to-connect-pta-quick-start/pta9.png)

![Azure Active Directory 管理中心：“下载代理”窗格](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>也可以直接[下载身份验证代理软件](https://aka.ms/getauthagent)。 安装身份验证代理之前 ，请查看并接受其[服务条款](https://aka.ms/authagenteula)。

以下是部署独立身份验证代理的两种方法：

首先，可以只运行已下载的身份验证代理可执行文件，并在出现提示时提供租户的全局管理员凭据来交互式地执行此操作。

其次，还可以创建并运行无人参与的部署脚本。 如果希望同时部署多个身份验证代理，或在没有启用用户界面的 Windows 服务器上安装身份验证代理，或无法使用远程桌面进行访问时，这种方法非常有用。 以下是如何使用此方法的说明：

1. 运行以下命令以安装身份验证代理：`AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`。
2. 可以通过我们的服务使用 Windows PowerShell 注册身份验证代理。 创建包含租户全局管理用户名和密码的 PowerShell 凭据对象 `$cred`。 运行以下命令，替换 *\<username\>* 和 *\<password\>* ：

  ```powershell
  $User = "<username>"
  $PlainPassword = '<password>'
  $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
  ```
3. 转到 C:\Program Files\Microsoft Azure AD Connect Authentication Agent 并使用创建的 `$cred` 对象运行以下脚本：

  ```powershell
  RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication
  ```

>[!IMPORTANT]
>如果在虚拟机上安装了身份验证代理，则无法克隆虚拟机以设置其他身份验证代理。 此方法 **不受支持**。

## <a name="step-5-configure-smart-lockout-capability"></a>步骤 5：配置 Smart Lockout 功能

Smart Lockout 功能可帮助锁定那些试图猜测用户密码或使用暴力方法闯入的不良参与者。 通过在 Azure AD 中配置 Smart Lockout 设置和/或在本地 Active Directory 中配置适当的锁定设置，可以在攻击访问 Active Directory 之前将其筛选排除。 阅读[本文](../authentication/howto-password-smart-lockout.md)，了解有关如何在租户中配置 Smart Lockout 设置以保护用户帐户的详细信息。

## <a name="next-steps"></a>后续步骤
- [从 AD FS 迁移到传递身份验证](https://aka.ms/adfstoptadp) - 从 AD FS（或其他联合技术）迁移到传递身份验证的详细指南。
- [智能锁定](../authentication/howto-password-smart-lockout.md)：了解如何在租户中配置智能锁定功能以保护用户帐户。
- [当前限制](how-to-connect-pta-current-limitations.md)：了解直通身份验证支持和不支持的方案。
- [技术深入了解](how-to-connect-pta-how-it-works.md)：了解直通身份验证功能的工作原理。
- [常见问题](how-to-connect-pta-faq.md)：查找常见问题的解答。
- [故障诊断](tshoot-connect-pass-through-authentication.md)：了解如何解决直通身份验证功能的常见问题。
- [安全深入了解](how-to-connect-pta-security-deep-dive.md)：获取直通身份验证功能的技术信息。
- [Azure AD 无缝 SSO](how-to-connect-sso.md)：深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 论坛来提交新的功能请求。
