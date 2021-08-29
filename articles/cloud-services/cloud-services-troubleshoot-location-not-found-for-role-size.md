---
title: 将云服务（经典）部署到 Azure 时对 LocationNotFoundForRoleSize 进行故障排除 | Microsoft Docs
description: 本文介绍了如何在将云服务（经典）部署到 Azure 时解决 LocationNotFoundForRoleSize 异常。
services: cloud-services
author: hirenshah1
ms.author: hirshah
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f45e28e2c7a816f923d1af579b818e546c992717
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821489"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>将云服务（经典）部署到 Azure 时对 LocationNotFoundForRoleSize 进行故障排除

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

本文介绍了在部署 Azure 云服务（经典）时，对虚拟机 (VM) 大小不可用的分配失败进行故障排除。

将实例部署到云服务（经典）或者添加新的 Web 角色或辅助角色实例时，Microsoft Azure 会分配计算资源。

在执行这些操作时，甚至在达到 Azure 订阅限制之前，有时可能会收到错误。

> [!TIP]
> 规划服务的部署时，本信息可能也有用。

## <a name="symptom"></a>症状

在 Azure 门户中，导航到云服务（经典），并在侧栏中选择“操作日志(经典)”来查看日志。

![图像显示“操作日志(经典)”边栏选项卡。](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

检查云服务（经典）的日志时，你将看到以下异常：

|异常类型  |错误消息  |
|---------|---------|
|LocationNotFoundForRoleSize     |操作“`{Operation ID}`”失败：“请求的 VM 层当前不可用于此订阅的区域 (`{Region ID}`) 中。 请尝试请求其他层或部署到其他位置。|

## <a name="cause"></a>原因

你正在向其中进行部署的区域或群集存在容量问题。 如果选择的资源 SKU（VM 大小）不可用于指定区域，则会出现“LocationNotFoundForRoleSize”异常。

## <a name="solution"></a>解决方案

在这种情况下，你应该选择一个不同的区域或 SKU 来部署云服务（经典）。 在部署或升级云服务（经典）之前，你可以确定某个区域或可用性区域中有哪些 SKU 可用。 请遵循下面的 [Azure CLI](#list-skus-in-region-using-azure-cli)、[PowerShell](#list-skus-in-region-using-powershell) 或 [REST API](#list-skus-in-region-using-rest-api) 过程。

### <a name="list-skus-in-region-using-azure-cli"></a>使用 Azure CLI 列出区域中的 SKU

可以使用 [az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) 命令。

- 使用 `--location` 参数将输出筛选为你正在使用的位置。
- 使用 `--size` 参数按部分大小名称搜索。
- 有关详细信息，请参阅[解决有关 SKU 不可用的错误](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli)指南。

    例如：

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    示例结果：![运行“az vm list-skus --location southcentralus --size Standard_F --output table”命令的 Azure CLI 输出，其中显示了可用的 SKU。](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>使用 PowerShell 列出区域中的 SKU

你可以使用 [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 命令。

- 按位置筛选结果。
- 必须拥有最新版本 PowerShell 才能运行此命令。
- 有关详细信息，请参阅[解决有关 SKU 不可用的错误](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell)指南。

例如：

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**一些有用的其他命令：**

筛选出包含大小 (Standard_DS14_v2) 的位置：

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

筛选出包含大小 (V3) 的所有位置：

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>使用 REST API 列出区域中的 SKU

你可以使用[资源 SKU - 列出](/rest/api/compute/resourceskus/list)操作。 它会以下列格式返回可用 SKU 和区域：

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>后续步骤

若要了解更多的分配失败解决方案，并更好地了解它们是如何生成的，请执行以下操作：

> [!div class="nextstepaction"]
> [分配失败 - 云服务（经典）](cloud-services-allocation-failures.md)

如果你有本文未解决的 Azure 问题，请访问 [MSDN 和 Stack Overflow](https://azure.microsoft.com/support/forums/) 上的 Azure 论坛。 可将问题发布到这些论坛上，或发布到 [Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 还可提交 Azure 支持请求。 若要提交支持请求，请在 [Azure 支持](https://azure.microsoft.com/support/options/)页上，选择“获取支持”。
