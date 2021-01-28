---
title: 作为 Azure IoT Edge 模块的机密应用程序
description: 使用 Open Enclave SDK 和 API 编写机密的应用程序并将其部署为机密计算 IoT Edge 模块
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: e63397176cae87178cfc5cc343aeed6f76099be2
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956456"
---
# <a name="confidential-computing-at-the-edge"></a>边缘上的机密计算

Azure IoT Edge 支持在设备上的 secure enclaves 中运行的机密应用程序。 加密会为传输中的数据或静态数据提供安全性，但 enclave 会为正在使用的数据和工作负载提供安全性。 IoT Edge 支持打开 Enclave 作为开发机密应用程序的标准。

安全性一直是物联网 (IoT) 的重要关注点，因为 IoT 设备通常位于外部世界，而不是在专用设施内受到保护。 这样的暴露会使设备面临篡改和伪造的风险，因为它们在物理上可由行为不端的人访问。 IoT Edge 设备甚至对信任和完整性有更多需求，因为它们允许敏感工作负载在边缘上运行。 与常见传感器和传动器不同，这些智能边缘设备可能会暴露以前仅在受保护的云或本地环境中运行的敏感工作负载。

[IoT Edge 安全管理器](iot-edge-security-manager.md)解决了一部分的机密计算难题。 安全管理器使用硬件安全模块 (HSM) 来保护 IoT Edge 设备的标识工作负载和正在进行的进程。

机密计算的另一个方面是保护正在边缘上使用的数据。 *受信任的执行环境* (TEE) 是处理器上的一种安全的隔离环境，有时称为“enclave”。 *机密应用* 程序是在 enclave 中运行的应用程序。 由于 enclaves 的性质，机密应用程序可防止在主处理器或 t a 中运行的其他应用程序受到保护。

## <a name="confidential-applications-on-iot-edge"></a>IoT Edge 上的机密应用程序

机密应用程序在传输和静态中加密，并且仅在受信任的执行环境内进行解密。 此标准适用于部署为 IoT Edge 模块的机密应用程序。

开发人员创建机密应用程序并将其打包为 IoT Edge 模块。 应用程序在推送到容器注册表之前会进行加密。 在 IoT Edge 设备上启动该模块之前，应用程序将在整个 IoT Edge 部署过程中保持“已加密”状态。 当机密应用程序在设备的 "t" 中时，将对其进行解密，并开始执行。

![关系图-在部署到安全 enclave 之前，机密应用程序在 IoT Edge 模块内进行加密](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

IoT Edge 上的机密应用程序是 [Azure 机密计算](../confidential-computing/overview.md)的逻辑扩展。 在云中安全 enclaves 中运行的工作负荷也可以部署到安全 enclaves 中的边缘运行。

## <a name="open-enclave"></a>开放式 Enclave

[Open ENCLAVE SDK](https://openenclave.io/sdk/)是一个开源项目，可帮助开发人员为多个平台和环境创建机密应用程序。 Open Enclave SDK 在设备的受信任执行环境中运行，而 Open Enclave API 则充当 TEE 和非 TEE 处理环境之间的接口。

Open Enclave 支持多个硬件平台。 IoT Edge 对 enclave 的支持目前需要 Open Portable TEE 操作系统 (OP-TEE OS)。 若要了解详细信息，请参阅[用于 OP-TEE OS 的Open Enclave SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md)。

Open Enclave 存储库还包含用于帮助开发人员入门的示例。 有关详细信息，请选择以下的介绍性文章之一：

* [在 Linux 上生成 Open Enclave SDK 示例](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [在 Windows 上生成 Open Enclave SDK 示例](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>硬件

目前， [TrustBox By Scalys](https://scalys.com/trustbox-industrial/) 是制造商服务协议支持的唯一设备，用于将机密应用程序部署为 IoT Edge 模块。 TrustBox 基于 TrustBox Edge 和 TrustBox EdgeXL 设备而构建，这两种设备均预加载了 Open Enclave SDK 和 Azure IoT Edge。

有关详细信息，请参阅 [Getting started with Open Enclave for the Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started)（开始为 Scalys TrustBox 使用 Open Enclave）。

## <a name="develop-and-deploy"></a>开发和部署

准备好开发和部署你的机密应用程序时，适用于 Visual Studio Code 的 [Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) 扩展可以提供帮助。 可以使用 Linux 或 Windows 作为开发计算机，以便针对 TrustBox 开发模块。

## <a name="next-steps"></a>后续步骤

了解如何开始将机密应用程序作为带有[Open Enclave extension for Visual Studio Code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension) IoT Edge 模块开发
