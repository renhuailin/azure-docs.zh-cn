---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561153"
---
不支持 GatewaySubnet 上具有 0.0.0.0/0 目标和 NSG 的用户定义的路由。 使用此配置创建的网关将被阻止创建。 网关需要访问管理控制器才能正常工作。 GatewaySubnet 上的 [BGP 路由传播](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol)应设置为“已启用”，以确保网关可用。 如果此项设置为“已禁用”，则网关将不起作用。