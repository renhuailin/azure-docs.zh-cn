---
title: Azure VMware Solution by CloudSimple - 使用私有云站点来托管使用 VMware Horizon 的虚拟桌面基础结构
description: 了解如何使用 CloudSimple 私有云站点来托管使用 VMware Horizon 的虚拟桌面基础结构
author: shortpatti
ms.author: v-patsho
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73435cbc7a630e77f7ed2d7804afd314f68c1a87
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433217"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>使用 CloudSimple 私有云站点来托管使用 VMware Horizon 的虚拟桌面基础结构

你可以使用 CloudSimple 私有云站点来托管使用 VMware Horizon 7.x 的虚拟桌面基础结构 (VDI)。 下图显示了 VDI 的逻辑解决方的体系结构。

![Horizon 部署](media/horizon-deployment.png)

使用此解决方案，你可以完全控制 Horizon View Manager 和应用卷。 利用熟悉的 UI、API 和 CLI 接口，你可以使用现有的脚本和工具。

使用 CloudSimple 解决方案需要进行以下操作：

* 在私有云中安装、配置和管理 VMware Horizon 7.x。
* 提供自己的 Horizon 许可证。

## <a name="deploy-the-solution"></a>部署解决方案

以下部分介绍如何在私有云中使用 Horizon 来部署 VDI 解决方案。

1. [验证 VMware 产品版本是否兼容](#verify-that-vmware-product-versions-are-compatible)
2. [预估桌面环境的大小](#estimate-the-size-of-your-desktop-environment)
3. [为环境创建私有云](#create-a-private-cloud-for-your-environment)
4. [在私有云中安装 VMware Horizon](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>验证 VMware 产品版本是否兼容

* 验证 Horizon、应用卷、统一访问网关和用户环境管理器的当前和计划版本是否相互兼容，以及是否与私有云中的 vCenter 和 PSC 兼容。 有关兼容性信息，请参阅 [Horizon 7.5 的 VMware 兼容性矩阵](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)。
* 若要在私有云中了解 vCenter 和 PSC 的当前版本，请在 [CloudSimple 门户](access-cloudsimple-portal.md)中，转到“资源”，选择私有云，然后单击“vSphere 管理网络”选项卡 。

![vCenter 和 PSC 版本](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>预估桌面环境的大小

* 验证确定的配置是否处于 VMware 运营限制内。
* 预估所有桌面和 Horizon 管理组件所需的资源。

### <a name="create-a-private-cloud-for-your-environment"></a>为环境创建私有云

1. 按照[配置私有云环境](quickstart-create-private-cloud.md)中的说明，从 CloudSimple 门户创建私有云。  CloudSimple 会在新创建的每个私有云中创建一个名为“cloudowner”的默认 vCenter 用户。 有关默认私有云用户和权限模型的详细信息，请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。
2. 在私有云中为 Horizon 管理平面创建 VLAN，并为其分配一个子网 CIDR。 有关说明，请参阅[创建和管理 VLAN/子网](create-vlan-subnet.md)。 这是所有解决方案组件（统一访问网关、连接服务器、应用卷服务器和用户环境管理器服务器）的安装位置。
3. 决定是否要将外部标识提供程序与私有云 vCenter 一起使用。 如果是，选择以下选项之一：
    * 使用本地 Active Directory 作为外部标识提供程序。 有关说明，请参阅 [vCenter 标识源](set-vcenter-identity.md)。
    * 在 Horizon 管理平面 VLAN 的私有云中设置 Active Directory 服务器，并将它用作外部标识提供程序。 有关说明，请参阅 [vCenter 标识源](set-vcenter-identity.md)。
    * 在私有云的 Horizon 管理平面 VLAN 中设置 DHCP 和 DNS 服务器。 有关说明，请参阅[在 CloudSimple 私有云中设置 DNS 和 DHCP 应用程序以及工作负载](dns-dhcp-setup.md)。
4. 在私有云上安装的 DNS 服务器上配置 DNS 转发。 有关说明，请参阅[创建条件转发器](on-premises-dns-setup.md#create-a-conditional-forwarder)。

### <a name="install-vmware-horizon-in-your-private-cloud"></a>在私有云中安装 VMware Horizon

以下部署关系图描述了在私有云中部署的 Horizon 解决方案。 统一访问网关、AD/DC、视图和应用卷服务器安装在用户创建的 VLAN 234 中。 统一访问网关具有可从 Internet 访问的分配的公共 IP 地址。 Horizon 桌面池 VM 部署在 VLAN 235 中，用于提供额外的隔离和安全性。

![私有云中的 Horizon 部署](media/horizon-private-cloud.png)

以下各节概括说明了如何设置与图中描述的部署类似的部署。 在开始之前，请验证您具有以下各项：

* 使用 CloudSimple 门户创建的私有云，具有足够的容量来运行桌面池。
* 在本地环境和私有云环境之间提供足够的带宽，以支持桌面的网络流量。
* 在本地数据中心和私有云之间设置的站点到站点 VPN 隧道。
* 从本地环境中的最终用户子网到 CloudSimple 私有云子网的 IP 可访问性。
* 为私有云安装的 AD/DHCP/DNS。

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple 门户：为桌面池创建专用 VLAN/子网

为 Horizon 桌面池创建 VLAN，并为其分配一个子网 CIDR。 有关说明，请参阅[创建和管理 VLAN/子网](create-vlan-subnet.md)。 这是将在其中运行所有桌面虚拟机的网络。

按照标准的安全最佳做法来保护 Horizon 部署：

* 仅允许桌面 RDP 流量/SSH 流量流向桌面 VM。
* 仅允许 Horizon 管理平面 VLAN 和桌面池 VLAN 之间的管理流量。
* 仅允许来自本地网络的管理流量。

可以通过从 CloudSimple 门户配置[防火墙规则](firewall.md)来强制实施这些最佳做法。

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple 门户：配置防火墙规则来保护 Horizon 管理平面

在 CloudSimple 门户中设置以下规则。 有关说明，请参阅[设置防火墙表和规则](firewall.md)。

1. 在 CloudSimple N-S 防火墙中配置防火墙规则，以允许本地子网和 Horizon 管理 VLAN 之间进行通信，以便仅允许 VMware 文档 [Horizon 端口列表](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html)中列出的网络端口。

2. 在私有云中创建 Horizon 管理 VLAN 和桌面池 VLAN 之间的 E-W 防火墙规则。

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple 门户：为统一访问网关创建公共 IP 地址

为统一访问网关设备创建公共 IP 地址，以启用来自 Internet 的桌面客户端连接。 有关说明，请参阅[分配公共 IP 地址](public-ips.md)。

设置完成后，将分配公共 IP 地址并在公共 IP 页面上列出该地址。

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple 门户：提升特权

默认的“cloudowner”用户在私有云 vCenter 中没有足够的特权来安装 Horizon，因此必须提升用户的 vCenter 特权。 有关详细信息，请参阅[提升特权](escalate-private-cloud-privileges.md)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI：在私有云中为 Horizon 安装创建用户

1. 使用“cloudowner”用户凭据登录到 vCenter。
2. 在 vCenter 中创建新用户“horizon-soln-admin”，并将该用户添加到 vCenter 中的管理员组。
3. 以“cloudowner”用户的身份注销 vCenter，并以“horizon-soln-admin”用户的身份进行登录。

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI：安装 VMware Horizon

如前面的逻辑体系结构部分所述，Horizon 解决方案包含以下组件：

* VMware Horizon 视图
* VMware 统一访问网关
* VMware 应用卷管理器
* VMware 用户环境管理器

按以下步骤安装组件：

1. 按照 VMware 文档[部署和配置 VMware 统一访问网关](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)中提供的说明，安装和配置统一访问网关。

2. 按照[安装指南](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)中的说明，在私有云中安装 Horizon 视图。

3. 按照[安装和配置 VMware 应用卷](https://docs.vmware.com/en/VMware-App-Volumes/4/com.vmware.appvolumes.install.doc/GUID-3F92761D-9F83-4610-978C-4DAA55E07D14.html)中的说明安装应用卷管理器。

4. 按照[关于安装和配置 VMware 用户环境管理器](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)中的说明，安装和配置用户环境管理器。

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>提交支持请求以上传 VMware Horizon 预打包应用卷

在安装过程中，应用卷管理器使用预打包的卷来预配应用堆栈和可写卷。 这些卷用作应用堆栈和可写卷的模板。

将卷上传到私有云数据存储需要 ESXi 根密码。 若要获取帮助，请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 附加 AppVolumes 安装程序捆绑包，以便 CloudSimple 支持人员可以将模板上传到私有云环境。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 门户：解除提升特权

现在，你可以[解除提升](escalate-private-cloud-privileges.md#de-escalate-privileges)“cloudowner”用户的特权。

## <a name="ongoing-management-of-your-horizon-solution"></a>Horizon 解决方案的持续管理

你可以完全控制私有云环境中的 Horizon 和应用卷管理器软件，并有望执行必要的软件生命周期管理。 在更新或升级 Horizon 或应用卷之前，请确保软件的任何新版本都与私有云 vCenter 和 PSC 兼容。
