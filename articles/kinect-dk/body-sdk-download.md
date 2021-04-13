---
title: Azure Kinect 人体跟踪 SDK 下载
description: 了解如何在 Windows 或 Linux 中下载每个版本的 Azure Kinect 传感器 SDK。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: azure, kinect, sdk, 下载更新, 最新, 可用, 安装, 人体, 跟踪
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654486"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>下载 Azure Kinect 人体跟踪 SDK

本文档提供各版本 Azure Kinect 正文跟踪 SDK 的安装链接。

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Azure Kinect 人体跟踪 SDK 的内容

- 标头和库，用于通过 Azure Kinect DK 生成人体跟踪应用程序。
- 使用 Azure Kinect DK 的人体跟踪应用程序所需的可分发 DLL。
- 示例人体跟踪应用程序。

## <a name="windows-download-links"></a>Windows 下载链接

版本       | 下载
--------------|----------
1.1.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Linux 安装说明

目前，只支持发行版 Ubuntu 18.04 和 Ubuntu 20.04。 若要请求对其他分发版的支持，请参阅[此页](https://aka.ms/azurekinectfeedback)。

首先，需要遵照[此处](/windows-server/administration/linux-package-repository-for-microsoft-software)的说明，配置 [Microsoft 的包存储库](https://packages.microsoft.com/)。

`libk4abt<major>.<minor>-dev` 包中包含针对 `libk4abt` 生成的头文件以及 CMake 文件。
`libk4abt<major>.<minor>` 包中包含运行依赖于 `libk4abt` 的可执行文件以及示例查看器所需的共享对象。

基本教程需要 `libk4abt<major>.<minor>-dev` 包。 若要安装该包，请运行

`sudo apt install libk4abt<major>.<minor>-dev`

如果该命令成功，则表示 SDK 可供使用。

> [!NOTE]
> 安装 SDK 时，请记住要安装到的路径。 例如，“C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0”。 你将要在此路径中查找文章中参考的示例。
> 正文跟踪示例位于 Azure-Kinect-Samples 存储库的 [body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) 文件夹中。 你将在此处的文章中找到参考的示例。

## <a name="change-log"></a>更改日志

### <a name="v110"></a>v1.1.0
* [功能] 添加了对通过 DirectML（仅 Windows）和 TensorRT 执行姿态估计模型的支持。 请参阅“有关新执行环境的常见问题解答”。
* [功能] 将 `model_path` 添加到 `k4abt_tracker_configuration_t` 结构。 允许用户指定姿态估计模型的路径名。 默认为当前目录中的 `dnn_model_2_0_op11.onnx` 标准姿态估计模型。
* [功能] 包括 `dnn_model_2_0_lite_op11.onnx` 精简姿态估计模型。 此模型的性能提高到原来的约 2 倍，而准确度下降了约 5%。
* [功能] 已验证的示例使用 Visual Studio 2019 进行编译，在更新示例后可以使用此版本。
* [中断性变更] 更新到 ONNX 运行时 1.6，并支持 CPU、CUDA 11.1、DirectML（仅 Windows）和 TensorRT 7.2.1 执行环境。 需要将 NVIDIA 驱动程序更新到 R455 或更高版本。
* [中断性变更] 不安装 NuGet。
* [Bug 修复] 添加了对 NVIDIA RTX 30xx 系列 GPU 的支持 [链接](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Bug 修复] 添加了对 AMD 和 Intel 集成 GPU 的支持（仅限 Windows）[链接](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Bug 修复] 更新到 CUDA 11.1 [链接](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [Bug 修复] 更新到传感器 SDK 1.4.1 [链接](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v1.0.1
* [Bug 修复] 修复了从 Windows 内部版本 19025 或更高版本的路径加载 onnxruntime.dll 时 SDK 崩溃的问题：[链接](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* [功能] 为 msi 安装程序添加了 C# 包装器。
* [Bug 修复] 修复了无法正确检测标头旋转的问题：[链接](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Bug 修复] 修复了在 Linux 计算机上 CPU 使用率高达 100% 的问题：[链接](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [示例] 将两个示例添加到示例存储库中。 示例 1 演示了如何将深度空间中的正文跟踪结果转换为颜色空间[链接](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)；示例 2 演示了如何检测地面[链接](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

## <a name="next-steps"></a>后续步骤

- [Azure Kinect DK 概述](about-azure-kinect-dk.md)

- [设置 Azure Kinect DK](set-up-azure-kinect-dk.md)

- [设置 Azure Kinect 人体跟踪](body-sdk-setup.md)