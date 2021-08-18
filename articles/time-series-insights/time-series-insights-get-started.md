---
title: 创建环境 - Azure 时序见解 | Microsoft Docs
description: 了解如何使用 Azure 门户创建新的 Azure 时序见解环境。
ms.service: time-series-insights
services: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 2e886c343a37b65d548f84f2faca76bdf92ff089
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136861"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>在 Azure 门户中创建新的 Azure 时序见解 Gen1 环境

> [!CAUTION]
> 这是一篇 Gen1 文章。

本文介绍如何使用 Azure 门户创建新的 Azure 时序见解环境。

使用 Azure 时序见解可在几分钟内开始可视化和查询流入 Azure IoT 中心和事件中心的数据，并在几秒内查询大量的时序数据。  时序见解面向物联网 (IoT) 规模的应用，可以处理 TB 量级的数据。

## <a name="steps-to-create-the-environment"></a>创建环境的步骤

遵循以下步骤创建环境：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“+ 创建资源”  按钮。

1. 选择“物联网”类别，再选择“时序见解”。  

   [![创建 Azure 时序见解环境](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. 在“时序见解”页上，选择“创建”。  

1. 填写必需的参数。 下表解释了每个参数：

   [![创建 Azure 时序见解资源组](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   设置|建议的值|说明
   ---|---|---
   环境名称 | 唯一的名称 | 此名称在[时序资源管理器](https://insights.timeseries.azure.com)中代表环境
   订阅 | 订阅 | 如果有多个订阅，最好是选择包含事件源的订阅。 Azure 时序见解可以自动检测 Azure IoT 中心和事件中心资源是否存在于同一订阅中。
   资源组 | 创建新资源组或使用现有的资源组 | 资源组是结合使用的 Azure 资源的集合。 可以选择现有的资源组，例如，包含事件中心或 IoT 中心的资源组。 或者，如果此资源与其他资源不相关，则可以创建新资源组。
   位置 | 最靠近事件源的位置 | 最好是选择包含事件源数据的同一个数据中心位置，以尽量避免增加跨界和跨区域的带宽费用，以及将数据移出区域时增大延迟。
   定价层 | S1 | 选择所需的吞吐量。 若要尽量降低费用并获得入门容量，请选择 S1。
   容量 | 1 | 容量是应用于入口速率、存储容量和所选 SKU 相关成本的乘数。  可以在创建环境后更改其容量。 若要尽量降低费用，请选择 1 个单位的容量。

1. 选择“创建”开始执行预配过程。  这可能需要几分钟时间才能完成。

1. 若要监视部署过程，请选择“通知”符号（钟形图标）。 

   [![监视通知](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. 在资源概述  中，验证部署配置设置。

   [![创建 Azure 时序见解（固定到仪表板）](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. （可选）选择右上角的“固定”图标，以方便将来访问 Azure 时序见解环境。

## <a name="next-steps"></a>后续步骤

* [定义数据访问策略](./concepts-access-policies.md)来保护环境。

* [将事件中心事件源添加](./how-to-ingest-data-event-hub.md)到 Azure 时序见解环境。

* [发送事件](time-series-insights-send-events.md)到事件源。

* 在 [Azure 时序见解资源管理器](https://insights.timeseries.azure.com)中查看环境。
