---
title: 主要优点
description: 了解 IoT 概念的基本 Defender。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: cb984ca1a74d8b3838b857f2f5679264d1445187
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508602"
---
# <a name="basic-concepts"></a>基本概念 

本文介绍 Azure Defender for IoT 的主要优点。

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>快速非侵害性的部署和被动监视

用于 IoT 传感器的 Defender 连接到交换机范围 (镜像) 端口和网络点击，并立即通过被动 (无代理) 监视立即开始收集 ICS 网络流量。 深层数据包检查 (DPI) 用于仔细分析来自串行和以太网控制网络设备的流量。 Defender for IoT 对每个网络不会有任何影响，因为它不会放置在数据路径中，也不会主动扫描 OT 设备。 

若要提供详细的 Windows 设备信息的即时快照，可以将 IoT 传感器的 Defender 配置为使用可选的活动组件来补充被动监视。 此组件使用安全的供应商批准的命令来查询 Windows 设备的设备详细信息，通常情况下或不经常需要。

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>ICS 协议、设备和应用程序的嵌入知识

仅使用 DPI 来识别协议异常并在精细级别标识设备。 用于 IoT 传感器的 Defender 能解决一些最大和最复杂的环境。 在所有行业部门中，已对超过1300个使用的网络进行了分析。

## <a name="analytics-and-self-learning-engines"></a>分析和自助学习引擎

引擎通过连续监视和五个分析引擎来识别安全问题，这些引擎合并了自学习，以消除更新签名或定义规则的需要。 这些引擎使用特定于 ICS 的行为分析和数据科学连续分析网络流量是否出现异常情况。 这五个引擎是：

- **协议冲突检测**：标识违反 ICS 协议规范的数据包结构和字段值的使用情况。

- **策略违规检测**：标识策略违规，如未经授权使用函数代码、对特定对象的访问权限或对设备配置的更改。

- **工业恶意软件检测**：标识指示存在已知恶意软件（如 Conficker、黑色能量、Havex、WannaCry 和 NotPetya）的行为。

- **异常情况检测**：检测 (M2M) 通信和行为的异常计算机到计算机。 通过将 ICS 网络作为确定性状态和转换的序列建模，引擎将使用一种名为工业有限状态建模 (IFSM) 的专利技术。 此解决方案需要比最初为其开发的一般数学方法或分析更短的学习时间，而不是。 它还能更快地检测到异常，且误报率最低。

- **操作事件检测**：标识一些操作问题，如间歇连接性，可以指示设备故障的前期迹象。

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>风险和漏洞评估的网络流量分析

在行业中独一无二，IoT 用于 IoT 使用专有的网络流量分析 (NTA) 算法，以被动标识所有网络和终结点漏洞，例如：

- 未经授权的远程访问连接
- 恶意或未记录的设备
- 弱身份验证
- 基于未修补的标识符)  (有漏洞的设备
- 子网之间的未授权桥
- 弱防火墙规则

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>调查、辩论和威胁搜寻的数据挖掘

该平台提供直观的数据挖掘接口，用于在所有相关维度中精细搜索历史流量。 示例包括时间段、IP 地址、MAC 地址和端口。 你还可以根据函数代码、协议服务和模块进行特定于协议的查询。 完全保真 PCAPs 可用于进一步向下钻取分析。

## <a name="sensor-cloud-management-mode"></a>传感器云管理模式

传感器云管理模式确定显示传感器检测到的设备、警报和其他信息的位置。

对于 **连接了云的传感器**，传感器检测的信息将显示在传感器控制台中。 警报信息通过 IoT 中心传送，可与其他 Azure 服务（如 Azure Sentinel）共享。

对于 **本地连接的传感器**，传感器检测的信息将显示在传感器控制台中。 如果传感器连接到本地管理控制台，则还会与本地管理控制台共享检测信息。

## <a name="air-gapped-networks"></a>气流网络

如果你在气流环境中工作，则 Defender for IoT 中的本地管理控制台将实时查看关键 IoT，并在所有设备中提供风险指标和警报。 它与 SOC 工作流和 runbook 紧密集成，可轻松确定缓解活动的优先级，以及威胁的跨站点关联。  

Defender for IoT 提供了所有设备的合并视图。 它还提供有关设备的关键信息，例如类型 (PLC、RTU、DC 和更高) 、制造商、型号和固件修订级别以及警报信息。  

使用 Defender for IoT，可以有效地管理多个部署和全面统一的网络视图。 Defender for IoT 优化了警报处理和控制操作网络安全。

本地管理控制台是一个基于 web 的管理平台，可让你监视和控制全局传感器安装的活动。 除了管理从部署的传感器接收的数据以外，本地管理控制台还可无缝集成来自各种业务资源的数据： CMDBs、DNS、防火墙、Web Api 等等。

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="本地管理控制台显示。":::

建议在使用本地管理控制台之前，先熟悉传感器的可用概念、功能和功能。

## <a name="integrations"></a>集成

可以通过与合作伙伴系统共享设备和警报信息，来扩展用于 IoT 的 Defender 功能。 集成有助于企业桥接以前孤立的安全解决方案，大大增强设备的可见性和威胁情报。 集成还有助于企业加速系统范围内的响应并更快地降低风险。 

集成通过将其集成到现有的 SOC 工作流和安全堆栈，降低了复杂性并消除了这些接收器。 例如：

- Siem，如 IBM QRadar、Splunk、ArcSight、LogRhythm 和 RSA NetWitness

- 安全业务流程和票证系统（如 ServiceNow 和 IBM 复原）

- 安全远程访问解决方案，如 CyberArk 特权会话管理器 (PSM) 和 BeyondTrust

-  (NAC) 系统（如 Aruba ClearPass 和 Forescout）的安全网络访问控制

- 防火墙（如 Fortinet 和检查点）

## <a name="complete-protocol-support"></a>完整的协议支持

除嵌入协议支持外，还可以保护运行专用和自定义协议的 IoT 和 ICS 设备，或者保护偏离任何标准的协议。 通过使用范围开放式开发环境 (O) ) SDK，开发人员可以创建基于定义的协议对网络流量进行解码的 dissector 插件。 服务会分析流量，以提供完整的监视、警报和报告功能。 使用 Horizon 可以：

- 无需升级到新版本即可扩展可见性和控件。

- 通过在现场上开发作为外部插件来保护专有信息。

- 将警报、事件和协议参数的文本本地化。

此外，还可以使用专用协议警报来传达信息：

- 关于基于专用范围插件中的协议和基础协议的通信检测。

- 关于来自所有协议层的协议字段的组合。 例如，在运行 MODBUS 的环境中，你可能想要在传感器检测到特定 IP 地址和以太网目标上的内存寄存器写入命令时生成警报。 或者，可能需要在对特定 IP 地址执行任何访问时生成警报。

满足范围警报规则条件时触发警报。

此外，使用范围自定义警报可以编写自己的警报标题和消息。 可以在警报消息文本中嵌入解析的协议字段和值。

使用自定义的基于条件的警报触发和消息传递可帮助确定特定的网络活动，并有效地更新您的安全、IT 和运营团队。


## <a name="high-availability"></a>高可用性

通过在本地管理控制台中安装高可用性设备，提高 IoT 用于 IoT 部署的复原能力。 高可用性部署确保托管传感器持续向活动的本地管理控制台报告。

此部署是使用本地管理控制台对（包含主要和辅助设备）实现的。

## <a name="localization"></a>本地化

许多控制台功能都支持多种语言。

## <a name="next-step"></a>后续步骤

[用于 IoT 的 Defender 入门](getting-started.md)
