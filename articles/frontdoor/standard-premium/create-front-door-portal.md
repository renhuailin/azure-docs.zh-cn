---
title: 快速入门：创建 Azure Front Door 标准版/高级版配置文件 - Azure 门户
description: 本快速入门介绍如何通过 Azure 门户对具有高可用性和高性能的全局 Web 应用程序使用 Azure Front Door 标准版/高级版服务。
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 8c6fe355621b4f096814268dea8fd2fc8e1166a7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552863"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>快速入门：创建 Azure Front Door 标准版/高级版配置文件 - Azure 门户

> [!Note]
> 本文档适用于 Azure Front Door 标准版/高级版（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看 [Azure Front Door 文档](../front-door-overview.md)。

本快速入门介绍如何使用 Azure 门户创建 Azure Front Door 标准版/高级版配置文件。 可以通过“快速创建”使用基本的配置，或者通过“自定义创建”使用更高级的配置，来创建 Azure Front Door 标准版/高级版配置文件。  通过“自定义创建”部署两个 Web 应用。 接下来，使用两个 Web 应用作为源，创建 Azure Front Door 标准版/高级版配置文件。 然后，使用 Azure Front Door 标准版/高级版前端主机名来验证与 Web 应用的连接。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-front-door-profile---quick-create"></a>创建 Front Door 配置文件 - 快速创建

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在主页或 Azure 菜单中，选择“+ 创建资源” **** 。 搜索“Front Door 标准版/高级版(预览版)”。 ** 然后选择“创建”。

1. 在“比较产品/服务”页上选择“快速创建”。  然后选择“继续创建 Front Door”。

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="比较产品/服务的屏幕截图。":::

1. 在“创建 Front Door 配置文件”页上，输入或选择以下设置。

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Front Door“快速创建”页的屏幕截图。":::    

    | 设置 | 值 |
    | --- | --- |
    | **订阅**  | 选择订阅。 |
    | **资源组**  | 选择“新建”并在文本框中输入 *contoso-appservice*。|
    | **Name** | 为配置文件命名。 此示例使用 **contoso-afd-quickcreate**。 |
    | **层** | 选择“标准”或“高级”SKU。 标准 SKU 在内容传送方面经过优化。 高级 SKU 构建于标准 SKU 的基础之上，更注重安全性。 请参阅[层级比较](tier-comparison.md)。  |
    | **端点名称** | 为终结点输入全局唯一的名称。 |
    | **源服务器类型** | 选择源的资源类型。 本示例选择了一个已启用专用链接的应用服务作为源。 |
    | **源主机名** | 输入源的主机名。 |
    | **启用专用链接** | 如果你希望在 Azure Front Door 与源之间建立专用连接。 有关更多详细信息，请参阅[专用链接指南](concept-private-link.md)和[启用专用链接](./how-to-enable-private-link-web-app.md)。
    | **缓存** | 若要使用 Azure Front Door 的边缘 POP 和 Microsoft 网络将内容全局缓存在更靠近用户的位置，请选中此复选框。 |
    | **WAF 策略** | 若要启用此功能，请选择“新建”或者从下拉列表中选择现有的 WAF 策略。 |

    > [!NOTE]
    > 创建 Azure Front Door 标准版/高级版配置文件时，必须从创建了 Front Door 的同一订阅中选择一个源。

1. 选择“查看 + 创建”以启动并运行 Front Door 配置文件。

   > [!NOTE]
   > 可能需要几分钟时间才能将配置传播到所有边缘 POP。

1. 然后单击“创建”以部署并运行 Front Door 配置文件。

1. 如果启用了“专用链接”，请转到源（在本示例中为应用服务）。 选择“网络” > “配置专用链接”。  然后选择 Azure Front Door 中待处理的请求并单击“批准”。 几秒钟后，即可安全地通过 Azure Front Door 访问你的应用程序。

## <a name="create-front-door-profile---custom-create"></a>创建 Front Door 配置文件 - 自定义创建

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>创建一个 Web 应用，其中包含两个实例作为源

如果已配置了源或源组，请跳转到“为应用程序创建 Front Door 标准版/高级版（预览版）配置文件”。

本示例将创建一个 Web 应用程序，其中包含两个在不同 Azure 区域中运行的实例。 这两个 Web 应用程序实例在“主动/主动”模式下运行，因此其中的任何一个实例都可以接收流量。  此配置不同于“主动/备用”配置，在后一种配置中，只有一个实例充当故障转移节点。 

如果你还没有 Web 应用，请使用以下步骤设置一个示例 Web 应用。

1. 通过 https://portal.azure.com 登录到 Azure 门户。

1. 在屏幕左上方，选择“创建资源” > “WebApp”。

1. 在“创建 Web 应用”页的“基本信息”选项卡中，输入或选择以下信息 。

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **订阅**               | 选择订阅。 |
    | **资源组**       | 选择“新建”并在文本框中输入“FrontDoorQS_rg1”。|
    | **名称**                   | 输入 Web 应用的唯一名称。 本示例使用 *WebAppContoso-001*。 |
    | **发布** | 选择“代码”。 |
    | **运行时堆栈**         | 选择“.NET Core 2.1 (LTS)”。 |
    | **操作系统**          | 选择“Windows”。 |
    | **区域**           | 选择“美国中部”。 |
    | **Windows 计划** | 选择“新建”，然后在文本框中输入“myAppServicePlanCentralUS”。 |
    | **SKU 和大小** | 选择“标准 S1: 总共 100 个 ACU，1.75 GB 内存”。 |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="在 Azure 门户中快速创建 Front Door 高级 SKU":::

1. 选择“查看 + 创建”，查看摘要，然后选择“创建”。  部署可能需要几分钟时间

部署完成后，创建另一个 Web 应用。 使用上述相同设置，但以下设置除外：

| 设置          | 值     |
| ---              | ---  |
| **资源组**   | 选择“新建”并输入 *FrontDoorQS_rg2*。 |
| **Name**             | 输入 Web 应用的唯一名称，在本示例中为 *WebAppContoso-002*。  |
| **区域**           | 一个不同的区域，本示例使用“美国中南部” |
| “应用服务计划” > “Windows 计划”         | 选择“新建”并输入 *myAppServicePlanSouthCentralUS*，然后选择“确定”。  |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>为应用程序创建 Front Door 标准版/高级版（预览版）配置文件

配置 Azure Front Door 标准版/高级版（预览版），以根据两个 Web 应用服务器之间的最小延迟来定向用户流量。 另外，请使用 Web 应用程序防火墙保护 Front Door。 

1. 登录 [Azure 门户](https://portal.azure.com)。
 
1. 在主页或 Azure 菜单中，选择“+ 创建资源” **** 。 搜索“Front Door 标准版/高级版(预览版)”。 ** 然后选择“创建”。

1. 在“比较产品/服务”页上选择“自定义创建”。  然后选择“继续创建 Front Door”。

1. 在“基本信息”选项卡上输入或选择以下信息，然后选择“下一步: 机密”。 ****   **** 

    | 设置 | 值 |
    | --- | --- |
    | **订阅** | 选择订阅。 |
    | **资源组** | 选择“新建”并在文本框中输入“FrontDoorQS_rg0”。 |
    | **资源组位置** | 选择“美国东部” |
    | **配置文件名** | 在此订阅中输入唯一名称 **Webapp-Contoso-AFD** |
    | **层** | 选择“高级”。 |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="创建 Front Door 配置文件":::

1. *可选*：**机密**。 如果你打算使用托管证书，则此步骤是可选的。 如果在 Azure 中有一个现有的密钥保管库可供你用来对自定义域实现“自带证书”，请选择“添加证书”。 也可以在创建后在管理体验中添加证书。

    > [!NOTE]
    > 需要拥有适当的权限才能以用户身份从 Azure 密钥保管库添加证书。 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="在“自定义创建”中添加机密的屏幕截图。":::

1. 在“终结点”选项卡中，选择“添加终结点”并为终结点指定全局唯一的名称。  完成创建体验后，可以在 Azure Front Door 标准版/高级版配置文件中创建多个终结点。 本示例使用 contoso-frontend。 将“源响应超时(秒)”和“状态”保留默认值。 选择“添加”以添加终结点。
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="“添加终结点”的屏幕截图。":::

1. 接下来，添加包含两个 Web 应用的源组。 选择“+ 添加”打开“添加源组”页。 ****   **** 在“名称”中输入“myOrignGroup” ** ，然后选择“+ 添加源” **** 。
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="“添加源组”的屏幕截图。":::

1. 在“添加源”页中输入或选择以下信息。 ****   然后选择“添加”  。

    | 设置 | 值 |
    | --- | --- |
    | **名称** | 输入 **webapp1** |
    | **源服务器类型** | 选择“应用服务” |
    | **主机名** | 选择 `WebAppContoso-001.azurewebsites.net` |
    | **源服务器主机标头** | 选择 `WebAppContoso-001.azurewebsites.net` |
    | **其他字段** | 将所有其他字段保留默认值。 |

    > [!NOTE]
    > 创建 Azure Front Door 标准版/高级版配置文件时，必须从创建了 Azure Front Door 标准版/高级版的同一订阅中选择一个源。
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="“添加更多源”的屏幕截图。":::

1. 重复步骤 8 以添加第二个源 webapp002。 选择 `webappcontoso-002.azurewebsite.net` 作为“源主机名”和“源主机头”。 

1. 在“添加源组”页上，会看到添加了两个源；请将所有其他字段保留默认值。
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="“添加源组”页的屏幕截图。":::

1. 接下来，添加一个路由以将前端终结点映射到源组。 此路由会将来自终结点的请求转发到 myOriginGroup。 在“路由”中选择“+ 添加”以配置路由。  

1. 在“添加路由”页上输入或选择以下信息。 然后选择“添加”  。
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="添加不使用缓存的路由":::

    | 设置 | 值 |
    | --- | --- |
    | **名称** | 输入 **MyRoute** |    
    | **域** | 选择 `contoso-frontend.z01.azurefd.net` | 
    | **主机名** | 选择 `WebAppContoso-001.azurewebsites.net` |
    | **匹配模式** | 保留为默认值。 |
    | **接受的协议** | 保留为默认值。 | 
    | **重定向** | 保留其默认值，即“重定向所有流量以使用 HTTPS”。 | 
    | **源组** | 选择“MyOriginGroup”。 | 
    | **源服务器路径** | 保留为默认值。 | 
    | **转发协议** | 选择“匹配传入的请求”。 | 
    | **缓存** | 在本快速入门中请保持未选中状态。 若要将内容缓存在边缘，请选中“启用缓存”对应的复选框。 |
    | **规则** | 保留为默认值。 创建 Front Door 配置文件后，可以创建自定义规则并将其应用到路由。 |  
       
    >[!WARNING]
    > **确保** 每个终结点都有一个路由。 缺少路由可能导致终结点故障。

1. 接下来，在“安全性”中选择“+ 添加”以添加 WAF 策略。 选择“新增”并为策略指定唯一的名称。 选中“添加机器人保护”对应的复选框。 在“域”中选择终结点，然后选择“添加”。 

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="添加 WAF 策略":::

1. 依次选择“查看 + 创建”、“创建”。 ****  **** 需要几分钟时间才能将配置传播到所有边缘 POP。 现已创建第一个 Front Door 配置文件和终结点。

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="查看 + 自定义创建":::

## <a name="verify-azure-front-door"></a>验证 Azure Front Door

创建 Azure Front Door 标准版/高级版配置文件时，需要花费几分钟时间才能全局部署完配置。 完成后，即可访问你创建的前端主机。 在浏览器中转到 `contoso-frontend.z01.azurefd.net`。 请求将从源组中指定的服务器自动路由到最靠近的服务器。

如果在本快速入门中创建了这些应用，将会看到信息页。

我们将使用以下步骤来测试即时全局故障转移：

1. 如上文所述打开浏览器并转到前端地址：`contoso-frontend.azurefd.net`。

1. 在 Azure 门户中，搜索并选择“应用服务”。 向下滚动以找到你的 Web 应用之一（在本示例中为 **WebAppContoso-001**）。

1. 选择该 Web 应用，选择“停止”，然后选择“是”以确认操作 。

1. 刷新浏览器。 应会看到相同的信息页。

   >[!TIP]
   >这些操作会略有延迟。 可能需要再次刷新。

1. 找到另一个 Web 应用，同样将其停止。

1. 刷新浏览器。 此时，应会看到一条错误消息。

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Web 应用的两个实例都已停止":::

## <a name="clean-up-resources"></a>清理资源

完成后，可以删除你创建的所有项。 删除资源组也会删除其内容。 如果你不打算使用此 Front Door，则应删除资源，以免产生不必要的费用。

1. 在 Azure 门户中，搜索并选择“资源组”，或者从 Azure 门户菜单中选择“资源组” 。

1. 通过筛选或者向下滚动来找到某个资源组，例如 FrontDoorQS_rg0。

1. 选择该资源组，然后选择“删除资源组”。

   >[!WARNING]
   >此操作不可逆。

1. 键入资源组名称以确认，然后选择“删除”。

对其他两个组重复此过程。

## <a name="next-steps"></a>后续步骤

继续学习下一篇文章，了解如何将自定义域添加到 Front Door。
> [!div class="nextstepaction"]
> [添加自定义域](how-to-add-custom-domain.md)