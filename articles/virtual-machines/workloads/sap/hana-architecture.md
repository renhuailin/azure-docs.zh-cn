---
title: Azure SAP HANA（大型实例）的体系结构 | Microsoft Docs
description: 了解用于部署 Azure SAP HANA（大型实例）的体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fee1aab009bdbf84acf1a73244d6686db50e4e3f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450699"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure 上的 SAP HANA（大型实例）体系结构

本文将介绍用于部署 Azure SAP HANA 大型实例的体系结构（也称为 BareMetal 基础结构）。 

概括而言，Azure SAP HANA（大型实例）解决方案在虚拟机 (VM) 上具有 SAP 应用层。 数据库层位于 SAP 认证的 HANA 大型实例 (HLI) 上， 该 HLI 与 Azure IaaS VM 位于同一 Azure 区域。

> [!NOTE]
> 在 SAP 数据库管理系统 (DBMS) 层所在的 Azure 区域部署 SAP 应用层。 有关 Azure 上的 SAP 工作负荷的已发布信息对此规则做了详细的阐述。 

## <a name="architectural-overview"></a>体系结构概述

Azure SAP HANA（大型实例）的整体架构提供了 SAP TDI 认证的硬件配置。 该硬件是用于 SAP HANA 数据库的非虚拟化、裸机高性能服务器。 它使你可以灵活地缩放 SAP 应用程序层的资源以满足你的需求。

![Azure 上的 SAP HANA（大型实例）的体系结构概述](./media/hana-overview-architecture/image1-architecture.png)

所示体系结构分为三个部分：

- **右侧**：显示在数据中心内运行各种应用程序的本地基础结构，以便最终用户可以访问业务线 (LOB) 应用程序（例如 SAP）。 理想情况下，此本地基础结构使用 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 连接到 Azure。

- **中间**：显示 Azure IaaS，而在本例中，显示如何使用 VM 来托管 SAP 或其他将 SAP HANA 用作 DBMS 的应用程序。 使用由 VM 提供的内存运行的较小 HANA 实例与其应用层一起部署在 Azure VM 中。 有关虚拟机的详细信息，请参阅[虚拟机](https://azure.microsoft.com/services/virtual-machines/)。

   Azure 网络服务用于将 SAP 系统以及其他应用程序分组到虚拟网络。 这些虚拟网络连接到本地系统以及 Azure SAP HANA（大型实例）。

   有关支持在 Azure 中运行的 SAP NetWeaver 应用程序和数据库，请参阅 [SAP 说明 #1928533 - Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533)。 有关如何在 Azure 上部署 SAP 解决方案的文档，请查看：

  -  [在 Windows 虚拟机上使用 SAP](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [在 Azure 虚拟机上使用 SAP 解决方案](get-started.md)

- **左侧：** 显示 Azure 大型实例模具中通过 SAP HANA TDI 认证的硬件。 HANA 大型实例单元使用本地服务器连接到 Azure 时所用的技术连接到 Azure 订阅的虚拟网络。 2019 年 5 月，我们引入了一项优化，允许在没有 ExpressRoute 网关的情况下，在 HANA 大型实例单元和 Azure VM 之间进行通信。 此优化称为 ExpressRoute FastPath，如上图中的红线所示。

## <a name="components-of-the-azure-large-instance-stamp"></a>Azure 大型实例标记的组件

Azure 大型实例模具本身包含以下组件：

- **计算：** 基于不同代次英特尔至强处理器的服务器，提供必要的计算功能，并通过 SAP HANA 认证。
- **网络**：统一的高速网络结构，与计算、存储和 LAN 组件互连。
- **存储**：通过统一网络结构访问的存储基础结构。 提供的存储容量取决于部署的 Azure SAP HANA（大型实例）配置。 可通过每月额外付费的方式获得更多存储容量。

## <a name="tenants"></a>租户

在大型实例模具的多租户基础结构中，客户被部署为隔离的租户。 在部署租户时，需在 Azure 合约中命名一个 Azure 订阅。 此 Azure 订阅是对 HANA 大型实例计费时依据的 Azure 订阅。 这些租户与 Azure 订阅之间存在 1:1 的对应关系。 

在网络级别，可以从属于不同 Azure 订阅的不同虚拟网络访问部署在一个 Azure 区域的一个租户中的 HANA 大型实例。 但是，这些 Azure 订阅必须属于同一 Azure 合约。

## <a name="availability-across-regions"></a>跨区域的可用性

与 VM 一样，Azure 上的 SAP HANA（大型实例）是在多个 Azure 区域中提供的。 若要提供灾难恢复功能，可以选择启用。 一个地缘政治区域中的各种大型实例模具相互连接在一起。 例如，在美国西部和美国东部的 HANA 大型实例模具是通过专用网络链接连接的，目的是进行灾难恢复复制。

## <a name="available-skus"></a>可用的 SKU

如同 Azure 允许在不同的 VM 类型之间选择一样，你也可以在 HANA 大型实例的不同 SKU 之间进行选择。 可选择适合特定 SAP HANA 工作负载类型的 SKU。 SAP 基于 Intel 处理器世代为各种工作负荷应用内存对处理器插槽比率。 有关可用 SKU 的详细信息，请参阅[适用于 HLI 的 SKU](hana-available-skus.md)。

## <a name="next-steps"></a>后续步骤

了解 SAP HANA 大型实例网络体系结构。

> [!div class="nextstepaction"]
> [SAP HANA（大型实例）网络体系结构](hana-network-architecture.md)
