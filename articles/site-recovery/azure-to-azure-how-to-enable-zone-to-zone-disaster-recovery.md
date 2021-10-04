---
title: 为 Azure 虚拟机启用局部区域间的灾难恢复
description: 本文介绍何时以及如何对 Azure 虚拟机使用局部区域间的灾难恢复。
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 7d6d2c3d4a4e1f085d19ff4ffe407ed78f79c325
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708598"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>在可用性区域之间启用 Azure VM 灾难恢复

本文介绍如何将 Azure 虚拟机从一个可用性区域复制、故障转移和故障回复到同一 Azure 区域中的另一个可用性区域。

>[!NOTE]
>
>- 对区域到区域灾难恢复的支持目前仅限于以下区域：东南亚、日本东部、韩国中部、澳大利亚东部、印度中部、英国南部、西欧、北欧、挪威东部、法国中部、加拿大中部、美国中部、美国中南部、美国东部、美国东部 2、美国西部 2、巴西南部和美国西部 3。  
>- 当客户使用局部区域间的灾难恢复时，Site Recovery 不会将客户数据移出其部署所在的 Azure 区域，也不会将数据存储在该 Azure 区域的外部。 如果客户愿意，可以从其他地区选择恢复服务保管库。 恢复服务保管库包含元数据，但不包含实际的客户数据。

Site Recovery 服务通过在计划内和计划外服务中断期间使业务应用保持正常运行，来帮助实现业务连续性并实施灾难恢复策略。 它是建议的灾难恢复选项，可在发生区域性服务中断时使应用程序保持正常运行。

可用性区域是 Azure 区域中独特的物理位置。 每个局部区域包含一个或多个数据中心。 

如果要将 VM 移到其他 Azure 区域中的可用性区域，请[查看此文](../resource-mover/move-region-availability-zone.md)。

## <a name="using-availability-zones-for-disaster-recovery"></a>使用可用性区域实现灾难恢复 

通常，可用性区域用于在高可用性配置中部署 VM。 这些局部区域可能相互过于靠近，以致在发生自然灾害时无法充当灾难恢复解决方案。

但在某些情况下，可以利用可用性区域实现灾难恢复：

- 许多制定了都市灾难恢复策略并在本地托管应用程序的客户在将应用程序迁移到 Azure 后，有时可能想要效仿此策略。 这些客户承认在发生大规模物理灾难时都市灾难恢复策略可能不起作用这一事实，并且接受这一风险。 对于此类客户，可以使用局部区域间的灾难恢复作为灾难恢复选项。

- 其他许多客户采用复杂的网络基础结构，由于相关的成本和复杂性，他们不希望在次要区域重新创建网络基础结构。 局部区域间的灾难恢复利用可用性区域之间的冗余网络概念，因此降低了复杂性，并大大简化了配置。 此类客户追求简单性，他们可以使用可用性区域实现灾难恢复。

- 某些 Azure 区域（例如东南亚）在同一法律管辖区内不存在配对的 Azure 区域，而由于应用程序和数据不会跨国界移动，因此局部区域间的灾难恢复可以充当实际上的灾难恢复解决方案，因为它有助于确保法律合规性。 

- 与 Azure 间的灾难恢复相比，局部区域间的灾难恢复意味着数据复制距离更短，因此延迟更低，RPO 也更低。

尽管这些优势非常强大，但如果发生 Azure 区域范围的自然灾害，区域间的灾难恢复可能达不到复原要求。

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>局部区域间灾难恢复的网络

如前所述，局部区域间的灾难恢复利用可用性区域之间的冗余网络概念，因此降低了复杂性，并大大简化了配置。 下面概述了局部区域间灾难恢复方案中的网络组件的行为： 

- 虚拟网络：可以使用与源网络相同的虚拟网络进行实际故障转移。 使用与源虚拟网络不同的虚拟网络进行测试性故障转移。

- 子网：支持故障转移到同一子网。

- 专用 IP 地址：如果使用静态 IP 地址，则可以在目标局部区域中使用相同的 IP（如果你选择以这种方式配置 IP）。

- 加速网络：如果 VM SKU 支持加速网络，则可以像在 Azure 间的灾难恢复中那样启用加速网络。

- 公共 IP 地址：可将先前在同一 Azure 区域中创建的标准公共 IP 地址附加到目标 VM。 基本公共 IP 地址不支持可用性区域相关的方案。

- 负载均衡器：标准负载均衡器是 Azure 区域性的资源，因此目标 VM 可以附加到同一个负载均衡器的后端池。 不需要新的负载均衡器。

- 网络安全组：可以使用应用于源 VM 的相同网络安全组。

## <a name="pre-requisites"></a>先决条件

在为 VM 部署局部区域间的灾难恢复之前，请务必确保 VM 上启用的其他功能可与局部区域间灾难恢复互操作。

|功能  | 支持声明  |
|---------|---------|
|经典 VM   |     不支持    |
|ARM VM    |    支持    |
|Azure 磁盘加密 v1（双通道，使用 Azure Active Directory (Azure AD)）     |     支持   |
|Azure 磁盘加密 v2（单通道，不使用 Azure AD）    |    支持    |
|非托管磁盘    |    不支持    |
|托管磁盘    |    支持    |
|客户管理的密钥    |    支持    |
|邻近放置组    |    支持    |
|备份互操作性    |    支持文件级备份和还原。 不支持磁盘和 VM 级备份与还原。    |
|热添加/移除    |    可以在启用局部区域间的复制后添加磁盘。 不支持在启用局部区域间的复制后移除磁盘。    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>设置 Site Recovery 局部区域间的灾难恢复

### <a name="log-in"></a>登录

登录到 Azure 门户。

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>为局部区域的 Azure 虚拟机启用复制

1. 在“Azure 门户”菜单中，选择“虚拟机”，或在任何页面上搜索并选择“虚拟机”。 选择要复制的 VM。 要实现局部区域间的灾难恢复，此 VM 必须已在某个可用性区域中。

2. 在“操作”中，选择“灾难恢复” 。

3. 如下所示，在“基本信息”选项卡中，对“是否在可用性区域之间进行灾难恢复?”选择“是”

    ![“基本设置”页](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. 如果你接受所有默认值，请单击“查看 + 开始复制”，然后单击“开始复制”。

5. 如果要对复制设置进行更改，请单击“下一步: 高级设置”。

6. 必要时请更改设置而不要使用默认值。 Azure 间灾难恢复的用户可能对此页感到很熟悉。 在[此处](./azure-to-azure-tutorial-enable-replication.md)可以找到有关此边栏选项卡上显示的选项的更多详细信息

    ![“高级设置”页](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. 单击“下一步: 查看 + 开始复制”，然后单击“开始复制”。

## <a name="faqs"></a>常见问题

**1.** 局部区域间的灾难恢复是如何定价的？
局部区域间灾难恢复的定价与 Azure 间灾难恢复相同。 可在[此处](https://azure.microsoft.com/pricing/details/site-recovery/)和[此处](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)的定价页上找到更多详细信息。 请注意，局部区域间灾难恢复的传出费用比 Azure 区域间灾难恢复更低。

**2.** RTO 和 RPO SLA 是什么？
RTO SLA 与 Site Recovery 的 SLA 总体上相同。 我们承诺最长 2 小时的 RTO。 在 RPO 方面，我们没有定义 SLA。

**3.** 次要局部区域是否提供容量保证？
Site Recovery 团队和 Azure 容量管理团队计划了足够的基础结构容量。 当你开始故障转移时，这些团队还会帮助确保受 Site Recovery 保护的 VM 实例将部署到目标局部区域。

**4.** 支持哪些操作系统？
局部区域间灾难恢复支持的操作系统与 Azure 间灾难恢复相同。 请参阅[此处](./azure-to-azure-support-matrix.md)的支持矩阵。

**5.** 源资源组和目标资源组是否可相同？
不可以，必须故障转移到不同的资源组。

## <a name="next-steps"></a>后续步骤

运行灾难恢复演练、故障转移、重新保护和故障回复时需要遵循的步骤与 Azure 间灾难恢复方案中的步骤相同。

若要执行灾难恢复演练，请遵循[此处](./azure-to-azure-tutorial-dr-drill.md)所述的步骤。

若要在次要局部区域中执行故障转移和重新保护 VM，请遵循[此处](./azure-to-azure-tutorial-failover-failback.md)所述的步骤。

若要故障回复到主要局部区域，请遵循[此处](./azure-to-azure-tutorial-failback.md)所述的步骤。
