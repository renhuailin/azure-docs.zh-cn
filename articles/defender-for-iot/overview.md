---
title: 服务概述
description: 详细了解 Defender for IoT 功能和服务，并了解 Defender for IoT 如何提供全面的 IoT 安全性。
ms.topic: overview
ms.date: 12/09/2020
ms.openlocfilehash: 15772ee21587ed2bc010e31174af6daac71dfc12
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786832"
---
# <a name="welcome-to-azure-defender-for-iot"></a>欢迎使用 Azure Defender for IoT

操作技术 (OT) 网络为我们社会的许多最关键方面提供了动力。 但其中的许多技术在设计上都没有考虑到安全性，无法通过传统的 IT 安全控制加以保护。 同时，物联网 (IoT) 正在通过数十亿个连接的设备推动新一波创新，从而增加了攻击面和风险。  

Azure Defender for IoT 是一种统一的安全解决方案，用于识别 IoT/OT 设备、漏洞和威胁。 无论你是需要保护现有的 IoT/OT 设备还是为新的 IoT 创新构建安全性，它都使你能够保护整个 IoT/OT 环境。  

Azure Defender for IoT 提供了两组功能，以满足环境需求。

对于具有 IoT/OT 环境的最终用户组织，Azure Defender for IoT 提供无代理的网络层监视，该监视具有以下特点：

- 可以快速部署。
- 可与不同的工业设备和 SOC 工具轻松集成。
- 对 IoT/OT 网络性能或稳定性无影响。 

该平台可以完全部署在本地，也可以部署在 Azure 连接的环境和混合环境中。  

对于 IoT 设备构建者，Azure Defender for IoT 还提供支持标准 IoT 操作系统（如 Linux 和 RTO）的轻量级微代理。 此轻量级代理有助于确保从边缘到云的安全性已内置到 IoT/OT 计划中。 它包含用于灵活、可自定义部署的源代码。 

## <a name="agentless-solution-for-organizations"></a>适用于组织的无代理解决方案 

较旧的 IoT 和 OT 设备不支持代理，并且通常未修补、配置错误，并且对 IT 团队不可见。 对于想要深入透视企业网络的威胁参与者，这些质量会使其成为软目标。 

为公司 IT 网络开发的传统网络安全监视工具无法应对这些环境，因为它们缺乏对 IoT 和 OT 环境中的专用协议、设备和机器对机器 (M2M) 行为的深入理解。 

Azure Defender for IoT 中的无代理监视功能为你提供了这些网络的可见性和安全性。 然后，你可以解决这些环境的关键问题。 

### <a name="automatic-device-discovery"></a>自动设备发现  

使用被动的无代理网络监视来获取所有 IoT/OT 设备的完整清单、其详细信息以及它们的通信方式，而不影响 IoT/OT 网络。  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>主动查看风险和漏洞
 
确定 IoT/OT 环境中的风险和漏洞。 例如，确定未修补的设备、开放端口、未经授权的应用程序和未经授权的连接。 还可以确定对设备配置、PLC 代码和固件的更改。 

### <a name="iotot-threat-detection"></a>IoT/OT 威胁检测  

通过专门的 IoT/OT 感知威胁情报和行为分析来检测异常或未经授权的活动。 甚至可以检测到静态 IOC 错过的高级威胁，例如零时差恶意软件、无文件恶意软件和离地攻击策略。 

### <a name="unified-security-management-across-iotot"></a>跨 IoT/OT 的统一安全管理

集成到 Azure Sentinel 中以鸟瞰整个组织。 通过集成到现有工作流（包括 Splunk、IBM QRadar 和 ServiceNow 等第三方工具）实现统一的 IoT/OT 安全监管。 

## <a name="agent-based-solution-for-device-builders"></a>适用于设备构建者的基于代理的解决方案 

安全是 IoT 实施者普遍关注的问题。 IoT 设备对终结点监视、安全状况管理和威胁检测具有独特的需求，所有这些都具有高度特定的性能要求。 

Azure Defender for IoT 安全代理使你可以直接在新的 IoT 设备和 Azure IoT 项目中构建安全性。 微代理具有灵活的部署选项，包括以二进制包进行部署或修改源代码的功能。 微代理适用于标准 IoT 操作系统，如 Linux 和 Azure RTO。  

Azure Defender for IoT 微代理为安全状况管理、威胁检测提供了终结点可见性，并提供与 Microsoft 的其他安全工具的集成，以进行统一的安全管理。 

### <a name="security-posture-management"></a>安全状况管理

主动监视 IoT 设备的安全状况。 Azure Defender for IoT 提供基于 CIS 基准的安全状况建议，以及设备特定的建议。 了解操作系统安全性，包括 OS 配置、防火墙配置和权限。 

### <a name="endpoint-iotot-threat-detection"></a>终结点 IoT/OT 威胁检测

检测威胁，如僵尸网络、暴力攻击尝试、加密挖掘者和可疑网络活动。 创建自定义警报，以针对唯一的组织中最重要的威胁。 

### <a name="flexible-distribution-and-deployment-models"></a>灵活的分发和部署模型 

Azure Defender for IoT 微代理包括源代码，因此可以将微代理并入固件，或对其进行自定义以仅包含所需的内容。 它还可以作为二进制包提供，也可以直接集成到其他 Azure IoT 解决方案中。 

## <a name="see-also"></a>另请参阅

[Azure Defender for IoT 体系结构](architecture.md)