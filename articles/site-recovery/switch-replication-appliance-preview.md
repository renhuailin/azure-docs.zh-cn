---
title: 在 Azure Site Recovery（预览版）中切换复制设备
description: 本文介绍了在 Azure Site Recovery（预览版）中将 VMware VM 复制到 Azure 时，如何在不同的复制设备之间实现切换
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: f95ed991833a9f6ed1b0635a8678e393f152cb56
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446630"
---
# <a name="switch-azure-site-recovery-replication-appliance"></a>切换 Azure Site Recovery 复制设备

>[!NOTE]
> 本文中的信息适用于 Azure Site Recovery（预览版）。

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 和物理服务器到 Azure 的灾难恢复时，需要[创建和部署本地 Azure Site Recovery 复制设备](deploy-vmware-azure-replication-appliance-preview.md)。 有关复制设备的详细信息，请参阅[体系结构](vmware-azure-architecture-preview.md)。 可以根据组织的容量需求创建并使用多个复制设备。

本文介绍了在各个复制设备之间实现切换的方式。

## <a name="application-resilience"></a>应用程序复原能力

通常情况下，在经典体系结构中，如需维护配置服务器的复原能力，建议采取的措施是定期手动备份计算机中的内容。 这一过程十分繁琐，同时也容易出错及遗漏内容。  

此预览版提供了更好的方法，可增强设备的复原能力。 如果复制设备发生故障，或者需要平衡设备上运行的计算机，只需启动另一台复制设备，并将先前设备上的所有计算机切换到新设备即可。


## <a name="consideration-for-switching-replication-appliance"></a>切换复制设备的注意事项

如遇以下情况，可切换复制设备：

- 如果当前 Azure Site Recovery 复制设备发生故障，即其所有组件均无检测信号，则需要执行切换操作。
  - 仅当设备的所有组件都没有检测信号时，才将其视作发生故障。 即使仅有一个组件存在检测信号，也无法执行切换操作。
  - 如果当前的设备发生故障，则需要再次提供凭据，以访问尝试切换的计算机。 如果正在执行负载均衡，且当前设备仍处于非严重状态，则系统会自动选择凭据，在切换到其他设备时便无需重新输入凭据。
- 如果需要对复制设备执行负载均衡，则可能需要执行切换操作。
- 如果出于使设备实现负载均衡的意向而尝试执行切换操作，则当前设备的所有组件都应处于正常或警告状态。 即使只有一个组件缺少检测信号，都无法完成切换操作。
-  请确保切换操作的目标设备处于正常或警告状态，以便成功完成此操作。
-  在对另一台设备执行切换操作时，只能选择从本地复制到 Azure 的计算机。  


## <a name="switch-a-replication-appliance"></a>切换复制设备

以下是具体示例：复制设备 1 (RA1) 已处于严重状态，并且你希望将受保护的工作负荷移动到处于正常状态的复制设备 2 (RA2)。 或者，你希望将 RA1 中的工作负荷切换到 RA2，以便执行任何负载均衡或组织级别的更改。

请按照以下步骤切换设备：

1. 转到“Site Recovery 基础结构”边栏选项卡，并选择“ASR 复制设备”。

   页面上将显示可用设备及其运行状况的列表。 例如，此处的 RA2 处于正常状态。

   ![正常复制装置列表](./media/switch-replication-appliance-preview/appliance-health.png)

2. 选择复制设备 (RA1)，然后选择“切换设备”。

   ![选择要切换的复制设备](./media/switch-replication-appliance-preview/select-switch-appliance.png)


3. 在“选择计算机”下，选择想要故障转移到另一台复制设备 (RA2) 的计算机。 选择“**下一页**”。

   >[!NOTE]
   > 只有受当前设备保护的计算机才会在列表中显示。 已故障转移的计算机不会出现在此处  

    ![选择要切换的计算机](./media/switch-replication-appliance-preview/select-machines.png)

4. 在“源设置” ****  页下，为每台选定的计算机选择不同的复制设备。

   ![复制设备的源设置](./media/switch-replication-appliance-preview/source-settings.png)

   >[!NOTE]
   > 如果当前的设备发生故障，则必须选择凭据才能访问计算机。 否则，将禁用此字段。

5. 查看所选内容，然后单击“切换设备”。

   ![查看复制设备](./media/switch-replication-appliance-preview/review-switch-appliance.png)

   重新同步完成后，对于移动到新设备的 VM，其复制状态将变为“正常”。

## <a name="next-steps"></a>后续步骤
设置 [VMware VM](vmware-azure-tutorial.md) 到 Azure 的灾难恢复。
