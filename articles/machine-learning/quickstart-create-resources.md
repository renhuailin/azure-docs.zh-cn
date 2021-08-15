---
title: 快速入门：创建工作区资源
titleSuffix: Azure Machine Learning
description: 创建可用于训练机器学习模型的 Azure 机器学习工作区和云资源。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 06/10/2021
adobe-target: true
ms.custom: FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 0d4c85ec1075e3925fa5657783ffce618faaa6ea
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760154"
---
# <a name="quickstart-create-workspace-resources-you-need-to-get-started-with-azure-machine-learning"></a>快速入门：创建 Azure 机器学习入门所需的工作区资源

在本快速入门中，你将创建一个工作区，然后将计算资源添加到该工作区。 然后你就拥有了开始使用 Azure 机器学习所需的所有资源。  

工作区是机器学习活动的顶级资源，为使用 Azure 机器学习时创建的所有项目提供一个集中的查看和管理位置。 计算资源提供预配置的基于云的环境，可用于训练、部署、自动执行、管理和跟踪机器学习模型。


## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-workspace"></a>创建工作区

如果已有工作区，请跳过此部分并继续[创建计算实例](#instance)。

如果还没有工作区，现在请创建一个：

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="create-compute-instance"></a><a name="instance"></a>创建计算实例

可在自己的计算机上安装 Azure 机器学习。  但在本快速入门中，将创建一个在线计算资源，该资源已安装开发环境并准备就绪。  你将使用此在线计算机（一个计算实例），供开发环境在 Python 脚本和 Jupyter 笔记本中编写和运行代码。

创建一个计算实例，以便在教程和快速入门的其余部分使用此开发环境。

1. 如果未选择上一部分中的“转到工作区”，请立即登录 [Azure 机器学习工作室](https://ml.azure.com)，并选择你的工作区。
1. 在左侧的“管理”下，选择“计算” 。
1. 选择“+ 新建”以创建新的计算实例。
1. 保留第一页上的所有默认值，然后选择“下一步”。
1. 提供名称并选择“创建”。
 
大约两分钟后，你将看到计算实例的状态从“正在创建”更改为“正在运行” 。  现已准备就绪。  

## <a name="create-compute-clusters"></a><a name="cluster"></a>创建计算群集

接下来将创建一个计算群集。  通过群集，可以在云中的 CPU 或 GPU 计算节点群集之间分布训练或批量推理过程。

创建一个将在零到四个节点之间自动缩放的计算群集：

1. 仍在“计算”部分的顶部选项卡中，选择“计算群集” 。
1. 选择“+ 新建”以创建新的计算群集。
1. 保留第一页上的所有默认值，然后选择“下一步”。
1. 将群集命名为“cpu-cluster”。  如果此名称已存在，请将你的姓名首字母缩写添加到此名称中，使其具有唯一性。
1. 将“最小节点数”保留为 0。
1. 将“最大节点数”更改为 4（如果可能）。  上限可能更小，具体取决于你的设置。
1. 将“纵向缩减前的空闲秒数”更改为 2400。
1. 将剩余的字段保留默认设置，然后选择“创建”。

在一分钟内，群集的“状态”将从“正在创建”更改为“已成功” 。  此列表显示预配的计算群集，以及空闲节点、忙碌节点和未预配节点的数量。  由于你尚未使用群集，因此所有节点当前都未预配。 

> [!NOTE]
> 该群集在创建之后将会预配 0 个节点。 因此在你提交作业之前，该群集不会产生成本。 此群集在空闲 2,400 秒（40 分钟）之后将会纵向缩减。  这将为你留一些时间在一些教程中使用它（如果你需要这样做的话），而无需等待它进行纵向扩展。

## <a name="quick-tour-of-the-studio"></a><a name="studio"></a>工作室快速导览

工作室是 Azure 机器学习的 Web 门户。 此门户将无代码和代码优先体验结合起来，打造包容的数据科学平台。

查看左侧导航栏上的工作室部分：

* 工作室的“创建者”部分包含多个创建机器学习模型的方法。  方法：

    * 通过“笔记本”部分，可以创建 Jupyter 笔记本、复制示例笔记本以及运行笔记本和 Python 脚本。
    * 通过“自动化 ML”步骤，可以创建机器学习模型，而无需编写代码。
    * 设计器提供一种使用预生成的模块生成模型的拖放方式。

* 工作室的“资产”部分可帮助你跟踪在运行作业时创建的资产。  如果你有新的工作区，则这些部分中还没有任何内容。

* 你已经使用工作室的“管理”部分来创建计算资源。  通过此部分，还可以创建和管理链接到工作区的数据和外部服务。  

## <a name="clean-up-resources"></a><a name="clean-up"></a>清理资源

如果打算继续学习下一个教程，请跳到[后续步骤](#next-steps)。

### <a name="stop-compute-instance"></a>停止计算实例

如果不打算现在使用它，请停止计算实例：

1. 在工作室的左侧，选择“计算”。
1. 在顶部选项卡中，选择“计算实例”
1. 在列表中选择该计算实例。
1. 在顶部工具栏中，选择“停止”。

### <a name="delete-all-resources"></a>删除所有资源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

现在有一个 Azure 机器学习工作区，其中包含：

- 用于开发环境的计算实例。
- 用于提交训练运行的计算群集。

使用这些资源来详细了解 Azure 机器学习，并使用 Python 脚本训练模型。

> [!div class="nextstepaction"]
> [使用 Python 脚本了解详细信息](tutorial-1st-experiment-hello-world.md)
>
