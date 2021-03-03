---
title: Azure Percept 安全概述
description: 了解有关 Azure Percept 安全性的详细信息
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: b5a345139114842c83cb1f11792076efb1461870
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662197"
---
# <a name="azure-percept-security-overview"></a>Azure Percept 安全概述

Azure Percept 深色设备使用信任的硬件根目录-每个设备上内置的安全性。 它有助于保护与隐私相关的传感器（如相机和麦克风、推理数据），并对 Azure Percept Studio 服务启用设备身份验证和授权。

> [!NOTE]
> Azure Percept 深色适用于开发和测试环境以及概念证明方案。

## <a name="devices"></a>设备

### <a name="azure-percept-dk"></a>Azure Percept 深色

Azure Percept 深色包含受信任的平台模块 (TPM) 版本2.0，该模块可用于将设备安全地连接到 Azure 设备预配服务。 TPM 是受信任的计算组中的行业范围 ISO 标准，你可以在 [完整的 tpm 2.0 规范](https://trustedcomputinggroup.org/resource/tpm-library-specification/) 或 ISO/IEC 11889 规范中阅读有关 tpm 的详细信息。有关 DPS 如何以安全方式预配设备的详细信息，请参阅 [Azure IoT 中心设备预配服务-TPM 证明](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)。

### <a name="azure-percept-system-on-module-som"></a>模块上的 Azure Percept 系统 (SOM) 

Azure Percept 深色视觉上启用的系统模块 (SOM) 和 Azure Percept 音频附件 SOM 两者都包括一个微控制器单元 (MCU) 用于保护对嵌入 AI 传感器的访问。 每次启动时，MCU 固件都会使用设备标识符组合引擎 (骰子) 体系结构，使用 Azure Percept Studio 服务对 AI 加速器进行身份验证和授权。 骰子的工作方式是：将启动分成多个层，并根据唯一的设备机密 (UDS) 来创建每个层和配置的唯一机密。 如果启动了不同的代码或配置，则在链中的任何位置，机密都是不同的。 若要详细了解 [有关骰子的](https://trustedcomputinggroup.org/work-groups/dice-architectures/)详细信息，请参阅。有关配置 Azure Percept Studio 和所需服务的访问权限，请参阅下面的为 **Azure Percept 配置防火墙** 。

Azure Percept 设备使用硬件根信任来保护固件。 启动 ROM 确保 ROM 和操作系统 (OS) 加载器之间的固件完整性，进而确保创建信任链的其他软件组件的完整性。

## <a name="services"></a>服务

### <a name="iot-edge"></a>IoT Edge

Azure Percept 深色使用传输层安全性 (TLS) 协议安全地连接到 Azure Percept Studio 和其他 Azure 服务。 Azure Percept 深色是启用 Azure IoT Edge 的设备。 IoT Edge 运行时是将设备转换为 IoT Edge 设备的程序的集合。 在 IoT Edge 运行时组件的共同作用下，IoT Edge 设备可以接收要在边缘上运行的代码并传递结果。 Azure Percept 深色利用 Docker 容器来隔离基于主机操作系统和启用边缘的应用程序的 IoT Edge 工作负荷。 有关 Azure IoT Edge 安全框架的详细信息，请阅读有关 [IoT Edge 安全管理器](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager?view=iotedge-2018-06)的内容。

### <a name="device-update-for-iot-hub"></a>IoT 中心的设备更新

IoT 中心的设备更新支持对 Azure Percept 设备进行可续订安全的安全、可缩放且可靠的无线更新。 它通过见解提供丰富的管理控制和更新符合性。 Azure Percept 深色包含一个预先集成的设备更新解决方案，该解决方案提供从固件到 OS 层的弹性更新 (A/B) 。

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>为 Azure Percept 深色配置防火墙

如果网络设置要求显式允许从 Azure Percept 深色设备进行的连接，请查看以下组件列表。

此清单可作为防火墙规则的入手点：

|URL ( * = 通配符)  |出站 TCP 端口|    使用情况|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    Azure 深色 SOM 身份验证和授权|
|*. auth.projectsantacruz.azure.net| 443|    Azure 深色 SOM 身份验证和授权|

此外，请查看 [AZURE IOT Edge 使用的连接](https://docs.microsoft.com/azure/iot-edge/production-checklist?view=iotedge-2018-06#allow-connections-from-iot-edge-devices)列表。

## <a name="additional-recommendations-for-deployment-to-production"></a>部署到生产环境的其他建议

Azure Percept 深色提供现成的各种安全功能。 除了当前版本中包含的功能强大的安全功能，Microsoft 还建议在考虑生产部署时遵循以下准则：

- 设备本身的强大物理保护
- 确保启用静态数据加密
- 持续监视设备状况并快速响应警报
- 限制有权访问该设备的管理员

## <a name="next-steps"></a>后续步骤

了解可用的 [Azure PERCEPT AI 模型](./overview-ai-models.md)。