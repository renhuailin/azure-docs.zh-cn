---
title: Azure VMware Solution by CloudSimple - 配置从本地到 CloudSimple VPN 网关的高可用性
description: 介绍如何配置从本地环境到启用了高可用性的 CloudSimple VPN 网关的高可用性连接
author: shortpatti
ms.author: v-patsho
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ea74aa319bd3cf736a50542e2e8d0285a13646
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577744"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>配置从本地到 CloudSimple VPN 网关的高可用性连接

网络管理员可以配置从其本地环境到 CloudSimple VPN 网关的高可用性 IPsec 站点到站点 VPN 连接。

本指南介绍为 IPsec 站点到站点 VPN 高可用性连接配置本地防火墙的步骤。 详细步骤具体针对本地防火墙的类型。 作为示例，本指南介绍了两种类型的防火墙的步骤：Cisco ASA 和 Palo Alto Networks。

## <a name="before-you-begin"></a>准备阶段

在配置本地防火墙之前，请完成以下任务。

1. 验证你的组织是否已[预配](create-nodes.md)必需的节点并创建了至少一个 CloudSimple 私有云。
2. 在本地网络与 CloudSimple 私有云之间[配置站点到站点 VPN 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。

请参阅 [VPN 网关概述](cloudsimple-vpn-gateways.md)，了解支持的阶段 1 和阶段 2 方案。

## <a name="configure-on-premises-cisco-asa-firewall"></a>配置本地 Cisco ASA 防火墙

本部分中的说明适用于 Cisco ASA 版本 8.4 及更高版本。 在配置示例中，将在 IKEv1 模式下部署和配置 Cisco 自适应安全设备软件版本 9.10。

要使站点到站点 VPN 正常工作，必须在本地 Cisco ASA VPN 网关的外部接口上允许来自 CloudSimple 主公共 IP 和辅助公共 IP（对等 IP）的 UDP 500/4500 和 ESP（IP 协议 50）。

### <a name="1-configure-phase-1-ikev1"></a>1. 配置阶段 1 (IKEv1)

要启用阶段 1 (IKEv1)，请在 Cisco ASA 防火墙中输入以下 CLI 命令。

`crypto ikev1 enable outside`

### <a name="2-create-an-ikev1-policy"></a>2. 创建 IKEv1 策略

创建一个 IKEv1 策略，以定义用于哈希、身份验证、Diffie-Hellman 组、生存期和加密的算法和方法。

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. 创建隧道组

在 IPsec 属性下创建一个隧道组。 配置对等节点 IP 地址和隧道预共享密钥（这些是在[配置站点到站点 VPN 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)时设置的）。

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. 配置阶段 2 (IPsec)

若要配置阶段 2 (IPsec) ，请创建用于定义要加密和隧道化的流量的访问控制列表 (ACL)。 在下面的示例中，相关流量来自从本地子网 (10.16.1.0/24) 到私有云远程子网 (192.168.0.0/24) 的隧道。 如果站点之间存在多个子网，则 ACL 可包含多个条目。

在 Cisco ASA 版本 8.4 及更高版本中，可以创建可用作网络、子网、主机 IP 地址或多个对象的容器的对象或对象组。 为本地和远程子网创建一个对象，并将其用于加密 ACL 和 NAT 语句。

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>将本地子网定义为对象

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>将 CloudSimple 远程子网定义为对象

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>为相关流量配置访问列表

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. 配置转换集

配置转换集 (TS)，它必须包含关键字 ```ikev1```。 TS 中指定的加密和哈希属性必须与 [CloudSimple VPN 网关的默认配置](cloudsimple-vpn-gateways.md)中列出的参数匹配。

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. 配置加密映射

配置加密映射，其中包含以下组件：

* 对等 IP 地址
* 定义的包含相关流量的 ACL
* 转换集

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. 应用加密映射

在外部接口上应用加密映射：

`crypto map mymap interface outside`

### <a name="8-confirm-applicable-nat-rules"></a>8. 确认适用的 NAT 规则

下面是所使用的 NAT 规则。 确保 VPN 流量不会传入任何其他 NAT 规则。

`nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside`

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Cisco ASA 中已建立 IPsec 站点到站点 VPN 的示例输出

阶段 1 输出：

![Cisco ASA 防火墙的阶段 1 输出](media/ha-vpn-connection-cisco-phase1.png)

阶段 2 输出：

![Cisco ASA 防火墙的阶段 2 输出](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>配置本地 Palo Alto Networks 防火墙

本部分中的说明适用于 Palo Alto Networks 版本 7.1 及更高版本。 在此配置示例中，将在 IKEv1 模式下部署并配置 Palo Alto Networks VM 系列软件版本 8.1.0。

要使站点到站点 VPN 正常工作，必须在本地 Palo Alto Networks 网关的外部接口上允许来自 CloudSimple 主公共 IP 和辅助公共 IP（对等 IP）的 UDP 500/4500 和 ESP（IP 协议 50）。

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. 创建主要和辅助隧道接口

登录到 Palo Alto 防火墙，选择“网络” > “接口” > “隧道” > “添加”，配置以下字段，然后单击“确定”。

* 接口名称。 第一个字段会自动填充关键字“tunnel”。 在相邻字段中输入介于 1 到 9999 之间的任意数字。 此接口将用作主隧道接口，用于在本地数据中心和私有云之间传输站点到站点通信。
* 备注。 输入注释以方便识别隧道的用途
* Netflow 配置文件。 保留默认值。
* 配置。将接口分配到：虚拟路由器：选择“默认值”。 
        安全区域：为受信任的 LAN 流量选择区域。 在此示例中，LAN 通信区域的名称是“信任”。
* IPv4。 单击“添加”，然后在你的环境中添加任何不重叠的未使用的 /32 ip 地址，该地址将分配到主隧道接口，并将用于监视隧道（稍后将对此进行说明）。

由于此配置适用于高可用性 VPN，因此需要两个隧道接口：一个主接口和一个辅助接口。 重复前面的步骤以创建辅助隧道接口。 选择其他隧道 ID 和其他未使用的 /32 ip 地址。

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. 为要通过站点到站点 VPN 访问的私有云子网设置静态路由

本地子网访问 CloudSimple 私有云子网需要路由。

选择“网络” > “虚拟路由器” > “默认值” > “静态路由” > “添加”，配置以下字段，然后单击“确定”。

* 名称： 输入任意名称，以便轻松识别路由的用途。
* 目标。 指定要从本地通过 S2S 隧道接口访问的 CloudSimple 私有云子网
* 接口。 从下拉列表中选择在步骤 1（第 2 部分）中创建的主隧道接口。 在此示例中，它是 tunnel.20。
* 下一个跃点。 选择“无”。
* 管理距离。 保留默认值。
* 跃点数。 输入一个介于 1 到 65535 之间的值。 关键是输入的主隧道接口对应的路由的指标要小于辅助隧道接口对应的路由的指标，从而使主隧道接口对应的路由成为首选路由。 如果 tunnel.20 的指标值为 20，而 tunnel.30 的指标值为 30，则优先选择 tunnel.20。
* 路由表。 保留默认值。
* BFD 配置文件。 保留默认值。
* 路径监视。 保持未选中状态。

重复前面的步骤，通过辅助隧道接口为私有云子网创建另一个路由作为辅助/备份路由。 这一次，请为主路由选择不同的隧道 ID 和更高的指标。

### <a name="3-define-the-cryptographic-profile"></a>3. 定义加密配置文件

定义一个加密配置文件，该配置文件指定用于在 IKEv1 阶段 1 中设置 VPN 隧道的标识、身份验证和加密的协议和算法。

选择“网络” > “展开网络配置文件” > “IKE 加密” > “添加”，配置以下字段，然后单击“确定”。

* 名称： 输入任何 IKE 加密配置文件的名称。
* DH 组。 单击“添加”，然后选择相应的 DH 组。
* 加密。 单击“添加”，然后选择相应的加密方法。
* 身份验证。 单击“添加”，然后选择相应的身份验证方法。
* 密钥生存期。 保留默认值。
* IKEv2 多重身份验证。 保留默认值。

### <a name="4-define-ike-gateways"></a>4. 定义 IKE 网关

定义 IKE 网关，以在每个 VPN 隧道端之间建立对等节点之间的通信。

选择“网络” > “展开网络配置文件” > “IKE 网关” > “添加”，配置以下字段，然后单击“确定”。

“常规”选项卡：

* 名称： 输入要与主 CloudSimple VPN 对等机对等互连的 IKE 网关的名称。
* 版本。 选择“仅限 IKEv1 模式”。
* 地址类型。 选择“IPv4”。
* 接口。 选择面向公众的接口或外部接口。
* 本地 IP 地址。 保留默认值。
* 对等 IP 地址类型。 选择“IP”。
* 对等地址。 输入主 CloudSimple VPN 对等节点 IP 地址。
* 身份验证。 选择“预共享密钥”。
* 预共享密钥/确认预共享密钥。 输入预共享密钥，使其与 CloudSimple VPN 网关密钥匹配。
* 本地标识。 输入本地 Palo Alto 防火墙的公共 IP 地址。
* 对等标识。 输入主 CloudSimple VPN 对等节点 IP 地址。

“高级选项”选项卡：

* 启用“被动模式”。 保持未选中状态。
* 启用“NAT 遍历”。 如果本地 Palo Alto 防火墙不在任何 NAT 设备后面，则保留未选中状态。 否则，请选中该复选框。

IKEv1：

* 交换模式。 选择“主要”。
* IKE 加密配置文件。 选择之前创建的 IKE 加密配置文件。 使“启用碎片”框保持未选中状态。
* 死对等体检测。 将该复选框保留为未选中状态。

重复前面的步骤以创建辅助 IKE 网关。

### <a name="5-define-ipsec-crypto-profiles"></a>5. 定义 IPSEC 加密配置文件

选择“网络” > “展开网络配置文件” > “IPSEC 加密” > “添加”，配置以下字段，然后单击“确定”。

* 名称： 输入 IPsec 加密配置文件的名称。
* IPsec 协议。 选择“ESP”。
* 加密。 单击“添加”，然后选择相应的加密方法。
* 身份验证。 单击“添加”，然后选择相应的身份验证方法。
* DH 组。 选择“no-pfs”。
* 生存期。 设置为 30 分钟。
* 启用。 将该复选框保留为未选中状态。

重复前面的步骤以创建另一个 IPsec 加密配置文件，该配置文件将用作辅助 CloudSimple VPN 对等机。 同一 IPSEC 加密配置文件也可以同时用于主辅 IPsec 隧道（参见以下过程）。

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. 为隧道监视定义监视器配置文件

选择“网络” > “展开网络配置文件” > “监视” > “添加”，配置以下字段，然后单击“确定”。

* 名称： 输入要用于隧道监视的监视器配置文件的任何名称，以主动响应故障。
* 操作。 选择“故障转移”。
* 间隔。 输入值 3。
* 阈值。 输入值 7。

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. 设置主要和辅助 IPsec 隧道。

选择“网络” > “IPsec 隧道” > “添加”，配置以下字段，然后单击“确定”。

“常规”选项卡：

* 名称： 输入要与主 CloudSimple VPN 对等机对等互连的主 IPSEC 隧道的名称。
* 隧道接口。 选择主隧道接口。
* 键入 。 保留默认值。
* 地址类型。 选择“IPv4”。
* IKE 网关。 选择主 IKE 网关。
* IPsec 加密配置文件。 选择主 IPsec 配置文件。 选择“显示高级选项”。
* 启用“重放保护”。 保留默认值。
* 复制 TOS 标头。 将该复选框保留为未选中状态。
* 隧道监视。 选中对应框。
* 目标 IP。 输入任意 IP 地址，该地址属于允许通过站点到站点连接的 CloudSimple 私有云子网。 请确保 Palo Alto 上的隧道接口（例如 tunnel.20 - 10.64.5.2/32 和 tunnel.30 - 10.64.6.2/32) 可通过站点到站点 VPN 访问 CloudSimple 私有云 IP 地址。 请参阅以下配置获取代理 ID。
* 个人资料。 选择监视器配置文件。

“代理 ID”选项卡：单击“IPv4” > “添加”并配置以下各项：

* 代理 ID。 为相关流量输入任意名称。 一个 IPsec 隧道内可能会有多个代理 ID。
* Local。 指定允许通过站点到站点 VPN 与私有云子网通信的本地子网。
* 远程。 指定允许与本地子网通信的私有云远程子网。
* 协议。 选择“任何”。

重复前面的步骤以额外创建一个 IPsec 隧道，用于辅助 CloudSimple VPN 对等机。

## <a name="references"></a>参考

在 Cisco ASA 上配置 NAT：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Cisco ASA 5500 系列配置指南</a>

Cisco ASA 支持的 IKEv1 和 IKEv2 属性：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa72/configuration/guide/conf_gd/ike.html" target="_blank">Cisco ASA 系列 CLI 配置指南</a>

在版本 8.4 和更高版本的 Cisco ASA 上配置 IPsec 站点到站点 VPN：

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">在 ASA 上配置具有 ASDM 或 CLI 的 IKEv1 IPsec 站点到站点隧道</a>

在 Azure 上配置 Cisco 自适应安全设备虚拟 (ASAv)：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Cisco 自适应安全虚拟设备 (ASAv) 快速入门指南</a>

在 Palo Alto 上配置具有代理 ID 的站点到站点 VPN：

[设置站点到站点 VPN](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

设置隧道监视器：

[设置隧道监视](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE 网关或 IPsec 隧道操作：

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">启用/禁用、刷新或重启 IKE 网关或 IPsec 隧道</a>
