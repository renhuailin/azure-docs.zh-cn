---
title: 快速入门：表单识别工作室 | 预览版
titleSuffix: Azure Applied AI Services
description: 使用表单识别器工作室（预览版）进行表单和文档处理、数据提取和分析
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/14/2021
ms.author: sajagtap
ms.openlocfilehash: 032669d298ef0aeda3663c303f5575e24cd02354
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809761"
---
# <a name="get-started-form-recognizer-studio--preview"></a>入门：表单识别器工作室 | 预览版

>[!NOTE]
> 表单识别器工作室当前为公共预览版。 某些功能可能不受支持或者受限。 

[表单识别器工作室预览版](https://formrecognizer.appliedai.azure.com/)是一种联机工具，用于在应用程序中通过表单识别器服务直观地浏览、了解和集成功能。 请先查看已使用示例文档或你自己的文档预先训练过的模型。 通过 [Python SDK 预览版](try-v3-python-sdk.md)和其他快速入门，创建项目来生成自定义表单模型，并在应用程序中引用这些模型。

## <a name="prerequisites"></a>先决条件

* 一个有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建一个免费帐户**](https://azure.microsoft.com/free/)。
* [表单识别器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)或[认知服务多服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)资源 。

## <a name="additional-steps-for-custom-projects"></a>自定义项目的其他步骤

除了 Azure 帐户和表单识别器/认知服务资源外，你还将需要：

### <a name="azure-blob-storage-container"></a>Azure Blob 存储容器

标准性能 [Azure Blob 存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) 。 你需创建一个容器，用于存储和整理存储帐户中的 blob 数据。 如果不知道如何使用容器创建 Azure 存储帐户，请按照以下快速入门操作：

  * [创建存储帐户](/azure/storage/common/storage-account-create)。 创建存储帐户时，请确保在“实例详细信息”→“性能”字段中选择“标准”性能 。
  * [创建容器](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)。 创建容器时，在“新建容器”窗口中将“公共访问级别”字段设置为“容器”（对容器和 Blob 进行匿名读取访问）  。

### <a name="sample-documents-set"></a>示例文档集

1. 转到 [Azure 门户](https://ms.portal.azure.com/#home)，按照“存储帐户”→“数据存储”→“容器”进行导航  

   :::image border="true" type="content" source="../media/sas-tokens/data-storage-menu.png" alt-text="屏幕截图：Azure 门户中的数据存储菜单。":::

1. 从列表中选择一个容器。

1. 从页面顶部的菜单中选择“上传”。

    :::image border="true" type="content" source="../media/sas-tokens/container-upload-button.png" alt-text="屏幕截图：Azure 门户中的容器上传按钮。":::

1. 这将显示“上传 Blob”窗口。

1. 选择要上传的文件。

    :::image border="true" type="content" source="../media/sas-tokens/upload-blob-window.png" alt-text="屏幕截图：Azure 门户中的“上传 Blob”窗口。":::

> [!NOTE]
> 默认情况下，工作室将使用容器根目录下的表单文档。 但是，如果在自定义表单项目创建步骤中进行了指定，则可使用在文件夹中整理的数据。 请参阅[在子文件夹中整理数据](/azure/applied-ai-services/form-recognizer/build-training-data-set#organize-your-data-in-subfolders-optional)

## <a name="sign-into-the-form-recognizer-studio-preview"></a>登录表单识别器工作室预览版

完成先决条件后，导航到[表单识别器工作室预览版](https://formrecognizer.appliedai.azure.com)。

1. 从工作室主页中，选择表单识别器服务功能。

1. 选择你的 Azure 订阅、资源组和资源。 （可以随时在顶部菜单的“设置”中更改资源。）

1. 查看并确认你的选择。

:::image border="true" type="content" source="../media/quickstarts/form-recognizer-studio-get-started-v2.gif" alt-text="表单识别器工作室入门示例":::

## <a name="layout"></a>Layout

在布局视图中：

1. 选择“分析”命令来对示例文档运行布局分析，或者使用“添加”命令来尝试使用你的文档。

1. 观察突出显示的提取文本、显示提取的表位置的表图标和突出显示的选择标记。

1. 使用屏幕底部的控件来放大、缩小和旋转文档视图。

1. 显示和隐藏文本、表和选择标记层，从而一次聚焦其中每一项。

1. 在输出部分的“结果”选项卡中，浏览 JSON 输出来了解服务响应格式。 复制并下载到快速入门集成。

:::image border="true" type="content" source="../media/quickstarts/layout-get-started-v2.gif" alt-text="表单识别器布局示例":::

## <a name="prebuilt-models"></a>预生成的模型

在预生成视图中：

1. 从工作室主页中，选择一个预生成的模型。 在此示例中，我们使用的是发票模型。

1. 选择“分析”命令来对示例文档运行分析，或者使用“添加”命令来尝试使用你的发票。

1. 在“可视化效果”部分中，观察突出显示的字段和值以及发票行项。 此处还显示了已提取的所有文本和表。

1. 在输出部分的“字段”选项卡中，记下列出的字段和值，并选择要按与表类似的格式查看的行项。

1. 在输出部分的“结果”选项卡中，浏览 JSON 输出来了解服务响应格式。 复制并下载到快速入门集成。

:::image border="true" type="content" source="../media/quickstarts/prebuilt-get-started-v2.gif" alt-text="表单识别器预生成示例":::

## <a name="custom-model-basics"></a>自定义模型基础知识

### <a name="getting-started"></a>入门

若要创建自定义模型，请先配置项目：

1. 从工作室主页中，选择自定义表单项目来打开自定义表单主页。

1. 使用“创建项目”命令来启动“新建项目配置”向导。

1. 输入项目详细信息，选择 Azure 订阅和资源，并选择包含你的数据的 Azure Blob 存储容器。

1. 查看并提交你的设置以创建项目。

:::image border="true" type="content" source="../media/quickstarts/1-custom-model-get-started-v2.gif" alt-text="表单识别器自定义项目入门示例":::

### <a name="basic-flow"></a>基本流程

项目创建步骤后，在自定义模型阶段执行以下操作：

1. 从标签视图中，定义有兴趣提取的标签及其类型。

1. 选择文档中的文本，然后从下拉列表或标签窗格中单击标签。

1. 标记 4 个以上的文档，以至少标记 5 个文档。

1. 选择“训练”命令并输入模型名称和说明，开始训练自定义模型。

1. 模型准备就绪后，使用“测试”命令通过测试文档对其进行验证并观察结果。

:::image border="true" type="content" source="../media/quickstarts/2-custom-model-basic-steps-v2.gif" alt-text="表单识别器自定义项目基本工作流示例":::

### <a name="other-features"></a>其他功能

此外，请使用左侧的“模型”选项卡查看所有模型。 从列表视图中，选择模型来执行以下操作：

1. 从列表视图测试模型。

1. 使用“删除”命令删除不需要的模型。

1. 下载模型详细信息供脱机查看。

1. 选择多个模型，并将其组合到一个新模型中，以便在应用程序中使用。

## <a name="labeling-as-tables"></a>标记为表

创建自定义模型时，可能需要从文档中提取值的集合。 这些集合以各种格式显示。 例如：

* 给定一组字段（行）的值（列）的动态集合

* 值的固定集合，按第二组字段（行或列）进行分组

### <a name="label-as-dynamic-table"></a>标记为动态表

用于标记给定一组字段的数据的动态行：

1. 添加新的“表”类型标签，选择“动态表”类型，并为标签命名。

1. 添加所需的列（字段）数和行（数据）数。

1. 选择页面中的文本，然后单击要分配给文本的单元格。 对所有文档的所有页中的所有行和列重复此操作。

:::image border="true" type="content" source="../media/quickstarts/custom-tables-dynamic.gif" alt-text="表单识别器标记为动态表的示例":::

### <a name="label-as-fixed-table"></a>标记为固定表

用于标记按两组字段分组的固定数据集合：

1. 创建新的“表”类型标签，选择“固定表”类型，并为其命名。

1. 添加需要对应于两组字段的列数和行数。

1. 选择页面中的文本，然后单击要将其分配给文本的单元格。 对其他文档重复上述步骤。

:::image border="true" type="content" source="../media/quickstarts/custom-tables-fixed.gif" alt-text="表单识别器标记为固定表的示例":::

## <a name="labeling-for-signature-detection"></a>用于签名检测的标记

若要针对签名检测进行标记：

1. 在标记视图中，创建新的“签名”类型标签并为其命名。

1. 使用“区域”命令在签名的预期位置创建一个矩形区域。

1. 选择绘制的区域，并单击“签名”类型标签，将其分配到绘制的区域。 对其他文档重复上述步骤。

:::image border="true" type="content" source="../media/quickstarts/custom-signature.gif" alt-text="表单识别器用于签名检测的标记示例":::

## <a name="next-steps"></a>后续步骤

[表单识别器工作室预览版入门](https://formrecognizer.appliedai.azure.com)。
