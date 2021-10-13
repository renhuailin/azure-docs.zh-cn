---
title: Azure VMware Solution by CloudSimple - 为私有云设置工作负载 DNS 和 DHCP
description: 描述如何为在 CloudSimple 私有云环境中运行的应用程序和工作负载设置 DNS 和 DHCP
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e947f5f277378b446a84bd8457fd02e55f91d0a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612838"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>在 CloudSimple 私有云中设置 DNS 和 DHCP 应用程序和工作负载

在私有云环境中运行的应用程序和工作负载需要使用名称解析和 DHCP 服务来进行查找和 IP 地址分配。  需有适当的 DHCP 和 DNS 基础结构才能提供这些服务。  在私有云环境中，可以配置一个虚拟机来提供这些服务。  

## <a name="prerequisites"></a>先决条件

* 配置了 VLAN 的分布式端口组
* 将设置路由到本地或基于 Internet 的 DNS 服务器
* 用于创建虚拟机的虚拟机模板或 ISO

## <a name="linux-based-dns-server-setup"></a>基于 Linux 的 DNS 服务器设置

Linux 提供了用于设置 DNS 服务器的各种包。  下面是 [DigitalOcean 的示例设置](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04)，其中包含有关如何设置开放源代码 BIND DNS 服务器的说明。

## <a name="windows-based-setup"></a>基于 Windows 的设置

这些 Microsoft 主题介绍如何将 Windows 服务器设置为 DNS 服务器和 DHCP 服务器。

* [将 Windows Server 作为 DNS 服务器](/windows-server/networking/dns/dns-top)
* [将 Windows Server 作为 DHCP 服务器](/windows-server/networking/technologies/dhcp/dhcp-top)
