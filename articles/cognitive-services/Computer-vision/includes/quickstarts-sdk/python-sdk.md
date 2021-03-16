---
title: 快速入门：适用于 Python 的计算机视觉客户端库
description: 通过本快速入门开始使用适用于 Python 的计算机视觉客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 900c019547ae542a42490f9a4f9d25ea6b0a97b3
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444216"
---
<a name="HOLTop"></a>

使用计算机视觉客户端库可以：

* 分析图像中的标记、文本说明、人脸、成人内容，等等。
* 使用 Read API 读取印刷文本和手写文本。

[参考文档](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [包 (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [示例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * 你的 Python 安装应包含 [pip](https://pip.pypa.io/en/stable/)。 可以通过在命令行上运行 `pip --version` 来检查是否安装了 pip。 通过安装最新版本的 Python 获取 pip。
* 拥有 Azure 订阅后，请在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 </a>，以获取密钥和终结点。 部署后，单击“转到资源”。

    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

可使用以下方式安装客户端库：

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

同时，安装 Pillow 库。

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建新的 Python 文件 &mdash;，例如 quickstart-file.py。 在喜好的编辑器或 IDE 中打开该文件，并导入以下库。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) 上找到它，其中包含此快速入门中的代码示例。

然后，为该资源的 Azure 终结点和密钥创建变量。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的计算机视觉资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../../key-vault/general/overview.md)。

> [!div class="nextstepaction"]
> [我设置了客户端](?success=set-up-client#object-model) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>对象模型

以下类和接口将处理计算机视觉 Python SDK 的某些主要功能。

|名称|说明|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| 此类直接处理所有图像操作，例如图像分析、文本检测和缩略图生成。|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | 所有计算机视觉功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。 它实现了 **ComputerVisionClientOperationsMixin**。|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| 此枚举定义可在标准分析操作中执行的不同类型的图像分析。 请根据需求指定一组 **VisualFeatureTypes** 值。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的计算机视觉客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析图像](#analyze-an-image)
* [读取印刷体文本和手写文本](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和密钥实例化某个客户端。 使用密钥创建 [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) 对象，然后在终结点上使用该对象创建 [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) 对象。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [我执行了客户端身份验证](?success=authenticate-client#analyze-an-image) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="analyze-an-image"></a>分析图像

使用客户端对象分析远程图像的可视化功能。 首先保存对要分析的图像的 URL 的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

> [!TIP]
> 还可以分析本地图像。 请参阅 [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) 方法，如 analyze_image_in_stream。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) 上的示例代码，了解涉及本地图像的方案。

### <a name="get-image-description"></a>获取图像说明

下面的代码获取为图像生成的描述文字列表。 有关更多详细信息，请参阅[描述图像](../../concept-describing-images.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>获取图像类别

下面的代码获取所检测到的图像类别。 有关更多详细信息，请参阅[对图像进行分类](../../concept-categorizing-images.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>获取图像标记

以下代码获取图像中检测到的标记集。 有关更多详细信息，请参阅[内容标记](../../concept-tagging-images.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>检测物体

以下代码检测图像中的常见物体并将其输出到控制台。 有关更多详细信息，请参阅[物体检测](../../concept-object-detection.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]        

### <a name="detect-brands"></a>检测品牌

以下代码检测图像中的公司品牌和徽标，并将其输出到控制台。 有关更多详细信息，请参阅[品牌检测](../../concept-brand-detection.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>检测人脸

下面的代码返回图像中检测到的人脸及其矩形坐标，以及选择面属性。 有关更多详细信息，请参阅[人脸检测](../../concept-detecting-faces.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>检测成人、色情或血腥内容

以下代码输出图像中检测到的成人内容。 有关更多详细信息，请参阅[成人、色情或血腥内容](../../concept-detecting-adult-content.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>获取图像配色方案

以下代码输出图像中检测到的颜色属性，如主色和主题色。 有关更多详细信息，请参阅[配色方案](../../concept-detecting-color-schemes.md)。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>获取特定于域的内容

计算机视觉可以使用专用模型对图像进行进一步分析。 有关更多详细信息，请参阅[特定于域的内容](../../concept-detecting-domain-content.md)。 

以下代码分析了图像中检测到的名人的相关数据。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

以下代码分析了图像中检测到的地标的相关数据。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>获取图像类型

以下代码输出有关图像类型的信息&mdash;无论它是剪贴画还是线条图。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

> [!div class="nextstepaction"]
> [我分析了图像](?success=analyze-image#read-printed-and-handwritten-text) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>读取印刷体文本和手写文本

计算机视觉可以读取图像中的可见文本，并将其转换为字符流。 分两部分来执行此操作。

### <a name="call-the-read-api"></a>调用读取 API

首先，使用以下代码对给定图像调用 read 方法。 这会返回一个操作 ID 并启动异步进程来读取图像的内容。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> 还可以从本地图像读取文本。 请参阅 [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) 方法，如 read_in_stream。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) 上的示例代码，了解涉及本地图像的方案。

### <a name="get-read-results"></a>获取读取结果

接下来，获取从 read 调用返回的操作 ID，并使用它查询服务以获取操作结果。 下面的代码每隔一秒钟检查一次操作，直到返回结果。 然后，它将提取的文本数据输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [我阅读了文字](?success=read-printed-handwritten-text#run-the-application) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [我运行了应用程序](?success=run-the-application#clean-up-resources) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [我清理了资源](?success=clean-up-resources#next-steps) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用适用于 Python 的计算机视觉库执行基本任务。 接下来，请在参考文档中详细了解该库。

> [!div class="nextstepaction"]
>[计算机视觉 API 参考 (Python)](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [什么是计算机视觉？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) 上找到此示例的源代码。
