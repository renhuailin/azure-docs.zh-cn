---
title: Azure 媒体服务 v3 问题收集
description: 本文解答了有关 Azure 媒体服务 v3 的一系列问题。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/25/2021
ms.author: inhenkel
ms.openlocfilehash: 8503d2ee4f795deab5b228773809f6b754ee8284
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602866"
---
<!-- NOTE this file is temporary and a placeholder until the FAQ file update is completed. -->

# <a name="media-services-v3-questions-collection"></a>媒体服务 v3 问题收集

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文解答有关 Azure 媒体服务 v3 的常见问题。

## <a name="general"></a>常规

### <a name="does-media-services-store-any-customer-data-outside-of-the-service-region"></a>媒体服务是否在服务区域之外存储任何客户数据？

- 客户将自己的存储帐户附加到 Azure 媒体服务帐户。  所有资产数据都存储在这些关联的存储帐户中，客户控制此存储的位置和复制类型。
- 与媒体服务帐户关联的其他数据（包括内容加密密钥、令牌验证密钥、JobInputHttp URL 以及其他实体元数据）存储在 Microsoft 拥有的存储中，而该存储则位于为媒体服务帐户选择的区域中。
    - 考虑到巴西南部和东南亚的[数据驻留要求](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information)，其他帐户数据以区域冗余方式存储，包含在单个区域中。 东南亚的所有其他帐户数据存储在新加坡，巴西南部的数据存储在巴西。
    - 在除巴西南部和东南亚之外的区域，其他帐户数据还可以存储在[配对区域](../../best-practices-availability-paired-regions.md)中 Microsoft 拥有的存储中。
- Azure 媒体服务是一种区域服务，不提供[高可用性](architecture-high-availability-encoding-concept.md)或数据复制。 如果客户需要这些功能，则强烈建议其使用多个区域中的媒体服务帐户来构建解决方案。  我们以指南的形式提供了一个示例，展示了如何使用媒体服务点播视频构建解决方案以实现高可用性。

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Azure 门户针对媒体服务 v3 有哪些限制？

可以使用 [Azure 门户](https://portal.azure.com/)执行以下操作：管理 v3 实时事件、查看 v3 资产和作业、获取有关访问 API 的信息以及加密内容。 <br/>对于其他所有管理任务（例如，管理转换和作业或分析 v3 内容），请使用 [CLI](/cli/azure/ams)或某个受支持的客户端 [SDK](media-services-apis-overview.md#sdks)。

如果视频以前是使用媒体服务 v3 API 上传到媒体服务帐户的，或者该内容是基于实时输出生成的，则 Azure 门户中不会显示“编码”、“分析”或“加密”按钮  。 使用媒体服务 v3 API 来执行这些任务。  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure 角色可对 Azure 媒体服务资源执行哪些操作？ 

请参阅[媒体服务帐户的 Azure 基于角色的访问控制 (Azure RBAC)](security-rbac-concept.md)。

### <a name="how-do-i-stream-to-apple-ios-devices"></a>如何流式传输到 Apple iOS 设备？

确保在路径的末尾（在 URL 的“/manifest”部分之后）有“(format=m3u8-aapl)”，目的是告知流式处理源服务器返回 HTTP Live Streaming (HLS) 内容，以便在 Apple iOS 本机设备上使用 。 有关详细信息，请参阅[传送内容](encode-dynamic-packaging-concept.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>什么是处理视频的建议方法？

[转换](/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个转换描述了用于处理视频或音频文件的脚本或任务工作流。 [作业](/rest/api/media/jobs)是针对媒体服务的实际请求，目的是将转换应用到输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 有关详细信息，请参阅[转换和作业](transform-jobs-concept.md)。

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>我已经上传、编码并发布了视频。 为什么在我尝试对视频进行流式处理时，它不播放？

最常见的原因之一是，你播放时使用的流式处理终结点未处于“正在运行”状态。

### <a name="how-does-pagination-work"></a>分页是如何工作的？

使用分页时，应始终使用下一链接来枚举集合，而不依赖特定的页面大小。 有关详细信息和示例，请参阅[筛选、排序、分页](filter-order-page-entitites-how-to.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>目前有哪些功能在 Azure 媒体服务 v3 中不可用？

有关详细信息，请参阅[迁移指南](migrate-v-2-v-3-migration-introduction.md)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>如何在订阅之间移动媒体服务帐户？  

有关详细信息，请参阅[在订阅之间移动媒体服务帐户](account-move-account-how-to.md)。

## <a name="live-streaming"></a>实时传送视频流 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>广播完成后如何停止实时传送流？

你可以从客户端或服务器端来实现。

#### <a name="client-side"></a>客户端

当用户关闭浏览器时，Web 应用程序应该提示用户是否要结束广播。 这是 Web 应用程序可以处理的浏览器事件。

#### <a name="server-side"></a>服务器端

可通过订阅 Azure 事件网格事件来监视实时事件。 有关详细信息，请参阅 [EventGrid 事件架构](monitoring/media-services-event-schemas.md#live-event-types)。

可以：

* [订阅](monitoring/reacting-to-media-services-events.md)流式传输级别 [Microsoft.Media.LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md#liveeventencoderdisconnected) 事件，并监视一段时间内没有重新连接来停止和删除实时事件。
* [订阅](monitoring/reacting-to-media-services-events.md)跟踪级别[检测信号](monitoring/media-services-event-schemas.md#liveeventingestheartbeat)事件。 如果所有跟踪的传入比特率下降到 0，或者最后时间戳不再增大，则可以安全地关闭实时事件。 每个跟踪每隔 20 秒出现一次检测信号事件，因此可能有点冗长。

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>如何在实时传送流过程中插入中断/视频和图像盖板？

媒体服务 v3 实时编码尚不支持在实时流过程中插入视频或图像盖板。 

可以使用[实时本地编码器](encode-recommended-on-premises-live-encoders.md)切换源视频。 许多应用提供切换源（包括 Telestream Wirecast、Switcher Studio（在 iOS 上）和 OBS Studio（免费应用））的功能。

## <a name="content-protection"></a>内容保护

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>应使用 AES-128 明文密钥加密还是 DRM 系统？

客户通常希望知道他们应该使用 AES 加密还是 DRM 系统。 这两个系统之间的主要差别在于，使用 AES 加密时，内容密钥将通过 TLS 传输到客户端，这样，密钥将经过传输中加密，但不会经过任何进一步的加密（“明文加密”）。 因此，用于解密内容的密钥可由客户端播放器访问，并且可以在客户端上的网络跟踪中以纯文本形式显示。 AES-128 明文密钥加密适合查看者是受信任方的用例（例如，加密员工观看的在公司内部分发的公司视频）。

相比 AES-128 明文密钥，PlayReady、Widevine 和 FairPlay 之类的 DRM 系统都可以对用于解密内容的密钥提供额外的加密级别。 内容密钥将会加密成受 DRM 运行时保护的密钥，此外还会进行 TLS 提供的任何传输级加密。 此外，解密是在安全的环境中在操作系统级别处理的，在这样的环境中，恶意用户更难进行攻击。 在观看者可能不是受信任方且需要更高等级的安全性的用例中，建议使用 DRM。

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何在不使用 Azure AD 的情况下仅向具有特定权限的用户显示视频？

无需使用任何特定的令牌提供程序，例如 Azure Active Directory (Azure AD)。 可以使用非对称密钥加密创建自己的 [JWT](https://jwt.io/) 提供程序（所谓的“安全令牌服务”，简称 STS）。 在自定义 STS 中，可以根据业务逻辑添加声明。

确保颁发者、受众和声明在 JWT 中的内容和 `ContentKeyPolicy` 中使用的 `ContentKeyPolicyRestriction` 值之间完全匹配。

有关详细信息，请参阅[使用媒体服务动态加密保护内容](drm-content-protection-concept.md)。

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>在使用 JWT 令牌请求许可证或密钥之前，如何以及在何处获取 JWT 令牌？

在生产环境中，需要获取安全令牌服务（一个 Web 服务），以便根据 HTTPS 请求颁发 JWT 令牌。 对于测试，可以使用 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/EncryptWithDRM/Program.cs) 定义的 `GetTokenAsync` 方法中所示的代码。

对用户进行身份验证后，播放器会向 STS 发出请求以获取此类令牌，并将其分配为令牌的值。 可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)。

有关使用对称密钥或非对称密钥运行 STS 的示例，请参阅 [JWT 工具](https://aka.ms/jwt)。 有关使用此类 JWT 令牌的基于 Azure Media Player 的播放器示例，请参阅 [Azure 媒体测试工具](https://aka.ms/amtest)。 （展开“player_settings”链接可查看令牌输入。）

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授权使用 AES 加密流式传输视频的请求？

正确的方法是使用安全令牌服务。 在 STS 中，根据用户配置文件添加不同的声明（例如“高级用户”、“基本用户”、“免费试用版用户”）。 在 JWT 中添加不同的声明后，用户可以查看不同的内容。 对于不同的内容或资产，`ContentKeyPolicyRestriction` 会包含相应的 `RequiredClaims` 值。

使用 Azure 媒体服务 API 来配置许可证/传送密钥以及加密资产（如[此示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/EncryptWithAES/Program.cs)中所示）。

有关详细信息，请参阅：

- [内容保护概述](drm-content-protection-concept.md)
- [设计带访问控制的多 DRM 内容保护系统](architecture-design-multi-drm-system.md)

### <a name="should-i-use-http-or-https"></a>应该使用 HTTP 还是 HTTPS？
构建的 ASP.NET MVC 播放器应用程序必须支持以下功能：

* 通过 Azure AD 进行用户身份验证（使用 HTTPS）。
* 客户端与 Azure AD 之间的 JWT 交换（使用 HTTPS）。
* 客户端的 DRM 许可证获取，如果许可证传送由媒体服务提供（必须使用 HTTPS）。 PlayReady 产品套件不会针对许可证传送强制要求使用 HTTPS。 如果 PlayReady 许可证服务器位于媒体服务以外的地方，则可以使用 HTTP 或 HTTPS。

最佳做法是让 ASP.NET 播放器应用程序使用 HTTPS，使媒体播放器位于使用 HTTPS 的页面上。 不过，最好是使用 HTTP 进行流式传输，因此需要考虑这些混合内容问题：

* 浏览器不允许混合内容。 但是 Silverlight 等插件和适用于平滑流与 DASH 的 OSMF 插件允许混合内容。 混合内容是一个安全隐患，因为存在插入恶意 JavaScript 的威胁，使客户数据处于风险之中。 默认情况下，浏览器会阻止此类内容。 唯一的解决方法是在服务器（来源）端允许所有域（不管是 HTTPS 还是 HTTP）。 这可能也不是个好主意。
* 避免混合内容。 播放器应用程序和媒体播放器应使用 HTTP 或 HTTPS。 播放混合内容时，SilverlightSS 技术需要清除混合内容警告。 FlashSS 技术在没有混合内容警告的情况下处理混合内容。
* 如果流式处理终结点是在 2014 年 8 月之前创建的，则它不支持 HTTPS。 在此情况下，请针对 HTTPS 创建并使用新的流式处理终结点。

### <a name="what-about-live-streaming"></a>如何使用实时流？

可以使用完全相同的设计和实现来帮助保护媒体服务中的实时传送视频流，方法是将与节目关联的资产视为 VOD 资产。 若要为实时内容提供多重 DRM 保护，请在将资产关联到实时输出之前，将相同的设置/处理应用到资产，就如同它是 VOD 资产一样。

### <a name="what-about-license-servers-outside-media-services"></a>如何使用媒体服务外部的许可证服务器？

通常，客户可能已在自己的数据中心或由 DRM 服务提供商托管的位置投资了许可证服务器场。 使用媒体服务内容保护，可以在混合模式下操作。 可以在媒体服务中托管和动态保护内容，而 DRM 许可证由 Azure 媒体服务外部的服务器传送。 在此情况下，请注意以下变更：

* STS 需要颁发被许可证服务器场认可和验证的令牌。 例如，Axinom 提供的 Widevine 许可证服务器需要特定的 JWT，其中包含权利消息。 你需要通过一个 STS 颁发此类 JWT。 
* 不再需要在媒体服务中配置许可证传送服务。 配置 `ContentKeyPolicy` 时，需要提供许可证获取 URL（针对 PlayReady、Widevine 和 FairPlay）。

> [!NOTE]
> Widevine 是 Google 提供的一项服务，受 Google 服务条款和隐私策略的约束。

## <a name="media-services-v2-vs-v3"></a>媒体服务 v2 与 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>能否使用 Azure 门户来管理 v3 资源？

目前，可以使用 [Azure 门户](https://portal.azure.com/)执行以下操作：

* 在媒体服务 v3 中管理[实时事件](live-event-outputs-concept.md)。 
* 查看（而不是管理）v3 [资产](assets-concept.md)。 
* [获取有关访问 API 的信息](./access-api-howto.md)。 

对于其他所有管理任务（例如，[转换和作业](transform-jobs-concept.md)和[内容保护](drm-content-protection-concept.md)），请使用 [REST API](/rest/api/media/)、[Azure CLI](/cli/azure/ams) 或某个受支持的 [SDK](media-services-apis-overview.md#sdks)。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

已从媒体服务 API 中删除了 `AssetFile` 概念，以便将媒体服务与存储 SDK 依赖项分开。 现在由 Azure 存储而非媒体服务来保存属于存储 SDK 的信息。 

有关详细信息，请参阅[迁移到媒体服务 v3](migrate-v-2-v-3-migration-introduction.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>客户端存储加密在哪里进行？

现在建议使用服务器端存储加密（在默认情况下为打开状态）。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](../../storage/common/storage-service-encryption.md)。

## <a name="offline-streaming"></a>脱机流式处理

### <a name="fairplay-streaming-for-ios"></a>适用于 iOS 的 FairPlay 流式处理

以下常见问题解答可帮助你排查适用于 iOS 的脱机 FairPlay 流式处理的问题。

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>为什么在脱机模式期间只播放音频而不播放视频？

此行为似乎是示例应用专门设计的。 存在备用音频曲目时（这适用于 HLS），在脱机模式期间，iOS 10 和 iOS 11 都默认播放备用音频曲目。为了补偿 FPS 脱机模式的此行为，需要从流删除备用音频曲目。 若要在媒体服务中完成此操作，请添加动态清单筛选器 audio-only=false。 换言之，HLS URL 将以 .ism/manifest(format=m3u8-aapl,audio-only=false) 结尾。 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>为什么添加 audio-only=false 之后，在脱机模式期间仍只播放音频而不播放视频？

根据内容分发网络的缓存键设计，可能会缓存该内容。 请清除缓存。

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>除 iOS 10 之外，iOS 11 是否也支持 FPS 脱机模式？

是的。 iOS 10 和 iOS 11 支持 FPS 脱机模式。

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>为什么在 FPS Server SDK 中，无法使用 FairPlay Streaming 和 HTTP Live Streaming 找到文档“脱机播放”？

从 FPS Server SDK 版本 4 开始，此文档已合并到“FairPlay Streaming 编程指南”。

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS 设备上的已下载/脱机文件结构是什么？

iOS 设备上的已下载文件结构如下屏幕截图所示。 `_keys` 文件夹存储已下载的 FPS 许可证，每个许可证服务主机一个存储文件。 `.movpkg` 文件夹存储音频和视频内容。 

第一个文件夹（文件名以破折号加数字结尾）包含视频内容。 数值是峰值带宽视频呈现形式。 第二个文件夹（文件名以破折号加 0 结尾）包含音频内容。 第三个文件夹（文件名为 `Data`）包含 FPS 内容的主播放列表。 最后，boot.xml 提供 `.movpkg` 文件夹内容的完整说明。 

![FairPlay iOS 示例应用的脱机文件结构](media/drm-offline-fairplay-for-ios-concept/offline-fairplay-file-structure.png)

下面是一个示例 boot.xml 文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>适用于 Android 的 Widevine 流式处理

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>如何为某些客户端/用户传送永久许可证（允许脱机）并为其他人传送非永久许可证（禁用脱机）？ 是否必须复制内容并使用单独的内容密钥？

由于媒体服务 v3 允许资产有多个 `StreamingLocator` 实例，因此你可以有：

* 一个 `license_type = "persistent"` 的 `ContentKeyPolicy` 实例，其中的 `ContentKeyPolicyRestriction` 包含对 `"persistent"` 的声明，此外还有 `StreamingLocator`。
* 另一个 `license_type="nonpersistent"` 的 `ContentKeyPolicy` 实例，其中的 `ContentKeyPolicyRestriction` 包含对 `"nonpersistent`" 的声明，此外还有 `StreamingLocator`。
* 两个具有不同 `ContentKey` 值的 `StreamingLocator` 实例。

根据自定义 STS 的业务逻辑，在 JWT 令牌中发出不同的声明。 使用该令牌，只能获得相应的许可证，并且只能播放相应的 URL。

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>在 Widevine 和媒体服务 DRM 安全级别之间进行的映射是什么？

Google 的“Widevine DRM 体系结构概述”定义了三种安全级别。 但是，[Widevine 许可证模板上的 Azure 媒体服务文档](drm-widevine-license-template-concept.md)概述了五种安全级别（播放的客户端稳定性要求）。 此部分介绍如何映射安全级别。

两组安全级别均由 Google Widevine 定义。 不同之处是使用级别：体系结构或 API。 Widevine API 中使用了这五种安全级别。 `content_key_specs` 对象包含 `security_level`，该对象被反序列化，并通过 Azure 媒体服务 Widevine 许可证服务传递给 Widevine 全球传送服务。 下表显示两组安全级别之间的映射。

| **Widevine 体系结构中定义的安全级别** |**Widevine API 中使用的安全级别**|
|---|---| 
| **安全级别 1**：所有内容处理、加密和控制操作均在受信任的执行环境 (TEE) 中进行。 在某些实现模型中，可在不同的芯片中执行安全处理。|**security_level=5**：加密、解码以及对媒体的所有处理（压缩和解压缩）必须在硬件支持的 TEE 中完成。<br/><br/>**security_level=4**：内容加密和解码必须在硬件支持的 TEE 中执行。|
**安全级别 2**：在 TEE 中进行加密（而不是视频处理）。 已解密的缓冲区返回到应用程序域，通过单独的视频硬件或软件进行处理。 但是，在级别 2，加密信息仍然只在 TEE 中进行处理。| **security_level=3**：密钥材料和加密操作必须在硬件支持的 TEE 中执行。 |
| **安全级别 3**：设备上没有 TEE。 可采取相应措施来保护主机操作系统中的加密信息和已解密内容。 级别 3 实现可能还包括硬件加密引擎，但只能增强性能，而不能增强安全性。 | **security_level=2**：需要软件加密和模糊处理解码器。<br/><br/>**security_level=1**：需要基于软件的白盒加密。|

#### <a name="why-does-content-download-take-so-long"></a>为什么下载内容需要很长时间？

可通过两种方法提高下载速度：

* 启用内容分发网络，这样用户就更有可能点击它而不是源/流式处理终结点来下载内容。 如果用户点击流式处理终结点，系统会动态打包和加密每个 HLS 段或 DASH 片段。 即使每个段或片段的这种延迟只有几毫秒，但如果视频时间长达一个小时，则累积延迟也可能会很严重，导致下载时间变长。
* 让用户能够选择性地下载视频质量层和音轨，而不是所有内容。 对于脱机模式，无需下载所有质量层。 可通过两种方式实现此目的：

  * 客户端控制：播放器应用自动选择要下载的视频质量层和音轨，或由用户进行选择。
  * 服务控制：你可以使用 Azure 媒体服务中的动态清单功能创建（全局）筛选器，将 HLS 播放列表或 DASH MPD 限制为单个视频质量层和所选音轨。 然后，向用户呈现的下载 URL 会包括此筛选器。

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)