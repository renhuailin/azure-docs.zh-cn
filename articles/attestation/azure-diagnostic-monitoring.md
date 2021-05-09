---
title: 用于 Azure 证明的 Azure 诊断监视
description: 用于 Azure 证明的 Azure 诊断监视
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 851557c4cdc0f913247d40f2aaea3230a2e8551f
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108204722"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>为 Azure 证明的受信任平台模块 (TPM) 终结点设置诊断

本文帮助你创建和配置诊断设置，以便将平台指标和平台日志发送到不同的目标。 Azure 中的[平台日志](../azure-monitor/essentials/platform-logs-overview.md)（包括 Azure 活动日志和资源日志）提供针对 Azure 资源及其所依赖的 Azure 平台的详细诊断和审核信息。 默认情况下会收集[平台指标](../azure-monitor/essentials/data-platform-metrics.md)，并将其存储在 Azure Monitor 指标数据库中。

在开始之前，请确保已[使用 Azure PowerShell 设置 Azure 证明](quickstart-powershell.md)。

受信任的平台模块 (TPM) 终结点服务将在诊断设置中启用并可用于监视活动。 使用以下代码为 TPM 服务终结点设置 [Azure 监视](../azure-monitor/overview.md)。

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

活动日志位于存储帐户的“容器”部分。 有关详细信息，请参阅[从 Azure 资源收集资源日志并对其进行分析](../azure-monitor/essentials/tutorial-resource-logs.md)。