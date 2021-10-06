---
title: 排查网络连接问题 - Azure 事件网格 | Microsoft Docs
description: 本文介绍如何排查 Azure 事件网格的网络连接问题。
author: batrived
ms.topic: article
ms.date: 09/28/2021
ms.author: batrived
ms.openlocfilehash: c565a465dbab683295cf42248640bbbf8242412c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229936"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>排查连接问题 - Azure 事件网格

客户端应用程序无法连接到事件网格主题/域的原因有很多。 你遇到的连接问题可能是永久性的，也可能是暂时性的。 如果问题一直发生（永久性的），则可能需要检查组织的防火墙设置、IP 防火墙设置、服务标记、专用终结点等等。 对于暂时性问题，运行命令来检查丢弃的数据包以及获取网络跟踪可能有助于解决问题。

本文提供了排查 Azure 事件网格的连接问题的技巧。

## <a name="troubleshoot-permanent-connectivity-issues"></a>排查永久性连接问题

如果应用程序根本无法连接到事件网格，请按此部分的步骤操作来解决问题。

### <a name="check-if-theres-a-service-outage"></a>检查是否存在服务中断

在 [Azure 服务状态站点](https://azure.microsoft.com/status/)上检查是否存在 Azure 事件网格服务中断的情况。

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>检查组织的防火墙是否阻止了与事件网格通信所需的端口

确认组织的防火墙未阻止用于与 Azure 事件网格通信的端口。 请查看下表，了解与 Azure 事件网格通信需要打开哪些出站端口。

| 协议 | 端口 |
| -------- | ----- |
| HTTPS    | 443   |

下面是用于检查 443 端口是否被阻止的示例命令。

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

在 Linux 上：

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>验证你的企业防火墙中是否允许使用这些 IP 地址

使用 Azure 时，有时必须在公司防火墙或代理中允许特定的 IP 地址范围或 URL 才能访问正在使用或尝试使用的所有 Azure 服务。 确认在事件网格使用的 IP 地址上是否允许该流量。 有关 Azure 事件网格使用的 IP 地址：请参阅 [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)和[服务标记 - AzureEventGrid](network-security.md#service-tags)。

[AZURE IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)文档还“按区域”列出了 IP 地址。 用户可以允许在公司防火墙或代理中为“主题的区域”和“配对区域”提供地址范围。 有关区域的配对区域，请参阅[业务连续性和灾难恢复 (BCDR)：Azure 配对区域](../best-practices-availability-paired-regions.md)。 

> [!NOTE]
> 可以将新的 IP 地址添加到 AzureEventGrid 服务标记中，但这种操作并不常见。 因此，最好是每周对服务标记执行一次检查。

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>验证网络安全组中是否允许使用 AzureEventGrid 服务标记

如果应用程序在子网内运行，并且存在关联的网络安全组，请确认是允许 Internet 出站还是允许 AzureEventGrid 服务标记。 请参阅[服务标记](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>检查主题/域的 IP 防火墙设置

确认运行应用程序的计算机的公共 IP 地址未被事件网格主题/域 IP 防火墙阻止。

默认情况下，只要请求附带有效的身份验证和授权，就可以从 Internet 访问事件网格主题/域命名空间。 有了 IP 防火墙，就可以使用 [CIDR（无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法将其进一步限制为仅一组 IPv4 地址或 IPv4 地址范围。

IP 防火墙规则应用于事件网格主题/域级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与事件网格主题/域上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“禁止”。 响应不会提及 IP 规则。

有关详细信息，请参阅[为 Azure 事件网格主题/域配置 IP 防火墙规则](configure-firewall.md)。

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>查找被 IP 防火墙阻止的 IP 地址

启用事件网格的诊断日志主题/域[启用诊断日志](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic)。 用户会看到连接遭到拒绝的 IP 地址。

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>检查是否只能使用专用终结点访问事件网格主题/域

如果事件网格主题/域配置为仅可通过专用终结点访问，请确认该客户端应用程序正在通过专用终结点访问主题/域。 要确认这一点，请检查客户端应用程序是否在子网内运行，以及该子网中的事件网格主题/域是否有专用终结点。

使用 [Azure 专用链接服务](../private-link/private-link-overview.md)，可以通过虚拟网络中的专用终结点访问 Azure 事件网格。 专用终结点是一个网络接口，可以通过专用且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅[配置专用终结点](configure-private-endpoints.md)。

## <a name="troubleshoot-transient-connectivity-issues"></a>排查暂时性连接问题

如果遇到间歇性连接问题，请参阅以下部分来了解排查技巧。

### <a name="run-the-command-to-check-dropped-packets"></a>运行命令来检查丢弃的数据包

出现间歇性连接问题时，请运行以下命令，检查是否存在任何丢弃的数据包。 此命令会尝试通过服务每隔 1 秒建立 25 个不同的 TCP 连接。 然后，可以检查其中有多少成功/失败，还可以查看 TCP 连接延迟。 可以从[此处](/sysinternals/downloads/psping)下载 `psping` 工具。

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

如果使用的是其他工具（如 `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php) 等），则可以使用等效的命令。

如果上述步骤没有帮助，请获取网络跟踪，并使用 [Wireshark](https://www.wireshark.org/) 之类的工具对其进行分析。 如果需要，请联系 [Microsoft 支持部门](https://support.microsoft.com/)。

### <a name="service-upgradesrestarts"></a>服务升级/重启

由于后端服务升级和重启，可能会出现暂时性连接问题。 出现这种情况时，可能会看到以下症状：

- 传入的消息/请求可能会减少。
- 日志文件可能包含错误消息。
- 应用程序可能会在几秒内断开与服务的连接。
- 可能会暂时限制请求。

捕获这些暂时性错误，后退然后重试调用，将确保代码能够从这些暂时性错误中复原。

## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)中发布问题，或开具[支持票证](https://azure.microsoft.com/support/options/)。