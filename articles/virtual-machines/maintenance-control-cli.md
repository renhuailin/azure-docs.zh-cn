---
title: 使用 CLI 对 Azure 虚拟机进行维护控制
description: 了解如何使用维护控制和 CLI 来控制对 Azure VM 应用维护的时间。
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: 3ee2fa74e8d030f12bd177999e5a2f146fa9b5c0
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215560"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>使用维护控制和 Azure CLI 来控制更新

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

利用维护控制，你可以决定何时为隔离的 VM 和 Azure 专用主机的主机基础结构应用平台更新。 本主题介绍维护控制的 Azure CLI 选项。 有关使用维护控制的好处、其限制和其他管理选项的详细信息，请参阅[使用维护控制管理平台更新](maintenance-control.md)。

## <a name="create-a-maintenance-configuration"></a>创建维护配置

使用 `az maintenance configuration create` 创建维护配置。 此示例创建名为 myConfig 的维护配置，该配置的范围限定为主机  。 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

复制输出中的配置 ID 供以后使用。

使用 `--maintenance-scope host` 可确保使用维护配置来控制对主机基础结构的更新。

如果尝试创建同名的但位于不同位置的配置，则会收到错误。 配置名称对于资源组必须是唯一的。

可以使用 `az maintenance configuration list` 来查询可用的维护配置。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>创建具有计划时段的维护配置
你也可以声明一个 Azure 将用于在资源上应用更新的计划时段。 本示例创建名为 myConfig 的维护配置，该配置的计划时段为每月第四个星期一的 5 个小时。 在创建计划时段后，不再需要手动应用更新。

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> 维护持续时间必须为 2 小时或更长时间。 维护重复周期必须设置为至少 35 天内发生一次。

维护重复周期可以表示为每日、每周或每月。 下面是一些示例：
- 每日 - maintenance-window-recur-every："Day" 或 "3Days"
- 每周 - maintenance-window-recur-every："3Weeks" 或 "Week Saturday,Sunday"
- 每月 - maintenance-window-recur-every："Month day23,day24" 或 "Month Last Sunday" 或 "Month Fourth Monday" 


## <a name="assign-the-configuration"></a>分配配置

使用 `az maintenance assignment create` 将配置分配到隔离的 VM 或 Azure 专用主机。

### <a name="isolated-vm"></a>隔离的 VM

使用此配置的 ID 将配置应用到 VM。 指定 `--resource-type virtualMachines`，为 `--resource-name` 提供 VM 的名称，为 `--resource-group` 提供 VM 的资源组，为 `--location` 提供 VM 的位置。 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>专用主机

若要将配置应用到专用主机，需要包含 `--resource-type hosts`、带主机组名称的 `--resource-parent-name`，以及 `--resource-parent-type hostGroups`。 

参数 `--resource-id` 是主机的 ID。 可以使用 [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view) 获取专用主机的 ID。

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>检查配置

可以验证是否正确应用了配置，或者使用 `az maintenance assignment list` 来检查当前应用了哪种配置。

### <a name="isolated-vm"></a>隔离的 VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>专用主机 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>检查挂起的更新

使用 `az maintenance update list` 查看是否存在挂起的更新。 将 --subscription 更新为包含 VM 的订阅的 ID。

如果没有更新，该命令会返回一条错误消息，其中包含文本：`Resource not found...StatusCode: 404`。

如果有更新，则只会返回一个更新，即使存在多个挂起的更新。 此更新的数据会在对象中返回：

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>隔离的 VM

检查隔离的 VM 的挂起更新。 在此示例中，输出的格式为表格，方便你阅读。

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>专用主机

检查专用主机的挂起更新。 在此示例中，输出的格式为表格，方便你阅读。 将资源的值替换为你自己的值。

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>应用更新

使用 `az maintenance apply update` 来应用挂起的更新。 成功后，此命令会返回包含更新详细信息的 JSON。 应用更新调用可能需要长达 2 小时才能完成。

### <a name="isolated-vm"></a>隔离的 VM

创建向隔离的 VM 应用更新的请求。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>专用主机

将更新应用到专用主机。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>检查应用更新的状态 

可以使用 `az maintenance applyupdate get` 检查更新进度。 

可以使用 `default` 作为更新名称来查看上次更新的结果，或将 `myUpdateName` 替换为运行 `az maintenance applyupdate create` 时返回的更新的名称。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime 是完成更新的时间，此更新是你发起的，或由平台在你未使用自行维护时段时发起的。 如果从未通过维护控制来应用更新，则会显示默认值。

### <a name="isolated-vm"></a>隔离的 VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>专用主机

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>删除维护配置

使用 `az maintenance configuration delete` 删除维护配置。 删除配置时，会从关联的资源中删除维护控制。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
