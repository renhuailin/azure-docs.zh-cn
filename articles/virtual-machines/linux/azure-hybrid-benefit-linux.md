---
title: Azure 混合权益和 Linux VM
description: 了解 Azure 混合权益如何帮助你在 Azure 上运行的 Linux 虚拟机方面节省资金。
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: billing
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 867d4b380266c5cbfbae68ca61d83ab3fd28aee3
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216586"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Azure 混合权益如何应用于 Linux 虚拟机

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

Azure 混合权益是一种许可权益，可帮助你显著降低在云中运行 Red Hat Enterprise Linux (RHEL) 和 SUSE Linux Enterprise Server (SLES) 虚拟机 (VM) 的成本。 利用此权益，只需支付 VM 的基础结构成本，因为 RHEL 或 SLES 订阅涵盖了软件费用。 该权益适用于所有 RHEL 和 SLES 市场即用即付 (PAYG) 映像。

适用于 Linux VM 的 Azure 混合权益现已公开发布。

## <a name="benefit-description"></a>权益说明

通过 Azure 混合权益可以将 Azure 上的现有 RHEL 和 SLES PAYG VM 转换为自带订阅 (BYOS) 计费，从而将本地 RHEL 和 SLES 服务器迁移到 Azure。 通常，在 Azure 上通过 PAYG 映像部署的 VM 会收取基础结构费用和软件费用。 借助 Azure 混合权益，可以在不重新部署的情况下将 PAYG VM 转换为 BYOS 计费模型，因此可以避免任何停机风险。

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Linux VM 上的 Azure 混合权益成本可视化效果。":::

对 RHEL 或 SLES VM 启用该权益后，将不会再对你收取 PAYG VM 上通常会产生的额外软件费用。 相反，VM 会开始产生 BYOS 费用，其中只包括计算硬件费用，而不包括软件费用。

还可以选择将启用了该权益的 VM 转换回 PAYG 计费模型。

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>适用于 Linux VM 的 Azure 混合权益范围

Azure 混合权益适用于 Azure 市场中的所有 RHEL 和 SLES PAYG 映像。 该权益目前不适用于 RHEL 或 SLES BYOS 映像或 Azure 市场中的自定义映像。

如果已在 Linux VM 上使用 Azure 专用主机实例和 SQL 混合权益，则这些权益不符合 Azure 混合权益的资格。

## <a name="get-started"></a>入门

### <a name="red-hat-customers"></a>Red Hat 客户

RHEL 的 Azure 混合权益适用于同时满足以下两个条件的 Red Hat 客户：

- 具有可在 Azure 中使用的有效或未使用的 RHEL 订阅
- 已启用一个或多个这些订阅，以在 Azure 中用于 [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 计划

> [!IMPORTANT]
> 确保已对[云访问](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)计划启用正确的订阅。

若要开始使用 Red Hat 权益：

1. 使用 [Red Hat Cloud Access 客户界面](https://access.redhat.com/management/cloud)在 Azure 中启用一个或多个符合资格的 RHEL 订阅以供使用。

   然后，在启用 Red Hat Cloud Access 的过程中提供的 Azure 订阅将能够使用 Azure 混合权益功能。
1. 将 Azure 混合权益应用于任何现有 RHEL PAYG VM 以及从 Azure 市场 PAYG 映像部署的任何新 RHEL VM。 可以使用 Azure 门户或 Azure CLI 来启用该权益。
1. 若要配置 RHEL VM 的更新源和获取 RHEL 订阅符合性指南，请遵循建议的[后续步骤](https://access.redhat.com/articles/5419341)。


### <a name="suse-customers"></a>SUSE 客户

SUSE 的 Azure 混合权益适用于具有以下订阅的客户：

- 未使用过且有资格在 Azure 中使用的 SUSE 订阅。
- 一个或多个在本地使用，且应移动到 Azure 的有效 SUSE 订阅。
- 已在 SUSE 客户中心激活并要在 Azure 中使用的已购买的订阅。 

> [!IMPORTANT]
> 确保选择要在 Azure 中使用的正确订阅。

若要开始使用 SUSE 权益：

1. 将从 SUSE 或 SUSE 分销商处购买的订阅注册到 [SUSE 客户中心](https://scc.suse.com)。
2. 在 SUSE 客户中心激活订阅。
3. 将接收权益的 VM 注册到 SUSE 客户中心，以从 SUSE 客户中心获取更新。

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>在 Azure 门户中启用和禁用权益

在 Azure 门户中，可以对现有 VM 启用权益，也可以在创建 VM 时对新的 VM 启用权益。

### <a name="enable-the-benefit-for-an-existing-vm-in-the-azure-portal"></a>在 Azure 门户中为现有 VM 启用权益

若要为现有 VM 启用权益，请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com/)。
1. 打开要应用转换的虚拟机页。
1. 在左侧转到“配置”选项。 你会看到“许可”部分。 若要启用 AHB 转换，请选中“是”单选按钮，并选中“确认”复选框。
![创建后的 AHB 配置边栏选项卡](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="enable-the-benefit-when-you-create-the-vm-in-the-azure-portal"></a>在 Azure 门户中创建 VM 时启用权益

若要在创建 VM 时启用权益（SUSE 工作流与此处所示的 RHEL 示例相同），请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com/)。
1. 在门户中转到“创建虚拟机”页。
 ![创建 VM 时的 AHB](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. 单击复选框以启用 AHB 转换并使用云访问许可证。
 ![创建 VM 时的 AHB 复选框](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. 按照下一组说明创建虚拟机。
1. 检查“配置”边栏选项卡，会看到选项已启用。 
![创建后的 AHB 配置边栏选项卡](./media/azure-hybrid-benefit/create-configuration-blade.png)

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>在 Azure CLI 中启用和禁用权益

可以使用 `az vm update` 命令来更新现有 VM。 对于 RHEL VM，使用 `--license-type` 参数 `RHEL_BYOS` 运行该命令。 对于 SLES VM，使用 `--license-type` 参数 `SLES_BYOS` 运行该命令。

### <a name="cli-example-to-enable-the-benefit"></a>启用权益的 CLI 示例
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>禁用权益的 CLI 示例
若要禁用权益，请使用 `--license-type` 值 `None`：

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>对大量 VM 启用权益的 CLI 示例
若要对大量 VM 启用权益，可以在 Azure CLI 中的使用 `--ids` 参数：

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

下面的示例演示了两种获取资源 ID 列表的方法：一种方法在资源组级别，另一种在订阅级别。

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>在 VM 创建时应用 Azure 混合权益
除了将 Azure 混合权益应用于现有即用即付 VM，还可以在创建 VM 时调用它。 这样做有三重好处：
- 可以通过使用相同映像和过程来预配 PAYG 和 BYOS VM。
- 它支持在将来更改许可模式，而这是仅限 BYOS 的映像或是自带 VM 时无法实现的。
- 默认情况下，VM 将连接到 Red Hat 更新基础结构 (RHUI)，以确保其保持最新且处于安全状态。 可以在部署后随时更改更新后的机制。

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>检查 VM 的 Azure 混合权益状态
可以使用 Azure CLI 或使用 Azure 实例元数据服务查看 VM 的 Azure 混合权益状态。

### <a name="azure-cli"></a>Azure CLI

可以将 `az vm get-instance-view` 命令用于此用途。 在响应中查找 `licenseType` 字段。 如果 `licenseType` 字段存在并且值为 `RHEL_BYOS` 或 `SLES_BYOS`，则 VM 启用了权益。

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务

可以在 VM 本身中查询 Azure 实例元数据服务中的证明元数据，以确定 VM 的 `licenseType` 值。 `RHEL_BYOS` 或 `SLES_BYOS` 的 `licenseType` 值会指示 VM 启用了权益。 [详细了解证明元数据](./instance-metadata-service.md#attested-data)。

## <a name="compliance"></a>合规性

### <a name="red-hat"></a>Red Hat

对 RHEL 使用 Azure 混合权益的客户同意与 Azure 市场 RHEL 产品/服务关联的标准[法律条款](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf)和[隐私声明](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf)。

对 RHEL 使用 Azure 混合权益的客户有三个选项可用于向这些 VM 提供软件更新和补丁：

- [Red Hat 更新基础结构](../workloads/redhat/redhat-rhui.md)（默认选项）
- Red Hat Satellite Server
- Red Hat 订阅管理器

选择 RHUI 选项的客户可以继续使用 RHUI 作为 Azure 混合权益 RHEL VM 的主要更新源，而无需将 RHEL 订阅附加到这些 VM。 选择 RHUI 选项的客户需负责确保 RHEL 订阅合规性。

选择 Red Hat Satellite Server 或 Red Hat 订阅管理器的客户应删除 RHUI 配置，然后将已启用 Cloud Access 的 RHEL 订阅附加到其 Azure 混合权益 RHEL VM。  

有关 Azure 混合权益 RHEL VM 的 Red Hat 订阅合规性、软件更新和源的详细信息，请参阅[关于将 RHEL 订阅与 Azure 混合权益配合使用的 Red hat 文章](https://access.redhat.com/articles/5419341)。

### <a name="suse"></a>SUSE

使用 Azure 混合权益的客户需要将云更新基础结构移动到以下三个选项之一，为这些 VM 提供软件更新和补丁：
- [SUSE 客户中心](https://scc.suse.com)
- SUSE 管理器
- SUSE 存储库镜像工具 (RMT) 

若要对 SLES VM 使用 Azure 混合权益，以及有关从 SLES PAYG 迁移到 BYOS 或从 SLES BYOS 迁移到 PAYG 的信息，请参阅 [SUSE Linux Enterprise 和 Azure 混合权益](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/)。 

## <a name="azure-hybrid-benefit-on-reserved-instances"></a>预留实例的 Azure 混合权益 

Azure 预留项（Azure 虚拟机预留实例）通过承诺多种产品的一年期或三年期计划，帮助你节省资金。 在[此处](../../cost-management-billing/reservations/save-compute-costs-reservations.md)了解有关预留实例的更多信息。 Azure 混合权益适用于[虚拟机预留实例 (RI)](../../cost-management-billing/reservations/save-compute-costs-reservations.md#charges-covered-by-reservation)。 

这意味着，如果你已使用 RI 按折扣费率支付计算成本，则可对 RHEL 及其中 SUSE 的许可成本应用 AHB 权益。 将 AHB 权益应用于 RI 实例的步骤与将其应用于常规 VM 的步骤完全相同。
![RI 的 AHB](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>如果已在 Azure 市场为 RHEL 或 SUSE PAYG 软件购买预留服务，请等到预留期结束后再使用 Azure 混合权益。


## <a name="frequently-asked-questions"></a>常见问题
问：我是否可以将 `RHEL_BYOS` 许可证类型用于 SLES 映像，或是反过来操作？

答：否，不能。 尝试输入未与 VM 上运行的分发版正确匹配的许可证类型不会更新任何计费元数据。 但如果意外输入错误的许可证类型，则再次将 VM 更新为正确的许可证类型仍会启用权益。

问：我已向 Red Hat Cloud Access 注册，但仍无法对 RHEL VM 启用权益。*我该怎样做？*

答：Red Hat Cloud Access 订阅注册可能需要一些时间才能从 Red Hat 传播到 Azure。 如果在一个工作日之后仍看到该错误，请联系 Microsoft 支持部门。

问：我使用 RHEL BYOS“黄金级映像”部署了 VM。是否可以将这些映像上的计费从 BYOS 转换为 PAYG？

答：否，不能。 Azure 混合权益仅支持在即用即付映像上进行转换。

问：我已经将自己的 RHEL 或 SLES 映像从本地（通过 Azure Migrate、Azure Site Recovery 或其他方式）上传到了 Azure。我是否可以将对这些映像的计费从 BYOS 转换为 PAYG？

答：否，不能。 Azure 混合权益功能目前仅适用于 Azure 市场中的 RHEL 和 SLES 映像。 

问：我已经将自己的 RHEL 或 SLES 映像从本地（通过 Azure Migrate、Azure Site Recovery 或其他方式）上传到了 Azure。我是否需要执行某些操作才能享受 Azure 混合权益？

答：否，不需要。 上传的 RHEL 或 SLES 映像已被视为 BYOS，你只需为 Azure 基础结构成本付费。 你需要负责 RHEL 订阅成本，正如在本地环境中一样。 

问：我是否可以在从 Azure 市场 RHEL 和 SLES SAP 映像部署的 VM 上使用 Azure 混合权益？

答：可以。 可以将 `RHEL_BYOS` 许可证类型用于 RHEL VM，将 `SLES_BYOS` 用于转换为从 Azure 市场 RHEL 和 SLES SAP 映像部署的 VM。

问：是否可以在 RHEL 和 SLES 的虚拟机规模集上使用 Azure 混合权益？

答：可以，RHEL 和 SLES 的虚拟机规模集 Azure 混合权益适用于所有用户。 用户可在[此处](../../virtual-machine-scale-sets/azure-hybrid-benefit-linux.md)了解有关此权益及其使用方法的更多信息。 

问：是否可以在 RHEL 和 SLES 的预留实例上使用 Azure 混合权益？

答：可以，RHEL 和 SLES 预留实例的 Azure 混合权益适用于所有用户。 用户可在[此处](#azure-hybrid-benefit-on-reserved-instances)了解有关此权益及其使用方法的更多信息。

问：是否可以在为 RHEL 上的 SQL Server 映像部署的虚拟机上使用 Azure 混合权益？

答：否，不能。 没有为这些虚拟机提供支持的计划。

问：是否可以在 RHEL 虚拟数据中心订阅上使用 Azure 混合权益？

答：否，不能。 Azure 上完全不支持 VDC，包括 AHB。  
 

## <a name="common-problems"></a>常见问题
本部分列出了可能会遇到的常见问题以及缓解措施。

| 错误 | 缓解措施 |
| ----- | ---------- |
| “操作无法完成，因为我们的记录显示你尚未在 Azure 订阅上成功启用 Red Hat Cloud Access….” | 若要将权益用于 RHEL VM，必须先[向 Red Hat Cloud Access 注册 Azure 订阅](https://access.redhat.com/management/cloud)。

## <a name="next-steps"></a>后续步骤
* [了解如何使用 Azure CLI 创建和更新 VM 以及添加许可证类型（RHEL_BYOS、SLES_BYOS）以获得 Azure 混合权益](/cli/azure/vm)
* RHEL 和 SLES 的虚拟机规模集 Azure 混合权益适用于所有用户。 用户可在[此处](../../virtual-machine-scale-sets/azure-hybrid-benefit-linux.md)了解有关此权益及其使用方法的更多信息。
