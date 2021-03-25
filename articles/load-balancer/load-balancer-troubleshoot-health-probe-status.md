---
title: 排查 Azure 负载均衡器运行状况探测状态的问题
description: 了解如何排查 Azure 负载均衡器运行状况探测状态的已知问题。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029169"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>排查 Azure 负载均衡器运行状况探测状态的问题

此页介绍有关 Azure 负载均衡器运行状况探测常见问题的故障排查信息。

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>故障描述：负载均衡器后端的 VM 不响应运行状况探测
后端服务器必须通过探测检查后，才可加入负载均衡器集。 有关运行状况探测的详细信息，请参阅[了解负载均衡器探测](load-balancer-custom-probe-overview.md)。 

负载均衡器后端池 VM 因下列任意原因而不响应探测： 
- 负载均衡器后端池 VM 不正常 
- 负载均衡器后端池 VM 未侦听探测端口 
- 防火墙或网络安全组要阻止负载均衡器后端池 VM 上的端口 
- 负载均衡器中的其他错误配置

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>原因 1：负载均衡器后端池 VM 不正常

验证及解决方法

要解决此问题，请登录参与的 VM，并检查 VM 状态是否正常，能否从池中的另一 VM 响应 PsPing 或 TCPing。 如果 VM 不正常或无法响应探测，必须先解决此问题并使 VM 恢复正常状态，然后该 VM 才可参与负载均衡。

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>原因 2：负载均衡器后端池 VM 未侦听探测端口
如果 VM 处于正常状态但未响应探测，则可能是因为参与的 VM 上的探测端口未打开，或 VM 未侦听此端口。

验证及解决方法

1. 登录到后端 VM。
2. 打开命令提示符并运行下列命令，以验证是否有应用程序在侦听探测端口：          netstat -an
3. 如果端口状态未列为“正在侦听”，请配置适当的端口。 
4. 或者，选择其他列为“正在侦听”的端口，并相应地更新负载均衡器配置。

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>原因 3：防火墙或网络安全组要阻止负载均衡器后端池 VM 上的端口
如果 VM 上的防火墙要阻止探测端口，或者子网或 VM 上配置的一个或多个网络安全组禁止探测到达端口，则 VM 无法响应运行状况探测。

验证及解决方法

1. 如果启用了防火墙，请检查它是否配置为允许探测端口。 如果没有，请将其配置为允许探测端口上的流量并重新测试。
2. 在网络安全组列表中，检查探测端口上的传入或传出流量是否受到干扰。
3. 此外，检查 VM NIC 或子网上是否存在优先级高于允许 LB 探测和流量的默认规则的“全部拒绝”网络安全组规则（网络安全组必须允许负载均衡器 IP 168.63.129.16）。
4. 如果上述某规则要阻止探测流量，请将其删除并将规则配置为允许探测流量。  
5. 测试 VM 是否现已开始响应运行状况探测。

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>原因 4：负载均衡器中的其他错误配置
如果上述各原因貌似都经过验证和正确解决，但后端 VM 仍未响应运行状况探测，请手动测试连接性，并收集一些跟踪信息以了解连接性。

验证及解决方法

1. 使用来自 VNet 中其他 VM 的 Psping 进行探测端口响应测试（例如 .\psping.exe -t 10.0.0.4:3389）并记录结果。 
2. 使用来自 VNet 中其他 VM 的 TCPing 进行探测端口响应测试（例如 .\tcping.exe 10.0.0.4 3389）并记录结果。 
3. 如果在这些 ping 测试中未收到响应，则
    - 在目标后端池 VM 中同时运行一个 Netsh 跟踪，再运行一个来自相同 VNet 的测试 VM。 现在，运行 PsPing 一段时间，收集一些网络跟踪，并停止测试。 
    - 分析网络捕获，查看是否同时存在与 ping 查询相关的传入和传出数据包。 
        - 如果在后端池 VM 中未观察到传入数据包，可能是某个网络安全组或 UDR 错误配置阻止了流量。 
        - 如果在后端池 VM 中未观察到传出数据包，需检查 VM 是否存在任何不相关的问题（例如，应用程序阻止探测端口）。 
    - 验证在到达负载均衡器之前，探测数据包是否强制发送到其他目标（可能通过 UDR 设置发送）。 这会使流量永远无法达到后端 VM。 
4. 更改探测类型（例如从 HTTP 到 TCP），并在网络安全组 ACL 和防火墙中配置相应端口，以验证问题是否与探测响应的配置有关。 有关运行状况探测配置的详细信息，请参阅[终结点负载均衡运行状况探测配置](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details)。

## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。