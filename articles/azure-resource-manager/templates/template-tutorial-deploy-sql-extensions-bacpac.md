---
title: 使用模板导入 SQL BACPAC 文件
description: 了解如何使用 Azure SQL 数据库扩展，以通过 Azure 资源管理器模板（ARM 模板）导入 SQL BACPAC 文件。
author: mumian
ms.date: 09/30/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3310d3f33d7aa31a24a19bc7c43b59173ffd78d6
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456037"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>教程：使用 ARM 模板导入 SQL BACPAC 文件

了解如何使用 Azure SQL 数据库扩展，以通过 Azure 资源管理器模板（ARM 模板）导入 [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 文件。 部署项目包括主模板文件以及完成部署所需的任何文件。 BACPAC 文件是一个项目。

在本教程中，你将创建一个模板来部署[逻辑 SQL Server](../../azure-sql/database/logical-servers.md)、单个数据库并导入 BACPAC 文件。 要了解如何使用 ARM 模板来部署 Azure 虚拟机扩展，请参阅[教程：使用 ARM 模板部署虚拟机扩展](./template-tutorial-deploy-vm-extensions.md)。

本教程涵盖以下任务：

> [!div class="checklist"]
>
> - 准备 BACPAC 文件。
> - 打开快速入门模板。
> - 编辑模板。
> - 部署模板。
> - 验证部署。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

- 包含资源管理器工具扩展的 Visual Studio Code。 请参阅[快速入门：使用 Visual Studio Code 创建 ARM 模板](./quickstart-create-templates-use-visual-studio-code.md)。
- 若要增强安全性，请使用为服务器管理员帐户生成的密码。 可以使用 [Azure Cloud Shell](../../cloud-shell/overview.md) 在 PowerShell 或 Bash 中运行以下命令：

    ```shell
    openssl rand -base64 32
    ```

    若要了解详细信息，请运行 `man openssl rand` 打开手册页。

    Azure Key Vault 旨在保护加密密钥和其他机密。 有关详细信息，请参阅[教程：在 ARM 模板部署中集成 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我们还建议你每三个月更新一次密码。

## <a name="prepare-a-bacpac-file"></a>准备 BACPAC 文件

BACPAC 文件在 [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) 中共享。 要创建自己的文件，请参阅[将 Azure SQL 数据库中的数据库导出到 BACPAC 文件](../../azure-sql/database/database-export.md)。 如果选择将文件发布到你自己的位置，则必须在教程的后面部分更新模板。

必须先将 BACPAC 文件存储在 Azure 存储帐户中，然后才能使用 ARM 模板导入该文件。 下面的 PowerShell 脚本通过以下步骤准备 BACPAC 文件：

- 下载 BACPAC 文件。
- 创建 Azure 存储帐户。
- 创建存储帐户 Blob 容器。
- 将 BACPAC 文件上传到该容器。
- 显示存储帐户密钥、Blob URL、资源组名称和位置。

1. 选择“试用”打开 Cloud Shell。 然后将以下 PowerShell 脚本复制并粘贴到 Shell 窗口中。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "The project name and location are $projectName and $location"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. 保存存储帐户密钥、BACPAC 文件 URL、项目名称和位置。 稍后在本教程中部署模板时，将要使用这些值。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

本教程中使用的模板存储在 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json) 中。

1. 在 Visual Studio Code 中，选择“文件” > “打开文件”。 
1. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. 选择“打开”以打开该文件。

    模板中定义了两个资源：

   - `Microsoft.Sql/servers`. 请参阅[模板参考](/azure/templates/microsoft.sql/servers)。
   - `Microsoft.SQL.servers/databases`. 请参阅[模板参考](/azure/templates/microsoft.sql/servers/databases)。

     在自定义模板之前，不妨对其进行一些基本的了解。

1. 选择“文件” > “另存为”，将该文件的副本保存到名为 _azuredeploy.json_ 的本地计算机。 

## <a name="edit-the-template"></a>编辑模板

1. 在 `parameters` 部分的末尾添加两个参数，以设置存储帐户密钥和 BACPAC URL。

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    在 `adminPassword` 属性的右大括号 (`}`) 后面添加一个逗号。 若要从 Visual Studio Code 设置 JSON 文件的格式，请选择 Shift+Alt+F。

1. 将两个资源添加到模板中。

    - 若要允许 SQL 数据库扩展导入 BACPAC 文件，必须允许来自 Azure 服务的流量。 部署 SQL 服务器后，防火墙规则将启用“允许 Azure 服务和资源访问此服务器”的设置。

      在服务器定义下添加以下防火墙规则：

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2021-02-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        以下示例显示更新的模板：

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png" alt-text="包含防火墙定义的模板。":::

    - 使用以下 JSON 将 SQL 数据库扩展资源添加到数据库定义：

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        以下示例显示更新的模板：

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png" alt-text="包含 SQL 数据库扩展的模板。":::

        若要了解资源定义，请参阅 API 版本的 [SQL 数据库扩展参考](/azure/templates/microsoft.sql/servers/databases/extensions)。 下面是一些重要元素：

        - `dependsOn`：必须在创建数据库以后才能创建扩展资源。
        - `storageKeyType`：指定要使用的存储密钥的类型。 值可以是 `StorageAccessKey` 或 `SharedAccessKey`。 在本教程中使用 `StorageAccessKey`。
        - `storageKey`：指定存储 BACPAC 文件的存储帐户的密钥。 如果存储密钥类型为 `SharedAccessKey`，则它前面必须是“?”。
        - `storageUri`：指定存储帐户中存储的 BACPAC 文件的 URL。
        - `administratorLogin`：SQL 管理员的帐户名称。
        - `administratorLoginPassword`：SQL 管理员的密码。 若要使用生成的密码，请参阅[先决条件](#prerequisites)。

以下示例显示已完成的模板：

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>部署模板

使用准备 BACPAC 文件时所用的项目名称和位置。 所有资源将放到同一个资源组，这也便于你删除资源。

1. 登录到 [Cloud Shell](https://shell.azure.com)。
1. 在左上角选择“PowerShell”。

    :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/cloud-shell-select.png" alt-text="在 PowerShell 中打开 Azure Cloud Shell 并上传文件。":::

1. 选择“上传/下载文件”并上传 azuredeploy.json 文件。
1. 若要部署模板，请将以下脚本复制并粘贴到 Shell 窗口中。

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the SQL admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $storageAccountKey = Read-Host -Prompt "Enter the storage account key"
    $bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUser $adminUsername `
        -adminPassword $adminPassword `
        -TemplateFile "$HOME/azuredeploy.json" `
        -storageAccountKey $storageAccountKey `
        -bacpacUrl $bacpacUrl

    Write-Host "Press [ENTER] to continue ..."
    ```

## <a name="verify-the-deployment"></a>验证部署

若要从客户端计算机访问服务器，需要添加防火墙规则。 由于网络地址转换 (NAT) 的原因，客户端的 IP 地址以及用于连接到服务器的 IP 地址可能不同。 有关详细信息，请参阅[创建和管理 IP 防火墙规则](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)。

例如，登录到查询编辑器时，会显示一条消息，指出不允许所用的 IP 地址。 由于执行了 NAT，该地址不同于客户端的 IP 地址。 请选择该消息中的链接，为 IP 地址添加防火墙规则。 完成后，可以从服务器的“防火墙和虚拟网络”设置中删除 IP 地址。

在 Azure 门户上的资源组中选择数据库。 选择“查询编辑器(预览版)”，然后输入管理员凭据。 此时你会看到两个表已导入到该数据库中。

![查询编辑器（预览版）](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>清理资源

不再需要部署的 Azure 资源时，请删除资源组。 将会删除该资源组、存储帐户、SQL 服务器和 SQL 数据库。

1. 在 Azure 门户中的搜索框内输入“资源组”。
1. 在“按名称筛选”字段中输入资源组名称。
1. 选择资源组名称。
1. 选择“删除资源组”。
1. 若要确认删除，请输入资源组名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，你部署了服务器和数据库，并导入了 BACPAC 文件。 若要了解如何排查模板部署问题，请参阅：

> [!div class="nextstepaction"]
> [排查 ARM 模板部署问题](./template-tutorial-troubleshoot.md)
