---
title: Azure IoT Central 中的应用程序模板是什么 | Microsoft Docs
description: 可以通过 Azure IoT Central 应用程序模板进行 IoT 解决方案开发。
author: ankitscribbles
ms.author: ankitgup
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: f11a033fc1cd97786e540773f5b8e6ca0a08106b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182846"
---
# <a name="what-are-application-templates"></a>什么是应用程序模板？

Azure IoT Central 中的应用程序模板是一种工具，可帮助你快速开始 IoT 解决方案开发。 可以将应用程序模板用于一切事项，包括感受一下可能的情况，以及完全自定义应用程序以将其重新销售给客户。

应用程序模板包含：

- 示例仪表板
- 示例设备模板
- 生成实时数据的模拟设备
- 预配置的规则和作业
- 包含教程和操作方法在内的富文档

在创建应用程序时，可以选择应用程序模板。 在应用程序创建后，就不能更改模板了。

## <a name="custom-templates"></a>自定义模板

若要从头开始创建应用程序，请选择“自定义应用程序”模板。 自定义应用程序模板 ID 是 `iotc-pnp-preview`。

## <a name="industry-focused-templates"></a>行业关注的模板

Azure IoT Central 是与行业无关的应用程序平台。 应用程序模板是目前行业关注的适用于这些行业的示例：

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="connected-logistics"></a>互联物流

在 2020 年，全球物流支出预计将达到 10.6 万亿美元。 货物运输占这项开支的大部分，货运提供商面临激烈的竞争压力和严峻的限制。

你可以使用物联网传感器来收集和监测环境状况，例如温度、湿度、倾斜、冲击、光照和装运位置。 你可以将从物联网传感器和设备收集的遥测数据与其他数据源（例如基于云的商业智能系统中的天气和交通信息）结合使用。

联接的物流解决方案有以下优势：

* 具有实时跟踪功能的发运监视。 
* 通过实时环境状况监视保持货运完整性。
* 安全性，避免货物被盗、丢失或损坏。
* 地理围栏、路线优化、船队管理以及车辆分析。
* 预测功能，可预测货物的出发和到达情况。

以下屏幕截图显示了应用程序模板中的现成仪表板。 该仪表板可完全自定义，以满足你的特定解决方案要求：

:::image type="content" source="media/concepts-app-templates/connected-logistics-dashboard-1.png" alt-text="显示互联物流操作仪表板的上半部分的屏幕截图。":::

:::image type="content" source="media/concepts-app-templates/connected-logistics-dashboard-2.png" alt-text="显示互联物流操作仪表板的下半部分的屏幕截图。":::

若要了解详细信息，请参阅[部署并演练互联物流应用程序模板](../retail/tutorial-iot-central-connected-logistics.md)教程。

## <a name="digital-distribution-center"></a>数字分发中心

随着越来越多的制造商和零售商进入世界各地，它们的供应链也不断扩展且越来越复杂。 消费者现在希望有大量的产品可供选择，并且希望在购买后的 1-2 天内即可到货。 分发中心必须适应这些趋势，并克服当前导致效率低下的问题。

如今，依赖人工意味着分拣和打包占据分发中心成本的 55%-65%。 人工分拣和打包通常也比自动化系统慢，快速波动的人员需求使得运输量需求更加难以满足。 这种季节性的波动导致员工流动率高，并且出错概率会大增且代价高昂。

通过启用数字反馈循环，基于 IoT 支持的摄像头的解决方案可以提供转换优势。 分发中心的数据有助于生成可操作见解，而后者反过来会促进生成更优质的数据。

数字分发中心的优势包括：

* 当货物抵达并通过输送系统转移时，摄像头进行监视。
* 自动识别有问题的货物。
* 高效的订单跟踪。
* 降低了成本，提高了工作效率并优化了使用。

以下屏幕截图显示了应用程序模板中的现成仪表板。 该仪表板可完全自定义，以满足你的特定解决方案要求： 

:::image type="content" source="media/concepts-app-templates/digital-distribution-center-dashboard.png" alt-text="数字分发中心仪表板":::

若要了解详细信息，请参阅[部署并演练数字分发中心应用程序模板](../retail/tutorial-iot-central-digital-distribution-center.md)教程。

## <a name="in-store-analytics---condition-monitoring"></a>店内分析 - 环境监视

对许多零售商来说，其店内的环境状况是他们与竞争对手的一个关键区别。 为客户着想，零售商希望在他们的店内保持舒适的环境。  

可以使用 IoT Central 的店内分析 - 环境监视应用程序模板来构建端到端解决方案。 借助此应用程序模板，你可以使用各种传感器设备进行数字连接，并监视零售店环境。 这些传感器设备生成遥测数据，你可以将其转换为业务见解，帮助零售商降低运营成本，为其客户创造良好的体验。

使用此应用程序模板，可以：

* 将不同种类的 IoT 传感器连接到 IoT Central 应用程序实例。
* 监视并管理传感器网络以及环境中所有网关设备的运行状况。
* 创建关于店内环境状况的自定义规则，以针对商店管理人员触发警报。
* 将店内的环境状况转换为见解，供零售店团队用来改进客户体验。
* 将聚合的见解导出到现有的或新的业务应用程序，为零售员工提供有用及时的信息。

应用程序模板附带了一组设备模板，并使用一组模拟设备来填充该仪表板。 

以下屏幕截图显示了应用程序模板中的现成仪表板。 该仪表板可完全自定义，以满足你的特定解决方案要求： 

:::image type="content" source="media/concepts-app-templates/in-store-analytics-condition-dashboard.png" alt-text="店内分析环境监视":::

若要了解详细信息，请参阅[在 Azure IoT Central 中创建店内分析应用程序](../retail/tutorial-in-store-analytics-create-app.md)教程。

## <a name="in-store-analytics---checkout"></a>店内分析 - 结帐

对许多零售商来说，其店内的结帐体验是他们与竞争对手的一个关键区别。 零售商希望在其店内提供顺畅的结帐体验，以促进客户回头购买。  

可以使用 IoT Central 的店内分析 - 结帐应用程序模板来构建解决方案，用以将关于商店结帐区域的见解提供给零售员工。 例如，传感器可以针对每个结帐通道提供有关队列长度和平均等待时间的信息。

使用此应用程序模板，可以：

* 将不同种类的 IoT 传感器连接到 IoT Central 应用程序实例。
* 监视并管理传感器网络以及环境中所有网关设备的运行状况。
* 创建关于店内结帐状况的自定义规则，以针对零售员工触发警报。
* 将店内的结帐状况转换为见解，供零售店团队用来改进客户体验。
* 将聚合的见解导出到现有的或新的业务应用程序，为零售员工提供有用及时的信息。

应用程序模板附带了一组设备模板，并使用一组模拟设备将通道占用数据填充到该仪表板。 

以下屏幕截图显示了应用程序模板中的现成仪表板。 该仪表板可完全自定义，以满足你的特定解决方案要求： 

:::image type="content" source="media/concepts-app-templates/In-Store-Analytics-Checkout-Dashboard.png" alt-text="店内分析结帐":::

若要了解详细信息，请参阅[在 Azure IoT Central 中创建店内分析应用程序](../retail/tutorial-in-store-analytics-create-app.md)教程。

## <a name="smart-inventory-management"></a>智能库存管理

库存是零售商持有的存货。 库存管理对于确保适当产品在适当时间处于适当位置至关重要。 零售商必须在储存过多库存产生的成本与没有足够库存来满足需求产生的成本之间进行权衡。

由射频识别 (RFID) 标签、信标和摄像头生成的物联网数据提供了改进库存管理流程的机会。 你可以将从物联网传感器和设备收集的遥测数据与其他数据源（例如基于云的商业智能系统中的天气和交通信息）结合使用。

智能库存管理的好处有：

* 降低了商品缺货的风险，并确保达到预期的客户服务水平。 
* 近乎实时地提供对库存准确性的深入分析和见解。
* 提供了工具来帮助确定满足客户订单需求所需保持的合适库存量。

此应用程序模板侧重于设备连接，以及 RFID 和蓝牙低功耗 (BLE) 读取器设备的配置和管理。

以下屏幕截图显示了应用程序模板中的现成仪表板。 该仪表板可完全自定义，以满足你的特定解决方案要求：

:::image type="content" source="media/concepts-app-templates/smart-inventory-management-dashboard.png" alt-text="智能库存管理仪表板":::

若要了解详细信息，请参阅[部署和演练智能库存管理应用程序模板](../retail/tutorial-iot-central-smart-inventory-management.md)教程。

## <a name="micro-fulfillment-center"></a>微履行中心

在竞争日益激烈的零售业格局中，零售商不断面临压力来缩小需求与履行之间的差距。 为了满足日益增长的消费需求，出现了一种新的趋势，即在最终客户及其访问的商店附近建立库存。

利用 IoT Central 微履行中心应用程序模板，你可以监视和管理完全自动化履行中心的所有方面。 该模板包含一组模拟状况监视传感器和机器人载体，用以加快解决方案的开发过程。 这些传感器设备捕获有意义的信号，这些信号可以转换为业务见解，使零售商可以降低运营成本，并营造客户喜欢的体验。

使用此应用程序模板，可以： 

- 将各种 IoT 传感器（例如机器人和状况监视传感器）无缝连接到 IoT Central 应用程序实例。
- 监视并管理传感器网络以及环境中所有网关设备的运行状况。
- 创建关于履行中心内环境状况的自定义规则，以触发相应的警报。
- 将履行中心内的环境状况转换为见解，供零售仓库团队使用。
- 将聚合的见解导出到现有的或新的业务应用程序，为零售员工成员提供支持。

以下屏幕截图显示了应用程序模板中的现成仪表板。 该仪表板可完全自定义，以满足你的特定解决方案要求：

:::image type="content" source="media/concepts-app-templates/MFC-Dashboard.png" alt-text="微履行中心":::

若要了解详细信息，请参阅[部署并演练微履行中心应用程序模板](../retail/tutorial-micro-fulfillment-center.md)教程。

## <a name="video-analytics---object-and-motion-detection"></a>视频分析 - 对象和运动检测

利用 IoT Central 视频分析 - 对象和运动检测应用程序模板，可快速体验如何部署、管理和监视使用智能边缘相机检测对象和移动的解决方案。

视频分析应用程序使用 IoT Edge 中运行的 [实时视频分析 (LVA)](#live-video-analytics) 模块。 LVA 模块提供了一个平台，可用于构建跨越边缘和云的智能视频应用程序。 你可使用该平台通过对象和运动检测来增强 IoT 解决方案，如视频分析应用程序。

应用程序模板包含四个应用程序仪表板：

* “入门”提供可帮助你开始使用应用程序模板的资源链接。

- “演示仪表板”显示可在已连接的相机中显示的信息类型。
- “(示例)真实相机管理”使用模拟相机展示如何在应用程序中管理相机。
- “(示例)真实相机监视器”使用模拟相机展示如何在应用程序中监视相机。

:::image type="content" source="media/concepts-app-templates/live-video-analytics.png" alt-text="视频分析 - 对象和运动检测":::

若要了解如何部署解决方案，请参阅[在 Azure IoT Central 中创建视频分析应用程序](../retail/tutorial-video-analytics-deploy.md)教程。

### <a name="live-video-analytics"></a>实时视频分析

[实时视频分析](https://github.com/Azure/live-video-analytics)提供了一个平台，可用于构建跨越边缘和云的智能视频应用程序。 此平台提供了捕获、记录和分析实时视频的功能，并且可以将结果（视频和/或视频分析）发布到 Azure 服务。 Azure 服务可以在云或边缘中运行。 可以使用该平台通过视频分析增强 IoT 解决方案。

## <a name="smart-meter-monitoring"></a>智能计量监视

 智能仪表不仅支持自动计费，而且还支持高级计量用例（例如实时读数和双向通信）。 借助智能仪表应用模板，公用事业部门和合作伙伴能够监视智能仪表的状态和数据、定义警报和通知。 它提供断开仪表和更新软件等示例命令。 可以将仪表数据设置为传出到其他商业应用程序和开发自定义解决方案。 

应用的主要功能：

- 仪表示例设备型号
- 仪表信息和实时状态
- 能量、功率和电压等仪表读数
- 仪表命令示例 
- 内置可视化效果和仪表板
- 自定义解决方案开发的可扩展性

可以免费试用[智能仪表监视应用](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)，无需 Azure 订阅和任何承诺。

部署应用后，你将在仪表板上看到模拟的仪表数据，如下图所示。 此模板是一个示例应用，可针对特定用例进行轻松扩展和自定义。

:::image type="content" source="media/concepts-app-templates/smart-meter-app-dashboard.png" alt-text="智能仪表应用仪表板":::

## <a name="solar-panel-monitoring"></a>太阳能电池板监视

借助太阳能电池板监视应用，公用事业单位和合作伙伴能够近实时地监视太阳能电池板（例如能源生成和连接状态）。 它可以根据定义的阈值标准发送通知。 它提供了更新固件和其他属性等示例命令。 可以将太阳能电池板数据设置为传出到其他商业应用程序和开发自定义解决方案。 

应用的主要功能： 

- 太阳能电池板示例设备型号 
- 太阳能电池板信息和实时状态
- 太阳能能源生成和其他读数
- 命令和控件示例
- 内置可视化效果和仪表板
- 自定义解决方案开发的可扩展性

可以免费试用[太阳能电池板监视应用](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)，无需 Azure 订阅和任何承诺。

部署应用后，你将在 1 到 2 分钟内看到模拟的太阳能电池板数据，如以下仪表板种所示。 此模板是一个示例应用，可针对特定用例进行轻松扩展和自定义。 

:::image type="content" source="media/concepts-app-templates/solar-panel-app-dashboard.png" alt-text="太阳能电池板应用仪表板":::

## <a name="water-quality-monitoring"></a>水质监测

传统的水质监测依赖于人工采样方法和现场实验室分析，这种方法非常耗时且成本高昂。 通过实时远程监测水质，可以在居民受到影响之前对水质问题进行管理。 此外，借助高级分析，自来水公司和环保机构可以提前针对潜在的水质问题发出警告，并提前规划净水处理措施。  

水质监测应用是一个 IoT Central 应用模板，可帮助你立即开始进行 IoT 解决方案开发，使自来水公司能够对智能城市中的水质进行数字化监测。

:::image type="content" source="media/concepts-app-templates/water-quality-monitoring-dashboard-full.png" alt-text="水质监测应用模板":::

该应用模板包括：

- 示例仪表板
- 示例水质监测设备模板
- 模拟的水质监测设备
- 预配置的规则和作业
- 贴牌式品牌设计 

在[水质监测应用程序教程](../government/tutorial-water-quality-monitoring.md)中获取入门信息。

## <a name="water-consumption-monitoring"></a>水消耗量监测

传统的用水量跟踪依赖于水务操作员在计量现场人工读取用水量仪表。 越来越多的城市正在将传统的计量表更换为先进的智能计量表，这样可以远程监测用水量，并可以远程控制阀门来控制水流量。 对用水量进行监测并将数字反馈消息发送给居民可以提高他们的节水意识并降低水的消耗量。 

用水量监测应用是一个 IoT Central 应用模板，可帮助你立即开始进行 IoT 解决方案开发，使自来水公司和市政部门能够远程监控水流量，以降低消耗。 

:::image type="content" source="media/concepts-app-templates/water-consumption-monitoring-dashboard-full.png" alt-text="用水量监测应用模板":::

用水量监测应用模板包括预配置的：

- 示例仪表板
- 示例水质监测设备模板
- 模拟的水质监测设备
- 预配置的规则和作业
- 贴牌式品牌设计

 在[用水量监测应用程序教程](../government/tutorial-water-consumption-monitoring.md)中获取入门信息。

## <a name="connected-waste-management"></a>联网废弃物管理 

联网废弃物管理应用是一个 IoT Central 应用模板，可帮助你立即开始进行 IoT 解决方案开发，使智能城市能够进行远程监测，以最大化垃圾回收的效率。 

:::image type="content" source="media/concepts-app-templates/connected-waste-management-dashboard.png" alt-text="联网废弃物管理应用模板":::

联网废弃物管理应用模板包括预配置的：

- 示例仪表板
- 示例联网垃圾箱设备模板
- 模拟的联网垃圾箱设备
- 预配置的规则和作业
- 贴牌式品牌设计 

在[联网废弃物管理应用程序教程](../government/tutorial-connected-waste-management.md)中获取入门信息。

## <a name="continuous-patient-monitoring"></a>持续患者监视 

在医疗保健 IoT 领域，持续患者监视是帮助降低再次入院的风险、更有效地管理慢性疾病和改善患者治疗效果的关键因素之一。 持续患者监视可以分为两大类：

1. **住院患者监视**：使用医院的医用可穿戴设备和其他设备，护理团队可以监视患者的生命体征和身体状况，而无需派护士对患者进行每日多次的检查。 通过通知功能，护理团队可以在患者需要关注的关键时刻及时了解情况，并有效地安排其时间。
1. **远程患者监视**：通过使用医用可穿戴设备和患者报告的结果 (PRO) 来监视非住院患者，可以降低再次入院的风险。 可以收集来自慢性病患者和复健患者的数据，以确保患者遵循医疗计划，并确保在患者病情恶化到危急状态前通知护理团队。

此应用程序模板可用于生成两类持续患者监视的解决方案。 优点包括：

- 将各种医用可穿戴设备无缝连接到 IoT Central 实例。
- 监视和管理设备，以确保其持续正常运行。
- 围绕设备数据创建自定义规则，以触发相应警报。
- 将患者健康状况数据导出到 Azure API for FHIR，它是一个合规的数据存储服务。
- 将聚合的见解导出到现有或新的业务应用程序中。

:::image type="content" source="media/concepts-app-templates/in-patient-dashboard.png" alt-text="CPM - 仪表板":::


## <a name="next-steps"></a>后续步骤

了解 IoT Central 应用程序模板是什么以后，即可开始[创建 IoT Central 应用程序](quick-deploy-iot-central.md)。
