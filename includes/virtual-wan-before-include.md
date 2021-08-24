---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ef4edb99ab2d6d4a25f2151c5b63c1c31ff91ef4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724990"
---
* 你有一个 Azure 订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你拥有一个要连接到的虚拟网络。 

   * 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 
   * 若要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../articles/virtual-network/quick-create-portal.md)一文。

* 虚拟网络不能包含任何现有虚拟网络网关。 

   * 如果虚拟网络已有网关（VPN 或 ExpressRoute），必须先删除所有网关再继续。 
   * 此配置要求虚拟网络仅连接到虚拟 WAN 中心网关。

* 虚拟中心是虚拟 WAN 创建和使用的虚拟网络。 这是区域中虚拟 WAN 网络的核心。 

   * 获取虚拟中心区域的 IP 地址范围。 
   * 为中心指定的地址范围不能与连接到的任何现有虚拟网络重叠。 
   * 地址范围不能与连接到的本地地址范围重叠。 
   * 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。
