---
title: Azure Pipelines 任务 Azure Database for PostgreSQL 灵活服务器
description: 启用 Azure Database for PostgreSQL 灵活服务器 CLI 任务以与 Azure Pipelines 配合使用
ms.topic: quickstart
ms.custom: seodec18, devx-track-azurecli
ms.author: sumuth
author: mksuni
ms.service: postgresql
ms.date: 08/09/2021
ms.openlocfilehash: 747a1c59d00ac4127fab4b6c28d02c837e540133
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515998"
---
# <a name="azure-pipelines-task-for-azure-database-for-postgresql-flexible-server"></a>适用于 Azure Database for PostgreSQL 灵活服务器的 Azure Pipelines 任务

每次使用 Azure Pipelines 成功生成后，便可以将数据库更新自动部署到 Azure Database for PostgreSQL 灵活服务器。  可以使用 Azure CLI 任务，并针对数据库使用 SQL 文件或内联 SQL 脚本来更新数据库。 任务可在跨平台代理上运行，而这些代理在 Linux、macOS 或 Windows 操作系统上运行。

## <a name="prerequisites"></a>必备条件

- 一个 Azure 帐户。 如果没有帐户，可[获取一个免费试用帐户](https://azure.microsoft.com/free/)。
- [Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)，与你的 Azure 帐户连接
- 已预安装 Azure CLI 的 Microsoft 托管代理。 但是，如果使用的是专用代理，请在运行生成和发布代理的计算机上[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果代理已经在安装了 Azure CLI 的计算机上运行，请重启代理，确保更新所有相关的阶段变量。
- 使用 [Azure 门户](./quickstart-create-server-portal.md)或 [Azure CLI](./quickstart-create-server-cli.md) 创建 Azure Database for PostgreSQL 灵活服务器


## <a name="use-sql-file"></a>使用 SQL 文件

以下示例演示如何传递数据库参数和运行 ```execute``` 命令  

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
    inlineScript: |
      az login --allow-no-subscription
      az postgres flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) --file-path /code/sql/db-schema-update.sql
```

## <a name="use-inline-sql-script"></a>使用内联 SQL 脚本

以下示例演示如何使用 ```execute``` 命令运行内联 SQL 脚本。

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
      -INLINESCRIPT 
    inlineScript: |
      az login --allow-no-subscription 
      az postgres flexible-server execute --name $(SERVERNAME) --admin-user $(DBUSER) \
      --admin-password '$(DBPASSWORD)'  --database-name $(DBNAME) \
      --query-text "UPDATE items SET items.retail = items.retail * 0.9 WHERE items.id =100;" 
```

## <a name="task-inputs"></a>任务输入

将 Azure CLI 任务与 Azure Pipelines 配合使用时，可以看到所有任务输入的完整列表。 

| 参数            | 说明         | 
| :------------------- | :-------------------|
| azureSubscription| （必需）为部署提供 Azure 资源管理器订阅。 此参数仅在所选任务版本为 0.* 时显示，因为 Azure CLI v1.0 仅支持 Azure 资源管理器订阅。 |
|scriptType| （必需）提供脚本类型。 支持的脚本包括 PowerShell、PowerShell Core、Bat、Shell 和脚本。 在 Linux 代理上运行时，请选择下面的其中一种：```bash``` 或 ```pscore```。 在 Windows 代理上运行时，请选择下面的其中一种：```batch```、```ps``` 或 ```pscore```。 |
|sriptLocation| （必需）提供脚本的路径（例如实际文件路径），或者在提供内联脚本时使用 ```Inline script```。 默认值是 ```scriptPath```。 |
|scriptPath| （必需）脚本的完全限定的路径（使用基于 Windows 的代理时的 .ps1、.bat 或 .cmd，或者使用基于 linux 的代理时的 <code>.ps1 </code> 或 <code>.sh </code>）或相对于默认工作目录的路径。 |
|inlineScript|（必需）可在此处编写内联脚本。 使用 Windows 代理时，请使用 PowerShell、PowerShell Core 或批处理脚本，而使用基于 Linux 的代理时，请使用 PowerShell Core 或 shell 脚本。 对于批处理文件，请在每个 Azure 命令前使用前缀 \"call\"。 还可使用参数将预定义变量和自定义变量传递给此脚本。 <br/>PowerShell/PowerShellCore/shell 的示例：``` az --version az account show``` <br/>批处理示例：``` call az --version call az account show```。 |
| 参数| （可选）提供传递给脚本的所有参数。 例如 ```-SERVERNAME mydemoserver```。 |
|powerShellErrorActionPreference| （可选）在 PowerShell/PowerShell Core 脚本顶部预置行 <b>$ErrorActionPreference = 'VALUE'</b>。 默认值设置为 stop。 支持的值为 stop、continue 和 silentlyContinue。 |
|addSpnToEnvironment|（可选）将选择的服务主体 ID 和 Azure 终结点的密钥添加到脚本的执行环境。 可以在脚本中使用以下变量：<b>$env:servicePrincipalId, $env:servicePrincipalKey and $env:tenantId</b>。 只有在 Azure 终结点具有服务主体身份验证方案时，才遵守这一点。 默认值为 false。|
|useGlobalConfig|（可选）如果这为 false，则此任务将使用自己的独立 <a href= "/cli/azure/azure-cli-configuration?preserve-view=true&view=azure-cli-latest#cli-configuration-file">Azure CLI 配置目录</a>。 这可用于在<b>并行</b>发布中运行 Azure CLI 任务 <br/>默认值：false</td>
|workingDirectory| （可选）用于运行脚本的当前工作目录。  空的是存储库的根（生成）或项目（发布），即 $(System.DefaultWorkingDirectory)。 |
|failOnStandardError|（可选）如果这为 true，则当任何错误写入 StandardError 流时，此任务都将失败。 取消选中复选框以忽略标准错误，并依赖退出代码来确定状态。 默认值为 false。|
|powerShellIgnoreLASTEXITCODE| （可选）如果这为 false，则会将 <code>if ((Test-Path -LiteralPath variable:\\LASTEXITCODE)) { exit $LASTEXITCODE }</code> 行追加到脚本的末尾。 这会使外部命令中的最后一个退出代码作为 PowerShell 的退出代码来传播。 否则，该行不会追加到脚本末尾。 默认值为 false。 |

CLI 任务遇到问题，请参阅[如何排查生成和发布问题](/azure/devops/pipelines/troubleshooting/troubleshooting?view=azure-devops)。

## <a name="next-steps"></a>后续步骤 
下面是一些可用于通过 Azure 管道进行部署的相关任务。

- [Azure 资源组部署](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops)
- [Azure Web 应用部署](/azure/devops/pipelines/tasks/deploy/azure-rm-web-app-deployment?view=azure-devops)

