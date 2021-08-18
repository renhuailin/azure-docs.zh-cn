---
title: Edge Managed 认证要求
description: Edge Managed 认证要求
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: b7ca60a674a044e5fa37604eadad7ca5cbbfddf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741132"
---
# <a name="edge-managed-certification-requirements"></a>Edge Managed 认证要求

本文档概述了将在 Azure 认证设备目录中呈现的特定于设备的功能。 功能是一个设备属性，可用于描述设备。 

## <a name="program-purpose"></a>计划的目的

Edge Managed 认证是基线 Azure 认证设备认证之外的一项增量认证。 Edge Managed 侧重于 Azure 连接设备的设备管理标准，并验证 IoT Edge 运行时对模块部署和管理的兼容性。 （以前，此计划被标识为 IoT Edge 认证计划。） 

Edge Managed 认证可验证 IoT Edge 运行时对模块部署和管理的兼容性。 此计划为管理 Azure 连接 IoT 设备提供了信心。

## <a name="requirements"></a>要求

Edge Managed 认证遵循 [Azure 认证设备基线计划](.\program-requirements-azure-certified-device.md)中的所有要求。

**DPS：测试的目的是通过三种证明方法中的一种来检查设备是否实现并支持 IoT 中心设备预配服务**

| **名称**                | AzureReady.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **目标可用性** | 目前可用                                                |
| **适用于**          | 任何设备                                                   |
| **OS**                  | 不可知                                                     |
| **验证类型**     | 自动                                                    |
| **验证**          | AICS 验证设备代码支持 DPS。 **1.** 用户必须选择一种证明方法（X.509、TPM 和 SAS 密钥）。 **2.** 根据证明方法，用户需要执行相应的操作（例如 a) 将 X.509 证书上传到 AICS 托管的 DPS 范围；b) 在设备中实现 SAS 密钥或认可密钥） 。 **3.** 然后，用户将点击“连接”按钮，通过 DPS 连接到 AICS 托管 IoT 中心                                                    |
| **资源**           |                                                      |
| **Azure 建议：**     | 不适用                                                    |

## <a name="iot-edge"></a>IoT Edge

**Edge 运行时已存在：测试的目的是确保包含 IoT Edge 运行时（$edgehub 和 $edgeagent）的设备正常工作。**

| **名称**                | EdgeManaged.EdgeRT                                           |
| ----------------------- | ------------------------------------------------------------ |
| **目标可用性** | 目前可用                                                |
| **适用于**          | IoT Edge 设备                                              |
| **OS**                  | [Tier1 和 Tier2 OS](../iot-edge/support.md)                 |
| **验证类型**     | 自动                                                    |
| **验证**          | AICS 验证已安装的 IoT Edge RT 的部署能力。 **1.** 用户需要指定特定的 OS（只接受 Tier1/2 列表中的 OS）**2.** AICS 生成其 config.yaml 并部署规范化 [模拟温度传感器边缘模块](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) **3.** AICS 验证设备上是否已安装与 Docker 兼容的容器子系统 (Moby) **4.** 测试结果基于模拟温度传感器边缘模块的成功部署和与 Docker 兼容的容器子系统功能                                                    |
| **资源**           | **a)** [AICS 博客](https://azure.microsoft.com/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/)，**b)** [认证步骤](./overview.md)（具有所有附加资源），**c)** [要求](./program-requirements-azure-certified-device.md) |
| **Azure 建议：**     | 不适用                                                    |

### <a name="capability-template"></a>功能模板：

**IoT Edge 易于安装：测试的目的是确保 IoT Edge 设备易于安装，并验证是否已在物理设备验证期间预安装了 IoT Edge 运行时**

| **名称**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **目标可用性** | 现在可用（由于新冠肺炎疫情，当前处于搁置状态）                                            |
| **适用于**          | IoT Edge 设备                                                   |
| **OS**                  | [Tier1 和 Tier2 OS](../iot-edge/support.md)                                                     |
| **验证类型**     | 手动/实验室验证                                                    |
| **验证**          | OEM 必须将物理设备寄送给 IoT 管理 (HCL)。 HCL 对物理设备执行手动验证：**1.** 检查 EdgeRT 是否正在使用 Moby 子系统（允许的再分配版本）， 而不是 Docker **2.** 选择最新的边缘模块，验证是否能够部署边缘。                                                     |
| **资源**           | **a)** [AICS 博客](https://azure.microsoft.com/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/)，**b)** [认证步骤](./overview.md)，**c)** [要求](./program-requirements-azure-certified-device.md) |
| **Azure 建议：**     | 空值                                                    |