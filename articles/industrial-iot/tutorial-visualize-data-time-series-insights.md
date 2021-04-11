---
title: 在 Azure 时序见解中可视化 OPC UA 数据
description: 在本教程中，了解如何用时序见解可视化数据。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787211"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>教程：使用时序见解 (TSI) 可视化数据

OPC 发布服务器模块连接到 OPC UA 服务器，并将这些服务器中的数据发布到 IoT 中心。 工业 IoT 平台中的遥测处理器处理这些事件，并将过程示例转发给 TSI 和其他使用者。  

本操作指南演示如何使用此时序见解环境可视化和分析 OPC UA 遥测数据。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 所有教程都包含一个列表，其中汇总了要完成的步骤
> * 每个项目符号点都与键 H2 对齐
> * 在教程中使用这些绿色复选框

## <a name="prerequisite"></a>先决条件

* 部署 IIoT 平台以获取自动创建的时序见解环境
* 正在将数据发布到 IoT 中心

## <a name="time-series-insights-explorer"></a>时序见解资源管理器

时序见解资源管理器是一个 Web 应用，可用于可视化遥测数据。 若要检索应用程序的 url，请打开作为部署结果保存的 `.env` 文件。  在浏览器中打开 `PCS_TSI_URL` 变量中的 Url。  

使用时序见解资源管理器之前，必须向有权可视化数据的用户授予对 TSI 数据的访问权限。 请注意，在新部署中，默认情况下不设置任何数据访问策略，因此无人可以看到数据。 需要在 Azure 门户的“时序见解环境”中设置数据访问策略，该环境部署在 IIoT 平台部署的资源组中，如下所示：

   ![时序见解资源管理器 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

选择数据访问策略：

   ![时序见解资源管理器 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

分配所需的用户：

   ![时序见解资源管理器 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


在 TSI 资源管理器中，请记下未分配的时序实例。 一个 TSI 实例对应于源自 OPC 服务器中已发布节点的特定数据点的时间/值系列。 EndpointId、SubscriptionId 和 NodeId 分别标识了 TSI 实例，即 OPC UA 数据点。 TSI 实例模型基于从 IIoT 平台遥测处理器的事件中心引入的遥测数据自动检测到并显示在资源管理器中。

   ![时序见解资源管理器 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

可以通过右键单击 TSI 实例并选择“值”来在图表中对遥测数据进行可视化。 要在图表中使用的期限可以在右上角进行调整。 可以基于相同的时间选择来可视化多个实例的值。

有关详细信息，请参阅[快速入门：浏览 Azure 时序见解预览版](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>定义并应用新模型

由于遥测实例现在只是原始格式，因此需要进行情景化 

有关 TSI 模型的详细信息，请参阅 [Azure 时序见解预览版中的时序模型](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)

1. 步骤 1 - 在资源管理器的“模型”选项卡中，为引入的遥测数据定义一个新的层次结构。 层次结构是一种逻辑树结构，旨在使用户能够插入通过 TSI 实例进行更直观的导航所需的元信息。 用户可以创建/删除/修改以后可针对各种 TSI 实例实例化的层次结构模板。

   ![步骤 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. 步骤 2 - 为值定义新类型。 在我们的示例中，仅处理数值数据类型

   ![步骤 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. 步骤 3 - 选择需要在之前定义的层次结构中分类的新的 TSI 实例

   ![步骤 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. 步骤 4 - 在实例属性 - 名称、说明、数据值和层次结构字段中填写，以便匹配逻辑结构 

   ![步骤 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. 步骤 5 - 针对所有未分类的 TSI 实例重复步骤 5

   ![步骤 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. 步骤 6 - 返回到 TSI 资源管理器的主页，遍历分类实例层次结构并选择要分析的数据点的值

   ![步骤 6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>将时序见解连接到 Power BI

还可以将时序见解环境连接到 Power BI。  有关详细信息，请参阅[如何将 TSI 连接到 Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) 和[在 Power BI 中可视化 TSI 中的数据](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi)。


## <a name="next-steps"></a>后续步骤
现在，您已了解如何在 TSI 中实现数据可视化，您可以查看工业 IoT GitHub 存储库：

> [!div class="nextstepaction"]
> [IIoT 平台 GitHub 存储库](https://github.com/Azure/iot-edge-opc-publisher)