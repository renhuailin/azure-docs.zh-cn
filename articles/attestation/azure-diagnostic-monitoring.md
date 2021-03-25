---
title: Azure 诊断监视 - Azure 证明
description: 用于 Azure 证明的 Azure 诊断监视
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726472"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>为 Azure 证明的受信任的平台模块 (TPM) 终结点设置诊断

Azure 中的[平台日志](../azure-monitor/essentials/platform-logs-overview.md)（包括 Azure 活动日志和资源日志）提供 Azure 资源及其所依赖的 Azure 平台的详细诊断和审核信息。 默认情况下会收集[平台指标](../azure-monitor/essentials/data-platform-metrics.md)，它们通常存储在 Azure Monitor 指标数据库中。 本文详细介绍如何创建和配置诊断设置，以将平台指标和平台日志发送到不同的目标。 

TPM 终结点服务已启用诊断设置，可用于监视活动。 要使用 PowerShell 为 TPM 服务终结点设置 [Azure 监视](../azure-monitor/overview.md)，请执行以下步骤。 

设置 Azure 证明服务。 

[使用 Azure PowerShell 设置 Azure 证明](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
可在存储帐户的“容器”部分找到活动日志。 有关详细信息，请参阅[从 Azure 资源收集资源日志并使用 Azure Monitor 进行分析 - Azure Monitor](../azure-monitor/essentials/tutorial-resource-logs.md)
