---
title: 适用于 Azure 政府的 Azure NetApp 文件  | Microsoft Docs
description: 介绍如何连接到 Azure 政府以在 Azure 政府中使用 Azure NetApp 文件和 Azure NetApp 文件功能可用性。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: b-juche
ms.openlocfilehash: 4dbdc8086b59553474d05297cdaf030d897d441e
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234065"
---
# <a name="azure-netapp-files-for-azure-government"></a>适用于 Azure 政府的 Azure NetApp 文件 

[Microsoft Azure 政府](../azure-government/documentation-government-welcome.md)提供一项专用云，使政府机构及其合作伙伴能够将任务关键型工作负荷转换为云。  

本文介绍 Azure 政府中的 Azure NetApp 文件功能可用性。 本文还介绍如何在 Azure 政府版中访问 Azure NetApp 文件服务。

## <a name="feature-availability"></a>功能可用性

有关 Azure NetApp 文件支持的 Azure 政府区域，请参阅[“可用产品（按区域）”页](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)。  

所有 [Azure NetApp 文件功能](whats-new.md)在 Azure 公共云上均有提供，也在支持的 Azure 政府区域提供，但下表中列出的功能除外： 

| Azure NetApp 文件功能 | Azure 公有云可用性 |  Azure 政府可用性 |
|:--- |:--- |:--- |
| Azure NetApp 文件跨区域复制 | 公共预览版 | [受限](cross-region-replication-introduction.md#supported-region-pairs) |
| Azure NetApp 文件备份 | 公共预览版 | 否 |

## <a name="portal-access"></a>门户访问权限

Azure 政府用户通过将其浏览器指向 portal.azure.us 即可访问 Azure NetApp 文件。  门户站点名称为 Microsoft Azure 政府。有关详细信息，请参阅[使用门户连接到 Azure 政府](../azure-government/documentation-government-get-started-connect-with-portal.md)。   

![Azure 政府门户的屏幕截图，突出显示 portal.azure.us 作为 URL](../media/azure-netapp-files/azure-government.jpg)

在 Microsoft Azure 政府门户中，你可以通过与在 Azure 门户中相同的方式访问 Azure NetApp 文件。例如，可以在门户的“搜索资源”框中输入“Azure NetApp 文件”，然后从出现的列表中选择“Azure NetApp 文件”。   

有关使用该服务的详细信息，请参阅 [Azure NetApp 文件](/azure/azure-netapp-files/)文档。

## <a name="azure-cli-access"></a>Azure CLI 访问权限

通过将云名称设置为 `AzureUSGovernment`，然后继续按通常使用 `az login` 命令登录，这样即可连接到 Azure 政府。 运行登录命令后，浏览器将启动，可在其中输入相应的 Azure 政府凭据。  

```azurecli 

az cloud set --name AzureUSGovernment 

``` 

若要确认云已设置为 `AzureUSGovernment`，请运行： 

```azurecli 

az cloud list --output table 

``` 

此命令将生成一个包含 Azure 云位置的表。 `AzureUSGovernment` 的 `isActive` 列条目应为 `true`。  

有关详细信息，请参阅[使用 Azure CLI 连接到 Azure 政府](../azure-government/documentation-government-get-started-connect-with-cli.md)。

## <a name="rest-api-access"></a>REST API 访问权限

Azure 政府版终结点不同于商业 Azure 终结点。 有关不同终结点的列表，请参阅 Azure 政府的[面向开发人员的指南](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)。

## <a name="powershell-access"></a>PowerShell 访问

通过 PowerShell 连接到 Azure 政府版时，必须指定环境参数以确保连接到正确的终结点。 在这里，你可以继续使用 Azure NetApp 文件，就像在 PowerShell 中那样。 

| 连接类型 | Command | 
| --- | --- | 
| [Azure](/powershell/module/az.accounts/Connect-AzAccount) 命令 |`Connect-AzAccount -EnvironmentName AzureUSGovernment` | 
| [Azure Active Directory](/powershell/module/azuread/connect-azuread) 命令 |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` | 
| [Azure（经典部署模型）](/powershell/module/servicemanagement/azure.service/add-azureaccount)命令 |`Add-AzureAccount -Environment AzureUSGovernment` | 
| [Azure Active Directory（经典部署模型）](/previous-versions/azure/jj151815(v=azure.100))命令 |`Connect-MsolService -AzureEnvironment UsGovernment` | 

有关详细信息，请参阅[使用 PowerShell 连接到 Azure 政府](../azure-government/documentation-government-get-started-connect-with-ps.md)。

## <a name="next-steps"></a>后续步骤
* [什么是 Azure 政府？](../azure-government/documentation-government-welcome.md)
* [Azure NetApp 文件中的新增功能](whats-new.md)
* [Azure 政府和全球 Azure](../azure-government/compare-azure-government-global-azure.md)
* [Azure NetApp 文件 REST API](azure-netapp-files-develop-with-rest-api.md)
* [使用 PowerShell 的 Azure NetApp 文件 REST API](develop-rest-api-powershell.md)
