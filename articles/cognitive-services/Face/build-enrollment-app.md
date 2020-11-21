---
title: 构建适用于 Android 的注册应用并做出反应
titleSuffix: Azure Cognitive Services
description: 了解如何设置开发环境并部署面部注册应用，以获得客户的同意。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: bd2032d565f5bd1fb430449be8b8c08e222f531d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025728"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>构建适用于 Android 的注册应用并做出反应

本指南将演示如何开始处理示例人脸注册应用程序。 该应用程序演示了获取有意义的许可的最佳实践，以便将用户注册到面部识别服务并获取高准确性的人脸数据。 集成系统可以使用类似于这样的注册应用，基于其面部数据提供 touchless 访问控制、身份验证、出勤跟踪、个性化展台或身份验证。

当启动时，应用程序会向用户显示一个详细的许可屏幕。 如果用户同意，则应用会提示输入用户名和密码，然后使用设备的相机捕获高质量的人脸图像。

示例注册应用是使用 JavaScript 编写的，并对本机框架进行了响应。 它当前可以部署在 Android 设备上;未来将提供更多部署选项。

## <a name="prerequisites"></a>先决条件 

* Azure 订阅– [免费创建一个](https://azure.microsoft.com/free/cognitive-services/)。  
* 获得 Azure 订阅后，请在 Azure 门户中 [创建一个人脸资源](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) ，以获取密钥和终结点。 部署后，选择”转到资源”。  
  * 你将需要已创建的资源的密钥和终结点，以便将应用程序连接到人脸 API。  
  * 对于本地开发和测试，你可以将 API 密钥和终结点粘贴到配置文件中。 对于最终部署，请将 API 密钥存储在安全位置，而不是在代码中。  

> [!IMPORTANT]
> 这些订阅密钥用于访问认知服务 API。 不要共享你的密钥。 安全地存储它们，例如，使用 Azure Key Vault。 此外，我们建议定期重新生成这些密钥。 发出 API 调用只需一个密钥。 重新生成第一个密钥时，可以使用第二个密钥来持续访问服务。

## <a name="set-up-the-development-environment"></a>设置开发环境

1. 克隆 [示例注册应用](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)的 git 存储库。
1. 若要设置开发环境，请按照响应本机文档中的说明进行操作 <a href="https://reactnative.dev/docs/environment-setup"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。 选择 " **响应本机 CLI 快速入门** " 作为开发操作系统，选择 " **Android** " 作为 "目标 OS"。 完成 **安装依赖项** 和 **Android 开发环境** 部分。
1. 在首选的文本编辑器（如 [Visual Studio Code](https://code.visualstudio.com/)）中打开 env.js文件，然后添加终结点和密钥。 可以在资源的 " **概述** " 选项卡下的 Azure 门户中获取终结点和密钥。 此步骤仅用于本地测试，而 &mdash; 不会将人脸 API 密钥签入到远程存储库。
1. 使用 Android Studio 中的 Android 虚拟设备模拟器或你自己的 Android 设备运行应用。 若要在物理设备上测试应用，请遵循相关的 <a href="https://reactnative.dev/docs/running-on-device"  title=" 响应本机文档来 "  target="_blank"> 响应本机文档 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。  


## <a name="create-an-enrollment-experience"></a>创建注册体验  

现在，你已设置了示例注册应用程序，你可以根据自己的注册体验进行定制。

例如，你可能想要在同意页上添加特定情况的相关信息：

> [!div class="mx-imgBorder"]
> ![应用许可页](./media/enrollment-app/1-consent-1.jpg)

此服务提供图像质量检查，以帮助您选择图像的质量是否足以注册客户或尝试人脸识别。 此应用程序演示如何从设备的照相机访问帧，选择最高质量的帧，并将检测到的人脸注册到人脸 API 服务。 

许多人脸识别问题都是由低质量的参考图像导致的。 可能降低模型性能的一些因素包括：
* 远离相机 (面部的面部尺寸) 
* 面部方向 (正面旋转或倾斜) 
* 光源 (低光或背光) ，图像可能会受到不良或干扰太多
* 封闭 (部分隐藏或阻碍的人脸) ，其中包括诸如帽子或个交通眼镜等附件) 
* 在) 拍摄照片时， (如快速的面部运动。 

> [!div class="mx-imgBorder"]
> ![应用映像捕获指令页](./media/enrollment-app/4-instruction.jpg)

请注意，该应用还提供了用于删除用户注册的功能，以及用于重新注册的选项。

> [!div class="mx-imgBorder"]
> ![配置文件管理页](./media/enrollment-app/10-manage-2.jpg)

若要扩展应用的功能以涵盖完整的注册体验，请阅读 [概述](enrollment-overview.md) ，了解要实现的其他功能和最佳实践。

## <a name="deploy-the-enrollment-app"></a>部署注册应用

### <a name="android"></a>Android

首先，请确保应用已准备好进行生产部署：从应用代码中删除任何密钥或机密，并确保遵循 [最佳安全方案](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp)。

当你准备好发布应用以进行生产时，将生成一个可供发布的 APK 文件，该文件是适用于 Android 应用的程序包文件格式。 此 APK 文件必须使用私钥进行签名。 在此发布版本中，你可以开始直接将应用分发到你的设备。 

按照准备发布的准备发布 <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 文档来了解如何生成私钥、签署应用程序以及生成发布 APK。  

创建已签名的 APK 后，请参阅发布你的应用程序 <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> 文档， <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 了解有关如何发布应用的详细信息。

## <a name="next-steps"></a>后续步骤  

在本指南中，您学习了如何设置开发环境并开始示例注册应用。 如果你不熟悉如何对本机做出响应，可以阅读其 [入门文档](https://reactnative.dev/docs/getting-started) 来了解更多背景信息。 它还有助于熟悉 [人脸 API](Overview.md)。 开始开发之前，请阅读注册应用文档中的其他部分。
