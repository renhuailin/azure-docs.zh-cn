---
title: 在 Azure VMware 解决方案中配置 NSX 网络组件
description: 了解如何使用 Azure VMware 解决方案控制台配置 NSX-T 网段。
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: dbed29fb1063b78386f9ec1e2ee00d9c685a944e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417109"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>在 Azure VMware 解决方案中配置 NSX 网络组件

默认情况下，Azure VMware 解决方案私有云作为软件定义的网络 (SDDC) 。 它在主动/主动模式下预配了一个 NSX-T 层-0 网关，并在主动/备用模式下提供默认的 NSX-T 层网关。  这些网关使你能够将段连接 (逻辑交换机) 并提供 East-West 和 North-South 连接性。 

部署 Azure VMware 解决方案私有云后，可以在 **工作负荷网络** 下的 Azure vmware 解决方案控制台中配置必要的 NSX-T 对象。  该控制台提供了一种简化的 NSX-T 操作视图，VMware 管理员每天都需要这些操作，并以不熟悉 NSX-T 的用户为目标。  

你将有四个选项可用于在 Azure VMware 解决方案控制台中配置 NSX 组件：
- **段** -创建在 "NSX-T 管理器" 和 "vCenter" 中显示的段。
- **Dhcp** -如果计划使用 dhcp，请创建 dhcp 服务器或 dhcp 中继。
- **端口镜像** –创建端口镜像以帮助对网络问题进行故障排除。
- **Dns** –创建 dns 转发器，将 dns 请求发送到指定的 dns 服务器进行解析。  

>[!NOTE]
>你仍可以访问 NSX-T 管理器控制台，你可以在其中使用所述的高级设置和其他 NSX-T 功能。 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="显示用于配置 NSX-T 的 Azure VMware 解决方案控制台中四个选项的屏幕截图。":::

## <a name="prerequisites"></a>先决条件
) 创建或迁移到 Azure VMware 解决方案私有云的虚拟机 (应附加到一个段。 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>在 Azure 门户中创建 NSX 段
可以在 Azure 门户的 Azure VMware 解决方案控制台中创建和配置 NSX-T 段。  这些段连接到默认的第1层网关，这些段中的工作负荷将 East-West 并 North-South 连接。 创建段后，它将显示在 "NSX-T 管理器" 和 "vCenter" 中。

>[!NOTE]
>如果计划使用 DHCP，则需要先 [配置 dhcp 服务器或 dhcp 中继](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) ，然后才能创建和配置 NSX-T 段。

1. 在 Azure VMware 解决方案私有云的 "**工作负荷网络**" 下，选择 "**段**" "  >  **添加**"。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="显示如何添加新段的屏幕截图。":::

1. 提供新逻辑段的详细信息。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/create-new-segment-details.png" alt-text="显示新段的详细信息的屏幕截图。":::
   
   - **段名称** -vCenter 中可见的逻辑交换机的名称。
   - **子网网关** -带有子网掩码的逻辑交换机子网的网关 IP 地址。 Vm 连接到逻辑交换机，连接到此交换机的所有 Vm 都属于同一子网。  而且，附加到此逻辑段的所有 Vm 必须从同一段传输 IP 地址。
   - **Dhcp** (可选) -逻辑段的 dhcp 范围。 [Dhcp 服务器或 dhcp 中继](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal)必须配置为在分段上使用 dhcp。  
   - **连接的网关**  - *默认情况下选择 "只读"。*  第1层网关和段信息的类型。 
      - **T1** -NSX-T Manager 中第1层网关的名称。 Azure VMware 解决方案私有云随附主动/主动模式下的 NSX T 层0网关，并在主动/备用模式下提供默认的 NSX-T 层级网关。  通过 Azure VMware 解决方案控制台创建的段只连接到默认的第1层网关，这些段的工作负荷 East-West 和 North-South 连接。 只能通过 NSX-T Manager 创建更多第1层网关。 从 NSX-T Manager 控制台创建的第1层网关在 Azure VMware 解决方案控制台中不可见。 
      - Azure VMware 解决方案支持的 **类型** 覆盖段。

1. 选择 **"确定"** 以创建段并将其附加到第1层网关。 

   此段现在可在 Azure VMware 解决方案控制台、NSX-T 管理器和 vCenter 中看到。

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>在 Azure 门户中创建 DHCP 服务器或 DHCP 中继
可以直接从 Azure 门户中的 Azure VMware 解决方案控制台创建 DHCP 服务器或中继。 DHCP 服务器或中继连接到第1层网关，该网关是在部署 Azure VMware 解决方案时创建的。 你为 DHCP 范围提供的所有段都将属于此 DHCP。  创建 DHCP 服务器或 DHCP 中继之后，必须在段级别定义子网或范围才能使用它。

1. 在 Azure VMware 解决方案私有云的 "**工作负荷网络**" 下，选择 " **DHCP**  >  **添加**"。

2. 选择 " **Dhcp 服务器** " 或 " **dhcp 中继** "，然后提供服务器或中继和三个 IP 地址的名称。 

   >[!NOTE]
   >对于 DHCP 中继，成功配置只需要一个 IP 地址。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="显示如何在 Azure VMware 解决方案中添加 DHCP 服务器或 DHCP 中继的屏幕截图。":::

4. 通过 [在逻辑段上提供 dhcp 范围](#create-an-nsx-t-segment-in-the-azure-portal) 来完成 dhcp 配置，然后选择 **"确定"**。
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>在 Azure 门户中配置端口镜像
可以配置端口镜像来监视网络流量，该流量涉及到将每个数据包的副本从一个网络交换机端口转发到另一个端口。 此选项将协议分析器置于接收镜像数据的端口上。 它会分析来自源、VM 或一组 Vm 的流量，然后将流量发送到定义的目标。 

若要在 Azure VMware 解决方案控制台中设置端口镜像，需执行以下操作：

* [步骤1。创建源和目标 Vm 或 VM 组](#step-1-create-source-and-destination-vms-or-vm-groups) –源组具有一个或多个 vm，其中的流量是镜像的。

* [步骤2。创建端口镜像配置文件](#step-2-create-a-port-mirroring-profile) –为源和目标 VM 组定义流量方向。

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>步骤 1。 创建源和目标 Vm 或 VM 组

在此步骤中，你将创建一个源 VM 组和一个目标 VM 组。

1. 在 Azure VMware 解决方案私有云的 "**工作负荷网络**" 下，选择 "**端口镜像**  >  **VM 组**" "  >  **添加**"。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="显示如何为端口镜像创建 VM 组的屏幕截图。":::

1. 为新 VM 组提供名称，从列表中选择所需 Vm，然后单击 **"确定"**。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="显示要添加到 VM 组的 Vm 列表的屏幕截图。":::

1. 重复上述步骤以创建目标 VM 组。

### <a name="step-2-create-a-port-mirroring-profile"></a>步骤 2。 创建端口镜像配置文件

在此步骤中，你将为源和目标 VM 组的流量方向定义一个配置文件。

>[!NOTE]
>请确保已创建源 VM 和目标 VM 组。

1. 选择 "**端口镜像**" "  >  **添加**"，然后提供：

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="显示端口镜像配置文件所需的信息的屏幕截图。":::

   - **端口镜像名称** -配置文件的描述性名称。
   - **方向** -从入口、出口或双向中进行选择。
   - **源** -选择源 VM 组。
   - **目标** -选择目标 VM 组。
   - **说明** -为端口镜像输入描述。

1. 选择 **"确定"** 完成配置文件。 

   配置文件和 VM 组在 Azure VMware 解决方案控制台中可见。

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>在 Azure 门户中配置 DNS 转发器
配置 DNS 转发器，其中特定的 DNS 请求将转发到指定的 DNS 服务器进行解析。  DNS 转发器与 **默认 dns 区域** 和最多三个 **FQDN 区域** 关联。

>[!TIP]
>你还可以使用 " [NSX-T 管理器控制台" 来配置 DNS 转发器](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html)。

若要在 Azure VMware 解决方案控制台中设置 DNS 转发器，请执行以下操作：

* [步骤1。配置默认 DNS 区域和 FQDN 区域](#step-1-configure-a-default-dns-zone-and-fqdn-zone) –接收 dns 查询时，dns 转发器会将域名与 FQDN DNS 区域中的域名进行比较。 

* [步骤2。配置 DNS 服务](#step-2-configure-dns-service) -将配置 dns 转发器服务。

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>步骤 1。 配置默认 DNS 区域和 FQDN 区域
你将配置默认 DNS 区域和 FQDN 区域，以便将 DNS 查询发送到上游服务器。  收到 DNS 查询时，DNS 转发器会将查询中的域名与 FQDN DNS 区域的域名进行比较。 如果找到匹配项，则会将查询转发到 FQDN DNS 区域中指定的 DNS 服务器。 如果未找到匹配项，则会将查询转发到默认 DNS 区域中指定的 DNS 服务器。

>[!NOTE]
>必须先定义默认 DNS 区域，然后才能配置 FQDN 区域。 

1. 在 Azure VMware 解决方案私有云的 "**工作负荷网络**" 下，选择 " **dns**  >  **dns 区域**  >  **添加**"。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="显示如何添加 DNS 区域和 DNS 服务的屏幕截图。":::

1. 选择 " **默认 DNS 区域** " 并提供：

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="显示如何添加默认 DNS 区域的屏幕截图。":::

   1. DNS 区域的名称。

   1. 格式为 **8.8.8.8** 的最多三个 DNS 服务器 IP 地址。

1. 选择 **FQDN 区域** 并提供：

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="显示如何添加 FQDN 区域的屏幕截图。 ":::

   1. DNS 区域的名称。

   1. FQDN 域。

   1. 格式为 **8.8.8.8** 的最多三个 DNS 服务器 IP 地址。

1. 选择 **"确定"** 完成添加默认 DNS 区域和 DNS 服务的操作。

### <a name="step-2-configure-dns-service"></a>步骤 2。 配置 DNS 服务

1. 选择 " **DNS 服务** " 选项卡，选择 " **添加**"，然后提供：

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="显示 DNS 服务所需的信息的屏幕截图。":::

   1. DNS 服务的名称。

   1. 输入 DNS 服务的 IP 地址。

   1. 选择在 "DNS 区域" 选项卡下创建的默认 DNS 区域。

   1. 选择在 "DNS 区域" 选项卡下添加的 FQDN 区域。

   1. 选择 **日志级别**。

   >[!TIP]
   >默认情况下会选择 **第1层网关**，并反映部署 Azure VMware 解决方案时创建的网关。

1. 选择“确定”。 

   DNS 服务已成功添加。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="显示 DNS 服务成功添加的屏幕截图。":::

