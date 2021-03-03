---
title: 注册 Azure VMware 解决方案资源提供程序
description: 注册 Azure VMware 解决方案资源提供程序的步骤。
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: cd4a6f3003945973f0fe5367eb198823595a412e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750501"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

若要使用 Azure VMware 解决方案，必须首先将资源提供程序注册到你的订阅。 有关资源提供程序的详细信息，请参阅 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Azure 门户
 
1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户菜单上，选择“所有服务”。

1. 在“所有服务”  框中，输入“订阅”  ，然后选择“订阅”  。

1. 从订阅列表中选择订阅进行查看。

1. 选择“资源提供程序”并在搜索中输入“Microsoft.AVS” 。 
 
1. 如果未注册资源提供程序，请选择“注册”。