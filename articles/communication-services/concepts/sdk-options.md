---
title: 适用于 Azure 通信服务的客户端库和 REST API
titleSuffix: An Azure Communication Services concept document
description: 详细了解 Azure 通信服务客户端库和 REST API。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 01a5f4a947f0b89b5881eddb3c743b9a9b184b19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495634"
---
# <a name="client-libraries-and-rest-apis"></a>客户端库和 REST API

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Azure 通信服务功能在概念上分为六个区域。 某些区域具有完全开源的客户端库。 呼叫客户端库使用专有网络接口，当前为闭源，而聊天库则包含闭源依赖项。 [Azure 通信服务 GitHub 存储库](https://github.com/Azure/communication)中发布了客户端库的示例和其他技术详细信息。

## <a name="client-libraries"></a>客户端库

| 程序集               | 协议             |开源和闭源| 命名空间                          | 功能                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure 资源管理器 | REST | 打开            | Azure.ResourceManager.Communication | 预配和管理通信服务资源             |
| 通用                 | REST | 打开               | Azure.Communication.Common          | 提供其他客户端库的基类型 |
| 标识         | REST | 打开               | Azure.Communication.Identity  | 管理用户、访问令牌 |
| 电话号码         | REST | 打开               | Azure.Communication.PhoneNumbers  | 管理电话号码 |
| 聊天                   | 使用专有信号的 REST | 开源（使用闭源信号包）    | Azure.Communication.Chat            | 向应用程序添加基于文本的实时聊天  |
| SMS                    | REST | 打开              | Azure.Communication.SMS             | 发送和接收短信 |
| 调用                | 专有传输 | 已关闭 |Azure.Communication.Calling         | 利用语音、视频、屏幕共享和其他实时数据通信功能          |

请注意，Azure 资源管理器、标识和短信客户端库侧重于服务集成。在许多情况下，如果将这些功能集成到最终用户应用程序中，则会出现安全问题。 公用客户端库和聊天客户端库适用于服务和客户端应用程序。 呼叫客户端库专用于客户端应用程序。 着重于服务方案的客户端库正在开发中。

### <a name="languages-and-publishing-locations"></a>语言和发布位置

下面详细介绍了各个客户端库包的发布位置。

| 领域           | JavaScript | .NET | Python | Java SE | iOS | Android | 其他                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure 资源管理器 | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [通过 GitHub 的 Go](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 通用         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | 不适用      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| 标识 | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| 电话号码 | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| 聊天           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| 调用        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| 参考文档     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>REST API

[docs.microsoft.com](/rest/api/azure/) 中介绍了通信服务 API 和其他 Azure REST API。 此文档将说明如何构造 HTTP 消息，并提供 Postman 使用指导。 此文档也在 [GitHub](https://github.com/Azure/azure-rest-api-specs) 上以 Swagger 格式提供。

## <a name="additional-support-details"></a>其他支持详细信息

### <a name="ios-and-android-support-details"></a>iOS 和 Android 支持详细信息

- 通信服务 iOS 客户端库面向 iOS 版本 13+ 和 Xcode 11+。
- Android Java 客户端库面向 Android API 级别 21+ 和 Android Studio 4.0+

### <a name="net-support-details"></a>.NET 支持详细信息

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

## <a name="calling-client-library-timeouts"></a>呼叫客户端库超时

以下超时适用于通信服务呼叫客户端库：

| 操作           | 超时（秒） |
| -------------- | ---------- |
| 重新连接/删除参与者 | 120 |
| 在调用中添加或删除新的模态（启动/停止视频或屏幕共享） | 40 |
| 呼叫转移操作超时 | 60 |
| 1:1 呼叫建立超时 | 85 |
| 组呼叫建立超时 | 85 |
| PSTN 呼叫建立超时 | 115 |
| 将 1:1 呼叫升级到组呼叫超时 | 115 |


## <a name="api-stability-expectations"></a>API 稳定性预期

> [!IMPORTANT]
> 此部分提供标记为“稳定”的 REST API 和客户端库的指导。 可能会 **在未通知的情况下** 更改或弃用标记为预发布版、预览版或 beta 版的 API。

将来，我们可能会停用通信服务客户端库的版本，并且可能会引入 REST API 和已发布客户端库的中断性变更。 Azure 通信服务通常会按照两个可支持性策略来停用服务版本：

- 如果我们因为通信服务接口更改而要求你更改代码，则至少会提前三年通知你。 通常情况下，在停止接口之前，至少会提前三年发出所有已记录 REST API 和客户端库 API 的警告。
- 我们在要求你将客户端库程序集更新到最新次要版本之前，至少会提前一年通知你。 这些必需的更新不会要求更改代码，因为它们都属于同一个主版本。 对于包含的实时组件经常需要安全更新和性能更新的呼叫库和聊天库，情况尤其如此。 强烈建议你经常更新通信服务客户端库。

### <a name="api-and-client-library-decommissioning-examples"></a>API 和客户端库停用示例

**你已将短信 REST API 的 v24 版本集成到应用程序中。Azure 通信版本 v25。**

我们在停用这些 API 并强制你将其更新到 v25 之前，会提前 3 年向你发出警告。 此更新可能需要进行代码更改。

**你已将呼叫客户端库的 v2.02 版本集成到应用程序中。Azure 通信版本 v2.05。**

你可能需要在 v2.05 版的呼叫客户端库发布之后的 12 个月内更新到 v2.05 版。 这只需将项目简单替换一下就可以了，不需更改代码，因为 v2.05 属于 v2 主版本，没有中断性变更。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下客户端库概述：

- [呼叫客户端库概述](../concepts/voice-video-calling/calling-sdk-features.md)
- [聊天客户端库概述](../concepts/chat/sdk-features.md)
- [短信客户端库概述](../concepts/telephony-sms/sdk-features.md)

若要开始使用 Azure 通信服务，请执行以下步骤：

- [创建 Azure 通信资源](../quickstarts/create-communication-resource.md)
- 生成[用户访问令牌](../quickstarts/access-tokens.md)
