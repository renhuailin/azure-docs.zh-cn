---
title: Azure VMware Solutions by CloudSimple - 保护私有云
description: 介绍如何保护 Azure VMware Solutions by CloudSimple 私有云
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9ca38542dd794aca6661565d6b4c02fe50d31f7e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616182"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>如何保护私有云环境

为 Azure 的 CloudSimple 服务、CloudSimple 门户和私有云定义基于角色的访问控制 (RBAC)。  使用 VMware SSO 指定用于访问私有云 vCenter 的用户、组和角色。  

## <a name="azure-rbac-for-cloudsimple-service"></a>CloudSimple 服务的 Azure RBAC

创建 CloudSimple 服务需要 Azure 订阅的“所有者”或“参与者”角色。  默认情况下，所有的所有者和参与者都可以创建 CloudSimple 服务，并访问 CloudSimple 门户来创建和管理私有云。  只能为每个区域创建一个 CloudSimple 服务。  若要限制对特定管理员的访问权限，请执行以下过程。

1. 在 Azure 门户上的新资源组中创建 CloudSimple 服务
2. 指定资源组的 Azure RBAC。
3. 购买节点并使用与 CloudSimple 服务相同的资源组

只有对资源组拥有“所有者”或“参与者”权限的用户才会看到 CloudSimple 服务并启动 CloudSimple 门户。

有关详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。

## <a name="rbac-for-private-cloud-vcenter"></a>私有云 vCenter 的 RBAC

创建私有云时，将在 vCenter SSO 域中创建一个默认用户 `CloudOwner@cloudsimple.local`。  CloudOwner 用户具有管理 vCenter 的权限。 其他标识源会添加到 vCenter SSO，以提供对不同用户的访问权限。  预定义的角色和组是在 vCenter 上设置的，可用于添加其他用户。

### <a name="add-new-users-to-vcenter"></a>将新用户添加到 vCenter

1. [提升](escalate-private-cloud-privileges.md)私有云上 CloudOwner\@cloudsimple.local 用户的权限。
2. 使用 CloudOwner\@cloudsimple.local 登录到 vCenter
3. [添加 VCenter 单一登录用户](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)。
4. 将用户添加到 [vCenter 单一登录组](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

有关预定义的角色和组的详细信息，请参阅 [VMware vCenter 的 CloudSimple 私有云权限模型](learn-private-cloud-permissions.md)一文。

### <a name="add-new-identity-sources"></a>添加新的标识源

你可以为私有云的 vCenter SSO 域添加其他标识提供者。  标识提供者提供身份验证，vCenter SSO 组为用户提供授权。

* 在私有云 vCenter 上[将 Active Directory 用作标识提供者](set-vcenter-identity.md)。
* 在私有云 vCenter 上[将 Azure AD 用作标识提供者](azure-ad.md)

1. [提升](escalate-private-cloud-privileges.md)私有云上 CloudOwner\@cloudsimple.local 用户的权限。
2. 使用 CloudOwner\@cloudsimple.local 登录到 vCenter
3. 将标识提供者中的用户添加到 [vCenter 单一登录组](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

## <a name="secure-network-on-your-private-cloud-environment"></a>在私有云环境中保护网络

私有云环境的网络安全通过保护网络访问和控制资源之间的网络流量来控制。

### <a name="access-to-private-cloud-resources"></a>访问私有云资源

私有云 vCenter 和资源访问通过安全的网络连接进行：

* [ExpressRoute 连接](on-premises-connection.md)。 ExpressRoute 从本地环境提供安全、高带宽、低延迟的连接。  使用连接可让你的本地服务、网络和用户访问私有云 vCenter。
* [站点到站点 VPN 网关](vpn-gateway.md)。 站点到站点 VPN 可通过安全隧道从本地访问私有云资源。  指定哪些本地网络可以向私有云发送和接收网络流量。
* [点到站点 VPN 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。 使用点到站点 VPN 连接快速远程访问私有云 vCenter。

### <a name="control-network-traffic-in-private-cloud"></a>控制私有云中的网络流量

防火墙表和规则控制私有云中的网络流量。  利用防火墙表，你可以基于在表中定义的规则组合来控制源网络或 IP 地址与目标网络或 IP 地址之间的网络流量。

1. 创建[防火墙表](firewall.md#add-a-new-firewall-table)。
2. 将[规则添加](firewall.md#create-a-firewall-rule)到防火墙表。
3. [将防火墙表附加到 VLAN/子网](firewall.md#attach-vlans-subnet)。
