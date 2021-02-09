---
title: 使用 Azure Pipelines 从应用程序配置中拉取设置
description: 了解如何使用 Azure Pipelines 从应用程序配置存储中拉取键值
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 7bd163781203a277f4c9d6866a156c11e4d5d520
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979566"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>使用 Azure Pipelines 从应用程序配置中拉取设置

[Azure 应用程序配置](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task)任务将从应用程序配置存储中拉取键值，并将其设置为 Azure 管道变量，这些变量可由后续任务使用。 此任务是对 [Azure 应用程序配置推送](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)任务的补充，后者可将配置文件中的键值推送到应用程序配置存储中。 有关详细信息，请参阅[使用 Azure Pipelines 将设置推送到应用程序配置](push-kv-devops-pipeline.md)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 应用程序配置存储 - 在 [Azure 门户](https://portal.azure.com)中免费创建一个。
- Azure DevOps 项目 - [免费创建一个](https://go.microsoft.com/fwlink/?LinkId=2014881)
- “Azure 应用程序配置”任务 - 从 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.) 免费下载。  

## <a name="create-a-service-connection"></a>创建服务连接

通过[服务连接](/azure/devops/pipelines/library/service-endpoints)，可以从 Azure DevOps 项目访问 Azure 订阅中的资源。

1. 在 Azure DevOps 中，访问包含目标管道的项目，并打开左下方的“项目设置”。
1. 在“管道”下，选择“服务连接” 。
1. 如果你没有任何现有的服务连接，请单击屏幕中间的“创建服务连接”按钮。 否则，请单击页面右上方的“新建服务连接”。
1. 选择“Azure 资源管理器”。
1. 选择“服务主体(自动)”。
1. 填写你的订阅和资源。 为服务连接命名。

创建服务连接后，请查找分配给它的服务主体的名称。 在下一步中，你将向此服务主体添加新的角色分配。

1. 访问“项目设置” > “服务连接”。
1. 选择在上一部分创建的服务连接。
1. 选择“管理服务主体”。
1. 请记下列出的显示名称。

## <a name="add-role-assignment"></a>添加角色分配

向任务中使用的服务连接分配正确的应用程序配置角色，以便任务可以访问应用程序配置存储。

1. 导航到目标应用配置存储。 有关设置应用程序配置存储的演练，请参阅 Azure 应用程序配置快速入门之一中的[创建应用程序配置存储](./quickstart-dotnet-core-app.md#create-an-app-configuration-store)。
1. 在左侧，选择“访问控制(IAM)”。
1. 在顶部，选择“+ 添加”，然后选取“添加角色分配” 。
1. 在“角色”下，选择“应用程序配置数据读取者” 。 此角色允许任务在应用程序配置存储中进行读取。 
1. 选择与在上一部分创建的服务连接关联的服务主体。

> [!NOTE]
> 若要解析应用程序配置中的 Azure Key Vault 引用，还必须授予服务连接读取引用的 Azure Key Vault 中机密的权限。
  
## <a name="use-in-builds"></a>在生成中使用

本部分介绍如何在 Azure DevOps 生成管道中使用“Azure 应用程序配置”任务。

1. 通过单击“管道” > “管道”，导航到“生成管道”页。 有关生成管道的文档，请参阅[创建你的第一个管道](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser)。
      - 如果要创建新的生成管道，请单击“新建管道”，然后为管道选择存储库。 选择管道右侧的“显示助手”，然后搜索“Azure 应用程序配置”任务。
      - 如果使用现有生成管道，请选择“编辑”以编辑管道。 在“任务”选项卡中，搜索“Azure 应用程序配置”任务。
1. 配置任务的必要参数，以便从应用程序配置存储中拉取键值。 参数说明在下面的“参数”部分以及每个参数旁边的工具提示中提供。
      - 将“Azure 订阅”参数设置为你在前面步骤中创建的服务连接的名称。
      - 将“应用程序配置名称”设置为应用程序配置存储的资源名称。
      - 保留其余参数的默认值。
1. 保存并将一个生成排入队列。 生成日志将显示在执行任务期间发生的任何故障。

## <a name="use-in-releases"></a>在发布中使用

本部分介绍如何在 Azure DevOps 发布管道中使用“Azure 应用程序配置”任务。

1. 通过选择“管道” > “发布”，导航到“发布管道”页。 有关发布管道的文档，请参阅[发布管道](/azure/devops/pipelines/release)。
1. 选择现有的发布管道。 如果没有，请单击“新建管道”创建一个新的发布管道。
1. 选择右上角的“编辑”按钮以编辑发布管道。
1. 选择“阶段”以添加任务。 有关阶段的详细信息，请参阅[添加阶段、依赖项和条件](/azure/devops/pipelines/release/environments)。
1. 为“在代理上运行”单击 **+** ，然后在“添加任务”选项卡下添加“Azure 应用程序配置”任务。
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
