---
title: 服务概述
description: 了解有关适用于 IoT 功能和服务的 Defender 的详细信息，并了解用于 IoT 的 Defender 如何提供全面的 IoT 安全性。
services: defender-for-iot
ms.service: azure
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2020
ms.openlocfilehash: 5b4ab207462955be3876dc1a25fae491e48a9cd2
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621704"
---
# <a name="welcome-to-azure-defender-for-iot"></a>欢迎使用 Azure Defender for IoT

操作技术 () 网络为我们的社会的许多最重要方面提供强大功能。 但其中的许多技术在设计上都没有考虑到安全性，无法通过传统的 IT 安全控制来保护。 同时，物联网 (IoT) 正在使用数十亿个连接的设备实现全新的创新，同时增加了攻击面和风险。  

用于 IoT 的 Azure Defender 是一种统一的安全解决方案，用于识别 IoT/OT 设备、漏洞和威胁。 它使你能够保护整个 IoT/OT 环境，无论你是否需要保护现有的 IoT/OT 设备，或将安全性构建为新的 IoT 创新。  

Azure Defender for IoT 提供了两组功能，以满足环境需求。

对于具有 IoT/OT 环境的最终用户组织，Azure Defender IoT 提供无代理的网络层监视：

- 可以快速部署。
- 与不同的工业设备和 SOC 工具轻松集成。
- 对 IoT/OT 网络性能或稳定性产生零影响。 

平台可以完全部署在本地，也可以部署在 Azure 连接的环境和混合环境中。  

对于 IoT 设备构建者，Azure Defender IoT 还提供支持标准 IoT 操作系统（如 Linux 和 RTO）的轻型版代理。 此轻型代理可帮助确保将安全内置于你从边缘到云的活动中。 它包括灵活的可自定义部署的源代码。 

## <a name="agentless-solution-for-organizations"></a>组织的无代理解决方案 

较旧的 IoT 和设备不支持代理，通常不会对其进行修补、配置错误，也不会对 IT 团队可见。 对于想要透视更深层企业网络的威胁参与者，这些质量会使其成为软目标。 

为公司 IT 网络开发的传统网络安全监视工具无法满足这些环境的需要，因为他们对 IoT 和 OT 环境中发现的专用协议、设备和计算机到计算机 (M2M) 行为缺乏深入了解。 

Azure Defender for IoT 中的无代理监视功能可为这些网络提供可见性和安全性。 然后，你可以解决这些环境的关键问题。 

### <a name="automatic-device-discovery"></a>自动设备发现  

使用被动的无代理网络监视来获取所有 IoT/OT 设备的完整清单、其详细信息以及它们的通信方式，而不会影响 IoT/OT 网络。  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>主动查看风险和漏洞
 
确定 IoT/OT 环境中的风险和漏洞。 例如，确定未修补的设备、开放端口、未经授权的应用程序和未授权的连接。 你还可以确定对设备配置、PLC 代码和固件的更改。 

### <a name="iotot-threat-detection"></a>IoT/OT 威胁检测  

利用专门的 IoT/项感知威胁情报和行为分析来检测异常或未经授权的活动。 你甚至可以检测静态 Ioc 丢失的高级威胁，如零天恶意软件、fileless 恶意软件和客厅策略。 

### <a name="unified-security-management-across-iotot"></a>跨 IoT/OT 的统一安全管理

集成到 Azure Sentinel，为整个组织提供鸟瞰视图。 通过集成到现有工作流（包括 Splunk、IBM QRadar 和 ServiceNow 等第三方工具）实现统一的 IoT/OT 安全监管。 

## <a name="agent-based-solution-for-device-builders"></a>适用于设备构建者的基于代理的解决方案 

安全对于 IoT 实施者来说是一个近乎普遍的问题。 IoT 设备对于终结点监视、安全状态管理和威胁检测具有独特的需求–所有这些都具有高度特定的性能要求。 

Azure Defender for IoT 安全代理允许你直接在新的 IoT 设备和 Azure IoT 项目中构建安全性。 微代理具有灵活的部署选项，包括将部署为二进制包或修改源代码的功能。 小型代理适用于标准 IoT 操作系统，如 Linux 和 Azure RTO。  

Azure Defender for IoT 微代理提供对安全状态管理、威胁检测以及与 Microsoft 的其他安全工具（用于统一安全管理）的集成。 

### <a name="security-posture-management"></a>安全状况管理

主动监视 IoT 设备的安全状况。 Azure Defender for IoT 提供基于 CIS 基准的安全状况建议，以及设备特定的建议。 获取操作系统安全性的可见性，包括操作系统配置、防火墙配置和权限。 

### <a name="endpoint-iotot-threat-detection"></a>终结点 IoT/OT 威胁检测

检测威胁，如僵尸网络、暴力破解尝试、加密挖掘者采用和可疑网络活动。 创建自定义警报，以在你的独特组织中以最重要的威胁为目标。 

### <a name="flexible-distribution-and-deployment-models"></a>灵活的分发和部署模型 

Azure Defender for IoT 微代理包括源代码，因此你可以将微代理并入固件，或对其进行自定义以仅包含你需要的内容。 它还可作为二进制包提供，也可直接集成到其他 Azure IoT 解决方案。 

## <a name="see-also"></a>另请参阅

[Azure Defender for IoT 体系结构](architecture.md)