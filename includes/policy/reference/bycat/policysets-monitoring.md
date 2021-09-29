---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c36bec44cf65d1269806f9d43950f0755c31132c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618276"
---
|名称 |说明 |策略 |版本 |
|---|---|---|---|
|[\[预览\]：在虚拟机上配置 Azure Defender for SQL 代理](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_AzureDefenderForSql.json) |将虚拟机配置为自动安装 Azure Defender for SQL 代理，其中已安装 Azure Monitor 代理。 安全中心从代理收集事件，并使用这些事件来提供安全警报和定制的强化任务（建议）。 在计算机所在的同一区域中创建一个资源组和 Log Analytics 工作区。 此策略仅适用于几个区域中的 VM。 |2 |1.0.0-preview |
|[将 Azure Monitor 代理配置到 Linux 虚拟机并关联到数据收集规则](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AMCS_LinuxPlatform_EnableDCR.json) |如果虚拟机映像 (OS) 和位置在定义的列表中且未安装代理，则部署适用于 Linux 虚拟机的 Azure Monitor 代理。  然后，部署关联以将虚拟机链接到指定的数据收集规则。 OS 映像列表会随着支持的扩大而不断更新。 |2 |1.0.0 |
|[将 Azure Monitor 代理配置到 Windows 虚拟机并关联到数据收集规则](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AMCS_WindowsPlatform_EnableDCR.json) |如果虚拟机映像 (OS) 和位置在定义的列表中且未安装代理，则部署适用于 Windows 虚拟机的 Azure Monitor 代理。  然后，部署关联以将虚拟机链接到指定的数据收集规则。 OS 映像列表会随着支持的扩大而不断更新。 |2 |1.0.0 |
|[\[预览\]：配置计算机，使之在虚拟机上自动安装 Azure Monitor 和 Azure 安全代理](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |配置计算机，以自动安装 Azure Monitor 和 Azure 安全代理。 安全中心从代理收集事件，并使用这些事件来提供安全警报和定制的强化任务（建议）。 在计算机所在的同一区域中创建资源组和 Log Analytics 工作区，以存储审核记录。 此策略仅适用于几个区域中的 VM。 |6 |2.0.0-preview |
|[启用用于虚拟机规模集的 Azure Monitor](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |在指定范围（管理组、订阅或资源组）内启用用于虚拟机规模集的 Azure Monitor。 将 Log Analytics 工作区用作参数。 注意：如果规模集 upgradePolicy 设置为“Manual”，则需要通过对规模集调用升级将扩展应用到集中的所有 VM。 在 CLI 中，这将是 az vmss update-instances。 |6 |1.0.1 |
|[启用用于 VM 的 Azure Monitor](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |在指定范围（管理组、订阅或资源组）内为虚拟机 (VM) 启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 |10 |2.0.0 |
