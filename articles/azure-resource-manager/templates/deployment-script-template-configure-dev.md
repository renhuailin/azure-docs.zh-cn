---
title: 为模板中的部署脚本配置开发环境 |Microsoft Docs
description: 为 Azure 资源管理器模板中的部署脚本配置开发环境 (ARM 模板) 。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: f731236b235883f019c74ef0b32f5066ca5b7514
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179361"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>为 ARM 模板中的部署脚本配置开发环境

了解如何创建开发环境以使用部署脚本映像开发和测试 ARM 模板部署脚本。 可以创建 [Azure 容器实例](../../container-instances/container-instances-overview.md) ，也可以使用 [Docker](https://docs.docker.com/get-docker/)。 本文介绍了这两个选项。

## <a name="prerequisites"></a>先决条件

### <a name="azure-powershell-container"></a>Azure PowerShell 容器

如果没有 Azure PowerShell 部署脚本，则可以使用以下内容创建 *hello.ps1* 文件：

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Azure CLI 容器

对于 Azure CLI 容器映像，可以使用以下内容创建 *hello.sh* 文件：

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> 运行 Azure CLI 部署脚本时，名为的环境变量将 `AZ_SCRIPTS_OUTPUT_PATH` 存储脚本输出文件的位置。 环境变量在开发环境容器中不可用。 有关使用 Azure CLI 输出的详细信息，请参阅 [使用 CLI 的输出脚本](deployment-script-template.md#work-with-outputs-from-cli-script)。

## <a name="use-azure-powershell-container-instance"></a>使用 Azure PowerShell 容器实例

若要在你的计算机上创建脚本，需要创建一个存储帐户，并将存储帐户装载到容器实例。 这样，你就可以将脚本上传到存储帐户，并在容器实例上运行该脚本。

> [!NOTE]
> 为测试你的脚本而创建的存储帐户与部署脚本服务用来执行脚本的存储帐户不是同一个。 部署脚本服务会在每次执行时创建唯一名称作为文件共享。

### <a name="create-an-azure-powershell-container-instance"></a>创建 Azure PowerShell 容器实例

以下 Azure 资源管理器模板 (ARM 模板) 创建容器实例和文件共享，然后将文件共享装载到容器映像。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

装载路径的默认值为 `/mnt/azscripts/azscriptinput` 。 这是在容器实例中装载到文件共享位置的路径。

模板中指定的默认容器映像为 **mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2**。 查看所有 [受支持的 Azure PowerShell 版本](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)的列表。

模板在1800秒后挂起容器实例。 在容器实例进入终止状态并且会话结束之前，你有30分钟的时间。

若要部署模板：

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>上载部署脚本

将部署脚本上传到存储帐户。 下面是 PowerShell 脚本的示例：

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

你还可以使用 Azure 门户或 Azure CLI 上传该文件。

### <a name="test-the-deployment-script"></a>测试部署脚本

1. 在 Azure 门户中，打开部署了容器实例的资源组和存储帐户。
2. 打开容器组。 默认容器组名称是附加了 *cg* 的项目名称。 容器实例处于 " **正在运行** " 状态。
3. 在 "资源" 菜单中，选择 " **容器**"。 容器实例名称是附加有 *容器* 的项目名称。

    ![Azure 门户中的部署脚本连接容器实例的屏幕截图。](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. 依次选择“连接”、“连接” 。 如果无法连接到容器实例，请重启容器组，然后重试。
5. 在控制台窗格中运行以下命令：

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    输出为 **Hello John Dole**。

    ![部署脚本的屏幕截图在控制台中连接容器实例测试输出。](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>使用 Azure CLI 容器实例

若要在计算机上创作脚本，请创建一个存储帐户，并将存储帐户装载到容器实例。 然后，可以将脚本上传到存储帐户，并在容器实例上运行该脚本。

> [!NOTE]
> 为测试脚本而创建的存储帐户与部署脚本服务用于执行脚本的存储帐户不同。 部署脚本服务会在每次执行时创建唯一名称作为文件共享。

### <a name="create-an-azure-cli-container-instance"></a>创建 Azure CLI 容器实例

以下 ARM 模板创建容器实例和文件共享，然后将文件共享装载到容器映像：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

装载路径的默认值为 `/mnt/azscripts/azscriptinput` 。 这是在容器实例中装载到文件共享位置的路径。

模板中指定的默认容器映像为 **mcr.microsoft.com/azure-cli:2.9.1**。 查看[支持的 Azure CLI 版本](https://mcr.microsoft.com/v2/azure-cli/tags/list)的列表。

> [!IMPORTANT]
> 部署脚本使用 Microsoft 容器注册表 (MCR) 中的可用 CLI 映像。 需要约一个月来验证部署脚本的 CLI 映像。 不要使用 30 天内发布的 CLI 版本。 若要查找映像的发行日期，请参阅 [Azure CLI 发行说明](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true)。 如果使用不受支持的版本，错误消息会列出受支持的版本。

模板在1800秒后挂起容器实例。 你在容器实例进入终端状态之前有30分钟的时间，并且会话结束。

若要部署模板：

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>上载部署脚本

将部署脚本上传到存储帐户。 下面是一个 PowerShell 示例：

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

你还可以使用 Azure 门户或 Azure CLI 上传该文件。

### <a name="test-the-deployment-script"></a>测试部署脚本

1. 在 Azure 门户中，打开部署了容器实例的资源组和存储帐户。
1. 打开容器组。 默认容器组名称是附加了 *cg* 的项目名称。 容器实例显示为 " **正在运行** " 状态。
1. 在 "资源" 菜单中，选择 " **容器**"。 容器实例名称是附加有 *容器* 的项目名称。

    ![部署脚本连接容器实例](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. 依次选择“连接”、“连接” 。 如果无法连接到容器实例，请重启容器组，然后重试。
1. 在控制台窗格中运行以下命令：

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    输出为 **Hello John Dole**。

    ![部署脚本容器实例测试](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>使用 Docker

你可以使用预配置的 Docker 容器映像作为部署脚本开发环境。 若要安装 Docker，请参阅[获取 Docker](https://docs.docker.com/get-docker/)。
你还需要配置文件共享以将包含部署脚本的目录装载到 Docker 容器中。

1. 将部署脚本容器映像拉取到本地计算机：

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    此示例使用 PowerShell 4.3.0 版本。

    从 MCR 拉取 CLI 映像：

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    此示例使用 CLI 2.0.80 版本。 部署脚本使用[此处](https://hub.docker.com/_/microsoft-azure-cli)的默认 CLI 容器映像。

1. 在本地运行 Docker 映像。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    将“&lt;主机驱动器号>”和“&lt;主机目录名>”替换为共享驱动器上的现有文件夹 。 它将文件夹映射到容器中的/data 文件夹。 例如，若要映射 _D:\docker_：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** 意味着使容器映像保持活动状态。

    CLI 示例：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 以下屏幕截图显示了如何运行 PowerShell 脚本，假设共享驱动器中有 *helloworld.ps1* 文件。

    ![资源管理器模板部署脚本 docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

成功测试脚本后，可以将其用作模板中的部署脚本。

## <a name="next-steps"></a>后续步骤

本教程已介绍部署脚本的用法。 完成部署脚本教程：

> [!div class="nextstepaction"]
> [教程：在 ARM 模板中使用部署脚本](./template-tutorial-deployment-script.md)
