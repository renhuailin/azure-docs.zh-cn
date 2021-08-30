---
title: 适用于 Azure 通信服务的 SDK 和 REST API
titleSuffix: An Azure Communication Services concept document
description: 详细了解 Azure 通信服务 SDK 和 REST API。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: cdc9ae8f89c13a528ff31855f731821b39e5a048
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742539"
---
# <a name="sdks-and-rest-apis"></a>SDK 和 REST API

Azure 通信服务功能在概念上分为八个领域。 大多数区域都具有针对已发布的 REST API 编程的完全开放源代码的 SDK，可以直接通过 Internet 使用。 呼叫 SDK 使用专有网络接口，并且是闭源的。

下表汇总了这些领域以及 REST API 和 SDK 库的可用性。 我们还标明了 API 和 SDK 是否适用于最终用户客户端或受信任的服务环境。 在低信任环境中，最终用户设备不应直接访问 API 和 SDK（如短信）。

通过使用 [Azure 通信服务 UI 库](https://azure.github.io/communication-ui-library)，可以更快速地开发基于 Web 的通话和聊天应用程序。 该 UI 库提供可用于生产的 UI 组件库，你可以将其置于应用程序中。

## <a name="rest-apis"></a>REST API
[docs.microsoft.com](/rest/api/azure/) 中介绍了通信服务 API 和其他 Azure REST API。 此文档将说明如何构造 HTTP 消息，并提供 Postman 使用指导。 REST 接口文档也在 [GitHub](https://github.com/Azure/azure-rest-api-specs) 上以 Swagger 格式提供。


## <a name="sdks"></a>SDK
| 程序集 | 协议| 环境 | 功能|
|--------|----------|---------|----------------------------------|
| Azure 资源管理器 | [REST](/rest/api/communication/communicationservice)| 服务| 预配和管理通信服务资源|
| 通用 | 不适用 | 客户端和服务 | 提供其他 SDK 的基类型 |
| 标识 | [REST](/rest/api/communication/communicationidentity) | 服务| 管理用户、访问令牌|
| 电话号码| [REST](/rest/api/communication/phonenumbers)| 服务| 获取和管理电话号码 |
| SMS| [REST](/rest/api/communication/sms) | 服务| 发送和接收短信|
| 聊天 | 使用专有信号的 [REST](/rest/api/communication/) | 客户端和服务 | 向应用程序添加实时文本聊天 |
| 调用| 专有传输 | 客户端 | 语音、视频、屏幕共享和其他实时通信 |
| 呼叫服务器 | REST| 服务| 发起并管理通话、播放音频以及配置录制 |
| Network Traversal| REST| 服务| 访问用于低级数据传输的 TURN 服务器 |
| UI 库 | 不适用 | 客户端 | 聊天和呼叫应用的可用于生产的 UI 组件 |

### <a name="languages-and-publishing-locations"></a>语言和发布位置

下面详细介绍了各个 SDK 包的发布位置。

| 领域 | JavaScript | .NET | Python | Java SE | iOS | Android | 其他|
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure 资源管理器 | [npm](https://www.npmjs.com/package/@azure/arm-communication) | [NuGet](https://www.NuGet.org/packages/Azure.ResourceManager.Communication)| [PyPi](https://pypi.org/project/azure-mgmt-communication/)| [Maven](https://search.maven.org/search?q=azure-resourcemanager-communication)| -| -| [通过 GitHub 的 Go](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 通用 | [npm](https://www.npmjs.com/package/@azure/communication-common) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Common/)| 不适用| [Maven](https://search.maven.org/search?q=a:azure-communication-common) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common) | -|
| 标识 | [npm](https://www.npmjs.com/package/@azure/communication-identity) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Identity)| [PyPi](https://pypi.org/project/azure-communication-identity/)| [Maven](https://search.maven.org/search?q=a:azure-communication-identity) | -| -| -|
| 电话号码 | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.PhoneNumbers)| [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)| [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers) | -| -| -|
| 聊天 | [npm](https://www.npmjs.com/package/@azure/communication-chat)| [NuGet](https://www.NuGet.org/packages/Azure.Communication.Chat) | [PyPi](https://pypi.org/project/azure-communication-chat/) | [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | -|
| SMS| [npm](https://www.npmjs.com/package/@azure/communication-sms) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Sms)| [PyPi](https://pypi.org/project/azure-communication-sms/) | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms) | -| -| -|
| 调用| [npm](https://www.npmjs.com/package/@azure/communication-calling) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Calling) | -| - | [GitHub](https://github.com/Azure/Communication/releases) | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)| -|
|通话自动化||[NuGet](https://www.NuGet.org/packages/Azure.Communication.CallingServer/)||[Maven](https://search.maven.org/artifact/com.azure/azure-communication-callingserver)
|Network Traversal| [npm](https://www.npmjs.com/package/@azure/communication-network-traversal)|[NuGet](https://www.NuGet.org/packages/Azure.Communication.NetworkTraversal/)
| UI 库| [npm](https://www.npmjs.com/package/@azure/communication-react) | - | - | - | - | - | [GitHub](https://github.com/Azure/communication-ui-library)、[Storybook](https://azure.github.io/communication-ui-library/?path=/story/overview--page) |
| 参考文档 | [docs](https://azure.github.io/azure-sdk-for-js/communication.html) | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)| -| [docs](http://azure.github.io/azure-sdk-for-java/communication.html) | [docs](/objectivec/communication-services/calling/)| [docs](/java/api/com.azure.android.communication.calling)| -|

友元程序集名称与命名空间之间的映射为：

| Assembly | 命名空间 |
|------------------------|--------------------------------------|
| Azure 资源管理器 | Azure.ResourceManager.Communication|
| 通用 | Azure.Communication.Common |
| 标识 | Azure.Communication.Identity |
| 电话号码| Azure.Communication.PhoneNumbers |
| SMS| Azure.Communication.SMS|
| 聊天 | Azure.Communication.Chat |
| 调用| Azure.Communication.Calling|
| 呼叫服务器 | Azure.Communication.CallingServer|
| Network Traversal| Azure.Communication.NetworkTraversal |
| UI 库 | Azure.Communication.Calling|


## <a name="rest-api-throttles"></a>REST API 限制
某些 REST API 和相应的 SDK 方法具有应该注意的限制。 超过这些限制会触发 `429 - Too Many Requests` 错误响应。 可以通过 [对 Azure 支持的请求](../../azure-portal/supportability/how-to-create-azure-support-request.md) 提高这些限制。

| API| 限制|
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [所有搜索电话号码计划 API](/rest/api/communication/phonenumbers) | 4 个请求/天|
| [购买电话号码计划](/rest/api/communication/phonenumbers/purchasephonenumbers) | 1 次购买/月|
| [发送短信](/rest/api/communication/sms/send) | 200 个请求/分钟 |


## <a name="sdk-platform-support-details"></a>SDK 平台支持详细信息

### <a name="ios-and-android"></a>iOS 和 Android

- 通信服务 iOS SDK 面向 iOS 版本 13+ 和 Xcode 11+。
- Android Java SDK 面向 Android API 级别 21+ 和 Android Studio 4.0+

### <a name="net"></a>.NET

通信服务包面向支持下列平台的 .NET Standard 2.0，呼叫除外。

通过 .NET Framework 4.6.1 提供的支持
- Windows 10、8.1、8 和 7
- Windows Server 2012 R2、2012 和 2008 R2 SP1

通过 .NET Core 2.0 提供的支持：
- Windows 10 (1607+)、7 SP1+、8.1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12+
- Linux 的多个版本/发行版
- UWP 10.0.16299 (RS3) 2017 年 9 月版
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

在以下系统上，呼叫包支持 .NET Native 或 C++/WinRT 生成的 UWP 应用：
- Windows 10 10.0.17763
- Windows Server 2019 10.0.17763

## <a name="api-stability-expectations"></a>API 稳定性预期

> [!IMPORTANT]
> 此部分提供标记为“稳定”的 REST API 和 SDK 的指导。 可能会 **在未通知的情况下** 更改或弃用标记为预发布版、预览版或 beta 版的 API。

将来，我们可能会停用通信服务 SDK 的版本，并且可能会引入 REST API 和已发布 SDK 的中断性变更。 Azure 通信服务通常会按照两个可支持性策略来停用服务版本：

- 如果我们因为通信服务接口更改而要求你更改代码，则至少会提前三年通知你。 通常情况下，在停用接口之前，至少会提前三年发出所有已记录 REST API 和 SDK API 的警告。
- 我们在要求你将 SDK 程序集更新到最新次要版本之前，至少会提前一年通知你。 这些必需的更新不会要求更改代码，因为它们都属于同一个主版本。 对于所含实时组件经常需要安全和性能更新的呼叫和聊天库，使用最新的 SDK 尤为重要。 强烈建议经常更新所有通信服务 SDK。

### <a name="api-and-sdk-decommissioning-examples"></a>API 和 SDK 停用示例

**你已将短信 REST API 的 v24 版本集成到应用程序中。Azure 通信版本 v25。**

我们在停用这些 API 并强制你将其更新到 v25 之前，会提前三年向你发出警告。 此更新可能需要进行代码更改。

“你已将呼叫 SDK 的 v2.02 版本集成到应用程序中。Azure 通信版本 v2.05。”

你可能需要在 v2.05 版的呼叫 SDK 发布之后的 12 个月内更新到 v2.05 版。 这只需将项目简单替换一下就可以了，不需更改代码，因为 v2.05 属于 v2 主版本，没有中断性变更。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下 SDK 概述：

- [呼叫 SDK 概述](../concepts/voice-video-calling/calling-sdk-features.md)
- [聊天 SDK 概述](../concepts/chat/sdk-features.md)
- [短信 SDK 概述](../concepts/telephony-sms/sdk-features.md)

若要开始使用 Azure 通信服务，请执行以下步骤：

- [创建 Azure 通信服务资源](../quickstarts/create-communication-resource.md)
- 生成[用户访问令牌](../quickstarts/access-tokens.md)
