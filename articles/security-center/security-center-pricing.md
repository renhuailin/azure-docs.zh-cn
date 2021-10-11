---
title: Azure 安全中心免费版与已启用 Azure Defender
description: 了解在 Azure 安全中心为云工作负载保护启用 Azure Defender 的好处
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 09/19/2021
ms.openlocfilehash: 3e76d298d30d2e191edf218e85a8fc64758f8359
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560781"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure 安全中心免费版与已启用 Azure Defender
Azure Defender 前 30 天免费。 30 天后，如果选择继续使用服务，我们会自动开始收取使用费用。

可以从“定价与设置”页升级，如[快速入门：启用 Azure Defender](enable-azure-defender.md) 中所述。 有关所选货币以及你所在区域的定价详细信息，请参阅[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>启用 Azure Defender 有哪些优点？

安全中心提供两种模式：

- **Azure Defender 关闭**（免费）- 当你首次在 Azure 门户中访问 Azure 安全中心仪表板时，或通过 API 以编程方式启用后，会在你的所有 Azure 订阅上免费启用未启用 Azure Defender 的安全中心。 此免费模式可提供安全策略、持续的安全评估和切实可行的安全建议来帮助你保护 Azure 资源。

- **Azure Defender 打开** - 启用 Azure Defender 后，会将免费模式的功能扩展到私有云和其他公有云中运行的工作负载，并在混合云工作负载中提供了统一的安全管理和威胁防护。 Azure Defender 的一些主要功能：

    - **Microsoft Defender for Endpoint** - 适用于服务器的 Azure Defender 包括 [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender)，以实现全面的终结点检测和响应 (EDR)。 如需详细了解结合使用 Microsoft Defender for Endpoint 和 Azure Defender 的好处，请参阅[使用安全中心的集成 EDR 解决方案](security-center-wdatp.md)。
    - **虚拟机和容器注册表的漏洞扫描** - 轻松将扫描程序部署到所有虚拟机，这是业界最先进的漏洞管理解决方案。 直接在安全中心内查看、调查和修复扫描到的漏洞。 
    - 混合安全 – 在所有本地和云工作负载上获得统一的安全视图。 应用安全策略并持续评估混合云工作负载的安全性，确保符合安全标准。 收集、搜索并分析来自多个源（包括防火墙和其他合作伙伴解决方案）的安全数据。
    - **威胁防护警报** - 高级行为分析和 Microsoft Intelligent Security Graph 针对不断演变的网络攻击提供防护边界。 内置行为分析和机器学习可识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。 使用交互工具和上下文威胁智能简化调查。
    - **跟踪是否符合一系列标准** - 安全中心持续评估混合云环境，根据 [Azure 安全基准](/security/benchmark/azure/introduction)中的控制措施和最佳做法来分析风险因素。 启用 Azure Defender 后，可以根据组织的需要应用一系列其他行业标准、监管标准和基准。 添加标准并在[监管合规性仪表板](update-regulatory-compliance-packages.md)中跟踪是否符合这些标准。
    - **访问和应用程序控件** (AAC) - 通过应用适合特定工作负载且由机器学习提供支持的建议来创建允许和拒绝列表，阻止恶意软件和其他不需要的应用程序。 实时减小网络受攻击面，控制对 Azure VM 上的管理端口的访问。 AAC 显著降低了遭受暴力攻击和其他网络攻击的风险。
    - **容器安全功能** - 获得在容器化环境中进行漏洞管理和实时威胁保护的好处。 启用适用于容器注册表的 Azure Defender 时，等待全部功能启用完毕可能需要最多 12 个小时。 根据推送到已连接注册表的唯一容器映像的数量收费。 一个映像经过一次扫描后，不会再对其收取相关费用，除非再次对其进行了修改和推送。
    - 适用于连接到 Azure 环境的资源的广度威胁防护 - Azure Defender 包括对所有资源通用的 Azure 服务的 Azure 本机广度威胁防护：Azure 资源管理器、Azure DNS、Azure 网络层和 Azure Key Vault。 Azure Defender 对 Azure 管理层和 Azure DNS 层具有独特的可见性，因此可以保护连接到这些层的云资源。


## <a name="faq---pricing-and-billing"></a>常见问题解答 - 定价和计费 

- [如何跟踪我的组织中谁已在安全中心启用 Azure Defender 更改？](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [安全中心提供了哪些计划？](#what-are-the-plans-offered-by-security-center)
- 如何为订阅启用 Azure Defender？
- 能否在我的订阅中为服务器子集上的服务器启用 Azure Defender？
- [如果我已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [我的订阅已为服务器启用了 Azure Defender，是否需要为未运行的服务器付费？](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [是否需要为未安装 Log Analytics 代理的计算机付费？](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [如果 Log Analytics 代理向多个工作区报告，是否需要重复付费？](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [如果 Log Analytics 代理向多个工作区报告，是否所有工作区上均提供 500-MB 的免费数据引入？](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [500 MB 免费数据引入量是针对整个工作区计算还是严格按每台计算机计算得出的？](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [每日 500 MB 数据限额中包含哪些数据类型？](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>如何跟踪我的组织中谁已在安全中心启用 Azure Defender 更改？
Azure 订阅可能具有多个管理员，这些管理员有权更改定价设置。 若要找到做出更改的用户，请使用 Azure 活动日志。

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="显示定价更改事件的 Azure 活动日志。":::

如果“事件发起者”列中未列出用户信息，请查看事件的 JSON 了解相关详细信息。

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure 活动日志 JSON 资源管理器。":::


### <a name="what-are-the-plans-offered-by-security-center"></a>安全中心提供了哪些计划？ 
安全中心有两个产品/服务： 

- Azure 安全中心免费版 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>如何为订阅启用 Azure Defender？ 
可以使用以下任一方法启用订阅的 Azure Defender： 

| 方法                                          | 说明                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure 门户的 Azure 安全中心页面 | [启用 Azure Defender](enable-azure-defender.md)                                                                                                  |
| REST API                                        | [定价 API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security 定价](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [捆绑定价](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>能否在我的订阅中为服务器子集上的服务器启用 Azure Defender？
不是。 在订阅上启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 时，Azure Defender 会保护订阅中的所有服务器。 

替代方法如下：在 Log Analytics 工作区级别为服务器启用 Azure Defender。 如果执行此操作，将仅保护向该工作区报告的服务器并对其计费。 但某些功能将不可用。 其中包括实时 VM 访问、网络检测、法规合规性、自适应网络强化、自适应应用程序控制等。 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>如果我已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？
如果你已获得 Microsoft Defender for Endpoint 的许可证，则无需为 Azure Defender 许可证的相应部分付费。

若要请求折扣，请与安全中心的支持团队联系，并提供相关的工作区 ID、区域以及为给定工作区中的计算机应用的 Microsoft Defender for Endpoint 许可证数。

该折扣将从批准之日起生效，不具追溯效力。

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>我的订阅已为服务器启用了 Azure Defender，是否需要为未运行的服务器付费？ 
不是。 如果在订阅中启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md)，则在计算机处于已解除分配电源状态时，你都无需为此状态的任何计算机付费。 计算机按照下表中所示的电源状态进行计费：

| 状态        | 说明                                                                                                                                      | 实例使用情况计费 |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| 正在启动     | VM 正在启动。                                                                                                                               | 不计费            |
| 正在运行      | VM 的正常工作状态                                                                                                                    | 计费                |
| 正在停止     | 这是一种过渡性状态。 完成后，会显示为“已停止”。                                                                           | 计费                |
| 已停止      | VM 已在来宾 OS 中关闭，或者已使用 PowerOff API 关闭。 硬件仍然分配给 VM 并保留在主机上。 | 计费                |
| 正在解除分配 | 过渡性状态。 完成后，VM 会显示为“已解除分配”。                                                                             | 不计费            |
| 已解除分配  | VM 已成功停止并从主机中删除。                                                                                  | 不计费            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="显示已解除分配的计算机的 Azure 虚拟机。":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>是否需要为未安装 Log Analytics 代理的计算机付费？
是的。 在订阅上启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 时，即使未安装 Log Analytics 代理，该订阅中的计算机也会受到一系列保护。 这适用于 Azure 虚拟机、Azure 虚拟机规模集实例和已启用 Azure Arc 的服务器。

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>如果 Log Analytics 代理向多个工作区报告，是否需要重复付费？ 
是的。 如果已将 Log Analytics 代理配置为将数据发送到两个或更多个不同的 Log Analytics 工作区（多宿主），则需为每个安装了“安全”或“反恶意软件”解决方案的工作区付费。 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>如果 Log Analytics 代理向多个工作区报告，是否所有工作区上均提供 500-MB 的免费数据引入？
是的。 如果已将 Log Analytics 代理配置为将数据发送到两个或多个不同的 Log Analytics 工作区（多宿主），则将获得 500-MB 的免费数据引入。 它是按每个节点、每个报告的工作区、每一天来计算的，适用于安装了“安全”或“反恶意软件”解决方案的所有工作区。 你将需要为超出 500 MB 限制的引入数据付费。

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>500 MB 免费数据引入量是针对整个工作区计算还是严格按每台计算机计算得出的？
对于连接到工作区的每台计算机，你每天都可免费引入 500 MB 的数据。 专用于直接由 Azure 安全中心收集的安全数据类型。

此数据在所有节点中按每日费率平摊。 因此，即使某些计算机发送 100 MB 的数据，另一些发送 800 MB 的数据，只要总量不超过免费限额（[计算机数量] x 500 MB），我们就不会对你额外收费。

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>每日 500 MB 数据限额中包含哪些数据类型？

安全中心的账单与 Log Analytics 账单密切相关。 安全中心根据以下[安全数据类型](/azure/azure-monitor/reference/tables/tables-category#security)子集提供 500 MB/节点/天分配额：
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- SysmonEvent
- ProtectionStatus
- 当工作区上未在运行更新管理解决方案或者启用了解决方案目标设定时，将更新 UpdateSummary 数据类型

如果工作区位于旧版按节点定价层中，则将合并安全中心和 Log Analytics 分配，并将其共同应用于所有可计费的引入数据。

## <a name="next-steps"></a>后续步骤
本文介绍了安全中心的定价选项。 如需查看相关材料，请参阅：

- [如何优化 Azure 工作负载成本](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [根据所选货币和所在区域的定价详细信息](https://azure.microsoft.com/pricing/details/security-center/)
- 你可能希望管理成本，并通过将解决方案的应用范围限制为特定的一组代理来限制为解决方案收集的数据量。 [解决方案目标](../azure-monitor/insights/solution-targeting.md)使用户可以向解决方案应用一个范围，并可将目标设定为工作区中的一个计算机子集。 如果使用解决方案目标功能，安全中心会将工作区列为没有解决方案。
