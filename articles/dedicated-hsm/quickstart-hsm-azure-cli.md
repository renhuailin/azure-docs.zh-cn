---
title: 快速入门：使用 Azure CLI 创建 Azure 专用 HSM
description: 使用 Azure CLI 创建、显示、列出、更新和删除 Azure 专用 HSM。
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 80d5bbb54715c5a1a5102f8991f366e273145edc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868944"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure 专用 HSM

本文介绍如何使用 [az dedicated-hsm](/cli/azure/dedicated-hsm) Azure CLI 扩展来创建和管理 Azure 专用 HSM。

## <a name="prerequisites"></a>必备条件

- Azure 订阅。 如果没有帐户，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。
  
  如果有多个 Azure 订阅，请使用 Azure CLI [az account set](/cli/azure/account#az_account_set) 命令设置要用于计费的订阅。
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- 满足专用 HSM 的所有要求，包括注册、批准以及用于预配的虚拟网络和虚拟机。 有关专用 HSM 要求和先决条件的详细信息，请参阅[教程：使用 Azure CLI 将 HSM 部署到现有虚拟网络中](tutorial-deploy-hsm-cli.md)。
  

## <a name="create-a-resource-group"></a>创建资源组

[Azure 资源组](../azure-resource-manager/management/overview.md)是以组形式部署和管理 Azure 资源的逻辑容器。 如果还没有用于专用 HSM 的资源组，请先使用 [az group create](/cli/azure/group#az_group_create) 命令创建一个资源组。 以下示例在 `westus` Azure 区域中创建名为 `myRG` 的资源组：

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>创建专用 HSM

若要创建专用 HSM，请使用 [az dedicated-hsm create](/cli/azure/dedicated-hsm#az_dedicated_hsm_create) 命令。 以下示例在 `westus` 区域、`myRG` 资源组、指定的订阅、虚拟网络和子网中预配名为 `hsm1` 的专用 HSM。 所需参数为 `name`、`location` 和 `resource group`。

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

完成部署大约需要 25 到 30 分钟。

## <a name="get-a-dedicated-hsm"></a>获取专用 HSM

若要获取当前的专用 HSM，请运行 [az dedicated-hsm show](/cli/azure/dedicated-hsm#az_dedicated_hsm_show) 命令。 以下示例获取 `myRG` 资源组中的 `hsm1` 专用 HSM。

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>更新专用 HSM

使用 [az dedicated-hsm update](/cli/azure/dedicated-hsm#az_dedicated_hsm_update) 命令更新专用 HSM。 以下示例更新 `myRG` 资源组中的 `hsm1` 专用 HSM 及其标记：

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>列出专用 HSM

运行 [az dedicated-hsm list](/cli/azure/dedicated-hsm#az_dedicated_hsm_list) 命令，获取有关当前专用 HSM 的信息。 以下示例列出 `myRG` 资源组中的专用 HSM：

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>删除专用 HSM

若要删除专用 HSM，请使用 [az dedicated-hsm delete](/cli/azure/dedicated-hsm#az_dedicated_hsm_delete) 命令。 以下示例从 `myRG` 资源组中删除 `hsm1` 专用 HSM：

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>删除资源组

如果不再需要为专用 HSM 创建的资源组，可运行 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。 此命令将删除组以及其中的所有资源，包括与专用 HSM 无关的任何资源。 以下示例删除 `myRG` 资源组以及其中的所有内容：

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 专用 HSM，请参阅 [Azure 专用 HSM](overview.md)。
