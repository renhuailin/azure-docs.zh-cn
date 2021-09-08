---
title: 如何在工作室中使用批处理终结点
titleSuffix: Azure Machine Learning
description: 在本文中，可了解如何在 Azure 机器学习工作室中创建批处理终结点。 批处理终结点用于连续对大型数据批量评分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
ms.date: 08/16/2021
ms.custom: how-to, studio, managed-batch-endpoints
ms.openlocfilehash: f5f5d71b22c505580ed4e6b339f5b9c30e19eb89
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868327"
---
# <a name="how-to-use-batch-endpoints-preview-in-azure-machine-learning-studio"></a>如何在 Azure 机器学习工作室中使用批处理终结点（预览版）

本文介绍如何在 [Azure 机器学习工作室](https://ml.azure.com)中使用批处理终结点（预览版）执行批量评分。 有关详细信息，请参阅[什么是 Azure 机器学习终结点（预览版）？](concept-endpoints.md)。

本文介绍：

> [!div class="checklist"]
> * 为 MLflow 模型创建一个附带无代码体验的批处理终结点
> * 检查批处理终结点详细信息
> * 启动批量评分作业
> * Azure 机器学习工作室中的批处理终结点功能概述

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

* 一个 Azure 订阅 - 如果你没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

* 示例存储库 - 克隆 [AzureML 示例存储库](https://github.com/Azure/azureml-examples)。 本文将使用 `/cli/endpoints/batch` 中的资产。

* 可在其中运行批量评分工作流的计算目标。 有关创建计算目标的详细信息，请参阅[在 Azure 机器学习工作室中创建计算目标](how-to-create-attach-compute-studio.md)。

* 注册机器学习模型。

## <a name="create-a-batch-endpoint"></a>创建批处理终结点

可通过两种方法在 Azure 机器学习工作室中创建批处理终结点：

* 在“终结点”页中，选择“批处理终结点”，然后选择“+ 创建”  。 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-endpoints.png" alt-text="从“终结点”页创建批处理终结点/部署的屏幕截图":::

OR

* 在“模型”页中，选择要部署的模型，然后选择“部署到批处理终结点(预览版)” 。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/models-page-deployment.png" alt-text="从“模型”页创建批处理终结点/部署的屏幕截图":::

> [!TIP]
> 如果使用的是 MLflow 模型，则可以使用无代码批处理终结点创建方式。 也就是说，你无需准备评分脚本和环境，系统会自动生成这两者。 有关详细信息，请参阅[使用 MLflow 和 Azure 机器学习训练和跟踪 ML 模型（预览版）](how-to-use-mlflow.md)。
> 
> :::image type="content" source="media/how-to-use-batch-endpoints-studio/mlflow-model-wizard.png" alt-text="部署 MLflow 模型的屏幕截图":::

完成向导中的所有步骤以创建批处理终结点和部署。

:::image type="content" source="media/how-to-use-batch-endpoints-studio/review-batch-wizard.png" alt-text="批处理终结点/部署查看屏幕的屏幕截图":::

## <a name="check-batch-endpoint-details"></a>检查批处理终结点详细信息

创建批处理终结点后，从“终结点”页中选择它可查看详细信息。

:::image type="content" source="media/how-to-use-batch-endpoints-studio/batch-endpoint-details.png" alt-text="检查批处理终结点和部署详细信息的屏幕截图":::

## <a name="start-a-batch-scoring-job"></a>启动批量评分作业

批量评分工作负载以脱机作业的形式运行。 默认情况下，批量评分将评分输出存储在 Blob 存储中。 还可以配置输出位置并覆盖某些设置以获得最佳性能。

1. 选择“+ 创建作业”：

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-job.png" alt-text="用于启动批量评分的创建作业选项的屏幕截图":::

1. 可以在从下拉列表提交作业时更新默认部署：

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/job-setting-batch-scoring.png" alt-text="使用部署提交批处理作业的屏幕截图":::

### <a name="overwrite-settings"></a>覆盖设置

可以在启动批量评分作业时覆盖某些设置。 例如，可以覆盖设置以更好地利用计算资源或提高性能。 若要替代设置，请选择“替代部署设置”并提供设置。 有关详细信息，请参阅[使用批处理终结点](how-to-use-batch-endpoint.md#overwrite-settings)。

:::image type="content" source="media/how-to-use-batch-endpoints-studio/overwrite-setting.png" alt-text="启动批处理作业时覆盖设置的屏幕截图":::

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>使用不同的输入选项启动批量评分作业

可以使用两个选项在 Azure 机器学习工作室中指定数据输入：

* 使用“已注册数据集”：

    > [!NOTE]
    > 在预览期，仅支持 FileDataset。 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-dataset-for-job.png" alt-text="选择已注册数据集作为输入选项的屏幕截图":::

OR

* 使用“数据存储”：

    可以指定 AML 注册的数据存储，或者，如果数据公开可用，请指定公共路径。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-datastore-job.png" alt-text="选择数据存储作为输入选项的屏幕截图":::

### <a name="configure-the-output-location"></a>配置输出位置

默认情况下，批量评分结果存储在工作区的默认 blob 存储中。 结果位于以作业名称（系统生成的 GUID）命名的文件夹中。

若要更改结果的存储位置，请在启动作业时提供 blob 存储和输出路径。

> [!IMPORTANT]
> 必须使用唯一的输出位置。 如果输出文件已存在，批量评分作业将失败。 

:::image type="content" source="media/how-to-use-batch-endpoints-studio/configure-output-location.png" alt-text="（可选）配置输出位置的屏幕截图":::

### <a name="summary-of-all-submitted-jobs"></a>所有已提交作业的摘要

若要查看终结点的所有已提交作业的摘要，请选择终结点，然后选择“运行”选项卡。

:::image type="content" source="media/how-to-use-batch-endpoints-studio/summary-jobs.png" alt-text="提交到批处理终结点的作业摘要的屏幕截图":::
## <a name="check-batch-scoring-results"></a>检查批量评分结果

若要了解如何查看评分结果，请参阅[使用批处理终结点](how-to-use-batch-endpoint.md#check-batch-scoring-results)。

## <a name="add-a-deployment-to-an-existing-batch-endpoint"></a>将部署添加到现有批处理终结点

在 Azure 机器学习工作室中，可通过两种方法将部署添加到现有批处理终结点：

* 在“终结点”页中，选择要将新部署添加到的批处理终结点。 选择“+ 添加部署”，然后完成向导以添加新部署。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-option.png" alt-text="添加新部署选项的屏幕截图":::

OR

* 在“模型”页中，选择要部署的模型。 然后从下拉列表中选择“部署到批处理终结点(预览版)”选项。 在向导中的“终结点”屏幕上，选择“现有” 。 完成向导以添加新部署。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-models-page.png" alt-text="选择现有批处理终结点以添加新部署的屏幕截图":::

## <a name="update-the-default-deployment"></a>更新默认部署

如果终结点有多个部署，则其中一个部署是默认部署。 默认部署会接收发送到终结点的 100% 流量。 若要更改默认部署，请使用以下步骤：

1. 在“终结点”页上选择终结点。
1. 选择“更新默认部署”。 在“详细信息”选项卡中，选择要设置为默认部署的部署，然后选择“更新” 。
    :::image type="content" source="media/how-to-use-batch-endpoints-studio/update-default-deployment.png" alt-text="更新默认部署的屏幕截图":::

## <a name="delete-batch-endpoint-and-deployments"></a>删除批处理终结点和部署

若要删除终结点，请从“终结点”页中选择终结点，然后选择删除 。

> [!WARNING]
> 删除终结点也会删除该终结点的所有部署。

若要删除部署，请从“终结点”页中选择终结点，选择相应部署，然后选择删除 。

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建和调用批处理终结点。 有关 Azure 机器学习的详细信息，请参阅下述其他文章：

* [排查批处理终结点问题](how-to-troubleshoot-batch-endpoints.md)
* [使用托管联机终结点（预览版）部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md)
