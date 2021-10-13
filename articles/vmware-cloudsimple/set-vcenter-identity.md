---
title: Azure VMware Solution by CloudSimple - 在私有云上设置 vCenter 标识源
description: 介绍如何将私有云 vCenter 设置为使用 Active Directory 进行身份验证，以便 VMware 管理员访问 vCenter
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36b45bcdc84b02dec5c54eff5f96cb7b07cf2ca5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616030"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>设置 vCenter 标识源以使用 Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>关于 VMware vCenter 标识源

VMware vCenter 支持使用不同的标识源对访问 vCenter 的用户进行身份验证。  CloudSimple 私有云 vCenter 可设置为使用 Active Directory 进行身份验证，以便 VMware 管理员访问 vCenter。 完成设置后，“cloudowner”用户可以将标识源中的用户添加到 vCenter。  

可以通过以下任一方式设置 Active Directory 域和域控制器：

* 在本地运行的 Active Directory 域和域控制器
* Azure 订阅中以虚拟机的形式在 Azure 上运行的 Active Directory 域和域控制器
* 在私有云中运行的新 Active Directory 域和域控制器
* Azure Active Directory 服务

本指南介绍了在本地运行或在订阅中以虚拟机形式运行的 Active Directory 域和域控制器的设置任务。  如果要使用 Azure AD 作为标识源，请参阅“[使用 Azure AD 作为 CloudSimple 私有云上 vCenter 的标识提供程序](azure-ad.md)”，以获取有关设置标识源的详细说明。

在[添加标识源](#add-an-identity-source-on-vcenter)之前，临时[升级 vCenter 特权](escalate-private-cloud-privileges.md)。

> [!CAUTION]
> 新用户必须只能添加到“云所有者组”、“云全局群集管理组”、“云全局存储管理组”、“云全局网络管理组”或“云全局 VM 管理组”。  添加到“管理员”组的用户将被自动删除。  只有服务帐户必须添加到“管理员”组，且服务帐户不得用于登录 vSphere Web UI。   


## <a name="identity-source-options"></a>标识源选项

* [添加本地 Active Directory 作为单一登录标识源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [在私有云上设置新的 Active Directory](#set-up-new-active-directory-on-a-private-cloud)
* [在 Azure 上设置 Active Directory](#set-up-active-directory-on-azure)

> [!IMPORTANT]
> **不支持 Active Directory（Windows 集成身份验证）。** 仅支持“通过 LDAP 的 Active Directory”选项作为标识源。

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>添加本地 Active Directory 作为单一登录标识源

如果要设置本地 Active Directory 作为单一登录标识源，需要：

* 从本地数据中心到私有云的[站点到站点 VPN 连接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* 添加到 vCenter 和 Platform Services Controller (PSC) 的本地 DNS 服务器 IP。

设置 Active Directory 域时，请使用下表中的信息。

| **选项** | **说明** |
|------------|-----------------|
| **Name** | 标识源的名称。 |
| **用户的基准 DN** | 用户的基准可分辨名称。 |
| **域名** | 域的 FQDN，例如 example.com。 请勿在此文本框中提供 IP 地址。 |
| **域别名** | 域 NetBIOS 名称。 如果使用的是 SSPI 身份验证，请将 Active Directory 域的 NetBIOS 名称添加为标识源的别名。 |
| **组的基准 DN** | 组的基准可分辨名称。 |
| **主服务器 URL** | 域的主域控制器 LDAP 服务器。<br><br>使用格式 `ldap://hostname:port` 或 `ldaps://hostname:port`。 对于 LDAP 连接，此端口通常为 389，对于 LDAPS 连接则为 636。 对于 Active Directory 多域控制器部署，用于 LDAP 的端口通常为 3268，用于 LDAPS 的则为 3269。<br><br>在主或辅助 LDAP URL 中使用 `ldaps://` 时，需要可为 Active Directory 服务器的 LDAPS 终结点建立信任的证书。 |
| **辅助服务器 URL** | 用于故障转移的辅助域控制器 LDAP 服务器的地址。 |
| **选择证书** | 如果要将 LDAPS 用于 Active Directory LDAP 服务器或 OpenLDAP 服务器标识源，则在向 URL 文本框中键入 `ldaps://` 后，系统将显示“选择证书”按钮。 不需要辅助 URL。 |
| **用户名** | 域中用户的 ID，这些用户至少具有对用户和组基准 DN 的只读访问权限。 |
| **密码** | 通过用户名指定的用户的密码。 |

具有上表中的信息时，可以添加本地 Active Directory 作为 vCenter 上的单一登录标识源。

> [!TIP]
> [VMware 文档页](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)中提供了有关单一登录标识源的详细信息。

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>在私有云上设置新的 Active Directory

可以在私有云上设置新的 Active Directory 域，并将其用作单一登录标识源。  Active Directory 域可以是现有 Active Directory 林的一部分，也可以设置为独立的林。

### <a name="new-active-directory-forest-and-domain"></a>新的 Active Directory 林和域

如果要设置新的 Active Directory 林和域，需要：

* 运行 Microsoft Windows Server 且用作新 Active Directory 林和域的域控制器的一个或多个虚拟机。
* 运行 DNS 服务进行名称解析的一个或多个虚拟机。

有关详细步骤，请参阅“[安装新的 Windows Server 2012 Active Directory 林](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)”。

> [!TIP]
> 为实现服务的高可用性，建议设置多个域控制器和 DNS 服务器。

设置 Active Directory 林和域后，可以为新 Active Directory [在 vCenter 上添加标识源](#add-an-identity-source-on-vcenter)。

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>现有 Active Directory 林中的新 Active Directory 域

如果要在现有 Active Directory 林中设置新的 Active Directory 域，需要：

* 到 Active Directory 林位置的站点到站点 VPN 连接。
* 用于解析现有 Active Directory 林的名称的 DNS 服务器。

有关详细步骤，请参阅“[安装新的 Windows Server 2012 Active Directory 子域或树域](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)”。

设置 Active Directory 域后，可以为新 Active Directory [在 vCenter 上添加标识源](#add-an-identity-source-on-vcenter)。

## <a name="set-up-active-directory-on-azure"></a>在 Azure 上设置 Active Directory

在 Azure 上运行的 Active Directory 类似于在本地运行的 Active Directory。  如果要将在 Azure 上运行的 Active Directory 设置为 vCenter 上的单一登录标识源，vCenter Server 和 PSC 必须与运行 Active Directory 服务的 Azure 虚拟网络建立网络连接。  可以使用[通过 ExpressRoute 进行的 Azure 虚拟网络连接](azure-expressroute-connection.md)，建立从运行 Active Directory 服务的 Azure 虚拟网络到 CloudSimple 私有云的连接。

建立网络连接后，请按照[添加本地 Active Directory 作为单一登录标识源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)中的步骤将其添加为标识源。  

## <a name="add-an-identity-source-on-vcenter"></a>在 vCenter 上添加标识源

1. 为私有云[提升特权](escalate-private-cloud-privileges.md)。

2. 登录私有云 vCenter。

3. 选择“主页”>“管理”。

    ![管理](media/OnPremAD01.png)

4. 选择“单一登录”>“配置”。

    ![单一登录](media/OnPremAD02.png)

5. 打开“标识源”选项卡，然后单击 + 添加新的标识源。

    ![标识源](media/OnPremAD03.png)

6. 选择“Active Directory 用作 LDAP 服务器”，然后单击“下一步”。

    ![突出显示“Active Directory 用作 LDAP 服务器”选项的屏幕截图。](media/OnPremAD04.png)

7. 为环境指定标识源参数，然后单击“下一步”。

    ![Active Directory](media/OnPremAD05.png)

8. 检查设置，并单击“完成”。
