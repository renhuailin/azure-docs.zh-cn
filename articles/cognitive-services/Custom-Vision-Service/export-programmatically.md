---
title: 以编程方式导出模型
titleSuffix: Azure Cognitive Services
description: 使用自定义视觉客户端库导出已训练模型。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: pafarley
ms.openlocfilehash: 0a9a804c98dc40c34ec8747df9eee7705d25a99b
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285534"
---
# <a name="export-a-model-programmatically"></a>以编程方式导出模型

[自定义视觉网站](https://www.customvision.ai/)上提供的所有导出选项也可以通过客户端库以编程方式完成。 可能需要执行此操作，以便完全自动执行重新训练和更新在本地设备上使用的模型迭代的过程。

本指南演示如何使用 Python SDK 将模型导出到 ONNX 文件。

## <a name="create-a-training-client"></a>创建训练客户端

需要使用 [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) 对象来导出模型迭代。 为自定义视觉训练资源 Azure 终结点和订阅密钥创建变量，并用其创建客户端对象。

```python
ENDPOINT = "PASTE_YOUR_CUSTOM_VISION_TRAINING_ENDPOINT_HERE"
training_key = "PASTE_YOUR_CUSTOM_VISION_TRAINING_SUBSCRIPTION_KEY_HERE"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)
```

> [!IMPORTANT]
> 请务必在完成后将密钥从代码中删除，同时切勿公开发布这些密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅[认知服务安全性](../cognitive-services-security.md)一文。

## <a name="call-the-export-method"></a>调用导出方法

调用 export_iteration 方法。
* 提供要导出模型的项目 ID、迭代 ID。 
* “平台”参数指定导出到的平台：允许的值为 `CoreML`、`TensorFlow`、`DockerFile`、`ONNX`、`VAIDK` 和 `OpenVino`。 
* “风格”参数指定导出模型的格式：允许的值为 `Linux`、`Windows`、`ONNX10`、`ONNX12`、`ARM`、`TensorFlowNormal` 和 `TensorFlowLite`。
* “原始”参数提供用于检索原始 JSON 响应以及对象模型响应的选项。

```python
project_id = "PASTE_YOUR_PROJECT_ID"
iteration_id = "PASTE_YOUR_ITERATION_ID"
platform = "ONNX"
flavor = "ONNX10"
export = trainer.export_iteration(project_id, iteration_id, platform, flavor, raw=False)
```

有关详情，请参阅 [export_iteration](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#export-iteration-project-id--iteration-id--platform--flavor-none--custom-headers-none--raw-false----operation-config-) 方法。

## <a name="download-the-exported-model"></a>下载导出的模型

接下来，你将调用 get_exports 方法来检查导出操作的状态。 操作将以异步方式运行，因此你应轮询此方法，直到操作完成。 完成此程序后，你可以检索 URI，将模型迭代下载到设备。

```python
while (export.status == "Exporting"):
    print ("Waiting 10 seconds...")
    time.sleep(10)
    exports = trainer.get_exports(project_id, iteration_id)
    # Locate the export for this iteration and check its status  
    for e in exports:
        if e.platform == export.platform and e.flavor == export.flavor:
            export = e
            break
    print("Export status is: ", export.status)
```

有关详情，请参阅 [get_exports](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#get-exports-project-id--iteration-id--custom-headers-none--raw-false----operation-config-) 方法。

然后，你可以通过编程方式将导出的模型下载到设备上的某个位置。

```python
if export.status == "Done":
    # Success, now we can download it
    export_file = requests.get(export.download_uri)
    with open("export.zip", "wb") as file:
        file.write(export_file.content)
```

## <a name="next-steps"></a>后续步骤

浏览以下文章或示例之一，将导出的模型集成到应用程序中：

* [将 Tensorflow 模型与 Python 配合使用](export-model-python.md)
* [将 ONNX 模型与 Windows 机器学习配合使用](custom-vision-onnx-windows-ml.md)
* 查看 [iOS 应用程序中的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)示例，了解如何使用 Swift 进行实时图像分类。
* 查看 [Android 应用程序中的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)示例，了解如何在 Android 上进行实时图像分类。
* 查看[将 CoreML 模型与 Xamarin 配合使用](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)示例，了解如何在 Xamarin iOS 应用中进行实时图像分类。