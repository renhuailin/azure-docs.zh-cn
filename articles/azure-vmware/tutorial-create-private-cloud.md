---
title: 教程 - 创建和部署 Azure VMware 解决方案私有云
description: 了解如何创建和部署 Azure VMware 解决方案私有云
ms.topic: tutorial
ms.date: 11/19/2020
ms.openlocfilehash: c8383e987e13e43ea9bc9ba5be196538a259aa8c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653120"
---
# <a name="tutorial-create-an-azure-vmware-solution-private-cloud"></a>教程：创建 Azure VMware 解决方案私有云

本教程将介绍如何创建和部署 Azure VMware 解决方案私有云。 最精简的初始部署包括三个主机。 可以逐个添加更多的主机，每个群集最多可以包含 16 个主机。 

由于在启动时 Azure VMware 解决方案不允许使用本地 vCenter 管理私有云，因此需要进行额外配置。 本教程将介绍这些过程和相关先决条件。

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建 Azure VMware 解决方案私有云
> * 验证已部署的私有云

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 拥有适当的管理权限和创建私有云的权限。 你在订阅中必须至少是参与者级别。
- 按照你在部署 Azure VMware 解决方案的[规划](production-ready-deployment-steps.md)文章中收集的信息进行操作。
- 确保已按照以下文章中所述配置相应的网络：[教程：网络清单](tutorial-network-checklist.md)。
- 已按[请求主机和启用 Microsoft.AVS 资源提供程序](enable-azure-vmware-solution.md)中所述预配了主机并注册了 Microsoft AVS 资源提供程序。

## <a name="create-a-private-cloud"></a>创建私有云

可以使用 [Azure 门户](#azure-portal)或 [Azure CLI](#azure-cli) 创建 Azure VMware 解决方案私有云。

### <a name="azure-portal"></a>Azure 门户

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

可以通过 Azure Cloud Shell 来使用 Azure CLI，而不是使用 Azure 门户来创建 Azure VMware 解决方案私有云。  有关可与 Azure VMware 解决方案一起使用的命令的列表，请参阅 [Azure VMware 命令](/cli/azure/ext/vmware/vmware)。

#### <a name="open-azure-cloud-shell"></a>打开 Azure Cloud Shell

从代码块的右上角选择“试用”。 也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

#### <a name="create-a-resource-group"></a>创建资源组

使用 `[az group create](/cli/azure/group)` 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组： 

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>创建私有云

提供资源组和私有云的名称、位置和群集大小。

| 属性  | 说明  |
| --------- | ------------ |
| -g（资源组名称）     | 私有云资源的资源组的名称。        |
| -n（私有云名称）     | Azure VMware 解决方案私有云的名称。        |
| --location     | 用于私有云的位置。         |
| --cluster-size     | 群集的大小。 最小值为 3。         |
| --network-block     | 用于私有云的 CIDR IP 地址网络块。 地址块不应与订阅和本地网络中的其他虚拟网络中使用的地址块重叠。        |
| --sku | SKU 值：AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Azure VMware 命令

有关可与 Azure VMware 解决方案一起使用的命令的列表，请参阅 [Azure VMware 命令](/cli/azure/ext/vmware/vmware)。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建 Azure VMware 解决方案私有云
> * 验证已部署的私有云
> * 删除 Azure VMware 解决方案私有云

继续学习下一教程，了解如何创建跳转盒。 使用跳转盒连接到你的环境，以便在本地管理你的私有云。


> [!div class="nextstepaction"]
> [访问 Azure VMware 解决方案私有云](tutorial-access-private-cloud.md)