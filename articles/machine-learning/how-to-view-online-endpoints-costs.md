---
title: 查看托管联机终结点（预览版）的成本
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中查看托管联机终结点的成本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 05/03/2021
ms.topic: conceptual
ms.custom: how-to, deploy, devplatv2
ms.openlocfilehash: acf8b01a0d1179cb029e0729cbeb57f856b1f11a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448144"
---
# <a name="view-costs-for-an-azure-machine-learning-managed-online-endpoint-preview"></a>查看 Azure 机器学习托管联机终结点（预览版）的成本

了解如何查看托管联机终结点（预览版）的成本。 终结点的成本是对关联的工作区计收的。 可以使用标记来查看特定终结点的成本。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!IMPORTANT]
> 本文仅适用于查看 Azure 机器学习托管联机终结点（预览版）的成本。 托管联机终结点不同于其他资源，因为它们必须使用标记来跟踪成本。 有关如何查看其他 Azure 资源的成本的详细信息，请参阅[快速入门：通过成本分析了解和分析成本](../cost-management-billing/costs/quick-acm-cost-analysis.md)。

## <a name="prerequisites"></a>先决条件

- 部署 Azure 机器学习托管联机终结点（预览版）。
- 在部署终结点的订阅上至少拥有[账单读取者](../role-based-access-control/role-assignments-portal.md)访问权限

## <a name="view-costs"></a>查看成本

导航到订阅的“成本分析”页：

1. 在 [Azure 门户](https://portal.azure.com)中，选择订阅的“成本分析”。

    [![托管联机终结点成本分析：Azure 门户中订阅的屏幕截图，左侧显示了由红框围绕的“成本分析”按钮。](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png#lightbox)

创建一个筛选器，以将数据范围限定为你的 Azure 机器学习工作区资源：

1. 在顶部导航栏中，选择“添加筛选器”。

1. 在第一个筛选器下拉列表中，选择“资源”作为筛选器类型。

1. 在第二个筛选器下拉列表中，选择你的 Azure 机器学习工作区。

    [![托管联机终结点成本分析：“成本分析”视图的屏幕截图，右上方显示了由红框围绕的“添加筛选器”按钮。](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png#lightbox)

创建一个标记筛选器，以显示你的托管联机终结点和/或托管联机部署：
1. 选择“添加筛选器” > “标记” > “azuremlendpoint”：“\<your endpoint name>”   
1. 选择“添加筛选器” > “标记” > “azuremldeployment”：“\<your deployment name>”  。

    > [!NOTE]
    > 此图中的美元金额值是虚构的，不反映实际成本。

    [![托管联机终结点成本分析：“成本分析”视图的屏幕截图，右上方显示了由红框围绕的“标记”按钮。](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png#lightbox)

## <a name="next-steps"></a>后续步骤
- [什么是终结点？](concept-endpoints.md)
- 了解如何[监视托管联机终结点](./how-to-monitor-online-endpoints.md)。
- [如何使用 Azure CLI 部署托管联机终结点](how-to-deploy-managed-online-endpoints.md)
- [如何使用工作室部署托管联机终结点](how-to-use-managed-online-endpoint-studio.md)