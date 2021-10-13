---
title: Azure VMware Solution by CloudSimple - 在本地和私有云之间配置 VPN
description: 介绍如何配置本地网络与 CloudSimple 私有云之间的站点到站点或点到站点 VPN 连接
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 55ba51c249b08a6d34b0a213a32b7e3c4d51978a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616087"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>配置到 CloudSimple 私有云的 VPN 连接

使用 VPN 网关，可以从本地网络和客户端计算机远程连接到 CloudSimple 网络。  在本文中，你可以从 CloudSimple 门户了解有关设置 VPN 网关的信息。  本地网络与 CloudSimple 网络之间的 VPN 连接提供对私有云上的 vCenter 和工作负载的访问权限。 CloudSimple 支持点到站点 VPN 和站点到站点 VPN 网关。

## <a name="vpn-gateway-types"></a>VPN 网关类型

* “点到站点 VPN”连接是从计算机连接到私有云的最简单方法。 使用点到站点 VPN 连接远程连接到私有云。
* 使用“站点到站点 VPN”连接，可以设置私有云工作负载以访问本地服务。 用户还可以使用本地 Active Directory 作为身份源，以便向私有云 vCenter 进行身份验证。  目前支持“基于策略的 VPN”类型。

在某个区域中，用户可以创建一个站点到站点 VPN 网关和一个点到站点 VPN 网关。

## <a name="point-to-site-vpn"></a>点到站点 VPN

若要创建点到站点 VPN 网关，请参阅[创建点到站点 VPN 网关](vpn-gateway.md#create-point-to-site-vpn-gateway)。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用点到站点 VPN 连接到 CloudSimple

从计算机连接到 CloudSimple 时需要 VPN 客户端。  下载适用于 Windows 的 [OpenVPN 客户端](https://openvpn.net/community-downloads/)或用于 macOS 和 OS X 的 [Viscosity](https://www.sparklabs.com/viscosity/download/)。

1. 启动 CloudSimple 门户，然后选择“网络”。
2. 选择“VPN 网关”。
3. 从 VPN 网关列表中，单击“点到站点 VPN 网关”。
4. 选择“用户”。
5. 单击“下载我的 VPN 配置”

    ![下载 VPN 配置](media/download-p2s-vpn-configuration.png)

6. 在 VPN 客户端上导入配置

    * 有关[在 Windows 客户端上导入配置](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)的说明
    * 有关[在 macOS 或 OS X 上导入配置](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)的说明

7. 连接到 CloudSimple VPN 网关。

下面的示例显示了如何使用 Viscosity 客户端导入连接。

#### <a name="import-connection-on-viscosity-client"></a>在 Viscosity 客户端上导入连接

1. 从下载的 .zip 文件中提取 VPN 配置的内容。

2. 在计算机上打开 Viscosity。

3. 单击该 + 图标，然后选择“导入连接” > “从文件” 。

    ![无法从文件导入 VPN 配置](media/import-p2s-vpn-config.png)

4. 选择要使用的协议的 OpenVPN 配置文件 (.ovpn)，然后单击“打开”。

    ![突出显示可选择的 OpenVPN 配置文件的屏幕截图。](media/import-p2s-vpn-config-choose-ovpn.png)

此时，该连接将显示在“Viscosity”菜单中。

#### <a name="connect-to-the-vpn"></a>连接到 VPN

若要使用 Viscosity OpenVPN 客户端连接到 VPN，请从菜单中选择该连接。 菜单图标将会更新以指示连接已建立。

![显示 CloudSimple VPN 连接状态的屏幕截图。](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>连接到多个私有云

点到站点 VPN 连接可以解析你创建的第一个私有云的 DNS 名称。 如果要访问其他私有云，则必须更新 VPN 客户端上的 DNS 服务器。

1. 启动 [CloudSimple 门户](access-cloudsimple-portal.md)。

2. 导航到“资源” > “私有云” ，然后选择要连接到的私有云。

3. 在私有云的“摘要”页面上，在“基本信息”下复制私有云 DNS 服务器 IP 地址。

    ![私有云 DNS 服务器](media/private-cloud-dns-server.png)

4. 右键单击计算机系统托盘中的 Viscosity 图标，然后选择“首选项”。

    ![VPN](media/vis00.png)

5. 选择 CloudSimple VPN 连接。

    ![VPN 连接](media/viscosity-client.png)

6. 单击“编辑”以更改连接属性。

    ![编辑 VPN 连接](media/viscosity-edit-connection.png)

7. 单击“网络”选项卡，然后输入私有云 DNS 服务器 IP 地址（以逗号或空格隔开），域为 ```cloudsimple.io```。  选择“忽略 VPN 服务器发送的 DNS 设置”。

    ![VPN 网络](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> 若要连接到第一个私有云，请删除这些设置，然后连接到 VPN 服务器。

## <a name="site-to-site-vpn"></a>站点到站点 VPN

若要创建站点到站点 VPN 网关，请参阅[创建站点到站点 VPN 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。  从本地网络到私有云的站点到站点 VPN 连接提供了这些优势。  

* 本地网络中任何工作站的私有云 vCenter 的可访问性
* 使用本地 Active Directory 作为 vCenter 标识源
* 方便地将 VM 模板、ISO 和其他文件从本地资源传输到私有云 vCenter
* 从本地网络在私有云上运行的工作负荷的可访问性

若要在高可用性模式下设置本地 VPN 网关，请参阅[配置高可用性 VPN 连接](high-availability-vpn-connection.md)。

> [!IMPORTANT]
>    1. 在 VPN 设备上将 TCP MSS 钳位设置为 1200。 或者，如果 VPN 设备不支持 MSS 固定，则可以改为在隧道接口上将 MTU 设置为 1240 字节。
> 2. 设置站点到站点 VPN 后，将对 *.cloudsimple.io 的 DNS 请求转发到私有云 DNS 服务器。  按照[本地 DNS 设置](on-premises-dns-setup.md)中的说明进行操作。
