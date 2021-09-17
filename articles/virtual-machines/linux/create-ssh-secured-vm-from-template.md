---
title: 使用模板在 Azure 中创建 Linux VM
description: 如何使用 Azure CLI 基于资源管理器模板创建 Linux VM
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: ae954f1a461f2a5fd98b0f0374f6583fed34ce05
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689878"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>如何使用 Azure Resource Manager 模板创建 Linux 虚拟机

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

了解如何使用 Azure 资源管理器模板和 Azure Cloud Shell 中的 Azure CLI 来创建 Linux 虚拟机 (VM)。 若要创建 Windows 虚拟机，请参阅[通过资源管理器模板创建 Windows 虚拟机](../windows/ps-template.md)。

一种替代方法是从 Azure 门户部署模板。 若要在门户中打开该模板，请选择“部署到 Azure”按钮。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>模板概述

Azure Resource Manager 模板是 JSON 文件，其中定义了 Azure 解决方案的基础结构和配置。 使用模板可以在解决方案的整个生命周期内重复部署该解决方案，确保以一致的状态部署资源。 若要详细了解模板的格式以及如何构造模板，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。 若要查看资源类型的 JSON 语法，请参阅[定义 Azure Resource Manager 模板中的资源](/azure/templates/microsoft.compute/allversions)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

创建 Azure 虚拟机通常包括两个步骤：

1. 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机前创建资源组。
1. 创建虚拟机。

以下示例通过 [Azure 快速入门模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-sshkey/azuredeploy.json)创建 VM。 此部署仅允许 SSH 身份验证。 出现提示时，提供自己的 SSH 公钥的值，例如 *~/.ssh/id_rsa.pub* 的内容。 如果需要创建 SSH 密钥对，请参阅[如何为 Azure 中的 Linux VM 创建和使用 SSH 密钥对](mac-create-ssh-keys.md)。 下面是该模板的副本：

[!code-json[create-linux-vm](~/quickstart-templates/quickstarts/microsoft.compute/vm-sshkey/azuredeploy.json)]

若要运行 CLI 脚本，请选择“试用”以打开 Azure Cloud Shell。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

最后一个 Azure CLI 命令显示新创建 VM 的公共 IP 地址。 需要通过公共 IP 地址连接到虚拟机。 请参阅本文的下一部分。

在前面的示例中，指定了 GitHub 中存储的一个模板。 还可以下载或创建模板并使用 `--template-file` 参数指定本地路径。

下面是一些其他资源：

- 若要了解如何开发资源管理器模板，请参阅 [Azure 资源管理器文档](../../azure-resource-manager/index.yml)。
- 若要查看 Azure 虚拟机架构，请参阅 [Azure 模板引用](/azure/templates/microsoft.compute/allversions)。
- 若要查看更多的虚拟机模板示例，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

然后，可以通过 SSH 照常连接到 VM。 在上述命令中提供自己的公共 IP 地址：

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>后续步骤

在此示例中，创建了一个基本的 Linux VM。 如需更多包含应用程序框架（或者可以用来创建更复杂环境）的资源管理器模板，请浏览 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

若要了解有关创建模板的更多信息，请查看所部署的资源类型的 JSON 语法和属性：

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- Microsoft.Compute/virtualMachines 
