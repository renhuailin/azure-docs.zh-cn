---
title: 规划 Avere vFXT 系统 - Azure
description: 规划符合需求的 Avere vFXT for Azure 群集。 了解在投放到 Azure 市场或创建虚拟机之前要提出的问题。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 9f852ec056f3252005bba0bc142c05a9cfbe6c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342393"
---
# <a name="plan-your-avere-vfxt-system"></a>规划 Avere vFXT 系统

本文介绍如何规划根据需求适当定位并调整大小的新 Avere vFXT for Azure 群集。

在投放到 Azure 市场或创建任何 VM 之前，请考虑以下细节：

* 群集如何与其他 Azure 资源交互？
* 应将群集元素置于专用网络和子网中的何处？
* 要使用哪种类型的后端存储，群集如何访问该存储？
* 群集节点需要具备多大的能力才能支持你的工作流？

请继续阅读了解更多信息。

## <a name="learn-the-components-of-the-system"></a>了解系统的组件

开始规划时，了解 Avere vFXT for Azure 系统的组件会很有帮助。

* 群集节点 - 群集由三个或更多个配置为群集节点的 VM 组成。 节点越多，系统的吞吐量就更高，且缓存更大。

* 缓存 - 缓存容量在群集节点之间均匀划分。 创建群集时，请按节点设置缓存大小；将节点大小相加即为总缓存大小。

* 群集控制器 - 群集控制器是一个附加的 VM，位于群集节点所在的同一个子网中。 需要通过该控制器来创建群集和执行日常管理任务。

* 后端存储 - 要缓存的数据将长期存储在硬件存储系统或 Azure Blob 容器中。 可以在创建 Avere vFXT for Azure 群集之后添加存储；如果使用 Blob 存储，则可以在创建群集时添加和配置容器。

* 客户端 - 使用缓存文件的客户端计算机通过虚拟文件路径连接到群集，而不是通过直接访问存储系统进行连接。 （在[装载 Avere vFXT 群集](avere-vfxt-mount-clients.md)中了解详细信息。）

## <a name="subscription-resource-group-and-network-infrastructure"></a>订阅、资源组和网络基础结构

考虑好 Avere vFXT for Azure 部署元素的位置。 下图显示了 Avere vFXT for Azure 组件的可能排列方式：

![显示一个子网中的群集控制器和群集 VM 的示意图。 子网边界的周围是 VNet 边界。 VNet 中的六边形表示存储服务终结点；它连接到 VNet 外部的 Blob 存储，如虚线箭头所示。](media/avere-vfxt-components-option.png)

规划 Avere vFXT 群集的网络基础结构时，请遵循以下准则：

* 为每个 Avere vFXT for Azure 部署创建一个新订阅。 管理此订阅中的所有组件。

  对每个部署使用新订阅的优势包括：
  * 更简单的成本跟踪 - 在一个订阅中查看和审核由资源、基础结构和计算循环产生的所有成本。
  * 更轻松的清理 - 完成项目后，可以删除整个订阅。
  * 方便分割资源配额 - 将 Avere vFXT 客户端和群集隔离到单个订阅中，以防止其他关键工作负载受到资源限制。 为高性能计算工作流启动大量客户端时，这种隔离可以防止冲突。

* 将客户端计算系统定位在靠近 vFXT 群集的位置。 后端存储可以位于较远的位置。  

* 将 vFXT 群集和群集控制器 VM 置于一起 - 具体而言，它们应该：

  * 位于同一个虚拟网络中
  * 位于同一个资源组中
  * 使用同一个存储帐户
  
  在大多数情况下，群集创建模板都会处理此配置。

* 群集必须位于其自身的子网中，以避免与客户端或其他计算资源发生 IP 地址冲突。

* 使用群集创建模板为群集创建大部分所需的基础结构资源，包括资源组、虚拟网络、子网和存储帐户。

  如果你要使用现有的资源，请确保它们满足下表中的要求。

  | 资源 | 是否使用现有资源？ | 要求 |
  |----------|-----------|----------|
  | 资源组 | 如果为空则使用 | 必须为空|
  | 存储帐户 | 如果要在创建群集后连接现有 Blob 容器，则使用 <br/>  如果要在创建群集期间创建新的 Blob 容器，则不使用 | 现有 Blob 容器必须为空 <br/> &nbsp; |
  | 虚拟网络 | 是 | 如果要创建新的 Azure Blob 容器，则必须包含存储服务终结点 |
  | 子网 | 是 | 不能包含其他资源 |

## <a name="ip-address-requirements"></a>IP 地址要求

确保群集的子网具有足够大的 IP 地址范围，以支持群集。

Avere vFXT 群集使用以下 IP 地址：

* 一个群集管理 IP 地址。 可根据需要在群集中的节点之间移动此地址，使其始终可用。 使用此地址连接到 Avere 控制面板配置工具。
* 对于每个群集节点：
  * 至少有一个面向客户端的 IP 地址。 （所有面向客户端的地址由群集的 vserver 管理，此工具可根据需要在节点之间移动 IP 地址。）
  * 一个用于群集通信的 IP 地址
  * 一个实例 IP 地址（分配给 VM）

如果使用 Azure Blob 存储，则还可能需要群集虚拟网络中的 IP 地址：  

* 一个 Azure Blob 存储帐户需要至少五个 IP 地址。 如果将 Blob 存储置于群集所在的同一虚拟网络中，请记住此要求。
* 如果使用群集虚拟网络外部的 Azure Blob 存储，应在虚拟网络中创建存储服务终结点。 该终结点不使用 IP 地址。

可以选择将网络资源和 Blob 存储（如果已使用）定位在不包含该群集的其他资源组中。

## <a name="vfxt-node-size"></a>vFXT 节点大小

充当群集节点的 VM 决定了请求吞吐量和缓存的存储容量。 <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

每个 vFXT 节点是相同的。 也就是说，如果创建三节点群集，则会获得三个具有相同类型和大小的 VM。

| 实例类型 | vCPU | 内存  | 本地 SSD 存储  | 最大数据磁盘数 | 非缓存磁盘吞吐量 | NIC（计数） |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MBps | 16,000 MBps (8)  |

每个节点的磁盘缓存可配置，范围为 1000 GB 到 8000 GB。 对于 Standard_E32s_v3 节点，建议的缓存大小为每个节点 4 TB。

有关这些 VM 的其他信息，请阅读 Microsoft Azure 文档：[内存优化虚拟机大小](../virtual-machines/sizes-memory.md)

## <a name="account-quota"></a>帐户配额

确保订阅具有足够的容量，可以运行 Avere vFXT 群集以及所用的任何计算系统或客户端系统。 有关详细信息，请阅读 [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。

## <a name="back-end-data-storage"></a>后端数据存储

后端存储系统不仅向群集的缓存提供文件，而且还会接收缓存中已更改的数据。 确定是要工作集长期存储在新 Blob 容器还是现有存储系统（云或硬件）中。 这些后端存储系统称为核心文件管理器。

### <a name="hardware-core-filers"></a>硬件核心文件管理器

创建群集后，请将硬件存储系统添加到 vFXT 群集。 可以使用各种流行的硬件系统（包括本地系统），前提是可以从群集的子网访问存储系统。

有关如何将现有存储系统添加到 Avere vFXT 群集的详细说明，请阅读[配置存储](avere-vfxt-add-storage.md)。

### <a name="cloud-core-filers"></a>云核心文件管理器

Avere vFXT for Azure 系统可将空的 Blob 容器用于后端存储。 容器在添加到群集时必须是空的 - vFXT 系统必须能够管理其对象存储，而无需保留现有数据。

> [!TIP]
> 若要对后端使用 Azure Blob 存储，请在创建 vFXT 群集的过程中创建一个新容器。 群集创建模板可以创建并配置新的 Blob 容器，以便在群集可用时可以立即使用该容器。 以后再添加容器会更复杂。
>
> 有关详细信息，请阅读[创建 Avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)。

将空的 Blob 存储容器添加为核心文件管理器后，可以通过群集将数据复制到其中。 使用并行多线程复制机制。 请阅读[将数据移到 vFXT 群集](avere-vfxt-data-ingest.md)，了解如何使用客户端计算机和 Avere vFXT 缓存高效地将数据复制到群集的新容器。

## <a name="cluster-access"></a>群集访问

Avere vFXT for Azure 群集位于专用子网中，并且该群集没有公共 IP 地址。 必须通过某种方式访问专用子网以进行群集管理和客户端连接。

访问选项包括：

* 跳转主机 - 向专用网络中的一个单独 VM 分配一个公共 IP 地址，并使用它来创建到群集节点的 TLS 隧道。

  > [!TIP]
  > 如果在群集控制器上设置公共 IP 地址，则可以使用它作为跳转主机。 有关详细信息，请阅读[群集控制器用作跳转主机](#cluster-controller-as-jump-host)。

* 虚拟专用网络 (VPN) - 在 Azure 中的专用网络与企业网络之间配置点到站点或站点到站点 VPN。

* Azure ExpressRoute - 通过任一 ExpressRoute 合作伙伴配置专用连接。

有关这些选项的详细信息，请参阅[有关 Internet 通信的 Azure 虚拟网络文档](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)。

### <a name="cluster-controller-as-jump-host"></a>群集控制器用作跳转主机

如果你在群集控制器上设置了公共 IP 地址，则可以使用它作为跳转主机来从专用子网外部联系 Avere vFXT 群集。 但是，因为控制器有权修改群集节点，这会带来小小的安全风险。

为提高使用公共 IP 地址的控制器的安全性，部署脚本将自动创建一个仅限对端口 22 进行入站访问的网络安全组。 可以通过锁定对 IP 源地址范围的访问来进一步保护系统，换句话说，只允许从你打算将其用于群集访问的计算机进行连接。

创建群集时，可以选择是否在群集控制器上创建公共 IP 地址。

* 如果你创建新的虚拟网络或新子网，系统将为群集控制器分配一个公共 IP 地址  。
* 如果你选择现有的虚拟网络和子网，则群集控制器只使用专用 IP 地址。

## <a name="vm-access-roles"></a>VM 访问角色

Azure 使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/index.yml) 来授权群集 VM 执行特定的任务。 例如，群集控制器需要获得授权才能创建和配置群集节点 VM。 群集节点需要能够向其他群集节点分配或重新分配 IP 地址。

有两个内置的 Azure 角色用于 Avere vFXT 虚拟机：

* 群集控制器使用内置角色“[Avere 参与者](../role-based-access-control/built-in-roles.md#avere-contributor)”。
* 群集节点使用内置角色“[Avere 操作者](../role-based-access-control/built-in-roles.md#avere-operator)”。

如果需要为 Avere vFXT 组件自定义访问角色，则必须定义自己的角色，然后在创建 VM 时为其分配该角色。 不能使用 Azure 市场中的部署模板。 请根据[获得有关系统的帮助](avere-vfxt-open-ticket.md)中所述，在 Azure 门户中开具票证来咨询 Microsoft 客服和支持人员。

## <a name="next-steps"></a>后续步骤

[部署概述](avere-vfxt-deploy-overview.md)大致介绍了创建 Avere vFXT for Azure 系统并使其准备好提供数据所要执行的步骤。