---
title: IoT 解决方案加速器简介 - Azure | Microsoft Docs
description: 了解 Azure IoT 解决方案加速器。 IoT 解决方案加速器是完整的、随时可部署的端到端 IoT 解决方案。
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: c966051ed5699d408fe83f1e9c862ca78b3282c4
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714535"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Azure IoT 解决方案加速器是什么？

基于云的 IoT 解决方案通常使用自定义代码和云服务来管理设备连接、数据处理、分析和呈现。

IoT 解决方案加速器是完整且易于部署的 IoT 解决方案，可以实现常见的 IoT 方案。 方案包括联网工厂和设备模拟。 部署解决方案加速器时，部署将包括全部所需的基于云的服务，以及全部所需的应用程序代码。

解决方案加速器是你自己的 IoT 解决方案的起点。 所有解决方案加速器的源代码都是开源的，并已在 GitHub 中提供。 建议按要求下载并自定义解决方案加速器。

此外，在从头开始生成自定义的 IoT 解决方案之前，可以使用解决方案加速器作为学习工具。 解决方案加速器针对基于云的 IoT 解决方案实施成熟的做法，你也可以遵循这些做法。

每个解决方案加速器中的应用程序代码包括一个 Web 应用，用于管理解决方案加速器。

> [!NOTE]
> 远程监视和预测性维护解决方案已从 [Azure IoT 解决方案加速器](https://www.azureiotsolutions.com/Accelerators)网站中删除。 若要了解详细信息，请参阅[什么是 Azure IoT 解决方案加速器？（早期版本）](/previous-versions/azure/iot-accelerators/about-iot-accelerators)。

## <a name="supported-iot-scenarios"></a>支持的 IoT 方案

目前，有两个解决方案加速器可供部署：

### <a name="connected-factory"></a>互连工厂

使用[连接工厂解决方案加速器](iot-accelerators-connected-factory-features.md)可以从配备了 [OPC 统一体系结构](https://opcfoundation.org/about/opc-technologies/opc-ua/)接口的工业资产收集遥测数据，以及控制这些资产。 工业资产可能包括工厂生产线上的组装和测试工位。

可以使用互联工厂仪表板来监视和管理工业设备：

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="显示“连接工厂解决方案”仪表板的屏幕截图。" lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>设备模拟

使用[设备模拟解决方案加速器](iot-accelerators-device-simulation-overview.md)可以运行能够生成真实遥测数据的模拟设备。 可以使用此解决方案加速器测试其他解决方案加速器的行为，或测试自己的自定义 IoT 解决方案。

可以使用设备模拟 Web 应用来配置并运行模拟：

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="显示设备模拟解决方案仪表板的屏幕截图。" lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>设计原理

所有解决方案加速器遵循相同的设计原理和目标。 它们在设计上具有以下特点：

* **可缩放**：允许连接和管理数百万个联网设备。
* **可扩展**：允许根据要求进行自定义。
* **易于理解**：可让你了解其工作原理及其实施方式。
* **模块化**：允许将服务更换为其他替代项。
* **安全**：将 Azure 安全性与内置的连接和设备安全功能相结合。

## <a name="architectures-and-languages"></a>体系结构和语言

原始的解决方案加速器是使用模型-视图-控制器 (MVC) 体系结构以 .NET 编写的。 Microsoft 正在将解决方案加速器更新为新的微服务体系结构。 下表显示了解决方案加速器的当前状态，并提供了 GitHub 存储库的链接：

| 解决方案加速器   | 体系结构  | Languages     |
| ---------------------- | ------------- | ------------- |
| 互连工厂      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| 设备模拟      | 微服务 | [.NET](https://github.com/Azure/azure-iot-pcs-device-simulation)          |

若要了解有关微服务体系结构的详细信息，请参阅 [Azure IoT 参考体系结构简介](/azure/architecture/reference-architectures/iot/)。

## <a name="deployment-options"></a>部署选项

可以通过 [Microsoft Azure IoT 解决方案加速器](https://www.azureiotsolutions.com/Accelerators#)站点或命令行部署解决方案加速器。

运行解决方案加速器的成本是[运行底层 Azure 服务的成本](https://azure.microsoft.com/pricing)的总和。 选择部署选项时，会看到所用的 Azure 服务的详细信息。

## <a name="next-steps"></a>后续步骤

若要试用某个 IoT 解决方案加速器，请参阅快速入门[尝试使用联网工厂解决方案](quickstart-connected-factory-deploy.md)。
