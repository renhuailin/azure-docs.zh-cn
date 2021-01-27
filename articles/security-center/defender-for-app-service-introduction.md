---
title: 适用于应用服务的 Azure Defender - 优点和功能
description: 了解适用于应用服务的 Azure Defender 有哪些功能，以及如何在订阅上启用它
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 200e1fd7bfffef403fa459d3de13dc31145b8a33
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796628"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>用于应用服务的 Azure Defender 简介

Azure 应用服务是一种完全托管的平台，用于构建和托管 Web 应用及 API。 该平台是完全托管的，因此你无需担心基础结构。 它提供管理、监视和操作见解，以满足企业级的性能、安全性和符合性要求。 有关详细信息，请参阅 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)。

用于应用服务的 Azure Defender 使用云的规模来识别针对应用服务运行的应用程序受到的攻击。 攻击者会探查 Web 应用程序，以找出并恶意利用其中的弱点。 向 Azure 中运行的应用程序发出的请求在路由到特定的环境之前会流经多个网关，网关会对其进行检查并记录其状态。 然后，将使用这些数据来识别恶意利用行为和攻击者，并了解稍后要使用的新模式。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|[用于应用服务的 Azure Defender](azure-defender.md) 按[定价页中](security-center-pricing.md)的定价计费<br>定价和设置页面列出了“资源数量”中的实例数。 该数字是指当你打开“定价层”页面时，此订阅的所有应用服务计划中正在运行的计算实例总数。<br>若要验证该计数，请在 Azure 门户打开“应用服务计划”，然后检查每个计划使用的计算实例数。|
|支持的应用服务计划：|![是](./media/icons/yes-icon.png)“基本”、“标准”、“高级”、“独立”或“Linux”<br>![否](./media/icons/no-icon.png)“免费”、“共享”或“消耗”<br>[了解有关应用服务计划的详细信息](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>适用于应用服务的 Azure Defender 有哪些优点？

启用适用于应用服务的 Azure Defender 后，你立即就能从该 Azure Defender 计划提供的下列服务中受益：

- **安全** - 安全中心会对你的应用服务计划覆盖的资源进行评估，并根据发现结果生成安全建议。 可按照这些建议中的详细说明来强化你的应用服务资源。

- **检测** - Azure Defender 会监视以下内容，检测你的应用服务资源面临的多种威胁：
    - 正在运行 Azure 应用服务的 VM 实例及其管理接口
    - 发送至/发送自你的应用服务应用的请求和响应
    - 基础沙盒和 VM
    - 应用服务内部日志 - 得益于 Azure 作为云提供商而具备的可见性，有这些日志可供查看

Azure Defender 是一种云原生解决方案，可识别应用于多个目标的攻击方法。 例如，从单个主机上很难确定来自一小部分 IP 的分布式攻击，这些 IP 对多个主机上相似的终结点进行爬网。

而日志数据和基础结构结合起来，可呈现从四处传播的新攻击到客户计算机遭到入侵的整个情况。 因此，即使是在 Web 应用被恶意利用后再部署安全中心，它也能够检测不断发生的攻击。


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>适用于应用服务的 Azure Defender 可检测哪些威胁？

### <a name="threats-by-mitre-attck-tactics"></a>按 MITRE ATT&CK 技巧排列的威胁

Azure Defender 会监视指向应用服务资源的多种威胁。 警报涵盖了从预攻击到命令和控制措施等各种 MITRE ATT&CK 技巧的完整列表。 Azure Defender 可检测：

- **预攻击威胁** - Defender 可检测攻击者经常用来探测应用程序薄弱之处的多种漏洞扫描程序类型的执行。

- **初始访问威胁** - [Microsoft 威胁情报](https://go.microsoft.com/fwlink/?linkid=2128684)为这些警报提供支持，其中包括在已知恶意 IP 地址连接到 Azure 应用服务 FTP 接口时触发警报。

- **执行威胁** - Defender 可检测尝试运行高权限命令、Windows 应用服务上的 Linux 命令、无文件攻击行为、数字货币挖掘工具，以及其他许多可疑和恶意代码执行活动的操作。

### <a name="dangling-dns-detection"></a>检测无关联的 DNS

适用于应用服务的 Azure Defender 还可识别当应用服务网站被解除授权时 DNS 注册器中剩下了任何 DNS 条目 - 它们被称作无关联的 DNS 条目。 如果删除某个网站但不从 DNS 注册机构删除其自定义域，则该 DNS 条目将指向一个不存在的资源，并且子域容易遭到接管。 Azure Defender 不会在 DNS 注册器中扫描当前是否存在无关联的 DNS 条目；当应用服务网站解除授权，但其自定义域（DNS 条目）没被删除时，它会发出警报。

子域接管是组织常见的严重威胁。 当威胁执行者检测到无关联的 DNS 条目时，会在目标地址创建自己的网站。 然后，本打算发送给组织的域的流量会被定向到威胁执行者的网站中，他们会利用该流量实施各种恶意活动。

无论你的域是由 Azure DNS 托管还是由外部域注册器托管，可都使用无关联 DNS 防护；该服务既适用于 Windows 上的应用服务，也适用于 Linux 上的应用服务。

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="关于已发现的无关联 DNS 条目的 Azure Defender 警报示例。启用适用于应用服务的 Azure Defender 来接收此警报以及针对你的环境的其他警报。" lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

若要详细了解无关联的 DNS 和子域接管威胁，请查看[防止无关联的 DNS 条目并避免子域接管](../security/fundamentals/subdomain-takeover.md)。

有关 Azure 应用服务警报的完整列表，请查看[警报参考表](alerts-reference.md#alerts-azureappserv)。

> [!NOTE]
> 如果你的自定义域不直接指向应用服务资源，或者由于已启用无关联 DNS 防护，因此 Defender 未监视指向你的网站的流量（原因是没有日志来帮助识别自定义域），那么 Defender 可能不会触发无关联 DNS 警报。

## <a name="how-to-protect-your-azure-app-service-web-apps-and-apis"></a>如何保护 Azure 应用服务 Web 应用和 API

若要通过适用于应用服务的 Azure Defender 保护 Azure 应用服务计划：

1. 确保具有与专用计算机关联的受支持的应用服务计划。 上文中的[可用性](#availability)中列出了支持的计划。

2. 按照 [Azure 安全中心定价](security-center-pricing.md)中所述，在订阅上启用 Azure Defender。

    可选择启用 Azure Defender 中的单个计划（例如适用于应用服务的 Azure Defender）。

    安全中心原生与应用服务集成，因此不需要部署和载入 - 集成是透明的。


## <a name="next-steps"></a>后续步骤

本文介绍了适用于应用服务的 Azure Defender。 

如需相关材料，请参阅以下文章： 

- 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报流式传输到 SIEM、SOAR，或 IT 服务管理解决方案](export-to-siem.md)中的说明操作。
- 有关适用于应用服务的 Azure Defender 警报的列表，请查看[警报参考表](alerts-reference.md#alerts-azureappserv)。
- 有关应用服务计划的详细信息，请参阅[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。
> [!div class="nextstepaction"]
> [启用 Azure Defender](security-center-pricing.md#enable-azure-defender)