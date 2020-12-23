---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359488"
---
* 你有一个 Azure 订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 若要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../articles/virtual-network/quick-create-portal.md)一文。

* 虚拟网络不能包含任何现有虚拟网络网关。 如果虚拟网络已有网关（VPN 或 ExpressRoute），必须先删除所有网关再继续。 此配置要求虚拟网络仅连接到虚拟 WAN 中心网关。

* 虚拟中心是虚拟 WAN 创建和使用的虚拟网络。 这是区域中虚拟 WAN 网络的核心。 获取虚拟中心区域的 IP 地址范围。 为中心指定的地址范围不能与连接到的任何现有虚拟网络重叠。 它也不能与连接到的本地地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。
