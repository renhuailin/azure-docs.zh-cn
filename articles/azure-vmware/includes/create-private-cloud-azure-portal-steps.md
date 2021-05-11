---
title: 创建 Azure VMware 解决方案私有云
description: 使用 Azure 门户创建 Azure VMware 解决方案私有云的步骤。
ms.topic: include
ms.date: 04/23/2021
ms.openlocfilehash: 40bd1880511f22d9518d0c4526bc697a3693a518
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945787"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

可以使用 Azure 门户或 Azure CLI 创建 Azure VMware 解决方案私有云。


### <a name="portal"></a>[门户](#tab/azure-portal)

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“创建新资源”。 

1. 在“搜索市场”文本框中键入 `Azure VMware Solution`，然后从列表中选择“Azure VMware 解决方案” 。 

1. 在“Azure VMware 解决方案”窗口中选择“创建” 。

1. 在“基本信息”选项卡上，输入相关字段的值。 

   >[!TIP]
   >你已在此快速入门的[规划阶段](../production-ready-deployment-steps.md)收集了此信息。

   | 字段   | 值  |
   | ---| --- |
   | **订阅** | 选择打算用于部署的订阅。|
   | **资源组** | 选择私有云资源的资源组。 |
   | **位置** | 选择一个位置，例如“美国东部”。 这是你在规划阶段定义的区域。 |
   | **资源名称** | 提供 Azure VMware 解决方案私有云的名称。 |
   | **SKU** | 选择 AV36。 |
   | **主机** | 显示为私有云群集分配的主机数。 默认值为 3，在部署后可以增大或减小此值。  |
   | **地址块** | 输入私有云 CIDR 网络的 IP 地址块,，例如 10.175.0.0/22。 |
   | **虚拟网络** | 将此项留空，因为会在部署后步骤中建立 Azure VMware 解决方案 ExpressRoute 线路。   |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="在“基本信息”选项卡上，输入相关字段的值。" border="true":::

1. 完成后，选择“审阅 + 创建”。 在下一个屏幕上，验证输入的信息。 如果信息全部正确，请选择“创建”。

   > [!NOTE]
   > 此步骤大约需要 3-4 小时。 在现有或同一群集中添加单个节点需要 30-45 分钟。

1. 验证部署是否成功。 导航到创建的资源组，然后选择私有云。  完成部署后，你将看到“成功”状态。 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="验证部署是否成功。" border="true":::


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
可以通过 Azure Cloud Shell 来使用 Azure CLI，而不是使用 Azure 门户来创建 Azure VMware 解决方案私有云。 有关可与 Azure VMware 解决方案一起使用的命令的列表，请参阅 [Azure VMware 命令](/cli/azure/ext/vmware/vmware)。

开始使用 Azure CLI：

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]


1. 使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

   ```azurecli-interactive
   
   az group create --name myResourceGroup --location eastus
   ```

2. 提供资源组和私有云的名称、位置和群集大小。

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
