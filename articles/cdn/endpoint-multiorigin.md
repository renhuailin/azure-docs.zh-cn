---
title: Azure CDN 终结点多原点（预览版）
description: Azure CDN 终结点多原点入门。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 08/18/2021
ms.author: allensu
ms.openlocfilehash: 06d0e35534db97f1de48c55f9cc6f46f979514fa
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444838"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN 终结点多原点

多原点支持可消除停机时间，并建立全局冗余。 

通过选择 Azure CDN 终结点内的多个原点，多原点提供的冗余会探测每个原点的运行状况并在必要时进行故障转移，从而分散风险。

设置一个或多个原点组，并选择默认原点组。 每个原点组都是一个或多个原点的集合，可以承担类似的工作负荷。

> [!NOTE]
> 目前，只有 Microsoft 的 Azure CDN 提供此功能。 

## <a name="create-the-origin-group"></a>创建原点组

1. 登录到 [Azure 门户](https://portal.azure.com)

2. 选择 Azure CDN 配置文件，然后选择要为多原点配置的终结点。

3. 在终结点配置的“设置”下，选择“原点”：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN 终结点" border="true":::

4. 若要启用多原点，需要至少一个原点组。 选择“创建原点组”：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="原点设置" border="true":::

5. 在“添加原点组”配置中，输入或选择以下信息：

   | 设置           | 值                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 原点组名称 | 输入原点组名称。                                   |
   | 探测状态      | 选择“启用”。  </br> Azure CDN 将从全球不同点运行运行状况探测，以确定原点运行状况。 如果当前原点组未处于活动状态，请勿启用此组，以免产生额外的费用。
   | 探测路径        | 用于确定运行状况的原点中的路径。 |
   | 探测间隔    | 选择探测间隔为 1 分钟、2 分钟或 4 分钟。                        |
   | 探测协议    | 选择“HTTP”或“HTTPS”。                                         |
   | 探测方法      | 选择“Head”或“Get”。                                           |
   | 默认原点组 | 勾选复选框，以设置为默认原点组。
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="添加原点组" border="true":::

6. 选择“添加”。

## <a name="add-multiple-origins"></a>添加多个原点

1. 在终结点的原点设置中，选择“+ 创建原点”：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="创建原点" border="true":::

2. 在“添加原点”配置中，输入或选择以下信息：

   | 设置           | 值                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 名称        | 输入原点名称。        |
   | 原点类型 | 选择“存储”、“云服务”、“Web 应用”或“自定义原点”。                                   |
   | 源服务器主机名        | 选择或输入源服务器主机名。  下拉列表列出其类型已在前面的设置中指定的所有可用的源。 如果选择“自定义原点”作为原点类型，请输入自定义源服务器的域。 |
   | 源主机标头    | 输入需要让 Azure CDN 随每个请求发送的主机标头，或保留默认值。                        |
   | HTTP 端口   | 输入 HTTP 端口。                                         |
   | HTTPS 端口     | 输入 HTTPS 端口。                                           |
   | 优先级    | 请输入介于 1 和 5 之间的数字。       |
   | 重量      | 请输入介于 1 和 1000 之间的数字。   |

    > [!NOTE]
    > 在原点组中创建原点时，必须赋予其优先级和权重。 如果原点组只有一个原点，则默认优先级和权重设置为 1。 如果原点正常，则流量将路由到最高优先级的原点。 如果原点被确定为不正常，则连接将按优先级顺序转移到另一个原点。 如果两个原点具有相同的优先级，则按为原点指定的权重分配流量 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="添加其他原点" border="true":::

3. 选择“添加”。

4. 选择“配置原点”，为所有原点设置原点路径：

    源路径将用于重写 Microsoft CDN 在构造转发到源的请求时要使用的 URL。 其还会携带传入请求的所有剩余部分。 默认情况下，不提供此路径。 因此，Microsoft CDN 将在对源路径的请求中使用传入 URL 路径。

    源路径：`/fwd/`

    传入 URL 路径：`/foo/a/b/image1.jpg` </br> 从 Microsoft CDN 到源路径的 URL：`fwd/foo/a/b/image1.jpg.`

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="配置原点路径" border="true":::

5. 选择“确定”。

## <a name="configure-origins-and-origin-group-settings"></a>配置原点和原点组设置

拥有多个原点和原点组后，可以将该原点添加到不同的组或从中删除。 同一组中的原点应提供类似的工作负荷。 流量将根据其运行状况、优先级和权重值分发到这些原点。 

1. 在 Azure CDN 终结点的原点设置中，选择要配置的原点组的名称：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="配置原点和原点组设置" border="true":::

2. 在“更新原点组”中，选择“+ 选择原点” ：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="更新原点组" border="true":::

4. 在下拉框中选择要添加到组的原点，并选择“确定”。

5. 验证原点是否已添加到组，然后选择“保存”：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="验证添加到组的其他原点" border="true":::

## <a name="remove-origin-from-origin-group"></a>从原点组中删除原点

1. 在 Azure CDN 终结点的原点设置中，选择原点组的名称：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="从组中删除原点" border="true":::

2. 若要从原点组中删除原点，请选择原点旁边的垃圾桶图标，然后选择“保存”：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="更新原点组，删除原点" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>使用规则引擎替代原点组

使用标准规则引擎自定义如何将流量分散至不同的原点组。

根据请求 URL 将流量分配到不同的组。

1. 在 CDN 终结点中选择“设置”下的“规则引擎” ：

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="规则引擎" border="true":::

2. 选择“+ 添加规则”。

3. 在“名称”中输入规则名称。

4. 选择“+ 条件”，然后选择“URL 路径” 。

5. 在“运算符”下拉列表中选择“包含” 。

6. 在“值”中输入“/images” 。

7. 选择“+ 添加操作”，然后选择“原点组替代” 。

8. 在“原点组”中选择下拉框中的原点组。

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="规则引擎条件" border="true":::

对于所有传入请求，如果 URL 路径包含“/images”，则会在操作部分将请求分配给原点组“myorigingroup” 。 

## <a name="next-steps"></a>后续步骤
在本文中，已启用 Azure CDN 终结点多原点。

有关 Azure CDN 和本文中所述的其他 Azure 服务的详细信息，请参阅：

* [Azure CDN](./cdn-overview.md)
* [比较 Azure CDN 产品功能](./cdn-features.md)
