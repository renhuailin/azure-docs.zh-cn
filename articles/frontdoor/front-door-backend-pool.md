---
title: Azure Front Door 中的后端和后端池 | Microsoft Docs
description: 本文介绍 Front Door 配置中包含的后端和后端池。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449286"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Azure Front Door 中的后端和后端池
本文介绍有关如何在 Azure Front Door 中映射 Web 应用程序部署的概念。 另外还解释了 Front Door 配置中使用的有关应用程序后端的各种术语。

## <a name="backends"></a>后端
后端是指区域中的 Web 应用程序部署。 Front Door 支持后端池中的 Azure 资源和非 Azure 资源。 应用程序可以位于本地数据中心，也可以位于其他云服务提供商。

Front Door 后端是指为客户端请求提供服务的应用程序的主机名或公共 IP。 请不要将后端与数据库层、存储层等概念相混淆。 应将后端视为应用程序后端的公共终结点。 将后端添加到 Front Door 后端池时，还必须添加以下信息：

- 后端主机类型。 要添加的资源的类型。 Front Door 支持从应用服务、云服务或存储自动发现应用后端。 如果需要 Azure 中的不同资源，或者甚至需要非 Azure 后端，请选择“自定义主机”。

    >[!IMPORTANT]
    >在配置期间，API 不会验证是否可从 Front Door 环境访问后端。 请确保 Front Door 可以访问你的后端。

- 订阅和后端主机名。 如果尚未选择“自定义主机”作为后端主机类型，请选择你的后端，方法是在 UI 中选择相应的订阅和相应的后端主机名。

- 后端主机头。 针对每个请求发送到后端的主机头值。 有关详细信息，请参阅[后端主机头](#hostheader)。

- **优先级**。 如果要对所有流量使用某个主服务后端，请为不同的后端分配优先级。 此外，如果主后端或备份后端不可用，请提供备份。 有关详细信息，请参阅[优先级](front-door-routing-methods.md#priority)。

- 权重。 为不同的后端分配权重，以便在一组后端之间均匀分配或根据权重系数分配流量。 有关详细信息，请参阅[权重](front-door-routing-methods.md#weighted)。

### <a name="backend-host-header"></a><a name = "hostheader"></a>后端主机标头

Front Door 转发到后端的请求包含由后端用来检索目标资源的主机头字段。 此字段的值通常来自包含主机头和端口的后端 URI。

例如，对 `www.contoso.com` 发出的请求将包含主机头 www.contoso.com。 如果使用 Azure 门户配置后端，则此字段的默认值为后端的主机名。 如果后端是 contoso-westus.azurewebsites.net，则在 Azure 门户中，为后端主机头自动填充的值将是 contoso-westus.azurewebsites.net。 但是，如果在没有显式设置此字段的情况下使用 Azure 资源管理器模板或其他方法，则 Front Door 会发送传入的主机名作为主机头的值。 如果对 www\.contoso.com 发出了请求，而后端是头字段为空的 contoso-westus.azurewebsites.net，则 Front Door 会将主机头设置为 www\.contoso.com。

大多数应用后端（例如 Azure Web 应用、Blob 存储和云服务）要求主机头与后端的域相匹配。 但是，路由到后端的前端主机将使用不同的主机名，例如 www.contoso.net。

如果后端要求主机头匹配后端主机名，请确保后端主机头包含后端的主机名。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>配置后端的后端主机标头

若要在后端池部分为后端配置后端主机头字段，请执行以下操作：

1. 打开你的 Front Door 资源，选择包含要配置的后端的后端池。

2. 添加后端（如果尚未这样做），或编辑现有后端。

3. 将后端主机头字段设置为自定义值，或将其留空。 将使用传入请求的主机名作为主机头值。

## <a name="backend-pools"></a>后端池
Front Door 中的后端池是指可为其应用接收类似流量的一组后端。 换而言之，它是你分布于全球的应用实例的逻辑分组，这些实例可以接收相同的流量，并以预期行为做出响应。 这些后端跨不同区域或在同一区域内部署。 后端可以全部处于主动/主动部署模式，或者采用定义为“主动/被动”的配置。

后端池定义了应如何通过运行状况探测评估不同的后端。 它还定义了如何在后端之间进行负载均衡。

### <a name="health-probes"></a>运行状况探测
Front Door 将周期性的 HTTP/HTTPS 探测请求发送到配置的每个后端。 探测请求可确定每个后端的邻近度和运行状况，以便对最终用户请求进行负载均衡。 后端池的运行状况探测设置会定义我们如何轮询应用后端的运行状况状态。 以下设置可用于负载均衡配置：

- 路径：针对后端池中所有后端的探测请求使用的 URL。 例如，如果你的某个后端是 contoso-westus.azurewebsites.net，并且路径设置为 /probe/test.aspx，则 Front Door 环境（假设协议设置为 HTTP）会将运行状况探测请求发送到 http\://contoso-westus.azurewebsites.net/probe/test.aspx。

- 协议：定义是否通过 HTTP 或 HTTPS 协议从 Front Door 将运行状况探测请求发送到后端。

- 方法：要用于发送运行状况探测的 HTTP 方法。 选项包括 GET 或 HEAD（默认）。
    > [!NOTE]
    > 为了降低后端的负载和成本，Front Door 建议将 HEAD 请求用于运行状况探测。

- 间隔(秒)：定义向后端发送运行状况探测请求的频率，或每个 Front Door 环境发送探测请求的时间间隔。

    >[!NOTE]
    >为了更快地进行故障转移，请将时间间隔设置为较小值。 但是值越低，你的后端接收到的运行状况探测请求量越多。 例如，如果将时间间隔设置为 30 秒（并且假设全球有 100 个 Front Door POP），则每个后端每分钟将收到约 200 个探测请求。

有关详细信息，请查看[运行状况探测](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>负载均衡设置
后端池的负载均衡设置定义我们评估运行状况探测的方式。 这些设置决定后端是否正常运行。 它们还会检查如何对后端池中不同后端之间的流量进行负载均衡。 以下设置可用于负载均衡配置：

- 样本大小。 标识我们需要考虑采用多少个运行状况探测样本来进行后端运行状况评估。

- 成功样本大小。 定义了在上述“样本大小”中，我们需要检查多少个成功样本，才能将后端判定为正常。 例如，假设 Front Door 运行状况探测时间间隔为 30 秒，样本大小为 5，成功的样本大小为 3。 每次评估后端的运行状况探测时，都将检查过去 150 秒 (5 x 30) 内的最后五个样本。 至少需要三个成功的探测，才能将后端声明为正常。

- 延迟敏感度(额外延迟)。 定义你是希望 Front Door 将请求发送到延迟度量敏感度范围内的后端，还是将请求转发到最近的后端。

有关详细信息，请参阅[基于最低延迟的路由方法](front-door-routing-methods.md#latency)。

## <a name="next-steps"></a>后续步骤

- [创建 Front Door 配置文件](quickstart-create-front-door.md)
- [Front Door 的工作原理](front-door-routing-architecture.md)
