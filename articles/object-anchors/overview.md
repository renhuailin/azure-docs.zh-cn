---
title: Azure Object Anchors 概述
description: 了解 Azure Object Anchors 如何帮助你检测物理世界中的对象。
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 84a69c46e9d7587ed87d8288fca5e7d8ef25f546
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983559"
---
# <a name="azure-object-anchors-overview"></a>Azure Object Anchors 概述

通过 Azure Object Anchors，应用程序可以使用 3D 模型在物理世界中检测对象，并估计其 6DoF 姿态。 6DoF（6 自由度）姿态定义为 3D 模型与其物理对应项（实际对象）之间的旋转和平移。

Azure Object Anchors 包含用于模型转换的服务和用于 HoloLens 的运行时客户端 SDK。 此服务接受 3D 对象模型，并输出 Azure Object Anchors 模型。 Azure Object Anchors 模型与运行时 SDK 一起使用，以使 HoloLens 应用程序能够在物理世界中加载对象模型、检测和跟踪该模型的实例。

:::image type="content" source="./media/object-anchors-overview.jpg" alt-text="Azure Object Anchors 实操演练":::

## <a name="examples"></a>示例

Azure Object Anchors 支持的一些示例用例包括：

- **训练**。 为你的工作人员创造“混合现实”训练体验，而无需放置标记或花时间手动调整全息图对齐方式。 如果希望使用自动检测和跟踪功能增强“混合现实”训练体验，请将模型引入到 Azure Object Anchors 服务，你将距离无标记体验更近一步。

- 任务指南。 使用混合现实时，可大大简化向员工演示完成一系列任务的过程。 将数字指令和最佳做法叠加在物理对象之上（无论是车间上的机器，还是员工餐厅厨房里的咖啡机），可以极大地降低完成一系列任务的难度。 触发这些体验通常需要某种形式的标记或手动对齐，但在使用 Azure Object Anchors 的情况下，你可以创造一种自动检测与手头任务相关的对象的体验。 然后，无需标记或手动对齐即可无缝完成混合现实指导。

- 资产查找。 如果物理空间中已经有了某个对象的 3D 模型，则可以使用 Azure Object Anchors 在物理环境中查找和跟踪该对象的实例。

## <a name="usage-flow"></a>使用情况流

首先，将 3d 资产上传到 Azure Object Anchors 转换服务。 可以按照以下其中一个快速入门中的步骤操作：

  - [Unity HoloLens](quickstarts/get-started-unity-hololens.md)
  - [Unity HoloLens 与 MRTK](quickstarts/get-started-unity-hololens-mrtk.md)
  - [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)

然后，该服务会将资产转换为 Azure Object Anchors 模型。 下载转换后的模型，然后你便可以[可视化其网格](visualize-converted-model.md)（如果需要）。 最后，将模型复制到具有 Runtime SDK for [Unity](/dotnet/api/Microsoft.Azure.ObjectAnchors) 或 [HoloLens C++/WinRT](/cpp/api/object-anchors/winrt) 的 HoloLens 设备：现在可以检测与原始模型匹配的物理对象。

:::image type="content" source="./media/object-anchors-flow.png" alt-text="使用情况流":::

## <a name="asset-requirements"></a>资产要求

资产的每个维度应介于 1 米到 10 米之间，文件大小应小于 150 MB。

目前支持的文件格式为：`fbx`、`ply`、`obj`、`glb` 和 `gltf`。

## <a name="next-steps"></a>后续步骤

以下部分提供有关通过 Azure Object Anchors 开始使用和构建应用的信息。

> [!div class="nextstepaction"]
> [模型引入](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens 与 MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
