---
title: Azure VMware 解决方案 - 从私有云到本地的 DNS 转发
description: 描述如何启用 CloudSimple 私有云 DNS 服务器来转发本地资源查找
author: suzizuber
ms.author: v-szuber
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8d6107df467d3be35612ff3d2ac5c9a602022f26
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616239"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>启用 CloudSimple 私有云 DNS 服务器以将本地资源的 DNS 查找转发到 DNS 服务器

私有云 DNS 服务器可以将任何本地资源的 DNS 查找转发到 DNS 服务器。  启用查找功能后，私有云 vSphere 组件便可查找在本地环境中运行的任何服务，并使用完全限定的域名 (FQDN) 与它们进行通信。

## <a name="scenarios"></a>方案 

转发本地 DNS 服务器的 DNS 查找支持在以下情况下使用私有云：

* 使用私有云作为本地 VMware 解决方案的灾难恢复设置
* 使用本地 Active Directory 作为私有云 vSphere 的标识源
* 使用 HCX 将虚拟机从本地迁移到私有云

## <a name="before-you-begin"></a>准备阶段

必须存在从私有云网络到本地网络的网络连接，才能进行 DNS 转发。  用户可以使用以下内容设置网络连接：

* [使用 ExpressRoute 从本地连接到 CloudSimple](on-premises-connection.md)
* [设置站点到站点 VPN 网关](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

为了使 DNS 转发正常工作，必须在此连接上打开防火墙端口。  使用的端口为 TCP 端口 53 或 UDP 端口 53。

> [!NOTE]
> 如果使用的是站点到站点 VPN，则必须将本地 DNS 服务器子网添加为本地前缀的一部分。

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>请求从私有云到本地的 DNS 转发

若要允许从私有云到本地的 DNS 转发，请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，并提供以下信息。

* 问题类型：技术
* 订阅：部署 CloudSimple 服务的订阅
* 服务：VMware Solution by CloudSimple
* 问题类型：公告或如何实现...
* 问题子类型：需要有关 NW 的帮助
* 在细节窗格中提供本地域的域名。
* 在细节窗格中提供将查找从私有云转发到的本地 DNS 服务器的列表。

## <a name="next-steps"></a>后续步骤

* [详细了解本地防火墙配置](on-premises-firewall-configuration.md)
* [本地 DNS 服务器配置](on-premises-dns-setup.md)
