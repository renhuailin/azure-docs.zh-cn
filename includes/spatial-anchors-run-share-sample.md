---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 9554e04c82299016076f09f85f604af10a94ab4a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185294"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android 示例支持跨设备共享。

在 Android Studio 中，打开示例文件夹中的 SharedActivity.java 文件。 

在 SharedActivity.java 文件中输入之前步骤（从 ASP.NET Web 应用 Azure 部署）中复制的 URL 作为 `SharingAnchorsServiceUrl` 的值。 

将 URL 中的 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS 示例支持跨设备共享。

打开示例文件夹中的“SharedDemoViewController.m”文件。 

在 SharedDemoViewController.m 文件中输入之前步骤（从 ASP.NET Web 应用 Azure 部署）中获得的 URL 作为 `SharingAnchorsServiceUrl` 的值。 

将 URL 中的 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

将应用部署到设备。 

应用启动后，选择“点击以启动共享演示”选项，然后遵循应用中的说明。 可以选择“点击以按定位点编号查找定位点”或“点击以创建定位点并将其保存到服务”。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android 和 iOS 示例都支持跨设备共享。

打开示例文件夹中的“AccountDetails.cs”文件。 

在 AccountDetails.cs 文件中输入之前步骤（从 ASP.NET Web 应用 Azure 部署）中获得的 URL 作为 `AnchorSharingServiceUrl` 的值。 

将 URL 中的 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>设置 Android 设备

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>设置 iOS 设备

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

在“项目”窗格中，转到 `Assets\AzureSpatialAnchors.Examples\Resources`。 

选择“SpatialAnchorSamplesConfig”。 然后，在“检查器”窗格中，输入 `Sharing Anchors Service` URL（来自 ASP.NET Web 应用 Azure 部署）作为 `Base Sharing Url` 的值。 将 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

选择“文件” > “保存”，从而保存场景 。

## <a name="deploy-to-your-device"></a>部署到设备

### <a name="deploy-to-an-android-device"></a>部署到 Android 设备

登录 Android 设备并使用 USB 线将其连接到计算机上。

通过选择“文件” > “生成设置”，打开“生成设置”    。

在“生成中的场景”下，确保每个场景旁边都有复选标记。

确保“导出项目”不显示复选标记。 单击“生成并运行”。 系统将提示你保存 .apk 文件。 你可为其选择任何名称。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>部署到 iOS 设备

通过选择“文件” > “生成设置”，打开“生成设置”    。

在“生成中的场景”下，确保每个场景旁边都有复选标记。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

在 Xcode 中，通过选择“停止”来停止应用。
