---
title: 快速入门：使用 Azure PowerShell 创建 Synapse 工作区
description: 按照本指南中的步骤使用 Azure PowerShell 创建 Azure Synapse 工作区。
services: synapse-analytics
author: rothja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/19/2020
ms.author: jroth
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee02f650ad20eb9f6673875823090ea3d242b7cd
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092996"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建 Azure Synapse 工作区

Azure PowerShell 是一组 cmdlet，用于直接从 PowerShell 管理 Azure 资源。 可以在浏览器中将它与 Azure Cloud Shell 配合使用。 也可以在 macOS、Linux 或 Windows 上安装它。

本快速入门介绍如何使用 Azure PowerShell 创建 Synapse 工作区。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

- [Azure Data Lake Storage Gen2 存储帐户](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Azure Synapse 工作区需要能够读取所选 ADLS Gen2 帐户以及向其写入内容。 对于作为主存储帐户链接的任何存储帐户，在创建存储帐户时必须启用“分层命名空间”，如[创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account)中所述。

如果你选择使用 Cloud Shell，请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)来了解详细信息。

### <a name="install-the-azure-powershell-module-locally"></a>在本地安装 Azure Powershell 模块

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

有关使用 Azure PowerShell 进行身份验证的详细信息，请参阅[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps)。

### <a name="install-the-azure-synapse-powershell-module"></a>安装 Azure Synapse PowerShell 模块

> [!IMPORTANT]
> 尽管 Az.Synapse PowerShell 模块为预览版，但你需要使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>使用 Azure PowerShell 创建 Azure Synapse 工作区

1. 定义创建 Azure Synapse 工作区资源所必需的环境变量。

   |        变量名称        |                                                 说明                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | 现有 ADLS Gen2 存储帐户的名称。                                                           |
   | StorageAccountResourceGroup | 现有 ADLS Gen2 存储帐户资源组的名称。                                             |
   | FileShareName               | 现有存储文件系统的名称。                                                                  |
   | SynapseResourceGroup        | 为 Azure Synapse 资源组选择新名称。                                                    |
   | 区域                      | 选择其中一个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/#overview)。 |
   | SynapseWorkspaceName        | 为新的 Azure Synapse 工作区选择唯一名称。                                                  |
   | SqlUser                     | 为新用户名选择一个值。                                                                          |
   | SqlPassword                 | 选择安全密码。                                                                                   |
   | ClientIP                    | 正在运行 PowerShell 的系统的公共 IP 地址。                                             |
   |                             |                                                                                                             |

1. 创建资源组作为 Azure Synapse 工作区的容器：

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. 创建 Azure Synapse 工作区：

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. 获取 Azure Synapse 工作区的 Web 和开发 URL：

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.dev
   ```

1. 创建防火墙规则以允许你从自己的计算机访问 Azure Synapse 工作区：

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. 打开环境变量 `WorkspaceWeb` 中存储的 Azure Synapse 工作区 Web URL 地址以访问工作区：

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse 工作区 Web](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>清理资源

执行以下步骤，删除 Azure Synapse 工作区。

> [!WARNING]
> 如果删除 Azure Synapse 工作区，将一并删除分析引擎，还将删除存储在包含的 SQL 池和工作区元数据所在的数据库中的数据。 删除后，该工作区将无法再连接到 SQL 或 Apache Spark 终结点。 将删除所有代码项目（查询、笔记本、作业定义和管道）。 删除工作区不会影响链接到工作区的 Data Lake Store Gen2 中的数据。

如果不需要本文中创建的 Azure Synapse 工作区，可以通过运行以下示例将其删除。

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>后续步骤

接下来，可以[创建 SQL 池](quickstart-create-sql-pool-studio.md)或[创建 Apache Spark 池](quickstart-create-apache-spark-pool-studio.md)，开始分析和探究你的数据。
