---
title: 内容保护迁移指南
description: 本文为你提供了基于内容保护场景的指南，可帮助你从 Azure 媒体服务 v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: 59f74740117ba9f549133c4ca9bcb510928eb105
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108138870"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>基于内容保护方案的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-4.svg)

本文提供了有关如何从 v2 API 将内容保护用例迁移到新的 Azure 媒体服务 v3 API 的详细信息和指南。

## <a name="protect-content-in-v3-api"></a>v3 API 中的保护内容

在新的 v3 API 中使用对 [Multi-key](architecture-design-multi-drm-system.md) 功能的支持。

有关具体步骤，请参阅本文末尾的内容保护概念、教程和操作方法指南。

> [!NOTE]
> 本文的其余部分将讨论如何使用 .NET 将 v2 内容保护迁移到 v3。  如果需要其他语言或方法的说明或示例代码，请针对此页创建一个 GitHub 问题。

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>v2 资产、流式处理定位符和属性的 v3 可见性

在 v2 API 中，`Assets`、`StreamingLocators` 和 `ContentKeys` 用于保护流式处理内容。 迁移到 v3 API 时，v2 API `Assets`、`StreamingLocators` 和 `ContentKeys` 都在 v3 API 中自动公开，它们的所有数据都可供你访问。

但是，你不能通过在 v2 中创建的 v3 API 来更新 v2 实体上的任何属性。

如果需要更新、更改或更换存储在 v2 实体上的内容，请使用 v2 API 对其进行更新，或创建新的 v3 API 实体对其进行迁移。

## <a name="asset-identifier-differences"></a>资产标识符差异

若要迁移，需要从 v2 资产访问属性或内容密钥。  必须了解的是，v2 API 使用 `AssetId` 作为主标识密钥，而新的 v3 API 使用实体的 Azure 资源管理名称作为主要标识符。  （v2 `Asset.Name` 属性不用作唯一标识符。）使用 v3 API 时，v2 资产名称现在显示为 `Asset.Description`。

例如，如果你以前有一个 ID 为 `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` 的 v2 资产，则标识符现在位于 GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` 的末尾。 通过 v3 API 列出 v2 资产时，将会看到这种情况。

任何使用 v2 API 创建和发布的资产将在 v3 API 中同时拥有 `ContentKeyPolicy` 和 `ContentKey`，而不是 `StreamingPolicy` 上的默认内容密钥策略。

有关详细信息，请参阅[内容密钥策略](./drm-content-key-policy-concept.md)文档和[流式处理策略](./stream-streaming-policy-concept.md)文档。

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>并行使用 Azure 媒体服务资源管理器 (AMSE) v2 工具和 AMSE v3 工具

结合使用 [v2 Azure 媒体服务资源管理器工具](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)和 [v3 Azure 媒体服务资源管理器工具](https://github.com/Azure/Azure-Media-Services-Explorer)，并行比较通过 v2 API 创建和发布的资产。 属性应该全部可见，但在不同的位置。

## <a name="use-the-net-content-protection-migration-sample"></a>使用 .NET 内容保护迁移示例

可以使用媒体服务代码示例中 ContentProtection 下的 [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) 查找用于比较资产标识符差异的代码示例。

## <a name="list-the-streaming-locators"></a>列出流式处理定位符

可使用资产实体上的新 v3 方法 [ListStreamingLocators](/rest/api/media/assets/liststreaminglocators)，查询与 v2 API 中创建的资产相关联的 `StreamingLocators`。  同时引用 [ListStreamingLocatorsAsync](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?preserve-view=true&view=azure-dotnet) 的 .NET 客户端 SDK 版本

`ListStreamingLocators` 方法的结果将提供定位符的 `Name` 和 `StreamingLocatorId`，以及 `StreamingPolicyName`。

## <a name="find-the-content-keys"></a>查找内容密钥

若要查找与 `StreamingLocators` 一起使用的 `ContentKeys`，可以调用 [StreamingLocator.ListContentKeysAsync](/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?preserve-view=true&view=azure-dotnet) 方法。  

有关 v3 API 中内容保护的详细信息，请参阅[通过媒体服务动态加密保护内容](./drm-content-protection-concept.md)一文。

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>更改 v2 ContentKeyPolicy 保留相同的 ContentKey

应首先通过 v2 SDK 在资产上取消发布（删除所有流式处理定位符）。 方法如下：

1. 删除定位符。
1. 取消链接 `ContentKeyAuthorizationPolicy`。
1. 取消链接 `AssetDeliveryPolicy`。
1. 取消链接 `ContentKey`。
1. 删除 `ContentKey`。
1. 使用 v3 `StreamingPolicy` 和 `ContentKeyPolicy` 在 v3 中创建新的 `StreamingLocator`，指定所需的特定内容密钥标识符和密钥值。

> [!NOTE]
> 可以使用 v3 API 删除 v2 定位符，但如果内容密钥或内容密钥策略是在 v2 API 中创建的，则此操作不会删除它们。

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>内容保护概念、教程和操作指南

### <a name="concepts"></a>概念

- [使用媒体服务动态加密保护内容](drm-content-protection-concept.md)
- [设计带访问控制的多 DRM 内容保护系统](architecture-design-multi-drm-system.md)
- [带有 PlayReady 许可证模板的媒体服务 v3](drm-playready-license-template-concept.md)
- [媒体服务 v3 Widevine 许可证模板概述](drm-widevine-license-template-concept.md)
- [Apple FairPlay 许可要求和配置](drm-fairplay-license-overview.md)
- [流式处理策略](stream-streaming-policy-concept.md)
- [内容密钥策略](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>教程

[快速入门：使用门户加密内容](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>操作方法指南

- [从现有策略获取签名密钥](drm-get-content-key-policy-dotnet-how-to.md)
- [适用于 iOS 的脱机 FairPlay 流式处理与媒体服务 v3](drm-offline-fairplay-for-ios-concept.md)
- [适用于 Android 的脱机 Widevine 流式处理和媒体服务 v3](drm-offline-widevine-for-android.md)
- [使用媒体服务 v3 进行适用于 Windows 10 的 PlayReady 脱机流式处理](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>示例

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- 还可[将代码示例中的 V2 和 V3 代码进行比较](migrate-v-2-v-3-migration-samples.md)。

## <a name="tools"></a>工具

- [v3 Azure 媒体服务资源管理器工具](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure 媒体服务资源管理器工具](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)