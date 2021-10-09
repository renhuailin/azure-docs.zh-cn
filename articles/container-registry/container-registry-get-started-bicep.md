---
title: 快速入门 - 创建注册表 - Bicep
description: 了解如何通过使用 Bicep 文件来创建 Azure 容器注册表。
services: azure-resource-manager
author: mumian
ms.author: jgao
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: azure-resource-manager
ms.openlocfilehash: 689048afd913cf081f993a5238c42f0914119acb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095123"
---
# <a name="quickstart-create-a-container-registry-by-using-a-bicep-file"></a>快速入门：使用 Bicep 文件来创建 Azure 容器注册表

本快速入门介绍如何使用 Bicep 文件来创建 Azure 容器注册表实例。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-bicep-introduction.md)]

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="review-the-bicep-file"></a>查阅 Bicep 文件

使用 Visual studio 代码或你喜欢的编辑器创建一个带有以下内容的文件，并将其命名为 main.bicep：

```bicep
@minLength(5)
@maxLength(50)
@description('Provide a globally unique name of your Azure Container Registry')
param acrName string = 'acr${uniqueString(resourceGroup().id)}'

@description('Provide a location for the registry.')
param location string = resourceGroup().location

@description('Provide a tier of your Azure Container Registry.')
param acrSku string = 'Basic'

resource acrResource 'Microsoft.ContainerRegistry/registries@2021-06-01-preview' = {
  name: acrName
  location: location
  sku: {
    name: acrSku
  }
  properties: {
    adminUserEnabled: false
  }
}
```

Bicep 文件中定义了以下资源：

* [Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)：创建 Azure 容器注册表

可以在[快速入门模板库](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)中找到更多 Azure 容器注册表模板示例。

## <a name="deploy-the-bicep-file"></a>部署 Bicep 文件

若要部署已创建的文件，请打开 PowerShell 或 Azure CLI。 如果要使用集成的 Visual Studio Code 终端，请选择 `ctrl` + ```` ` ```` 组合键。 将当前目录更改为 Bicep 文件所在的位置。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name myContainerRegRG --location centralus

az deployment group create --resource-group myContainerRegRG --template-file main.bicep --parameters acrName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name myContainerRegRG -Location centralus

New-AzResourceGroupDeployment -ResourceGroupName myContainerRegRG -TemplateFile ./main.bicep -acrName "{your-unique-name}"
```

---

> [!NOTE]
> 将 {your-unique-name}（包括大括号）替换为唯一的容器注册表名称。

部署完成后，应会看到一条指出部署成功的消息。

## <a name="review-deployed-resources"></a>查看已部署的资源

使用 Azure 门户或诸如 Azure CLI 之类的工具来查看容器注册表的属性。

1. 在门户中，搜索“容器注册表”，然后选择所创建的容器注册表。

1. 在“概述”页上，记下注册表的“登录服务器” 。 使用 Docker 标记映像并将其推送到注册表时，请使用此 URI。 有关信息，请参阅[使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)。

    :::image type="content" source="media/container-registry-get-started-bicep/registry-overview.png" alt-text="注册表概述":::

## <a name="clean-up-resources"></a>清理资源

当你不再需要该资源时，请删除资源组和注册表。 为此，请访问 Azure 门户，选择包含注册表的资源组，然后选择“删除资源组”。

删除资源组

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 Bicep 文件创建了一个 Azure 容器注册表。 请继续阅读 Azure 容器注册表教程，以更深入地了解 ACR。

> [!div class="nextstepaction"]
> [Azure 容器注册表教程](container-registry-tutorial-prepare-registry.md)

有关引导你完成 Bicep 文件创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> [快速入门：使用 Visual Studio Code 创建 Bicep 文件](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md)
