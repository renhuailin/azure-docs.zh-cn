---
title: 使用 Azure Pipelines 将设置推送到应用配置
description: 了解如何使用 Azure Pipelines 将键值推送到应用配置存储
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkemper
ms.openlocfilehash: c5e0cc3eb29fb612460b16d8de9dee62949b5bd2
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979600"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>使用 Azure Pipelines 将设置推送到应用配置

[Azure 应用配置推送](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)任务可将配置文件中的键值推送到你的应用配置存储中。 此任务在管道内实现了全循环功能，因为你现在可以从应用配置存储中拉取设置，还可以将设置推送到应用配置存储。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 应用配置资源 - 在 [Azure 门户](https://portal.azure.com)中免费创建一个。
- Azure DevOps 项目 - [免费创建一个](https://go.microsoft.com/fwlink/?LinkId=2014881)
- “Azure 应用配置推送”任务 - 从 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) 免费下载。

## <a name="create-a-service-connection"></a>创建服务连接

通过[服务连接](/azure/devops/pipelines/library/service-endpoints)，可以从 Azure DevOps 项目访问 Azure 订阅中的资源。

1. 在 Azure DevOps 中，访问包含目标管道的项目，并打开左下方的“项目设置”。
1. 在“管道”下选择“服务连接”，并在右上方选择“新建服务连接”。
1. 选择“Azure 资源管理器”。
1. 选择“服务主体(自动)”。
1. 填写你的订阅和资源。 为服务连接命名。

创建服务连接后，请查找分配给它的服务主体的名称。 在下一步中，你将向此服务主体添加新的角色分配。

1. 访问“项目设置” > “服务连接”。
1. 选择在上一部分创建的服务连接。
1. 选择“管理服务主体”。
1. 请记下列出的显示名称。

## <a name="add-role-assignment"></a>添加角色分配

向任务中使用的凭据分配正确的应用配置角色分配，以便任务可以访问应用配置存储。

1. 导航到目标应用配置存储。 
1. 在左侧，选择“访问控制(IAM)”。
1. 在顶部，选择“+ 添加”，然后选取“添加角色分配” 。
1. 在“角色”下，选择“应用配置数据所有者” 。 此角色允许任务在应用配置存储中进行读取和写入。 
1. 选择与在上一部分创建的服务连接关联的服务主体。
  
## <a name="use-in-builds"></a>在生成中使用

本部分介绍如何在 Azure DevOps 生成管道中使用“Azure 应用配置推送”任务。

1. 通过单击“管道” > “管道”，导航到“生成管道”页。 可在[此处](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)找到有关生成管道的文档。
      - 如果要创建新的生成管道，请在管道右侧选择“显示助手”，然后搜索“Azure 应用配置推送”任务。
      - 如果要使用现有的生成管道，请在编辑管道时导航到“任务”选项卡，然后搜索“Azure 应用配置推送”任务。
2. 配置任务的必要参数，以将配置文件中的键值推送到应用配置存储。 “配置文件路径”参数从文件存储库的根目录开始。
3. 保存并将一个生成排入队列。 生成日志将显示在执行任务期间发生的任何故障。

## <a name="use-in-releases"></a>在发布中使用

本部分介绍如何在 Azure DevOps 发布管道中使用“Azure 应用配置推送”任务。

1. 通过选择“管道” > “发布”，导航到“发布管道”页。 可在[此处](/azure/devops/pipelines/release)找到有关发布管道的文档。
1. 选择现有的发布管道。 如果没有现有的发布管道，请选择“+ 新建”创建一个新的发布管道。
1. 选择右上角的“编辑”按钮以编辑发布管道。
1. 选择“阶段”以添加任务。 可在[此处](/azure/devops/pipelines/release/environments)找到有关阶段的详细信息。
1. 选择与该作业对应的 **+** ，然后在“部署”选项卡下添加“Azure 应用配置推送”任务。 
1. 配置任务内的必要参数，以将配置文件中的键值推送到应用配置存储。 参数的说明在下面的“参数”部分以及每个参数旁边的工具提示中提供。
1. 保存并将一个发布排入队列。 发布日志将显示任务执行过程中遇到的任何故障。

## <a name="parameters"></a>参数

“应用配置推送”任务使用以下参数：

- **Azure 订阅**：包含可用 Azure 服务连接的下拉列表。 若要更新和刷新可用 Azure 服务连接的列表，请按文本框右侧的“刷新 Azure 订阅”按钮。
- 应用配置名称：用于在所选订阅下加载可用配置存储的下拉列表。 若要更新和刷新可用配置存储的列表，请按文本框右侧的“刷新应用配置名称”按钮。
- 配置文件路径：你的配置文件的路径。 可以浏览生成项目以选择一个配置文件。 （文本框右侧的 `...` 按钮）。
- 分隔符：用于平展 .json 和 .yml 文件的分隔符。
- **深度**：要将 .json 和 .yml 文件平展到的深度。
- 前缀：一个字符串，它会追加到向应用配置存储推送的每个键的开头。
- **标签**：一个字符串，它会作为标签添加到应用配置存储中的每个键值。
- 内容类型：一个字符串，它会作为内容类型添加到应用配置存储中的每个键值。
- **标记**：`{"tag1":"val1", "tag2":"val2"}` 格式的 JSON 对象，它定义的标记会添加到向应用配置存储推送的每个键值。
- 删除存储中具有指定前缀和标签的所有其他键值：默认值为“未选中”。
  - 已选中：推送配置文件中的新键值之前，在应用配置存储中删除所有与指定前缀和标签匹配的键值。
  - 未选中：将配置文件中的所有键值推送到应用配置存储，并将应用配置存储中的所有其他内容保持不变。

填写所需参数后，运行管道。 指定的配置文件中的所有键值均将上传到“应用配置”。

## <a name="troubleshooting"></a>疑难解答

如果发生意外错误，可以通过将管道变量 `system.debug` 设置为 `true` 来启用调试日志。

## <a name="faq"></a>FAQ

**如何上传多个配置文件？**

在同一管道中创建“Azure 应用配置推送”任务的多个实例，以将多个配置文件推送到应用配置存储。

**为什么在尝试将键值推送到配置存储时会收到 409 错误？**

如果任务尝试删除或覆盖在应用配置存储中锁定的键值，将会出现“409 冲突”错误消息。
