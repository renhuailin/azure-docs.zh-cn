---
title: 在工作室中使用托管联机终结点（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何通过 Azure 机器学习工作室创建和使用托管联机终结点（预览版）。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: how-to, managed online endpoints, devplatv2
ms.author: ssambare
author: shivanissambare
ms.reviewer: peterlu
ms.date: 05/25/2021
ms.openlocfilehash: 4f2b69a4a4009784e7943d57b0ec5653528a418e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448162"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>在工作室中创建和使用托管联机终结点（预览版）

了解如何在 Azure 机器学习中使用工作室创建和管理托管联机终结点（预览版）。 使用托管联机终结点可以简化生产规模的部署。 有关托管联机终结点的详细信息，请参阅[什么是终结点](concept-endpoints.md)。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建托管联机终结点
> * 查看托管联机终结点
> * 更新托管联机终结点
> * 删除托管联机终结点和部署

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
- 一个[已注册到工作区的模型](how-to-deploy-and-where.md#registermodel)。
- 一个用于已部署的模型的评分文件。 有关注册模型和创建评分文件的分步示例，请参阅[教程：图像分类](tutorial-train-models-with-aml.md)。
- 一个已注册到工作区的自定义环境，或者一个使用 Python 环境的 Docker 容器注册表映像。 有关环境的详细信息，请参阅[在 Azure 机器学习中创建和使用软件环境](how-to-use-environments.md)。

## <a name="create-a-managed-online-endpoint-preview"></a>创建托管联机终结点（预览版）

直接在浏览器中使用工作室创建托管联机终结点（预览版）。 在工作室中创建托管联机终结点时，必须定义初始部署。 不能创建空的托管联机终结点。

1. 转到 [Azure 机器学习工作室](https://ml.azure.com)。
1. 在左侧导航栏中，选择“终结点”页。
1. 选择“+ 创建(预览版)”。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="在“终结点”选项卡中创建托管联机终结点":::

也可以通过工作室中的“模型”页创建托管联机终结点。 这是将模型添加到现有托管联机部署的简单方法。

1. 转到 [Azure 机器学习工作室](https://ml.azure.com)。
1. 在左侧导航栏中，选择“模型”页。
1. 通过选中模型名称旁边的圆圈来选择该模型。
1. 选择“部署” > “部署到终结点(预览版)” 。

按照设置向导中的步骤配置托管联机终结点。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/models-page-deployment-latest.png" alt-text="在“模型”选项卡中创建托管联机终结点":::

## <a name="view-managed-online-endpoints-preview"></a>查看托管联机终结点（预览版）

可以在“终结点”页中查看托管联机终结点（预览版）。 使用终结点详细信息页查找关键信息，包括终结点 URI、状态、测试工具、活动监视器、部署日志和示例使用代码：

1. 在左侧导航栏中，选择“终结点”。
1. （可选）创建一个基于“计算类型”的筛选器，以便仅显示“托管”计算类型  。
1. 选择终结点名称以查看终结点详细信息页。

### <a name="test"></a>测试

使用终结点详细信息页中的“测试”选项卡来测试托管联机部署。 提供示例输入并查看结果。

1. 在终结点的详细信息页中选择“测试”选项卡。
1. 使用下拉列表选择要测试的部署。
1. 提供示例输入。
1. 选择“测试”。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="直接在浏览器中提供示例数据来测试部署":::

### <a name="monitoring"></a>监视

使用“监视”选项卡查看托管联机终结点的概要活动监视图。

若要使用“监视”选项卡，必须在创建终结点时选择“启用 Application Insights 诊断和数据收集”。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="在工作室中监视终结点级别的指标":::

有关如何查看其他监视器和警报的详细信息，请参阅[如何监视托管联机终结点](how-to-monitor-online-endpoints.md)。

## <a name="update-managed-online-endpoints-preview"></a>更新托管联机终结点（预览版）

了解如何更新托管联机终结点（预览版），以添加更多部署和调整流量分配。

### <a name="add-a-managed-online-deployment"></a>添加托管联机部署

使用以下说明将部署添加到现有的托管联机终结点：

1. 在[终结点详细信息页](#view-managed-online-endpoints-preview)中选择“+ 添加部署”按钮。
2. 按照说明完成部署。

或者，可以使用“模型”页添加部署：

1. 在左侧导航栏中，选择“模型”页。
1. 通过选中模型名称旁边的圆圈来选择该模型。
1. 选择“部署” > “部署到终结点(预览版)” 。
1. 选择部署到现有的托管联机终结点。

> [!NOTE]
> 添加新部署时，可以调整终结点中各部署之间的流量均衡。
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="使用滑块控制多个部署之间的流量分布":::

### <a name="update-deployment-traffic-allocation"></a>更新部署流量分配

使用“部署流量分配”来控制传入终结点中每个部署的请求百分比。

1. 在终结点详细信息页中，选择“更新流量”。
2. 调整流量，然后选择“更新”。

> [!TIP]
> “总流量百分比”之和必须要么是 0%（禁用流量），要么是 100%（启用流量）。

### <a name="update-deployment-instance-count"></a>更新部署实例计数

使用以下说明，通过调整实例数来纵向扩展或缩减单个部署：

1. 在终结点详细信息页中： 找到要更新的部署对应的卡。
1. 在部署详细信息卡中选择编辑图标。
1. 更新实例计数。
1. 选择“更新”。


## <a name="delete-managed-online-endpoints-and-deployments-preview"></a>删除托管联机终结点和部署（预览版）

了解如何删除整个托管联机终结点（预览版）及其关联的部署（预览版）。 或者，从托管联机终结点中删除单个部署。

### <a name="delete-a-managed-online-endpoint"></a>删除托管联机终结点

删除托管联机终结点也会删除与其关联的所有部署。

1. 转到 [Azure 机器学习工作室](https://ml.azure.com)。
1. 在左侧导航栏中，选择“终结点”页。
1. 通过选中模型名称旁边的圆圈来选择终结点。
1. 选择“删除”。

或者，可以直接在[终结点详细信息页](#view-managed-online-endpoints-preview)中删除托管联机终结点。 


### <a name="delete-an-individual-deployment"></a>删除单个部署

使用以下步骤从托管联机终结点中删除单个部署。 这会影响托管联机终结点中的其他部署：

> [!NOTE]
> 无法删除已分配了流量的部署。 在删除部署之前，必须先将其[流量分配设置](#update-deployment-traffic-allocation)为 0%。

1. 转到 [Azure 机器学习工作室](https://ml.azure.com)。
1. 在左侧导航栏中，选择“终结点”页。
1. 选择你的托管联机终结点。
1. 在终结点详细信息页中，找到要删除的部署。
1. 选择“删除”图标。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何使用 Azure 机器学习托管联机终结点。 请参阅以下后续步骤：

- [什么是终结点？](concept-endpoints.md)
- [如何使用 Azure CLI 部署托管联机终结点](how-to-deploy-managed-online-endpoints.md)
- [通过 REST 部署模型（预览版）](how-to-deploy-with-rest.md)
- [如何监视托管联机终结点](how-to-monitor-online-endpoints.md)
- [排查托管联机终结点的部署和评分问题（预览版）](how-to-troubleshoot-managed-online-endpoints.md)
- [查看 Azure 机器学习托管联机终结点（预览版）的成本](how-to-view-online-endpoints-costs.md)
- [管理和增大 Azure 机器学习资源的配额](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)