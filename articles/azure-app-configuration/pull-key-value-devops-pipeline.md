---
title: 使用 Azure Pipelines 从应用程序配置中拉取设置
description: 了解如何使用 Azure Pipelines 从应用程序配置存储中拉取键值
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 55a17a70db3f23b169da90c746cd41f24273262e
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666554"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>使用 Azure Pipelines 从应用程序配置中拉取设置

[Azure 应用程序配置](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task)任务将从应用程序配置存储中拉取键值，并将其设置为 Azure 管道变量，这些变量可由后续任务使用。 此任务是对 [Azure 应用程序配置推送](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)任务的补充，后者可将配置文件中的键值推送到应用程序配置存储中。 有关详细信息，请参阅[使用 Azure Pipelines 将设置推送到应用程序配置](push-kv-devops-pipeline.md)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 应用程序配置存储 - 在 [Azure 门户](https://portal.azure.com)中免费创建一个。
- Azure DevOps 项目 - [免费创建一个](https://go.microsoft.com/fwlink/?LinkId=2014881)
- “Azure 应用程序配置”任务 - 从 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.) 免费下载。  

## <a name="create-a-service-connection"></a>创建服务连接

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>添加角色分配

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>在生成中使用

本部分介绍如何在 Azure DevOps 生成管道中使用“Azure 应用程序配置”任务。

1. 通过单击“管道” > “管道”，导航到“生成管道”页。 有关生成管道的文档，请参阅[创建你的第一个管道](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser)。
      - 如果要创建新的“生成管道”，则在该过程的最后一步，在“检查”选项卡上，选择管道右侧的“显示助手” 。
      ![屏幕截图显示新管道的“显示辅助”按钮。](./media/new-pipeline-show-assistant.png)
      - 如果使用的是现有的“生成管道”，请单击右上角的“编辑”按钮。
      ![屏幕截图显示现有管道的“编辑”按钮。](./media/existing-pipeline-show-assistant.png)
1. 搜索“Azure 应用程序配置”任务。
![屏幕截图显示添加任务对话框，其中搜索框中有“Azure 应用程序配置”。](./media/add-azure-app-configuration-task.png)
1. 配置任务的必要参数，以便从应用程序配置存储中拉取键值。 参数说明在下面的“参数”部分以及每个参数旁边的工具提示中提供。
      - 将“Azure 订阅”参数设置为你在前面步骤中创建的服务连接的名称。
      - 将“应用程序配置名称”设置为应用程序配置存储的资源名称。
      - 保留其余参数的默认值。
![屏幕截图显示“应用程序配置”任务参数。](./media/azure-app-configuration-parameters.png)
1. 保存并将一个生成排入队列。 生成日志将显示在执行任务期间发生的任何故障。

## <a name="use-in-releases"></a>在发布中使用

本部分介绍如何在 Azure DevOps 发布管道中使用“Azure 应用程序配置”任务。

1. 通过选择“管道” > “发布”，导航到“发布管道”页。 有关发布管道的文档，请参阅[发布管道](/azure/devops/pipelines/release)。
1. 选择现有的发布管道。 如果没有，请单击“新建管道”创建一个新的发布管道。
1. 选择右上角的“编辑”按钮以编辑发布管道。
1. 从“任务”下拉列表中，选择要向其添加任务的“阶段” 。 可在[此处](/azure/devops/pipelines/release/environments)找到有关阶段的详细信息。
![屏幕截图显示“任务”下拉列表中选定的阶段。](./media/pipeline-stage-tasks.png)
1. 单击要向其中添加新任务的作业旁的 +。
![屏幕截图显示作业旁的“+”按钮。](./media/add-task-to-job.png)
1. 搜索“Azure 应用程序配置”任务。
![屏幕截图显示添加任务对话框，其中搜索框中有“Azure 应用程序配置”。](./media/add-azure-app-configuration-task.png)
1. 配置任务内的必要参数，以便从应用程序配置存储中拉取键值。 参数说明在下面的“参数”部分以及每个参数旁边的工具提示中提供。
      - 将“Azure 订阅”参数设置为你在前面步骤中创建的服务连接的名称。
      - 将“应用程序配置名称”设置为应用程序配置存储的资源名称。
      - 保留其余参数的默认值。
1. 保存并将一个发布排入队列。 发布日志将显示任务执行过程中遇到的任何故障。

## <a name="parameters"></a>参数

“Azure 应用程序配置”任务使用以下参数：

- **Azure 订阅**：包含可用 Azure 服务连接的下拉列表。 若要更新和刷新可用 Azure 服务连接的列表，请按文本框右侧的“刷新 Azure 订阅”按钮。
- 应用配置名称：用于在所选订阅下加载可用配置存储的下拉列表。 若要更新和刷新可用配置存储的列表，请按文本框右侧的“刷新应用配置名称”按钮。
- **键筛选器**：该筛选器可用于选择从 Azure 应用程序配置中请求哪些键值。 值 * 将选择所有键值。 有关详细信息，请参阅[查询键值](concept-key-value.md#query-key-values)。
- **标签**：指定从应用程序配置存储中选择键值时应使用的标签。 如果未提供标签，则将检索带有 no 标签的键值。 不允许使用以下字符：, *。
- **剪裁键前缀**：指定在将应用程序配置键设置为变量之前，应从中剪裁的一个或多个前缀。 多个前缀可以用换行符分隔。

## <a name="use-key-values-in-subsequent-tasks"></a>在后续任务中使用键值

从应用程序配置中提取的键值将设置为管道变量，可以作为环境变量进行访问。 环境变量的键是剪裁前缀后从应用程序配置中检索的键值的键（如果指定）。

例如，如果后续任务运行 PowerShell 脚本，则它可能会使用包含“myBuildSetting”键的键值，如下所示：
```powershell
echo "$env:myBuildSetting"
```
值将输出到控制台。

> [!NOTE]
> 将解析应用程序配置中的 Azure Key Vault 引用，并将其设置为[机密变量](/azure/devops/pipelines/process/variables#secret-variables)。 在 Azure 管道中，会从日志中屏蔽机密变量。 它们不会作为环境变量传递到任务中，而是作为输入传递。 

## <a name="troubleshooting"></a>疑难解答

如果发生意外错误，可以通过将管道变量 `system.debug` 设置为 `true` 来启用调试日志。

## <a name="faq"></a>FAQ

**如何根据多个键和标签撰写配置？**

有时可能需要根据多个标签撰写配置，例如 default 和 dev。 可以在一个管道中使用多个应用程序配置任务来实现此方案。 任务在后面步骤中提取的键值将取代前面步骤中的所有值。 在上述示例中，可以使用一个任务来选择具有 default 标签的键值，而第二个任务可以选择具有 dev 标签的键值。 具有 dev 标签的键将替代具有 default 标签的相同键。
