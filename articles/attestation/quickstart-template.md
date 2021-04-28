---
title: 使用 Azure 资源管理器模板创建 Azure 证明证书
description: 了解如何使用 Azure 资源管理器模板创建 Azure 证明证书。
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 7d70f9ebd071d6699412f56e9dca1abcebb38105
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834239"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure 证明提供程序

[Microsoft Azure 证明](overview.md)是证明受信任的执行环境 (TEE) 的解决方案。 本快速入门将重点介绍部署 Azure 资源管理器模板（ARM 模板）以创建 Microsoft Azure 证明策略的过程。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-attestation-provider-create)。

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

该模板中定义的 Azure 资源：

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开模板。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. 选择或输入以下值。

    除非另有指定，否则请使用默认值创建证明提供程序。

    - **证明提供程序名称**：为 Azure 证明提供程序选择一个名称。
    - 位置：选择一个位置。 例如“美国中部”。
    - **标记**：选择一个位置。 例如“美国中部”。

1. 选择“购买”。 成功部署证明资源后，你会收到通知。

使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

你可以使用 Azure 门户来查看证明资源。

## <a name="clean-up-resources"></a>清理资源

其他 Azure 证明教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。

如果不再需要资源组，可以将其删除，这会删除证明资源。 使用 Azure CLI 或 Azure PowerShell 删除资源组：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 ARM 模板创建了证明资源，并验证了部署。 若要详细了解 Azure 证明，请参阅 [Azure 证明概述](overview.md)。
