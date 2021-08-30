---
title: 从旧代理迁移到新的 Azure Monitor 代理
description: 有关从现有的旧代理迁移到新的 Azure Monitor 代理 (AMA) 和数据收集规则 (DCR) 的指南。
ms.topic: conceptual
author: shseth
ms.author: shseth
ms.date: 7/12/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 01c2f7b49b051e9166ad1ceef99fc816e611f579
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271969"
---
# <a name="migrate-from-log-analytics-agents"></a>从 Log Analytics 代理进行迁移
本文简要介绍何时以及如何迁移到新的 Azure Monitor 代理 (AMA) 和数据收集规则 (DCR)。 当新的迁移工具可用时，将更新本文。


## <a name="review"></a>检查
- 为了帮助你决定是现在还是以后迁移到新的 Azure Monitor 代理，请参阅 [Azure Monitor 代理概述](./azure-monitor-agent-overview.md#should-i-switch-to-the-azure-monitor-agent)中的指南。
- 对于 Azure Monitor 代理，查看新功能和现有功能、服务和解决方案的可用性，以及 [Azure Monitor 代理概述](./agents-overview.md#azure-monitor-agent)中的当前限制。


## <a name="test-migration-by-using-the-azure-portal"></a>使用 Azure 门户测试迁移
1. 为确保迁移期间进行安全部署，请在运行现有 Log Analytics 代理的非生产环境中使用一些资源开始测试。 在可以对在这些测试资源上收集的数据进行验证之后，请按照相同的步骤部署到生产环境中。
1. 转到“监视” > “设置” > “数据收集规则”并[创建新的数据收集规则](./data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal)，以开始收集一些现有的数据规则  。 当使用门户 GUI 时，它会对所有目标资源执行以下步骤：
    - 启用系统分配的托管标识
    - 安装 Azure Monitor 代理扩展
    - 创建和部署数据收集规则关联
1. 验证数据是否通过 Azure Monitor 代理按预期流动。 查看检测信号表，获取新的代理版本值。 确保它与流经现有 Log Analytics 代理的数据匹配。


## <a name="at-scale-migration-by-using-policies"></a>使用策略进行大规模迁移
1. 首先使用以下条件分析当前的 MMA/OMS 监视设置：
    - 源，例如虚拟机、虚拟机规模集和本地服务器
    - 数据源，例如性能计数器、Windows 事件日志和 Syslog
    - 目标，例如 Log Analytics 工作区
1. 使用上述配置[创建新的数据收集规则](/rest/api/monitor/datacollectionrules/create#examples)。 作为最佳做法，建议为 Windows 和 Linux 源制定单独的数据收集规则。 或者建议为具有不同数据收集需求的各个团队制定单独的数据收集规则。
1. 在目标资源上[启用系统分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#system-assigned-managed-identity)。
1. 安装 Azure Monitor 代理扩展。 使用[内置策略计划](../deploy-scale.md#built-in-policy-initiatives)在所有目标资源上部署数据收集规则关联。 提供前面的数据收集规则作为输入参数。 
1. 验证数据是否通过 Azure Monitor 代理按预期流动。 查看检测信号表，获取新的代理版本值。 确保它与流经现有 Log Analytics 代理的数据匹配。
1. 验证仪表板、警报和 runbook 辅助角色等所有下游依赖项。 通过使用来自新代理的数据，所有工作簿现在都可以继续工作。
1. 从资源中[卸载 Log Analytics 代理](./agent-manage.md#uninstall-agent)。 如果需要将该代理用于 System Center Operations Manager 方案或 Azure Monitor 代理上尚不可用的其他解决方案，请不要卸载它。
1. 清理 Log Analytics 代理以前所使用的任何配置文件、工作区密钥或证书。


