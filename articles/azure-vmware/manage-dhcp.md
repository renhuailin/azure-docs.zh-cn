---
title: 管理 Azure VMware 解决方案的 DHCP
description: 了解如何创建和管理 Azure VMware 解决方案私有云的 DHCP。
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2020
ms.locfileid: "97708545"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>管理 Azure VMware 解决方案的 DHCP

在私有云环境中运行的应用程序和工作负荷需要 DHCP 服务进行 IP 地址分配。  本文介绍如何通过两种方式在 Azure VMware 解决方案中创建和管理 DHCP：

- 如果你使用 NSX-T 来托管 DHCP 服务器，则需要[创建 DHCP 服务器](#create-a-dhcp-server)并[中继到该服务器](#create-dhcp-relay-service)。 创建 DHCP 服务器时，还将添加一个网络段并指定 DHCP IP 地址范围。   

- 如果你在网络中使用第三方外部 DHCP 服务器，则需要[创建 DHCP 中继服务](#create-dhcp-relay-service)。 当你创建到 DHCP 服务器的中继时，无论是使用 NSX 还是第三方来托管你的 DHCP 服务器，你都需要指定 DHCP IP 地址范围。

>[!IMPORTANT]
>当 DHCP 服务器位于本地数据中心时，DHCP 不适用于 VMware HCX L2 stretch 网络上 (Vm) 的虚拟机。  默认情况下，NSX 会阻止所有 DHCP 请求遍历 L2 stretch。 有关解决方案，请参阅 [向本地 dhcp 服务器发送 dhcp 请求](#send-dhcp-requests-to-the-on-premises-dhcp-server) 过程。


## <a name="create-a-dhcp-server"></a>创建 DHCP 服务器

如果要使用 NSX 来托管 DHCP 服务器，请创建 DHCP 服务器。 然后，你将添加一个网络段并指定 DHCP IP 地址范围。

1. 在 "NSX-T 管理器" 中，选择 "**网络**  >  **DHCP**"，然后选择 "**添加服务器**"。

1. 为 "**服务器类型**" 选择 " **DHCP** "，提供服务器名称和 IP 地址，然后选择 "**保存**"。

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="添加 DHCP 服务器" border="true":::

1. 选择 **第1层网关**，选择第1层网关上的垂直省略号，然后选择 " **编辑**"。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="选择要使用的网关" border="true":::

1. 选择 " **无 IP 分配设置** " 以添加子网。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="添加子网" border="true":::

1. 对于 " **类型**"，选择 " **DHCP 本地服务器**"。 
   
1. 对于 **DHCP 服务器**，选择 " **默认 DHCP**"，然后选择 " **保存**"。

1. 再次选择 " **保存** "，然后选择 " **关闭编辑**"。

### <a name="add-a-network-segment"></a>添加网段

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>创建 DHCP 中继服务

如果要使用第三方外部 DHCP 服务器，则需要创建 DHCP 中继服务。 还将在 NSX-T Manager 中指定 DHCP IP 地址范围。 

1. 在 "NSX-T 管理器" 中，选择 "**网络**  >  **DHCP**"，然后选择 "**添加服务器**"。

1. 对于 "**服务器类型**" 选择 " **DHCP 中继**"，提供服务器名称和 IP 地址，然后选择 "**保存**"。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="创建 dhcp 中继服务" border="true":::

1. 选择 **第1层网关**，选择第1层网关上的垂直省略号，然后选择 " **编辑**"。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="编辑第1层网关" border="true":::

1. 选择 " **没有 Ip 分配集** "，以定义 ip 地址分配。

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="编辑 ip 地址分配" border="true":::

1. 对于 " **类型**"，选择 " **DHCP 服务器**"。 
   
1. 对于 **Dhcp 服务器**，选择 " **dhcp 中继**"，然后选择 " **保存**"。

1. 再次选择 " **保存** "，然后选择 " **关闭编辑**"。


## <a name="specify-the-dhcp-ip-address-range"></a>指定 DHCP IP 地址范围

1. 在 NSX-T Manager 中，选择“网络” > “段” 。 
   
1. 选择段名称上的垂直省略号，然后选择 " **编辑**"。
   
1. 选择 " **设置子网** " 以指定子网的 DHCP IP 地址。 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="网段" border="true":::
      
1. 修改网关 IP 地址（如果需要），然后输入 DHCP 范围 IP。 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="编辑子网" border="true":::
      
1. 选择 " **应用**"，然后单击 " **保存**"。 将为该段分配一个 DHCP 服务器池。
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="分配给分段的 DHCP 服务器池" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>将 DHCP 请求发送到本地 DHCP 服务器

如果要将来自 L2 扩展段上的 Azure VMware 解决方案 Vm 的 DHCP 请求发送到本地 DHCP 服务器，则需要创建安全段配置文件。 

1. 登录到你的本地 vCenter，然后在“主页”下选择“HCX”。

1. 选择 "**服务**" 下的 **网络扩展**。

1. 选择你想要支持从 Azure VMware 解决方案到本地的 DHCP 请求的网络扩展。 

1. 记下 "目标网络名称"。  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="VMware vSphere 客户端中的网络扩展的屏幕截图" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. 在 Azure VMware 解决方案 NSX-T Manager 中，选择 "**网络**  >  **分段**"  >  **部分配置文件**。 

1. 选择 " **添加分段配置文件** "，然后选择 " **安全段**"。

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="如何在 NSX 中添加分段配置文件的屏幕截图" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. 提供 "名称" 和 "标记"，然后将 " **BPDU" 筛选器** 切换到 "打开"，所有 DHCP 都切换为 "关闭"。

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="显示 BPDU 筛选器开启并关闭 DHCP 的屏幕截图" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. 删除 **BPDU Filter Allow List** 下的所有 MAC 地址（如果有）。  再选择“保存”。

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="在 BPDU 筛选允许列表中显示 MAC 地址的屏幕截图":::

1. 在 "**网络**  >  **段**" 下  >  ，在 "搜索" 区域中输入定义网络名称。

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="网络 > 段筛选器字段的屏幕截图":::

1. 选择段名称上的垂直省略号，然后选择 " **编辑**"。

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="段的 &quot;编辑&quot; 按钮的屏幕截图" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. 将 **段安全** 更改为之前创建的段配置文件。

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="段安全字段的屏幕截图" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>后续步骤

详细了解 [主机维护和生命周期管理](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)。