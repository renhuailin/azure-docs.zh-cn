---
title: 注册 Azure VMware 解决方案资源提供程序
description: 注册 Azure VMware 解决方案资源提供程序的步骤。
ms.topic: include
ms.date: 02/17/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 8f1074a0fa703afb6878b708861a67b4bc6d4454
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729067"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

若要使用 Azure VMware 解决方案，必须首先将资源提供程序注册到你的订阅。 有关资源提供程序的详细信息，请参阅 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户菜单上，选择“所有服务”。

1. 在“所有服务”  框中，输入“订阅”  ，然后选择“订阅”  。

1. 从订阅列表中选择订阅进行查看。

1. 选择“资源提供程序”并在搜索中输入“Microsoft.AVS” 。 
 
1. 如果未注册资源提供程序，请选择“注册”。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

开始使用 Azure CLI：

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

通过 Azure CLI 登录到用于 Azure VMware 解决方案部署的 Azure 订阅。 使用 [az provider register](/cli/azure/provider#az_provider_register) 命令注册 `Microsoft.AVS` 资源提供程序：

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

可以使用 [az provider list](/cli/azure/provider#az_provider_list) 命令查看所有可用的提供程序。

---


 
