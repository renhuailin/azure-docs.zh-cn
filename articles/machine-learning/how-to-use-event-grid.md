---
title: 在 ML 工作流中触发事件（预览版）
titleSuffix: Azure Machine Learning
description: 在 Azure 机器学习中设置事件驱动的应用程序、进程或 CI/CD 机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 0f5cdc9acd68f67c57027d6b68e6c79a8ab1a24e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767292"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>基于 Azure 机器学习事件触发应用程序、进程或 CI/CD 工作流（预览版）

本文介绍如何在 [Azure 事件网格](../event-grid/index.yml)检测到某些条件时，基于 Azure 机器学习事件（例如，失败通知电子邮件或 ML 管道运行）设置事件驱动的应用程序、进程或 CI/CD 工作流。

Azure 机器学习管理机器学习进程的整个生命周期，包括模型训练、模型部署和监视。 可以借助现代无服务器体系结构，使用事件网格对 Azure 机器学习事件做出反应，例如完成训练运行、注册和部署模型以及检测数据偏移。 然后，可以订阅和使用事件，例如运行状态更改、运行完成、模型注册、模型部署以及工作区中的数据偏移检测。

何时将事件网格用于事件驱动的操作：
* 在运行失败和运行完成时发送电子邮件
* 注册模型后使用 Azure 函数
* 将事件从 Azure 机器学习流式传输到各种终结点
* 检测到偏移时触发 ML 管道

## <a name="prerequisites"></a>先决条件
要使用事件网格，需要以参与者或所有者身份访问将为其创建事件的 Azure 机器学习工作区。

## <a name="the-event-model--types"></a>事件模型与类型

Azure 事件网格从 Azure 机器学习和其他 Azure 服务等源中读取事件。 然后，会将这些事件发送到事件处理程序，例如，Azure 事件中心、Azure Functions、逻辑应用，等等。 下图显示了事件网格如何连接源和处理程序，但这不是所支持的集成的完整列表。

![Azure 事件网格功能模型](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

有关事件源和事件处理程序的详细信息，请参阅[什么是事件网格？](../event-grid/overview.md)。

### <a name="event-types-for-azure-machine-learning"></a>Azure 机器学习的事件类型

Azure 机器学习在机器学习生命周期的各个点提供事件： 

| 事件类型 | 说明 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 在机器学习试验运行完成时引发 |
| `Microsoft.MachineLearningServices.ModelRegistered` | 在工作区中注册机器学习模型时引发 |
| `Microsoft.MachineLearningServices.ModelDeployed` | 当具有一个或多个模型的推理服务完成部署时引发 |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 在两个数据集的数据偏移检测作业完成时引发 |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 运行状态更改时引发，当前仅在运行状态为“失败”时引发 |

### <a name="filter--subscribe-to-events"></a>筛选并订阅事件

这些事件通过 Azure 事件网格发布。 使用 Azure门户、PowerShell 或 Azure CLI，客户可以通过[指定一种或多种事件类型并筛选条件](../event-grid/event-filtering.md)来轻松地订阅事件。 

设置事件时，可以应用筛选器，以仅在特定事件数据上触发。 在下面的示例中，对于运行状态更改事件，你可以按运行类型进行筛选。 仅当满足条件时才会触发该事件。 要了解可以作为筛选依据的事件数据，请参阅 [Azure 机器学习事件网格架构](../event-grid/event-schema-machine-learning.md)。 

Azure 机器学习事件的订阅由 Azure 基于角色的访问控制 (Azure RBAC) 进行保护。 只有工作区[参与者或所有者](how-to-assign-roles.md#default-roles)可以创建、更新和删除事件订阅。  可在[创建](/cli/azure/eventgrid/event-subscription)事件订阅期间或以后将筛选器应用于事件订阅。 


1. 转到 Azure 门户，选择新订阅或现有订阅。 

1. 选择“筛选器”选项卡，然后向下滚动到“高级筛选器”。 对于“键”和“值”，提供要作为筛选依据的属性类型 。 可以在下面看到事件仅在运行类型为管道运行或管道步骤运行时触发。  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="筛选事件":::


+ **按事件类型筛选：** 事件订阅可以指定一个或多个 Azure 机器学习事件类型。

+ **按事件主题筛选：** Azure 事件网格支持根据“开头为”和“结尾为”的匹配筛选主题，以便将具有匹配主题的事件传递给订阅者 。 不同的机器学习事件具有不同的主题格式。

  | 事件类型 | 使用者格式 | 示例主题 |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **高级筛选**：Azure 事件网格还支持基于已发布事件架构的高级筛选。 有关 Azure 机器学习事件架构的详细信息，请参阅 [Azure 机器学习的 Azure 事件网格事件架构](../event-grid/event-schema-machine-learning.md)。  可以执行的一些高级筛选的示例包括：

  对于 `Microsoft.MachineLearningServices.ModelRegistered` 事件，要筛选模型的标记值：

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  要了解有关如何应用筛选器的详细信息，请参阅[事件网格的筛选事件](../event-grid/how-to-filter-events.md)。

## <a name="consume-machine-learning-events"></a>使用机器学习事件

处理机器学习事件的应用程序应遵循以下建议的做法：

> [!div class="checklist"]
> * 由于可将多个订阅配置为将事件路由至相同的事件处理程序，因此请勿假定事件来自特定的源，而是应检查消息的主题，确保它来自所期望的机器学习工作区。
> * 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
> * 消息在一段延迟时间后会无序到达，请使用 etag 字段来了解对象的相关信息是否是最新的。  此外，还可使用 sequencer 字段来了解任何特定对象的事件顺序。
> * 忽略不了解的字段。 此做法有助于适应将来可能添加的新功能。
> * 失败或取消的 Azure 机器学习操作不会触发事件。 例如，如果模型部署失败，则不会触发 Microsoft.MachineLearningServices.ModelDeployed。 设计应用程序时，请考虑此类失败模式。 始终可以使用 Azure 机器学习 SDK、CLI 或门户来检查操作状态并了解详细的失败原因。

Azure 事件网格允许客户生成可由 Azure 机器学习事件触发的反耦合消息处理程序。 消息处理程序的一些值得注意的示例如下：
* Azure Functions
* Azure 逻辑应用
* Azure 事件中心
* Azure 数据工厂管道
* 通用 Webhook，可在 Azure 平台或其他平台上托管

## <a name="set-up-in-azure-portal"></a>在 Azure 门户中设置

1. 打开 [Azure 门户](https://portal.azure.com)，然后转到 Azure 机器学习工作区。

1. 在左侧栏中选择“事件”，然后选择“事件订阅”。 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. 选择要使用的事件类型。 例如，以下屏幕截图中已选择“模型注册”、“模型部署”、“运行完成”以及“检测到数据集偏移”   ：

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. 选择要将事件发布到的终结点。 以下屏幕截图中选择的终结点是“事件中心”：

    ![屏幕截图显示了“创建事件订阅”窗格，其中的“选择事件中心”处于打开状态。](./media/how-to-use-event-grid/select-event-handler.png)

确认选择后，请单击“创建”。 配置后，这些事件将被推送到终结点。


### <a name="set-up-with-the-cli"></a>使用 CLI 进行设置

可以安装最新的 [Azure CLI](/cli/azure/install-azure-cli)，也可以使用作为 Azure 订阅的一部分提供的 Azure Cloud Shell。

要安装事件网格扩展，请在 CLI 中使用以下命令：

```azurecli-interactive
az add extension --name eventgrid
```

下面的示例演示如何选择 Azure 订阅以及为 Azure 机器学习创建新的事件订阅：

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>示例

### <a name="example-send-email-alerts"></a>示例：发送电子邮件警报

使用 [Azure 逻辑应用](../logic-apps/index.yml)为所有事件配置电子邮件。 根据条件进行自定义，并指定收件人，以实现跨协作团队的协作和意识。

1. 在 Azure 门户中转到 Azure 机器学习工作区，并从左侧栏中选择“事件”选项卡。 在此处选择“逻辑应用”。 

    ![屏幕截图显示了一个包含“逻辑应用”的机器学习工作区“事件”页。](./media/how-to-use-event-grid/select-logic-ap.png)

1. 登录到逻辑应用 UI，并选择机器学习服务作为主题类型。 

    ![屏幕截图显示了“当资源事件发生时”对话框，其中的机器学习被选为资源类型。](./media/how-to-use-event-grid/select-topic-type.png)

1. 选择要进行通知的事件。 例如，以下屏幕截图中选择的是“RunCompleted”。

    ![屏幕截图显示了“当资源事件发生时”对话框，其中选择了一种事件类型。](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 你可以使用上一部分中的筛选方法，或者添加筛选器以仅对部分事件类型触发逻辑应用。 在以下屏幕截图中，使用了前缀筛选器 /datadriftID/runs/ 。

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. 接下来添加一个使用此事件的步骤并搜索电子邮件。 可以使用多个不同的电子邮件帐户来接收事件。 还可以配置发送电子邮件警报的时间。

    ![屏幕截图显示了“选择操作”对话框，在搜索行中输入了电子邮件。](./media/how-to-use-event-grid/select-email-action.png)

1. 选择“发送电子邮件”并填写参数。 可以在主题中包含“事件类型”和“主题”来帮助筛选事件 。 还可以在邮件正文中包含指向工作区页面的链接。 

    ![屏幕截图显示了“发送电子邮件”对话框，其中的“主题”和“事件类型”已添加到右侧列表的主题行。](./media/how-to-use-event-grid/configure-email-body.png)

1. 若要保存此操作，请选择页面左上角的“另存为”。 在出现的右侧栏中，确认已创建此操作。

    ![屏幕截图显示了逻辑应用设计器中的“另存为”和“创建”按钮。](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>示例：重新训练数据偏移触发器

随着时间的推移，模型会过时，并且在其运行上下文中也不再有用。 要判断是否应重新训练某个模型，一种方法是检测数据偏移。 

此示例演示如何使用 Azure 逻辑应用和事件网格来触发重新训练。 当模型的训练和服务数据集之间发生数据偏移时，此示例将触发 Azure 数据工厂管道。

在开始之前，请执行以下操作：

* 设置数据集监视器以[检测工作区中的数据偏移](how-to-monitor-datasets.md)
* 创建已发布的 [Azure 数据工厂管道](../data-factory/index.yml)。

此示例中使用的是简单的数据工厂管道，用于将文件复制到 blob 存储区，并运行已发布的机器学习管道。 有关此方案的详细信息，请参阅如何设置 [Azure 数据工厂中的机器学习步骤](../data-factory/transform-data-machine-learning-service.md)

![屏幕截图显示了工厂资源中的训练管道，其中包含馈送 ML 执行管道 1 的复制数据 1。](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 首先，创建逻辑应用。 转到 [Azure 门户](https://portal.azure.com)，搜索逻辑应用并选择“创建”。

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 填写所需信息。 若要简化此过程，请使用与 Azure 数据工厂管道和 Azure 机器学习工作区相同的订阅和资源组。

    ![屏幕截图显示了“逻辑应用创建”窗格。](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 创建逻辑应用后，选择“当事件网格资源事件发生时”。 

    ![屏幕截图显示了逻辑应用设计器，开头为常用触发器选项，包括“当事件网格资源事件发生时”。](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 登录并填写事件的详细信息。 将“资源名称”设为工作区名称。 将“事件类型”设为 DatasetDriftDetected 。

    ![屏幕截图显示了“当资源事件发生时”对话框，其中选择了一个事件类型项。](./media/how-to-use-event-grid/login-and-add-event.png)

1. 添加新步骤并搜索“Azure 数据工厂”。 选择“创建管道运行”。 

    ![屏幕截图显示了“选择操作”窗格，其中已选择“创建管道运行”。](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 登录并指定要运行的已发布的 Azure 数据工厂管道。

    ![屏幕截图显示了“创建管道运行”窗格，其中包含各种值。](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 使用页面左上角的“保存”按钮保存并创建逻辑应用。 要查看创建的应用，请在 [Azure 门户](https://portal.azure.com)中转到自己的工作区并单击“事件”。

    ![屏幕截图显示了突出显示“逻辑应用”的事件。](./media/how-to-use-event-grid/show-logic-app-webhook.png)

现在，只要发生偏移，就会触发数据工厂管道。 在[新的工作区门户](https://ml.azure.com)上查看数据偏移运行和机器学习管道的详细信息。 

![屏幕截图显示了管道终结点。](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>示例：基于标记部署模型

Azure 机器学习模型对象包含可以基于其进行部署的参数，例如模型名称、版本、标记和属性。 模型注册事件可触发终结点，你可使用 Azure 函数基于这些参数的值部署模型。

有关示例，请参阅 [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) 存储库，并执行自述文件中的步骤。

## <a name="next-steps"></a>后续步骤

详细了解事件网格并尝试使用 Azure 机器学习事件：

- [关于事件网格](../event-grid/overview.md)

- [Azure 机器学习的事件架构](../event-grid/event-schema-machine-learning.md)
