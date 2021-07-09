---
title: 快速入门：安全代理概述
description: 本快速入门将介绍如何了解 Azure Defender for IoT 服务中使用的代理专用安全代理体系结构。
ms.topic: quickstart
ms.date: 4/4/2021
ms.openlocfilehash: 0937cccb0335f4eee16ca3590babe9574320b89f
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113011036"
---
# <a name="quickstart-security-agent-reference-architecture"></a>快速入门：安全代理参考体系结构

Azure Defender for IoT 为通过 IoT 中心记录、处理、聚合和发送安全数据的安全代理提供了参考体系结构。

安全代理设计为在受约束的 IoT 环境中工作，并且与它们所使用的资源相比，可高度自定义以实现不同的价值。

安全代理不支持以下功能：

- 使用现有的设备标识或专用模块标识进行身份验证。 若要了解详细信息，请参阅 [安全代理身份验证方法](concept-security-agent-authentication-methods.md)。

- 从基础操作系统（Linux、Windows）收集原始安全事件。 请参阅 [Defender for IoT 代理配置](how-to-agent-configuration.md)详细了解可用的安全数据收集器。

- 将原始安全事件聚合到通过 IoT 中心发送的消息中。

- 使用 azureiotsecurity 模块孪生远程配置。 若要了解详细信息，请参阅[配置 Defender for IoT 代理](how-to-agent-configuration.md)。

Defender for IoT 安全代理作为开源项目开发，可从 GitHub 获得：

- [Defender for IoT 基于 C 的代理](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender for IoT 基于 C# 的代理](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="prerequisites"></a>先决条件

- 无

## <a name="agent-supported-platforms"></a>代理支持的平台

Defender for IoT 为 32 位和 64 位 Windows 提供不同的安装程序代理，为 32 位和 64 位 Linux 提供相同的安装程序代理。 请确保按照下表为每个设备设置正确的代理安装程序：

| 体系结构 | Linux | Windows | 详细信息 |
|--|--|--|--|
| 32 位 | C | C# |  |
| 64 位 | C# 或 C | C# | 建议在具有更多受限或最少设备资源的设备中使用 C 代理。 |


## <a name="next-steps"></a>后续步骤

本文提供了有关 Defender for IoT Defender-IoT-micro-agent 体系结构以及可用的安装程序的综合概述。
若要继续着手部署 Defender for IoT， 

> [!div class="nextstepaction"]
> [安全代理身份验证方法](concept-security-agent-authentication-methods.md)
