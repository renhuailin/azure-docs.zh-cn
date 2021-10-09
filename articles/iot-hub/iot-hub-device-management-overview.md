---
title: 使用 Microsoft Azure IoT 中心进行设备管理概述
description: Azure IoT 中心的设备管理概述 -- 企业设备生命周期和设备管理模式，如重启、恢复出厂设置、固件更新、配置、设备孪生、查询、作业、威胁检测。
author: anastasia-ms
ms.service: iot-hub
services: iot-hub
ms.author: v-stharr
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: f76996f721287679982f92345ecc075c3db55aba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612921"
---
# <a name="overview-of-device-management-with-iot-hub"></a>使用 IoT 中心进行设备管理的概述

Azure IoT 中心提供功能和可扩展性模型，使设备和后端开发人员可以构建功能强大的设备管理解决方案。 设备的范围从受约束的传感器和单一用途微控制器到功能强大的路由设备组通信的网关。  此外，在不同行业中，IoT 操作员的用例和要求也显著不同。  尽管有此不同，但使用 IoT 中心进行设备管理提供了功能、模式和代码库，以满足不同设备和最终用户的需要。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

创建成功的企业 IoT 解决方案的一个重要部分，是提供操作员如何处理其设备集合的日常管理的策略。 IoT 操作员需要简单且可靠的工具和应用程序，使他们能够重点处理其工作的更具战略意义方面。 本文提供以下内容：

* Azure IoT 中心设备管理方法的简要概述。
* 常见设备管理原则的说明。
* 设备生命周期的说明。
* 常见设备管理模式的概述。

## <a name="device-lifecycle"></a>设备生命周期

常规设备管理阶段在大多数企业 IoT 项目中很常见。 在 Azure IoT 中，设备生命周期有五个阶段：

![Azure IoT 设备生命周期的五个阶段：计划、预配、配置、监视、停用](./media/iot-hub-device-management-overview/image5.png)

在上述五个阶段的每个阶段中，都有几项应满足以提供完整解决方案的设备操作员要求：

* **计划**：使操作员能够创建可让他们轻松且精确地查询的设备元数据方案，并针对一组设备进行批量管理操作。 可以使用设备孪生以标记和属性的形式存储此设备元数据。
  
    *延伸阅读*： 
  * [设备孪生入门](iot-hub-node-node-twin-getstarted.md)
  * [了解设备孪生](iot-hub-devguide-device-twins.md)
  * [如何使用设备孪生属性](tutorial-device-twins.md)
  * [IoT 解决方案中设备配置的最佳做法](iot-hub-configuration-best-practices.md)

* **预配**：安全地为 IoT 中心预配新设备，使操作员立即发现设备功能。  使用 IoT 中心标识注册表创建灵活的设备标识和凭据，并使用作业成批执行此操作。 通过设备孪生中的设备属性构建设备来报告其功能和情况。
  
    *延伸阅读*： 
    * [管理设备标识](iot-hub-devguide-identity-registry.md)
    * [批量管理设备标识](iot-hub-bulk-identity-mgmt.md)
    * [如何使用设备孪生属性](tutorial-device-twins.md)
    * [IoT 解决方案中设备配置的最佳做法](iot-hub-configuration-best-practices.md)
    * [Azure IoT 中心设备预配服务](../iot-dps/index.yml)

* **配置**：在保持正常运行和安全性的同时便于对设备进行批量配置更改和固件更新。 使用所需属性或通过直接方法和广播作业成批执行这些设备管理操作。
  
    *延伸阅读*：
    * [如何使用设备孪生属性](tutorial-device-twins.md)
    * [大规模配置和监视 IoT 设备](./iot-hub-automatic-device-management.md)
    * [IoT 解决方案中设备配置的最佳做法](iot-hub-configuration-best-practices.md)

* **监视**：监视总体设备集合运行状况、正在进行的操作的状态并针对可能需要操作员注意的问题向操作员发出警报。  应用设备孪生以允许设备报告实时操作情况和更新操作的状态。 使用设备孪生查询生成显示最直接问题的功能强大的仪表板报告。 利用多个部署选项（包括完全本地部署、连接云的部署或混合部署）保护 IoT 环境免受威胁。
  
    *延伸阅读*： 
    * [如何使用设备孪生属性](tutorial-device-twins.md)
    * [用于设备孪生、作业和消息路由的 IoT 中心查询语言](iot-hub-devguide-query-language.md)
    * [大规模配置和监视 IoT 设备](./iot-hub-automatic-device-management.md)
    * [适用于组织的 Azure Defender for IoT，可提供全面的威胁检测](../defender-for-iot/organizations/overview.md)
    * [IoT 解决方案中设备配置的最佳做法](iot-hub-configuration-best-practices.md)

* **停用**：在发生故障、升级周期后，或在服务生存期结束时更换或停用设备。  使用设备孪生来维护设备信息（如果正在更换物理设备），或者将设备信息存档（如果正在停用设备）。 使用 IoT 中心标识注册表来安全地撤销设备标识和凭据。
  
    *延伸阅读*： 
    * [如何使用设备孪生属性](tutorial-device-twins.md)
    * [管理设备标识](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>设备管理模式

IoT 中心启用以下设备管理模式集。 [设备管理教程](iot-hub-node-node-device-management-get-started.md)更详细地介绍如何扩展这些模式以适合具体方案，以及如何基于这些核心模板设计新模式。

* 重启：后端应用通过直接方法通知设备它已开始重启。  设备使用报告属性来更新设备的重新启动状态。
  
    ![设备管理重新启动模式图形](./media/iot-hub-device-management-overview/reboot-pattern.png)

* 恢复出厂设置：后端应用通过直接方法通知设备它已开始恢复出厂设置。 设备使用报告属性来更新设备的恢复出厂设置状态。
  
    ![设备管理恢复出厂设置模式图形](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **配置**：后端应用使用所需属性来配置设备上运行的软件。 设备使用报告属性来更新设备的配置状态。
  
    ![设备管理配置模式图形](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **报告进度和状态**：解决方案后端在一组设备上运行设备孪生查询，以报告设备上运行的操作的状态和进度。
  
    ![设备管理报告进度和状态模式图形](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="device-updates"></a>设备更新

[Device Update for IoT Hub](../iot-hub-device-update/understand-device-update.md) 是一个综合性平台，客户可以使用它来发布、分发和管理从微型传感器到网关级设备的所有内容的无线更新。 使用 Device Update for IoT Hub，客户可以快速响应安全威胁并部署功能来满足业务目标，在构建自定义更新平台时不会产生更多的开发和维护成本。

Device Update for IoT Hub 通过与 Azure IoT 中心集成，提供优化的更新部署和简化的操作。 通过使用 Azure IoT Edge 进行扩展访问，它提供了一个几乎可连接任何设备的云托管解决方案。 它支持范围广泛的 IoT 操作系统，包括 Linux 和 Azure RTOS（实时操作系统），并且可通过开放源代码进行扩展。 包括的一些功能为：

* 支持更新边缘设备，包括 Azure IoT Edge 的主机级组件
* 与 Azure IoT 中心集成的更新管理 UX
* 通过设备分组和更新计划控件逐步推出更新
* 用于实现自动化和自定义门户体验的编程 API
* 异质设备群中一目了然的更新合规性和状态视图
* 支持弹性设备更新 (A/B) 以提供无缝回滚
* 通过内置的 Microsoft 联网缓存以及与 Azure IoT Edge 的集成，提供内容缓存和离线设备（包括那些使用嵌套配置的设备）支持
* 通过 Azure.com 门户提供订阅和基于角色的访问控制
* 全面的云到边缘安全功能和隐私控制

有关详细信息，请参阅 [Device Update for IoT Hub](../iot-hub-device-update/index.yml)。

## <a name="next-steps"></a>后续步骤

可以使用 IoT 中心设备管理提供的功能、模式和代码库，在每个设备生命周期阶段创建满足企业 IoT 操作员需求的 IoT 应用程序。

若要继续了解 IoT 中心设备管理功能，请参阅[设备管理入门](iot-hub-node-node-device-management-get-started.md)教程。