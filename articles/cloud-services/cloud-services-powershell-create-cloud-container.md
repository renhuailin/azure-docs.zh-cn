---
title: 使用 PowerShell 创建云服务（经典）容器 | Microsoft Docs
description: 本文说明如何使用 PowerShell 创建云服务容器。 该容器承载 Web 角色和辅助角色。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b24e1ea84a907edc2960b6638def84806aaf18d1
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113092610"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>使用 Azure PowerShell 命令可创建一个空的云服务（经典）容器

> [!IMPORTANT]
> [Azure 云服务（外延支持）](../cloud-services-extended-support/overview.md)是 Azure 云服务产品基于 Azure 资源管理器的新型部署模型。 进行此更改后，在基于 Azure 服务管理器的部署模型上运行的 Azure 云服务已重命名为云服务（经典），所有新部署都应使用[云服务（外延支持）](../cloud-services-extended-support/overview.md)。

本文介绍如何使用 Azure PowerShell cmdlet 快速创建云服务容器。 请执行以下步骤：

1. 从 [Azure PowerShell 下载](https://aka.ms/webpi-azps)页安装 Microsoft Azure PowerShell cmdlet。
2. 打开 PowerShell 命令提示符。
3. 使用 [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) 登录。

   > [!NOTE]
   > 有关安装 Azure PowerShell cmdlet 和连接到 Azure 订阅的更多说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/)。
   >
   >
4. 使用 **New-AzureService** cmdlet 创建一个空的 Azure 云服务容器。

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. 请按照本示例操作以调用 cmdlet：

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

有关创建 Azure 云服务的详细信息，请运行：

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>后续步骤

* 若要管理云服务部署，请参阅 [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService)、[Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService) 和 [Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice) 命令。 有关更多信息，还可以参阅[如何配置云服务](cloud-services-how-to-configure-portal.md)。
* 若要将云服务项目发布到 Azure，请参见 [已存档的云服务存储库](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)中的 **PublishCloudService.ps1** 代码示例。