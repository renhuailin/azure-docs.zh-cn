---
title: 使用 Azure Monitor 中的应用程序更改分析查找 web 应用问题 |Microsoft Docs
description: 使用 Azure Monitor 中的应用程序更改分析排查 Azure 应用服务中的实时站点应用程序问题。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: e59d4ecd238879eddb9d842245395d58aff28385
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519416"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>使用 Azure Monitor 中的应用程序更改分析（预览版）

发生实时站点问题或服务中断时，快速确定根本原因至关重要。 标准的监视解决方案可能会针对问题发出警报。 它们甚至可以指出哪个组件发生了故障。 但是，此警报不一定总能立即解释故障的原因。 你知道你的站点在五分钟前还一切正常，但现在它已中断。 那么，过去 5 分钟发生了什么？ Azure Monitor 中的应用程序更改分析就是为了解答此类问题而开发的。

更改分析构建在 [Azure Resource Graph](../../governance/resource-graph/overview.md) 的强大功能基础之上，可让你洞察 Azure 应用程序的更改，以提高可观察性并减少 MTTR（平均修复时间）。

> [!IMPORTANT]
> 更改分析目前以预览版提供。 此预览版不附带服务级别协议。 不建议对生产工作负荷使用此版本。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概述

更改分析可以检测从基础结构层到应用程序部署中的各种更改。 它是订阅级别的 Azure 资源提供程序，可检查订阅中的资源更改。 更改分析提供各种数据诊断工具，帮助用户了解哪些更改可能导致出现了问题。

下图演示了更改分析的体系结构：

![演示更改分析如何获取更改数据并将其提供给客户端工具的体系结构示意图](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>支持的资源类型

应用程序更改分析服务支持所有 Azure 资源类型中的资源属性级别更改，包括常见资源，如：
- 虚拟机
- 虚拟机规模集
- 应用服务
- Azure Kubernetes 服务
- Azure 函数
- 网络资源：网络安全组、虚拟网络、应用程序网关等。
- 数据服务：存储、SQL、Redis 缓存、Cosmos DB 等。

## <a name="data-sources"></a>数据源

适用于 Azure 的应用程序更改分析查询，适用于 Azure 资源管理器跟踪的属性、代理的配置和来宾内的 web 应用更改。 此外，该服务会跟踪资源依赖项更改，以对应用程序进行端到端的诊断和监视。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure 资源管理器跟踪的属性更改

更改分析使用 [Azure Resource Graph](../../governance/resource-graph/overview.md) 提供托管应用程序的 Azure 资源在不同时间的更改历史记录。 可以检测到管理的设置，例如托管标识、平台操作系统升级和主机名。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure 资源管理器代理设置更改

设置（例如 IP 配置规则、TLS 设置和扩展版本）在 Azure 资源图中尚不可用，因此更改分析查询并安全地计算这些更改，以提供应用程序中更改的内容的更多详细信息。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web 应用部署和配置的更改（来宾中的更改）

更改分析每隔 4 小时捕获一次应用程序的部署和配置状态。 例如，它可以检测应用程序环境变量的更改。 该工具会计算差异，并显示已发生更改的内容。 与资源管理器更改不同，代码部署更改信息可能不会立即在该工具中提供。 若要查看更改分析中的最新更改，请选择 " **刷新**"。

![“立即扫描更改”按钮的屏幕截图](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依赖项更改

对资源依赖项所做的更改也可能会导致资源问题。 例如，如果某个 Web 应用调用 Redis 缓存，Redis 缓存 SKU 可能会影响该 Web 应用的性能。 另一个示例是，如果端口22在虚拟机的网络安全组中关闭，则会导致连接错误。

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Web 应用诊断和解决问题导航器 (预览) 

若要检测依赖项的更改，更改分析将检查 Web 应用的 DNS 记录。 它通过这种方式识别所有应用组件中可能导致出现问题的更改。
目前， **Web 应用诊断和解决问题中支持以下依赖项 |导航器 (预览)**：

- Web 应用
- Azure 存储
- Azure SQL

#### <a name="related-resources"></a>相关资源

应用程序更改分析检测相关资源。 常见示例包括与虚拟机相关的网络安全组、虚拟网络、应用程序网关和负载均衡器。
通常，网络资源是在使用资源的资源组中自动预配的，因此按资源组筛选更改将显示虚拟机和相关网络资源的所有更改。

![网络更改屏幕截图](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>应用程序更改分析服务支持

应用程序更改分析服务计算并聚合上述数据源中的更改数据。 它提供一组分析，使用户能够轻松地浏览所有资源更改，并识别故障排除或监视上下文中的相关更改。
需将“Microsoft.ChangeAnalysis”资源提供程序注册到某个订阅，然后才可使用 Azure 资源管理器的跟踪属性和代理设置更改数据。 当你输入 Web 应用 "诊断和解决问题" 工具或显示 "更改分析独立" 选项卡时，将自动注册此资源提供程序。
对于 Web 应用的来宾中更改，需要单独的支持才能在 Web 应用中扫描代码文件。 有关详细信息，请参阅本文后面 [的诊断和解决问题工具](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) 一节中的更改分析。

## <a name="cost"></a>Cost

应用程序更改分析是一项免费服务，它不会对启用它的订阅产生任何计费费用。 对于扫描 Azure 资源属性更改，服务也不会产生任何性能影响。 当你为 web 应用中的 "来宾文件更改" 启用更改分析 (或启用) 的 "诊断和解决问题" 工具时，它将对 web 应用造成性能影响，无需支付费用。

## <a name="enable-change-analysis-at-scale"></a>大规模启用更改分析

如果订阅包含大量的 Web 应用，在 Web 应用级别启用该服务的做法就不够有效。 运行以下脚本以启用订阅中的所有 Web 应用。

先决条件：

- PowerShell Az 模块。 请按照[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)中的说明操作

运行以下脚本：

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="next-steps"></a>后续步骤

- 了解 [更改分析中的可视化效果](change-analysis-visualizations.md)
- 了解如何 [解决更改分析中的问题](change-analysis-troubleshoot.md)
- 为 [Azure 应用服务应用](azure-web-apps.md)启用 Application Insights。
- 为 [Azure VM 和 Azure 虚拟机规模集的 IIS 托管应用](azure-vm-vmss-apps.md)启用 Application Insights。
