---
title: 快速入门：使用 DirectX 创建 HoloLens 应用
description: 此快速入门介绍如何使用 Object Anchors 生成 HoloLens 应用。
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: eee130b0736c87b118b38f19e7523c07a431e5c9
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202879"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>快速入门：在 C++/WinRT 和 DirectX 中使用 Azure Object Anchors 创建 HoloLens 应用

此快速指南介绍如何在 C++/WinRT 和 DirectX 中使用 [Azure Object Anchors](../overview.md) 创建 HoloLens 应用。 Azure Object Anchors 是一项托管的云服务，它将 3D 资产转换为 AI 模型，以便为 HoloLens 启用对象感知混合现实体验。 完成本教程后，你将拥有一个 HoloLens 应用，可在全息版 DirectX 11（通用 Windows）应用程序中检测对象及其姿势。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建和旁加载 HoloLens 应用程序
> * 检测对象并将其模型可视化

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

* 环境中存在一个物理对象及其 3D 模型（CAD 或扫描的）。
* 安装了以下内容的 Windows 计算机：
  * <a href="https://git-scm.com" target="_blank">用于 Windows 的 Git</a>
  * 包含“通用 Windows 平台开发”工作负荷和“Windows 10 SDK (10.0.18362.0 或更高版本)”组件的 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>
* 已启用[开发人员模式](/windows/mixed-reality/using-visual-studio#enabling-developer-mode)的最新 HoloLens 2 设备。
  * 若要在 HoloLens 上更新为最新版本，请打开“设置”应用，转到“更新和安全”，然后选择“检查更新”    。

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

## <a name="open-the-sample-project"></a>打开示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

在 Visual Studio 中打开 `quickstarts/apps/directx/DirectXAoaSampleApp.sln`。

将“解决方案配置”更改为“发布”，将“解决方案平台”更改为 ARM64，并从部署目标选项中选择“设备”    。

## <a name="configure-the-account-information"></a>配置帐户信息

接下来是将应用配置为使用你的帐户信息。 记下[“创建 Object Anchors 帐户”](#create-an-object-anchors-account)部分中的“帐户密钥”、“帐户 ID”和“帐户域”值  。

打开 `Assets\ObjectAnchorsConfig.json`。

找到 `AccountId` 字段，并将 `Set me` 替换为帐户 ID。

找到 `AccountKey` 字段，并将 `Set me` 替换为帐户密钥。

找到 `AccountDomain` 字段，并将 `Set me` 替换为帐户域。

现在，右键单击该项目并选择“生成”，生成“AoaSampleApp”项目 。

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="配置 Visual Studio 项目以进行部署":::

## <a name="deploy-the-app-to-hololens"></a>将应用部署到 HoloLens

成功编译示例项目后，可以将应用部署到 HoloLens。

打开 HoloLens 设备，登录并使用 USB 电缆将其连接到电脑。 请确保“设备”是选择的部署目标（参见上文）。

右键单击“AoaSampleApp”项目，然后从弹出菜单中单击“部署”以安装应用。 如果 Visual Studio 的“输出窗口”中没有显示任何错误，则会在 HoloLens 上安装应用。

:::image type="content" source="./media/vs-deploy-app.png" alt-text="将应用部署到 HoloLens":::

在启动应用之前，需要上传对象模型。 按照下面的“引入对象模型并检测其实例”一节中的说明进行操作。

若要启动和调试应用，请选择“调试” **>“开始调试”** 。 若要停止应用，请选择“停止调试”或按 Shift + F5。

## <a name="ingest-object-model-and-detect-its-instance"></a>引入对象模型并检测其实例

需要创建一个对象模型来运行示例应用。 假设你已在空间中获得对象的 CAD 或扫描的 3D 网格模型。 请参阅[快速入门：引入 3D 模型](./get-started-model-conversion.md)了解如何创建模型。

在本例中，将模型 chair.ou 下载到你的计算机。 然后，从 HoloLens 设备门户选择“系统”>“文件资源管理器”>“LocalAppData”>“AoaSampleApp”>“LocalState”，然后选择“浏览...” 。然后，选择模型文件，例如 chair.ou，再选择“上传” 。 随后你应该会在本地缓存中看到该模型文件。

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="门户上传模型":::

在 HoloLens 中启动 AoaSampleApp 应用（如果它已打开，请将其关闭并重新打开）。 走近（2 米距离内）目标对象（椅子），并从多个角度观察以进行扫描。 应该会看到对象周围出现一个粉红色的边框，一些黄色的点呈现在对象表面的附近，这表示检测到了该对象。

:::image type="content" source="./media/chair-detection.png" alt-text="椅子检测":::

图：一个检测到的椅子呈现出其边框（粉红色）、点云（黄色）和搜索区域（大黄框）。

你可以通过右手或左手的手指在空中点击，来定义应用中对象的搜索空间。 搜索空间将在半径为 2 米的球形、4 米 ^3 的边框和视锥之间切换。 对于汽车等大型对象，最佳选择通常是在面对对象的一角站立在大约 2 米的距离时，使用视锥选择。
每当搜索区域发生更改时，应用都将删除当前正在跟踪的实例，然后在新的搜索区域尝试再次查找它们。

此应用可一次跟踪多个对象。 为此，请将多个模型上传到“LocalState”文件夹，并设置一个涵盖所有目标对象的搜索区域。 检测和跟踪多个对象可能需要更长时间。

应用将 3D 模型紧密地对齐到其物理对应项。 用户可以在空中使用左手点击以启用高精度跟踪模式，这可以计算出更准确的姿势。 这仍是一项试验性功能，它会消耗更多的系统资源，并可能会导致估计姿势的抖动更剧烈。 在空中使用左手再次点击即可切换回正常跟踪模式。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：引入 3D 模型](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [概念：SDK 概述](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [常见问题](../faq.md)

> [!div class="nextstepaction"]
> [转换 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
