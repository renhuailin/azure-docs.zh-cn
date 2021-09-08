---
title: 创建放置策略（预览版）
description: 了解如何在 Azure VMware 解决方案中创建放置策略，以通过 Azure 门户控制群集中主机上的虚拟机 (VM) 放置。
ms.topic: how-to
ms.date: 8/18/2021
ms.openlocfilehash: 85146ce86dea0d3cfa7397cdaae6fefc8cf4a23b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967538"
---
# <a name="create-a-placement-policy-in-azure-vmware-solution-preview"></a>在 Azure VMware 解决方案中创建放置策略（预览版）

>[!IMPORTANT]
>Azure VMware 解决方案放置策略（预览版）目前为预览版。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 若要使用预览功能，[需要同时注册 DRS 放置策略和提前访问功能 ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade)。  搜索并选择功能，然后选择“注册”。

在 Azure VMware 解决方案中，私有云中的群集是托管资源。 因此，cloudadmin 角色无法从 vSphere 客户端对群集进行某些更改，包括管理分布式资源计划程序 (DRS) 规则。

放置策略功能适用于所有 Azure VMware 解决方案区域。  使用放置策略可以通过 Azure 门户控制群集中主机上的虚拟机 (VM) 放置。 创建放置策略时，它会在指定 vSphere 群集中包含 DRS 规则。 它还包含其他逻辑，用于实现与 Azure VMware 解决方案操作的互操作性。

放置策略至少具有五个必需组件： 

- 名称 - 定义策略的名称，受 [Azure 资源](../azure-resource-manager/management/resource-name-rules.md)的命名约束所约束。

- 类型 - 定义要应用于策略中包含的资源的控件类型。

- 群集 - 为策略定义群集。 放置策略的范围是 vSphere 群集，因此，只有来自同一群集的资源才能属于同一放置策略。

- 状态 - 定义启用还是禁用策略。 在某些情况下，创建冲突的规则时，可能会自动禁用策略。 有关详细信息，请参阅以下[注意事项](#considerations)。

- 虚拟机 - 为策略定义 VM 和主机。 根据创建的规则类型，策略可能要求指定一些 VM 和主机。 有关详细信息，请参阅[放置策略类型](#placement-policy-types)。


## <a name="prerequisites"></a>先决条件

- 必须对私有云具有“参与者”级别访问权限才能管理放置策略。

- DRS 放置策略和提前访问 [功能已注册](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade)。


## <a name="placement-policy-types"></a>放置策略类型

### <a name="vm-vm-policies"></a>VM-VM 策略

VM-VM 策略指定所选 VM 是应在同一主机上运行还是保留在单独的主机上。  除了为策略选择名称和群集之外，VM-VM 策略还要求至少选择两个 VM 进行分配。 此策略类型不需要或不允许分配主机。

- VM-VM 相关性策略指示 DRS 尝试使指定 VM 一起处于同一主机上。 例如，它会由于性能原因而十分有用。

- VM-VM 反相关性策略指示 DRS 尝试将指定 VM 彼此隔离在单独的主机上。 在一个主机的问题不影响同一策略中的多个 VM 的方案中，它十分有用。


### <a name="vm-host-policies"></a>VM-主机策略

VM-主机策略指定所选 VM 是否可以在所选主机上运行。  为了避免对平台管理的操作（如主机维护模式和主机替换）产生干扰，Azure VMware 解决方案中的 VM-主机策略始终优先（也称为“应该遵守”的规则）。 因此，VM-主机策略[在某些情况下可能不会得到遵守](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-793013E2-0976-43B7-9A00-340FA76859D0.html)。 有关详细信息，请参阅下面的[监视策略的操作](#monitor-the-operation-of-a-policy)。

某些平台操作会动态更新 VM-主机策略中定义的主机列表。 例如，删除属于放置策略成员的主机时，如果有多个主机属于该策略，则删除该主机。 此外，如果主机是策略的一部分，并且需要作为平台管理的操作的一部分进行替换，则使用新主机动态更新策略。

除了为策略选择名称和群集外，VM-主机策略还要求至少选择一个 VM 和一个主机以分配给策略。

- VM-主机相关性策略指示 DRS 尝试在定义的主机上运行指定 VM。

- VM-主机反相关性策略指示 DRS 尝试在定义的主机之外的主机上运行指定 VM。


## <a name="considerations"></a>注意事项

### <a name="cluster-scale-in"></a>群集横向缩减

Azure VMware 解决方案在执行群集横向缩减操作时，会尝试防止发生某些 DRS 规则冲突。

不能从 VM-主机策略中删除最后一个主机。 但是，如果需要从策略中删除最后一个主机，则可以在从群集中删除主机之前将另一个主机添加到策略，从而修正此问题。 或者，可以在删除主机之前删除放置策略。

VM-VM 反相关性策略中的 VM 数不能超过群集中的主机数。 如果删除主机会导致群集中的主机数少于 VM 数，则会收到阻止操作的错误。 可以先从规则中删除 VM，然后再从群集中删除主机，从而修正此问题。


### <a name="rule-conflicts"></a>规则冲突

如果在创建 VM-VM 策略时检测到 DRS 规则冲突，则会导致按照标准 [VMware DRS 规则行为](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-69C738B6-5FC8-4189-9CB5-DD90A5A05979.html)，以禁用状态创建该策略。 有关查看规则冲突的详细信息，请参阅下面的[监视策略的操作](#monitor-the-operation-of-a-policy)。



## <a name="create-a-placement-policy"></a>创建放置策略

所创建的策略数量没有定义限制。 但是，创建的放置约束越多，vSphere DRS 有效地移动群集中的虚拟机并提供工作负载所需的资源的挑战性就越大。      

请确保查看[策略类型](#placement-policy-types)的要求。

1. 在 Azure VMware 解决方案私有云中的“管理”下，选择“放置策略” > “创建”  。

   >[!TIP]
   >还可以从放置策略概述窗格中选择群集，然后选择“创建”。
   >
   >:::image type="content" source="media/placement-policies/create-placement-policy-cluster.png" alt-text="显示用于创建放置策略的替代选项的屏幕截图。":::



   :::image type="content" source="media/placement-policies/create-placement-policy.png" alt-text="显示如何启动创建 VM-VM 放置策略的过程的屏幕截图。" lightbox="media/placement-policies/create-placement-policy.png":::


1. 提供描述性名称，选择策略类型，然后选择创建策略的群集。 然后选择“启用”。

   >[!WARNING]
   >如果禁用策略，则创建策略和基础 DRS 规则，但在启用策略之前会忽略策略操作。 

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-host-affinity-1.png" alt-text="显示放置策略选项的屏幕截图。" lightbox="media/placement-policies/create-placement-policy-vm-host-affinity-1.png":::   

1. 如果选择“VM-主机相关性”或“VM-主机反相关性”作为类型，请选择“+ 添加主机”和策略中包含的主机  。 可以选择多个主机。

   >[!NOTE]
   >选择主机窗格显示与主机关联的 VM-主机策略数，以及这些关联策略中包含的 VM 总数。
   >
   >:::image type="content" source="media/placement-policies/hosts-associated-policies-vms.png" alt-text="显示和主机关联的 VM-主机策略的数量以及包含在这些关联策略中的 VM 数量的屏幕截图。":::


1. 选择“+ 添加虚拟机”和要包含在策略中的 VM。 可以选择多个 VM。

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-2.png" alt-text="显示要选择的 VM 列表的屏幕截图。":::
   
   >[!NOTE]
   >选择主机窗格显示与主机关联的 VM-主机策略数，以及这些关联策略中包含的 VM 总数。 

1. 添加完所需 VM 后，选择“添加虚拟机”。 

1. 选择“下一步: 审阅并创建”以审阅策略。 

1. 选择“创建策略”。  如果要进行更改，请选择“返回: 基本信息”。

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-3.png" alt-text="显示创建之前的放置策略设置的屏幕截图。":::

1. 创建放置策略后，选择“刷新”可在列表中查看它。

   :::image type="content" source="media/placement-policies/create-placement-policy-8.png" alt-text="显示创建后为“已启用”状态的放置策略。" lightbox="media/placement-policies/create-placement-policy-8.png":::



## <a name="edit-a-placement-policy"></a>编辑放置策略

可以更改策略的状态、添加新资源或取消分配现有资源。

### <a name="change-the-policy-state"></a>更改策略状态

可以将策略的状态更改为“已启用”或“已禁用” 。 

1. 在 Azure VMware 解决方案私有云中的“管理”下，选择“放置策略” 。

1. 对于要编辑的策略，选择“更多”(...)，然后选择“编辑” 。

   >[!TIP]
   >可以通过从“设置”下拉列表中选择“禁用”，在放置策略概述中禁用策略。 无法从“设置”下拉列表启用策略。

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="显示如何编辑放置策略的屏幕截图。" lightbox="media/placement-policies/edit-placement-policy.png":::

1.  如果策略已启用，但是你要禁用它，请选择“已禁用”，然后在确认消息中选择“已禁用” 。 否则，如果策略已禁用，并且你要启用它，请选择“启用”。

1.  选择“审阅 + 更新”。 
 
1.  审阅所做的更改，然后选择“更新策略”。 如果要进行更改，请选择“返回: 基本信息”。


### <a name="update-the-resources-in-a-policy"></a>更新策略中的资源

可以将新资源（例如 VM 或主机）添加到策略或删除现有资源。 

1. 在 Azure VMware 解决方案私有云中的“管理”下，选择“放置策略” 。

1. 对于要编辑的策略，选择“更多”(...)，然后选择“编辑” 。

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="显示如何在放置策略中编辑资源的屏幕截图。" lightbox="media/placement-policies/edit-placement-policy.png":::

   - 若要删除现有资源，请选择一个或多个要删除的资源，然后选择“取消分配”。 

      :::image type="content" source="media/placement-policies/edit-placement-policy-unassign.png" alt-text="显示如何从放置策略中删除现有资源的屏幕截图。":::

   - 若要添加新资源，请选择“编辑虚拟机”或“编辑主机”，选择要添加的资源，然后选择“保存”  。 

1. 选择“下一步: 审阅并更新”。 

1. 审阅所做的更改，然后选择“更新策略”。  如果要进行更改，请选择“返回: 基本信息”。



## <a name="delete-a-policy"></a>删除策略

可以删除放置策略及其对应的 DRS 规则。 

1. 在 Azure VMware 解决方案私有云中的“管理”下，选择“放置策略” 。

1. 对于要编辑的策略，选择“更多”(...)，然后选择“删除” 。

   :::image type="content" source="media/placement-policies/delete-placement-policy.png" alt-text="显示如何删除放置策略的屏幕截图。" lightbox="media/placement-policies/delete-placement-policy.png":::

1. 在确认消息中选择“删除”。



## <a name="monitor-the-operation-of-a-policy"></a>监视策略的操作

使用 vSphere 客户端可监视放置策略对应 DRS 规则的操作。 

作为 cloudadmin 角色的持有者，可以在群集“配置”选项卡上的“VM/主机规则”下查看（但不能编辑）放置策略创建的 DRS 规则。 通过该方法可以查看其他信息，例如 DRS 规则是否处于冲突状态。

此外，还可以从群集的“监视”选项卡监视各种 DRS 规则操作，例如建议和错误。


## <a name="faqs"></a>常见问题

### <a name="are-these-the-same-as-drs-affinity-rules"></a>它们是否与 DRS 相关性规则相同？
“是”和“否”。 尽管 vSphere DRS 可实现当前的一组策略，但我们已简化了体验。 修改 VM 组和主机组是一项繁琐的操作，特别是因为主机本质上是短暂的，可以在云环境中替换。 由于可以在本地环境的 vSphere 库存中替换主机，因此 vSphere 管理员必须修改主机组，以确保所需的 VM-主机放置约束仍然有效。 Azure VMware 解决方案中的放置策略会在旋转或更改主机时更新主机组。 同样，如果在群集中进行缩放，主机组会自动更新（如果适用）。 这可消除管理客户主机组产生的开销。


### <a name="as-this-is-an-existing-functionality-available-in-vcenter-why-cant-i-use-it-directly"></a>这是 vCenter 中可用的现有功能，为什么我无法直接使用它？ 

Azure VMware 解决方案在 Azure 中提供 VMware 私有云。 在此托管 VMware 基础结构中，Microsoft 在私有云中管理群集、主机、数据存储和分布式虚拟交换机。 同时，租户负责管理在专用云上部署的工作负载。 因此，管理私有云的租户[没有与本地部署中的 VMware 管理员可用的一组相同特权](concepts-identity.md)。 

此外，vSphere 特权中缺乏所需的粒度，这对在私有云上管理工作负载的放置带来了一些挑战。 例如，通常在本地使用的 vSphere DRS 规则定义相关性和反相关性规则，在 VMware 云环境中不能按原样使用，因为其中一些规则可能会阻止在私有云上进行日常操作。 放置策略提供了一种使用 Azure VMware 解决方案门户定义这些规则的方法，从而避开了使用 DRS 规则的需要。 再加上简化的体验，它们还确保规则不会影响日常基础结构维护和操作活动。 


###  <a name="what-caveats-should-i-know-about"></a>我应该了解哪些注意事项？

VM-主机“必须”规则不受支持，因为这些规则会阻止维护操作。 

VM-主机“应该”规则是首选规则，其中 vSphere DRS 尝试尽可能适应规则。 有时，vSphere DRS 让 vMotion VM 受限于 VM-主机“应该”规则以确保工作负载获得所需的资源。 这是标准的 vSphere DRS 行为，且放置策略功能不会更改基础 vSphere DRS 行为。

如果创建冲突规则，则这些冲突可能会显示在 vCenter 上，并且新定义的规则可能不会生效。 这是标准的 vSphere DRS 行为，可以在 vCenter 中观察到该行为的日志。
