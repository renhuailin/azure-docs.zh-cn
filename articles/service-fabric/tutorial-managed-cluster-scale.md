---
title: 横向扩展 Service Fabric 托管群集
description: 本教程介绍如何横向扩展 Service Fabric 托管群集的节点类型。
ms.topic: tutorial
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e2bd57925ddb78dcfe23742b35c1490584558f8
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110666870"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster"></a>教程：横向扩展 Service Fabric 托管群集

在此教程系列中，我们将讨论以下内容：

> [!div class="checklist"]
> * [如何部署 Service Fabric 托管群集。](tutorial-managed-cluster-deploy.md)
> * 如何横向扩展 Service Fabric 托管群集
> * [如何在 Service Fabric 托管群集中添加和删除节点类型](tutorial-managed-cluster-add-remove-node-type.md)
> * [如何将应用程序部署到 Service Fabric 托管群集](tutorial-managed-cluster-deploy-app.md)

本系列中的这一部分介绍如何执行以下操作：

> [!div class="checklist"]
> * 缩放 Service Fabric 托管群集节点

## <a name="prerequisites"></a>先决条件

* Service Fabric 托管群集（请参阅[部署托管群集](tutorial-managed-cluster-deploy.md)）。
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) 或更高版本（请参阅 [*安装 Azure PowerShell*](/powershell/azure/install-az-ps)）。

## <a name="scale-a-service-fabric-managed-cluster"></a>缩放 Service Fabric 托管群集
更改实例计数，以增加或减少要缩放的节点类型上的节点数。 可以在群集部署的 Azure 资源管理器模板（ARM 模板）或 Service Fabric 资源管理器中找到节点类型名称。  

> [!NOTE]
> 如果节点类型为主节点，则基本 SKU 群集的节点数不能少于 3 个，标准 SKU 群集的节点数不能少于 5 个。

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

群集会自动开始升级，几分钟后你将看到其他节点。

## <a name="next-steps"></a>后续步骤

在此步骤中，我们在 Service Fabric 托管群集上缩放了节点类型。 若要详细了解如何添加和删除节点类型，请参阅：

> [!div class="nextstepaction"]
> [在 Service Fabric 托管群集中添加和删除节点类型](tutorial-managed-cluster-add-remove-node-type.md)
