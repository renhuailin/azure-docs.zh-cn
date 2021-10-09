---
title: 教程 - 部署链接模板
description: 了解如何部署链接模板
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9b9f8f594fbc85c69a546a82f2857802382eea28
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128556464"
---
# <a name="tutorial-deploy-a-linked-template"></a>教程：部署链接模板

[前一篇教程](./deployment-tutorial-local-template.md)已介绍如何部署存储在本地计算机中的模板。 若要部署复杂的解决方案，可将一个模板分解为多个模板，并通过主模板部署这些模板。 本教程介绍如何部署包含对链接模板的引用的主模板。 部署主模板时，会触发链接模板的部署。 本教程还将介绍如何使用 SAS 令牌存储和保护模板。 完成该过程需要大约 **12 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成前一篇教程，但这不是一项要求。

## <a name="review-template"></a>审阅模板

在前面的教程中，你部署了一个用于创建存储帐户、应用服务计划和 Web 应用的模板。 使用的模板是：

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>创建链接模板

可将存储帐户资源隔离到链接模板中：

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

以下模板是主模板。 突出显示的 `Microsoft.Resources/deployments` 对象演示如何调用链接模板。 无法将链接模板存储为本地文件，或存储为只能在本地网络中使用的文件。 可以提供包含 HTTP 或 HTTPS 的链接模板的 URI 值，也可以使用 relativePath 属性在相对于父模板的位置部署远程链接模板。 一种选择是将主模板和链接模板均放在存储帐户中。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>存储链接模板

主模板和链接模板均存储在 GitHub 中：

以下 PowerShell 脚本将创建存储帐户、创建容器，然后将这两个模板从 GitHub 存储库复制到该容器。 这两个模板是：

- 主模板： https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- 链接模板： https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

选择“尝试”打开 Cloud Shell，选择“复制”以复制 PowerShell 脚本，然后右键单击 Shell 窗格以粘贴该脚本：  

> [!IMPORTANT]
> 存储帐户名称必须唯一，长度介于 3 到 24 个字符，只能使用数字和小写字母。  示例模板的 `storageAccountName` 变量将 `projectName` 参数的最多 11 个字符与 13 个字符的 [uniqueString](./template-functions-string.md#uniquestring) 值组合。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>部署模板

若要在存储帐户中部署模板，请生成 SAS 令牌并将其提供给 -QueryString 参数。 设置到期时间以允许足够的时间来完成部署。 只有帐户所有者才能访问包含这些模板的 blob。 但是，如果为某个 blob 创建 SAS 令牌，则拥有该 SAS 令牌的任何人都可以访问该 blob。 如果其他用户截获了该 URI 和 SAS 令牌，则此用户可以访问该模板。 使用 SAS 令牌是限制对模板的访问的好办法，但不应直接在模板中包括密码等敏感数据。

如果尚未创建资源组，请参阅[创建资源组](./deployment-tutorial-local-template.md#create-resource-group)。

> [!NOTE]
> 在下面的 Azure CLI 代码中，`date` 参数 `-d` 在 macOS 中是无效参数。 因此，macOS 用户若要在 macOS 的终端中将当前时间增加 2 小时，则应使用 `-v+2H`。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>清理资源

通过删除资源组来清理你部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何部署链接模板。 下一篇教程将介绍如何创建用于部署模板的 DevOps 管道。

> [!div class="nextstepaction"]
> [创建管道](./deployment-tutorial-pipeline.md)
