---
title: 从本地访问 Azure VMware Solution by CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: 通过防火墙从本地网络访问 Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3cc9ad0c694be8f95ddcffbc3d952d59b83f89f3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077204"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>从本地访问 CloudSimple 私有云环境和应用程序

可以使用 Azure ExpressRoute 或站点到站点 VPN，设置从本地网络到 CloudSimple 的连接。  访问 CloudSimple 私有云 vCenter 和通过连接在私有云上运行的任何工作负载。  可以使用本地网络中的防火墙，控制连接上打开的端口。  本文介绍了一些典型的应用程序端口要求。  对于任何其他应用程序，请参阅对应的应用程序文档中的端口要求。

## <a name="ports-required-for-accessing-vcenter"></a>访问 vCenter 所需的端口

若要访问私有云 vCenter 和 NSX-T 管理器，本地防火墙上必须打开下表中定义的端口。  

| Port       | 源                           | 目标                      | 目的                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | 本地 DNS 服务器          | 私有云 DNS 服务器        | 将 az.cloudsimple.io 的 DNS 查找从本地网络转发到私有云 DNS 服务器时需要。       |
| 53 (UDP)   | 私有云 DNS 服务器        | 本地 DNS 服务器          | 将本地域名的 DNS 查找从私有云 vCenter 转发到本地 DNS 服务器时需要。 |
| 80 (TCP)   | 本地网络              | 私有云管理网络 | 将 vCenter URL 从 http 重定向到 https 时需要。                                                           |
| 443 (TCP)  | 本地网络              | 私有云管理网络 | 从本地网络访问 vCenter 和 NSX-T 管理器时需要。                                             |
| 8000 (TCP) | 本地网络              | 私有云管理网络 | 从本地到私有云的虚拟机 vMotion 需要。                                            |
| 8000 (TCP) | 私有云管理网络 | 本地网络              | 从私有云到本地的虚拟机 vMotion 需要。                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>使用本地 Active Directory 作为标识源所需的端口

若要在私有云 vCenter 上将本地 Active Directory 配置为标识源，必须打开表中定义的端口。  有关配置步骤，请参阅[使用 Azure AD 作为 CloudSimple 私有云上 vCenter 的标识提供者](./azure-ad.md)。

| Port         | 源                           | 目标                                         | 目的                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | 私有云 DNS 服务器        | 本地 DNS 服务器                             | 将本地 Active Directory 域名的 DNS 查找从私有云 vCenter 转发到本地 DNS 服务器时需要。          |
| 389 (TCP/UDP) | 私有云管理网络 | 本地 Active Directory 域控制器     | 从私有云 vCenter Server 到 Active Directory 域控制器的 LDAP 通信以进行用户身份验证时需要。                |
| 636 (TCP)     | 私有云管理网络 | 本地 Active Directory 域控制器     | 从私有云 vCenter Server 到 Active Directory 域控制器的安全 LDAP (LDAPS) 通信以进行用户身份验证时需要。 |
| 3268 (TCP)    | 私有云管理网络 | 本地 Active Directory 全局目录服务器 | 多域控制器部署中的 LDAP 通信需要。                                                                        |
| 3269 (TCP)    | 私有云管理网络 | 本地 Active Directory 全局目录服务器 | 多域控制器部署中的 LDAPS 通信需要。                                                                       |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>访问工作负载虚拟机所需的常用端口

若要访问在私有云上运行的工作负载虚拟机，需要在本地防火墙上打开端口。  下表显示了所需的一些常用端口及其用途。  对于任何特定于应用程序的端口要求，请参阅对应的应用程序文档。

| Port         | 源                         | 目标                          | 目的                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | 本地网络            | 私有云工作负载网络       | 通过 Secure Shell 访问在私有云上运行的 Linux 虚拟机。              |
| 3389 (TCP)    | 本地网络            | 私有云工作负载网络       | 远程桌面到私有云上运行的 Windows 虚拟机。                 |
| 80 (TCP)      | 本地网络            | 私有云工作负载网络       | 访问在私有云上运行的虚拟机上部署的任何 Web 服务器。        |
| 443 (TCP)     | 本地网络            | 私有云工作负载网络       | 访问在私有云上运行的虚拟机上部署的任何安全 Web 服务器。 |
| 389 (TCP/UDP) | 私有云工作负载网络 | 本地 Active Directory 网络 | 将 Windows 工作负载虚拟机加入到本地 Active Directory 域中。       |
| 53 (UDP)      | 私有云工作负载网络 | 本地网络                  | 工作负载虚拟机对本地 DNS 服务器进行 DNS 服务访问。         |

## <a name="next-steps"></a>后续步骤

* [创建并管理 VLAN 和子网](./create-vlan-subnet.md)
* [使用 Azure ExpressRoute 连接到本地网络](./on-premises-connection.md)
* [从本地设置站点到站点 VPN](./vpn-gateway.md)
