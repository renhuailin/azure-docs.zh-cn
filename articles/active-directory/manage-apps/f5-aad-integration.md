---
title: 使用 F5 实现 Azure AD 安全混合访问 | Microsoft Docs
description: 将 F5 BIG-IP Access Policy Manager 与 Azure Active Directory 集成以实现安全混合访问
services: active-directory
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: miccohen
ms.openlocfilehash: a5066eece8950d4e9dffabedb2e0cca4c527353f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823000"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>将 F5 BIG-IP Access Policy Manager 与 Azure Active Directory 集成以实现安全混合访问

移动办公人员的激增以及不断变化的威胁格局要求对资源访问与治理实施额外的审查，使[零信任](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)成为编写所有现代化程序时要考虑到的首要因素。
Microsoft 和 F5 已经意识到，这项数字变革对于任何企业而言通常是一场历时多年的旅程，在实现现代化之前，可能会使关键资源暴露在威胁之下。 开发 F5 BIG-IP 和 Azure Active Directory 安全混合访问 (SHA) 的初衷不仅仅是为了改进对本地应用程序的远程访问，而且还要增强这些易受攻击的服务的总体安全态势。

在背景方面，根据调查，估计有 60-80% 的本地应用程序在性质上已经陈旧，换而言之，它们不能直接与 Azure Active Directory (AD) 集成。 相同的研究还表明，在这些系统中，有很大一部分仍在低级版本的 SAP、Oracle、SAGE 以及其他提供关键服务的知名工作负载上运行。

SHA 使组织能够继续使用其 F5 投资实现优异的网络和应用程序交付质量，因而解决了这一盲点。 与 Azure AD 相结合，它可以将异构应用程序布局与新式标识控制平面连接到一起。

让 Azure AD 预先验证对 BIG-IP 发布的服务的访问权限可带来诸多好处：

- 通过 [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)、[MS Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)、[Fast Identity Online (FIDO) 密钥](../authentication/howto-authentication-passwordless-security-key.md)和[基于证书的身份验证](../authentication/active-directory-certificate-based-authentication-get-started.md)进行无密码身份验证

- 先行把关的[条件访问](../conditional-access/overview.md)和[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)

- [标识保护](../identity-protection/overview-identity-protection.md) - 通过用户和会话风险分析进行自适应控制

- [泄漏凭据检测](../identity-protection/concept-identity-protection-risks.md)

- [自助式密码重置 (SSPR)](../authentication/tutorial-enable-sspr.md)

- [合作伙伴协作](../governance/entitlement-management-external-users.md) - 针对受监管的来宾访问实施权利管理

- [Cloud App Security (CASB)](/cloud-app-security/what-is-cloud-app-security) - 实现全面的应用发现和控制

- 威胁监视 - [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 可执行高级威胁分析

- [Azure AD 门户](https://azure.microsoft.com/features/azure-portal/) - 用于监管标识和访问的单个控制平面

## <a name="scenario-description"></a>方案描述

作为应用程序交付控制器 (ADC) 和 SSL-VPN，BIG-IP 系统提供对所有类型的服务的本地与远程访问，这些服务包括：

- 新式与旧式 Web 应用程序

- 非基于 Web 的应用程序

- REST 和 SOAP Web API 服务

该系统的 Local Traffic Manager (LTM) 允许通过反向代理功能安全发布服务。 同时，精良的 Access Policy Manager (APM) 通过更丰富的一组功能扩展了 BIG-IP，可以实现联合与单一登录 (SSO)。

集成基于 APM 与 Azure AD 之间的标准联合信任，这种信任在包含 [SSL-VPN 方案](f5-aad-password-less-vpn.md)的大多数 SHA 用例中很常见。 安全断言标记语言 (SAML)、OAuth 和 Open ID Connect (OIDC) 资源也不例外，因为它们也可以受到保护，以确保对它们的远程访问安全。 还可能存在这样的情况：对任何服务（包括 SaaS 应用）进行零信任访问时，BIG-IP 变成了瓶颈。

由于 BIG-IP 可与 Azure AD 集成，因此能够实现所需的协议转换，从而可以通过[无密码身份验证](https://www.microsoft.com/security/business/identity/passwordless)和[条件访问](../conditional-access/overview.md)等新式控制措施来保护旧式或非 Azure AD 集成式服务。 在这种情况下，BIG-IP 持续履行其作为反向代理的角色，同时根据每个服务，将预身份验证和授权工作转交给 Azure AD。

图中的步骤 1-4 演示了服务提供商发起的流中，用户、BIG-IP 与 Azure AD 之间的前端预身份验证交换。 步骤 5-6 显示了后续的 APM 会话扩充，以及对各个后端服务执行的 SSO。

![插图显示了概要体系结构](./media/f5-aad-integration/integration-flow-diagram.png)

| 步骤 | 说明 |
|:------|:-----------|
| 1. | 用户在门户中选择一个应用程序图标，此时会解析 SAML SP (BIG-IP) 的 URL |
| 2. | BIG-IP 将用户重定向到 SAML IDP (Azure AD) 进行预身份验证|
| 3. | Azure AD 处理有关授权的条件访问策略和[会话控制](../conditional-access/concept-conditional-access-session.md)|
| 4. | 用户重定向回到 BIG-IP，并提供 Azure AD 颁发的 SAML 声明 |
| 5. | BIG-IP 请求任何额外的会话信息，这些信息将包含在对发布的服务执行的 [SSO](../hybrid/how-to-connect-sso.md) 和[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 中 |
| 6. | BIG-IP 将客户端请求转发到后端服务

## <a name="user-experience"></a>用户体验

无论是直属员工、隶属单位还是消费者，大多数用户都已经熟悉了 Office 365 登录体验，因此通过 SHA 访问 BIG-IP 服务基本上都是轻车熟路。

现在，无论使用哪种类型的设备或位于何处，用户都可以找到 BIG-IP 发布的服务：这些服务已与更广泛的一组服务的自助功能一起，整合到了[“我的应用”](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)或 [O365 Launchpad](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) 中。 如果需要，用户甚至可以继续通过 BIG-IP 专属的 Webtop 门户直接访问已发布的服务。 注销时，SHA 可确保在两端（BIG-IP 和 Azure AD）终止用户的会话，确保服务仍完全受到保护，不会受到未经授权的访问。  

提供的屏幕截图取自 Azure AD 应用门户，该门户可供用户安全访问以查找 BIG-IP 发布的服务，以及管理其帐户属性。  

![该屏幕截图显示了 woodgrove myapps 库](media/f5-aad-integration/woodgrove-app-gallery.png)

![该屏幕截图显示了 woodgrove myaccounts 自助服务页](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>见解和分析

BIG-IP 的角色对于任何企业而言至关重要，因此应该监视已部署的 BIG-IP 实例，确保发布的服务在 SHA 级别以及在操作上都高度可用。

可以使用多个选项通过安全信息和事件管理 (SIEM) 解决方案在本地或者远程记录事件，实现遥测数据的离线存储和处理。 监视 Azure AD 和 SHA 特定活动的高效解决方案是使用 [Azure Monitor](../../azure-monitor/overview.md) 和 [Azure Sentinel](../../sentinel/overview.md)，两者共同提供：

- 可能跨多个云或本地位置的组织的详细概述，包括 BIG-IP 基础结构

- 单个控制平面，提供所有信号的组合视图，可避免依赖复杂的异类工具

![该插图显示了监视流](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>先决条件

必须满足以下先决条件，才能将 F5 BIG-IP 与 Azure AD 集成以实现 SHA：

- 在以下任一平台上运行的 F5 BIG-IP 实例：

  - 物理设备

  - 虚拟机监控程序虚拟版，例如 Microsoft Hyper-V、VMware ESXi、Linux KVM 和 Citrix Hypervisor

  - 云虚拟版，例如 Azure、VMware、KVM、Community Xen、MS Hyper-V、AWS、OpenStack 和 Google Cloud

    BIG-IP 实例的实际位置可以是本地或任何受支持的云平台（包括 Azure），前提是它已连接到 Internet、所要发布的资源，以及任何其他所需服务（例如 Active Directory）。  

- 有效的 F5 BIG-IP APM 许可证，可通过以下选项之一获取：

  - F5 BIG-IP® Best 捆绑包（或者）

    - F5 BIG-IP Access Policy Manager™ 独立许可证

    - 现有 BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) 上的 F5 BIG-IP Access Policy Manager™ (APM) 加载项许可证

    - 有效期 90 天的 BIG-IP Access Policy Manager™ (APM) [试用版许可证](https://www.f5.com/trial/big-ip-trial.php)

- 通过以下选项之一获取 Azure AD 许可：

  - Azure AD [免费订阅](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20)可以满足使用无密码身份验证实现 SHA 的最低核心要求

  - [Premium 订阅](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)提供前言中所述的所有附加价值，包括[条件访问](../conditional-access/overview.md)、[MFA](../authentication/concept-mfa-howitworks.md) 和[标识保护](../identity-protection/overview-identity-protection.md)

无需具备任何经验或 F5 BIG-IP 知识也能实现 SHA，但我们建议你熟悉 F5 BIG-IP 的术语。 F5 丰富的[知识库](https://www.f5.com/services/resources/glossary)也是一个开始积累 BIG-IP 知识的好去处。

## <a name="deployment-scenarios"></a>部署方案

以下教程提供了有关实现一些较常用 BIG-IP 和 Azure AD SHA 模式的详细指导：

- [在Azure 中部署 F5 BIG-IP 的演练](f5-bigip-deployment-guide.md)

- [为 Kerberos 应用程序配置 F5 BIG-IP APM 和 Azure AD SSO](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [为基于头的应用程序配置 F5 BIG-IP APM 和 Azure AD SSO](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [使用 Azure AD SHA 保护 F5 BIG-IP SSL-VPN](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>其他资源

- [密码身份验证的时代已结束，请改用无密码方式](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory 安全混合访问](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [用于实现远程办公的 Microsoft 零信任框架](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure Sentinel 入门](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>后续步骤

考虑使用现有 BIG-IP 基础结构或通过部署试验实例来运行 SHA 概念证明 (POC)。 [将 BIG-IP 虚拟版 (VE) VM 部署到 Azure](f5-bigip-deployment-guide.md) 大约需要 30 分钟，完成后你将获得：

- 一个完全受保护的平台，用于为 SHA 概念证明建模

- 一个生产前实例（完全受保护的平台），用于测试新 BIG-IP 系统的更新和修补程序

同时，应该确定一个或两个可以通过 BIG-IP 发布并通过 SHA 保护的目标应用程序。  

我们的建议是从一个尚未通过 BIG-IP 发布的应用程序着手，以尽可能避免生产服务中断。 本文中所述的指导原则可帮助你熟悉创建各种 BIG-IP 配置对象及设置 SHA 的一般过程。 完成后，你应该可以对任何其他新服务执行同样的操作，此外还具备了足够的知识，可以轻松转换 BIG-IP 发布的现有服务以实现 SHA。

以下交互式指南逐步介绍了实现 SHA 以及了解最终用户体验的概要过程。

[![该插图显示了交互式指南的封面](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)