---
title: include 文件
description: include 文件
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356634"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>是否仍可在对等互连虚拟网络中部署多个堡垒主机？

是的。 默认情况下，用户会看到部署在 VM 所在的同一虚拟网络中的堡垒主机。 但是，在 " **连接** " 菜单中，用户可以看到在对等互连网络上检测到的多个堡垒主机。 他们可以选择想要用于连接到虚拟网络中部署的 VM 的堡垒主机。

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>如果我的对等互连 Vnet 部署在不同的订阅中，会通过堡垒工作吗？

是的，通过堡垒的连接将继续针对单个租户的不同订阅中的对等互连 Vnet 运行。 不支持跨两个不同租户的订阅。 若要在 " **连接** " 下拉菜单中查看堡垒，用户必须选择他们在 **订阅 > 全局订阅** 中有权访问的权限。

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="全局订阅筛选器" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>我有权访问对等互连 VNet，但看不到在此部署的 VM。

请确保用户对 VM 和对等互连 VNet 都具有 **读取** 权限。 此外，在 IAM 下检查用户对以下资源具有 **读取** 访问权限：

* 虚拟机上的读者角色。
* NIC 上的读者角色（使用虚拟机的专用 IP）。
* Azure Bastion 资源上的读者角色。
* 如果没有对等互连虚拟网络) ，则虚拟网络上的 "读取者" 角色 (不需要。

|权限|说明|权限类型|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |获取守护主机|操作|
|Microsoft.Network/virtualNetworks/BastionHosts/action |获取虚拟网络中的守护主机引用。|操作|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|获取虚拟网络中的守护主机引用。|操作|
|Microsoft.Network/networkInterfaces/read|获取网络接口定义。|操作|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|获取网络接口 IP 配置定义。|操作|
|Microsoft.Network/virtualNetworks/read|获取虚拟网络定义|操作|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|获取对虚拟网络子网中的所有虚拟机的引用|操作|
|Microsoft.Network/virtualNetworks/virtualMachines/read|获取对虚拟网络中的所有虚拟机的引用|操作|