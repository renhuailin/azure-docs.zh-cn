---
title: 使用 Azure VMware 解决方案配置 NXS 网络组件
description: 了解如何使用 Azure VMware 解决方案配置 NSX-T 网段。
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 190ec1e93cb9fef795e47693f80e5e9296354b83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699135"
---
# <a name="configure-nsx-network-components-using-azure-vmware-solution"></a>使用 Azure VMware 解决方案配置 NXS 网络组件

Azure VMware 解决方案私有云默认附带 NSX-T。 私有云预先配置了主动/主动模式的 NSX-T 第 0 层网关和主动/待机模式的默认 NSX-T 第 1 层网关。  通过这些网关可以将段连接（逻辑交换机）并提供东-西和北-南连接。 

部署 Azure VMware 解决方案后，可从 Azure 门户配置必要的 NSX-T 对象。  它针对不熟悉 NSX-T 管理器的用户提供了 VMware 管理员日常所需 NSX-T 操作的简化视图。  

在 Azure VMware 解决方案控制台中配置 NSX-T 组件时有四个选项：

- “段” - 创建在 NSX-T Manager 和 vCenter 中显示的段。 有关详细信息，请参阅[使用 Azure 门户添加 NSX-T 网段](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment)。

- “DHCP” - 如果计划使用 DHCP，请创建 DHCP 服务器或 DHCP 中继。  有关详细信息，请参阅[使用 Azure 门户创建 DHCP 服务器或中继](configure-dhcp-azure-vmware-solution.md#use-the-azure-portal-to-create-a-dhcp-server-or-relay)。

- “端口镜像” – 创建端口镜像以帮助对网络问题进行故障排除。 有关详细信息，请参阅[在 Azure 门户中配置端口镜像](configure-port-mirroring-azure-vmware-solution.md)。

- “DNS” – 创建 DNS 转发器，将 DNS 请求发送到指定 DNS 服务器进行解析。  有关详细信息，请参阅[在 Azure 门户中配置 DNS 转发器](configure-dns-azure-vmware-solution.md)。

>[!IMPORTANT]
>你仍可以访问 NSX-T Manager 控制台，在其中使用所述的高级设置和其他 NSX-T 功能。 

