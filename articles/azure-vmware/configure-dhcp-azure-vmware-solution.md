---
title: 配置适用于 Azure VMware 解决方案的 DHCP
description: 了解如何使用 NSX-T Manager 配置 DHCP，以便托管 DHCP 服务器或使用第三方外部 DHCP 服务器。
ms.topic: how-to
ms.custom: contperf-fy21q2, contperf-fy22q1
ms.date: 07/13/2021
ms.openlocfilehash: d781a7cc0ced6df4f5ad1e562a78f00e023ea10a
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323623"
---
# <a name="configure-dhcp-for-azure-vmware-solution"></a>配置适用于 Azure VMware 解决方案的 DHCP

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]

在本操作方法文章中，你会采用以下两种方法之一，使用 NSX-T Manager 配置适用于 Azure VMware 解决方案的 DHCP： 

- [用于托管 DHCP 服务器的 NSX-T](#use-nsx-t-to-host-your-dhcp-server)

- [第三方外部 DHCP 服务器](#use-a-third-party-external-dhcp-server)

>[!TIP]
>如果要使用对 NSX-T 操作的简化视图来配置 DHCP，请参阅[配置适用于 Azure VMware 解决方案的 DHCP](configure-dhcp-azure-vmware-solution.md)。


>[!IMPORTANT]
>对于在 2021 年 7 月 1 日或之后创建的云，必须使用 NSX-T 操作的简化视图在环境中的默认第 1 层网关上配置 DHCP。
>
>当 DHCP 服务器位于本地数据中心时，DHCP 对于 VMware HCX L2 延伸网络中的虚拟机不起作用。  默认情况下，NSX 会阻止所有 DHCP 请求遍历 L2 延伸网络。 有关解决方案，请参阅[在 L2 延伸 VMware HCX 网络上配置 DHCP](configure-l2-stretched-vmware-hcx-networks.md) 过程。


## <a name="use-nsx-t-to-host-your-dhcp-server"></a>使用 NSX 托管 DHCP 服务器
如果要使用 NSX-T 托管 DHCP 服务器，会创建 DHCP 服务器和中继服务。 然后，添加一个网段并指定 DHCP IP 地址范围。

### <a name="create-a-dhcp-server"></a>创建 DHCP 服务器

1. 在 NSX-T Manager 中，选择“网络” > “DHCP”，然后选择“添加服务器” 。

1. 对于“服务器类型”选择“DHCP”，提供服务器名称和 IP 地址，然后选择“保存”  。

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="显示如何在 NSX-T Manager 中添加 DHCP 服务器的屏幕截图。" border="true":::

1. 选择“第 1 层网关”，选择第 1 层网关上的垂直省略号，然后选择“编辑”。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="显示如何编辑第 1 层网关以便使用 DHCP 服务器的屏幕截图。" border="true":::

1. 选择“无 IP 分配集”以添加子网。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="显示如何将子网添加到第 1 层网关以便使用 DHCP 服务器的屏幕截图。" border="true":::

1. 对于“类型”，选择“DHCP 本地服务器”。 
   
1. 对于“DHCP 服务器”，选择“默认 DHCP”，然后选择“保存”。

1. 再次选择“保存”，然后选择“关闭编辑”。

### <a name="add-a-network-segment"></a>添加网段

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

### <a name="specify-the-dhcp-ip-address-range"></a>指定 DHCP IP 地址范围
 
创建 DHCP 服务器时，还会指定 DHCP IP 地址范围。

>[!NOTE]
>IP 地址范围不应与订阅中的其他虚拟网络以及本地网络中使用的 IP 范围重叠。

1. 在 NSX-T Manager 中，选择“网络” > “段” 。 
   
1. 选择网段名称上的垂直省略号，然后选择“编辑”。
   
1. 选择“设置子网”，以指定子网的 DHCP IP 地址。 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="显示如何设置子网来指定 DHCP IP 地址以便使用 DHCP 服务器的的屏幕截图。" border="true":::
      
1. 如果需要，修改网关 IP 地址，然后输入 DHCP IP 范围。 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="显示使用 DHCP 服务器时的网关 IP 地址和 DHCP 范围的屏幕截图。" border="true":::
      
1. 选择“应用”，然后选择“保存”。 系统将为该网段分配一个 DHCP 服务器池。
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="显示将 DHCP 服务器池分配给网段以便使用 DHCP 服务器的屏幕截图。" border="true":::


## <a name="use-a-third-party-external-dhcp-server"></a>使用第三方外部 DHCP 服务器

如果要使用第三方外部 DHCP 服务器，则会在 NSX-T Manager 中创建 DHCP 中继服务。 还会指定 DHCP IP 地址范围。


>[!IMPORTANT]
>对于在 2021 年 7 月 1 日或之后创建的云，必须使用 NSX-T 操作的简化视图在环境中的默认第 1 层网关上配置 DHCP。


### <a name="create-dhcp-relay-service"></a>创建 DHCP 中继服务

为任何非基于 NSX 的 DHCP 服务使用 DHCP 中继。 例如，在 Azure VMware 解决方案、Azure IaaS 或本地运行 DHCP 的虚拟机。

1. 在 NSX-T Manager 中，选择“网络” > “DHCP”，然后选择“添加服务器” 。

1. 对于“服务器类型”选择“DHCP 中继”，提供服务器名称和 IP 地址，然后选择“保存”  。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="显示如何在 NSX-T Manager 中创建 DHCP 中继服务的屏幕截图。" border="true":::

1. 选择“第 1 层网关”，选择第 1 层网关上的垂直省略号，然后选择“编辑”。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="显示如何编辑第 1 层网关的屏幕截图。" border="true":::

1. 选择“无 IP 分配集”，以定义 IP 地址分配。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="显示如何将子网添加到第 1 层网关的屏幕截图。" border="true":::

1. 对于“类型”，选择“DHCP 服务器”。 
   
1. 对于“DHCP 服务器”，选择“DHCP 中继”，然后选择“保存”。

1. 再次选择“保存”，然后选择“关闭编辑”。


### <a name="specify-the-dhcp-ip-address-range"></a>指定 DHCP IP 地址范围

创建 DHCP 服务器时，还会指定 DHCP IP 地址范围。

>[!NOTE]
>IP 地址范围不应与订阅中的其他虚拟网络以及本地网络中使用的 IP 范围重叠。

1. 在 NSX-T Manager 中，选择“网络” > “段” 。 
   
1. 选择网段名称上的垂直省略号，然后选择“编辑”。
   
1. 选择“设置子网”，以指定子网的 DHCP IP 地址。 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="显示如何设置子网以指定 DHCP IP 地址的屏幕截图。" border="true":::
      
1. 如果需要，修改网关 IP 地址，然后输入 DHCP IP 范围。 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="显示网关 IP 地址和 DHCP 范围的屏幕截图。" border="true":::
      
1. 选择“应用”，然后选择“保存”。 系统将为该网段分配一个 DHCP 服务器池。
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="显示将 DHCP 服务器池分配给网段的屏幕截图。" border="true":::


## <a name="next-steps"></a>后续步骤

如果要将 DHCP 请求从 Azure VMware 解决方案 VM 发送到非 NSX-T DHCP 服务器，请参阅[在 L2 延伸 VMware HCX 网络上配置 DHCP](configure-l2-stretched-vmware-hcx-networks.md)过程。
