---
title: 什么是 Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 服务概述
author: kgremban
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: d2ec1be20c7453d64b3255f9756676d06b023d38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747315"
---
# <a name="what-is-azure-iot-edge"></a>什么是 Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge 将云分析和自定义业务逻辑移到设备，这样你的组织就可以专注于业务见解而非数据管理。 通过将业务逻辑打包到标准容器中，横向扩展 IoT 解决方案，然后可以将这些容器部署到任何设备，并从云中监视所有这些设备。

分析可以提升 IoT 解决方案中的业务价值，但并非所有分析都需要在云中进行。 如果希望尽快响应突发事件，可以在边缘运行异常情况检测工作负荷。 如果想要降低带宽成本并避免传输数 TB 的原始数据，可以在本地清理和聚合数据，然后只将见解发送到云进行分析。

Azure IoT Edge 包含三个组件：

* IoT Edge 模块是容器，可以运行 Azure 服务、第三方服务或者你自己的代码。 这些模块部署到 IoT Edge 设备，在设备上以本地方式执行。
* IoT Edge 运行时在每个 IoT Edge 设备上运行，并管理部署到每个设备的模块。
* 可以通过基于云的界面远程监视和管理 IoT Edge 设备。

>[!NOTE]
>Azure IoT Edge 在 IoT 中心的免费层和标准层中提供。 免费层仅用于测试和评估。 有关基本和标准层的详细信息，请参阅[如何选择合适的 IoT 中心层](../iot-hub/iot-hub-scaling.md)。

## <a name="iot-edge-modules"></a>IoT Edge 模块

IoT Edge 模块是执行单位，以 Docker 兼容容器的方式来实现，在边缘运行业务逻辑。 可以将多个模块配置为互相通信，创建一个数据处理管道。 可以开发自定义模块，或者将某些 Azure 服务打包到模块中，以脱机方式在边缘提供见解。

### <a name="artificial-intelligence-at-the-edge"></a>边缘的人工智能

可以使用 Azure IoT Edge 来部署复杂事件处理、机器学习、图像识别和其他高价值 AI，不需在内部编写代码。 Azure Functions、Azure 流分析、Azure 机器学习之类的 Azure 服务均可通过 Azure IoT Edge 在本地运行。 但是，你也可以运行 Azure 服务之外的内容。 任何人均可创建 AI 模块，通过 Azure 市场提供给社区使用。

### <a name="bring-your-own-code"></a>自带代码

如果希望将自己的代码部署到设备，则也可使用 Azure IoT Edge。 与其他 Azure IoT 服务一样，Azure IoT Edge 始终使用同一编程模型。 可以在设备或云中运行相同的代码。 Azure IoT Edge 既支持 Linux，也支持 Windows，允许你根据所选平台来编码。 它支持 Java、.NET Core 2.0、Node.js、C、Python，允许开发人员使用熟悉的语言和现有的业务逻辑进行编码。

## <a name="iot-edge-runtime"></a>IoT Edge 运行时

Azure IoT Edge 运行时允许在 IoT Edge 设备上使用自定义逻辑和云逻辑。 运行时位于 IoT Edge 设备上，并执行管理和通信操作。 该运行时执行多个功能：

* 在设备上安装和更新工作负荷。
* 维护设备上的 Azure IoT Edge 安全标准。
* 确保 IoT Edge 模块始终运行。
* 将模块运行状况报告给云以进行远程监控。
* 管理下游叶设备与 IoT Edge 设备之间、IoT Edge 设备上的模块之间以及 IoT Edge 设备与云之间的通信。

![IoT Edge 运行时将见解和报告发送到 IoT 中心](./media/about-iot-edge/runtime.png)

如何使用 Azure IoT Edge 设备取决于你自己。 通常使用运行时将 AI 部署到网关设备，由后者聚合和处理来自其他本地设备的数据，但此部署模型只是一个选项。

Azure IoT Edge 运行时在各种大型 IoT 设备上运行，因此可以通过各种方式来使用该运行时。 它支持 Linux 和 Windows 操作系统，并可提取硬件详细信息。 如果要处理的数据不多，请使用比 Raspberry Pi 3 小的设备；如果要运行资源密集型工作负荷，请使用工业服务器。

## <a name="iot-edge-cloud-interface"></a>IoT Edge 云接口

管理数百万台 IoT 设备的软件生命周期很困难，这些设备通常具有不同的品牌且型号各异，或者地理位置分散。 为特定类型的设备创建和配置工作负载，部署到所有设备，并监视以捕获任何行为异常的设备。 这些活动无法逐一为设备进行，必须大规模操作。

Azure IoT Edge 与 Azure IoT 解决方案加速器无缝集成，提供一个符合解决方案需要的控制平面。 云服务允许：

* 创建和配置在特定类型的设备上运行的工作负荷。
* 将工作负荷发送到一组设备。
* 监视在现场设备上运行的工作负荷。

![设备遥测和操作与云进行协调](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>后续步骤

通过将第一个 IoT Edge 模块部署到设备来体会这些概念：

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

* [将模块部署到 Linux IoT Edge 设备](quickstart-linux.md)
* [将模块部署到 Windows IoT Edge 设备](quickstart.md)

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

[将模块部署到 IoT Edge 设备](quickstart-linux.md)

:::moniker-end
