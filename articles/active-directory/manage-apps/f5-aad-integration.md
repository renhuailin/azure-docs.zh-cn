---
title: 使用 F5 Azure AD 安全的混合访问 |Microsoft Docs
description: F5 大 IP 访问策略管理器和 Azure Active Directory 集成以实现安全的混合访问
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8210e00824d7680f4eecde2f0b299dfcdc93b90
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730565"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 大 IP 访问策略管理器和 Azure Active Directory 集成以实现安全的混合访问

移动性和不断变化的威胁的发展方向是对资源访问和管理进行了额外的审查，并将 [零信任](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) 的前端和中心置于所有的现代化计划中。
在 Microsoft 和 F5 下，我们意识到这种数字转换通常为任何业务的多年旅程，并可能会在现代化之前公开关键资源。 Genesis 位于 F5 大 IP 和 Azure Active Directory 安全混合访问 (SHA) 旨在提高对本地应用程序的远程访问权限，同时增强这些易受攻击的服务的总体安全状态。

对于上下文，调查估计，60-80% 的本地应用程序在本质上是旧的，或者不能与 Azure Active Directory (AD) 直接集成。 同样的一项研究还表明，这些系统中的大量系统在 SAP、Oracle、SAGE 和其他提供重要服务的常见工作负载的下层版本上运行。

SHA 通过使组织能够继续将其 F5 投资用于高级网络和应用程序交付，来解决此盲人的作用。 与 Azure AD 结合在一起，它将异类应用程序环境与新式标识控制平面结合在一起。

让 Azure AD 对大 IP 已发布服务进行预身份验证访问具有很多好处：

- 通过[Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)、 [MS 身份](../user-help/user-help-auth-app-download-install.md)验证器、[快速标识在线 (FIDO) 密钥](../authentication/howto-authentication-passwordless-security-key.md)和[基于证书的身份验证](../authentication/active-directory-certificate-based-authentication-get-started.md)进行密码不太验证

- 抢先 [条件访问](../conditional-access/overview.md) 和 [多重身份验证 (MFA) ](../authentication/concept-mfa-howitworks.md)

- [标识保护](../identity-protection/overview-identity-protection.md) -通过用户和会话风险分析进行自适应控制


- [泄漏的凭据检测](../identity-protection/concept-identity-protection-risks.md)

- [自助服务密码重置 (SSPR) ](../authentication/tutorial-enable-sspr.md)

- [合作伙伴协作](../governance/entitlement-management-external-users.md) -受管辖的来宾访问权限管理

- [Cloud App Security (CASB) ](/cloud-app-security/what-is-cloud-app-security) -用于完整应用发现和控制

- 威胁监视-用于高级威胁分析的[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)

- [Azure AD 门户](https://azure.microsoft.com/features/azure-portal/)-用于管理标识和访问的单个控制平面

## <a name="scenario-description"></a>方案描述

作为应用程序传送控制器 (ADC) 和 SSL VPN，大型 IP 系统提供对所有类型服务的本地和远程访问，包括：

- 新式 web 应用程序和旧版 web 应用程序

- 非基于 web 的应用程序

- REST 和 SOAP Web API 服务

其本地流量管理器 (LTM) 允许通过反向代理功能安全发布服务。 同时，高级访问策略管理器 (APM) 使用一组更丰富的功能扩展了大 IP，同时启用了 (SSO) 的联合和单一登录。

此集成基于 APM 与 Azure AD （包括 [SSL VPN 方案](f5-aad-password-less-vpn.md)的大多数 SHA 用例共有）之间的标准联合身份验证信任。 安全断言标记语言 (SAML) 、OAuth 和 Open ID Connect (OIDC) 资源也不例外，因为它们也可以安全地进行远程访问。 还可能会出现这样的情况：大 IP 变为不能访问所有服务（包括 SaaS 应用）的不受信任点。

与 Azure AD 进行集成的大 IP 能力可以实现协议转换，以使用新式控件（例如无 [密码身份验证](https://www.microsoft.com/security/business/identity/passwordless) 和 [条件性访问](../conditional-access/overview.md)）保护旧式或非 Azure AD 集成的服务。 在这种情况下，大 IP 会继续将其角色作为反向代理来实现，同时根据每个服务，将预身份验证和授权提交到 Azure AD。

此图中的步骤1-4 说明了在服务提供商启动的流中，用户、大 IP 和 Azure AD 之间的前端预身份验证交换。 步骤5-6 将后续的 APM 会话扩充和 SSO 显示给单个后端服务。

![图像显示高级体系结构](./media/f5-aad-integration/integration-flow-diagram.png)

| 步骤 | 说明 |
|:------|:-----------|
| 1. | 用户在门户中选择一个应用程序图标，将 URL 解析为 SAML SP (大 IP)  |
| 2. | 大 IP 将用户重定向到 SAML IDP (Azure AD) 进行预身份验证|
| 3. | Azure AD 处理身份验证的条件性访问策略和[会话控制](../conditional-access/concept-conditional-access-session.md)|
| 4. | 用户重定向回大 IP，以显示 Azure AD 颁发的 SAML 声明 |
| 5. | 大 IP 请求任何附加的会话信息，包括在 [SSO](../hybrid/how-to-connect-sso.md) 和 [基于角色的访问控制 (RBAC) ](../../role-based-access-control/overview.md) 发布到服务 |
| 6. | 大 IP 将客户端请求转发到后端服务

## <a name="user-experience"></a>用户体验

无论是直接员工、附属人员还是消费者，大多数用户都已经熟悉 Office 365 登录体验，因此通过 SHA 访问大 IP 服务仍非常熟悉。

用户现在可以在  [MyApps](../user-help/my-apps-portal-end-user-access.md) 或 [O365 launchpads](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) 中整合其大 IP 已发布服务，还可以将自助服务功能合并到更广泛的一组服务，不管设备类型或位置如何。 如果首选，用户甚至可以通过 BIG-IPs 专有的 Webtop 门户继续直接访问已发布的服务。 注销时，SHA 确保用户的会话在两个端（即大 IP 和 Azure AD）都能终止，确保服务能够完全受到未经授权的访问。  

提供的屏幕截图来自 Azure AD 应用门户，用户可以通过安全的方式进行访问，查找其大 IP 已发布服务并管理其帐户属性。  

![屏幕截图显示 woodgrove myapps 库](media/f5-aad-integration/woodgrove-app-gallery.png)

![屏幕截图显示 woodgrove myaccounts 自助服务页](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>见解和分析

大 IP 角色对于任何企业而言都是至关重要的，因此，应监视已部署的大 IP 实例，以确保已发布的服务高度可用，同时在 SHA 级别和操作上操作。

有多个选项可用于以本地方式记录事件，或者通过安全信息和事件管理以远程方式 (SIEM) 解决方案，从而实现了脱机存储和遥测处理。 用于监视 Azure AD 和特定于 SHA 的活动的一种非常有效的解决方案是使用 [Azure Monitor](../../azure-monitor/overview.md) 和 [Azure Sentinel](../../sentinel/overview.md)，同时提供：

- 组织的详细概述，可能跨多个云以及本地位置（包括大 IP 基础结构）

- 单个控制平面，提供所有信号的组合视图，避免依赖复杂、完全不同的工具

![此图像显示监视流](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>先决条件

将 F5 大 IP 与 SHA 的 Azure AD 集成具有以下先决条件：

- 在以下任一平台上运行的 F5 大 IP 实例：

  - 物理设备

  - 虚拟机监控程序虚拟版，如 Microsoft Hyper-V、VMware ESXi、Linux KVM 和 Citrix 虚拟机监控程序

  - 云虚拟版，例如 Azure、VMware、KVM、社区 Xen、MS Hyper-v、AWS、OpenStack 和 Google Cloud

    大 IP 实例的实际位置可以是本地的，也可以是任何受支持的云平台（包括 Azure），前提是它已连接到 Internet、要发布的资源以及其他任何所需的服务，如 Active Directory。  

- 活动 F5 大 IP APM 许可证，通过以下选项之一：

   - F5 BIG-IP® Best 捆绑包（或者）

   - F5 大 IP 访问策略管理器™独立许可证

   - F5 大 IP 访问策略管理器™ (APM) 现有大 IP F5 大 IP 上的附加许可证®本地流量管理器™ (LTM) 

   - 90天的大 IP 访问策略管理器™ (APM) [试用许可证](https://www.f5.com/trial/big-ip-trial.php)

- 通过以下任一选项 Azure AD 许可：

   - Azure AD [免费订阅](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) 提供了通过无密码身份验证实现 SHA 的最低核心要求

   - [高级订阅](https://azure.microsoft.com/pricing/details/active-directory/)提供了前言中概括的所有附加值，包括条件性[访问](../conditional-access/overview.md)、 [MFA](../authentication/concept-mfa-howitworks.md)和[标识保护](../identity-protection/overview-identity-protection.md)

若要实现 SHA，无需任何以前的经验或 F5 大 IP 知识，但我们建议你自行熟悉。 F5 bigip 丰富的 [知识库](https://www.f5.com/services/resources/glossary) 也是开始构建大 IP 知识的好地方。

## <a name="deployment-scenarios"></a>部署方案

以下教程提供了有关为大 IP 和 Azure AD SHA 实现一些更常见的模式的详细指导：

- [在 Azure 部署演练中按 F5 大 IP](f5-bigip-deployment-guide.md)

- [F5 大 IP APM 并 Azure AD SSO 应用程序](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 大 IP APM 和 Azure AD SSO 到基于标头的应用程序](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [通过 Azure AD SHA 保护 F5 大 IP SSL VPN](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>其他资源

- [密码结束，请无密码](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory 安全的混合访问](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [用于启用远程工作的 Microsoft 零信任框架](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure Sentinel 入门](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>后续步骤

请考虑使用现有的大 IP 基础结构或部署试用实例， (POC) 运行 SHA 概念证明。 将[大 IP 虚拟版 (VE) VM 部署到 Azure](f5-bigip-deployment-guide.md)大约需要30分钟，此时你会遇到以下情况：

- 一个完全安全的平台，用于建模 SHA 概念证明

- 预生产实例，用于测试新的大型 IP 系统更新和修补程序的完全安全的平台

同时，你应该确定一个或两个应用程序，该应用程序可以通过大 IP 进行发布并通过 SHA 保护。  

我们的建议是从尚未通过大 IP 发布的应用程序开始，以避免对生产服务的潜在中断。 本文中所述的准则将帮助你熟悉创建各种大 IP 配置对象和设置 SHA 的一般过程。 完成后，您应该能够使用任何其他新服务执行同样的操作，此外还提供了足够的知识来将现有的大 IP 已发布服务转换到 SHA，只需最少的工作量。

下面的交互式指南介绍了实现 SHA 并查看最终用户体验的高级过程。

[![此图像显示了交互式指南涵盖](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
