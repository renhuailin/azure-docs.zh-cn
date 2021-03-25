---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 91dc6ba0bcd455aefe9de2efdff2feb20938152d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376373"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>步骤 1：导航到虚拟网络网关

1. 在 [Azure 门户](https://portal.azure.com)中导航到“所有资源”。 
2. 若要打开虚拟网关页，请导航到虚拟网关并单击以选择它。 

### <a name="step-2-delete-connections"></a>步骤 2：删除连接

1. 在“虚拟网络网关”页中，单击“连接”以查看连接到该网关的所有连接。
2. 在连接名称所在的行中，单击“...”，并从下拉列表中选择“删除”。
3. 单击“是”以确认要删除该连接。 如有多个连接，请删除每个连接。

### <a name="step-3-delete-the-virtual-network-gateway"></a>步骤 3：删除虚拟网络网关

请注意，除了 S2S 配置，如果还有此 VNet 的 P2S 配置，则删除虚拟网关将自动断开所有 P2S 客户端且不发出警告。

1. 在“虚拟网关”页中，单击“概览”。
2. 在“概述”页上，单击“删除”以删除网关。
