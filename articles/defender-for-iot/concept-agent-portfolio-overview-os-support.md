---
title: 代理项目组合概述和操作系统支持
description: Azure Defender for IoT 基于设备类型提供一系列的代理。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: e2897018d1695bde665e1d1aca180e5268851a0b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120141"
---
# <a name="agent-portfolio-overview-and-os-support"></a>代理项目组合概述和操作系统支持 

Azure Defender for IoT 基于设备类型提供一系列的代理。 

## <a name="standalone-agent"></a>独立代理

独立代理涵盖大多数 Linux 操作系统，它们可作为二进制包或源代码部署，而源代码可整合为固件的一部分，使你能够根据客户需求进行修改和自定义。 OS 支持示例： 

| 操作系统 | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

若要了解更多详细信息和操作系统支持，或者要请求访问源代码，以便将它整合为设备固件的一部分，请联系你的帐户管理员或发送电子邮件至 <defender_micro_agent@microsoft.com>。 

## <a name="azure-rtos-micro-agent"></a>Azure RTO 微代理

Azure Defender for IoT 微代理为使用 Azure RTOS 的设备提供一个全面轻量级的安全解决方案。 Azure Defender for IoT 微代理涵盖实时操作系统 (RTOS) 设备上的常见威胁和潜在恶意活动。 微代理作为 Azure RTOS NetX Duo 组件的一部分内置到其中，可监视设备的网络活动。 

Azure Defender for IoT 微代理作为 Azure RTOS NetX Duo 组件的一部分内置到其中，可监视设备的网络活动。 微代理包含一个全面轻量级的安全解决方案，它涵盖了实时操作系统 (RTOS) 设备上的常见威胁和潜在恶意活动。

## <a name="next-steps"></a>后续步骤

详细了解[独立微代理概述](concept-standalone-micro-agent-overview.md)。