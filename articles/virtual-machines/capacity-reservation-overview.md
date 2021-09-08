---
title: Azure 中的按需容量预留（预览版）
description: 了解如何通过容量预留来预留 Azure 地区或可用性区域中的计算容量。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: ee14ea525575a49abd4e4026201c3fa39ffa84b9
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273285"
---
# <a name="on-demand-capacity-reservation-preview"></a>按需容量预留（预览版）

通过按需容量预留，可将 Azure 地区或可用性区域中的计算容量预留任意一段时间。 与[预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)不同，你无需注册 1 年或 3 年期承诺。 随时可创建和删除预留，并可完全控制预留管理方式。  

创建容量预留后，容量将立即可用，并专门保留供你使用，直到删除预留为止。  


> [!IMPORTANT]
> 容量预留功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


容量预留具有一些始终在创建时定义的基本属性： 
- **VM 大小** - 每个预留用于一个 VM 大小。 例如，`Standard_D2s_v3`。 
- **位置** - 每个预留用于一个位置（地区）。 如果该位置具有可用性区域，则预留还可指定其中一个区域。 
- **数量** - 每个预留都有一个要预留的实例数量。 

若要创建容量预留，这些参数将作为容量请求传递到 Azure。 如果订阅缺少必需的配额，或者 Azure 没有满足规范的可用容量，则无法部署预留。 为了避免部署失败，请请求更多配额，或者尝试使用不同的 VM 大小、位置或区域组合。 

Azure 接受预留请求后，具有匹配配置的 VM 就可使用预留。 若要使用容量预留，VM 必须指定预留作为其属性之一。 否则，将不会使用容量预留。 这种设计的一个好处是，你可仅使关键工作负载面向预留，其他非关键工作负载可在不使用预留容量的情况下运行。   

> [!NOTE]
> 容量预留功能还附带了 Azure 可用性 SLA 可用于虚拟机。 在公共预览版期间，不会强制实施 SLA；在容量预留功能正式发布时，将定义它。


## <a name="register-for-capacity-reservation"></a>注册容量预留 

必须先注册预览版订阅，然后才能使用容量预留功能。 注册可能需要几分钟才能完成。 可以使用 Azure CLI 或 PowerShell 完成功能注册。

### <a name="cli"></a>[CLI](#tab/cli1)

1. 使用 [az feature register](/cli/azure/feature#az_feature_register) 为订阅启用预览版：

    ```azurecli-interactive
    az feature register --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. 功能注册最多可能需要 15 分钟。 检查注册状态：

    ```azurecli-interactive
    az feature show --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. 为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程：

    ```azurecli-interactive
    az provider register --namespace Microsoft.Compute
    ``` 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 使用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为订阅启用预览版：

    ```powershell-interactive
    Register-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. 功能注册最多可能需要 15 分钟。 检查注册状态：

    ```powershell-interactive
    Get-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. 为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程：

    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ``` 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="benefits-of-capacity-reservation"></a>容量预留的优势 

- 部署后，容量会保留供你使用，并且在适用 SLA 的范围内使用可用  
- 可随时部署和删除它，无需期限承诺 
- 可自动与预留实例组合，来享受期限承诺折扣  

## <a name="sla-for-capacity-reservation"></a>容量预留的 SLA 

稍后将在功能正式发布时定义容量预留的 SLA。  


## <a name="limitations-and-restrictions"></a>限制和局限 

- 创建容量预留需要配额，方式与创建虚拟机时一样。 
- 容量预留不支持现成 VM 和 Azure 专用主机节点。 
- 一些部署约束不受支持： 
    - 邻近放置组 
    - 更新域 
    - UltraSSD 存储  
- 公共预览版期间仅支持 Av2、B、D、E 和 F VM 系列。 
- 对于公共预览版期间支持的 VM 系列，支持最多 3 个容错域 (FD)。 FD 超过 3 个的部署将无法针对容量预留进行部署。 
- 容量预留不支持可用性集。 
- 在此预览版期间，只有创建预留的订阅可使用它。 
- 预留仅适用于 Azure 付费客户。 免费试用版和面向学生的 Azure 等折扣帐户没有资格使用此功能。 


## <a name="pricing-and-billing"></a>定价和计费 

容量预留的价格费率与基础 VM 大小相同。 例如，如果你为 10 个 D2s_v3 VM 创建一个预留，则创建该预留后，即使没有使用预留，我们也将就 10 D2s_v3 VM 开始对你计费。  

如果你随后部署了一个 D2s_v3 VM，并指定预留作为其属性，则会使用容量预留。 使用后，你将只需为该 VM 付费，容量预留不额外产生费用。 假设你针对上述容量预留部署 5 个 D2s_v3 VM。 你的账单上将显示 5 个 D2s_v3 VM 和 5 个未使用的容量预留的费用，两者都按 D2s_v3 VM 的费率收费。    

已使用和未使用的容量预留都有资格享有预留实例期限承诺折扣。 在上例中，如果你对同一 Azure 地区中的 2 个 D2s_v3 VM 具有预留实例，则这两项资源（VM 或未使用的容量预留）的账单将归零，你将仅对其余 8 项资源（即 5 个未使用的容量预留和 3 个 D2s_v3 VM）付费。 在此情况下，期限折扣承诺可应用于 VM 或未使用的容量预留，两者都按即用即付费率收费。 


## <a name="work-with-capacity-reservation"></a>使用容量预留 

可在 Azure 地区或可用性区域中为特定 VM 大小创建容量预留。 所有预留都作为容量预留组的一部分进行创建和管理，这样可在单个多层应用程序中创建一个组来管理不同的 VM 大小。 每个预留用于一个 VM 大小，一个组中的每个 VM 大小只能有一个预留。  

若要使用容量预留，请指定容量预留组作为 VM 属性之一。 如果组没有匹配的预留，Azure 将返回错误消息。 

初始部署后，可通过更改容量属性来调整预留的预留数量。 不可对容量预留进行其他更改，例如 VM 大小或位置。 建议删除现有预留，然后创建具有新要求的新预留。 

容量预留不对 VM 部署数设定限制。 Azure 支持针对预留分配所需数量的 VM。 由于预留本身需要配额，因此不超过预留数量的 VM 部署省略配额检查。 若要针对预留分配更多 VM，则需要进行配额检查，且 Azure 能够提供额外容量。 部署后，这些额外的 VM 实例会导致针对预留分配的 VM 数超过预留数量。 此状态被称为“过度分配”。 若要了解详细信息，请转到[容量预留过度分配](capacity-reservation-overallocate.md)。 


## <a name="capacity-reservation-lifecycle"></a>容量预留生命周期

创建预留后，Azure 会在指定位置留出请求的容量实例数： 

![容量预留图像 1。](\media\capacity-reservation-overview\capacity-reservation-1.jpg) 

通过以下属性跟踪整体预留的状态：  
- `capacity` = 客户预留的实例总数 
- `virtualMachinesAllocated` = 针对容量预留分配且计入使用容量的 VM 的列表。 这些 VM 的状态是“正在运行”或“已停止(已分配)”，或者可能处于“正在启动”或“正在停止”等过渡状态    。 此列表不包含处于“已解除分配”状态的 VM（此状态称为“已停止(已解除分配)”） 。 
- `virtualMachinesAssociated` = 与容量预留关联的 VM 列表。 此列表包含已配置为使用预留的所有 VM，包括处于“已解除分配”状态的 VM。  

在上例中，初始设置将是 `capacity` 为 2，长度为 `virutalMachinesAllocated`，`virtualMachinesAssociated` 为 0。  

随后在针对容量预留分配 VM 后，该 VM 将在逻辑上使用其中一个预留容量实例： 

![容量预留图像 2。](\media\capacity-reservation-overview\capacity-reservation-2.jpg) 

容量预留的状态显示将是 `capacity` 为 2，长度为 `virutalMachinesAllocated`，而 `virtualMachinesAssociated` 为 1。  

只有 VM 具有匹配的属性，且至少有一个空的容量实例，针对容量预留的分配就将成功。  

根据我们的示例，针对容量预留分配第三个 VM 时，预留会进入[过度分配](capacity-reservation-overallocate.md)状态。 第三个 VM 将需要未使用的配额且要求 Azure 额外提供容量。 分配第三个 VM 后，容量预留现如下所示： 

![容量预留图像 3。](\media\capacity-reservation-overview\capacity-reservation-3.jpg) 

`capacity` 为 2，长度为 `virutalMachinesAllocated`，而 `virtualMachinesAssociated` 为 3。 

现在假设应用程序纵向缩减到最少两个 VM。 VM 0 需要更新，因此选择它来解除分配。 预留会自动切换到此状态： 

![容量预留图像 4。](\media\capacity-reservation-overview\capacity-reservation-4.jpg) 

`virtualMachinesAllocated` 的长度和 `capacity` 都是 2。 不过，`virtualMachinesAssociated` 的长度仍然是 3，原因是 VM 0 虽然已解除分配，但仍与容量预留关联。  

容量预留将一直存在，直到被显式删除为止。 若要删除容量预留，首先要在 `virtualMachinesAssociated` 属性中取消关联所有 VM。 取消关联后，容量预留应如下所示： 

![容量预留图像 5。](\media\capacity-reservation-overview\capacity-reservation-5.jpg) 

容量预留的状态显示将是 `capacity` 为 2，长度为 `virtualMachinesAssociated`，而 `virtualMachinesAllocated` 为 0。 在此状态中，可删除容量预留。 删除后，你不再为预留付费。  

![容量预留图像 6。](\media\capacity-reservation-overview\capacity-reservation-6.jpg)


## <a name="usage-and-billing"></a>使用情况和计费 

当容量预留为空时，将报告相应 VM 大小和位置的 VM 使用量。 即使未部署 VM，[VM 预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)也将涵盖部分或全部预留容量使用量。 

### <a name="example"></a>示例

例如，假设创建了一个容量预留，它的预留数量为 2。 订阅有权访问一个大小相同的匹配预留 VM 实例。 结果就是容量预留有两个使用流，其中一个由预留容量涵盖： 

![容量预留图像 7。](\media\capacity-reservation-overview\capacity-reservation-7.jpg)

在上图中，向其中一个未使用的实例应用了一个预留 VM 实例，因此该实例的成本将归零。对于其他实例，预留的 VM 大小采用即用即付费率计费。  

针对容量预留分配 VM 时，还必须分配其他 VM 组件（例如磁盘、网络、扩展和其他任何请求的组件）。 在此状态中，VM 使用量将反映出一个已分配的 VM 和一个未使用的容量实例。 预留 VM 实例将使该 VM 或未使用的容量实例的费用归零。 账单上还将显示与分配的 VM 关联的磁盘、网络和其他组件的其他费用。 

![容量预留图像 8。](\media\capacity-reservation-overview\capacity-reservation-8.jpg)

在上图中，VM 预留实例折扣应用于 VM 0，这将仅针对磁盘和网络等其他组件收费。 另一个未使用的实例按预留的 VM 大小的即用即付费率收费。


## <a name="frequently-asked-questions"></a>常见问题 

- **按需容量预留的价格是多少？**

    按需容量预留的价格与预留关联的基础 VM 大小的价格相同。 使用容量预留时，无论 VM 是否已预配，我们都将按即用即付费率对你选择的 VM 大小收费。  有关更多详细信息，请访问 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 和 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) VM 定价页面。

- **对于按需容量预留和我最终预配后的实际 VM，会分别向我收费吗？**

    否，我们将仅对按需容量预留收费一次。   

- **我是否可将虚拟机预留实例 (RI) 应用于按需容量预留来节省成本？**

    是，你可将现有或未来的 RI 应用于按需容量预留并享受 RI 折扣。 可用 RI 会按应用到 VM 的方式自动应用于容量预留。

- **虚拟机预留实例 (RI) 和按需容量预留之间有何区别？**

    RI 和按需容量预留都适用于 Azure VM。 但是，得益于 1 年或 3 年期承诺，RI 与即用即付费率相比为 VM 提供更优惠的预留费率。 相反，无需作出承诺即可按需容量预留。 你可随时创建或取消容量预留。 但是，不会应用折扣，而且在成功预配容量预留后，将按即用即付费率向你收费。 RI 优先考虑容量但不保证提供，与它不同，当你购买按需容量预留时，Azure 会为你的 VM 留出计算容量并提供 SLA 保证。  

- **哪些方案从按需容量预留中获益最大？**

    典型方案包括业务连续性、灾难恢复和任务关键型应用程序横向扩展。  


## <a name="next-steps"></a>后续步骤

创建容量预留，并开始在 Azure 地区或可用性区域中预留计算容量。 

> [!div class="nextstepaction"]
> [创建容量预留](capacity-reservation-create.md)