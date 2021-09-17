---
title: 快速入门：使用资源管理器模板创建 Ubuntu Linux VM
description: 本快速入门介绍如何使用资源管理器模板创建 Linux 虚拟机
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 173adbaa32444a2eb28c050266a503c9f448f927
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693927"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Ubuntu Linux 虚拟机

适用于：:heavy_check_mark: Linux VM 

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）在 Azure 中部署 Ubuntu Linux 虚拟机 (VM)。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/vm-simple-linux/)。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.compute/vm-simple-linux/azuredeploy.json":::


该模板中定义了多个资源：

- [Microsoft.Network/virtualNetworks/subnets](/azure/templates/Microsoft.Network/virtualNetworks/subnets)：创建子网。
- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts)：创建存储帐户。
- [Microsoft.Network/networkInterfaces](/azure/templates/Microsoft.Network/networkInterfaces)：创建 NIC。
- [Microsoft.Network/networkSecurityGroups](/azure/templates/Microsoft.Network/networkSecurityGroups)：创建网络安全组。
- [Microsoft.Network/virtualNetworks](/azure/templates/Microsoft.Network/virtualNetworks)：创建虚拟网络。
- [Microsoft.Network/publicIPAddresses](/azure/templates/Microsoft.Network/publicIPAddresses)：创建公共 IP 地址。
- [Microsoft.Compute/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)：创建虚拟机。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 该模板将创建 Key Vault 和机密。

    [![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-linux%2fazuredeploy.json)

1. 选择或输入以下值。 使用可用的默认值。

    - 订阅：选择一个 Azure 订阅。
    - 资源组：从下拉列表中选择现有资源组，或选择“新建”并输入资源组的唯一名称，然后单击“确定” 。
    - 位置：选择一个位置。  例如“美国中部”。
    - 管理员用户名：提供用户名，如 azureuser。
    - **身份验证类型**：可以在使用 SSH 密钥或密码之间进行选择。
    - 管理员密码或密钥，具体取决于你选择了哪种身份验证类型：
        - 如果选择“密码”，则密码长度必须至少为 12 个字符，且符合[定义的复杂性要求](faq.yml#what-are-the-password-requirements-when-creating-a-vm-)。
        - 如果选择“sshPublicKey”，则粘贴公钥的内容。
    - DNS 标签前缀：输入要用作 DNS 标签一部分的唯一标识符。
    - Ubuntu OS 版本：选择想要在 VM 上运行的 Ubuntu 版本。
    - 位置：默认与资源组位置相同（如果已存在）。
    - VM 大小：选择要用于 VM 的[大小](../sizes.md)。
    - 虚拟网络名称：要用于 vNet 的名称。
    - 子网名称：VM 应使用的子网的名称。
    - 网络安全组名称：NSG 的名称。
1. 选择“查看 + 创建”。 验证完成后，选择“创建”以创建和部署 VM。


使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure CLI、Azure PowerShell 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../../azure-resource-manager/templates/deploy-cli.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

你可以使用 Azure 门户来检查 VM 和创建的其他资源。 部署完成后，请选择“转到资源组”以查看 VM 和其他资源。


## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组，可以将其删除，这将删除 VM 和资源组中的所有资源。

1. 选择“资源组”。
1. 在资源组页上，选择“删除”。
1. 出现提示时，键入资源组的名称，然后选择“删除”。


## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 ARM 模板部署了一个简单的虚拟机。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。


> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)