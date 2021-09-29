---
title: 快速入门：创建新的 HoloLens Unity 应用
description: 本快速入门介绍如何使用 Object Anchors 创建新的 HoloLens Unity 应用。
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: a83606d76beb4fd9cead19293636b9e5348d4990
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777557"
---
# <a name="quickstart-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-object-anchors"></a>快速入门：有关使用 Azure Object Anchors 创建新 HoloLens Unity 应用的分步说明

本快速入门将演示如何使用 [Azure Object Anchors](../overview.md) 创建新的 HoloLens Unity 应用。 Azure Object Anchors 是一项托管的云服务，它将 3D 资产转换为 AI 模型，以便为 HoloLens 启用对象感知混合现实体验。 完成本教程后，你将拥有一个使用 Unity 生成的 HoloLens 应用，该应用可检测到物理世界中的物体。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

* [Unity HoloLens](get-started-unity-hololens.md) 或[通过 MRTK 创建 Unity HoloLens](get-started-unity-hololens-mrtk.md) 快速入门的所有先决条件。
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity Hub 与 Unity 2020.3.8f1 或更高版本</a>

## <a name="getting-started"></a>入门

首先设置项目和 Unity 场景：

1. 启动 Unity Hub。
1. 选择“新建”，选取 Unity 2020.3.8f1 或更高版本。
1. 确保已选中“3D”。
1. 为项目命名并输入保存位置。
1. 选择“创建”。
1. “Unity 编辑器”打开后，使用“文件” > “另存为”将空的默认场景保存为新文件。
1. 选择“场景”文件夹，将新场景命名为“Main”，然后按“保存”  按钮。

## <a name="configure-as-uwp"></a>配置为 UWP

1. 选择“文件”->“生成设置”。
1. 依次选择“通用 Windows 平台”、“切换平台”。
1. 如果 Unity 编辑器指出需要先下载某些组件，请下载并安装这些组件。

## <a name="install-mixed-reality-feature-tool-feature-packages"></a>安装混合现实功能工具功能包

1. 按照<a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">混合现实功能工具</a>文档来设置工具，并了解如何使用该工具。
1. 在“平台支持”部分，将混合现实 OpenXR 插件功能包（版本 1.0.0 或更高版本）安装到 Unity 项目文件夹中。 
1. 在“Azure 混合现实服务”部分，将 Microsoft Azure Object Anchors 功能包安装到 Unity 项目文件夹中。
1. 返回到“Unity 编辑器”。 安装“混合现实功能工具”功能包可能需要几分钟时间。
1. 你将看到一个对话框，要求你确认启用新的输入系统。
1. 选择“是”按钮。
1. 安装过程完成后，Unity 将自动重启。

## <a name="configure-openxr"></a>配置 OpenXR

1. 你仍应位于“生成设置”窗口。
1. 选择“播放器设置...”按钮。
1. 此时将打开“项目设置”窗口。
1. 选择“XR 插件管理”条目。
1. 按照<a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">为 OpenXR 配置 XR 插件管理</a>文档中的说明进行操作，使用“插件提供程序”列表中的“Microsoft HoloLens 功能集”对“OpenXR”进行设置  。

## <a name="set-capabilities"></a>设置功能

1. 你仍应位于“项目设置”窗口。
1. 选择“播放器”条目。
1. 在“播放器设置”的“检查器面板” 中，确保选中“通用 Windows 平台设置”图标。
1. 在“发布设置”功能部分，确保选中“InternetClientServer”、“网络摄像头”和“SpatialPerception”  。

## <a name="set-up-the-project-settings"></a>设置项目设置

1. 你仍应位于“项目设置”窗口。
1. 选择“质量”条目。
1. 在“通用 Windows 平台”徽标下的列中，选择“默认”行中的箭头，然后选择“极低”。 当“通用 Windows 平台”列和“极低”行中的框为绿色时，表明已正确应用该设置 。
1. 关闭“项目设置”和“生成设置”窗口。
1. 按照<a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">优化</a>文档中的说明操作，为 HoloLens 2 应用建议的项目设置。

## <a name="set-up-the-main-virtual-camera"></a>设置主虚拟照相机

1. 在“层次结构”窗格，选择“主相机” 。
1. 在“检查器”中，将其转换位置设置为“0,0,0” 。
1. 找到“清除标志”属性，将下拉列表从“Skybox”更改为“纯色”  。
1. 选择“背景”字段以打开颜色选取器。
1. 将“R、G、B 和 A”设置为“0” 。
1. 将“剪裁平面近距”属性设置为 0.1。
1. 选择“添加组件”，然后搜索并添加“跟踪姿势驱动程序”。
1. 选择“文件” > “保存”。

## <a name="trying-it-out"></a>体验一下

若要测试一切设置是否有效，请在“Unity”中生成应用，并从“Visual Studio”进行部署 。 按照 <a href="/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio" target="_blank">**MR 基础知识 100：Unity 入门** 课程</a>中第 6 章进行操作。 应会显示 Unity 启动屏幕，然后是清晰的显示屏。

## <a name="create-your-script"></a>创建脚本

1. 在“项目”窗格中，在“资产”文件夹下创建新文件夹“脚本”  。
1. 右键单击该文件夹，依次选择“创建>”、“C# 脚本” 。 将其命名为“ObjectSearch”。
1. 转到“GameObject” -> “创建空白项” 。
1. 选择它，然后在“检查器”中将其从“GameObject”重命名为“Object Observer”  。
1. 选择“添加组件”，然后搜索并添加“ObjectSearch”脚本。
1. 选择“文件” > “保存”。

## <a name="start-implementing-your-app"></a>开始实现应用

可以使用 Object Anchors 运行时 SDK 开始将自己的代码添加到 ObjectSearch 脚本。 可以参考 [SDK 概述](../concepts/sdk-overview.md)作为学习基础知识的起点，并使用一些示例代码进行尝试。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [概念：SDK 概述](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [常见问题](../faq.md)

> [!div class="nextstepaction"]
> [转换 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
