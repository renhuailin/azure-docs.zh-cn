---
title: Azure VMware 解决方案中的 DHCP 和 DNS 说明
description: Azure VMware 解决方案 DHCP 和 DNS 说明。
ms.topic: include
ms.date: 05/28/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0e92259c2ece3b92b841d2be192684679d37a635
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638487"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

在私有云环境中运行的应用程序和工作负载需要使用名称解析和 DHCP 服务来进行查找和 IP 地址分配。 需有适当的 DHCP 和 DNS 基础结构才能提供这些服务。 在私有云环境中，可以配置一个虚拟机来提供这些服务。  

使用内置于 NSX 的 DHCP 服务，或使用私有云中的本地 DHCP 服务器，而不要通过 WAN 将广播 DHCP 流量路由回本地。
