---
title: Azure 混合权益和 Linux Vm
description: 了解 Azure 混合权益如何帮助你在 Azure 上运行的 Linux 虚拟机上节省资金。
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 990c396c859477c83e0ef421659a299920ee0c35
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209128"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>如何将 Azure 混合权益适用于 Linux 虚拟机

Azure 混合权益是一种授权权益，可帮助你大大降低 Red Hat Enterprise Linux 云中运行 (RHEL) 和 SUSE Linux Enterprise Server (SLES) 虚拟机 (虚拟机的成本。 利用此权益，只需支付 VM 的基础结构成本，因为 RHEL 或 SLES 订阅涵盖软件费用。 该权益适用于所有 RHEL 和 SLES Marketplace 即用即付 (PAYG) 映像。

适用于 Linux Vm 的 Azure 混合权益现已公开发布。

## <a name="benefit-description"></a>权益说明

通过 Azure 混合权益，你可以通过将 Azure 上的现有 RHEL 和 SLES PAYG Vm 转换为 Azure 来将本地 RHEL 和 SLES 服务器迁移到 Azure，从而 (BYOS) 计费。 通常，从 Azure 上的 PAYG 映像部署的 Vm 将对基础结构费用和软件费用收费。 使用 Azure 混合权益，可以在不重新部署的情况下将 PAYG Vm 转换为 BYOS 计费模型，因此可以避免任何停机风险。

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure 混合权益 Linux Vm 的成本可视化。":::

启用 RHEL 或 SLES VM 权益后，你将不再需要支付 PAYG VM 上通常会产生的额外软件费用。 相反，你的 VM 会开始支付 BYOS 费用，其中只包括计算硬件费用和不收取软件费用。

还可以选择将启用了权益的 VM 转换回 PAYG 计费模型。

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>适用于 Linux Vm 的 Azure 混合权益资格范围

Azure 混合权益适用于 Azure Marketplace 中的所有 RHEL 和 SLES PAYG 映像。 此权益尚不适用于 RHEL 或 SLES BYOS 映像或 Azure Marketplace 中的自定义映像。

如果已在 Linux Vm 中使用了权益，则保留实例、Azure 专用主机实例和 SQL 混合权益不适用于 Azure 混合权益。

## <a name="get-started"></a>入门

### <a name="red-hat-customers"></a>Red Hat 客户

RHEL Azure 混合权益适用于满足这两个条件的 Red Hat 客户：

- 具有可在 Azure 中使用的有效或未使用的 RHEL 订阅
- 已在 Azure 中启用了一个或多个使用 [Red Hat 云访问](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 计划的订阅

> [!IMPORTANT]
> 确保已在 [云访问](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 计划中启用了正确的订阅。

开始使用 Red Hat 权益：

1. 启用一个或多个合格的 RHEL 订阅，以便在 Azure 中使用 [Red Hat 云访问客户界面](https://access.redhat.com/management/cloud)。

   然后，你在 Red Hat 云访问支持过程中提供的 Azure 订阅将允许使用 Azure 混合权益功能。
1. 将 Azure 混合权益应用到现有的 RHEL PAYG Vm 以及从 Azure Marketplace PAYG 映像部署的任何新 RHEL Vm。 您可以使用 Azure 门户或 Azure CLI 来启用此权益。
1. 按照建议的 [后续步骤](https://access.redhat.com/articles/5419341) 配置 rhel vm 的更新源和 rhel 订阅符合性指南。


### <a name="suse-customers"></a>SUSE 客户

开始使用 SUSE 权益：

1. 向 SUSE 公有云计划注册。
1. 通过 Azure 门户或 Azure CLI 将权益应用于新创建的或现有的 Vm。
1. 使用单独的更新源注册接收权益的 Vm。

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>在 Azure 门户中启用和禁用权益

可以通过访问左侧的 " **配置** " 选项并按照此处的步骤来启用现有 vm 的权益。 在创建 VM 时，可以在新 Vm 上启用权益。

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure 门户示例，为现有 VM 启用权益：
1. 访问 [Microsoft Azure 门户](https://portal.azure.com/)
1. 请在门户中中转到 "创建虚拟机" 页面。
 ![创建 VM 时 AHB](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. 单击复选框以启用 AHB 转换并使用云访问许可证。
 !["创建 VM 时 AHB" 复选框](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. 按照下一组说明创建虚拟机
1. 检查 " **配置** " 边栏选项卡，会看到 "已启用" 选项。 
![创建后的 AHB 配置边栏选项卡](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure 门户示例，以便在创建 VM 期间启用此权益：
1. 访问 [Microsoft Azure 门户](https://portal.azure.com/)
1. 打开要应用转换的虚拟机页。
1. 请在左侧 **配置** 选项。 你将看到 "授权" 部分。 若要启用 AHB 转换，请选中 "是" 单选按钮，并选中 "确认" 复选框。
![创建后的 AHB 配置边栏选项卡](./media/azure-hybrid-benefit/create-configuration-blade.png)


>[!NOTE]
> 如果已创建 **自定义快照** 或 **共享映像 (** RHEL 或 SLES PAYG Marketplace 映像的 SIG) ，则只能使用 Azure CLI 启用 Azure 混合权益。 这是已知的限制，当前没有用于在 Azure 门户上提供此功能的时间线。



## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>在 Azure CLI 中启用和禁用权益

你可以使用 `az vm update` 命令来更新现有的 vm。 对于 RHEL Vm，请使用的参数运行命令 `--license-type` `RHEL_BYOS` 。 对于 SLES Vm，请使用的参数运行命令 `--license-type` `SLES_BYOS` 。

### <a name="cli-example-to-enable-the-benefit"></a>启用权益的 CLI 示例
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>用于禁用权益的 CLI 示例
若要禁用权益，请使用 `--license-type` 值 `None` ：

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>启用大量 Vm 权益的 CLI 示例
若要在大量 Vm 上启用权益，可以使用 `--ids` Azure CLI 中的参数：

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

下面的示例演示了两种获取资源 Id 列表的方法：一个在资源组级别，另一个在订阅级别。

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>在 VM 创建时应用 Azure 混合权益
除了将 Azure 混合权益应用到现有的即用即付 Vm，你还可以在创建 VM 时调用它。 这样做的好处是 threefold：
- 可以通过使用相同的映像和过程来预配 PAYG 和 BYOS Vm。
- 它支持未来的授权模式更改，不能用于 BYOS 映像或自带 VM。
- 默认情况下，VM 将连接到 Red Hat 更新基础结构 (RHUI) ，以确保其保持最新和安全。 可以在部署后随时更改更新的机制。

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>检查 VM 的 Azure 混合权益状态
可以通过使用 Azure CLI 或使用 Azure 实例元数据服务查看 VM 的 Azure 混合权益状态。

### <a name="azure-cli"></a>Azure CLI

你可以使用 `az vm get-instance-view` 命令来实现此目的。 查找 `licenseType` 响应中的字段。 如果该 `licenseType` 字段存在并且值为 `RHEL_BYOS` 或 `SLES_BYOS` ，则 VM 启用了权益。

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务

可以从 VM 本身中查询 Azure 实例元数据服务中的证明元数据，以确定 VM 的 `licenseType` 值。 `licenseType`或的值 `RHEL_BYOS` `SLES_BYOS` 将指示 VM 启用了权益。 [详细了解证明元数据](./instance-metadata-service.md#attested-data)。

## <a name="compliance"></a>合规性

### <a name="red-hat"></a>Red Hat

使用 RHEL Azure 混合权益的客户同意与 Azure Marketplace RHEL 产品/服务相关的标准 [法律条款](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) 和 [隐私声明](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) 。

使用 RHEL Azure 混合权益的客户有三个选项可用于向这些 Vm 提供软件更新和修补程序：

- [Red Hat 更新基础结构](../workloads/redhat/redhat-rhui.md) (默认选项) 
- Red Hat 卫星服务器
- Red Hat 订阅管理器

选择 RHUI 选项的客户可以继续使用 RHUI 作为 Azure 混合权益 RHEL Vm 的主要更新源，而无需将 RHEL 订阅附加到这些 Vm。 选择 RHUI 选项的客户负责确保 RHEL 订阅符合性。

选择 Red Hat 卫星 Server 或 Red Hat 订阅管理器的客户应删除 RHUI 配置，然后将已启用云访问权限的 RHEL 订阅附加到其 Azure 混合权益 RHEL Vm。  

有关 Azure 混合权益 RHEL Vm 的 Red Hat 订阅相容性、软件更新和源的详细信息，请参阅 [关于将 RHEL 订阅与 Azure 混合权益配合使用的 Red hat 文章](https://access.redhat.com/articles/5419341)。

### <a name="suse"></a>SUSE

若要为 SLES Vm 使用 Azure 混合权益，以及从 SLES PAYG 迁移到 BYOS 或从 SLES BYOS 迁移到 PAYG 的信息，请参阅 [SUSE Linux Enterprise and Azure 混合权益](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/)。 

## <a name="frequently-asked-questions"></a>常见问题
*问：我能否将许可证类型用于 `RHEL_BYOS` SLES 映像，反之亦然？*

答：不可以。 尝试输入不正确地匹配 VM 上运行的分发的许可证类型将不会更新任何计费元数据。 但如果你意外地输入了错误的许可证类型，则再次将 VM 更新为正确的许可证类型将仍然会启用此权益。

*问：我已注册 Red Hat 云访问，但仍无法启用我的 RHEL Vm 权益。我该怎么办？*

答： Red Hat 云访问订阅注册可能需要一些时间才能从 Red Hat 传播到 Azure。 如果在一个工作日内仍看到此错误，请联系 Microsoft 支持部门。

*问：我已使用 RHEL BYOS "黄金映像" 部署了 VM。是否可以将这些映像的帐单从 BYOS 转换为 PAYG？*

答：不可以。 Azure 混合权益仅支持在即用即付映像上进行转换。

*问：我通过 Azure Migrate、Azure Site Recovery 或) 到 Azure 从本地 (上传了我自己的 RHEL 映像。是否可以将这些映像的帐单从 BYOS 转换为 PAYG？*

答：不可以。 Azure 混合权益功能目前仅适用于 Azure Marketplace 中的 RHEL 和 SLES 映像。 

*问：我通过 Azure Migrate、Azure Site Recovery 或) 到 Azure 从本地 (上传了我自己的 RHEL 映像。是否需要执行任何操作才能受益于 Azure 混合权益？*

答：不可以。 你上载的 RHEL 映像已被视为 BYOS，你只需为 Azure 基础结构成本付费。 你需要负责 RHEL 订阅费用，就像在本地环境中一样。 

*问：是否可以在从 Azure Marketplace RHEL 和 SLES SAP 映像部署的 Vm 上使用 Azure 混合权益？*

答：可以。 你可以使用 RHEL Vm 的许可证类型 `RHEL_BYOS` ，并 `SLES_BYOS` 为从 AZURE Marketplace RHEL 和 SLES SAP 映像部署的 vm 转换。

*问：是否可以在 RHEL 和 SLES 的虚拟机规模集上使用 Azure 混合权益？*

答：不可以。 虚拟机规模集目前不在 RHEL 和 SLES Azure 混合权益范围内。

*问：我是否可以在 RHEL 和 SLES 的预订实例上使用 Azure 混合权益？*

答：不可以。 保留实例目前不在 RHEL 和 SLES Azure 混合权益范围内。

*问：是否可以在部署用于 SQL Server RHEL 映像的虚拟机上使用 Azure 混合权益？*

答：不可以。 没有用于支持这些虚拟机的计划。

*问：我是否能在我的 RHEL Virtual Data Center 订阅上使用 Azure 混合权益？*

答：不可以。 Azure 根本不支持 VDC，包括 AHB。  
 

## <a name="common-problems"></a>常见问题
本部分列出了你可能会遇到的常见问题和缓解措施。

| 错误 | 缓解措施 |
| ----- | ---------- |
| "该操作无法完成，因为我们的记录显示你尚未在 Azure 订阅上成功启用 Red Hat 云访问 ..." | 若要将此权益用于 RHEL Vm，必须先 [使用 Red Hat 云访问权限注册 Azure 订阅](https://access.redhat.com/management/cloud)。

## <a name="next-steps"></a>后续步骤
* [了解如何使用 Azure 混合权益创建和更新 Vm，并 (RHEL_BYOS，SLES_BYOS) Azure CLI](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)