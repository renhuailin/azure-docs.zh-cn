---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 02118ff7bf6568ba928ecea3658db1aa23e5015a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504819"
---
|名称 |说明 |策略 |版本 |
|---|---|---|---|
|[\[预览版\]：部署 - 配置必备组件，以便在虚拟机上启用 Azure Monitor 和 Azure 安全代理](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |配置计算机，以自动安装 Azure Monitor 和 Azure 安全代理。 安全中心从代理收集事件，并使用这些事件来提供安全警报和定制的强化任务（建议）。 在计算机所在的同一区域中创建资源组和 Log Analytics 工作区，以存储审核记录。 此策略仅适用于几个区域中的 VM。 |5 |1.0.0-preview |
|[启用用于虚拟机规模集的 Azure Monitor](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |在指定范围（管理组、订阅或资源组）内启用用于虚拟机规模集的 Azure Monitor。 将 Log Analytics 工作区用作参数。 注意：如果规模集 upgradePolicy 设置为“Manual”，则需要通过对规模集调用升级将扩展应用到集中的所有 VM。 在 CLI 中，这将是 az vmss update-instances。 |6 |1.0.1 |
|[启用用于 VM 的 Azure Monitor](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |在指定范围（管理组、订阅或资源组）内为虚拟机 (VM) 启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 |10 |2.0.0 |
