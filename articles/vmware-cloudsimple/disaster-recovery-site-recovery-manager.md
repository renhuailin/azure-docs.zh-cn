---
title: Azure VMware Solution by CloudSimple - 通过使用 VMware Site Recovery Manager 将私有云设置为灾难恢复站点
description: 介绍如何通过使用 VMware Site Recovery Manager 将 CloudSimple 私有云设置为灾难恢复站点。
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e62a9e67c7bc29143e495b983a232b48e3d353a9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612876"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>使用 VMware Site Recovery Manager 将私有云设置为灾难恢复目标

可以将 CloudSimple 私有云用作本地 VMware 工作负载的灾难恢复 (DR) 站点。

DR 解决方案基于 vSphere 复制和 VMware Site Recovery Manager (SRM)。 可以遵循类似的方法，将私有云启用为受本地恢复站点保护的主站点。

CloudSimple 解决方案的优点如下：

* 无需为 DR 设置数据中心。
* 可以利用为全球地理复原能力而部署了 CloudSimple 的 Azure 位置。
* 有机会降低用于建立 DR 的部署成本和总拥有成本。

CloudSimple 解决方案需要执行以下操作：

* 在私有云中安装、配置和管理 vSphere 复制和 SRM。
* 在私有云是受保护的站点时，为 SRM 提供你自己的许可证。 在 CloudSimple 站点用作恢复站点时，不需要该站点的任何其他 SRM 许可证。

使用此解决方案，可以完全控制 vSphere 复制和 SRM。 利用熟悉的 UI、API 和 CLI 接口，可以使用现有的脚本和工具。

![Site Recovery Manager 部署](media/srm-deployment.png)

可以使用与私有云和本地环境兼容的任何版本的 vRA 和 SRM。 本指南中的示例使用 vRA 6.5 和 SRM 6.5。 这些版本与 CloudSimple 支持的 vSphere 6.5 兼容。

## <a name="deploy-the-solution"></a>部署解决方案

以下部分介绍如何在私有云中使用 SRM 来部署 DR 解决方案。

1. [验证 VMware 产品版本是否兼容](#verify-that-vmware-product-versions-are-compatible)
2. [估计 DR 环境的大小](#estimate-the-size-of-your-dr-environment)
3. [为环境创建私有云](#create-a-private-cloud-for-your-environment)
4. [为 SRM 解决方案设置私有云网络](#set-up-private-cloud-networking-for-the-srm-solution)
5. [设置本地网络与私有云之间的站点到站点 VPN 连接并打开所需端口](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [在私有云中设置基础结构服务](#set-up-infrastructure-services-in-your-private-cloud)
7. [在本地环境中安装 vSphere 复制设备](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [在私有云环境中安装 vSphere 复制设备](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [在本地环境中安装 SRM 服务器](#install-srm-server-in-your-on-premises-environment)
10. [在私有云中安装 SRM 服务器](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>验证 VMware 产品版本是否兼容

本指南中的配置受以下兼容性要求的影响：

* 必须在私有云和本地环境中部署相同的 SRM 版本。
* 必须在私有云和本地环境中部署相同的 vSphere 复制的版本。
* 私有云和本地环境中的 Platform Services Controller (PSC) 版本必须兼容。
* 私有云和本地环境中的 vCenter 版本必须兼容。
* SRM 和 vSphere 复制的版本必须相互兼容，并且必须与 PSC 和 vCenter 的版本兼容。

有关 VMware 文档和兼容性信息的链接，请转到 [VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) 文档。

若要查看私有云中 vCenter 和 PSC 的版本，请打开 CloudSimple 门户。 转到“资源”，选择私有云，然后单击“vSphere 管理网络”选项卡 。

![私有云中的 vCenter 和 PSC 版本](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>估计 DR 环境的大小

1. 验证是否已确定本地配置处于受支持的限制之内。 对于 SRM 6.5，这些限制记录在有关 [Site Recovery Manager 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)的 VMware 知识库文章中。
2. 请确保你有足够的网络带宽来满足工作负载大小和 RPO 要求。 有关[计算 vSphere 复制的带宽要求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)的 VMware 知识库文章提供了有关带宽限制的指导。
3. 使用 CloudSimple sizer 工具来估算 DR 站点用于保护本地环境所需的资源。

### <a name="create-a-private-cloud-for-your-environment"></a>为环境创建私有云

按照[创建私有云](create-private-cloud.md)中的说明和大小建议从 CloudSimple 门户创建私有云。

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>为 SRM 解决方案设置私有云网络

访问 CloudSimple 门户，为 SRM 解决方案设置私有云网络。

为 SRM 解决方案创建 VLAN，并为它分配子网 CIDR。 有关说明，请参阅[创建和管理 VLAN/子网](create-vlan-subnet.md)。

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>设置本地网络与私有云之间的站点到站点 VPN 连接并打开所需端口

设置本地网络和私有云之间的站点到站点连接。 有关说明，请参阅[配置到 CloudSimple 私有云的 VPN 连接](set-up-vpn.md)。

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>在私有云中设置基础结构服务

在私有云中配置基础结构服务，以便于管理工作负载和工具。

如果需要执行以下任一操作，可按[使用 Azure AD 作为 CloudSimple 私有云上 vCenter 的标识提供者](azure-ad.md)中所述来添加外部标识提供者：

* 在私有云中确定本地 Active Directory (AD) 中的用户。
* 在私有云中为所有用户设置 AD。
* 使用 Azure AD。

若要为私有云中的工作负载提供 IP 地址查找、IP 地址管理和名称解析服务，请按[在 CloudSimple 私有云中设置 DNS 和 DHCP 应用程序和工作负载](dns-dhcp-setup.md)中的说明设置 DHCP 和 DNS 服务器。

*. cloudsimple.io 域由私有云中的管理 VM 和主机使用。 若要请求解析到此域，请按[创建条件转发器](on-premises-dns-setup.md#create-a-conditional-forwarder)中的说明在 DNS 服务器上配置 DNS 转发。

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>在本地环境中安装 vSphere 复制设备

通过按照 VMware 文档的说明操作，在本地环境中安装 vSphere 复制设备 (vRA)。 安装包括以下主要步骤：

1. 准备用于 vRA 安装的本地环境。

2. 使用 vmware.com 提供的 VR ISO 中的 OVF 在本地环境中部署 vRA。 对于 vRA 6.5，[这篇 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)中提供了相关信息。

3. 在本地站点向 vCenter 单一登录注册本地 vRA。 有关 vSphere 复制 6.5 的详细说明，请参阅 VMware 文档 [VMware vSphere 复制 6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)。

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>在私有云环境中安装 vSphere 复制设备

在开始之前，请验证您具有以下各项：

* 从本地环境中的子网到私有云的管理子网的 IP 可访问性
* 从本地 vSphere 环境中的复制子网到私有云的 SRM 解决方案子网的 IP 可访问性

有关说明，请参阅[配置到 CloudSimple 私有云的 VPN 连接](set-up-vpn.md)。 这些步骤类似于本地安装的步骤。

CloudSimple 建议在 vRA 和 SRM 安装过程中使用 FQDN 而不使用 IP 地址。 若要查看私有云中 vCenter 和 PSC 的版本，请打开 CloudSimple 门户。 转到“资源”，选择私有云，然后单击“vSphere 管理网络”选项卡 。

![在私有云中查找 vCenter/PSC 的 FQDN](media/srm-resources.png)

CloudSimple 要求你不要使用默认的“cloudowner”用户来安装 vRA 和 SRM，而是要创建新用户。 这是为了帮助确保私有云 vCenter 环境能够长时间正常运行并且可用。 但是，私有云 vCenter 中的默认 cloudowner 用户没有足够的权限来创建具有管理员特权的新用户。

在安装 vRA 和 SRM 之前，必须先升级 cloudowner 用户的 vCenter 特权，然后再在 vCenter SSO 域中创建具有管理员特权的用户。 有关默认私有云用户和权限模型的详细信息，请参阅[了解私有云权限模型](learn-private-cloud-permissions.md)。

安装包括以下主要步骤：

1. [提升特权](escalate-private-cloud-privileges.md)。
2. 在私有云中为 vSphere 复制和 SRM 安装创建用户。 下面的 [vCenter UI：在私有云中为 vRA 和 SRM 安装创建用户](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)中提供了说明。
3. 准备私有云环境以进行 vRA 安装。
4. 使用 vmware.com 提供的 VR ISO 中的 OVF 在私有云中部署 vRA。 对于 vRA 6.5，[这篇 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)中提供了相关信息。
5. 为 vRA 配置防火墙规则。 下面的 [CloudSimple 门户：为 vRA 配置防火墙规则](#cloudsimple-portal-configure-firewall-rules-for-vra)中提供了说明。
6. 在私有云网站向 vCenter 单一登录注册私有云 vRA。
7. 配置两个设备之间的 vSphere 复制连接。 确保在防火墙中打开所需端口。 有关必须为 vSphere 复制 6.5 打开的端口号的列表，请参阅[这篇 VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

有关 vSphere 复制 6.5 的详细安装说明，请参阅 VMware 文档 [VMware vSphere 复制 6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI：在私有云中为 vRA 和 SRM 安装创建用户

在从 CloudSimple 门户提升特权后，使用 cloudowner 用户凭据登录到 vCenter。

在 vCenter 中创建新用户 `srm-soln-admin` 并将其添加到 vCenter 中的管理员组。
以 cloudowner 用户的身份注销 vCenter，并以 srm-soln-admin 用户身份登录。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple 门户：为 vRA 配置防火墙规则

按照[设置防火墙表和规则](firewall.md)中的说明配置防火墙规则来打开端口，使以下各方之间能够通信：

* SRM 解决方案网络中的 vRA 与管理网络中的 vCenter 和 ESXi 主机。
* 两个站点的 vRA 设备。

有关必须为 vSphere 复制 6.5 打开的端口号的列表，请参阅[这篇 VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

### <a name="install-srm-server-in-your-on-premises-environment"></a>在本地环境中安装 SRM 服务器

在开始之前，请先验证是否具备以下条件：

* 在本地和私有云环境中安装了 vSphere 复制设备。
* 两个站点的 vSphere 复制设备都已相互连接。
* 你已查看了有关先决条件和最佳做法的 VMware 信息。 对于 SRM 6.5，可参考 VMware 文档 [SRM 6.5 的先决条件和最佳做法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

按照这篇 [VMWare 文档](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)中的说明，在“每个 Platform Services Controller 一个 vCenter 实例的双站点拓扑”部署模型中遵循 VMware 文档执行 SRM 服务器安装。 VMware 文档[安装 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html) 中提供了 SRM 6.5 的安装说明。

### <a name="install-srm-server-in-your-private-cloud"></a>在私有云中安装 SRM 服务器

在开始之前，请先验证是否具备以下条件：

* 在本地和私有云环境中安装了 vSphere 复制设备。
* 两个站点的 vSphere 复制设备都已相互连接。
* 你已查看了有关先决条件和最佳做法的 VMware 信息。 对于 SRM 6.5，可参考 [Site Recovery Manager 6.5 服务器安装的先决条件和最佳做法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

以下步骤介绍了私有云 SRM 安装。

1. [vCenter UI：安装 SRM](#vcenter-ui-install-srm)
2. [CloudSimple 门户：为 SRM 配置防火墙规则](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI：配置 SRM](#vcenter-ui-configure-srm)
4. [CloudSimple 门户：解除提升特权](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI：安装 SRM

在使用 srm-soln-admin 凭据登录到 vCenter 后，按照这篇 [VMWare 文档](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)中的说明，在“每个 Platform Services Controller 一个 vCenter 实例的双站点拓扑”部署模型中遵循 VMware 文档执行 SRM 服务器安装。 VMware 文档[安装 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html) 中提供了 SRM 6.5 的安装说明。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple 门户：为 SRM 配置防火墙规则

按照[设置防火墙表和规则](firewall.md)中的说明配置防火墙规则，以允许以下各方之间进行通信：

私有云中的 SRM 服务器和 vCenter/PSC。
两个站点的 SRM 服务器

有关必须为 vSphere 复制 6.5 打开的端口号的列表，请参阅[这篇 VMware 知识库文章](https://kb.vmware.com/s/article/2087769)。

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI：配置 SRM

在私有云中安装了 SRM 后，请按照“VMware Site Recovery Manager 安装”和“配置指南”部分中的说明来执行以下任务。 对于 SRM 6.5，VMware 文档[安装 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html) 中提供了说明。

1. 连接受保护站点和恢复站点上的 Site Recovery Manager 服务器实例。
2. 建立到远程 Site Recovery Manager 服务器实例的客户端连接。
3. 安装 Site Recovery Manager 许可证密钥。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 门户：解除提升特权

若要解除提升特权，请参阅[解除提升特权](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="ongoing-management-of-your-srm-solution"></a>持续管理 SRM 解决方案

你可以完全控制私有云环境中的 vSphere 复制和 SRM 软件，并且应该执行必要的软件生命周期管理。 在更新或升级 vSphere 复制或 SRM 之前，请确保软件的任何新版本都与私有云 vCenter 和 PSC 兼容。

> [!NOTE]
> CloudSimple 当前正在探索用于提供托管 DR 服务的可选方案。 

## <a name="multiple-replication-configuration"></a>多个复制配置

 [基于阵列的复制和 vSphere 复制技术都可以同时用于 SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)。 但是，必须将它们应用到不同的 VM 集（可以通过基于阵列的复制或者通过 vSphere 复制（但不能两种方法都使用）来保护给定的 VM）。 此外，CloudSimple 站点可以配置为多个受保护站点的恢复站点。 有关多站点配置的详细信息，请参阅 [SRM 多站点选项](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)。

## <a name="references"></a>参考

* [VMware Site Recovery Manager 文档](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Site Recovery Manager 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)
* [计算 vSphere 复制的带宽要求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [在部署 vSphere 复制 6.5 时的 OVF 选择](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere 复制 6.5 安装和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5 的先决条件和最佳做法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [双站点拓扑（每个 Platform Services Controller 一个 vCenter Server 实例）中的 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 安装和配置指南](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [关于采用基于阵列的复制与采用 vSphere 复制的 SRM 的 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [有关 SRM 多站点选项的 VMware 博客](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [必须为 vSphere 复制 5.8.x、6.x 和 8 打开的端口号](https://kb.vmware.com/s/article/2147112)
