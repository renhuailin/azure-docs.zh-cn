---
title: Azure 安全中心定价
description: Azure 安全中心提供两种模式，分别随附和不随附 Azure Defender。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2020
ms.author: memildin
ms.openlocfilehash: 99f47df86d89e9daf2bc8878b868b04b7038ffd5
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071198"
---
# <a name="pricing-of-azure-security-center"></a>Azure 安全中心定价
Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。 它可以提供针对混合云工作负载的可见性和可控性、可减小在威胁下的曝光面的积极防御功能以及有助于随时响应快速演变的网络风险的智能检测功能。


## <a name="free-option-vs-azure-defender-enabled"></a>“免费选项”和“启用 Azure Defender”

安全中心提供两种模式：

- **Azure Defender 关闭**（免费）- 当你首次在 Azure 门户中访问 Azure 安全中心仪表板时，或通过 API 以编程方式启用后，会在你的所有 Azure 订阅上免费启用未启用 Azure Defender 的安全中心。 此免费模式可提供安全策略、持续的安全评估和切实可行的安全建议来帮助你保护 Azure 资源。

- **Azure Defender 打开** - 启用 Azure Defender 后，会将免费模式的功能扩展到私有云和其他公有云中运行的工作负载，并在混合云工作负载中提供了统一的安全管理和威胁防护。 Azure Defender 的一些主要功能：

    - **Microsoft Defender for Endpoint** - 适用于服务器的 Azure Defender 包括 [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender)，以实现全面的终结点检测和响应 (EDR)。 如需详细了解结合使用 Microsoft Defender for Endpoint 和 Azure Defender 的好处，请参阅[使用安全中心的集成 EDR 解决方案](security-center-wdatp.md)。
    - **虚拟机和容器注册表的漏洞扫描** - 轻松将扫描程序部署到所有虚拟机，这是业界最先进的漏洞管理解决方案。 直接在安全中心内查看、调查和修复扫描到的漏洞。 
    - 混合安全 – 在所有本地和云工作负载上获得统一的安全视图。 应用安全策略并持续评估混合云工作负载的安全性，确保符合安全标准。 收集、搜索并分析来自多个源（包括防火墙和其他合作伙伴解决方案）的安全数据。
    - **威胁防护警报** - 高级行为分析和 Microsoft Intelligent Security Graph 针对不断演变的网络攻击提供防护边界。 内置行为分析和机器学习可识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。 使用交互工具和上下文威胁智能简化调查。
    - **访问和应用程序控件** (AAC) - 通过应用适合特定工作负载且由机器学习提供支持的建议来创建允许和拒绝列表，阻止恶意软件和其他不需要的应用程序。 实时减小网络受攻击面，控制对 Azure VM 上的管理端口的访问。 AAC 显著降低了遭受暴力攻击和其他网络攻击的风险。
    - **容器安全功能** - 获得在容器化环境中进行漏洞管理和实时威胁保护的好处。 启用适用于容器注册表的 Azure Defender 时，等待全部功能启用完毕可能需要最多 12 个小时。 根据推送到已连接注册表的唯一容器映像的数量收费。 一个映像经过一次扫描后，不会再对其收取相关费用，除非再次对其进行了修改和推送。
    - 适用于连接到 Azure 环境的资源的广度威胁防护 - Azure Defender 包括对所有资源通用的 Azure 服务的 Azure 本机广度威胁防护：Azure 资源管理器、Azure DNS、Azure 网络层和 Azure Key Vault。 Azure Defender 对 Azure 管理层和 Azure DNS 层具有独特的可见性，因此可以保护连接到这些层的云资源。


## <a name="try-azure-defender-free-for-30-days"></a>免费试用 Azure Defender 30 天
Azure Defender 前 30 天免费。 30 天后，如果选择继续使用服务，我们会自动开始收取使用费用。

## <a name="enable-azure-defender"></a>启用 Azure Defender
可以使用 Azure Defender 保护整个 Azure 订阅，且该保护将由订阅中的所有资源继承。

启用 Azure Defender：

1. 从安全中心的主菜单中，选择“定价和设置”。
1. 选择要升级的订阅。
1. 选择要升级的“Azure Defender 打开”。
1. 选择“保存”。

下面是一个示例订阅的定价页。 可以看到，Azure Defender 中的每个计划是单独定价的，并可以单独设置为“打开”或“关闭”。

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="门户中安全中心的定价页":::

> [!NOTE]
> 若要启用包括威胁防护功能在内的全部安全中心功能，必须在包含适用工作负载的订阅上启用 Azure Defender。 如果在工作区级别启用它，则不会为 Azure 资源启用实时 VM 访问、自适应应用程序控制和网络检测功能。 此外，只在工作区级别提供两种 Azure Defender 计划：适用于服务器的 Azure Defender，以及适用于计算机上的 SQL Server 的 Azure Defender。
>
> 可以在订阅级别或资源级别启用 **用于存储的 Azure Defender 帐户**。
> 可以在订阅级别或资源级别启用 **用于 SQL 的 Azure Defender**。
> 只能在资源级别为 **Azure Database for MariaDB/MySQL/PostgreSQL** 启用威胁防护。


## <a name="faq---pricing-and-billing"></a>常见问题解答 - 定价和计费 

- [如何跟踪我的组织中谁已在 Azure 安全中心启用 Azure Defender 更改？](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [安全中心提供了哪些计划？](#what-are-the-plans-offered-by-security-center)
- 如何为订阅启用 Azure Defender？
- 能否在我的订阅中为服务器子集上的服务器启用 Azure Defender？
- [如果我已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [我的订阅已为服务器启用了 Azure Defender，是否需要为未运行的服务器付费？](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [是否需要为未安装 Log Analytics 代理的计算机付费？](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [如果 Log Analytics 代理向多个工作区报告，是否需要重复付费？](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [如果 Log Analytics 代理向多个工作区报告，是否所有工作区上均提供 500-MB 的免费数据引入？](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [500 MB 免费数据引入量是针对整个工作区计算还是严格按每台计算机计算得出的？](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>如何跟踪我的组织中谁已在安全中心启用 Azure Defender 更改？
Azure 订阅可能具有多个管理员，这些管理员有权更改定价设置。 若要找到做出更改的用户，请使用 Azure 活动日志。

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="显示定价更改事件的 Azure 活动日志":::

如果“事件发起者”列中未列出用户信息，请查看事件的 JSON 了解相关详细信息。

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure 活动日志 JSON 资源管理器":::


### <a name="what-are-the-plans-offered-by-security-center"></a>安全中心提供了哪些计划？ 
安全中心有两个产品/服务： 

- Azure 安全中心免费版 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>如何为订阅启用 Azure Defender？ 
可以使用以下任一方法启用订阅的 Azure Defender： 

|方法  |说明  |
|---------|---------|
|Azure 门户的 Azure 安全中心页面|[启用 Azure Defender](#enable-azure-defender)|
|REST API|[定价 API](/rest/api/securitycenter/pricings)|
|Azure CLI|[az security 定价](/cli/azure/security/pricing)|
|PowerShell|[Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)|
|Azure Policy|[捆绑定价](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json)|
|||

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>能否在我的订阅中为服务器子集上的服务器启用 Azure Defender？
不是。 在订阅上启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 时，Azure Defender 会保护订阅中的所有服务器。 

替代方法如下：在 Log Analytics 工作区级别为服务器启用 Azure Defender。 如果执行此操作，将仅保护向该工作区报告的服务器并对其计费。 但某些功能将不可用。 其中包括实时 VM 访问、网络检测、法规合规性、自适应网络强化、自适应应用程序控制等。 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>如果我已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？
如果你已获得 Microsoft Defender for Endpoint 的许可证，则无需为 Azure Defender 许可证的相应部分付费。

若要确认折扣，请联系安全中心的支持团队，并提供相关工作区 ID、区域和许可证信息。

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>我的订阅已为服务器启用了 Azure Defender，是否需要为未运行的服务器付费？ 
不是。 在订阅上启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 时，仅针对正在运行的服务器按小时计费。 无需为关闭的任何服务器付费。 

> [!TIP]
> 这也适用于安全中心所保护的其他资源类型。 

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>是否需要为未安装 Log Analytics 代理的计算机付费？
是的。 在订阅上启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 时，即使未安装 Log Analytics 代理，该订阅中的计算机也会受到一系列保护。

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>如果 Log Analytics 代理向多个工作区报告，是否需要重复付费？ 
是的。 如果已将 Log Analytics 代理配置为将数据发送到两个或多个不同的 Log Analytics 工作区（多宿主），则需要为每个安装了“安全”或“反恶意软件”解决方案的工作区付费。 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>如果 Log Analytics 代理向多个工作区报告，是否所有工作区上均提供 500-MB 的免费数据引入？
是的。 如果已将 Log Analytics 代理配置为将数据发送到两个或多个不同的 Log Analytics 工作区（多宿主），则将获得 500-MB 的免费数据引入。 它是按每个节点、每个报告的工作空间、每一天来计算的，并且适用于安装了“安全”或“反恶意软件”解决方案的所有工作空间。 你将需要为超出 500 MB 的引入数据付费。

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>500 MB 免费数据引入量是针对整个工作区计算还是严格按每台计算机计算得出的？
对于连接到工作区的每台计算机，你每天都可免费引入 500 MB 的数据。 专用于直接由 Azure 安全中心收集的安全数据类型。

此数据在所有节点中按每日费率平摊。 因此，即使某些计算机发送 100 MB 的数据，另一些发送 800 MB 的数据，只要总量不超过免费限额（[计算机数量] x 500 MB），我们就不会对你额外收费。

## <a name="next-steps"></a>后续步骤
本文介绍了安全中心的定价选项。 如需查看相关材料，请参阅：

- [如何优化 Azure 工作负载成本](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [根据所选货币和所在区域的定价详细信息](https://azure.microsoft.com/pricing/details/security-center/)
- 你可能希望管理成本，并通过将解决方案的应用范围限制为特定的一组代理来限制为解决方案收集的数据量。 [解决方案目标](../azure-monitor/insights/solution-targeting.md)使用户可以向解决方案应用一个范围，并可将目标设定为工作区中的一个计算机子集。 如果使用解决方案目标功能，安全中心会将工作区列为没有解决方案。