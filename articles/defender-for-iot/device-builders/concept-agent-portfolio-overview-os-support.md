---
title: 代理组合概述和操作系统支持（预览）
description: Azure Defender for IoT 基于设备类型提供一系列的代理。
ms.date: 08/08/2021
ms.topic: conceptual
ms.openlocfilehash: 84cc68bc30925efbff4a67db315efdb923531a45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722319"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>代理组合概述和操作系统支持（预览）

Azure Defender for IoT 基于设备类型提供一系列的代理。

## <a name="standalone-agent"></a>独立代理

独立代理涵盖大多数 Linux 操作系统 (OS)，它们可作为二进制包或源代码部署，而源代码可整合为固件的一部分，使你能够根据客户需求进行修改和自定义。 下面是一些受支持的 OS 示例：

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
|--|--|--|--|
| Debian 9 | ✓ | ✓ | |
| Ubuntu 18.04 | ✓ |  | ✓ |
| Ubuntu 20.04 | ✓ |  | |

若要了解详细信息、受支持的操作系统，或者要请求访问源代码以便将它整合为设备固件的一部分，请联系你的帐户管理员或发送电子邮件至 <defender_micro_agent@microsoft.com>。

## <a name="azure-rtos-micro-agent"></a>Azure RTO 微代理

Azure Defender for IoT 微代理为使用 Azure RTOS 的设备提供一个全面轻量级的安全解决方案。 Azure Defender for IoT 微代理涵盖实时操作系统 (RTOS) 设备上的常见威胁和潜在恶意活动。 微代理作为 Azure RTOS NetX Duo 组件的一部分内置到其中，可监视设备的网络活动。 

Azure Defender for IoT 微代理作为 Azure RTOS NetX Duo 组件的一部分内置到其中，可监视设备的网络活动。 微代理包含一个全面轻量级的安全解决方案，它涵盖了实时操作系统 (RTOS) 设备上的常见威胁和潜在恶意活动。

## <a name="next-steps"></a>后续步骤

详细了解[独立微代理概述（预览）](concept-standalone-micro-agent-overview.md)。
