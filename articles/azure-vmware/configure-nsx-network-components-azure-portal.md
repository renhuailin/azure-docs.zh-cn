---
title: 在 Azure VMware 解决方案中配置 NSX 网络组件
description: 了解如何使用 Azure VMware 解决方案控制台配置 NSX-T 网段。
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 0478582a9bc4fb77a1784c27ec4f5c302d6b89fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716881"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>在 Azure VMware 解决方案中配置 NSX 网络组件

Azure VMware 解决方案私有云默认带有 NSX-T 作为软件定义的网络 (SDDC)。 它预先配置了主动/主动模式的 NSX-T 第 0 层网关和主动/待机模式的默认 NSX-T 第 1 层网关。  通过这些网关可以将段连接（逻辑交换机）并提供东-西和北-南连接。 

部署 Azure VMware 解决方案私有云后，可以在“工作负荷网络”下的 Azure VMware 解决方案控制台中配置必要的 NSX-T 对象。  该控制台针对不熟悉 NSX-T 的用户提供了 VMware 管理员日常所需 NSX-T 操作的简化视图。  

在 Azure VMware 解决方案控制台中配置 NSX-T 组件时有四个选项：
- “段” - 创建在 NSX-T Manager 和 vCenter 中显示的段。
- “DHCP” - 如果计划使用 DHCP，请创建 DHCP 服务器或 DHCP 中继。
- “端口镜像” – 创建端口镜像以帮助对网络问题进行故障排除。
- “DNS” – 创建 DNS 转发器，将 DNS 请求发送到指定 DNS 服务器进行解析。  

>[!NOTE]
>你仍可以访问 NSX-T Manager 控制台，在其中使用所述的高级设置和其他 NSX-T 功能。 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="显示用于配置 NSX-T 的 Azure VMware 解决方案控制台中的四个选项的屏幕截图。":::

## <a name="prerequisites"></a>先决条件
在 Azure VMware 解决方案私有云上创建的或迁移到其上的虚拟机 (VM) 应附加到段。 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>在 Azure 门户中创建 NSX-T 段
可以在 Azure 门户的 Azure VMware 解决方案控制台中创建和配置 NSX-T 段。  这些段连接到默认的第 1 层网关，段中的工作负荷会获得东-西和北-南连接。 创建段后，它将显示在 NSX-T Manager 和 vCenter 中。

>[!NOTE]
>如果计划使用 DHCP，则需要先[配置 DHCP 服务器或 DHCP 中继](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal)，然后才能创建和配置 NSX-T 段。

1. 在 Azure VMware 解决方案私有云的“工作负荷网络”下，选择“段” > “添加”。 提供新逻辑段的详细信息，然后选择“确定”。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="显示如何添加新段的屏幕截图。":::
   
   - “段名称” - vCenter 中可见的逻辑交换机的名称。
   - “子网网关” - 带有子网掩码的逻辑交换机的子网的网关 IP 地址。 VM 附加到逻辑交换机，连接到此交换机的所有 VM 都属于同一子网。  而且，附加到此逻辑段的所有 VM 必须携带来自同一段的 IP 地址。
   - “DHCP”（可选） - 逻辑段的 DHCP 范围。 必须配置 [DHCP 服务器或 DHCP 中继](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal)，以使用段的 DHCP。  
   - “已连接网关” - 默认情况下已选择且只读。  第 1 层网关和段信息类型。 
      - “T1” - NSX-T Manager 中第 1 层网关的名称。 Azure VMware 解决方案私有云预先配置了主动/主动模式的 NSX-T 第 0 层网关和主动/待机模式的默认 NSX-T 第 1 层网关。  通过 Azure VMware 解决方案控制台创建的段只连接到默认的第 1 层网关，这些段的工作负荷获得东-西和北-南连接。 只能通过 NSX-T Manager 创建更多第 1 层网关。 从 NSX-T Manager 控制台创建的第 1 层网关在 Azure VMware 解决方案控制台中不可见。 
      - “类型” - Azure VMware 解决方案支持的覆盖段。

   此段现在可在 Azure VMware 解决方案控制台、NSX-T Manger 和 vCenter 中看到。

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>在 Azure 门户中创建 DHCP 服务器或 DHCP 中继
可以直接在 Azure 门户中的 Azure VMware 解决方案控制台创建 DHCP 服务器或中继。 DHCP 服务器或中继连接到第 1 层网关，该网关在部署 Azure VMware 解决方案时创建。 所有接受 DHCP 范围的段都将属于此 DHCP。  创建 DHCP 服务器或 DHCP 中继之后，必须在段级别定义子网或范围才能对其进行使用。

1. 在 Azure VMware 解决方案私有云的“工作负荷网络”下，选择“DHCP” > “添加”。

2. 选择“DHCP 服务器”或“DHCP 中继”，然后提供服务器或中继的名称和三个 IP 地址。 

   >[!NOTE]
   >对于 DHCP 中继，只需一个 IP 地址就能成功配置。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="显示如何在 Azure VMware 解决方案中添加 DHCP 服务器或 DHCP 中继的屏幕截图。":::

4. 通过[在逻辑段上提供 DHCP 范围](#create-an-nsx-t-segment-in-the-azure-portal)来完成 DHCP 配置，然后选择“确定”。
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>在 Azure 门户中配置端口镜像
可以配置端口镜像来监视网络流量，其中包括将每个数据包的副本从一个网络交换机端口转发到另一个端口。 此选项将协议分析器置于接收镜像数据的端口上。 它会分析来自源、VM 或一组 VM 的流量，然后将流量发送到定义的目标。 

若要在 Azure VMware 解决方案控制台中设置端口镜像，需执行以下操作：

* [步骤 1。创建源和目标 VM 或 VM 组](#step-1-create-source-and-destination-vms-or-vm-groups) – 源组具有一个或多个 VM，其中的流量是镜像的。

* [步骤 2.创建端口镜像配置文件](#step-2-create-a-port-mirroring-profile) – 为源和目标 VM 组定义流量方向。

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>步骤 1。 创建源和目标 VM 或 VM 组

此步骤将创建源 VM 组和目标 VM 组。

1. 在 Azure VMware 解决方案私有云的“工作负荷网络”下，选择“端口镜像” > “VM 组” > “添加”。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="显示如何为端口镜像创建 VM 组的屏幕截图。":::

1. 为新 VM 组提供名称，从列表中选择所需 VM，然后单击“确定”。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="显示要添加到 VM 组的 VM 列表的屏幕截图。":::

1. 重复上述步骤以创建目标 VM 组。

### <a name="step-2-create-a-port-mirroring-profile"></a>步骤 2. 创建端口镜像配置文件。

此步骤将为源和目标 VM 组的流量方向定义配置文件。

>[!NOTE]
>请确保已创建源和目标 VM 组。

1. 选择“端口镜像” > “添加”，然后提供：

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="显示端口镜像配置文件所需信息的屏幕截图。":::

   - “端口镜像名称” - 配置文件的描述性名称。
   - “方向” - 从入口、出口或双向中进行选择。
   - “源” - 选择源 VM 组。
   - “目标” - 选择目标 VM 组。
   - “说明” - 输入端口镜像的说明。

1. 单击“确定”来完成配置文件。 

   配置文件和 VM 组在 Azure VMware 解决方案控制台中可见。

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>在 Azure 门户中配置 DNS 转发器
配置 DNS 转发器，其中特定的 DNS 请求将转发到指定的 DNS 服务器进行解析。  DNS 转发器与一个“默认 DNS 区域”和最多三个“FQDN 区域” 关联。

>[!TIP]
>还可以使用 [NSX-T Manager 控制台来配置 DNS 转发器](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html)。

若要在 Azure VMware 解决方案控制台中设置 DNS 转发器，请执行以下操作：

* [步骤 1。配置默认 DNS 区域和 FQDN 区域](#step-1-configure-a-default-dns-zone-and-fqdn-zone) – 收到 DNS 查询时，DNS 转发器会将域名与 FQDN DNS 区域中的域名进行比较。 

* [步骤 2.配置 DNS 服务](#step-2-configure-dns-service) - 配置 DNS 转发器服务。

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>步骤 1。 配置默认 DNS 区域和 FQDN 区域
配置默认 DNS 区域和 FQDN 区域，以便将 DNS 查询发送到上游服务器。  收到 DNS 查询时，DNS 转发器会将查询中的域名与 FQDN DNS 区域的域名进行比较。 如果匹配，则会将查询转发到 FQDN DNS 区域中指定的 DNS 服务器。 如果不能匹配，则会将查询转发到默认 DNS 区域中指定的 DNS 服务器。

>[!NOTE]
>必须先定义默认 DNS 区域，然后才能配置 FQDN 区域。 

1. 在 Azure VMware 解决方案私有云的“工作负荷网络”下，选择“DNS” > “DNS区域” > “添加”。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="显示如何添加 DNS 区域和 DNS 服务的屏幕截图。":::

1. 选择“默认 DNS 区域”并提供：

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="显示如何添加默认 DNS 区域的屏幕截图。":::

   1. DNS 区域的名称。

   1. 格式为“8.8.8.8” DNS 服务器 IP 地址，最多可提供三个。

1. 选择“FQDN 区域”并提供：

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="显示如何添加 FQDN 区域的屏幕截图。 ":::

   1. DNS 区域的名称。

   1. FQDN 域。

   1. 格式为“8.8.8.8” DNS 服务器 IP 地址，最多可提供三个。

1. 选择“确定”完成添加默认 DNS 区域和 DNS 服务的操作。

### <a name="step-2-configure-dns-service"></a>步骤 2. 配置 DNS 服务

1. 选择“DNS 服务”选项卡，选择“添加”。 提供详细信息，然后选择“确定”。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="显示 DNS 服务所需信息的屏幕截图。":::

   >[!TIP]
   >默认情况下会选择“第 1 层网关”，并反映部署 Azure VMware 解决方案时创建的网关。

   DNS 服务已成功添加。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="显示 DNS 服务成功添加的屏幕截图。":::

