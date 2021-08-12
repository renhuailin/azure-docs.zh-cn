---
title: 快速入门：创建要在应用中使用的 Object Anchors 模型
description: 本快速入门教程介绍如何从 3D 模型创建 Object Anchors 模型。
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 06/10/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: e3b67bee93a0e9a7b823d3a91d6d95e1bccbbe71
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202752"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>快速入门：从 3D 模型创建 Object Anchors 模型

Azure Object Anchors 是一项托管的云服务，它将 3D 模型转换为 AI 模型，以便为 HoloLens 启用对象感知混合现实体验。 此快速入门介绍如何使用[适用于 .NET 的 Azure Object Anchors 转换 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre) 从 3D 模型创建 Object Anchors 模型。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 Object Anchors 帐户。
> * 使用[适用于 .NET 的 Azure Object Anchors 转换 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre) ([NuGet](https://www.nuget.org/packages/Azure.MixedReality.ObjectAnchors.Conversion/)) 转换 3D 模型以创建 Object Anchors 模型。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

* 已安装 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> 的 Windows 计算机。
* <a href="https://git-scm.com" target="_blank">适用于 Windows 的 Git</a>。
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3.1 SDK</a>。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

## <a name="get-the-sample-project"></a>获取示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>转换 3D 模型

现在，可以继续转换 3D 模型。

1. 在 Visual Studio 中打开 `quickstarts/conversion/Conversion.sln`。 此解决方案包含一个 C# 控制台项目。

2. 打开位于项目根目录中的 `Configuration.cs` 文件，并替换以下字段的 `set-me` 值：

   | 字段         | 说明                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | 上面创建的 Object Anchors 帐户的帐户域。 |
   | AccountId     | 上面创建的 Object Anchors 帐户的帐户 ID。     |
   | AccountKey    | 上面创建的 Object Anchors 帐户的帐户密钥     |

   除此之外，还需要验证以下四个字段：

    | 字段                    | 说明                       |
    | ---                      | ---                               |
    | InputAssetPath           | 本地计算机上 3D 模型的绝对路径。 支持的文件格式为 `fbx`、`ply`、`obj`、`glb` 和 `gltf`。 |
    | AssetDimensionUnit       | 3D 模型的度量单位。 所有支持的度量单位都可以使用 `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` 枚举进行访问。 |
    | 引力                  | 3D 模型的重力向量方向。 此 3D 向量给出了模型坐标系中的向下方向。 例如，如果负数 `y` 表示模型的 3D 空间中的向下方向，则此值为 `Vector3(0.0f, -1.0f, 0.0f)`。 |

3. 生成并运行项目以上传 3D 模型，向服务注册新的转换作业，并等待其完成。 完成该作业后，将在 `InputAssetPath` 中指定的文件旁边下载 Object Anchors 模型。 你应会看到类似于下面的控制台输出：

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   记下作业 ID 以供将来参考。 调试或排查故障时，这可能很有用。

4. 成功完成作业后，你应该会在指定的输出位置看到一个格式为 `<Model-Filename-Without-Extension>_<JobID>.ou` 的文件。 例如，如果你的 3D 模型文件名为 `chair.ply`，而你的作业 ID 为 `00000000-0000-0000-0000-000000000000`，则服务输出的文件名将为 `chair_00000000-0000-0000-0000-000000000000.ou`。

## <a name="error-codes"></a>错误代码
若要详细了解资产转换作业失败可能导致的不同错误代码以及处理每个错误代码的方式，请参阅[转换错误代码页](..\model-conversion-error-codes.md)。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Object Anchors 帐户，并转换了 3D 模型来创建 Object Anchors 模型。 若要了解如何在混合现实应用中将该模型与 Object Anchors SDK 集成，请继续阅读以下文章：

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens 与 MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)

> [!div class="nextstepaction"]
> [转换 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
