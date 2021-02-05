---
title: 纹理
description: 纹理资源工作流
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594411"
---
# <a name="textures"></a>纹理

纹理是不可变[共享资源](../concepts/lifetime.md)。 纹理可从 [blob 存储](../how-tos/conversion/blob-storage.md)加载并直接应用于模型，如[教程：更改环境和材料](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md)所示。 不过，通常情况下，纹理会成为[转换的模型](../how-tos/conversion/model-conversion.md)的一部分，在该模型中，纹理由其[材料](materials.md)引用。

## <a name="texture-types"></a>纹理类型

不同的纹理类型具有不同的用例：

* “2D 纹理”主要用于[材料](materials.md)。
* “立方体贴图”可用于[天空](../overview/features/sky.md)。

## <a name="supported-texture-formats"></a>支持的纹理格式

为 ARR 指定的所有纹理都必须采用 [DDS 格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)。 最好使用纹理图和纹理压缩。

## <a name="loading-textures"></a>加载纹理

加载纹理时，必须指定其预期类型。 如果类型不匹配，纹理加载将失败。
加载具有相同 URI 的纹理两次将返回相同的纹理对象，因为它是[共享的资源](../concepts/lifetime.md)。

与加载模型类似，有两种用于在源 blob 存储中为纹理资产寻址的变体：

* 如果 [Blob 存储已链接到帐户](../how-tos/create-an-account.md#link-storage-accounts)，则可直接通过 Blob 存储参数对纹理进行寻址。 在本例中，相关的加载函数为具有参数 `LoadTextureOptions` 的 `LoadTextureAsync`。
* 纹理资产可以通过其 SAS URI 寻址。 相关的加载函数为具有参数 `LoadTextureFromSasOptions` 的 `LoadTextureFromSasAsync`。 加载[内置纹理](../overview/features/sky.md#built-in-environment-maps)时也使用此变体。

下面的示例代码演示如何加载纹理：

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

请注意，在使用其 SAS 变体时，仅加载函数/参数不同。

纹理类型和内容可能存在限制，具体取决于要使用的纹理。 例如，[PBR 材料](../overview/features/pbr-materials.md)的粗糙度图必须为灰度。

## <a name="api-documentation"></a>API 文档

* [C # 纹理类](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C # RenderingConnection LoadTextureAsync ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C # RenderingConnection LoadTextureFromSasAsync ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [C + + 纹理类](/cpp/api/remote-rendering/texture)
* [C + + RenderingConnection：： LoadTextureAsync ( # B1 ](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C + + RenderingConnection：： LoadTextureFromSasAsync ( # B1 ](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>后续步骤

* [材料](materials.md)
* [天空](../overview/features/sky.md)