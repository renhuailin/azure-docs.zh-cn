---
title: 使用 .NET 从策略中获取签名密钥
description: 本主题说明如何使用媒体服务 v3 .NET SDK 从现有策略中获取签名密钥。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 05/25/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 373a97f26ee2fd2d448d142c89528576d3495510
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110791785"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>从现有策略获取签名密钥

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V3 API 的主要设计原则之一是使 API 更安全。 v3 API 不会在 **Get** 或 **List** 操作中返回机密或凭据。 请参阅此处的详细说明：有关详细信息，请参阅 [Azure RBAC 和媒体服务帐户](security-rbac-concept.md)

本文中的示例演示如何使用 .NET 从现有策略中获取签名密钥。 
 
## <a name="download"></a>下载 

使用以下命令将包含完整 .NET 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
带有机密的 ContentKeyPolicy 示例位于 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/main/AMSV3Tutorials/EncryptWithDRM) 文件夹中。

## <a name="get-contentkeypolicy-with-secrets"></a>获取带有机密的 ContentKeyPolicy 

若要访问密钥，请使用 **GetPolicyPropertiesWithSecretsAsync**，如下例所示。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>后续步骤

[设计带访问控制的多 DRM 内容保护系统](architecture-design-multi-drm-system.md) 
