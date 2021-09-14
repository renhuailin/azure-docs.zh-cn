---
title: 什么是适用于 Oracle 的 BareMetal 基础结构？
description: 了解 BareMetal 基础结构为 Oracle 工作负载提供的功能。
ms.topic: conceptual
ms.subservice: baremetal-oracle
ms.date: 09/03/2021
ms.openlocfilehash: e74d1a3e3b6cb1b2cd0b99927a57a88117715e7b
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542433"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>什么是适用于 Oracle 的 BareMetal 基础结构？

在本文中，我们将概述 BareMetal 基础结构为 Oracle 工作负载提供的功能。

适用于 Oracle 的 BareMetal 基础结构基于 Oracle 认证的统一计算系统 (UCS) 和 FLexPod。 FlexPod 平台提供预验证的存储、网络和服务器技术。 该平台提供 NetApp 网络文件系统 (NFS) 存储，并使用 DirectNFS 协议提供集成。 BareMetal 服务器专用于你，并且 BareMetal 实例上没有任何虚拟机监控程序。 

这些实例用于运行需要 Oracle 工作负载的任务关键型应用程序。 BareMetal 实例为在 Azure 虚拟机 (VM) 中运行的应用程序提供低延迟 (0.35 ms)。 BareMetal 提供共享存储。 BareMetal 还支持与专门的专用互连网络进行节点到节点通信所需的多播。 

适用于 Oracle 的 BareMetal 基础结构的其他功能包括：

- Oracle 认证的 UCS 刀片 - UCSB200-M5、UCSB460-M4、UCSB480-M5
- 使用专用虚拟 LAN (VLAN) 的 Oracle Real Application Clusters (RAC) 节点到节点（多播）通信 - 40 Gb。
- Microsoft 托管硬件
  - 冗余存储、网络、电源、管理
  - 针对基础结构、修复和替换的监视
  - 将 Azure ExpressRoute 包含到客户的域控制器
  - 安全的物理和网络安全性，可以访问所有 Azure 云服务

### <a name="supported-protocols"></a>支持的协议

以下协议用于适用于 Oracle 工作负载的 BareMetal 服务器中的不同装入点。

- 操作系统装载 – iSCSI
- 数据/日志 – NFSv3
- 备份/存档 – NFSv4

### <a name="licensing"></a>许可

- 你自带本地操作系统和 Oracle 许可证。

### <a name="operating-system"></a>操作系统

服务器预加载了操作系统 RHEL 7.6。

## <a name="next-steps"></a>后续步骤

了解适用于 Oracle BareMetal 工作负载的 SKU。

> [!div class="nextstepaction"]
> [适用于 Oracle 工作负载的 BareMetal SKU](oracle-baremetal-skus.md)
