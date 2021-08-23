---
title: 机器学习工作室（经典）：应用程序生命周期管理 - Azure
description: 在机器学习工作室（经典）中应用应用程序生命周期管理最佳做法
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 16d5765d03c02ac733c3d3dc2dbc8ac117caea35
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583421"
---
# <a name="application-lifecycle-management-in-machine-learning-studio-classic"></a>机器学习工作室（经典）中的应用程序生命周期管理

**适用对象：** ![适用于.](../../../includes/media/aml-applies-to-skus/yes.png)机器学习工作室（经典）   ![不适用于.](../../../includes/media/aml-applies-to-skus/no.png)[Azure 机器学习](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


机器学习工作室（经典）是一个在 Azure 云平台中运行的工具，用于开发机器学习试验。 它类似于将 Visual Studio IDE 和可缩放云服务合并到单个平台。 可以将标准的应用程序生命周期管理 (ALM) 实践（从各种资产的版本控制到自动执行和部署）合并到机器学习工作室（经典）中。 本文介绍一些选项和方法。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>实验的版本控制
有两种用于控制实验版本的建议方法。 可以依赖内置的运行历史记录，或以 JSON 格式导出实验以在外部管理它。 每种方法各有利弊。

### <a name="experiment-snapshots-using-run-history"></a>使用运行历史记录的实验快照
在机器学习工作室（经典）学习试验的执行模型中，每次单击试验编辑器中的“运行”时，都会将该试验的不可变快照提交到作业计划程序。 要查看此快照列表，单击实验编辑器视图中命令栏上的“运行历史记录”按钮。

![“运行历史记录”按钮](./media/version-control/runhistory.png)

然后，按以下方式操作即可在锁定模式下打开快照：在提交试验供运行并创建快照时，单击该试验的名称。 请注意，仅列表中表示当前实验的第一项处于“可编辑”状态。 另请注意，每个快照同样可以处于各种状态，包括完成（部分运行）、失败、失败（部分运行）或草稿。

![“运行历史记录”列表](./media/version-control/runhistorylist.png)

打开后，可以将快照实验另存为新的实验，然后修改它。 如果实验快照包含训练模型、转换、数据集等资产，由于版本已更新，该快照保留的引用是捕获快照时的原始版本。 如果已将锁定快照另存为新试验，机器学习工作室（经典）会检测是否存在这些资产的更新版本，并会在新试验中自动更新资产。

如果删除实验，则会删除该实验的所有快照。

### <a name="exportimport-experiment-in-json-format"></a>采用 JSON 格式导出/导入实验
每次提交要运行的实验时，运行历史记录快照都会在机器学习工作室（经典）中保留该实验的不可变版本。 也可保存实验的本地副本并将其签入最常用的源代码管理系统（例如 Team Foundation Server），然后通过该本地文件重新创建实验。 可以使用 [机器学习工作室（经典）PowerShell](https://aka.ms/amlps) commandlet [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) 和 [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) 来实现导出/导入操作。

JSON 文件是实验图的文本表示形式，可能包含对工作区中数据集或训练模型等资产的引用。 它不包含资产的序列化版本。 如果尝试将 JSON 文档导回到工作区，引用的资产中必须已经存有实验中引用的相同资产 ID， 否则将无法访问导入的试验。

## <a name="versioning-trained-model"></a>训练模型的版本控制
机器学习工作室（经典）中的已训练模型将序列化为一种称为 iLearner 文件 (`.iLearner`) 的格式，并存储在与工作区关联的 Azure Blob 存储帐户中。 获取 iLearner 文件副本的一种方法是重新训练 API。 [本文](./retrain-machine-learning-model.md)介绍如何对 API 重新训练。 概略性步骤：

1. 设置训练实验。
2. 将 Web 服务输出端口添加到“训练”模块或生成训练模型（如调整模型超参数或创建 R 模型）的模块。
3. 运行训练实验，然后将其部署为模型训练 Web 服务。
4. 调用对 Web 服务训练的 BES 终结点，并指定所需的 iLearner 文件名以及将存储它的 Blob 存储帐户位置。
5. BES 调用完成后，即可获得生成的 iLearner 文件。

检索 iLearner 文件的另一种方法是通过 PowerShell commandlet [Download-AmlExperimentNodeOutput](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput)。 如果只想获取 iLearner 文件的副本，不需要以编程方式重新训练模型，此方法可能比较容易。

有了 iLearner 文件（包含训练的模型）以后，即可采用自己的版本控制策略。 该策略就像将前缀/后缀作为命名约定应用一样简单，只需将 iLearner 文件保留在 Blob 存储中即可，也可将其复制/导入到版本控制系统中。

之后，保存的 iLearner 文件可用于通过部署的 Web 服务进行评分。

## <a name="versioning-web-service"></a>Web 服务的版本控制
可以从机器学习工作室（经典）试验部署两种类型的 Web 服务。 经典 Web 服务与实验以及工作区紧密耦合。 新的 Web 服务使用 Azure 资源管理器框架，不再与原始实验或工作区耦合。

### <a name="classic-web-service"></a>经典 Web 服务
若要对经典 Web 服务进行版本控制，可以利用 Web 服务终结点构造。 典型工作流如下所示：

1. 从预测实验，部署新的经典 Web 服务（包含默认终结点）。
2. 创建名为 ep2 的新终结点（显示实验/训练模型的当前版本）。
3. 返回并更新预测实验和训练模型。
4. 重新部署预测实验，这会更新默认终结点。 但此操作不会更改 ep2。
5. 创建名为 ep3 的另一终结点，以便公开新版实验和训练模型。
6. 必要时返回到步骤 3。

随着时间的推移，就会在同一 Web 服务中创建多个终结点。 每一个终结点都代表实验的一个时间点副本，其中包含训练模型的时间点版本。 可以使用外部逻辑确定要调用哪一个终结点，这实际上意味着为评分运行选择某个版本的训练模型。

还可以创建许多相同的 Web 服务终结点，然后将不同版本的 iLearner 文件修补到要实现类似效果的终结点。 [本文](create-models-and-endpoints-with-powershell.md)更详细地介绍了如何完成此操作。

### <a name="new-web-service"></a>新的 Web 服务
如果创建新的基于 Azure 资源管理器的 Web 服务，终结点构造不再可用。 相反，可以通过以下两种方法生成 JSON 格式的 Web 服务定义 (WSD) 文件：使用 [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell commandlet 从预测实验，或使用 [*Export-AzMlWebservice*](/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell commandlet 从已部署的基于 Resource Manager 的 Web 服务。

有了导出的 WSD 文件并可对其进行版本控制以后，还可以将 WSD 部署为不同 Azure 区域中不同 Web 服务计划中的新 Web 服务。 只需确保提供正确的存储帐户配置以及新的 Web 服务计划 ID。 要修补其他 iLearner 文件，可以修改 WSD 文件、更新训练模型的位置引用，然后将其部署为新的 Web 服务。

## <a name="automate-experiment-execution-and-deployment"></a>自动化实验执行和部署
ALM 的一个重要方面是能够自动化应用程序的执行和部署过程。 在机器学习工作室（经典）中，可以使用 [PowerShell 模块](https://aka.ms/amlps)完成此操作。 下面举例说明与使用[机器学习工作室（经典）PowerShell 模块](https://aka.ms/amlps)完成的标准 ALM 自动化执行/部署过程有关的端到端步骤。 每个步骤都链接到一个或多个用于完成该步骤的 PowerShell cmdlet。

1. [上传数据集](https://github.com/hning86/azuremlps#upload-amldataset)。
2. 将训练实验从[工作区](https://github.com/hning86/azuremlps#copy-amlexperiment)或[库](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)复制到工作区，或者[导入](https://github.com/hning86/azuremlps#import-amlexperimentgraph)从本地磁盘中[导出](https://github.com/hning86/azuremlps#export-amlexperimentgraph)的实验。
3. 在训练实验中[更新数据集](https://github.com/hning86/azuremlps#update-amlexperimentuserasset)。
4. [运行训练实验](https://github.com/hning86/azuremlps#start-amlexperiment)。
5. [提升训练模型](https://github.com/hning86/azuremlps#promote-amltrainedmodel)。
6. [复制预测实验](https://github.com/hning86/azuremlps#copy-amlexperiment)到工作区。
7. 在预测实验中[更新训练模型](https://github.com/hning86/azuremlps#update-amlexperimentuserasset)。
8. [运行预测实验](https://github.com/hning86/azuremlps#start-amlexperiment)。
9. 从预测实验[部署 Web 服务](https://github.com/hning86/azuremlps#new-amlwebservice)。
10. 测试 Web 服务 [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) 或 [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) 终结点。

## <a name="next-steps"></a>后续步骤
* 下载[机器学习工作室（经典）PowerShell](https://aka.ms/amlps) 模块，开始自动执行 ALM 任务。
* 了解如何通过 PowerShell 和重新训练 API，[只使用单个实验创建和管理大量 ML 模型](create-models-and-endpoints-with-powershell.md)。
* 详细了解如何[部署机器学习 Web 服务](deploy-a-machine-learning-web-service.md)。