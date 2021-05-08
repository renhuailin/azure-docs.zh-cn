---
title: Azure 负载均衡器分发模式
description: 开始了解 Azure 负载均衡器的不同分发模式。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99551204"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure 负载均衡器分发模式

Azure 负载均衡器支持两种分发模式，用于将连接路由到负载均衡的应用程序：

* 基于哈希
* 源 IP 关联

## <a name="hash-based"></a>基于哈希

Azure 负载均衡器的默认分配模式是五元组哈希。 

元组由以下内容组成：
* **源 IP**
* **源端口**
* **目标 IP**
* **目标端口**
* **协议类型**

哈希用于将流量映射到可用的服务器。 算法仅在传输会话内部提供粘性。 同一会话中的数据包会定向到经过负载均衡的终结点后面的同一数据中心 IP。 客户端从同一源 IP 发起新会话时，源端口会更改，并导致流量定向到其他数据中心终结点。

![基于五元组哈希的分配模式](./media/distribution-mode-concepts/load-balancer-distribution.png)

基于哈希的模式提供一种配置类型：

* **无(基于哈希)** - 指定任何虚拟机可能处理来自同一客户端的后续请求。

## <a name="source-ip-affinity"></a>源 IP 关联

此分配模式也称为为会话关联或客户端 IP 关联。 该模式使用二元组（源 IP 和目标 IP）或三元组（源 IP、目标 IP 和协议）哈希将流量映射到可用的服务器。 

使用源 IP 关联，从同一客户端计算机启动的连接会进入同一个数据中心终结点。

下图演示二元组配置。 请注意二元组如何从负载均衡器运行到虚拟机 1 (VM1)。 VM1 随后由 VM2 和 VM3 备份。

![二元组会话关联分配模式](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

源 IP 关联模式提供两种配置类型：

* **客户端 IP (源 IP 关联 2 元组)** - 指定来自同一客户端 IP 地址的后续请求将由同一虚拟机处理。
* **客户端 IP 和协议(源 IP 关联 3 元组)** - 指定来自同一客户端 IP 地址和协议组合的连续请求将由同一虚拟机处理。

## <a name="use-cases"></a>用例

源 IP 关联模式中的客户端 IP 和协议（源 IP 关联 3 元组）类型解决了 Azure 负载均衡器与远程桌面网关（RD 网关）之间的不兼容性。 

另一个用例方案是媒体上传。 数据上传通过 UDP 进行，但控制平面通过 TCP 实现：

* 客户端启动与负载均衡公共地址的 TCP 会话，并定向到特定 DIP。 通道将保持活动状态以监视连接运行状况。
* 来自同一客户端计算机的新 UDP 会话在同一个负载均衡公共终结点中启动。 连接像前面的 TCP 连接一样定向到同一个 DIP 终结点。 能够以较高的吞吐量执行媒体上传，同时通过 TCP 维护控制通道。

> [!NOTE]
> 如果通过删除或添加虚拟机来更改负载均衡集，则会重新计算客户端请求的分配。 无法确保现有客户端的新连接最终都会抵达同一台服务器。 此外，使用源 IP 关联分配模式可能导致流量的不均衡分配。 在代理后面运行的客户端可被视为唯一的客户端应用程序。


## <a name="next-steps"></a>后续步骤

有关如何配置 Azure 负载均衡器的分发模式的详细信息，请参阅[配置 Azure 负载均衡器的分发模式](load-balancer-distribution-mode.md)。

