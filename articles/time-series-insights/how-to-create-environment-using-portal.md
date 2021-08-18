---
title: 使用 Azure 门户设置第 2 代环境 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解如何使用 Azure 门户在 Azure 时序见解第 2 代中设置环境。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 9ac665304e874c711306b0fae16d2970de7be6a9
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135458"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>使用 Azure 门户创建 Azure 时序见解第 2 代环境

本文介绍了如何使用 [Azure 门户](https://portal.azure.com/)创建 Azure 时序见解第 2 代环境。

环境预配教程会指导你完成整个过程。 你将了解如何选择正确的时序 ID 并查看两个 JSON 有效负载中的示例。</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>概述

预配 Azure 时序见解第 2 代环境时，你将创建以下 Azure 资源：

* 遵循即用即付定价模型的 Azure 时序见解第 2 代环境
* Azure 存储帐户
* 一个可选的暖存储，用于更快速且无限制的查询

> [!TIP]
>
> * 了解[如何规划环境](./how-to-plan-your-environment.md)。
> * 了解如何[添加事件中心源](./how-to-ingest-data-event-hub.md)或如何[添加 IoT 中心源](./how-to-ingest-data-iot-hub.md)。

将了解如何执行以下操作：

1. 将每个 Azure 时序见解第 2 代环境与一个事件源关联。 你还将提供时间戳 ID 属性和唯一的使用者组，以确保环境有权访问相应的事件。

1. 预配完成后，可根据业务需求修改访问策略和其他环境属性。

   > [!NOTE]
   > 预配环境时，第一步是可选的。 如果跳过此步骤，那么你以后必须向环境附加事件源，以便数据可以开始流动到你的环境中并可供用户通过查询对其进行访问。

## <a name="create-the-environment"></a>创建环境

若要创建 Azure 时序见解第 2 代环境，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中的“物联网”下创建一个 Azure 时序见解资源。

1. 选择“第 2 代(L1)”作为“层级”。 提供一个环境名称，并选择要使用的订阅组和资源组。 然后，选择一个支持的位置，用于托管环境。

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="创建 Azure 时序见解实例。" lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. 输入时序 ID。

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="创建 Azure 时序见解环境配置（续）。" lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * 时序 ID *区分大小写* 且 *不可变*。 （一经设置，不可更改。）
   > * 时序 ID 最多可以是 *三个* 键。 可将其视为数据库中的主键，它唯一地表示会向你的环境发送数据的每个设备传感器。 它可以是一个属性，也可以是最多包含三个属性的组合。
   > * 有关详细信息，请参阅[如何选择时序 ID](./how-to-select-tsid.md)

1. 通过选择存储帐户名称、帐户种类并指定[复制](../storage/common/redundancy-migration.md?tabs=portal)选项来创建 Azure 存储帐户。 这样做会自动创建一个 Azure 存储帐户。 默认情况下将创建[常规用途 v2](../storage/common/storage-account-overview.md) 帐户。 该帐户在之前选择的 Azure 时序见解第 2 代环境所在的区域中创建。
另外，在创建新的 Azure 时序第 2 代环境时，还可以通过 [Azure 资源管理器模板](./time-series-insights-manage-resources-using-azure-resource-manager-template.md)自带存储 (BYOS)。

1. **（可选）** 如果需要在环境中对最新数据进行更快且不受限制的查询，请为环境启用暖存储。 也可在创建 Azure 时序第 2 代环境后，在左导航窗格中通过“存储配置”选项创建或删除暖存储。

1. **（可选）** 可以现在就添加事件源， 也可以等到预配完实例后再添加。

   * Azure 时序见解支持使用 [Azure IoT 中心](./how-to-ingest-data-iot-hub.md)和 [Azure 事件中心](./how-to-ingest-data-event-hub.md)作为事件源选项。 虽然在创建环境时只能添加单个事件源，但可以在以后添加其他事件源。

     在添加事件源时，可以选择现有的使用者组，也可以创建新的使用者组。 请确保事件源使用一个适合你的环境的唯一使用者组将数据读入其中。

   * 选择开始从事件源收集数据的时间。 默认是从创建事件源时开始收集。

     > [!TIP]
     > 如果选择“我的所有数据”选项来收集事件源中任何预先存在的数据，则可能会遇到较高的初始延迟，因为 Azure 时序见解第二代环境会处理所有数据。 这种高延迟最终应该会在索引数据时得到缓解。

   * 选择相应的时间戳属性。 默认情况下，Azure 时序见解对每个事件源使用消息排队时间。

     > [!TIP]
     > 在批处理事件方案或历史数据上传方案中，消息排队时间可能不是要使用的最佳配置设置。 在这种情况下，请确保验证你是决定使用还是不使用时间戳属性。

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="事件源配置选项卡" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. 选择“查看 + 创建”以确认你的环境已按照你所需要的方式进行了预配和配置。

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="“查看 + 创建”选项卡" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>后续步骤

* 阅读[规划你的环境](./how-to-plan-your-environment.md)，详细了解 Azure 时序见解正式版环境和第 2 代环境。
* 了解 Azure 时序见解第 2 代环境的[流式引入事件源](./concepts-streaming-ingestion-event-sources.md)。
* 详细了解[如何管理环境](./how-to-provision-manage.md)。
