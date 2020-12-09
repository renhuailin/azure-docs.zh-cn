---
title: Azure 中的自动缩放入门
description: 了解如何在 Azure 中缩放资源：Web 应用、云服务、虚拟机或虚拟机规模集。
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 95f94bd1e80c05658d9033047950d4b49fca4643
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920661"
---
# <a name="get-started-with-autoscale-in-azure"></a>Azure 中的自动缩放入门
本文介绍如何在 Microsoft Azure 门户中为资源指定自动缩放设置。

Azure Monitor 自动缩放仅适用于[虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[云服务](https://azure.microsoft.com/services/cloud-services/)、[应用服务 - Web 应用](https://azure.microsoft.com/services/app-service/web/)和 [API 管理服务](../../api-management/api-management-key-concepts.md)。

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>了解订阅中的自动缩放设置

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

可在 Azure Monitor 中查找自动缩放功能适用的所有资源。 按下列步骤进行分步演练：

1. 打开 [Azure 门户][1]。
1. 单击左窗格中的“Azure Monitor”图标。
  ![打开 Azure Monitor][2]
1. 单击“自动缩放”以查看自动缩放适用的所有资源及其当前的自动缩放状态。
  ![了解 Azure Monitor 中的自动缩放功能][3]

可使用顶部的筛选器窗格缩小列表的范围，以选择特定资源组中的资源、特定的资源类型或特定资源。

对于每个资源，将会看到其当前实例计数和自动缩放状态。 自动缩放状态可以是：

- **未配置**：尚未对此资源启用自动缩放功能。
- **已启用**：已对此资源启用自动缩放功能。
- **Disabled**：已对此资源禁用自动缩放功能。

## <a name="create-your-first-autoscale-setting"></a>创建第一个自动缩放设置

现在，让我们完成一个简单的分步演练，以创建第一个自动缩放设置。

1. 在 Azure Monitor 中打开“自动缩放”边栏选项卡，然后选择要缩放的资源。 （以下步骤使用与某 Web 应用关联的应用服务计划。 [仅需 5 分钟，就可在 Azure 中创建首个 ASP.NET Web 应用。][4]）
1. 请注意当前实例计数为 1。 单击“启用自动缩放”。
  ![新 Web 应用的缩放设置][5]
1. 提供缩放设置的名称，然后单击“添加规则”。 请注意右侧以上下文窗格形式打开的缩放规则选项。 默认情况下，这将选项设置为当资源的 CPU 百分比超过 70% 时，将实例计数缩放 1 个单位。 请将此选项保留默认值，并单击“添加”。
  ![为 Web 应用创建缩放设置][6]
1. 现已创建第一个缩放规则。 请注意，UX 建议了最佳做法，并指出“建议至少在规则中包含一个缩放设置”。 为此，请执行以下操作：

    a. 单击“添加规则”。

    b. 将“运算符”设置为“小于”。 

    c. 将“阈值”设置为 20。 

    d. 将“操作”设置为“按以下值递减计数”。 

   现在应已创建一个可以根据 CPU 使用率进行扩展/缩减的缩放设置。
   ![基于 CPU 进行缩放][8]
1. 单击“保存” 。

祝贺！ 现已成功创建第一个缩放设置，用于根据 CPU 使用率自动缩放 Web 应用。

> [!NOTE]
> 若要开始使用虚拟机规模集或云服务角色，才可采用相同步骤操作。

## <a name="other-considerations"></a>其他注意事项
### <a name="scale-based-on-a-schedule"></a>基于计划的缩放
除了基于 CPU 进行缩放，还可设置为在特定的星期日期按其他方式缩放。

1. 单击“添加缩放条件”。
1. 缩放模式和规则的设置方式与默认条件的相同。
1. 为计划选择“重复特定的星期日期”。
1. 选择星期日期，以及需应用缩放条件的开始/结束时间。

![基于计划的缩放条件][9]
### <a name="scale-differently-on-specific-dates"></a>在特定的日期以不同的方式缩放
除了基于 CPU 进行缩放，还可设置为在特定日期按其他方式缩放。

1. 单击“添加缩放条件”。
1. 缩放模式和规则的设置方式与默认条件的相同。
1. 为计划选择“指定开始/结束日期”。
1. 选择开始/结束日期，以及需应用缩放条件的开始/结束时间。

![基于日期的缩放条件][10]

### <a name="view-the-scale-history-of-your-resource"></a>查看资源的缩放历史记录
每次增加或缩小资源后，都会在活动日志中记录一个事件。 切换到“运行历史记录”选项卡即可查看资源在过去 24 小时的缩放历史记录。

![运行历史记录][11]

若要查看完整的缩放历史记录（最长 90 天），请选择“单击此处查看更多详细信息”。 随后将启动活动日志，其中包含已预先选择“自动缩放”的资源和类别。

### <a name="view-the-scale-definition-of-your-resource"></a>查看资源的缩放定义
“自动缩放”是一种 Azure 资源管理器资源。 切换到“JSON”选项卡即可在 JSON 中查看缩放定义。

![缩放定义][12]

如果需要，可以直接在 JSON 中进行更改。 这些更改将在保存后生效。

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>禁用“自动缩放”并手动缩放实例
有时，可能需要禁用当前的缩放设置并手动缩放资源。

单击顶部的“禁用自动缩放”按钮。
![禁用自动缩放][13]

> [!NOTE]
> 此选项将禁用你的配置。 但是，再次启用“自动缩放”后，则可恢复此设置。

现在，可手动设置要缩放到的实例数。

![设置手动缩放][14]

始终可单击“启用自动缩放”，再单击“保存”来恢复自动缩放。 

## <a name="route-traffic-to-healthy-instances-app-service"></a>将流量路由到正常运行的实例（应用服务）

横向扩展到多个实例时，应用服务可以对实例执行运行状况检查，以仅将流量路由到正常运行的实例。 为此，请打开门户并转到应用服务，然后选择“监视”下的“运行状况检查” 。 选择“启用”，然后在应用程序上提供有效的 URL 路径，例如 `/health` 或 `/api/health`。 单击“保存”  。

若要在 ARM 模板中启用该功能，请将 `Microsoft.Web/sites` 资源的 `healthcheckpath` 属性设置为站点中的运行状况检查路径，例如：`"/api/health/"`。 若要禁用该功能，请将属性重新设置为空字符串 `""`。

### <a name="health-check-path"></a>运行状况检查路径

路径必须在一分钟内响应，且状态码介于 200 到 299（含）之间。 如果路径未在一分钟内响应，或返回范围之外的状态代码，则将该实例视为“运行不正常”。 应用服务未遵循关于运行状况检查路径的 302 重定向。 运行状况检查集成了应用服务的身份验证和授权功能，即使启用了这些安全功能，系统也会到达终结点。 如果使用自己的身份验证系统，则必须允许匿名访问运行状况检查路径。 如果站点启用了“仅限 HTTPS”，则将通过 HTTPS 发送运行状况检查请求 。

运行状况检查路径应检查应用程序的关键组件。 例如，如果应用程序依赖于数据库和消息传递系统，则运行状况检查终结点应连接到这些组件。 如果应用程序无法连接到关键组件，则路径应返回介于 500 级别的响应代码，以指示应用运行不正常。

#### <a name="security"></a>安全性 

大型企业的开发团队通常需要遵守已公开 API 的安全性要求。 若要保护运行状况检查终结点，应先使用 [IP 限制](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule)、[客户端证书](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule)或虚拟网络等功能来限制对应用程序的访问。 可以要求传入请求的 `User-Agent` 与 `ReadyForRequest/1.0` 匹配，以保护运行状况检查终结点。 由于先前的安全功能已对该请求进行了保护，因此无法冒名顶替用户代理。

### <a name="behavior"></a>行为

提供运行状况检查路径后，应用服务将在所有实例上 ping 通该路径。 如果进行 5 次 ping 后未收到表示成功的响应代码，则将该实例视为“运行不正常”。 如果扩展到2个或多个实例并使用 [基本层](../../app-service/overview-hosting-plans.md) 或更高级别，则不能从负载均衡器轮换中排除不正常的实例 () 。 可以通过应用设置配置所需的失败 ping 操作数 `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` 。 此应用设置可设置为2到10之间的任意整数。 例如，如果此设置为，则在出现 `2` 两个失败 ping 后，将从负载均衡器中删除实例。 此外，在纵向扩展或横向扩展时，应用服务将对运行状况检查路径进行 ping 操作，以确保在将新实例添加到负载均衡器之前已准备好请求。

> [!NOTE]
> 请记住，你的应用服务计划必须扩展到2个或更多个实例，并且必须是 **基本层或更高级别** ，才能排除负载均衡器。 如果只有1个实例，则它不会从负载均衡器中删除，即使它不正常。 

其余正常运行的实例的负载可能会增大。 为避免其余实例不堪重负，排除的实例不得过半。 例如，如果应用服务计划横向扩展到 4 个实例，且其中 3 个运行不正常，则负载均衡器轮换最多排除 2 个。 其他 2 个实例（1 个运行正常的实例和 1 个运行不正常的实例）将继续接收请求。 在所有实例均不正常的最坏情况下，不排除任何实例。如果要替代此行为，可以将 `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` 应用设置设置为介于 `0` 和 `100` 之间的值。 将此值设置为较高值意味着将删除更多运行不正常的实例（默认值为 50）。

如果实例在一小时内仍无法正常运行，则将其更换为新实例。 每小时最多更换一个实例，每个应用服务计划每天最多更换三个实例。

### <a name="monitoring"></a>监视

提供应用程序的运行状况检查路径后，可以使用 Azure Monitor 监视站点的运行状况。 在门户的“运行状况检查”边栏选项卡中，单击顶部工具栏中的“指标” 。 此操作将打开新的边栏选项卡，可以在其中查看站点的历史运行状况以及新建预警规则。 有关监视站点的更多信息，请[参阅 Azure Monitor 指南](../../app-service/web-sites-monitor.md)。

## <a name="moving-autoscale-to-a-different-region"></a>将自动缩放移动到不同的区域
本部分介绍如何在同一订阅和资源组下将 Azure 自动缩放移到另一个区域。 可以使用 REST API 来移动自动缩放设置。
### <a name="prerequisite"></a>先决条件
1. 确保订阅和资源组可用，并且源区域和目标区域中的详细信息是相同的。
1. 确保 Azure [区域](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all)中的 azure 自动缩放功能可用。

### <a name="move"></a>移动
使用 [REST API](/rest/api/monitor/autoscalesettings/createorupdate) 在新环境中创建自动缩放设置。 在目标区域中创建的自动缩放设置将是源区域中的自动缩放设置的副本。

无法移动在源区域中与自动缩放设置关联的中创建的[诊断设置](./diagnostic-settings.md)。 完成创建 autosale 设置后，你将需要在目标区域中重新创建诊断设置。 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>详细了解如何在 Azure 区域之间移动资源
若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅 [将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="next-steps"></a>后续步骤
- [创建活动日志警报以监视订阅上的所有自动缩放引擎操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
