---
title: 媒体服务 v2 与 v3 的迁移示例比较
description: 一组示例可帮助你比较 Azure 媒体服务 v2 与 v3 之间的代码差异。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 640b9b40295ae9b9aea865f7b6159da6ff4a3251
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898301"
---
# <a name="media-services-migration-code-sample-comparison"></a>媒体服务迁移代码示例比较

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

可以使用一些代码示例来比较 SDK 之间的操作方式。

## <a name="samples-for-comparison"></a>用于比较的示例

下表列出了在常见方案中用于比较 v2 和 v3 的示例。

|方案|v2 API|v3 API|
|---|---|---|
|创建资产并上传文件 |[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|提交作业|[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>演示如何先创建转换，再提交作业。|
|发布使用 AES 加密的资产 |1.创建 `ContentKeyAuthorizationPolicyOption`<br/>2.创建 `ContentKeyAuthorizationPolicy`<br/>3.创建 `AssetDeliveryPolicy`<br/>4.创建 `Asset` 并上传内容或提交 `Job` 并使用 `OutputAsset`<br/>5.将 `AssetDeliveryPolicy` 与 `Asset` 关联<br/>6.创建 `ContentKey`<br/>7.将 `ContentKey` 附加到 `Asset`<br/>8.创建 `AccessPolicy`<br/>9.创建 `Locator`<br/><br/>[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1.创建 `ContentKeyPolicy`<br/>2.创建 `Asset`<br/>3.上传内容或将 `Asset` 用作 `JobOutput`<br/>4.创建 `StreamingLocator`<br/><br/>[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|获取作业详细信息和管理作业 |[使用 v2 管理作业](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[使用 v3 管理作业](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
