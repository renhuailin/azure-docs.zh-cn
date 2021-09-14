---
title: 生成反应应用以将用户添加到人脸服务
titleSuffix: Azure Cognitive Services
description: 了解如何设置开发环境并部署人脸应用以获得客户的同意。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: db6ee292a088d530ea858d75e6649684e03d92b6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540471"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>生成反应应用以将用户添加到人脸服务

本指南将演示如何开始使用示例人脸注册应用程序。 该应用演示了有关获得有意义的同意，以便将用户添加到人脸识别服务并获取高准确度人脸数据的最佳做法。 集成系统可以使用诸如此类的应用，基于用户的人脸数据提供无接触的访问控制、身份验证、出勤跟踪或个性化展台。

启动时，应用程序会向用户显示一个详细的同意屏幕。 如果用户同意，则应用会提示用户输入用户名和密码，然后使用设备的相机捕获高质量的人脸图像。

该示例应用使用 JavaScript 和 React Native 框架编写而成。 它当前可以部署到 Android 和 iOS 设备上；将来将提供更多部署选项。

## <a name="prerequisites"></a>先决条件 

* Azure 订阅 – [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)。  
* 拥有 Azure 订阅后，在 Azure 门户中[创建人脸资源](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace)，获取密钥和终结点。 部署后，选择”转到资源”。  
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到人脸 API。  
  * 仅对于本地开发和测试，API 密钥和终结点是环境变量。 对于最终部署，请将 API 密钥存储在一个安全位置，而不要存储在代码或环境变量中。  

### <a name="important-security-considerations"></a>重要的安全注意事项
* 对于本地开发和初始有限测试，使用环境变量保存 API 密钥和终结点是可接受的（虽然不是最佳做法）。 对于试点和最终部署，应安全地存储 API 密钥，这可能涉及使用中间服务来验证登录期间生成的用户令牌。 
* 不要在代码中存储 API 密钥或终结点，也不要将它们提交给版本控制系统（如 Git）。 如果错误地出现了这种情况，你应立即生成新的 API 密钥/终结点，并撤消以前的密钥/终结点。
* 最佳做法是考虑为开发和生产使用单独的 API 密钥。

## <a name="set-up-the-development-environment"></a>设置开发环境

#### <a name="android"></a>[Android](#tab/android)
 
1. 克隆[示例应用](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)的 git 存储库。
1. 若要设置开发环境，请按照 <a href="https://reactnative.dev/docs/environment-setup"  title="React Native 文档"  target="_blank">React Native 文档<span class="docon docon-navigate-external x-hidden-focus"></span></a>进行操作。 选择“React Native CLI 快速入门”。 选择你的开发 OS，并选择“Android”作为目标 OS。 完成“安装依赖项”和“Android 开发环境”部分 。
1. 下载你喜欢的文本编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)。
1. 在 Azure 门户中资源的“概述”选项卡下检索 FaceAPI 终结点和密钥。 请勿将人脸 API 密钥签入到远程存储库。
1. 使用 Android Studio 中的 Android 虚拟设备仿真器或你自己的 Android 设备运行应用。 若要在物理设备上测试应用，请按照相关 <a href="https://reactnative.dev/docs/running-on-device"  title="React Native 文档"  target="_blank">React Native 文档<span class="docon docon-navigate-external x-hidden-focus"></span></a>进行操作。

#### <a name="ios"></a>[iOS](#tab/ios)

1. 克隆[示例应用](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)的 git 存储库。
1. 若要设置开发环境，请按照 <a href="https://reactnative.dev/docs/environment-setup"  title="React Native 文档"  target="_blank">React Native 文档<span class="docon docon-navigate-external x-hidden-focus"></span></a>进行操作。 选择“React Native CLI 快速入门”。 选择“macOS”作为开发 OS，并选择“iOS”作为目标 OS 。 完成“安装依赖项”部分。
1. 下载你喜欢的文本编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)。 还需要下载 Xcode。 
1. 在 Azure 门户中资源的“概述”选项卡下检索 FaceAPI 终结点和密钥。 请勿将人脸 API 密钥签入到远程存储库。
1. 使用 Xcode 中的模拟设备或自己的 iOS 设备运行应用。 若要在物理设备上测试应用，请按照相关 <a href="https://reactnative.dev/docs/running-on-device"  title="React Native 文档"  target="_blank">React Native 文档<span class="docon docon-navigate-external x-hidden-focus"></span></a>进行操作。

---

## <a name="create-a-user-add-experience"></a>创建用户添加体验  

现在，你已设置示例应用，接着可根据自己的需求对其进行调整。

例如，你可能想要在同意页上添加特定情况的信息：

> [!div class="mx-imgBorder"]
> ![应用同意页](./media/enrollment-app/1-consent-1.jpg)

许多人脸识别问题都是低质量的参考图像所致。 下面是一些会降低模型性能的因素：
* 人脸大小（人脸离相机很远）
* 人脸方向（人脸转过相机或向远离相机的方向倾斜）
* 光照条件差（低光或背光），在此情况下，图像可能曝光较差或噪点过多
* 遮挡（人脸被部分隐藏或遮挡，包括帽子或厚框眼镜等配饰）
* 模糊（例如在拍照时人脸迅速移动）。 

此服务提供图像质量检查，帮助你根据上述因素确定图像的质量是否足以用于添加客户或尝试人脸识别。 此应用演示如何从设备的相机访问帧、检测质量并向用户显示用户界面消息，以帮助他们捕获更高质量的图像，选择最高质量的帧，并将检测到的人脸添加到人脸 API 服务中。 


> [!div class="mx-imgBorder"]
> ![应用图像捕获说明页](./media/enrollment-app/4-instruction.jpg)

请注意，该应用还提供了用于删除用户信息的功能，以及用于重新添加的选项。

> [!div class="mx-imgBorder"]
> ![配置文件管理页](./media/enrollment-app/10-manage-2.jpg)

若要扩展应用的功能以涵盖完整的体验，请阅读[概述](enrollment-overview.md)，了解要实现的其他功能以及最佳做法。

## <a name="deploy-the-app"></a>部署应用

#### <a name="android"></a>[Android](#tab/android)

首先，确保应用已准备好进行生产部署：从应用代码中删除任何密钥或机密，并确保已遵循[安全最佳做法](../cognitive-services-security.md?tabs=command-line%2ccsharp)。

在准备好发布应用以进行生产时，你将生成一个可随时发布的 APK（适用于 Android 应用的包文件格式）文件。 必须使用私钥对此 APK 文件进行签名。 借助此发布版本，你可以开始将应用直接分发到设备。 

查看<a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title="准备发布"  target="_blank">准备发布<span class="docon docon-navigate-external x-hidden-focus"></span></a>文档，了解如何生成私钥、对应用程序进行签名以及生成发布 APK。  

创建已签名的 APK 后，请参阅<a href="https://developer.android.com/studio/publish"  title="发布应用"  target="_blank">发布应用<span class="docon docon-navigate-external x-hidden-focus"></span></a>文档，详细了解如何发布应用。

#### <a name="ios"></a>[iOS](#tab/ios)

首先，确保应用已准备好进行生产部署：从应用代码中删除任何密钥或机密，并确保已遵循[安全最佳做法](../cognitive-services-security.md?tabs=command-line%2ccsharp)。 若要准备分配，你需要创建一个应用图标和一个启动屏幕，并配置部署信息设置。 按照 [Xcode 的文档](https://developer.apple.com/documentation/Xcode/preparing_your_app_for_distribution)准备应用以进行分配。 

在准备好发布应用以进行生产时，你将生成应用的存档。 请参阅 [Xcode 文档](https://developer.apple.com/documentation/Xcode/distributing_your_app_for_beta_testing_and_releases)，了解如何创建存档版本和分配应用的选项。  

---

## <a name="next-steps"></a>后续步骤  

在本指南中，你学习了如何设置开发环境和开始使用示例应用。 如果你不熟悉 React Native，可以阅读相关[入门文档](https://reactnative.dev/docs/getting-started)，了解更多背景信息。 它还可以帮助你熟悉[人脸 API](Overview.md)。 在开始开发之前，请阅读有关添加用户的其他部分。
