---
title: 主要优点
description: 了解 Defender for IoT 的基本概念。
ms.date: 09/09/2021
ms.topic: article
ms.openlocfilehash: be5c7f31cb652bb40f4bae35fa42b76d7a576276
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836466"
---
# <a name="basic-concepts"></a>基本概念

本文介绍 Azure Defender for IoT 的主要优点。

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>快速非入侵性的部署和被动监视

Defender for IoT 传感器连接到 SPAN（镜像）端口和网络 TAP，并立即开始通过被动（无代理）监视来收集 ICS 网络流量。 深度数据包检查 (DPI) 用于仔细分析来自串行和以太网控制网络设备的流量。 Defender for IoT 不会对 OT 网络造成任何影响，因为它不会放置在数据路径中，也不会主动扫描 OT 设备。 

若要提供详细的 Windows 设备信息的即时快照，可以将 Defender for IoT 传感器配置为使用可选的活动组件来补充被动监视。 此组件使用安全的供应商批准的命令来查询 Windows 设备以获取设备详细信息，次数随意。

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>ICS 协议、设备和应用程序的嵌入知识

只靠 DPI 不足以标识协议异常和在粒度级别标识设备。 Defender for IoT 传感器可解决一些最大且最复杂的环境。 到目前为止，已在各个行业中分析了超过 1,300 个 OT 网络。

## <a name="analytics-and-self-learning-engines"></a>分析和自学习引擎

引擎通过连续监视和五个分析引擎来识别安全问题，这些引擎合并自学习，以避免需要更新签名或定义规则。 这些引擎使用特定于 ICS 的行为分析和数据科学来连续分析 OT 网络流量的异常。 这五个引擎包括：

- 协议冲突检测：识别违反 ICS 协议规范的数据包结构或字段值的使用。

- 策略冲突检测：识别策略冲突，如未经授权使用函数代码、对特定对象的访问或对设备配置的更改。

- 工业恶意软件检测：识别指示存在已知恶意软件（如 Conficker、Black Energy、Havex、WannaCry 和 NotPetya）的行为。

- 异常情况检测：检测异常计算机到计算机 (M2M) 通信和行为。 通过将 ICS 网络建模为确定性的状态和转换序列，该引擎使用一种称为工业有限状态建模 (IFSM) 的专利技术。 解决方案需要比常规数学方法或分析更短的学习周期，常规数学方法或分析最初是为 IT 而不是 OT 开发的。 它还能更快地检测到异常，且误报率最低。

- 操作事件检测：识别操作问题（如间歇性连接），这些问题可指示设备故障的早期迹象。

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>风险和漏洞评估的网络流量分析

Defender for IoT 在业内独树一帜，它使用专有的网络流量分析 (NTA) 算法来被动标识所有网络和终结点漏洞，例如：

- 未经授权的远程访问连接
- 恶意或未记录的设备
- 弱身份验证
- 易受攻击的设备（基于未修补的 CVE）
- 子网之间的未授权桥
- 弱防火墙规则

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>调查、取证和威胁搜寻的数据挖掘

该平台提供直观的数据挖掘界面，用于在所有相关维度中精细搜索历史流量。 示例包括时间段、IP 地址、MAC 地址和端口。 还可以根据函数代码、协议服务和模块进行特定于协议的查询。 全保真的 PCAP 可用于进一步向下钻取分析。

## <a name="sensor-cloud-management-mode"></a>传感器云管理模式

传感器云管理模式确定显示传感器检测到的设备、警报和其他信息的位置。

对于连接到云的传感器，传感器检测到的信息显示在传感器控制台中。 警报信息通过 IoT 中心进行传递，并且可以与其他 Azure 服务（例如 Azure Sentinel）共享。

对于本地连接的传感器，传感器检测到的信息显示在传感器控制台中。 如果传感器连接到本地管理控制台，则还会与本地管理控制台共享检测信息。

## <a name="air-gapped-networks"></a>气隙网络

如果你在气隙环境中工作，则 Defender for IoT 中的本地管理控制台将提供与各设施相关的关键 IoT 和 OT 风险指标和警报的实时视图。 它紧密集成了 SOC 工作流和 runbook，可轻松确定缓解活动的优先级和整个站点的威胁风险。

Defender for IoT 提供所有设备的合并视图。 它还提供有关设备的关键信息，如类型（PLC、RTU、DCS 等）、制造商、型号和固件修订级别以及警报信息。

借助 Defender for IoT，可以有效地管理多个部署和全面统一的网络视图。 Defender for IoT 优化了操作网络安全的警报处理和控制。

本地管理控制台是一个基于 Web 的管理平台，可让你监视和控制全局传感器安装的活动。 除了管理从已部署传感器收到的数据以外，本地管理控制台还可无缝集成来自各种业务资源的数据：CMDB、DNS、防火墙、Web API 等。

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="本地管理控制台显示。":::

在使用本地管理控制台之前，建议先熟悉可供传感器使用的概念、功能和特性。

## <a name="integrations"></a>集成

可以通过与合作伙伴系统共享设备信息和警报信息来扩展 Defender for IoT 功能。 集成可帮助企业桥接以前孤立的安全解决方案，从而显著增强设备的可见性和威胁智能。 集成还可帮助企业加速系统范围内的响应，从而更快地降低风险。 

集成可降低复杂性并消除 IT 和 OT 接收器，方法是将其集成到现有 SOC 工作流和安全堆栈中。 例如：

- SIEM，如 IBM QRadar、Splunk、ArcSight、LogRhythm 和 RSA NetWitness

- 安全业务流程和票证系统，如 ServiceNow 和 IBM Resilient

- 安全远程访问解决方案，如 CyberArk 特权会话管理器 (PSM) 和 BeyondTrust

- 安全网络访问控制 (NAC) 系统，如 Aruba ClearPass 和 Forescout CounterACT

- 防火墙，如 Fortinet 和 Check Point

## <a name="complete-protocol-support"></a>完整的协议支持

除嵌入式协议支持外，还可以保护运行专有和自定义协议或非标准协议的 IoT 和 ICS 设备。 使用 Horizon 开放式开发环境 (ODE) SDK，开发人员可以创建析像器插件，用于基于定义的协议解码网络流量。 服务分析流量，以提供完整的监视、警报和报告。 使用 Horizon 可以：

- 无需升级到新版本即可扩展可见性和控件。

- 通过现场开发以外部插件的形式来保护专有信息。

- 将警报、事件和协议参数的文本本地化。

此外，还可以使用专有协议警报来传达信息：

- 关于基于专有 Horizon 插件中的协议和基础协议的流量检测。

- 关于所有协议层的协议字段的组合。 例如，在运行 MODBUS 的环境中，你可能希望在传感器检测到对特定 IP 地址和以太网目标上的内存寄存器发出 write 命令时生成警报。 或者你可能希望在对特定 IP 地址执行任何访问时生成警报。

满足 Horizon 警报规则条件时，将触发警报。

此外，使用 Horizon 自定义警报可以编写自己的警报标题和消息。 可以将已解析的协议字段和值嵌入到警报消息文本中。

使用基于条件的自定义警报触发和消息传递可帮助查明特定的网络活动，并有效地将最新情况告知安全、IT 和运营团队。

有关受支持协议的完整列表，请参阅[支持的协议](concept-supported-protocols.md#supported-protocols)。

## <a name="high-availability"></a>高可用性

通过安装本地管理控制台中的高可用性设备，提高你的 Defender for IoT 部署的复原能力。 高可用性部署确保你的托管传感器持续向活动的本地管理控制台进行报告。

此部署的实现需要一个本地管理控制台对，其中包括一个主要设备和一个辅助设备。

## <a name="localization"></a>本地化

许多控制台功能支持多种语言。

## <a name="next-step"></a>后续步骤

[Defender for IoT 入门](getting-started.md)
