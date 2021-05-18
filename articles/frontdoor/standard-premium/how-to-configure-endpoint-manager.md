---
title: 使用 Endpoint Manager 配置 Azure Front Door 标准版/高级版终结点
description: 本文演示如何使用 Endpoint Manager 配置终结点。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098491"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>使用 Endpoint Manager 配置 Azure Front Door 标准版/高级版（预览版）终结点

> [!NOTE]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看 [Azure Front Door 文档](../front-door-overview.md)。

本文演示如何使用 Endpoint Manager 为现有 Azure Front Door 标准版/高级版配置文件创建终结点。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

必须先创建至少一个 Azure Front Door 配置文件，才能使用 Endpoint Manager 创建 Azure Front Door 标准版/高级版终结点。 配置文件必须至少具有一个或多个 Azure Front Door 标准版/高级版终结点。 若要通过 Internet 域、Web 应用程序或其他条件来组织 Azure Front Door 标准版/高级版终结点，可以使用多个配置文件。 

若要创建 Azure Front Door 配置文件，请参阅[创建新的 Azure Front Door 标准版/高级版配置文件](create-front-door-portal.md)。

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>创建新的 Azure Front Door 标准版/高级版终结点

1. 登录 [Azure 门户](https://portal.azure.com)，并导航到你的 Azure Front Door 标准版/高级版配置文件。

1. 选择“Endpoint Manager”。 然后选择“添加终结点”以创建新的终结点。
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="通过 Endpoint Manager 添加终结点的屏幕截图。":::

1. 在“添加终结点”页上，输入并选择以下设置。
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="“添加终结点”页的屏幕截图。":::

    | 设置 | 值 |
    | -------- | ----- |
    | 名称 | 为新的 Azure Front Door 标准版/高级版终结点输入唯一名称。 此名称用于访问在域 `<endpointname>.az01.azurefd.net` 中缓存的资源 |
    | 源响应超时(以秒为单位) | 输入在考虑与源的连接超时之前，Azure Front Door 会等待的超时值（以秒为单位）。 |
    | 状态 | 选中复选框以启用此终结点。 |

## <a name="add-domains-origin-group-routes-and-security"></a>添加域、源组、路由和安全性

1. 对终结点选择“编辑终结点”以配置路由。

1. 在“编辑终结点”页上，选择“域”下的“+ 添加” 。
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="在“编辑终结点”页上选择域的屏幕截图。":::

### <a name="add-domain"></a>添加域

1. 在“添加域”页上，选择关联来自 Azure Front Door 配置文件的域，或添加新域 。 有关如何创建全新域的信息，请参阅[创建新的 Azure Front Door 标准版/高级版自定义域](how-to-add-custom-domain.md)。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="“添加域”页的屏幕截图。":::

1. 选择“添加”以将域添加到当前终结点。 所选域应出现在“域”面板中。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="域视图中的域的屏幕截图。":::

### <a name="add-origin-group"></a>添加源组

1. 在“源组”视图中选择“添加”。 此时将显示“添加源组”页 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="“添加源组”页的屏幕截图":::

1. 对于“名称”，请为新源组输入唯一名称

1. 选择“添加源”以将新源添加到当前组。
 
#### <a name="health-probes"></a>运行状况探测
Front Door 将周期性的 HTTP/HTTPS 探测请求发送到每个源。 探测请求可确定每个源的接近度和运行状况，以便对最终用户请求进行负载均衡。 源组的运行状况探测设置会定义我们如何轮询应用源的运行状况状态。 以下设置可用于负载均衡配置：

> [!WARNING]
> 由于 Front Door 在全局范围内存在许多边缘环境，因此针对源的运行状况探测量可能非常大 - 从每分钟 25 个请求到最高每分钟 1200 个请求不等，具体取决于配置的运行状况探测频率。 如果默认探测频率为 30 秒，则源的探测量应在每分钟 200 个请求左右。

* 状态：指定是否要打开运行状况探测。 如果源组中只有一个源，则可以选择禁用运行状况探测，从而减少应用程序后端上的负载。 即使源组中有多个源，但只有一个处于启用状态，则也可以禁用运行状况探测。
   
* 路径：用于源组中的所有源的探测请求的 URL。 例如，如果你的某个源是 contoso-westus.azurewebsites.net，并且路径设置为 /probe/test.aspx，则 Front Door 环境中（假设协议设置为 HTTP）会将运行状况探测请求发送到 `http://contoso-westus.azurewebsites.net/probe/test.aspx`。 
   
* 协议：定义是否通过 HTTP 或 HTTPS 协议从 Front Door 将运行状况探测请求发送到源。
   
* 探测方法：要用于发送运行状况探测的 HTTP 方法。 选项包括 GET 或 HEAD（默认）。

    > [!NOTE]
    > 为了降低源的负载和成本，Front Door 建议将 HEAD 请求用于运行状况探测。

* 间隔(秒)：定义向源发送运行状况探测的频率，或每个 Front Door 环境发送探测的时间间隔。
   
    >[!NOTE]
    >为了更快地进行故障转移，请将时间间隔设置为较小值。 值越低，你的源便会接收到越多的运行状况探测量。 例如，如果将时间间隔设置为 30 秒（并且假设全球有 100 个 Front Door POP），则每个后端每分钟将收到约 200 个探测请求。

#### <a name="load-balancing"></a>负载均衡
源组的负载均衡设置定义我们评估运行状况探测的方式。 这些设置决定后端是否正常运行。 它们还会检查如何在源组中的不同源之间对流量进行负载均衡。 以下设置可用于负载均衡配置：

- 样本大小。 标识我们需要考虑采用多少个运行状况探测样本来进行源运行状况评估。

- 成功样本大小。 定义在上述“样本大小”中，我们需要检查多少个成功样本，才能将源判定为正常。 例如，假设 Front Door 运行状况探测时间间隔为 30 秒，样本大小为 5，成功的样本大小为 3。 每次评估源的运行状况探测时，都将检查过去 150 秒内的最后五个样本 (5 x 30)。 至少需要三个成功的探测，才能将源声明为正常。

- 延迟敏感度(额外延迟)。 定义你是希望 Front Door 将请求发送到延迟度量敏感度范围内的源，还是将请求转发到最近的源。

选择“添加”以将源组添加到当前终结点。 源组应出现在“源组”面板中

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="源组中的源的屏幕截图。":::

### <a name="add-route"></a>添加路由

在“路由”视图中选择“添加”，此时将显示“添加路由”页 。 有关如何关联域和源组的信息，请参阅[创建新的 Azure Front Door 路由](how-to-configure-route.md)

### <a name="add-security"></a>添加安全性

1. 在“安全性”视图中选择“添加”，此时将显示“添加 WAF 策略”页 
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="添加 WAF 策略页的屏幕截图。":::

1. WAF 策略：选择要在此终结点中应用于所选域的 WAF 策略。 
  
   选择“新建”以创建全新 WAF 策略。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="创建新 WAF 策略的屏幕截图。":::
   
    名称：为新 WAF 策略输入唯一名称。 可以在“Web 应用程序防火墙”页中使用更多配置编辑此策略。

    域：选择要应用 WAF 策略的域。

1. 选择“添加”按钮。 WAF 策略应显示在“安全性”面板中

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="安全性视图中的 WAF 策略的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

若要删除不再需要的终结点，请选择终结点行末尾的“删除终结点” 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="如何删除终结点的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

若要了解自定义域，请继续[添加自定义域](how-to-add-custom-domain.md)。
