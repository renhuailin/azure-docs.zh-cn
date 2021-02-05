---
title: 模型
description: 描述 Azure 远程渲染中的模型是什么
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593868"
---
# <a name="models"></a>模型

Azure 远程渲染中的模型指的是一个完整的对象表示法，由多个[实体](entities.md)和[组件](components.md)构成。 模型是将自定义数据引入远程渲染服务的主要方式。

## <a name="model-structure"></a>模型结构

一个模型恰好有一个[实体](entities.md)作为其根节点。 在该实体之下，可能有任意层次结构的子实体。 加载模型时，系统将返回对此根实体的引用。

每个实体都可能有附加的[组件](components.md)。 在最常见的情况下，实体具有 MeshComponents，该组件引用了[网格资源](meshes.md)。

## <a name="creating-models"></a>创建模型

通过从文件格式（例如 FBX 和 GLTF）[转换输入模型](../how-tos/conversion/model-conversion.md)来创建运行时模型。 转换过程将提取所有资源，例如纹理、材料和网格，并将其转换为优化的运行时格式。 该过程还将提取结构信息，并将其转换为 ARR 的实体/组件图结构。

> [!IMPORTANT]
> [模型转换](../how-tos/conversion/model-conversion.md)是创建[网格](meshes.md)的唯一方式。 尽管可以在运行时在实体之间共享网格，但是除了加载模型之外，没有其他方法可以将网格引入运行时。

## <a name="loading-models"></a>加载模型

转换模型后，可将其从 Azure Blob 存储加载到运行时。

这里有两种不同的加载函数，它们在 Blob 存储中对资产的寻址方式不同：

* 如果 [Blob 存储已关联到帐户](../how-tos/create-an-account.md#link-storage-accounts)，则可直接按 Blob 存储参数直接对模型进行寻址。 在本例中，相关的加载函数为 `LoadModelAsync`，其参数为 `LoadModelOptions`。
* 可以通过模型的 SAS URI 对模型进行寻址。 相关的加载函数为 `LoadModelFromSasAsync`，其参数为 `LoadModelFromSasOptions`。 加载[内置模型](../samples/sample-model.md)时也使用此变体。

以下代码片段演示了如何使用这两个函数加载模型。 若要使用 blob 存储参数加载模型，请使用如下所示的代码：


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

如果要使用 SAS 令牌加载模型，请使用类似于以下代码片段的代码：

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

之后，你可以遍历实体层次结构并修改实体和组件。 多次加载同一模型会创建多个实例，每个实例都有自己的实体/组件结构副本。 由于网格、材料和纹理是[共享的资源](../concepts/lifetime.md)，将不会再次加载其数据。 因此，多次实例化模型会产生相对较少的内存开销。

## <a name="api-documentation"></a>API 文档

* [C # RenderingConnection LoadModelAsync ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C # RenderingConnection LoadModelFromSasAsync ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C + + RenderingConnection：： LoadModelAsync ( # B1 ](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C + + RenderingConnection：： LoadModelFromSasAsync ( # B1 ](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [网格](meshes.md)