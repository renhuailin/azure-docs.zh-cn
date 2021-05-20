---
title: 适用于 SAP 应用程序的 Azure 邻近放置组 | Microsoft Docs
description: 介绍使用 Azure 邻近放置组的 SAP 部署方案
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 924fdef475c43023c69c3006db19cd9a5aa15349
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669658"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>用于最大程度降低 SAP 应用程序网络延迟的 Azure 邻近放置组
基于 SAP NetWeaver 或 SAP S/4HANA 体系结构的 SAP 应用程序对 SAP 应用层与 SAP 数据库层之间的网络延迟很敏感。 这种敏感性是大多数业务逻辑在应用层运行的结果。 因为 SAP 应用层运行业务逻辑，所以它以每秒数千或数万次的高频率向数据库层发出查询。 在大多数情况下，这些查询的性质很简单。 它们通常可以在 500 毫秒或更短的时间内在数据库层上运行。

以下过程花费的时间对运行业务流程所需的时间有重大影响：在网络上将此类查询从应用层发送到数据库层并接收返回的结果。 对网络延迟的这种敏感性是你可能想要在 SAP 部署项目中实现特定的最大网络延迟的原因。 有关如何对网络延迟进行分类的准则，请参阅 [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E)（SAP 说明 #1100926 - 常见问题解答：网络性能）。

在许多 Azure 区域，数据中心的数量不断增加。 同时，客户（特别是高端 SAP 系统的客户）越来越多地使用 M 或 Mv2 系列的特殊 VM SKU 或 HANA 大型实例。 这些 Azure 虚拟机类型并不总是在 Azure 区域的所有数据中心都可用。 这些事实可以为优化 SAP 应用层与 SAP DBMS 层之间的网络延迟创造机会。

为了使你能够优化网络延迟，Azure 提供了[邻近放置组](../../co-location.md)。 邻近放置组可强制将不同的 VM 类型分组到单个 Azure 数据中心，以尽可能优化这些不同 VM 类型之间的网络延迟。 在将第一个 VM 部署到此类邻近放置组的过程中，VM 会绑定到特定的数据中心。 这项功能听起来很吸引人，但此构造的使用也引入了一些限制：

- 你不能假设所有 Azure VM 类型可用于所有 Azure 数据中心。 因此，在一个邻近放置组中，不同 VM 类型的组合可能会受到限制。 之所以会出现这些限制，是因为将放置组部署到的数据中心可能不存在运行某种 VM 类型所需的主机硬件
- 在对一个邻近放置组中的部分 VM 重设大小时，你无法自动假设在所有情况下，新的 VM 类型与邻近放置组中的其他 VM 可用于同一数据中心
- 当 Azure 解除硬件授权时，可能会将邻近放置组的某些 VM 强制放入另一个 Azure 数据中心。 有关这种情况的详细信息，请阅读文档[共置资源以改善延迟](../../co-location.md#planned-maintenance-and-proximity-placement-groups)  

> [!IMPORTANT]
> 由于存在潜在限制，只有在以下情况下才应使用邻近放置组：
>
> - 必要时
> - 用于单个 SAP 系统的粒度，而非整个系统环境或完整的 SAP 环境
> - 使用邻近放置组时，应将邻近放置组内的不同 VM 类型和 VM 数量保持在最低水平

假设如果通过指定可用性区域来部署 VM，并选择相同的可用性区域，这些 VM 之间的网络延迟应足以运行 SAP NetWeaver 和 S/4HANA 系统，并具有令人满意的性能和吞吐量。 该假设与特定区域是由一个数据中心还是多个数据中心组成无关。 在区域部署中使用邻近放置组的唯一原因是你希望将 Azure 可用性集部署的 VM 与区域部署的 VM 一起分配。


## <a name="what-are-proximity-placement-groups"></a>什么是邻近放置组？ 
Azure 邻近放置组是一种逻辑构造。 定义邻近放置组后，它会绑定到 Azure 区域和 Azure 资源组。 部署 VM 时，以下 VM 将引用邻近放置组：

- 在数据中心部署的第一个 Azure VM。 你可以将第一个虚拟机看作是基于 Azure 分配算法部署到数据中心的“范围 VM”，这些算法最终与特定可用性区域的用户定义相结合。
- 引用邻近放置组的所有后续部署的 VM，以将所有后续部署的 Azure VM 放在第一个虚拟机所在的数据中心内。

> [!NOTE]
> 如果未在放置第一个 VM 的数据中心部署可以运行特定 VM 类型的主机硬件，所请求的 VM 类型的部署将失败。 你会收到一条失败消息。

单个 [Azure 资源组](../../../azure-resource-manager/management/manage-resources-portal.md)可以分配有多个邻近放置组。 但是，一个邻近放置组只能分配给一个 Azure 资源组。


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>仅使用 Azure VM 的 SAP 系统的邻近放置组
Azure 上的大多数 SAP NetWeaver 和 S/4HANA 系统部署都不使用 [HANA 大型实例](./hana-overview-architecture.md)。 对于不使用 HANA 大型实例的部署，必须在 SAP 应用层和 DBMS 层之间提供最佳性能。 要做到这一点，只需为系统定义一个 Azure 邻近放置组。

在大多数客户部署中，客户会为 SAP 系统生成单个 [Azure 资源组](../../../azure-resource-manager/management/manage-resources-portal.md)。 在这种情况下，比如说，生产 ERP 系统资源组与其邻近放置组之间存在一对一的关系。 在其他情况下，客户会横向整理其资源组，并将所有生产系统收集在一个资源组中。 在本例中，生产 SAP 系统的资源组与生产 SAP ERP、SAP BW 等系统的多个邻近放置组之间会有一对多的关系。

避免将多个 SAP 生产或非生产系统捆绑在一个邻近放置组中。 当少量 SAP 系统或某个 SAP 系统与周边的某些应用程序需要低延迟的网络通信时，你可以考虑将这些系统移至一个邻近放置组。 避免捆绑系统，因为在邻近放置组中分组的系统越多，出现以下情况的几率就越大：

- 你需要某种 VM 类型，但该类型无法在邻近放置组所作用的特定数据中心运行。
- 当你随着时间推移，因为向邻近放置组添加软件而需要更多资源时，非主流 VM（例如 M 系列 VM）的资源最终可能无法满足你的需求。

如前文所述，理想的配置如下所示：

![仅具有 Azure VM 的邻近放置组](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

在本例中，单个 SAP 系统分组到一个资源组中，每个资源组有一个邻近放置组。 此配置与你使用 HANA 横向扩展配置还是 DBMS 纵向扩展配置无关。

## <a name="proximity-placement-groups-and-hana-large-instances"></a>邻近放置组和 HANA 大型实例
如果应用层的某些 SAP 系统依赖于 [HANA 大型实例](./hana-overview-architecture.md)，则在使用[修订版 4 行或标记](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)中部署的 HANA 大型实例单元时，HANA 大型实例单元和 Azure VM 之间的网络延迟将得到显著改进。 其中一项改进是，HANA 大型实例单元在部署时会与邻近放置组一起部署。 你可以使用该邻近放置组来部署应用层 VM。 因此，这些 VM 将部署在托管 HANA 大型实例单元的同一数据中心内。

若要确定 HANA 大型实例单元是否部署在修订版 4 标记或行中，请查看[通过 Azure 门户控制 Azure HANA 大型实例](./hana-li-portal.md#look-at-attributes-of-single-hli-unit)一文。 在 HANA 大型实例单元的属性概述中，你还可以确定邻近放置组的名称，因为它是在部署 HANA 大型实例单元时创建的。 属性概述中显示的名称是应将应用层 VM 部署到的邻近放置组的名称。

与仅使用 Azure 虚拟机的 SAP 系统相比，当你使用 HANA 大型实例时，在决定要使用多少个 [Azure 资源组](../../../azure-resource-manager/management/manage-resources-portal.md)方面的灵活性较差。 如[此文章](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal)所述，[HANA 大型实例租户](./hana-know-terms.md)的所有 HANA 大型实例单元都分组在一个资源组中。 除非为了分隔生产和非生产系统或其他系统，或因其他原因而部署到不同的租户中，否则，所有 HANA 大型实例单元都将部署在一个 HANA 大型实例租户中。 此租户与资源组具有一对一关系。 但是，将为每个单元定义单独的邻近放置组。

因此，单个租户的 Azure 资源组和邻近放置组之间的关系将如下所示：

![邻近放置组和 HANA 大型实例](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>使用邻近放置组进行部署的示例
下面是一些 PowerShell 命令，可用于通过 Azure 邻近放置组部署 VM。

登录到 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 之后，第一步是检查你是否在要用于部署的 Azure 订阅中：

<pre><code>
Get-AzureRmContext
</code></pre>

如果需要更改为其他订阅，可以通过运行以下命令来进行更改：

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

通过运行以下命令来创建新的 Azure 资源组：

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

通过运行以下命令来创建新的邻近放置组：

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

使用如下所示的命令将第一个 VM 部署到邻近放置组中：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

上述命令将部署基于 Windows 的 VM。 成功部署此 VM 后，将在 Azure 区域内定义邻近放置组的数据中心范围。 所有引用邻近放置组的后续 VM 部署（如上一命令中所示）都将部署在同一 Azure 数据中心，但前提是该 VM 类型可以托管在该数据中心放置的硬件上，并且该 VM 类型的容量可用。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>将可用性集和可用性区域与邻近放置组结合使用
使用可用性区域部署 SAP 系统的缺点之一是，你无法使用特定区域内的可用性集来部署 SAP 应用层。 你希望将 SAP 应用层与 DBMS 层部署在相同的区域中。 系统不支持在部署单个 VM 时引用可用性区域和可用性集。 因此，以前，你不得不通过引用区域来部署应用层。 你失去了确保应用层 VM 分布在不同更新域和故障域的能力。

通过使用邻近放置组，你可以绕过此限制。 下面是部署顺序：

- 创建邻近放置组。
- 通过引用可用性区域来部署定位 VM（通常是 DBMS 服务器）。
- 创建一个引用 Azure 邻近组的可用性集。 （请参阅本文后面的命令。）
- 通过引用可用性集和邻近放置组来部署应用层 VM。

部署 VM 时，无需像上一部分演示的那样部署第一个 VM，你可以引用可用性区域和邻近放置组：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

成功部署此虚拟机后，SAP 系统的数据库实例将托管在一个可用性区域中。 邻近放置组的范围固定为代表你定义的可用性区域的数据中心之一。

假设以与 DBMS VM 相同的方式部署中央服务 VM，并引用相同的一个或多个区域和相同的邻近放置组。 在下一步中，你需要创建要用于 SAP 系统的应用层的可用性集。

定义并创建邻近放置组。 用于创建可用性集的命令需要另外引用邻近放置组 ID（而不是名称）。 你可以使用以下命令获取邻近放置组的 ID：

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

创建可用性集时，如果使用托管磁盘（除非另有说明，否则为默认设置）和邻近放置组，则需要考虑其他参数：

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

理想情况下，应使用三个容错域。 但是，受支持容错域的数量可能因地区而异。 在本例中，特定区域可能具有的最大容错域数量为 2 个。 若要部署应用层 VM，你需要添加对可用性集名称和邻近放置组名称的引用，如下所示：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

此部署的结果为：
- SAP 系统的 DBMS 层和中央服务位于一个或多个特定可用性区域中。
- SAP 应用层与一个或多个 DBMS VM 通过可用性集放置在同一 Azure 数据中心。

> [!NOTE]
> 为了创建高可用性配置，你将一个 DBMS VM 部署到一个区域，而将第二个 DBMS VM 部署到另一个区域，因此，你需要为每个区域提供不同的邻近放置组。 对于你使用的任何可用性集，也是如此。

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>将现有系统移至邻近放置组
如果已经部署了 SAP 系统，你可能需要优化某些关键系统的网络延迟，并将应用层和 DBMS 层放在同一数据中心。 若要将完整 Azure 可用性集的 VM 移至已限定范围的现有邻近放置组，你需要关闭该可用性集的所有 VM，并通过 Azure 门户、PowerShell 或 CLI 将该可用性集分配给现有的邻近放置组。 如果要将不属于可用性集的 VM 移至现有邻近放置组，只需关闭 VM 并将其分配给现有邻近放置组即可。 


## <a name="next-steps"></a>后续步骤
查看文档：

- [Azure 上的 SAP 工作负载：规划和部署清单](./sap-deployment-checklist.md)
- [预览版：使用 Azure CLI 将 VM 部署到邻近放置组](../../linux/proximity-placement-groups.md)
- [预览版：使用 PowerShell 将 VM 部署到邻近放置组](../../windows/proximity-placement-groups.md)
- [部署适用于 SAP 工作负载的 Azure 虚拟机 DBMS 的注意事项](./dbms_guide_general.md)