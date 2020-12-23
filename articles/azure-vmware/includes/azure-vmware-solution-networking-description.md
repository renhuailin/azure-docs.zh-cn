---
title: Azure VMware 解决方案网络和连接
description: Azure VMware 解决方案网络和连接说明。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2020
ms.locfileid: "92924976"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware 解决方案提供一个私有云环境，可通过本地和基于 Azure 的环境或资源进行访问。 Azure ExpressRoute 和 VPN 连接等服务可提供连接。 这些服务需要特定的网络地址范围和防火墙端口才能启用。

部署私有云时，会创建用于管理、预配和 vMotion 的专用网络。 可使用这些专用网络来访问 vCenter 和 NSX-T 管理器，还可访问虚拟机 vMotion 或部署。  ExpressRoute Global Reach 用于将私有云连接到本地环境。 要实现该连接，你的订阅中必须存在带有 ExpressRoute 线路的虚拟网络。

通过 Azure 虚拟 WAN 公共 IP 功能可在 Internet 中访问 Web 服务器和虚拟机等资源。  默认情况下，会针对新的私有云禁用 Internet 访问。 有关详细信息，请参阅[如何使用 Azure VMware 解决方案中的公共 IP 功能](../public-ip-usage.md)。