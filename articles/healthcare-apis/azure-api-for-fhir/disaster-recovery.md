---
title: Azure API for FHIR 的灾难恢复
description: 本文介绍如何启用 Azure API for FHIR 的灾难恢复功能。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/03/2021
ms.author: zxue
ms.openlocfilehash: c060581bb25e8708893ac9c3e48e694a0b86c50d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778761"
---
# <a name="disaster-recovery-for-azure-api-for-fhir"></a>Azure API for FHIR 的灾难恢复

Azure API for FHIR® 是基于快速医疗保健互操作性资源 (FHIR®) 的全托管式服务。 可以使用 Azure API for FHIR 的灾难恢复 (DR) 功能来满足业务与合规性要求。

DR 功能提供 15 分钟恢复点目标 (RPO) 和 60 分钟恢复时间目标 (RTO)。

 ## <a name="how-to-enable-dr"></a>如何启用 DR 
  
若要启用 DR 功能，请创建一次性支持票证。 可以选择 Azure 配对区域或支持 Azure API for FHIR 的其他区域。 Microsoft 支持团队将根据支持优先级启用 DR 功能。

## <a name="how-the-dr-process-works"></a>DR 过程的工作原理

DR 过程包括以下步骤： 
* 数据复制
* 自动故障转移
* 受影响区域的恢复
* 手动故障回复

### <a name="data-replication-in-the-secondary-region"></a>次要区域中的数据复制

默认情况下，Azure API for FHIR 通过备份和还原来提供数据保护。 启用灾难恢复功能后，将开始数据复制。 将在次要 Azure 区域中自动创建并同步数据副本。 初始数据复制可能需要花费几分钟到几小时甚至更长时间，具体取决于数据量。 次要数据副本是主要数据的复制件。 此副本直接用于恢复该服务，并帮助加快恢复过程。

需要注意的是，吞吐量（RU/秒）在主要和次要区域中必须采用相同的值。

[ ![Azure 流量管理器。](media/disaster-recovery/azure-traffic-manager.png) ](media/disaster-recovery/azure-traffic-manager.png#lightbox)

### <a name="automatic-failover"></a>自动故障转移

在主要区域服务中断期间，Azure API for FHIR 会自动故障转移到次要区域，并使用相同的服务终结点。 服务预计会在一小时以内恢复，最多可能丢失 15 分钟的数据。 可能需要进行其他配置更改。 有关详细信息，请参阅 [DR 中的配置更改](#configuration-changes-in-dr)。

[ ![灾难恢复中的故障转移。](media/disaster-recovery/failover-in-disaster-recovery.png) ](media/disaster-recovery/failover-in-disaster-recovery.png#lightbox)

### <a name="affected-region-recovery"></a>受影响区域的恢复

受影响区域恢复后，它可自动用作次要区域，然后重新开始数据复制。 可以开始数据恢复过程，也可以等到故障回复步骤完成为止。

[ ![灾难恢复中的复制。](media/disaster-recovery/replication-in-disaster-recovery.png) ](media/disaster-recovery/replication-in-disaster-recovery.png#lightbox)

如果计算已故障回复到已恢复的区域，但数据未故障回复到已恢复的区域，则可能表示出现了网络延迟。 主要原因是计算和数据位于两个不同的区域。 一旦通过手动触发器将数据故障回复到已恢复的区域，网络延迟就会自动消失。

[ ![网络延迟。](media/disaster-recovery/network-latency.png) ](media/disaster-recovery/network-latency.png#lightbox)


### <a name="manual-failback"></a>手动故障回复

计算会自动故障回复到已恢复的区域。 Microsoft 支持团队会使用脚本手动将数据切换回到已恢复的区域。 

[ ![灾难恢复中的故障回复。](media/disaster-recovery/failback-in-disaster-recovery.png) ](media/disaster-recovery/failback-in-disaster-recovery.png#lightbox)

## <a name="configuration-changes-in-dr"></a>DR 中的配置更改

使用专用链接、客户管理的密钥 (CMK)、IoMT FHIR 连接器（医疗物联网）和 $export 时，可能需要进行其他配置更改。

### <a name="private-link"></a>专用链接

可以在预配 Azure API for FHIR 之前或之后启用专用链接功能。 也可以在启用 DR 功能之前或之后预配专用链接。 准备好为 DR 配置专用链接时，请参阅以下列表。

* 在主要区域中配置 Azure 专用链接。 不需要在次要区域中执行此步骤。 有关详细信息，请参阅[配置专用链接](/azure/healthcare-apis/fhir/configure-private-link)

* 在主要区域中创建一个 Azure VNet，在次要区域中创建另一个 VNet。 有关信息，请参阅[使用 Azure 门户创建虚拟网络](../../virtual-network/quick-create-portal.md)。

* 主要区域中的 VNet 将与次要区域 VNet 建立 VNet 对等互连。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

* 使用默认设置，或者可以根据需要定制配置。 重要的是，流量可以在两个虚拟网络之间流动。

* 设置专用 DNS 后，需要手动将次要区域中的 VNet 设置为“虚拟网络链接”。 主要 VNet 应已添加为专用链接终结点创建流的一部分。 有关详细信息，请参阅[虚拟网络链接](../../dns/private-dns-virtual-network-links.md)。

* （可选）在主要区域 VNet 和次要区域 VNet 中各设置一个 VM。 可以从这两个 VM 访问 Azure API for FHIR。

在发生区域性服务中断期间以及故障回复完成之后，专用链接功能应会继续正常工作。 有关详细信息，请参阅[配置专用链接](/azure/healthcare-apis/fhir/configure-private-link)。

> [!NOTE]
> 配置虚拟网络和 VNet 对等互连不会影响数据复制。

### <a name="cmk"></a>CMK

如果可以访问用于托管订阅中管理的密钥的密钥保管库，则你就仍然可以访问 Azure API for FHIR。 由于密钥保管库最长可能需要花费 20 分钟来重新建立连接，因此有可能会出现暂时性的停机。 有关详细信息，请参阅 [Azure Key Vault 可用性和冗余](../../key-vault/general/disaster-recovery-guidance.md)。  

### <a name="export"></a>$export

10 分钟后，将从另一个区域拾取导出作业，但不更新作业状态。 请按照有关在发生区域性服务中断后恢复存储帐户的 Azure 存储指导进行操作。 有关详细信息，请参阅[灾难恢复和存储帐户故障转移](../../storage/common/storage-disaster-recovery-guidance.md)。 

确保为 Azure API for FHIR 的系统标识授予相同的权限。 此外，如果为存储帐户配置了选定的网络，请参阅[如何导出 FHIR 数据](/azure/healthcare-apis/fhir/export-data)。

### <a name="iomt-fhir-connector"></a>IoMT FHIR 连接器

在还原已发生故障的区域之前，任何现有连接都不会正常工作。 一旦故障转移完成并且 FHIR 服务器可供访问，即可创建新的连接。 发生故障回复时，此新连接将继续正常工作。

> [!NOTE]
> IoMT 连接器是一项预览版功能，不提供对灾难恢复的支持。 

## <a name="how-to-test-dr"></a>如何测试 DR

可以在非生产环境中测试 DR 功能，但不一定非要这样做。 要进行 DR 测试，请只包含数据，而不要包含计算。 

考虑使用以下步骤进行 DR 测试。

* 使用测试数据准备测试环境。 建议使用包含少量数据的服务实例，以减少数据复制时间。
 
* 创建支持票证，并针对你的测试环境提供你的 Azure 订阅和 Azure API for FHIR 的服务名称。

* 像执行任何 DR 测试一样制定一个测试计划。
 
* Microsoft 支持团队将启用 DR 功能，并确认故障转移已发生。

* 执行 DR 测试并记录测试结果，其中应包括任何数据丢失和网络延迟问题。 

* 对于故障回复，请通知 Microsoft 支持团队来完成故障回复步骤。
 
* （可选）与 Microsoft 支持团队分享任何反馈。


> [!NOTE]
> 在 DR 测试期间，测试环境的成本将会翻倍。 完成 DR 测试并禁用 DR 功能后，不会产生额外的成本。

## <a name="cost-of-disaster-recovery"></a>灾难恢复的成本

由于需要在次要区域的环境中运行计算数据和数据副本，灾难恢复功能会产生额外的成本。 有关更多定价详细信息，请参阅 [Azure API for FHIR 定价]( https://azure.microsoft.com/pricing/details/azure-api-for-fhir)网页。

> [!NOTE]
> DR 产品/服务受 [Azure API for FHIR 的 SLA](https://azure.microsoft.com/support/legal/sla/azure-api-for-fhir/v1_0/) 1.0 的约束。


## <a name="next-steps"></a>后续步骤

在本文中，你已了解 Azure API for FHIR 的 DR 工作原理，以及如何启用此功能。 若要了解 Azure API for FHIR 的其他受支持功能，请参阅：

>[!div class="nextstepaction"]
>[FHIR 支持的功能](fhir-features-supported.md)