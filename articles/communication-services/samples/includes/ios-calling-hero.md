---
title: 包含文件
description: include 文件
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: 287c7c2cdc709f4026ddd4dfa744119568b7950c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114200894"
---
适用于 iOS 的 Azure 通信服务群组通话主图示例演示了如何使用通信服务通话 iOS SDK 来创造一个包括语音和视频的群组通话体验。 在这篇示例快速入门中，你将了解如何设置和运行该示例。 针对上下文提供了此示例的概述。

## <a name="download-code"></a>下载代码

在 [GitHub](https://github.com/Azure-Samples/communication-services-ios-calling-hero) 上查找此示例项目。 可在单独的[分支](https://github.com/Azure-Samples/communication-services-ios-calling-hero/tree/feature/teams_interop)中找到具有 [Teams 互操作](../../concepts/teams-interop.md)的示例版本。

## <a name="overview"></a>概述

此示例是一款原生 iOS 应用程序，该应用程序使用 Azure 通信服务 iOS SDK 来创造一个包括语音和视频通话功能的通话体验。 该应用程序使用服务器端组件来预配访问令牌，这些令牌随后用于初始化 Azure 通信服务 SDK。 若要配置该服务器端组件，可随时按照[使用 Azure Functions 的受信任服务](../../tutorials/trusted-service-tutorial.md)教程操作。

该示例如下所示：

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="显示示例应用程序登陆页面的屏幕截图。":::

按下“开始新通话”按钮时，iOS 应用程序将创建一个新通话并加入其中。 借助该应用程序，你可通过指定现有通话的 ID 来加入现有的 Azure 通信服务通话。

加入通话后，系统将提示你向该应用程序授予访问相机和麦克风的权限。 系统还将要求你提供显示名称。

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="显示示例应用程序呼叫前屏幕的屏幕截图。":::

配置显示名称和设备后，即可加入通话。 你将看到核心通话体验所在的主要通话画布。

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="显示示例应用程序主屏幕的屏幕截图。":::

主要呼叫屏幕的各个组件：

- **媒体库**：显示参与者的主要阶段。 如果参与者启用了摄像头，则会在此处显示其视频源。 每位参与者都有一个单独的磁贴，上面显示了该参与者的显示名称和视频流（如果有）。 库支持每位参与者，并在通话中添加或删除参与者时进行更新。
- **操作栏**：这里有主要通话控件。 通过这些控件，你可打开/关闭视频和麦克风、共享屏幕，还可退出通话。

下面你将找到有关设置该示例的先决条件和步骤的详细信息。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一部运行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及一个安装到密钥链的有效开发人员证书。
- 一个 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../quickstarts/create-communication-resource.md)。
- 运行[身份验证终结点](../../tutorials/trusted-service-tutorial.md)以获取访问令牌的 Azure Function。

## <a name="running-sample-locally"></a>在本地运行示例

可使用 XCode 在本地运行群组通话示例。 开发人员可使用其物理设备或模拟器来测试该应用程序。

### <a name="before-running-the-sample-for-the-first-time"></a>第一次运行示例之前

1. 通过运行 `pod install` 安装依赖项。
2. 在 XCode 中打开 `AzureCalling.xcworkspace`。
3. 更新 `AppSettings.plist`。 将 `communicationTokenFetchUrl` 密钥的值设置为身份验证终结点的 URL。

### <a name="run-sample"></a>运行示例

在 XCode 中生成并运行示例。

## <a name="optional-securing-an-authentication-endpoint"></a>（可选）保护身份验证终结点

为了进行演示，此示例在默认情况下使用可公开访问的终结点来获取 Azure 通信服务访问令牌。 对于生产方案，建议使用你自己的安全终结点来预配你自己的令牌。

通过其他配置，此示例支持连接到受 Azure Active Directory (Azure AD) 保护的终结点，因此用户需要登录应用程序才能获取 Azure 通信服务访问令牌。 请查看以下步骤：

1. 在应用中启用 Azure Active Directory 身份验证。  
   - [在 Azure Active Directory 下注册应用（使用 iOS/macOS 平台设置）](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [将应用服务或 Azure Functions 应用配置为使用 Azure AD 登录](../../../app-service/configure-authentication-provider-aad.md)
2. 转到 Azure Active Directory 应用注册下的注册应用概述页面。 记下 `Application (client) ID`、`Directory (tenant) ID` 和 `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure 门户上的 Azure Active Directory 配置。":::

3. 在 Xcode 中打开 `AppSettings.plist`，再添加以下键值：
   - `communicationTokenFetchUrl`：用于请求 Azure 通信服务令牌的 URL 
   - `isAADAuthEnabled`：一个布尔值，用于指示是否需要 Azure 通信服务令牌身份验证
   - `aadClientId`：你的应用程序（客户端）ID
   - `aadTenantId`：你的目录（租户）ID
   - `aadRedirectURI`：重定向 URI 的格式应为：`msauth.<app_bundle_id>://auth`
   - `aadScopes`：从用户请求授权的权限范围数组。 将 `<Application ID URI>/user_impersonation` 添加到数组中来授予对身份验证终结点的访问权限

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../../quickstarts/create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[从 GitHub 下载示例](https://github.com/Azure-Samples/communication-services-ios-calling-hero)

有关详细信息，请参阅以下文章：

- 自行熟悉如何[使用通话 SDK](../../quickstarts/voice-video-calling/calling-client-samples.md)
- 详细了解[呼叫工作原理](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>其他阅读材料

- [Azure 通信 GitHub](https://github.com/Azure/communication) - 在官方 GitHub 页上查找更多示例和信息
- [示例](./../overview.md) - 在示例概述页上查找更多示例。
- [Azure 通信通话功能](../../concepts/voice-video-calling/calling-sdk-features.md)：如欲了解有关通话 iOS SDK 的详细信息，请参阅 [Azure 通信 iOS 通话 SDK](https://github.com/Azure/Communication/releases/)