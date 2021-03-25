---
title: Azure NetApp 文件 SDK 和 CLI 工具 | Microsoft Docs
description: 了解有关 Azure NetApp 文件的受支持的 SDK 及其在 GitHub 中的发布位置，以及受支持的命令行工具：Azure CLI 和 PowerShell。
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
ms.date: 10/21/2020
ms.author: b-juche
ms.openlocfilehash: 705b74fe1fe38e8f65fe24e88c5ffa9bf1ad6175
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507783"
---
# <a name="azure-netapp-files-sdks-cli-tools-and-arm-templates"></a>Azure NetApp Files SDK、CLI 工具和 ARM 模板

本文列出了 Azure NetApp 文件支持的 SDK、命令行 (CLI) 工具和 Azure 资源管理器 (ARM) 模板。

## <a name="supported-sdks"></a>支持的 SDK 

下表列出了受支持的 SDK。  你可以在 GitHub 的发布位置找到有关受支持的 SDK 的详细信息。  

|    语言    |    GitHub 中的 SDK 发布位置    |
|------------------|--------------------------------------------------------------|
|    .NET  |    [Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/netapp)    |
|    Python  |  [Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/netapp)    |
|    Go    |    [Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/netapp)       |
|    Java |     [Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/netapp) |
|    JavaScript    |    [Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/netapp/arm-netapp)    |
|    Ruby   |    [Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_netapp)    |

## <a name="cli-tools"></a>CLI 工具

下表列出了受支持的 CLI 工具及其命令参考。   

|    工具    |    命令参考    |
|------------------|--------------------------------------------|
|    Azure CLI  |    [az netappfiles](/cli/azure/netappfiles)    |
|    PowerShell   |    [Azure NetApp 文件的 Azure PowerShell](/powershell/module/az.netappfiles/#netapp_files&preserve-view=true)    |

## <a name="code-samples"></a>代码示例

请参阅 [Azure NetApp 文件的代码示例](/samples/browse/?filter-products=netapp&products=azure-netapp-files)。

## <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板  

Azure 资源管理器 (ARM) 允许用户使用声明性模板预配应用程序。 在单个模板中，可以部署多个服务及其依赖项。 在应用程序生命周期的每个阶段，可使用相同模板重复部署应用程序。   

请参阅 [Azure NetApp 文件可用的 ARM 模板](https://azure.microsoft.com/resources/templates/?term=anf)。

## <a name="next-steps"></a>后续步骤   
 [下载 Azure SDK](https://azure.microsoft.com/downloads/)
