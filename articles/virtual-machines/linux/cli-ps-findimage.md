---
title: 利用 CLI 查找并使用市场购买计划信息
description: 了解如何使用 Azure CLI 针对市场 VM 映像查找映像 URN 和购买计划参数，如发布服务器、产品/服务、SKU 和版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: b0df8d446716363f21c468838523ffe4804f2e9c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691903"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>使用 Azure CLI 查找 Azure 市场映像信息

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

本主题介绍如何使用 Azure CLI 在 Azure 市场中查找 VM 映像。 使用 CLI、资源管理器模板或其他工具以编程方式创建 VM 时，使用此信息指定市场映像。

还可以使用 [Azure 市场](https://azuremarketplace.microsoft.com/)或 [Azure PowerShell](../windows/cli-ps-findimage.md) 浏览可用的映像和产品/服务。 

## <a name="terminology"></a>术语

Azure 中的市场映像具有以下属性：

* **发布者**：创建映像的组织。 示例：Canonical、MicrosoftWindowsServer
* **产品/服务**：发布者创建的一组相关映像的名称。 示例：UbuntuServer、WindowsServer
* **SKU**：产品/服务的实例，例如分发的主要版本。 示例：18.04-LTS、2019-Datacenter
* **版本**：映像 SKU 的版本号。 

可以单独传递这些值，或将这些值作为映像 URN 传递，并将它们用冒号 (:) 隔开。 例如：发布者:产品/服务:SKU:版本   。 可将 URN 中的版本号替换为 `latest`，以使用最新版本的映像。 

如果映像发布服务器提供附加许可和购买条款，则必须先接受这些许可和条款才能使用映像。  有关详细信息，请参阅[查看购买计划信息](#check-the-purchase-plan-information)。



## <a name="list-popular-images"></a>列出常用映像

运行 [az vm image list](/cli/azure/vm/image) 命令，无需选择 `--all` 选项即可在 Azure 市场中查看常用 VM 映像的列表。 例如，运行以下命令以表格形式显示缓存的常用映像列表：

```azurecli
az vm image list --output table
```

输出包括映像 URN。 还可以使用 UrnAlias，这是为 UbuntuLTS 等常用映像创建的简化版本。

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>查找特定映像

若要在市场中查找特定 VM 映像，请结合使用 `az vm image list` 命令和 `--all` 选项。 此版本命令完成需要一些时间，且会返回冗长输出，因此通常可按 `--publisher` 或其他参数筛选列表。 

例如，以下命令显示所有 Debian 产品/服务（请记住，不使用 `--all` 开关时，只搜索常见映像的本地缓存）：

```azurecli
az vm image list --offer Debian --all --output table 
```

部分输出： 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>查看所有可用映像
 
在某个位置查找映像的另一种方法是，运行序列中的 [az vm image list-publishers](/cli/azure/vm/image)、[az vm image list-offers](/cli/azure/vm/image) 和 [az vm image list-skus](/cli/azure/vm/image) 命令。 可以使用这些命令确定以下值：

1. 列出某个位置的映像发布服务器。 在本例中，我们探讨的是美国西部区域。
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. 对于给定的发布者，列出其产品。 在本例中，我们将 Canonical 添加为发布服务器。
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. 对于给定的产品，列出其 SKU。 在本例中，我们将 UbuntuServer 添加为产品/服务。
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. 对于给定的发布服务器、产品/服务和 SKU，显示所有版本的映像。 在本例中，我们将 18.04-LTS 添加为 SKU。

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

使用 [az vm create](/cli/azure/vm) 命令创建 VM 时，可将 URN 列的此值与 `--image` 参数一起传递。 还可以将 URN 中的版本号替换为“latest”以简单使用最新版本的映像。 

如果使用资源管理器模板部署 VM，请在 `imageReference` 属性中单独设置映像参数。 请参阅[模板参考](/azure/templates/microsoft.compute/virtualmachines)。


## <a name="check-the-purchase-plan-information"></a>查看购买计划信息

Azure 市场中的某些 VM 映像具有附加许可条款和购买条款，你必须接受这些条款，然后才能以编程方式部署这些映像。  

若要从此类映像部署 VM，需要在首次使用时接受映像的条款，每个订阅接受一次。 还需要指定采购计划参数，以便从该映像部署 VM

若要查看映像的购买计划信息，请使用映像的 URN 运行 [az vm image show](/cli/azure/image) 命令。 如果输出中的 `plan` 属性不是 `null`，则映像有条款，在以编程方式部署前需要接受该条款。

例如，Canonical Ubuntu Server 18.04 LTS 映像没有附加条款，因为 `plan` 信息为 `null`：

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

输出：

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

对 Bitnami 映像认证的 RabbitMQ 运行类似的命令显示以下 `plan` 属性：`name`、`product` 和 `publisher`。 （某些映像还具有 `promotion code` 属性。） 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
输出：

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

若要部署此映像，则在使用该映像部署 VM 时，需要接受条款，并提供购买计划参数。

## <a name="accept-the-terms"></a>接受条款

若要查看并接受许可条款，请使用 [az vm image terms](/cli/azure/vm/image/terms) 命令。 接受条款时，将在订阅中启用编程部署。 对于映像的每个订阅，只需接受条款一次。 例如：

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

输出包括指向许可条款的 `licenseTextLink`，并指示 `accepted` 的值为 `true`：

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

若要接受条款，请键入：

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>使用映像参数部署全新 VM

如果已有映像的相关信息，可以使用 `az vm create` 命令来进行部署。 

若要部署没有计划信息的映像（例如，来自 Canonical 的最新 Ubuntu Server 18.04 映像），请传递 `--image` 的 URN：

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


对于具有购买计划参数的映像，如由 Bitnami 映像认证的 RabbitMQ，则可以传递 `--image` 的 URN，并提供购买计划参数：

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

如果收到有关接受映像条款的消息，请查看[接受条款](#accept-the-terms)部分。 请确保 `az vm image accept-terms` 的输出返回值 `"accepted": true,`，表明你已接受映像的条款。


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>将现有 VHD 与购买计划信息配合使用

如果来自 VM 的现有 VHD 是使用付费 Azure 市场映像创建，则在从该 VHD 创建全新 VM 时，可能需要提供购买计划信息。 

如果你仍有原始 VM 或有使用同一市场映像创建的其他 VM，则可以使用 [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) 从其中获取计划名称、发布者和产品信息。 此示例获取 myResourceGroup 资源组中名为 myVM 的 VM，然后显示购买计划信息 。

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

如果在删除原始 VM 之前未获得计划信息，则可提交[支持请求](https://ms.portal.azure.com/#create/Microsoft.Support)。 他们将需要 VM 名称、订阅 ID 和删除操作的时间戳。

获得计划信息后，可以使用 `--attach-os-disk` 参数指定 VHD 来创建新的 VM。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>后续步骤
若要使用映像信息快速创建虚拟机，请参阅[使用 Azure CLI 创建和管理 Linux VM](tutorial-manage-vm.md)。
