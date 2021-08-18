---
title: 使用 Azure 视频分析器 Visual Studio Code 扩展
description: 本参考文章介绍如何使用 Azure 视频分析器 Visual Studio Code 扩展中的各项功能。
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: cec3b6549b854cf321390b7598d36b5a4b12378b
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604341"
---
# <a name="use-azure-video-analyzer-visual-studio-code-extension"></a>使用 Azure 视频分析器 Visual Studio Code 扩展

Azure 视频分析器是一个平台，用于更轻松地构建视频分析程序，而关联的 Visual Studio Code 扩展是使学习该平台更容易的工具。  本文是帮助读者了解该扩展提供的各种功能的参考性文章。  其中涵盖以下项的基础知识：

* 管道拓扑 – 创建、编辑、删除、查看 JSON
* 实时管道 – 创建、激活、停用、删除、查看 JSON
* 编辑管道拓扑 – 模块、参数、系统变量、连接、验证

如果尚未设置该扩展来连接到边缘设备，请遵循 [Azure 视频分析器 Visual Studio Code 扩展](./create-pipeline-vs-code-extension.md)快速入门进行操作。

## <a name="managing-pipelines-topology"></a>管理管道拓扑

若要创建拓扑，请在左侧的模块下，右键单击 `Pipelines topologies` 并选择 `Create pipeline topology`。  此时会打开一个新的空白拓扑。  然后，可以通过从顶部的 `Try sample topologies` 下拉列表中进行选择来加载其中一个预制的拓扑，也可以自己构建一个。  

完成所有必填区域后，需要使用右上方的 `Save` 保存拓扑。  对于示例拓扑，应预先填充必填字段。  这将使其可用于创建实时管道。

若要编辑现有拓扑，请在左侧的“管道拓扑”下，右键单击该拓扑的名称并选择 `Edit pipeline topology`。

若要删除现有拓扑，请在左侧的“管道拓扑”下，右键单击该拓扑的名称并选择 `Delete pipeline topology`。  首先需要删除实时管道。

如果要查看现有拓扑背后的基础 JSON，请在左侧的“管道拓扑”下，右键单击该拓扑的名称并选择 `Show pipeline topology JSON`。

## <a name="live-pipelines"></a>实时管道

若要创建实时管道，请在左侧的“管道拓扑”下，右键单击该拓扑的名称并选择 `Create live pipeline`。  然后需要填写实时管道名称以及所有必需的参数，然后再继续。  然后可以单击右上方的 `Save` 将其保存为非活动状态，或单击 `Save and activate` 立即启动实时管道。 

若要激活现有实时管道，请在左侧的“管道拓扑”下，右键单击该实时管道的名称并选择 `Activate live pipeline`。

若要停用正在运行的实例，请在左侧的“管道拓扑”下，右键单击该实时管道并选择 `Deactivate live pipeline`。  这不会删除实时管道。

若要删除现有实时管道，请在左侧的“管道拓扑”下，右键单击该实时管道并选择 `Delete live pipeline`。  无法删除活动的实时管道。

如果要查看现有实时管道背后的基础 JSON，请在左侧的“管道拓扑”下，右键单击该实时管道并选择 `Show live pipeline JSON`。

## <a name="editing-a-topology"></a>编辑拓扑 

管道拓扑由各种不同的部分组成。  可以通过阅读[管道](./pipeline.md)概念文档来了解这些部分。本部分介绍界面的各个部分，以帮助你构建或编辑拓扑。

### <a name="modules"></a>模块

左侧提供可用的模块，模块分为源、处理器和接收器。  若要将一个模块添加到拓扑中，请将其拖动到编辑区域。

如果要使用具有相同起始参数的相同类型的另一个模块，可以通过右键单击该模块并选择 `Copy` 来复制它。

如果要从拓扑中删除模块，请右键单击该模块，然后选择 `Delete`。

### <a name="parameters"></a>参数

可以通过在编辑区域中左键单击模块来编辑其属性。  此时将在右侧弹出一个包含所有元素列表的窗格。  必填项标有红色的星号。  信息文本中按每个项的名称提供了简短说明。

由于拓扑实际上是可以从中有效创建多个实时管道的模板，因此需要在创建时填写一些项。  可以使用参数实现此目的。  若要激活此功能，请右键单击特性右侧的三个点，然后选择 `Parameterize`。  此时会打开一个窗口。

在参数化窗口中，可以创建一个新参数，该参数将作为创建实时管道时填写的值，也可以从现有参数中选择一个。  创建新参数需要填写名称和类型。  如果只需要在个别时候更改默认值，可以选择输入默认值。  创建实时管道时，只需要无默认值的参数。  完成后，单击 `Set`。

如果要管理现有参数，可以使用顶部的 `Manage parameters` 选项来实现此目的。  通过随即显示的窗格可以添加新参数，以及编辑或删除现有参数。

### <a name="system-variable"></a>系统变量

创建一系列实时管道时，你可能会希望使用变量来帮助命名文件或输出。  例如，你可能希望使用实时管道名称和日期/时间来命名视频剪辑，以便知道它来自何处及其生成时间。  视频分析器提供了三个系统变量，可以在模块中使用这些变量来提供帮助。

| 系统变量        | 说明                                                  | 示例              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | 采用 ISO8601 文件标准格式的 UTC 日期时间（基本表示形式为 YYYYMMDDThhmmss）。 | 20200222T173200Z     |
| System.TopologyName    | 用户提供的执行管道拓扑的名称。          | IngestAndRecord      |
| System.PipelineName    | 用户提供的执行实时管道的名称。          | camera001            |

### <a name="connections"></a>连接 

创建拓扑时，需要将各种模块连接在一起。  可以通过连接实现。  从一个模块边缘的圆圈拖动到希望数据流向的下一个模块上的圆圈。  这将生成一个连接。

默认情况下，连接会将一个模块的所有数据发送到另一个模块。  如果只想发送某些类型的数据，可以左键单击连接，然后编辑要发送的输出类型。  可选择的数据类型包括视频、音频和应用程序。
