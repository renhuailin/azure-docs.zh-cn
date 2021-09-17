---
title: 使用 CLI 将 VM 和规模集实例部署到专用主机
description: 使用 Azure CLI 将 VM 和规模集实例部署到专用主机。
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: 3fbc398a937e1b36e88cfd14b0c123d554ca2cd7
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687907"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>使用 Azure CLI 到专用主机
 
适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM 

本文介绍如何创建 Azure [专用主机](../dedicated-hosts.md)来托管虚拟机 (VM)。

确保已安装 Azure CLI 2.16.0 或更高版本，并已使用 `az login` 登录到 Azure 帐户。


## <a name="limitations"></a>限制

- 专用主机可用的大小和硬件类型因区域而异。 请参阅主机[定价页](https://aka.ms/ADHPricing)来了解详细信息。

## <a name="create-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 az group create 创建资源组。 以下示例在“美国东部”位置创建名为“myDHResourceGroup”的资源组。

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus
```

## <a name="list-available-host-skus-in-a-region"></a>列出区域中的可用主机 SKU

并非所有主机 SKU 都适用于所有区域和可用性区域。

在开始预配专用主机之前，列出主机可用性和任何套餐限制。

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

## <a name="create-a-host-group"></a>创建主机组

主机组是表示专用主机集合的资源。 可在区域和可用性区域中创建主机组，并向其添加主机。 规划高可用性时，有其他选项可供选择。 你可以将以下一个或两个选项与专用主机一起使用：
- 跨多个可用性区域。 在这种情况下，你需要在要使用的每个区域中都有一个主机组。
- 跨映射到物理机架的多个容错域。

在任一情况下，都需要为主机组提供容错域计数。 如果你不希望跨组中的容错域，请使用容错域计数 1。

还可以决定使用可用性区域和容错域。


在此示例中，我们将通过 [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) 使用可用性区域和容错域创建主机组。

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

添加 `--automatic-placement true` 参数，将 VM 和规模集实例自动放置在主机组中的主机上。 如需了解详情，请参阅[手动放置与自动放置](../dedicated-hosts.md#manual-vs-automatic-placement)。


### <a name="other-examples"></a>其他示例

还可以使用 [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) 在可用性区域 1（无容错域）中创建主机组。

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1
```

下面通过 [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create)，只使用容错域创建主机组（在不支持可用性区域的区域中使用）。

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2
```

## <a name="create-a-host"></a>创建主机

现在，让我们在主机组中创建一个专用主机。 除了主机名称外，还需要提供主机的 SKU。 主机 SKU 捕获受支持的 VM 系列以及专用主机的硬件代系。

有关主机 SKU 和定价的详细信息，请参阅 [Azure 专用主机定价](https://aka.ms/ADHPricing)。

使用 [az vm host create](/cli/azure/vm/host#az_vm_host_create) 可创建主机。 如果为主机组设置了容错域计数，则系统会要求你为主机指定容错域。

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```



## <a name="create-a-virtual-machine"></a>创建虚拟机
使用 [az vm create](/cli/azure/vm#az_vm_create) 在专用主机中创建虚拟机。 如果在创建主机组时指定了可用性区域，则需要在创建虚拟机时使用同一区域。

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

若要将 VM 放置在特定主机上，请使用 `--host`，而不是使用 `--host-group` 指定主机组。

> [!WARNING]
> 如果在没有足够资源的主机上创建虚拟机，则虚拟机将创建为“失败”状态。

## <a name="create-a-scale-set"></a>创建规模集

部署规模集时，需要指定主机组。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

如果要手动选择要将规模集部署到哪个主机，请添加 `--host` 和主机名称。


## <a name="check-the-status-of-the-host"></a>检查主机的状态

可以使用 [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view) 查看主机运行状况以及仍可部署到主机的虚拟机数。

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 输出类似于以下内容：

```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```

## <a name="export-as-a-template"></a>作为模板导出
如果现在要使用相同参数创建额外的开发环境或与其匹配的生产环境，则可以导出模板。 Resource Manager 使用定义了所有环境参数的 JSON 模板。 通过引用此 JSON 模板构建出整个环境。 可以手动构建 JSON 模板，也可以通过导出现有环境来为自己创建 JSON 模板。 使用 [az group export](/cli/azure/group#az_group_export) 导出资源组。

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json
```

此命令在当前工作目录中创建 `myDHResourceGroup.json` 文件。 从此模板创建环境时，系统会提示输入所有资源名称。 可以通过将 `--include-parameter-default-value` 参数添加到 `az group export` 命令在模板文件中填充这些名称。 请编辑 JSON 模板以指定资源名称，或创建 parameters.json 文件来指定资源名称。

若要通过模板创建环境，请使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)。

```azurecli-interactive
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myDHResourceGroup.json
```


## <a name="clean-up"></a>清理

即使没有部署虚拟机，也会对专用主机收费。 你应删除当前未使用的任何主机以节省成本。

只有当不再有虚拟机使用主机时，才能删除该主机。 使用 [az vm delete](/cli/azure/vm#az_vm_delete) 删除 VM。

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

删除 VM 之后，可以使用 [az vm host delete](/cli/azure/vm/host#az_vm_host_delete) 删除主机。

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost
```

删除所有主机后，可以使用 [az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete) 删除主机组。

```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup
```

还可以在单个命令中删除整个资源组。 这会删除在组中创建的所有资源，包括所有 VM、主机和主机组。

```azurecli-interactive
az group delete -n myDHResourceGroup
```

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[专用主机](../dedicated-hosts.md)概述。

- 也可以使用 [Azure 门户](../dedicated-hosts-portal.md)创建专用主机。

- [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)提供了示例模板，该模板同时使用区域和容错域以实现区域中的最大复原能力。
