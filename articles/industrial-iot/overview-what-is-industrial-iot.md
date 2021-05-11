---
title: Azure 工业 IoT 概述
description: 本文提供了工业 IoT 的概述。 它介绍了 IIoT 中的车间连接和安全组件。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813265"
---
# <a name="what-is-industrial-iot-iiot"></a>什么是工业 IoT (IIoT)？

![工业 IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure 工业物联网 (IIoT) 是将云的强大功能集成到工业及生产车间的一套 Azure 模块和服务。 通过使用[开放平台通信统一体系结构 (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/) 之类的行业标准开放接口，Azure IIoT 使你能够将资产和传感器中的数据（包括已在工厂车间中运行的那些数据）集成到 Azure 云中。 据开发转型业务和工业流程的反馈，将数据放入云中后，可更快、更灵活地使用这些数据。

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>通过 Azure 发现、注册和管理工业资产

使用 Azure 工业 IoT，工厂操作员可以发现工厂网络中已启用 OPC UA 的服务器，并将这些服务器注册到 Azure IoT 中心。 操作人员可以从世界各地的任何地点订阅和响应工厂的事件，接收警报和告警，并实时对它们做出反应。

IIoT 提供一组用于实现 OPC UA 功能的微服务。 微服务 REST API 将镜像 OPC UA 服务边缘。 使用 Azure Active Directory (AAD) 支持的 OAUTH 身份验证和授权来保护这些服务。 这使你的云应用程序能够浏览服务器地址空间或读/写变量，并使用 HTTPS 和简单的 OPC UA JSON 负载来执行各种方法。 边缘服务以 Azure IoT Edge 模块方式实现并在本地运行。 云微服务以 ASP.NET 微服务方式实现，带有 REST 接口，并在托管的 Azure Kubernetes 服务上运行或在 Azure 应用服务上独立。 对于边缘服务和云服务，IIoT 在 Microsoft Container Registry (MCR) 中提供预生成的 Docker 容器，为客户省去了这一步。 边缘和云服务相互利用，并且必须一起使用。 IIoT 还提供了易于使用的部署脚本，允许使用一个命令部署整个平台。

此外，REST API 可通过其公开的开放 API 规范 (Swagger) 与任何编程语言一起使用。 这意味着，在将 OPC UA 集成到云管理解决方案中时，开发人员可自由选择与他们的技能、兴趣和体系结构选择相匹配的技术。 例如，开发警报和事件仪表板应用程序的完整堆栈 Web 开发人员能够以 JavaScript 或 TypeScript 编写逻辑来响应事件，而无需加深对 OPC UA SDK、C、C++、Java 或 C# 的了解。

## <a name="manage-certificates-and-trust-groups"></a>管理证书和信任组

Azure 工业 IoT 管理 OPC UA 应用程序证书以及工厂车间机械和控制系统的信任列表，以保持 OPC UA 客户端与服务器通信的安全。 它对允许哪个客户端与哪个服务器通信进行了限制。 私钥的存储和证书的签名由 Azure Key Vault 支持，Azure Key Vault 支持基于硬件的安全性 (HSM)。

## <a name="industrial-iot-components"></a>工业 IoT 组件

Azure IIoT 解决方案基于特定组件生成而来。 这些组件包括：

- 至少一个 Azure IoT 中心。
- IoT Edge 设备。
- 工业 Edge 模块。

### <a name="iot-hub"></a>IoT 中心
[Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/ )充当中央消息中心，用于在任何 IoT 应用程序及其管理的设备之间实现安全的双向通信。 它是一个开放且灵活的云平台即服务 (PaaS)，支持开源 SDK 和多种协议。 

通过将工业和业务数据收集到 IoT 中心，可安全地存储数据，对其执行业务和效率分析，并基于其生成报告。 你还可以对合并的数据应用 Microsoft Azure 服务和工具，如 [Power BI](https://powerbi.microsoft.com)。

### <a name="iot-edge-devices"></a>IoT Edge 设备
[边缘服务](https://azure.microsoft.com/services/iot-edge/)以 Azure IoT Edge 模块方式实现并在本地运行。 云微服务以 ASP.NET 微服务方式实现，带有 REST 接口，并在托管的 Azure Kubernetes 服务上运行或在 Azure 应用服务上独立。 对于边缘服务和云服务，我们已在 Microsoft Container Registry（MCR）中提供预建的 Docker 容器，为客户删除此步骤。 边缘和云服务相互利用，并且必须一起使用。 我们还提供了易于使用的部署脚本，允许使用一个命令部署整个平台。

IoT Edge 设备由 Edge 运行时和 Edge 模块组成。
- **边缘模块** 是 docker 容器，而这些容器又是最小的计算单位，如 OPC 发布服务器和 OPC 孪生。 
- **边缘设备** 用于部署此类模块，这些模块充当 OPC UA 服务器与云中的 IoT 中心之间的媒介。

### <a name="industrial-edge-modules"></a>工业边缘模块
- **OPC 发布服务器**：OPC 发布服务器在 IoT Edge 中运行。 OPC 发布服务器连接到 OPC UA 服务器，并将来自这些服务器的 JSON 编码遥测数据发送到 Azure IoT 中心（以 OPC UA“Pub/Sub”格式）。 可以使用 Azure IoT 中心客户端 SDK 支持的所有传输协议，即 HTTPS、AMQP 和 MQTT。
- **OPC 孪生**：PC 孪生由使用 Azure IoT Edge 和 IoT 中心连接云与工厂网络的微服务构成。 OPC 孪生通过 REST API 提供发现、注册和远程控制工业设备的功能。 OPC 克隆不需要 OPC 统一体系结构 (OPC UA) SDK。 OPC 克隆对编程语言不可知，可包含在无服务器工作流中。
- **发现**：发现模块（由发现器标识表示）在边缘提供发现服务，其中包括 OPC UA 服务器发现。 如果已配置并已启用发现，则发现模块将通过 IoT Edge 和 IoT 中心遥测路径向载入服务发送扫描探测的结果。 服务处理结果，并更新注册表中所有相关的标识。

## <a name="next-steps"></a>后续步骤
现在你已了解什么是工业 IoT，可以阅读有关工业 IoT 平台和 OPC 发布服务器的内容：

> [!div class="nextstepaction"]
> [什么是 OPC 发布服务器？](overview-what-is-opc-publisher.md)