---
title: Azure VMware 解决方案网络和连接
description: Azure VMware 解决方案网络和连接说明。
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0ee52f437027690787ca5dc95eda2705b796b604
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638326"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware 解决方案提供了一个私有云环境，可以在本地对其进行访问，以及通过基于 Azure 的资源对其进行访问。 Azure ExpressRoute、VPN 连接或 Azure 虚拟 WAN 等服务可提供连接。 但是，这些服务需要特定的网络地址范围和防火墙端口才能启用。

部署私有云时，会创建用于管理、预配和 vMotion 的专用网络。 你将使用这些专用网络来访问 vCenter 和 NSX-T Manager，还将访问虚拟机 vMotion 或部署。

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

使用 [Azure 虚拟 WAN 公共 IP](../enable-public-internet-access.md) 功能，可从 Internet 访问部署在私有云上的虚拟机。 默认情况下，会针对新的私有云禁用 Internet 访问。 



