---
title: 设置图像标记项目
titleSuffix: Azure Machine Learning
description: 使用数据标记工具创建用于标记图像的项目。 启用 ML 辅助标记，或“人机共生”标记，以协助完成任务。
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: e600c5d6d880a5aba0a714173897660e306aa853
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424552"
---
# <a name="create-an-image-labeling-project-and-export-labels"></a>创建图像标记项目和导出标签

了解如何创建和运行数据标记项目，以标记 Azure 机器学习中的图像。  使用机器学习辅助数据标记或“人机回圈”标记，以协助完成任务。

设置标签以进行分类、对象检测（边界框）或实例分段（多边形）。

还可以使用数据标记工具[创建文本标记项目](how-to-create-text-labeling-projects.md)。

## <a name="image-labeling-capabilities"></a>图像标记功能

Azure 机器学习数据标记是创建、管理和监视数据标记项目的一个中心位置：

- 可以协调数据、标签和团队成员，以有效地管理标记任务。
- 跟踪进度并维护未完成标记任务的队列。
- 启动和停止项目并控制标记进度。
- 检查已标记的数据，并将已标记的数据导出为 Azure 机器学习数据集。

> [!Important]
> 数据映像必须是 Azure Blob 数据存储中提供的文件。 （如果没有现有的数据存储，可以在创建项目过程中上传文件。）

图像数据可以是以下任意类型的文件：“jpg”、“.jpeg”、“.png”、“.jpe”、“.jfif”、“.bmp”、“.tif”、“.tiff”、“.dcm”、“dicom”。 每个文件都是要标记的项。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-an-image-labeling-project"></a>创建图像标记项目

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. 若要创建项目，请选择“添加项目”。 为项目指定适当的名称。 项目名称不能重复使用，即使该项目会在将来被删除也是如此。

1. 选择“图像”以创建图像标签项目。

    :::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="图像标签的标签项目创建":::

    * 若要将一组标签中的单个标签应用到某个图像，请为项目选择“多类图像分类”。
    * 若要将一组标签中的一个或多个标签应用到某个图像，请为项目选择“多标签图像分类”。 例如，可以使用“狗”和“白天”标记狗的照片。
    * 若要将标签和边界框应用到图像中的每个对象，请为项目选择“对象标识(边界框)”。
    * 如果要分配标签并在图像中每个对象的周围绘制多边形，请选择“实例分段(多边形)”。

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
1. 数据集类型设置为文件，图像仅支持文件数据集类型。
1. 选择数据存储。
1. 如果数据位于 Blob 存储中的子文件夹中，请选择“浏览”以选择相应的路径。
    * 将“/**”追加到路径中可以包括所选路径的子文件夹中的所有文件。
    * 追加“* */* .*”可以包括当前容器及其子文件夹中的所有数据。
1. （可选）提供数据集的说明。
1. 选择“**下一步**”。
1. 确认详细信息。 选择“后退”以修改设置，或选择“创建”以创建数据集。

### <a name="create-a-dataset-from-uploaded-data"></a>基于上传的数据创建数据集

若要直接上传数据：

1. 选择“创建数据集” > “从本地文件”。
1. 为数据集指定一个 **名称**。
1. 数据集类型设置为文件，图像仅支持文件数据集类型。
1. （可选）提供数据集的说明。
1. 选择“下一步”  。
1. （可选）选择或创建数据存储。 或者继续默认上传到机器学习工作区的默认 Blob 存储（“workspaceblobstore”）。
1. 选择“浏览”以选择要上传的本地文件或文件夹。
1. 选择“**下一步**”。
1. 确认详细信息。 选择“后退”以修改设置，或选择“创建”以创建数据集。

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a>配置增量刷新

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>添加标签类

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-image-labeling-task"></a>描述图像标记任务

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

对于边界框，重要的问题包括：

* 如何为此任务定义边界框？ 边界框应是完全位于对象的内部还是外部？ 是要尽可能准确地裁剪边界框，还是可以接受一定的间隙？
* 希望标记程序在定义边界框中应用何种程度的缜密性和一致性？
* 如何标记图像中部分显示的对象？ 
* 如何标记其他对象部分遮盖的对象？

>[!NOTE]
> 请务必注意，标记程序可以使用编号键 1-9 选择前 9 个标签。

## <a name="use-ml-assisted-data-labeling"></a>使用 ML 辅助数据标记

在“ML 辅助标记”页中可以触发自动机器学习模型，以加速完成标记任务。 医学图像（“.dcm”）不包含在辅助标记中。

在标记项目的开头，项将按随机顺序排列，以减少潜在的偏差。 但是，数据集中的任何偏差都会反映在训练的模型中。 例如，如果 80% 的项属于单个类，则用于训练模型的大约 80% 的数据将属于该类。 此训练不包括主动学习。

选择“启用 ML 辅助标记”并指定 GPU，以启用由以下两个阶段构成的辅助标记过程：

* 群集功能
* 预先标记

启动辅助标记所需的确切标记数据数目不是固定的。  它可能根据标记项目的不同而有很大的差异。 对于某些项目，在手动标记 300 个项后，有时可能会看到预先标记或聚类任务。 ML 辅助标记使用称为“迁移学习”的技术，该技术使用预先训练的模型来直接启动训练过程。 如果数据集的类类似于预先训练的模型中的类，则只有在手动标记数百个项之后，才能使用预先标签。 如果数据集与用于预先训练模型的数据有很大的不同，此时间可能要长得多。

由于最终的标签仍依赖于标记人员的输入，因此，此技术有时称为“人在回路”标记。

> [!NOTE]
> ML 辅助数据标签不支持[虚拟网络](how-to-network-security-overview.md)后面保护的默认存储帐户。 对于 ML 辅助数据标记，必须使用非默认存储帐户。 可在虚拟网络后面保护非默认存储帐户。

### <a name="clustering"></a>群集功能

提交一定数量的标签后，用于分类的机器学习模型开始将类似的项分组到一起。  这些类似的图像在同一个屏幕上向标记人员显示，以加速完成手动标记。 当标记人员查看包含 4、6 或 9 个图像的网格时，聚类将特别有用。

基于手动标记的数据训练机器学习模型后，该模型将截断至其最后一个完全连接的层。 然后，将在通常称作“嵌入”或“特征化”的流程中通过截断的模型传递未标记的图像。 这会将每个图像嵌入到此模型层定义的某个高维空间。 属于该空间中最近的邻域的图像将用于聚类任务。 

对象检测模型或文本分类不会出现聚类分析阶段。

### <a name="prelabeling"></a>预先标记

提交足够的标签后，将使用分类模型来预测标记。 或者使用对象检测模型来预测边界框。 标记人员现在会看到包含一些页面，其中包含每个项上存在的预测标签。 对于对象检测，还会显示预测框。 接下来的任务是检查这些预测，并更正任何错误标记的图像，然后提交页面。  

基于手动标记的数据训练机器学习模型后，将会基于手动标记的项的测试集评估该模型，以根据不同的置信度阈值确定其准确度。 此评估过程用于确定置信度阈值，如果超过该阈值，则表示模型足够准确，可以显示预先标签。 然后，将会根据未标记的数据评估模型。 预测结果的置信度高于此阈值的项将用于预先标记。

## <a name="initialize-the-image-labeling-project"></a>初始化图像标记项目

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>运行和监视项目

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>仪表板

“仪表板”选项卡将显示标记任务的进度。

:::image type="content" source="./media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="数据标签仪表板":::

进度图显示已标记的项数、已跳过的项数、需要检查的项数以及尚未完成的项数。  将鼠标悬停在该图表上可以查看每个部分中的项数。

中间部分显示尚未分配的任务队列。 当 ML 辅助标记处于关闭状态时，此部分显示要分配的手动任务数。 当 ML 辅助标记处于开启状态时，这还会显示：

* 在队列中包含群集项的任务
* 在队列中包含预标记项的任务

此外，当 ML 辅助标记处于启用状态时，一个小进度栏会显示下一次训练运行的时间。  “试验”部分提供每个机器学习运行的链接。

* 训练 - 训练模型以预测标签
* 验证 - 确定此模型的预测是否将用于预标记项 
* 推理 - 新项的预测运行
* 特征化 - 群集项（仅适用于图像分类项目）

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
* 编辑 ML 辅助标记的详细信息，包括启用/禁用

### <a name="access-for-labelers"></a>标记人员的访问权限

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>将新标签类添加到项目

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>导出标签

使用标记项目的“项目详细信息”页上的“导出”按钮。  随时可以导出标签数据以进行机器学习试验。 

* 图像标签可以导出为：
    * COCO 格式。COCO 文件是在 Azure 机器学习工作区的默认 Blob 存储中创建的，该存储位于 [Labeling/export/coco](http://cocodataset.org/#format-data) 内的某个文件夹中。 
    * [具有标记的 Azure 机器学习数据集](how-to-use-labeled-dataset.md)。 

在机器学习的“数据集”部分访问导出的 Azure 机器学习数据集。 数据集详细信息页还提供了演示如何从 Python 访问标签的示例代码。

![导出的数据集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>疑难解答

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]

### <a name="object-detection-troubleshooting"></a>物体检测疑难解答

|问题  |解决方法  |
|---------|---------|
|在为对象检测提供标记时按 Esc 键会在左上角创建大小为零的标签。 在此状态下提交标签会失败。     |   单击标签旁边的打叉标记来删除该标签。  |

## <a name="next-steps"></a>后续步骤

* [教程：创建第一个图像分类标记项目](tutorial-labeling.md)。
* [如何标记图像](how-to-label-data.md)
