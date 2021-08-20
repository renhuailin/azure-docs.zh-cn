---
title: PowerShell：使用 FTP 上传文件
description: 了解如何使用 Azure PowerShell 自动部署和管理应用服务。 此示例演示如何使用 FTP 将文件上传到应用。
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.topic: sample
ms.date: 06/23/2021
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 83ec2fad8d4f3ec6efb1b88e1e765ebcfa0d260d
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575255"
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>使用 FTP 将文件上传到 Web 应用

本示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后使用 FTPS 将某个文件部署到 Web 应用（通过 [System.Net.FtpWebRequest](/dotnet/api/system.net.ftpwebrequest)）。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Upload files to a web app using FTP")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 创建 Web 应用。 |
| [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) | 获取 Web 应用的发布配置文件。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
