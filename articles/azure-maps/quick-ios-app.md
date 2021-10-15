---
title: 创建 iOS 应用
description: 创建 Azure Maps 帐户和第一个 iOS 应用的步骤。
author: deniseatmicrosoft
ms.author: v-dbogomolov
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 46e5ec41c0cea9a417b920e8441ff1129040c209
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389601"
---
#  <a name="create-an-ios-app-public-preview"></a>创建 iOS 应用（公共预览版）

本文介绍如何将 Azure Maps 添加到 iOS 应用。 本文演示如何完成以下基本步骤：

* 设置开发环境。
* 创建自己的 Azure Maps 帐户。
* 获取主 Azure Maps 密钥以在应用中使用。
* 从项目中引用 Azure Maps 库。
* 将 Azure Maps 控件添加到应用。

## <a name="prerequisites"></a>先决条件

* 通过登录到 [Azure 门户](https://portal.azure.com/)来创建 Azure Maps 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
* [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)。
* 免费下载 [Mac App Store 上的 Xcode](https://apps.apple.com/cz/app/xcode/id497799835?mt=12)。

## <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

通过以下步骤创建新的 Azure Maps 帐户：

1. 在 [Azure 门户](https://portal.azure.com/)的左上角单击“创建资源”。

2. 在“在市场中搜索”框中，键入“Azure Maps”。

3. 从“结果”中，选择“Azure Maps”。 单击地图下面显示的“创建”按钮。

4. 在“创建 Maps 帐户”页上，输入以下值  ：
   - 要用于此帐户的订阅。
   - 此帐户的资源组名称。 可以选择新建或使用现有的资源组。
   - 新帐户的名称。
   - 此帐户的定价层  。
   - 阅读许可证和隐私声明，并选择复选框接受这些条款   。
   - 单击“创建”  按钮。

   :::image source="./media/quick-ios-app/create-account.png" alt-text="创建 Azure Maps 帐户的屏幕截图。":::

## <a name="get-the-primary-key-for-your-account"></a>获取帐户的主密钥

成功创建 Maps 帐户后，检索查询 Maps API 的主密钥。

1. 在门户中打开 Maps 帐户。

2. 在设置部分中，选择“身份验证”  。

3. 将“主密钥”复制到剪贴板。 本地保存它以便稍后在本教程中使用。

   > [!Note]
   > 如果使用 Azure 订阅密钥而不是 Azure Maps 主密钥，那么不会正确显示你的映射。 此外，出于安全考虑，建议轮换使用主密钥和辅助密钥。 若要轮换密钥，请更新应用以使用辅助密钥、进行部署，然后按主密钥旁边的循环/刷新按钮以生成新的主密钥。 将禁用旧的主密钥。 有关密钥轮换的详细信息，请参阅[使用密钥轮换和审核功能设置 Azure Key Vault](../key-vault/secrets/tutorial-rotation-dual.md)

   :::image source="./media/quick-ios-app/get-key.png" alt-text="获取订阅密钥的屏幕截图。":::

## <a name="create-a-project-in-xcode"></a>在 Xcode 中创建项目

首先，创建新的 iOS 应用项目。 请完成以下步骤来创建 Xcode 项目：

1. 在“文件”下，选择“新建” -> “项目”  。

2. 在“iOS”选项卡上，依次选择“应用”和“下一步”  。

3. 输入应用名称、捆绑 ID，然后选择“下一步”。

   请参阅[为应用创建 Xcode 项目](https://developer.apple.com/documentation/xcode/creating-an-xcode-project-for-an-app)，获取有关创建新项目的更多帮助。

:::image source="./media/quick-ios-app/create-app.png" alt-text="创建第一个 iOS 应用程序。":::

## <a name="install-the-azure-maps-ios-sdk"></a>安装 Azure Maps iOS SDK

生成应用程序的下一步是安装 Azure Maps iOS SDK。 请完成以下步骤来安装该 SDK：

1. 在“项目导航器”中选择项目文件

2. 在打开的“项目编辑器”中选择项目

3. 切换到“Swift 包”选项卡

4. 添加 Azure Maps iOS SDK：`{link goes here}`

   :::image source="./media/quick-ios-app/add-project.png" alt-text="添加 iOS 项目的屏幕截图。":::
  
## <a name="add-mapcontrol-view"></a>添加 MapControl 视图

1. 添加自定义 `UIView` 以查看控制器

2. 从 `AzureMapsControl` 模块中选择 `MapControl` 类

   :::image source="./media/quick-ios-app/add-map-control.png" alt-text="添加 Azure Maps 控件的屏幕截图。":::

3. 在 AppDelegate.swift 文件中，需要执行以下操作：

   - 添加 Azure Maps SDK 的导入。
   - 设置 Azure Maps 身份验证信息。
   
   使用 `AzureMaps.configure(subscriptionKey:)` 或 `AzureMaps.configure(aadClient:aadAppId:aadTenant:)` 方法在 AzureMaps 类上全局设置身份验证信息后，无需在每个视图上添加身份验证信息。

4. 如下图所示，选择“运行”按钮（或按 `CMD` + `R`）来生成应用程序。

   :::image source="./media/quick-ios-app/run.png" alt-text="运行 iOS 应用程序的屏幕截图。":::

   Xcode 将花费几秒钟时间来生成应用程序。 生成完成后，可在 iOS 仿真设备中测试应用程序。 应会看到如下所示的地图：

   :::image source="./media/quick-ios-app/example.png" alt-text="iOS 应用程序上第一个地图的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

> [!WARNING]
> [后续步骤](#next-steps)部分中列出的教程详细介绍了如何通过帐户使用和配置 Azure Maps。 如何打算继续学习这些教程，请勿清除本快速入门中创建的资源。

如果不打算继续学习这些教程，请通过以下步骤来清理资源：

1. 关闭 Xcode 并删除创建的项目。
2. 如果在外部设备上测试了应用程序，请从该设备上卸载它。

如果不打算继续使用 Azure Maps iOS SDK 进行开发，请执行以下操作：

1. 导航到 Azure 门户页面。 选择门户主页中的“所有资源”。 或者，单击左上角的菜单图标。 选择“所有资源”，
2. 单击你的 Azure Maps 帐户。 在页面顶部，单击“删除”。
3. 如果不打算继续开发 iOS 应用，请卸载 Xcode。

<!--
For more code examples, see these guides:

*  [Manage authentication in Azure Maps](how-to-manage-authentication.md)
*  [Change map styles in iOS maps](Set%20map%20style%20%28iOS%20SDK%29.md)
*  [Add a symbol layer](Add%20a%20symbol%20layer%20%28iOS%20SDK%29.md)
*  [Add a line layer](Add%20a%20line%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
*  [Add a polygon layer](Add%20a%20polygon%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
-->

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了 Azure Maps 帐户和演示版应用程序。 请查看以下教程，详细了解 Azure Maps：

> [!div class="nextstepaction"]
> [将 GeoJSON 数据加载到 Azure Maps 中](tutorial-load-geojson-file-android.md)
