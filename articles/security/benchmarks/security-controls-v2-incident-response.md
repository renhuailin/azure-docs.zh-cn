---
title: Azure 安全基准 V2 - 事件响应
description: Azure 安全基准 V2 事件响应
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 002/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 40a88349040482b3325e63d7035dea35df444cdb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698580"
---
# <a name="security-control-v2-incident-response"></a>安全控制 V2：事件响应

事件响应涵盖事件响应生命周期中的控制 - 准备、检测和分析、遏制以及事件后活动。 这包括使用 azure 服务（如 Azure 安全中心和 Sentinel）自动执行事件响应过程。

若要查看适用的内置 Azure 策略，请参阅 [Azure 安全基准管理法规符合性内置计划的详细信息：事件响应](../../governance/policy/samples/azure-security-benchmark#incident-response)

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-1 | 19 | IR-4、IR-8 |

确保组织具有响应安全事件的流程，已为 Azure 更新了这些流程，并定期执行这些流程以确保准备就绪。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-2 | 19.5 | IR-4、IR-5、IR-6、IR-8 |

在 Azure 安全中心中设置安全事件联系信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-3 | 19.6 | IR-4、IR-5 |

确保你具有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于从过去的事件中吸取的经验、经过验证的社区来源以及各种工具来生成高质量警报，这些工具旨在通过融合和关联各种信号源来生成和清除警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

确保分析人员在调查潜在事件时可以查询和使用各种数据源，以全面了解发生的情况。 应收集各种各样的日志，以跟踪整个终止链中潜在攻击者的活动，避免出现盲点。 还应确保收集见解和经验，以供其他分析人员使用和用作将来的历史参考资料。

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据 - 使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 使用操作系统的本机内存转储功能来创建正在运行的系统内存的快照。

    - 使用 Azure 服务的快照功能或软件自带的功能来创建正在运行的系统的快照。

Azure Sentinel 提供几乎针对任何日志源的广泛数据分析，并提供一个事例管理门户来管理事件的整个生命周期。 调查过程中的情报信息可与事件相关联，以便进行跟踪和报告。 

- [Windows 计算机的磁盘快照](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 调查事件](../../sentinel/tutorial-investigate-cases.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-5 | 19.8 | CA-2、IR-4 |

根据警报严重性和资产敏感性，为分析人员提供优先处理的事件的上下文。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的可信度，以及对导致警报的活动背后存在恶意意图的可信度级别。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](../../azure-resource-manager/management/tag-resources.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-6 | 19 | IR-4、IR-5、IR-6 |

自动执行手动重复性任务，以加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../../sentinel/tutorial-respond-threats-playbook.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)