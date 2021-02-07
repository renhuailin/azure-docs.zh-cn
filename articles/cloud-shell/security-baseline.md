---
title: 适用于 Cloud Shell 的 Azure 安全基线
description: Cloud Shell 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: fde7c7fa619c6d0a36ef0890f31375ac3933e6ee
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807659"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>适用于 Cloud Shell 的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指南应用到 Cloud Shell。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容由 Azure 安全基准定义的 **安全控制** 和适用于 Cloud Shell 的相关指南进行分组。 排除了不适用于 Cloud Shell 的 **控件**。

 
若要查看 Cloud Shell 完全映射到 Azure 安全基准，请参阅 [完整的 Cloud Shell 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：客户可将 Azure Cloud Shell 部署到客户拥有的虚拟网络中。

将 Azure Cloud Shell 部署到客户拥有的虚拟网络中时，必须创建或使用现有虚拟网络。 确保所选虚拟网络具有一个网络安全组，该网络安全组应用到其子网和配置了特定于应用程序的受信任端口和源的网络访问控制。

- [将 Cloud Shell 部署到 Azure 虚拟网络](private-vnet.md)

- [如何创建包含安全规则的网络安全组](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，它使用用于访问 Azure 门户的相同授权，在这种情况下，AZURE 门户的 SSO 还将使用 Cloud Shell 进行身份验证。 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，它使用用于访问 Azure 门户的相同授权，在这种情况下，Cloud Shell 还需要任何连接到 AZURE 门户的 MFA。 

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  每个客户容器都是暂时的，每个会话都使用一个新容器。  Cloud Shell 团队监视和更新容器映像。

Azure Cloud Shell 允许客户根据组织的需要在其自己的映像中安装自己的工具或软件。

客户负责针对环境中运行的软件运行自动漏洞扫描工具。  

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动化补丁管理解决方案

**指南**：不适用;Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  每个客户容器都是暂时的，每个会话都使用一个新容器。  Cloud Shell 团队监视和更新容器映像。

Azure Cloud Shell 允许客户根据组织的需要在其自己的映像中安装自己的工具或软件。

客户负责在其环境中运行的软件修补程序管理。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  每个客户容器都是暂时的，每个会话都使用一个新容器。  Cloud Shell 团队监视和更新容器映像。

Azure Cloud Shell 允许客户根据组织的需要在其自己的映像中安装自己的工具或软件。

客户负责修正通过其软件漏洞扫描发现的漏洞。 以一致的间隔导出扫描结果，并将结果与以前的扫描进行比较以验证漏洞是否已修复。 使用 Azure 安全中心建议的漏洞管理建议时，可以转到选定解决方案的门户查看历史扫描数据。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  每个客户容器都是暂时的，每个会话都使用一个新容器。  Cloud Shell 团队监视和更新容器映像。

Azure Cloud Shell 允许客户根据组织的需要在其自己的映像中安装自己的工具或软件。

客户负责修正通过其软件漏洞扫描发现的漏洞。  使用通用风险评分程序（例如通用漏洞评分系统）或第三方扫描工具提供的默认风险评级。 

- [NIST 出版物 - 通用漏洞评分系统](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  每个客户容器都是暂时的，每个会话都使用一个新容器。  Cloud Shell 团队监视和更新容器映像和工具。  根据组织的需要，客户可以在自己的映像中安装自己的工具，并且在安装过程中，这些工具不需要 `sudo` 权限。

建议客户根据组织的需要，创建通过 Azure Cloud Shell 安装的已批准软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**： Azure Cloud Shell 是一种免费服务，没有客户拥有的资产。  Cloud Shell 团队监视和更新容器映像和工具。 

Azure Cloud Shell 允许客户根据组织的需要在其自己的映像中安装自己的工具或软件。

客户负责监视环境中运行的软件应用程序，以确保按组织策略批准它们。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**： Azure Cloud Shell 是一种免费服务，没有客户拥有的资产。  Cloud Shell 团队监视和更新容器映像和工具。 

Azure Cloud Shell 允许客户根据组织的需要在其自己的映像中安装自己的工具或软件。

客户负责监视环境中运行的软件应用程序，以确保按组织策略管理未批准的软件。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**： Azure Cloud Shell 是一种免费服务，没有客户拥有的资产。  Cloud Shell 团队监视和更新容器映像和工具。  用户可能不会删除特定的工具。

Azure Cloud Shell 允许客户根据组织的需要在其自己的映像中安装自己的工具或应用程序。

客户负责监视环境中运行的应用程序，以确保按组织策略批准它们。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：不适用;Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  每个客户容器都是暂时的，每个会话都使用一个新容器。  Cloud Shell 团队监视和更新容器映像。

Azure Cloud Shell 允许客户根据组织的需要在其自己的映像中安装自己的工具或软件。

客户负责维护在环境中运行的已批准软件的清单，以确保它们是按组织策略批准的软件。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  在 Cloud Shell 函数中执行的操作与在本地环境中运行的相同工具或语言中执行的操作相同。  应限制各个工具和语言的操作，客户无法限制对 Cloud Shell 或限制用户可用的内容。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**： Azure Cloud Shell 可以在客户虚拟网络中隔离。

- [将 Cloud Shell 部署到 Azure 虚拟网络](private-vnet.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**： Cloud Shell 允许脚本在中运行、创作并上载到 Cloud Shell 环境。  建议将凭据移动到 Azure Key Vault。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反恶意软件

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  每个客户容器都是暂时的，每个会话都使用一个新容器。  Cloud Shell 团队监视和更新容器映像和工具。  根据组织的需要，客户可以在自己的映像中安装自己的工具，并且在安装过程中，这些工具不需要 `sudo` 权限。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**： Azure Cloud Shell 是一种基于浏览器的命令行体验，用于交互式管理云资源。  每个客户容器都是暂时的，每个会话都使用一个新容器。  Cloud Shell 团队监视和更新容器映像和工具。  根据组织的需要，客户可以在自己的映像中安装自己的工具，并且在安装过程中，这些工具不需要 `sudo` 权限。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。
- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md) 
- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果或分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。
此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。
- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md) 
- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md) 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。
- [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南） 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。
- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md) 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。
- [如何配置连续导出](../security-center/continuous-export.md) 
- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md) 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。
- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。
- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 
- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) 

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)