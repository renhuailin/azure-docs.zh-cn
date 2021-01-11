---
title: 注册 Azure VMware 解决方案资源提供程序
description: 注册 Azure VMware 解决方案资源提供程序的步骤。
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770805"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

若要使用 Azure VMware 解决方案，必须首先将资源提供程序注册到你的订阅。  

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```


### <a name="azure-portal"></a>Azure 门户
 
1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户菜单上，选择“所有服务”。

1. 在“所有服务”  框中，输入“订阅”  ，然后选择“订阅”  。

1. 从订阅列表中选择订阅进行查看。

1. 选择“资源提供程序”并在搜索中输入“Microsoft.AVS” 。 
 
1. 如果未注册资源提供程序，请选择“注册”。