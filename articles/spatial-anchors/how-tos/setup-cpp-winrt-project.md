---
title: 为 C++/WinRT HoloLens 应用程序安装 Azure 空间定位点
description: 配置 C++/WinRT HoloLens 项目以使用 Azure 空间定位点
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 69134ef87d90fe69de2d9e4e9222e90f65edc785
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "95507000"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>在 C++/WinRT HoloLens 项目中配置 Azure 空间定位点

## <a name="requirements"></a>要求

* 安装包含“通用 Windows 平台开发”工作负荷和 Windows 10 SDK（10.0.18362.0 或更高版本）组件的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)

## <a name="configuring-a-project"></a>配置项目

使用 [Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 包分发用于 HoloLens 和 C++/WinRT 的 Azure 空间定位点。

按照[此处](/nuget/consume-packages/install-use-packages-visual-studio)说明使用 Visual Studio 的 NuGet 包管理器将 [Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 包安装到你的项目中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [操作说明：在 C++/WinRT 中创建并找到定位点](./create-locate-anchors-cpp-winrt.md)