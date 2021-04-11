---
title: Azure 工业 IoT 平台
description: 本文提供有关工业 IoT 平台及其组件的概述。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801547"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>什么是工业 IoT（IIoT）平台？

Azure 工业 IoT 平台是在 Azure 上部署的 Microsoft 模块和服务套件。 这些模块和服务具有完全的开放性。 具体来说，我们应用 Azure 的托管平台即服务（PaaS）产品/服务、通过 MIT 许可证授予的开源软件、开放用于通信的国际标准（OPC UA、AMQP、MQTT、HTTP）和接口（开放 API），以及在边缘和云中开放工业数据模型（OPC UA）。

## <a name="enabling-shopfloor-connectivity"></a>启用车间连接 

Azure 工业 IoT 平台涵盖了车间资产的工业连接（包括发现启用了 OPC UA 的资产），将其数据标准化为 OPC UA 格式，并以 OPC UA PubSub 格式将资产遥测数据传输到 Azure。 在那里，它将遥测数据存储在云数据库中。 此外，该平台允许通过 OPC UA 从云中安全访问车间资产。 还内置了设备管理功能（包括安全性配置）。 OPC UA 功能已使用 Docker 容器技术生成，以便进行部署和管理。 对于非 OPC UA 启用的资产，我们已与领先的行业连接提供商合作，帮助他们将其 OPC UA 适配器软件移植到 Azure IoT Edge。 Azure 市场中提供了这些适配器。

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>工业 IoT 组件：IoT Edge 模块和云微服务

边缘服务以 Azure IoT Edge 模块方式实现并在本地运行。 云微服务以 ASP.NET 微服务方式实现，带有 REST 接口，并在托管的 Azure Kubernetes 服务上运行或在 Azure 应用服务上独立。 对于边缘服务和云服务，我们已在 Microsoft Container Registry（MCR）中提供预建的 Docker 容器，为客户删除此步骤。 边缘和云服务相互利用，并且必须一起使用。 我们还提供了易于使用的部署脚本，允许使用一个命令部署整个平台。

## <a name="next-steps"></a>后续步骤

了解 Azure 工业 IoT 平台的定义后，即可了解 OPC 发布服务器：

> [!div class="nextstepaction"]
> [什么是 OPC 发布服务器？](overview-what-is-opc-publisher.md)