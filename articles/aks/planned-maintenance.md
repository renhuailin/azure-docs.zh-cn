---
title: 为 Azure Kubernetes 服务 (AKS) 群集使用计划内维护（预览版）
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用计划内维护。
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 1c0a7768c0f5e30d9d5ab3b0fa32b0eb6f4946c3
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428414"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>使用计划内维护为 Azure Kubernetes 服务 (AKS) 群集安排维护时段（预览版）

AKS 群集会自动进行定期维护。 默认情况下，随时都可能进行这项工作。 通过计划内维护，可以安排每周维护时段，以便更新控制平面以及 VMSS 实例上的 kube-system Pod 并最大程度地减少工作负荷影响。 安排后，所有维护都将在所选时段内进行。 可以通过指定某一日期或特定日期的时间范围，在群集上安排一个或多个每周时段。 维护时段是使用 Azure CLI 配置的。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>限制

使用计划内维护时，以下限制适用：

- 对于紧急或关键的计划外/反应性维护操作，AKS 保留在这些时段外进行操作的权利。
- 目前，尽最大努力执行维护操作，但不保证在指定时段内执行。
- 阻止更新的时间不能超过 7 天。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

还需要 aks-preview Azure CLI 扩展 0.5.4 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 或者使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>允许在每个星期一的凌晨 1:00 至凌晨 2:00 进行维护

若要添加维护时段，可以使用 `az aks maintenanceconfiguration add` 命令。

> [!IMPORTANT]
> 目前，必须设置 `default` 作为 `--name` 的值。 使用任何其他名称将导致维护时段无法运行。
>
> 计划内维护时段以协调世界时 (UTC) 方式指定。

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

以下示例输出显示了每个星期一凌晨 1:00 至凌晨 2:00 间的维护时段。

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

若要允许在一天中的任意时间进行维护，请忽略 start-hour 参数。 例如，以下命令可将维护时段设置为每个星期一的全天：

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>使用 JSON 文件添加维护配置

也可以使用 JSON 文件（而不是使用参数）来创建维护时段。 创建具有以下内容的 `test.json` 文件：

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

上述 JSON 文件将维护时段指定为每个星期二的凌晨 1:00 - 凌晨 3:00，以及每个星期三的凌晨 1:00 - 凌晨 2:00 和上午 6:00 - 上午 7:00。 从 2021-05-26T03:00:00Z 到 2021-05-30T12:00:00Z 还有一个例外时段，即使该时段与维护时段重叠，也不允许在该时段进行维护 。 以下命令从 `test.json` 添加维护时段。

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>更新现有的维护时段

若要更新现有的维护配置，请使用 `az aks maintenanceconfiguration update` 命令。

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>在现有群集中列出所有维护时段

若要查看 AKS 群集中所有的当前维护配置时段，请使用 `az aks maintenanceconfiguration list` 命令。

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

在以下输出中，可以看到为 myAKSCluster 配置了两个维护时段。 一个时段是星期一的凌晨 1:00，另一个时段是星期五的凌晨 4:00。

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>在 AKS 群集中显示特定的维护配置时段

若要查看 AKS 群集中的特定维护配置时段，请使用 `az aks maintenanceconfiguration show` 命令。

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

以下示例输出显示了默认的维护时段：

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>删除现有 AKS 群集中的某个维护配置时段

若要删除 AKS 群集中的某个维护配置时段，请使用 `az aks maintenanceconfiguration delete` 命令。

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="using-planned-maintenance-with-cluster-auto-upgrade"></a>将计划内维护与群集自动升级结合使用

计划内维护会检测你是否在使用群集自动升级并在维护时段内自动安排升级。 有关群集自动升级的更多详细信息，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集][aks-upgrade]。

## <a name="next-steps"></a>后续步骤

- 若要开始升级 AKS 群集，请参阅[升级 AKS 群集][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
