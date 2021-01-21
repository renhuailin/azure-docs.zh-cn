---
title: Azure Defender for IoT 体系结构
description: 了解 Azure Defender for IoT 体系结构和信息流。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/13/2021
ms.author: shhazam
ms.openlocfilehash: 66b960bf874cc46985230e488c749663eff0b835
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621091"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender for IoT 体系结构

本文介绍了 Defender for IoT 解决方案的功能系统体系结构。 Azure Defender for IoT 提供了两组功能，可满足你的环境需求、组织的无代理解决方案以及设备构建者的基于代理的解决方案。

## <a name="agentless-solution-for-organizations"></a>组织的无代理解决方案
### <a name="defender-for-iot-components"></a>Defender for IoT 组件

Defender for IoT 同时连接到 Azure 云和本地组件。 该解决方案旨在实现具有多个远程位置的大型地理分布式环境中的可伸缩性。 此解决方案支持按国家、地区、业务部门或区域划分的多层分布式体系结构。 

Azure Defender for IoT 包括以下组件： 

云连接的部署

- Azure Defender for IoT 传感器 VM 或设备
- Azure 门户，用于云管理和与 Azure Sentinel 的集成
- 本地管理控制台，用于本地站点管理
- 嵌入式安全代理（可选）

气隙（脱机）部署

- Azure Defender for IoT 传感器 VM 或设备
- 本地管理控制台，用于本地站点管理

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="用于 IoT 的的体系结构。":::

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender for IoT 传感器

用于 IoT 传感器的 Defender 发现并持续监视网络设备。 传感器使用 IoT 和 OT 设备上的被动（无代理）监视来收集 ICS 网络流量。 
 
无代理技术专为 IoT 和 OT 网络而构建，在连接到该网络的几分钟内就能深入了解 IoT 和 OT 风险。 由于其非侵入式网络流量分析 (NTA) 方法，它对网络和网络设备的性能没有影响。 
 
利用获得专利的 IoT 和 OT 感知行为分析和第 7 层深度数据包检测 (DPI)，它允许你超越传统的基于签名的解决方案进行分析，以基于异常或未经授权的活动立即检测高级 IoT 和 OT 威胁（例如无文件恶意软件）。 
  
用于 IoT 传感器的 Defender 连接到 SPAN 端口或网络分流点，并立即开始执行 IoT 和网络流量的 DPI。 
 
数据收集、处理、分析和报警直接在传感器上进行。 这使得它非常适合用于带宽低或连接延迟高的位置，因为只有元数据会被传输到管理控制台。

该传感器包括五个分析检测引擎。 这些引擎根据实时和预先录制的流量分析触发警报。 提供以下引擎： 

#### <a name="protocol-violation-detection-engine"></a>协议冲突检测引擎
协议冲突检测引擎识别违反 ICS 协议规范的数据包结构和字段值的使用，例如：Modbus 异常和过时函数代码警报的启动。

#### <a name="policy-violation-detection-engine"></a>策略冲突检测引擎
通过机器学习，策略冲突检测引擎会提醒用户任何偏离基线的行为，如对特定函数代码的未经授权使用、对特定对象的访问或对设备配置的更改。 例如：DeltaV 软件版本更改，和未经授权的 PLC 编程警报。 具体而言，策略冲突引擎使用一种称为工业有限状态建模 (IFSM) 的专利技术，将 ICS 网络建模为确定性的状态和转换序列。 策略冲突检测引擎建立了 ICS 网络的基准，因此该平台需要比常规数学方法或分析更短的学习周期来构建网络的基线，常规数学方法或分析最初是为 IT 而不是 OT 网络开发的。

#### <a name="industrial-malware-detection-engine"></a>工业恶意软件检测引擎
工业恶意软件检测引擎标识指示已知恶意软件（如 Conficker、黑色能量、Havex、WannaCry、NotPetya 和 Triton）是否存在的行为。 

#### <a name="anomaly-detection-engine"></a>异常情况检测引擎
异常检测引擎可检测异常的计算机到计算机 (M2M) 通信和行为。 通过将 ICS 网络建模为确定性的状态和转换序列，该平台比最初为 IT 而非 OT 开发的常规数学方法或分析需要更短的学习周期。 它还能更快地检测到异常，且误报率最低。 异常情况检测引擎警报包括过多的 SMB 登录尝试以及 PLC 扫描检测到的警报。

#### <a name="operational-incident-detection"></a>操作事件检测
操作事件检测可检测操作问题（如间歇性连接），这些问题可指示设备故障的早期迹象。 例如，设备被认为是断开连接 (无响应) ，而 Siemens S7 stop PLC 命令已发送警报。

### <a name="management-consoles"></a>管理控制台
跨混合环境管理 Azure Defender for IoT 是通过两个管理门户完成的： 
- 传感器控制台
- 本地管理控制台
- Azure 门户

### <a name="sensor-console"></a>传感器控制台
传感器检测显示在传感器控制台中，可以在网络图、设备清单和大量的报告中查看、调查和分析传感器，如风险评估报告、数据挖掘查询和攻击向量。 你还可以使用控制台来查看和处理传感器引擎检测到的威胁、将信息转发给合作伙伴系统、管理用户等。

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender for IoT 传感器控制台":::

### <a name="on-premises-management-console"></a>本地管理控制台
本地管理控制台使安全操作中心 (SOC) 操作员能够管理和分析从多个传感器聚合到一个仪表板的警报，并提供 OT 网络运行状况的总体视图。

此体系结构提供了一个位于 SOC 级别的全面统一的网络视图、优化的警报处理和对运营网络安全的控制，确保决策和风险管理保持完美无缺。

除了多租户、监视、数据分析和集中式传感器远程控制外，管理控制台还为每个远程设备提供了额外的系统维护工具（如警报排除）和完全自定义的报告功能。 这个可缩放的体系结构既支持站点级别、区域级别的本地管理，也支持 SOC 内的全局管理。

可部署管理控制台以进行高可用性配置，高可用性配置提供一个备份控制台，可定期接收执行恢复所需的所有配置文件的备份。 如果主控制台发生故障，本地站点管理设备将自动进行故障转移以与备份控制台同步，以保持可用性，而不会中断。

它紧密集成了 SOC 工作流和 runbook，可轻松确定缓解活动的优先级和整个站点的威胁风险。

- 整体-通过用于设备管理、风险和漏洞管理的单个统一平台以及与事件响应的威胁监视，降低复杂性。

- 聚合和关联–显示、聚合和分析从所有站点收集的数据和警报。

- 控制所有传感器 - 在单个位置配置和监视所有传感器。

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="管理所有警报和信息。":::

### <a name="azure-portal"></a>Azure 门户

Azure 中的 IoT 门户 Defender 用于帮助你：

- 购买解决方案设备

- 安装和更新软件
- 将传感器载入 Azure
- 更新威胁智能包

## <a name="agent-based-solution-for-device-builders"></a>适用于设备构建者的基于代理的解决方案

### <a name="embedded-security-agent-built-in-mode"></a>嵌入式安全代理：内置模式

在 **内置** 模式下，在你选择启用 iot 中心中的 " **安全** " 选项时，会启用 "用于 iot 的 Defender"。 内置模式提供实时监视、建议和警报，提供单步设备可见性和无与伦比的安全性。 内置模式不要求在任何设备上安装代理，并对记录的活动使用高级分析，以分析和保护现场设备和 IoT 中心。

### <a name="embedded-security-agent-enhanced-mode"></a>嵌入式安全代理：增强模式

在 **增强** 模式下，在 IoT 中心启用 **安全** 选项并在设备上安装用于 IoT 设备代理的 Defender 后，代理会从设备收集、聚合和分析原始安全事件。 原始安全事件可能包括 IP 连接、进程创建、用户登录和其他安全相关的信息。 用于 IoT 设备代理的 Defender 还处理事件聚合，以帮助避免高网络吞吐量。 这些代理具有高度可自定义性，使你可以将它们用于特定的任务（例如以最快的 SLA 仅发送重要信息），或者用于将大量安全信息和上下文聚合到更大的段中，从而避免更高的服务成本。

设备代理和其他应用程序使用 **azure 发送安全消息 SDK** 将安全信息发送到 azure IoT 中心。 IoT 中心获取此信息，并将其转发到 IoT 服务的 Defender。

一旦启用了用于 IoT 服务的 Defender，IoT 中心还将发送其所有内部数据，供 Defender 用于分析。 此数据包括设备云操作日志、设备标识和中心配置。 所有这些信息都有助于创建用于 IoT 分析管道的 Defender。

用于 IoT 分析的 Defender 还可以从 Microsoft 和 Microsoft 合作伙伴的各种来源接收其他威胁情报流。 适用于 IoT 整个分析管道的 Defender 适用于在服务 (上进行的每个客户配置，例如自定义警报和发送安全消息 SDK) 的使用。

使用分析管道，将所有信息流结合起来，以生成可操作的建议和警报。 该管道包含由安全研究人员和专家创建的自定义规则，以及用于搜索标准设备行为偏差和风险分析的机器学习模型。

用于 IoT 的 Defender 建议和警报 (分析管道输出) 写入每个客户的 Log Analytics 工作区。 如果将工作区中的原始事件以及警报和建议包括在内，则可以使用检测到的可疑活动的确切详细信息进行深入调查和查询。

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="微代理体系结构。":::

## <a name="see-also"></a>另请参阅

[Defender for IoT 常见问题](resources-frequently-asked-questions.md)

[系统先决条件](quickstart-system-prerequisites.md)
