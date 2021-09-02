---
title: Azure Percept 安全性
description: 了解有关 Azure Percept 安全的详细信息
author: mimcco
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: cfee4f14b5e7f135c7bbe609b5f3453b5fce8589
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221941"
---
# <a name="azure-percept-security"></a>Azure Percept 安全性

Azure Percept 设备采用硬件根信任设计。 这一内置安全机制可帮助保护推理数据以及在隐私方面具有敏感性的传感器（如相机和麦克风），并可以启用 Azure Percept Studio 服务的设备身份验证和授权。

> [!NOTE]
> Azure Percept DK 仅授权用于开发和测试环境。

## <a name="devices"></a>设备

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK 包含受信任的平台模块 (TPM) 版本 2.0，该模块可用于将设备连接到 Azure 设备预配服务 (DPS)，同时提供额外的安全性。 TPM 是一种行业通用的 ISO 标准，由 Trusted Computing Group 提出。 请查看[受信任的计算组网站](https://trustedcomputinggroup.org/resource/tpm-library-specification/)，详细了解完整的 TPM 2.0 规范或 ISO/IEC 11889 规范。要深入了解 DPS 如何以安全的方式预配设备，请参阅 [Azure IoT 中心设备预配服务 - TPM 证明](../iot-dps/concepts-tpm-attestation.md)。

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept 模块上系统 (SoM)

Azure Percept Vision 模块上系统 (SoM) 和 Azure Percept Audio SoM 都包含微控制器单元 (MCU)，它用于保护对嵌入式 AI 传感器的访问。 每次启动时，MCU 固件都会使用设备标识符组合引擎 (DICE) 体系结构，通过 Azure Percept Studio 服务对 AI 加速器进行身份验证和授权。 DICE 的工作原理是：将启动分解成多层，并为每一层和配置创建唯一设备机密 (UDS)。 如果在链中的任意一点启动了不同的代码或配置，机密都将不同。 你可在 [DICE 工作组规范](https://trustedcomputinggroup.org/work-groups/dice-architectures/)中了解 DICE 的详细信息。要了解如何配置 Azure Percept Studio 和所需服务的访问权限，请参阅[为 Azure Percept DK 配置防火墙](concept-security-configuration.md)一文。

Azure Percept 设备使用硬件信任根来保护固件。 启动 ROM 可确保 ROM 和操作系统 (OS) 加载器之间的固件完整性，而这又可确保其他软件组件的完整性，从而创建信任链。

## <a name="services"></a>服务

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK 使用传输层安全性 (TLS) 协议，通过额外的安全性和其他 Azure 服务连接到 Azure Percept Studio。 Azure Percept DK 是启用了 Azure IoT Edge 的设备。 IoT Edge 运行时是将设备转变为 IoT Edge 设备的程序集合。 在 IoT Edge 运行时组件的共同作用下，IoT Edge 设备可以接收要在边缘上运行的代码并传递结果。 Azure Percept DK 使用 Docker 容器将 IoT Edge 工作负载与主机操作系统和支持 Edge 的应用程序隔离。 有关 Azure IoT Edge 安全框架的详细信息，请阅读有关 [IoT Edge 安全管理器](../iot-edge/iot-edge-security-manager.md)的内容。

### <a name="device-update-for-iot-hub"></a>IoT 中心的设备更新文档

IoT 中心的设备更新支持进行更安全、可缩放且可靠的无线更新，可为 Azure Percept 设备带来可续订安全性。 它通过见解提供丰富的管理控件和更新符合性。 Azure Percept DK 包括预先集成的设备更新解决方案，该解决方案提供从固件到 OS 层的弹性更新 (A/B)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解防火墙配置和安全建议](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [从 Microsoft 在线商店购买 Azure Percept DK](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
