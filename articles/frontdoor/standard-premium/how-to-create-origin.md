---
title: 设置 Azure 前门标准/高级 (预览版) 源
description: 本文介绍如何使用终结点管理器配置源。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741882"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>设置 Azure 前门标准/高级 (预览版) 源

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

本文介绍如何在现有源组中创建 Azure 前门标准/高级源。 

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

你必须至少创建一个源组，然后才能创建 Azure 前门标准/高级版。

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>创建新的 Azure 前门标准/高级源

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 前门标准/高级配置文件。

1. 选择 " **源组**"。 然后选择 " **+ 添加** " 以创建新的源组。
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="源组登录页的屏幕截图。":::

1. 在 " **添加源组** " 页上，输入新的源组的唯一 **名称** 。

1. 然后选择 " **+ 添加原点** " 向此源组添加新的原点。 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="&quot;添加源&quot; 页的屏幕截图。":::
  
    | 设置 | Value |
    | --- | --- |
    | 名称 | 为新的 Azure 前门源输入唯一的名称。 |   
    | 原点类型 | 要添加的资源的类型。 Azure 前门标准/高级版支持从应用服务、云服务或存储中自动发现应用源。 如果要在 Azure 或非 Azure 后端中使用其他资源，请选择 " **自定义主机**"。 |
    | 主机名  | 如果未选择 " **自定义主机** 源主机类型"，请选择下拉列表中的 "源主机名" 以选择后端。 |
    | 源主机标头 | 输入要发送到每个请求的后端的主机标头值。 有关详细信息，请参阅 [源主机标头](concept-origin.md#hostheader)。 |
    | HTTP 端口 | 输入源支持的 HTTP 协议的端口值。 |
    | HTTPS 端口 | 输入源支持 HTTPS 协议的端口值。 |
    | 优先级 | 如果要为所有流量使用主服务源，请将优先级分配给不同的源。 此外，如果主副本或备份源不可用，请提供备份。 有关详细信息，请参阅 [Priority](concept-origin.md#priority)。 |
    | 重量 | 将权重分配给不同的源，以便在一组来源之间分配流量，不管是平均分配还是根据权重系数分配流量。 有关详细信息，请参阅 [权重](concept-origin.md#weighted)。 |
    | 状态 | 选择此选项以启用原点。 |
    | 规则 | 选择将应用于此路由的规则集。 有关如何配置规则的详细信息，请参阅 [为 Azure 前门配置规则集](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > 在配置期间，如果无法从前门环境访问源，则 Api 不会进行验证。 请确保前门可以到达原点。

1. 选择 " **添加** " 以创建新的源。 创建的源应与组显示在源列表中。
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="列表视图中的源屏幕截图。":::

1. 选择 " **添加** " 将源组添加到 "当前终结点"。 源组应出现在 "源组" 面板中。

## <a name="clean-up-resources"></a>清理资源
若要删除不再需要的源组，请单击 " **...** "，然后从下拉项中选择 " **删除** "。

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="如何删除源组的屏幕截图。":::

若要删除不再需要的源，请单击 " **...** "，然后从下拉项中选择 " **删除** "。 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="如何删除源的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

若要了解自定义域，请参阅 [将自定义域添加](how-to-add-custom-domain.md) 到 Azure 前门标准/高级终结点。
