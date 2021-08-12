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
ms.openlocfilehash: cc6264e00cb627815f4008f147266357c625b72a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201125"
---
适用于 Android 的 Azure 通信服务群组通话主图示例演示了如何使用通信服务通话 Android SDK 来创造一个包括语音和视频的群组通话体验。 在这篇示例快速入门中，你将了解如何设置和运行该示例。 针对上下文提供了此示例的概述。

## <a name="download-code"></a>下载代码

在 [GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero) 上查找此示例项目。 可在单独的[分支](https://github.com/Azure-Samples/communication-services-android-calling-hero/tree/feature/teams_interop)中找到具有 [Teams 互操作](../../concepts/teams-interop.md)的示例版本。

## <a name="overview"></a>概述

此示例是一款原生 Android 应用程序，它使用 Azure 通信服务 Android SDK 来创造一个兼具语音和视频通话功能的通话体验。 该应用程序使用服务器端组件来预配访问令牌，这些令牌随后用于初始化 Azure 通信服务 SDK。 若要配置该服务器端组件，可随时按照[使用 Azure Functions 的受信任服务](../../tutorials/trusted-service-tutorial.md)教程操作。

该示例如下所示：

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="显示示例应用程序登陆页面的屏幕截图。":::

按下“开始新通话”按钮时，Android 应用程序将创建一个新通话并加入。 借助该应用程序，还可通过指定现有通话的 ID 来加入现有的 Azure 通信服务通话。

加入通话后，系统将提示你向该应用程序授予访问相机和麦克风的权限。 系统还将要求你提供显示名称。

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="显示示例应用程序呼叫前屏幕的屏幕截图。":::

配置显示名称和设备后，即可加入通话。 你将看到核心通话体验所在的主要通话画布。

:::image type="content" source="../media/calling/main-app-android.png" alt-text="显示示例应用程序主屏幕的屏幕截图。":::

主要呼叫屏幕的各个组件：

- **媒体库**：显示参与者的主要阶段。 如果参与者启用了摄像头，则会在此处显示其视频源。 每位参与者都有一个单独的磁贴，上面显示了该参与者的显示名称和视频流（如果有）。 库支持每位参与者，并在通话中添加或删除参与者时进行更新。
- **操作栏**：这里有主要通话控件。 通过这些控件，你可打开/关闭视频和麦克风、共享屏幕，还可退出通话。

下面你将找到有关设置该示例的先决条件和步骤的详细信息。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 计算机上运行的 [Android Studio](https://developer.android.com/studio)
- 一个 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../quickstarts/create-communication-resource.md)。
- 运行[身份验证终结点](../../tutorials/trusted-service-tutorial.md)以获取访问令牌的 Azure Function。

## <a name="running-sample-locally"></a>在本地运行示例

可使用 Android Studio 在本地运行群组通话示例。 开发人员可使用其物理设备或模拟器来测试该应用程序。

### <a name="before-running-the-sample-for-the-first-time"></a>第一次运行示例之前

1. 打开 Android Studio，然后选择“`Open an Existing Project`”
2. 在 示例的下载版本内打开 `AzureCalling` 文件夹。
3. 展开要更新 `appSettings.properties` 的应用程序/资产。 将 `communicationTokenFetchUrl` 密钥的值设置为身份验证终结点（前提条件）的 URL。

### <a name="run-sample"></a>运行示例

在 Android Studio 中构建并运行示例。

## <a name="optional-securing-an-authentication-endpoint"></a>（可选）保护身份验证终结点

出于演示目的，此示例在默认情况下使用可公开访问的终结点来获取 Azure 通信服务令牌。 对于生产方案，建议使用你自己的安全终结点来预配你自己的令牌。

通过其他配置，此示例支持连接到受 Azure Active Directory (Azure AD) 保护的终结点，因此应用需要用户登录才能获取 Azure 通信服务令牌。 请查看以下步骤：

1. 在应用中启用 Azure Active Directory 身份验证。  
   - [在 Azure Active Directory 下注册应用程序（使用 Android 平台设置）](../../../active-directory/develop/tutorial-v2-android.md) 
    - [将应用服务或 Azure Functions 应用配置为使用 Azure AD 登录](../../../app-service/configure-authentication-provider-aad.md)

2. 转到 Azure Active Directory 应用注册下的注册应用概述页面。 记下 `Package name`、`Signature hash` 和 `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure 门户上的 Azure Active Directory 配置。":::

3. 编辑 `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` 并设置 `isAADAuthEnabled` 以启用 Azure Active Directory
4. 编辑 `AndroidManifest.xml` 并设置 `android:path` 为密钥存储签名哈希。 （可选。 当前值使用捆绑的 debug.keystore 中的哈希。 如果使用不同的密钥存储，则必须对其进行更新。
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. 从 Azure 门户复制 MSAL Android 配置并粘贴到 `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`。 包括 "account_mode" : "SINGLE"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. 编辑 `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`，并将 `communicationTokenFetchUrl` 密钥的值设置为安全身份验证终结点的 URL。
7. 编辑 `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` 并为 `Azure Active Directory` `Expose an API` 作用域中的 `aadScopes` 密钥设置值

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../../quickstarts/create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[从 GitHub 下载示例](https://github.com/Azure-Samples/communication-services-android-calling-hero)

有关详细信息，请参阅以下文章：

- 自行熟悉如何[使用通话 SDK](../../quickstarts/voice-video-calling/calling-client-samples.md)
- 详细了解[呼叫工作原理](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>其他阅读材料

- [Azure 通信 GitHub](https://github.com/Azure/communication) - 在官方 GitHub 页上查找更多示例和信息
- [示例](./../overview.md) - 在示例概述页上查找更多示例。
- [Azure 通信通话功能](../../concepts/voice-video-calling/calling-sdk-features.md)：如欲了解有关通话 Android SDK 的详细信息，请参阅 [Azure 通信 Android 通话 SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)