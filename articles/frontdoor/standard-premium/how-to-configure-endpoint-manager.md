---
title: 通过终结点管理器配置 Azure 前门标准/高级终结点
description: 本文介绍如何使用终结点管理器配置终结点。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098491"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>使用终结点管理器配置 Azure 前门标准/高级 (预览版) 终结点

> [!NOTE]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 **[Azure 前门文档](../front-door-overview.md)**。

本文介绍如何使用终结点管理器为现有的 Azure 前门标准/高级配置文件创建终结点。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

必须先创建至少一个 Azure 前门配置文件，才能使用终结点管理器创建 Azure 前门标准/高级终结点。 配置文件必须至少具有一个或多个 Azure 前门标准/高级终结点。 若要按 internet 域、web 应用程序或其他条件组织 Azure 前门标准/高级终结点，可以使用多个配置文件。 

若要创建 Azure 前门配置文件，请参阅 [创建新的 Azure 前门标准/高级配置文件](create-front-door-portal.md)。

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>创建新的 Azure 前门标准/高级终结点

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 前门标准/高级配置文件。

1. 选择 **终结点管理器**。 然后选择 " **添加终结点** " 以创建新的终结点。
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="通过终结点管理器添加终结点的屏幕截图。":::

1. 在 " **添加终结点** " 页上，输入，然后选择以下设置。
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="&quot;添加终结点&quot; 页的屏幕截图。":::

    | 设置 | 值 |
    | -------- | ----- |
    | 名称 | 为新的 Azure 前门标准/高级终结点输入唯一的名称。 此名称用于访问在域中缓存的资源 `<endpointname>.az01.azurefd.net` |
    | 源响应超时 (秒)  | 输入一个超时值（以秒为单位），在考虑使用源的连接超时之前，Azure 前门将等待。 |
    | 状态 | 选中复选框以启用此终结点。 |

## <a name="add-domains-origin-group-routes-and-security"></a>添加域、源组、路由和安全

1. 选择终结点上的 " **编辑终结点** " 来配置路由。

1. 在 " **编辑终结点** " 页上，选择 "域" 下的 " **+ 添加** "。
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="在 &quot;编辑终结点&quot; 页上选择域的屏幕截图。":::

### <a name="add-domain"></a>添加域

1. 在 " **添加域** " 页上，选择将域 *与 Azure 前门配置文件* 关联，或 *添加新域*。 有关如何创建全新域的信息，请参阅 [创建新的 Azure 前门标准/高级自](how-to-add-custom-domain.md)定义域。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="添加域页的屏幕截图。":::

1. 选择 " **添加** " 将域添加到当前终结点。 所选域应显示在 "域" 面板中。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="域视图中的域的屏幕截图。":::

### <a name="add-origin-group"></a>添加源组

1. 选择 "源组" 视图中的 " **添加** "。 此时将显示 " **添加源组** " 页 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="添加源组页的屏幕截图":::

1. 对于 " **名称**"，请输入新的源组的唯一名称

1. 选择 " **添加原点** " 可将新的原点添加到当前组。
 
#### <a name="health-probes"></a>运行状况探测
前门向每个源发送定期 HTTP/HTTPS 探测请求。 探测请求会确定每个源的邻近性和运行状况，以对最终用户请求进行负载平衡。 源组的运行状况探测设置定义我们如何轮询应用源的运行状况状态。 以下设置可用于负载平衡配置：

> [!WARNING]
> 由于前门在全局上具有许多边缘环境，因此，源的运行状况探测量可以从25个请求每分钟到最高每分钟1200请求，具体取决于配置的运行状况探测频率。 如果默认探测频率为30秒，则源上的探测量应为每分钟约200请求。

* **状态**：指定是否打开运行状况探测。 如果源组中有单个源，则可以选择禁用运行状况探测，从而减少应用程序后端上的负载。 即使组中有多个源，但其中只有一个存在于 "已启用" 状态，你可以禁用运行状况探测。
   
* **路径**：用于对此源组中所有源的探测请求的 URL。 例如，如果你的一个来源为 contoso-westus.azurewebsites.net，路径设置为/probe/test.aspx，则如果将协议设置为 HTTP，则前门环境会将运行状况探测请求发送到 `http://contoso-westus.azurewebsites.net/probe/test.aspx` 。 
   
* **协议**：定义是否通过 HTTP 或 HTTPS 协议将运行状况探测请求从前门发送到源。
   
* **探测方法**：用于发送运行状况探测的 HTTP 方法。 选项包括 GET 或 HEAD (默认) 。

    > [!NOTE]
    > 对于低于源的负载和开销，前门建议使用 HEAD 请求进行运行状况探测。

* **间隔 (秒)**：定义运行状况探测的频率，或每个前门环境发送探测的间隔。
   
    >[!NOTE]
    >为了更快地进行故障转移，请将间隔设置为较小的值。 值越低，源接收的运行状况探测量就越高。 例如，如果将时间间隔设置为30秒（例如，100前门），则每个后端将每分钟收到约200探测请求。

#### <a name="load-balancing"></a>负载均衡
源组的负载平衡设置定义我们评估运行状况探测的方式。 这些设置确定后端是否正常运行。 它们还会检查如何在源组中的不同来源之间对流量进行负载均衡。 以下设置可用于负载平衡配置：

- **样本大小**。 确定对于源运行状况评估需要考虑的运行状况探测样本的数量。

- **样本大小成功**。 定义前面提到的样本大小，调用原情况所需的成功样本的数目。 例如，假定前门运行状况探测间隔为30秒，样本大小为5，成功的样本大小为3。 每次评估源的运行状况探测时，将在150秒内查看最后五个样本 (5 x 30) 。 需要至少三个成功的探测，才能将后端声明为正常。

- **延迟敏感度 () 额外的延迟**。 定义是否希望前门将请求发送到延迟测量敏感度范围内，或将请求转发到最近的后端。

选择 " **添加** " 将源组添加到 "当前终结点"。 源组应出现在 "源组" 面板中

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="源组中的源的屏幕截图。":::

### <a name="add-route"></a>添加路由

在 "路由" 视图中选择 " **添加** "，此时将显示 " **添加路由** " 页。 有关如何关联域和源组的信息，请参阅 [创建新的 Azure 前门路由](how-to-configure-route.md)

### <a name="add-security"></a>添加安全性

1. 选择 "安全" 视图中的 **"添加"** ，随即出现 " **添加 WAF 策略** " 页
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="添加 WAF 策略页的屏幕截图。":::

1. **WAF 策略**：选择要应用于此终结点中所选域的 WAF 策略。 
  
   选择 " **新建** " 以创建全新的 WAF 策略。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="创建新的 WAF 策略的屏幕截图。":::
   
    **名称**：输入新 WAF 策略的唯一名称。 可以在 "Web 应用程序防火墙" 页中编辑此策略的更多配置。

    **域**：选择要应用 WAF 策略的域。

1. 选择 " **添加** 按钮"。 WAF 策略应显示在安全面板中

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="安全视图中 WAF 策略的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

若要在不再需要某个终结点时将其删除，请选择终结点行末尾的 " **删除终结点** " 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="如何删除终结点的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

若要了解自定义域，请继续 [添加自](how-to-add-custom-domain.md)定义域。
