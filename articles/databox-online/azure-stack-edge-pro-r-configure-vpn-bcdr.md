---
title: 在 Azure Stack Edge 虚拟专用网 (VPN) 上配置业务连续性和灾难恢复 (BCDR)
description: 介绍如何在 Azure Stack Edge VPN 上配置 BCDR。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367684"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>为 Azure Stack Edge VPN 配置业务连续性和灾难恢复

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

本文介绍如何在 Azure Stack Edge 设备上配置的虚拟专用网 (VPN) 上配置业务连续性和灾难恢复 (BCDR)。

本文适用于 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备。

## <a name="configure-failover-to-a-paired-region"></a>配置到配对区域的故障转移

Azure Stack Edge 设备使用其他 Azure 服务，例如 Azure 存储。 可以在 Azure Stack Edge 设备使用的任何特定 Azure 服务上配置 BCDR。 如果 Azure Stack Edge 使用的 Azure 服务故障转移到其配对的区域，则 Azure Stack Edge 设备现在将连接到新的 IP 地址，并且不会对通信进行双重加密。 

Azure Stack Edge 设备使用拆分隧道以及在主区域（与 Azure Stack Edge 设备关联的区域）中通过 VPN 隧道配置的所有数据和服务。 如果 Azure 服务故障转移到主区域外的配对区域，则数据将不再通过 VPN，因此不会进行双重加密。 

在此方案中，通常只影响少数 Azure 服务。 要解决此问题，应在 Azure Stack Edge VPN 配置中进行以下更改：

1. 在 Azure Stack Edge 上的 VPN 的非独占路由中，添加故障转移 Azure 服务 IP 范围。 然后，服务将开始通过 VPN 进行路由。

    要添加非独占路由，需要下载具有服务特定路由的 json 文件。 请确保用新路由更新此文件。
2. 在 Azure 路由表中添加相应的 Azure 服务 IP 范围。
3. 将路由添加到防火墙。

> [!NOTE]
>
> 1. Azure VPN 网关和 Azure 虚拟网络 (VNET) 的故障转移问题在[从因灾难而出现故障的 Azure 区域恢复](#recover-from-a-failed-azure-region)部分中得以解决。
> 2. Azure 路由表中添加的 IP 范围可能超过 400 的限制。 如果发生这种情况，需要按照[从一个 Azure 区域迁移到另一个 Azure 区域](#move-from-an-azure-region-to-another)部分中的指导进行操作。

## <a name="recover-from-a-failed-azure-region"></a>从出现故障的 Azure 区域恢复

如果整个 Azure 区域由于灾难性事件（如地震）而发生故障转移，则该区域中的所有 Azure 服务（包括 Azure Stack Edge 服务）都将进行故障转移。 由于存在多个服务，非独占路由很容易达到几百个。 Azure 路由限制为 400 个。 

如果区域发生故障转移，虚拟网络 (Vnet) 也会故障转移到新区域，虚拟网关（VPN 网关）也是如此。 要解决这一变化，请在 Azure Stack Edge VPN 配置中进行以下更改：

1. 将 Vnet 迁移到目标区域。 有关详细信息，请参阅：[通过 Azure 门户将 Azure 虚拟网络迁移到其他区域](../virtual-network/move-across-regions-vnet-portal.md)。
2. 在迁移 Vnet 的目标区域中部署新的 Azure VPN 网关。 有关详细信息，请参阅[创建虚拟网关](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)。
3. 更新 Azure Stack Edge VPN 配置以在 VPN 连接中使用上述 VPN 网关，然后选择目标区域以添加使用该 VPN 网关的路由。
4. 如果客户端地址池也发生更改，请更新传入的 Azure 路由表。 

## <a name="move-from-an-azure-region-to-another"></a>从一个 Azure 区域迁移到另一个 Azure 区域

可以将 Azure Stack Edge 设备从一个位置移到另一个位置。 要使用离设备部署位置最近的区域，需要为新的主区域配置该设备。 进行以下更改：

1. 你可以更新 Azure Stack Edge VPN 配置以使用新区域的 VPN 网关，并选择该新区域来添加使用 VPN 网关的路由。

## <a name="next-steps"></a>后续步骤

[备份 Azure Stack Edge 设备](azure-stack-edge-gpu-prepare-device-failure.md)。