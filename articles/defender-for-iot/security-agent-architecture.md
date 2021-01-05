---
title: 安全代理体系结构
description: 了解 Azure Defender for IoT 服务中所使用代理的安全代理体系结构。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 5773f7af3bb065976e8f05d7b54c58b90da2d3d2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835119"
---
# <a name="security-agent-reference-architecture"></a>安全代理参考体系结构

Azure Defender for IoT 为通过 IoT 中心记录、处理、聚合和发送安全数据的安全代理提供了参考体系结构。

安全代理设计为在受约束的 IoT 环境中工作，并且与它们所使用的资源相比，可高度自定义以实现不同的价值。

安全代理不支持以下功能：

- 从基础操作系统（Linux、Windows）收集原始安全事件。 请参阅 [Defender for IoT 代理配置](how-to-agent-configuration.md)详细了解可用的安全数据收集器。

- 将原始安全事件聚合到通过 IoT 中心发送的消息中。

- 使用现有的设备标识或专用模块标识进行身份验证。 请参阅[安全代理身份验证方法](concept-security-agent-authentication-methods.md)了解详细信息。

- 使用 azureiotsecurity 模块孪生远程配置。 请参阅[配置 Defender for IoT 代理](how-to-agent-configuration.md)了解详细信息。

Defender for IoT 安全代理作为开源项目开发，可从 GitHub 获得：

- [Defender for IoT 基于 C 的代理](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender for IoT 基于 C# 的代理](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>代理支持的平台

Defender for IoT 为 32 位及 64 位 Windows 和 Linux 提供不同的安装程序代理。 请确保按照下表为每个设备设置正确的代理安装程序：

| 体系结构 | Linux | Windows | 详细信息 |
|--|--|--|--|
| 32 位 | C | C# |  |
| 64 位 | C# 或 C | C# | 建议在具有更多受限或最少设备资源的设备中使用 C 代理。 |


## <a name="next-steps"></a>后续步骤

本文介绍了 Defender for IoT 安全代理聚合体系结构以及可用的安装程序。

请参阅以下文章继续着手部署 Defender for IoT：

- 了解[安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- 选择并部署[安全代理](how-to-deploy-agent.md)
- 查看适用于 IoT[系统先决条件](quickstart-system-prerequisites.md)的 Defender
- 了解如何[在 IoT 中心启用 Defender for IoT 服务](quickstart-onboard-iot-hub.md)
- 通过 [Defender for IoT 常见问题解答](resources-frequently-asked-questions.md)详细了解该服务
