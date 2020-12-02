---
title: '配置业务连续性和灾难恢复 (在 Azure Stack Edge 虚拟专用网络 (VPN) 上的 BCDR) '
description: 介绍如何在 Azure Stack 边缘 VPN 上配置 BCDR。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: 4d735c623a6dffe24108d06d00caa7fba987c7df
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466091"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>为 Azure Stack Edge VPN 配置业务连续性和灾难恢复

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

本文介绍如何在 Azure Stack 边缘设备上配置的虚拟专用网络 (VPN) 上配置业务连续性和灾难恢复 (BCDR) 。

本文适用于 Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 设备。

## <a name="configure-failover-to-a-paired-region"></a>配置到配对区域的故障转移

Azure Stack Edge 设备使用其他 Azure 服务，例如 Azure 存储。 可以在 Azure Stack Edge 设备使用的任何特定 Azure 服务上配置 BCDR。 如果 Azure Stack Edge 使用的 Azure 服务故障转移到其配对的区域，则 Azure Stack Edge 设备现在将连接到新的 IP 地址，并且不会对通信进行双重加密。 

Azure Stack 边缘设备使用拆分隧道以及在主区域中配置的所有数据和服务 (与 Azure Stack 边缘设备关联的区域) 通过 VPN 隧道。 如果 Azure 服务故障转移到超出本地区的配对区域，则数据将不再通过 VPN，因此不会进行双重加密。 

在此方案中，通常只影响少数 Azure 服务。 若要解决此问题，应在 Azure Stack Edge VPN 配置中进行以下更改：

1. 在 Azure Stack 边缘上的 VPN 的包含路由中，添加) 的故障转移 Azure 服务 IP 范围 (s。 然后，服务将开始通过 VPN 进行路由。

    若要添加包含路由，需要下载具有特定服务路由的 json 文件。 请确保用新路由更新此文件。
2. 在 Azure 路由表中 (s) 添加相应的 Azure 服务 IP 范围。
3. 将路由添加到防火墙。

> [!NOTE]
>
> 1. Azure VPN 网关和 Azure 虚拟网络 (VNET) 的故障转移部分在 [从因灾难故障而导致的 azure 区域恢复](#recover-from-a-failed-azure-region)部分中进行了解决。
> 2. Azure 路由表中添加的 IP 范围可能会超过400的限制。 如果发生这种情况，你将需要按照一节 [从一个 azure 区域迁移到另一个 azure 区域](#move-from-an-azure-region-to-another)中的指导进行操作。

## <a name="recover-from-a-failed-azure-region"></a>从失败的 Azure 区域恢复

如果整个 Azure 区域由于灾难性事件（如地震）而发生故障，则该区域中的所有 Azure 服务（包括 Azure Stack Edge 服务）都将进行故障转移。 由于存在多个服务，因此包含的路由可以轻松地分为几个。 Azure 限制为400个路由。 

当区域发生故障转移时，虚拟网络 (Vnet) 也会故障转移到新区域，因此虚拟网络网关 (VPN 网关) 。 若要解决此更改，请在 Azure Stack Edge VPN 配置中进行以下更改：

1. 将 Vnet 移到目标区域。 有关详细信息，请参阅： [通过 Azure 门户将 Azure 虚拟网络移到另一个区域](../virtual-network/move-across-regions-vnet-portal.md)。
2. 在移动 Vnet 的目标区域中部署新的 Azure VPN 网关。 有关详细信息，请参阅 [创建虚拟网络网关](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)。
3. 更新 Azure Stack Edge VPN 配置以在 VPN 连接中使用上述 VPN 网关，然后选择目标区域以添加使用 VPN 网关的路由。
4. 如果客户端地址池也发生更改，请更新传入的 Azure 路由表。 

## <a name="move-from-an-azure-region-to-another"></a>从 Azure 区域移到另一个区域

可以将 Azure Stack Edge 设备从一个位置移到另一个位置。 若要使用离设备部署位置最近的区域，则需要为新的主区域配置设备。 进行以下更改：

1. 你可以更新 Azure Stack Edge VPN 配置以使用新区域的 VPN 网关，并选择新的区域来添加使用 VPN 网关的路由。

## <a name="next-steps"></a>后续步骤

[备份 Azure Stack Edge 设备](azure-stack-edge-gpu-prepare-device-failure.md)。