---
title: 快速入门：将快速入门应用升级到 Unity 2020
description: 本快速入门介绍如何将快速入门应用（即使用 Object Anchors 的 HoloLens Unity 应用）升级到 Unity 2020 版本。
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b628aab20e08460ea6d023651098227aa3390d5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791985"
---
# <a name="quickstart-upgrade-quickstart-app-to-unity-2020"></a>快速入门：将快速入门应用升级到 Unity 2020

在本快速入门中，将使用 [Azure Object Anchors](../overview.md) 的 Unity HoloLens 应用从 Unity 2019 升级到 Unity 2020。 Azure Object Anchors 是一项托管的云服务，它将 3D 资产转换为 AI 模型，以便为 HoloLens 启用对象感知混合现实体验。 完成本教程后，你将拥有一个使用 Unity 生成的 HoloLens 应用，该应用可检测到物理世界中的物体。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 将应用从 Unity 2019 升级到 Unity 2020。
> * 升级包依赖项。
> * 升级 Unity 生成设置。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

* [Unity HoloLens](get-started-unity-hololens.md) 或[通过 MRTK 创建 Unity HoloLens](get-started-unity-hololens-mrtk.md) 快速入门的所有先决条件。
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity Hub 与 Unity 2020.3.8f1 或更高版本</a>

## <a name="open-and-upgrade-the-sample-project"></a>打开并升级示例项目

按照 [Unity HoloLens](get-started-unity-hololens.md) 或[通过 MRTK 创建 Unity HoloLens](get-started-unity-hololens-mrtk.md) 快速入门中的步骤克隆[示例存储库](https://github.com/Azure/azure-object-anchors)，并下载适用于 Unity 的 Azure Object Anchors 包。

打开 Unity 中心。 选择“添加”按钮，然后选择 `quickstarts/apps/unity/basic` 或 `quickstarts/apps/unity/mrtk` 项目。 然后，在“Unity 版本”列下，从下拉列表中选择你已在计算机上安装的 Unity 2020 版本。 在“目标平台”列下，选择“通用 Windows 平台”。 最后，选择“项目名称”列，然后在 Unity 中打开示例。

:::image type="content" source="./media/upgrade-unity-2020.png" alt-text="升级 unity 2020":::

你将看到一个对话框，要求确认是否升级项目。 选择“确认”按钮。

:::image type="content" source="./media/confirm-unity-upgrade.png" alt-text="确认 unity 升级":::

## <a name="upgrade-package-dependencies"></a>升级包依赖项

升级过程完成后，Unity 编辑器将打开。

按照<a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">混合现实功能工具</a>文档来设置工具，并了解如何使用该工具。

在“平台支持”部分，将“混合现实 OpenXR 插件”功能包（版本 1.0.0 或更高版本）安装到 Unity 项目文件夹中。 如果你处理的是 `quickstarts/apps/unity/mrtk` 项目，请同时打开“混合现实工具包”部分，找到“混合现实工具包基础”和“混合现实工具包工具”功能包，并将其升级到版本 2.7.0 或更高版本。

返回到“Unity 编辑器”。 安装“混合现实功能工具”功能包可能需要几分钟时间。

你将看到一个对话框，要求你确认启用新的输入系统。 选择“是”按钮。

:::image type="content" source="./media/new-input-system.png" alt-text="新输入系统":::

 如果你看到一个对话框，要求你覆盖 MRTK 着色器，请选择“是”。

:::image type="content" source="./media/mrtk-shaders.png" alt-text="MRTK 着色器":::

安装过程完成后，Unity 将自动重启。

## <a name="update-configuration-settings"></a>更新配置设置

返回到“Unity 编辑器”，按照<a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">为 OpenXR 配置 XR 插件管理</a>文档中的说明，在“项目设置”中设置“XR 插件管理”。 然后按照<a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">优化</a>文档中的说明操作，为 HoloLens 2 应用建议的项目设置。

## <a name="update-mrtk-settings"></a>更新 MRTK 设置

如果你处理的是 `quickstarts/apps/unity/mrtk` 项目，则 MRTK 还需要进行一些调整。 在这种情况下，请执行以下步骤。 否则，请跳到“生成、部署和运行应用”部分。

在“Unity 编辑器”中，导航到 `Assets/MixedReality.AzureObjectAnchors/Scenes` 并打开“AOASampleScene”。 在“层次结构”窗格下，选择“MixedRealityToolkit”对象。

:::image type="content" source="./media/open-sample-scene.png" alt-text="打开示例场景":::

在“检查器”窗格下，选择“相机”按钮，并将配置文件从“ObsoleteXRSDKCameraProfile”更改为“DefaultMixedRealityCameraProfile”。

:::image type="content" source="./media/update-camera-profile.png" alt-text="更新相机配置文件":::

还是在“检查器”窗格下，选择“输入”按钮，然后展开“输入数据提供程序”下拉列表。 然后，按照<a href="/windows/mixed-reality/mrtk-unity/configuration/getting-started-with-mrtk-and-xrsdk#configuring-mrtk-for-the-xr-sdk-pipeline" target="_blank">为 XR SDK 管道配置 MRTK</a> 文档来设置正确的输入数据提供程序（OpenXRDeviceManager 和 WindowsMixedRealityDeviceManager）。

:::image type="content" source="./media/update-input-profile.png" alt-text="更新输入配置文件":::

## <a name="build-deploy-and-run-the-app"></a>生成、部署和运行应用

你的项目现已完全升级到 Unity 2020。 请按照 [Unity HoloLens](get-started-unity-hololens.md) 或[通过 MRTK 创建 Unity HoloLens](get-started-unity-hololens-mrtk.md) 快速入门中的说明进行操作，以生成、部署和运行应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [概念：SDK 概述](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [常见问题](../faq.md)

> [!div class="nextstepaction"]
> [转换 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
