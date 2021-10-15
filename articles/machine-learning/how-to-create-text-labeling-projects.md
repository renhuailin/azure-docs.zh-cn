---
title: 设置文本标记项目
titleSuffix: Azure Machine Learning
description: 使用数据标记工具创建用于标记文本的项目。 指定要应用于每个文本段的单个或多个标签。
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: e4d227cf215ab5d37af8a25e6e44e811f739f25b
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423702"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>创建文本标记项目和导出标签（预览版）

了解如何创建和运行数据标记项目以标记 Azure 机器学习中的文本数据。  指定要应用于每个文本段的单个或多个标签。

还可使用数据标记工具[创建图像标记项目](how-to-create-image-labeling-projects.md)。

> [!IMPORTANT]
> 文本标签目前提供公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="text-labeling-capabilities"></a>文本标记功能

Azure 机器学习数据标记是创建、管理和监视数据标记项目的一个中心位置：

- 可以协调数据、标签和团队成员，以有效地管理标记任务。 
- 跟踪进度并维护未完成标记任务的队列。
- 启动和停止项目并控制标记进度。
- 检查已标记的数据，并将已标记的数据导出为 Azure 机器学习数据集。

> [!Important]
> 必须在 Azure Blob 数据存储中提供文本数据。 （如果没有现有的数据存储，可以在创建项目过程中上传文件。）

文本数据可以是“.txt”或“.csv”文件。

* 对于“.txt”文件，每个文件表示一个要标记的项。
* 对于“.csv”文件，每一行表示一个呈现给标记工具的项。  可在标记该行时显示一个或多个要使用的列。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-a-text-labeling-project"></a>创建文本标记项目

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. 若要创建项目，请选择“添加项目”。 为项目指定适当的名称。 项目名称不能重复使用，即使该项目会在将来被删除也是如此。

1. 选择“文本”以创建文本标签项目。

    :::image type="content" source="media/how-to-create-labeling-projects/text-labeling-creation-wizard.png" alt-text="文本标签的标签项目创建":::

    * 若要仅将单个标签从一组标签应用到每段文本，请选择项目的“文本分类多类(预览)”。
    * 若要将一个或多个标签从一组标签应用到每段文本，请选择项目的“文本分类多类(预览)”。

1. 准备好继续时，选择“下一步”。

## <a name="add-workforce-optional"></a>添加工作人员（可选）

[!INCLUDE [outsource](../../includes/machine-learning-data-labeling-outsource.md)]

## <a name="specify-the-data-to-label"></a>指定要标记的数据

如果已创建包含数据的数据集，请从“选择现有数据集”下拉列表中选择该数据集。 或者，选择“创建数据集”以使用现有的 Azure 数据存储或上传本地文件。

> [!NOTE]
> 一个项目最多包含 500,000 个文件。  如果数据集中包含的图像超过此限制，将只加载前 500,000 个文件。  

### <a name="create-a-dataset-from-an-azure-datastore"></a>从 Azure 数据存储创建数据集

在许多情况下，上传本地文件就可以了。 但是，使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)可以更快、更可靠地传输大量数据。 建议将存储资源管理器用作移动文件的默认方式。

若要基于已存储在 Azure Blob 存储中的数据创建数据集：

1. 选择“创建数据集” > “从数据存储”。
1. 为数据集指定一个 **名称**。
1. 选择“数据集类型”：
    * 如果使用的是其中每行都包含一个响应的 .csv 文件，请选择“表格”。
    * 如果对每个响应使用单独的 .txt 文件，请选择“文件”。
1. （可选）提供数据集的说明。
1. 选择“下一步”  。
1. 选择数据存储。
1. 如果数据位于 Blob 存储中的子文件夹中，请选择“浏览”以选择相应的路径。
    * 将“/**”追加到路径中可以包括所选路径的子文件夹中的所有文件。
    * 追加“* */* .*”可以包括当前容器及其子文件夹中的所有数据。
1. 选择“**下一步**”。
1. 确认详细信息。 选择“后退”以修改设置，或选择“创建”以创建数据集。

### <a name="create-a-dataset-from-uploaded-data"></a>基于上传的数据创建数据集

若要直接上传数据：

1. 选择“创建数据集” > “从本地文件”。
1. 为数据集指定一个 **名称**。
1. 选择“数据集类型”。
    * 如果使用 .csv 文件，其中每行都是一个响应，请选择“表格”。
    * 如果对每个响应使用单独的 .txt 文件，请选择“文件”。
1. （可选）提供数据集的说明。
1. 选择“下一步”
1. （可选）选择或创建数据存储。 或者继续默认上传到机器学习工作区的默认 Blob 存储（“workspaceblobstore”）。
1. 选择“上传”以选择要上传的本地文件或文件夹。
1. 选择“下一步”  。
1. 如果正在上传 .csv 文件：
    * 确认设置和预览版，然后选择“下一步”。
    * 在对该行进行分类时，请包含要标记工具看到的所有文本列。
    * 选择“**下一步**”。
1.  确认详细信息。 选择“后退”以修改设置，或选择“创建”以创建数据集。


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a>配置增量刷新

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>添加标签类

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>说明文本标记任务

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> 请务必注意，标记程序可以使用编号键 1-9 选择前 9 个标签。


## <a name="initialize-the-text-labeling-project"></a>初始化文本标记项目

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>运行和监视项目

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>仪表板


“仪表板”选项卡将显示标记任务的进度。

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="文本数据标记仪表板":::


进度图显示已标记、已跳过、需要检查或尚未完成的项数。  将鼠标悬停在该图表上可以查看每个部分中的项数。

中间部分显示尚未分配的任务队列。 


右侧是已完成的任务的标签分布。  请记住，在某些项目类型中，一个项可以具有多个标签，在这种情况下，总标签数可以大于总项数。

### <a name="data-tab"></a>“数据”选项卡

在“数据”选项卡上，可以查看数据集并检查已标记的数据。 滚动已标记的数据以查看标签。 如果发现数据标记不正确，可以选择该数据，然后选择“拒绝”，这会删除标签，并将数据放回到未标记队列中。

### <a name="details-tab"></a>“详细信息”选项卡

查看项目的详细信息。  在此选项卡中，可以：

* 查看项目详细信息和输入数据集
* 启用增量刷新
* 查看用于在项目中存储已标记的输出的存储容器详细信息
* 将标签添加到项目
* 编辑为标签提供的说明

### <a name="access-for-labelers"></a>标记人员的访问权限

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>将新标签类添加到项目

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>导出标签
 
使用标记项目的“项目详细信息”页上的“导出”按钮。  随时可以导出标签数据以进行机器学习试验。

可以导出以下内容：

* CSV 文件。 CSV 文件是在 Azure 机器学习工作区的默认 Blob 存储中创建的，该存储位于 Labeling/export/csv 内的某个文件夹中。 
* [具有标记的 Azure 机器学习数据集](how-to-use-labeled-dataset.md)。 

在机器学习的“数据集”部分访问导出的 Azure 机器学习数据集。 数据集详细信息页还提供了演示如何从 Python 访问标签的示例代码。

![导出的数据集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>疑难解答

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]


## <a name="next-steps"></a>后续步骤

* [如何标记文本](how-to-label-data.md#label-text)

