---
title: Azure VMware 解决方案网络和连接
description: Azure VMware 解决方案网络和连接说明。
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462570"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware 解决方案提供了一个私有云环境，可以在本地对其进行访问，以及通过基于 Azure 的资源对其进行访问。 Azure ExpressRoute、VPN 连接或 Azure 虚拟 WAN 等服务可提供连接。 这些服务需要特定的网络地址范围和防火墙端口才能启用。

部署私有云时，会创建用于管理、预配和 vMotion 的专用网络。 可使用这些专用网络来访问 vCenter 和 NSX-T 管理器，还可访问虚拟机 vMotion 或部署。  

ExpressRoute Global Reach 用于将私有云连接到本地环境。 要实现该连接，你的订阅中必须存在一个使用 ExpressRoute 线路（连接到本地）的虚拟网络。

使用 Azure 虚拟 WAN 公共 IP 功能，可从 Internet 访问部署在私有云上的虚拟机 (VM)。  默认情况下，会针对新的私有云禁用 Internet 访问。 有关详细信息，请参阅[如何使用 Azure VMware 解决方案中的公共 IP 功能](../public-ip-usage.md)。

