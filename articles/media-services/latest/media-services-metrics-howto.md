---
title: 使用 Azure Monitor 查看指标
description: 本文介绍如何使用 Azure 门户图表和 Azure CLI 监视指标。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 91e74668224d1cdbbc462bf6c2c0a22c4f0a9837
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916569"
---
# <a name="monitor-media-services-metrics"></a>监视媒体服务指标

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

可以通过 [Azure Monitor](../../azure-monitor/overview.md) 监视指标和诊断日志，以便了解应用程序的执行情况。 有关此功能的详细说明并了解应如何使用 Azure 媒体服务指标和诊断日志的详细说明，请参阅 [监视媒体服务指标和诊断](media-services-metrics-diagnostic-logs.md)日志。

Azure Monitor 提供多种方式来与指标交互，包括在门户中制作指标图表、通过 REST API 访问指标，或者使用 Azure CLI 查询指标。 本文介绍如何使用 Azure 门户图表和 Azure CLI 监视指标。

## <a name="prerequisites"></a>必备条件

- [创建媒体服务帐户](./create-account-howto.md)
- 参阅[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>在 Azure 门户中查看指标

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 导航到你的 Azure 媒体服务帐户，选择“指标”。 
1. 单击 " **作用域** " 框，然后选择要监视的资源。

    " **选择作用域** " 窗口将显示在右侧，并显示可用的资源列表。 在本例中，你会看到：

    * &lt;媒体服务帐户名称&gt;
    * &lt;媒体服务帐户名称&gt;/&lt;流式处理终结点名称&gt;
    * &lt;存储帐户名称&gt;

    筛选器并选择资源，然后按 " **应用**"。 有关支持的资源和指标的详细信息，请参阅[监视媒体服务指标](media-services-metrics-diagnostic-logs.md)。

    > [!NOTE]
    > 若要在要监视的资源之间进行切换，请再次单击 " **源** " 框，然后重复此步骤。

1. 可选：通过在顶部) ，为图表指定名称 (编辑名称。
1. 添加要查看的度量值。
1. 可将图表固定到仪表板。

## <a name="view-metrics-with-azure-cli"></a>使用 Azure CLI 查看指标

若要使用 Azure CLI 获取“出口”指标，请运行以下 `az monitor metrics` 命令：

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

若要获取其他指标，请将“传出”替换为所需的指标名称。

## <a name="see-also"></a>另请参阅

- [Azure Monitor 指标](../../azure-monitor/platform/data-platform.md)
- [使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>后续步骤

[诊断日志](media-services-diagnostic-logs-howto.md)
