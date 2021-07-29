---
title: 在 Azure VMware 解决方案中配置和管理 DHCP
description: 了解如何创建和管理适用于 Azure VMware 解决方案私有云的 DHCP。
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 05/17/2021
ms.openlocfilehash: 28fb995cbc91d6e350e8d295c1f57d62c706e59e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103693"
---
# <a name="configure-and-manage-dhcp-in-azure-vmware-solution"></a>在 Azure VMware 解决方案中配置和管理 DHCP

在私有云环境中运行的应用程序和工作负载，需要使用 DHCP 服务来分配 IP 地址。  本文介绍如何通过两种方式在 Azure VMware 解决方案中创建和管理 DHCP：

- 如果你使用 NSX-T 来托管 DHCP 服务器，则需要[创建 DHCP 服务器](#create-a-dhcp-server)并[中继到该服务器](#create-dhcp-relay-service)。 创建 DHCP 服务器时，还需要添加一个网段并指定 DHCP IP 地址范围。   

- 如果你在网络中使用第三方外部 DHCP 服务器，则需要[创建 DHCP 中继服务](#create-dhcp-relay-service)。 创建到 DHCP 服务器的中继时，无论使用 NSX-T 还是第三方来托管 DHCP 服务器，都需要指定 DHCP IP 地址范围。

>[!IMPORTANT]
>当 DHCP 服务器位于本地数据中心时，DHCP 对于 VMware HCX L2 延伸网络中的虚拟机不起作用。  默认情况下，NSX 会阻止所有 DHCP 请求遍历 L2 延伸网络。 有关解决方案，请参阅[在 L2 延伸 VMware HCX 网络上配置 DHCP](configure-l2-stretched-vmware-hcx-networks.md) 过程。


## <a name="create-a-dhcp-server"></a>创建 DHCP 服务器

如果要使用 NSX-T 来托管 DHCP 服务器，则需创建 DHCP 服务器。 然后，添加一个网段并指定 DHCP IP 地址范围。

1. 在 NSX-T Manager 中，选择“网络” > “DHCP”，然后选择“添加服务器” 。

1. 对于“服务器类型”选择“DHCP”，提供服务器名称和 IP 地址，然后选择“保存”  。

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="添加 DHCP 服务器" border="true":::

1. 选择“第 1 层网关”，选择第 1 层网关上的垂直省略号，然后选择“编辑”。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="选择要使用的网关" border="true":::

1. 选择“无 IP 分配集”以添加子网。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="添加子网" border="true":::

1. 对于“类型”，选择“DHCP 本地服务器”。 
   
1. 对于“DHCP 服务器”，选择“默认 DHCP”，然后选择“保存”。

1. 再次选择“保存”，然后选择“关闭编辑”。

### <a name="add-a-network-segment"></a>添加网段

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>创建 DHCP 中继服务

如果要使用第三方外部 DHCP 服务器，则需要创建 DHCP 中继服务。 此外，还需要在 NSX-T Manager 中指定 DHCP IP 地址范围。 

1. 在 NSX-T Manager 中，选择“网络” > “DHCP”，然后选择“添加服务器” 。

1. 对于“服务器类型”选择“DHCP 中继”，提供服务器名称和 IP 地址，然后选择“保存”  。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="创建 dhcp 中继服务" border="true":::

1. 选择“第 1 层网关”，选择第 1 层网关上的垂直省略号，然后选择“编辑”。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="编辑第 1 层网关" border="true":::

1. 选择“无 IP 分配集”，以定义 IP 地址分配。

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="编辑 ip 地址分配" border="true":::

1. 对于“类型”，选择“DHCP 服务器”。 
   
1. 对于“DHCP 服务器”，选择“DHCP 中继”，然后选择“保存”。

1. 再次选择“保存”，然后选择“关闭编辑”。


## <a name="specify-the-dhcp-ip-address-range"></a>指定 DHCP IP 地址范围

1. 在 NSX-T Manager 中，选择“网络” > “段” 。 
   
1. 选择网段名称上的垂直省略号，然后选择“编辑”。
   
1. 选择“设置子网”，以指定子网的 DHCP IP 地址。 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="网段" border="true":::
      
1. 如果需要，修改网关 IP 地址，然后输入 DHCP IP 范围。 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="编辑子网" border="true":::
      
1. 选择“应用”，然后选择“保存”。 系统将为该网段分配一个 DHCP 服务器池。
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="分配给网段的 DHCP 服务器池" border="true":::



## <a name="next-steps"></a>后续步骤

如果要将 DHCP 请求从 Azure VMware 解决方案 VM 发送到非 NSX-T DHCP 服务器，则需要创建新的安全网段配置文件。 有关解决方案，请参阅[在 L2 延伸 VMware HCX 网络上配置 DHCP](configure-l2-stretched-vmware-hcx-networks.md) 过程。

