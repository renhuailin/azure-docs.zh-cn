---
title: 操作说明：使用表单识别器分析文档、标记表单、训练模型和分析表单
titleSuffix: Azure Applied AI Services
description: 在本操作说明中，你将使用表单识别器示例工具来分析文档、发票和收据等内容。 标记并创建自定义模型，以从文档中提取文本、表、选择标记、结构和键值对。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/11/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: 文档处理
ms.openlocfilehash: decd5b23f0b88e078cee47a833cc913d5cb095b4
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326233"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
# <a name="train-a-custom-model-using-the-sample-labeling-tool"></a>使用示例标记工具训练自定义模型

在本文中，你要将示例标记工具和表单识别器 REST API 结合使用，通过手动标记的数据训练自定义文档处理模型。 如需详细了解如何使用表单识别器进行受监督的学习，请参阅概述的[通过标签进行训练](overview.md#train-with-labels)部分。

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，必须具备以下条件：

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="创建表单识别器资源"  target="_blank">创建表单识别器资源 </a>，获取密钥和终结点。 部署后，选择”转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
  * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 至少有六个相同类型的表单。 你将使用此数据训练模型并测试表单。 对于此快速入门，可使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)（下载并提取 sample_data.zip）。 将训练文件上传到标准性能层 Azure 存储帐户中 blob 存储容器的根目录。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](includes/create-resource.md)]

## <a name="try-it-out"></a>试试看

在线试用 [**表单识别器示例标记工具**](https://fott-2-1.azurewebsites.net/)：

> [!div class="nextstepaction"]
> [试用预生成模型](https://fott-2-1.azurewebsites.net/)

若要试用表单识别器服务，你将需要一个 Azure 订阅（[免费创建一个](https://azure.microsoft.com/free/cognitive-services)）和一个[表单识别器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)终结点和密钥。

## <a name="set-up-the-sample-labeling-tool"></a>设置示例标记工具

> [!NOTE]
>
> 如果存储数据位于 VNet 或防火墙后，则必须在 VNet 或防火墙后部署表单识别器示例标记工具，并通过创建[系统分配的托管标识](managed-identity-byos.md "Azure 托管标识是一种服务主体，可为 Azure 托管资源创建 Azure Active Directory (Azure AD) 标识和特定权限")来授予访问权限。

你将使用 Docker 引擎来运行示例标记工具。 请按以下步骤设置 Docker 容器。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。

> [!TIP]
> OCR 窗体标签工具也可用作 GitHub 上的开源项目。 该工具是使用 React + Redux 生成的 TypeScript Web 应用程序。 若要了解详细信息或进行参与，请参阅 [OCR 窗体标记工具](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application)存储库。 若要在线试用该工具，请转到[表单识别器示例标签工具网站](https://fott-2-1.azurewebsites.net/)。

1. 首先，在主计算机上安装 Docker。 本指南介绍如何将本地计算机用作主机。 若要在 Azure 中使用 Docker 托管服务，请参阅[部署示例标记工具](deploy-label-tool.md)操作指南。

   主计算机必须满足以下硬件要求：

    | 容器 | 最小值 | 建议|
    |:--|:--|:--|
    |示例标记工具|2 核心，4 GB 内存|4 核心，8 GB 内存|

    按照适用于所用操作系统的说明在计算机上安装 Docker：

   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. 通过 `docker pull` 命令获取示例标记工具容器。

    ```console
     docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1
    ```

1. 现在，已准备好通过 `docker run` 运行容器。

    ```console
     docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1 eula=accept
    ```

   此命令将使示例标记工具可通过 web 浏览器提供。 转到  `http://localhost:3000` 。

> [!NOTE]
> 还可以使用表单识别器 REST API 来标记文档和训练模型。 若要使用 REST API 进行训练和分析，请参阅[使用 REST API 和 Python 通过标签进行训练](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)。

## <a name="set-up-input-data"></a>设置输入数据

首先，请确保所有训练文档的格式相同。 如果有多种格式的表单，请根据通用格式将它们组织到子文件夹中。 训练时，需要将 API 定向到子文件夹。

### <a name="configure-cross-domain-resource-sharing-cors"></a>配置跨域资源共享 (CORS)

对存储帐户启用 CORS。 在 Azure 门户中选择你的存储帐户，然后在左侧窗格中选择“CORS”选项卡。 在底部行上，填写以下值。 选择顶部的“保存”。

* 允许的来源 = *
* 允许的方法 = \[全选\]
* 允许的标头 = *
* 公开的标头 = *
* 最长时间 = 200

> [!div class="mx-imgBorder"]
> ![Azure 门户中的 CORS 设置](media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>连接到示例标记工具

 示例标记工具将连接到源（上传的原始表单）和目标（创建的标签和输出数据）。

可以在项目之间设置和共享连接。 它们使用可扩展的提供程序模型，因此你可以轻松地添加新的源/目标提供程序。

若要创建新连接，请在左侧导航栏中选择“新建连接”（插头）图标。

在字段中填写以下值：

* **显示名称** - 连接显示名称。
* **说明** - 项目说明。
* **SAS URL** - Azure Blob 存储容器的共享访问签名 (SAS) URL。 [!INCLUDE [get SAS URL](includes/sas-instructions.md)]

   :::image type="content" source="media/quickstarts/get-sas-url.png" alt-text="SAS URL 检索":::

:::image type="content" source="media/label-tool/connections.png" alt-text="示例标记工具的连接设置。":::

## <a name="create-a-new-project"></a>创建新项目

在示例标记工具中，项目会存储你的配置和设置。 创建新项目并在字段中填写以下值：

* **显示名称** - 项目显示名称
* **安全令牌** - 某些项目设置可以包含敏感值，如 API 密钥或其他共享机密。 每个项目都会生成一个安全令牌，可用于加密/解密敏感项目设置。 可以在左侧导航栏底部选择齿轮图标，然后在“应用程序设置”中找到安全令牌。
* **源连接** - 上一步中创建的、用于此项目的 Azure Blob 存储连接。
* **文件夹路径** -（可选）如果源表单位于 blob 容器上的某个文件夹中，请在此处指定文件夹名称
* **表单识别器服务 Uri** - 表单识别器终结点 URL。
* **API 密钥** - 表单识别器订阅密钥。
* **说明** -（可选）项目说明

:::image type="content" source="media/label-tool/new-project.png" alt-text="示例标记工具上的“新建项目”页面。":::

## <a name="label-your-forms"></a>标记表单

创建或打开项目时，会打开主标记编辑器窗口。 标记编辑器由三部分组成：

* 一个可调整大小的预览窗格，其中包含源连接中的可滚动表单列表。
* 可以应用标记的主编辑器窗格。
* 通过标记编辑器窗格，用户可以修改、锁定、重新排序和删除标记。

### <a name="identify-text-and-tables"></a>标识文本和标签 

在左侧窗格中选择“对所有文件运行 OCR”，以获取每个文档的文本和表布局信息。 标记工具将在每个文本元素周围绘制边界框。

标记工具还将显示已自动提取的表。 选择文档左侧的表/网格图标可查看提取的表。 在本快速入门中，由于表内容是自动提取的，因此我们不会对表内容进行标记，而是依靠自动提取。

:::image type="content" source="media/label-tool/table-extraction.png" alt-text="示例标记工具中的表可视化效果。":::

在 v2.1 中，如果训练文档中有某个值未填写，可以在该值的输入位置绘制一个框。 使用窗口左上角的“绘制区域”使该区域可供标记。

### <a name="apply-labels-to-text"></a>将标签应用于文本

接下来创建标记（标签），并将其应用到希望模型分析的文本元素。

1. 首先，使用标记编辑器窗格创建要识别的标记。
   1. 选择 **+** 创建新标记。
   1. 输入标记名称。
   1. 按 Enter 保存标记。
1. 在主编辑器中，从突出显示的文本元素或绘制的区域中选择字词。
1. 选择要应用的标记，或按相应的键盘键。 数字键已分配为前 10 个标记的热键。 可以使用标记编辑器窗格中的向上和向下箭头图标对标记进行重新排序。
    > [!Tip]
    > 在标记窗体时，请记住以下提示：
    >
    > * 只能将一个标记应用到所选的每个文本元素。
    > * 每页只能应用每个标记一次。 如果某个值多次显示在同一窗体中，将为每个实例创建不同的标记。 例如：“invoice# 1”、“invoice# 2”等。
    > * 标记不能跨页。
    > * 按照值在窗体中的显示方式标记值；不要尝试使用两个不同的标记将值拆分为两个部分。 例如，即使某个地址字段跨越多行，也应使用单个标记对其进行标记。
    > * 不要在标记的字段中包含键 &mdash; 仅包含值。
    > * 系统应自动检测表数据，最终的输出 JSON 文件中将提供这些数据。 但是，如果模型无法检测所有表数据，你也可以手动标记这些字段。 使用不同的标签标记表中的每个单元格。 如果窗体中的表包含不同的行数，请确保使用可能最大的表标记至少一个窗体。
    > * 使用 + 右侧的按钮搜索、重命名、重新排序和删除标记。
    > * 若要在不删除标记本身的情况下删除已应用的标记，请在文档视图上选择带标记的矩形，并按 Delete 键。
    >

:::image type="content" source="media/label-tool/main-editor-2-1.png" alt-text="示例标记工具的主编辑器窗口。":::

按照上述步骤标记至少五个窗体。

### <a name="specify-tag-value-types"></a>指定标记值类型

可以设置每个标记的预期数据类型。 打开标记右侧的上下文菜单，然后从菜单中选择一个类型。 此功能可让检测算法做出假设，从而提高文本检测准确度。 它还确保在最终 JSON 输出中以标准格式返回检测到的值。 值类型信息保存在与标签文件位于相同路径的 fields.json 文件中。

> [!div class="mx-imgBorder"]
> ![使用示例标记工具选择值类型](media/whats-new/value-type.png)

目前支持以下值类型和变体：

* `string`
  * default、`no-whitespaces`、`alphanumeric`

* `number`
  * default、`currency`

* `date`
  * default、`dmy`、`mdy`、`ymd`

* `time`
* `integer`
* `selectionMark`

> [!NOTE]
> 请参阅以下日期格式设置规则：
>
> 必须指定格式（`dmy`、`mdy`、`ymd`）以使日期格式生效。
>
> 以下字符可用作日期分隔符：`, - / . \`。 空格不能用作分隔符。 例如：
>
> * 2020,01,01
> * 2020-01-01
> * 2020/01/01
>
> 日期和月份均可采用一位数字或两位数字的形式写入，而年份可以是两位数字或四位数字：
>
> * 2020-1-1
> * 20-01-1
>
> 如果日期字符串具有八位数字，则可以使用也可以不使用分隔符：
>
> * 20200101
> * 2020 01 01
>
> 月份还可以按其完整名称或简短名称写入。 如果使用该名称，则可以使用也可以不使用分隔符。 但是，这种格式可能会被认为没有其他格式那么准确。
>
> * 2020/Jan/01
> * 2020Jan01
> * 2020 Jan 01

### <a name="label-tables-v21-only"></a>标签表（仅适用于 v2.1）

有时，将数据标记为表可能比标记为键值对更好。 在这种情况下，可以单击“添加新的表标记”来创建表标记，根据文档指定该表包含固定还是可变的行数，并定义架构。

:::image type="content" source="media/label-tool/table-tag.png" alt-text="配置表标记。":::

定义表标记后，标记单元格值。

:::image type="content" source="media/table-labeling.png" alt-text="标记表。":::

## <a name="train-a-custom-model"></a>训练自定义模型

在左侧窗格中选择“训练”图标打开“训练”页。 然后选择“训练”按钮开始训练模型。 训练过程完成后，你将看到以下信息：

* **模型 ID** - 已创建和训练的模型的 ID。 每个训练调用都将创建一个具有自己的 ID 的新模型。 将此字符串复制到安全位置；如果要通过 [REST API](quickstarts/client-library.md?pivots=programming-language-rest-api&tabs=preview%2Cv2-1) 或[客户端库指南](quickstarts/client-library.md)进行预测调用，则需要使用此字符串。
* **平均准确度** - 模型的平均准确性。 可以通过标记更多表单并重新训练来创建新的模型，从而提高模型准确度。 建议首先标记五个表单，然后根据需要添加更多表单。
* 标记列表，以及每个标记的估计准确度。


:::image type="content" source="media/label-tool/train-screen.png" alt-text="训练视图。":::

训练完成后，检查“平均准确度”值。 如果该值较低，应添加更多输入文档，并重复上述步骤。 已标记的文档将保留在项目索引中。

> [!TIP]
> 还可以使用 REST API 调用来运行训练过程。 若要了解如何执行此操作，请参阅[使用 Python 通过标签进行训练](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)。

## <a name="compose-trained-models"></a>组合已训练的模型

通过模型组合，最多可将 100 个模型组合到一个模型 ID。 使用组合的 `modelID` 调用“分析”时，表单识别器首先将对提交的表单进行分类，选择最佳匹配的模型，然后返回该模型的结果。 当传入的表单可能属于多个模板中的一个模板时，此操作非常有用。

若要在示例标记工具中组合模型，请选择左侧的“模型组合”（合并箭头）图标。 在左侧，选择要组合在一起的模型。 带有箭头图标的模型是已组合的模型。
选择“组合”按钮。 在弹出窗口中，为新的组合模型命名，然后选择“组合”。 操作完成后，新组合的模型应会出现在列表中。

:::image type="content" source="media/label-tool/model-compose.png" alt-text="模型组合 UX 视图。":::

## <a name="analyze-a-form"></a>分析表单

选择左侧的“分析”（灯泡）图标来测试模型。 选择源“本地文件”。 浏览该文件，然后从你在测试文件夹中解压缩的示例数据集中选择一个文件。 再选择“运行分析”按钮，获取表单的键/值对、文本和表预测值。 该工具将在边界框中应用标记，并报告每个标记的置信度。

:::image type="content" source="media/analyze.png" alt-text="屏幕截图：分析自定义表单窗口":::

> [!TIP]
> 还可以通过 REST 调用运行分析 API。 若要了解如何执行此操作，请参阅[使用 Python 通过标签进行训练](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)。

## <a name="improve-results"></a>改进结果

可能需要执行进一步的训练来改进模型，具体取决于报告的准确度。 完成预测后，检查每个已应用标记的置信度值。 如果平均准确度训练值较高，但置信度分数较低（或结果不准确），则应将预测文件添加到训练集，标记该文件，然后再次训练。

如果分析的文档不同于训练中使用的文档，则报告的平均准确度、置信度分数和实际准确度可能不一致。 请记住，用户查看某些文档时，虽然看起来很相似，但在 AI 模型中却有明显的区别。 例如，你可以使用具有两种变体的表单类型进行训练，其中训练集由 20% 变体 A 和 80% 变体 B 组成。在预测过程中，变体 A 文档的置信度分数可能会较低。

## <a name="save-a-project-and-resume-later"></a>保存项目并在稍后恢复

若要在其他时间或在另一个浏览器中恢复项目，需要保存项目的安全令牌，稍后再重新输入。

### <a name="get-project-credentials"></a>获取项目凭据

转到项目设置页（滑块图标），并记下安全令牌名称。 然后，转到应用程序设置（齿轮图标），其中显示了当前浏览器实例中的所有安全令牌。 查找项目的安全令牌，并将其名称和密钥值复制到一个安全位置。

### <a name="restore-project-credentials"></a>还原项目凭据

若要恢复项目，首先需要创建到同一 blob 存储容器的连接。 为此，请重复上述步骤。 然后，转到应用程序设置页（齿轮图标），查看项目的安全令牌是否存在。 如果没有，请添加一个新的安全令牌，并复制上一步中的令牌名称和密钥。 选择“保存”以重新训练设置。

### <a name="resume-a-project"></a>恢复项目

最后，转到主页（房子图标），然后选择“打开云项目”。 然后选择 Blob 存储连接，并选择项目的 .fott 文件。 应用程序将加载项目的设置，因为它具有安全令牌。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用表单识别器示例标记工具通过手动标记的数据来训练模型。 如果要构建自己的实用工具来标记训练数据，请使用用于处理标记数据训练的 REST API。

> [!div class="nextstepaction"]
> [使用 Python 通过标签进行训练](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [什么是表单识别器？](overview.md)
* [表单识别器快速入门](quickstarts/client-library.md)
