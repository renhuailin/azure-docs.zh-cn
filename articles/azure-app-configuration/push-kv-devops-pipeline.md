---
title: 使用 Azure Pipelines 将设置推送到应用配置
description: 了解如何使用 Azure Pipelines 将键值推送到应用配置存储
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: 61b50f8e3c5b32401b4f7339627b9b704a4b94fd
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112122984"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>使用 Azure Pipelines 将设置推送到应用配置

[Azure 应用配置推送](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)任务可将配置文件中的键值推送到你的应用配置存储中。 此任务在管道内实现了全循环功能，因为你现在可以从应用配置存储中拉取设置，还可以将设置推送到应用配置存储。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 应用配置资源 - 在 [Azure 门户](https://portal.azure.com)中免费创建一个。
- Azure DevOps 项目 - [免费创建一个](https://go.microsoft.com/fwlink/?LinkId=2014881)
- “Azure 应用配置推送”任务 - 从 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) 免费下载。

## <a name="create-a-service-connection"></a>创建服务连接

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>添加角色分配

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>在生成中使用

本部分介绍如何在 Azure DevOps 生成管道中使用“Azure 应用配置推送”任务。

1. 通过单击“管道” > “管道”，导航到“生成管道”页。 可在[此处](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)找到有关生成管道的文档。
      - 如果要创建新的“生成管道”，则在该过程的最后一步，在“检查”选项卡上，选择管道右侧的“显示助手” 。
      ![屏幕截图显示新管道的“显示辅助”按钮。](./media/new-pipeline-show-assistant.png)
      - 如果使用的是现有的“生成管道”，请单击右上角的“编辑”按钮。
      ![屏幕截图显示现有管道的“编辑”按钮。](./media/existing-pipeline-show-assistant.png)
1. 搜索“Azure 应用程序配置推送”任务。
![屏幕截图显示“添加任务”对话框，其中搜索框中显示“Azure 应用程序配置推送”。](./media/add-azure-app-configuration-push-task.png)
1. 配置任务的必要参数，以将配置文件中的键值推送到应用配置存储。 参数的说明在下面的“参数”部分以及每个参数旁边的工具提示中提供。
![屏幕截图显示应用程序配置推送任务参数。](./media/azure-app-configuration-push-parameters.png)
1. 保存并将一个生成排入队列。 生成日志将显示在执行任务期间发生的任何故障。

## <a name="use-in-releases"></a>在发布中使用

本部分介绍如何在 Azure DevOps 发布管道中使用“Azure 应用配置推送”任务。

1. 通过选择“管道” > “发布”，导航到“发布管道”页。 可在[此处](/azure/devops/pipelines/release)找到有关发布管道的文档。
1. 选择现有的发布管道。 如果没有现有的发布管道，请选择“+ 新建”创建一个新的发布管道。
1. 选择右上角的“编辑”按钮以编辑发布管道。
1. 从“任务”下拉列表中，选择要向其添加任务的“阶段” 。 可在[此处](/azure/devops/pipelines/release/environments)找到有关阶段的详细信息。
![屏幕截图显示“任务”下拉列表中选定的阶段。](./media/pipeline-stage-tasks.png)
1. 单击要向其中添加新任务的作业旁的 +。
![屏幕截图显示作业旁的加号按钮。](./media/add-task-to-job.png)
1. 在“添加任务”对话框中，将“Azure 应用程序配置推送”键入到搜索框并选中它 。
1. 配置任务内的必要参数，以将配置文件中的键值推送到应用配置存储。 参数的说明在下面的“参数”部分以及每个参数旁边的工具提示中提供。
1. 保存并将一个发布排入队列。 发布日志将显示任务执行过程中遇到的任何故障。

## <a name="parameters"></a>参数

“应用配置推送”任务使用以下参数：

- **Azure 订阅**：包含可用 Azure 服务连接的下拉列表。 若要更新和刷新可用 Azure 服务连接的列表，请按文本框右侧的“刷新 Azure 订阅”按钮。
- 应用配置名称：用于在所选订阅下加载可用配置存储的下拉列表。 若要更新和刷新可用配置存储的列表，请按文本框右侧的“刷新应用配置名称”按钮。
- 配置文件路径：你的配置文件的路径。 “配置文件路径”参数从文件存储库的根目录开始。 可以浏览生成项目以选择一个配置文件。 （文本框右侧的 `...` 按钮）。 支持的文件格式有：yaml、json 和 properties。 下面是格式为 json 的示例配置文件。
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- 分隔符：用于平展 .json 和 .yml 文件的分隔符。
- **深度**：要将 .json 和 .yml 文件平展到的深度。
- 前缀：一个字符串，它会追加到向应用配置存储推送的每个键的开头。
- **标签**：一个字符串，它会作为标签添加到应用配置存储中的每个键值。
- 内容类型：一个字符串，它会作为内容类型添加到应用配置存储中的每个键值。
- **标记**：`{"tag1":"val1", "tag2":"val2"}` 格式的 JSON 对象，它定义的标记会添加到向应用配置存储推送的每个键值。
- 删除存储中具有指定前缀和标签的所有其他键值：默认值为“未选中”。
  - 已选中：推送配置文件中的新键值之前，在应用配置存储中删除所有与指定前缀和标签匹配的键值。
  - 未选中：将配置文件中的所有键值推送到应用配置存储，并将应用配置存储中的所有其他内容保持不变。



## <a name="troubleshooting"></a>疑难解答

如果发生意外错误，可以通过将管道变量 `system.debug` 设置为 `true` 来启用调试日志。

## <a name="faq"></a>FAQ

**如何上传多个配置文件？**

在同一管道中创建“Azure 应用配置推送”任务的多个实例，以将多个配置文件推送到应用配置存储。

如何使用此任务创建 Key Vault 引用？

若要创建 Key Vault 引用，请将“内容类型”参数设置为“application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8”。 如果并非配置文件中的所有键值都是 Key Vault 引用，请将 Key Vault 引用和标准键值放在单独的配置文件中，并分别推送这些配置文件。

**为什么在尝试将键值推送到配置存储时会收到 409 错误？**

如果任务尝试删除或覆盖在应用配置存储中锁定的键值，将会出现“409 冲突”错误消息。
