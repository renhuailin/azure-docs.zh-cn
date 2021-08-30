---
title: 应用服务环境网络
description: 应用服务环境网络详细信息
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 06/30/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89a14dc204e10231a134477650081396fc8e433f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723037"
---
# <a name="app-service-environment-networking"></a>应用服务环境网络

> [!NOTE]
> 本文介绍与独立 v2 应用服务计划一起使用的应用服务环境 v3
> 


应用服务环境 (ASE) 是托管 web 应用、api 应用和函数应用的 Azure 应用服务的单个租户部署。 安装 ASE 时，需要选择要在其中部署的 Azure 虚拟网络 (VNet)。 所有入站和出站流量应用程序都将位于你指定的 VNet 中。 ASE 部署到 VNet 的单个子网中。 不能将任何其他组件部署到同一子网中。 需将该子网委托给 Microsoft.Web/HostingEnvironments

## <a name="addresses"></a>地址 

在创建时，ASE 具有以下网络信息：

| 地址类型 | description |
|--------------|-------------|
| ASE 虚拟网络 | ASE 部署到的 VNet |
| ASE 子网 | ASE 部署到的子网 |
| 域后缀 | 在此 ASE 中创建的应用使用的域后缀 |
| 虚拟 IP | 这是 ASE 使用的 VIP 类型。 两个可能的值为“内部”和“外部” |
| 入站地址 | 入站地址是用于访问此 ASE 上的应用的地址。 如果你有内部 VIP，则此地址是 ASE 子网中的地址。 如果该地址是外部地址，则它是面向公众的地址 |
| 默认出站地址 | 在向 Internet 发出出站调用时，此 ASE 中的应用默认使用此地址。 |

ASEv3 详细介绍了 ASE 在 ASE 门户的“IP 地址”部分中使用的地址。

![ASE 地址 UI](./media/networking/networking-ip-addresses.png)

在 ASE 中缩放应用服务计划时，将使用 ASE 子网中的更多地址。 使用的地址数根据你的应用服务计划实例数以及 ASE 接收的流量大小而异。 ASE 中的应用在 ASE 子网中没有专用地址。 应用在 ASE 子网中使用的特定地址会随时间而改变。

## <a name="ports"></a>端口

ASE 接收端口 80 和 443 上的应用程序流量。 如果这些端口被阻止，则你无法访问自己的应用。 

> [!NOTE]
> 必须允许通过端口 80 从 Azure 负载均衡器访问 ASE 子网，使负载均衡器与 ASE 基础结构之间的流量保持连接。 仍可以控制发往 ASE 虚拟 IP 的端口 80 流量。
> 

## <a name="extra-configurations"></a>额外配置

可以设置网络安全组 (NSG) 和路由表 (UDR)，而不会受到限制。 可以强制要求使用隧道将来自 ASE 的所有出站流量传送到出口防火墙设备（例如 Azure 防火墙），且无需考虑除应用程序依赖关系以外的任何其他配置。 可将 WAF 设备（例如应用程序网关）放置在发往 ASE 的入站流量的前面，以公开该 ASE 上的特定应用。 对于用于访问 Internet 的不同专用出站地址，可以在 ASE 中使用 NAT 网关。 若要在 ASE 中使用 NAT 网关，请针对 ASE 子网配置 NAT 网关。 

## <a name="dns"></a>DNS

### <a name="dns-configuration-to-your-ase"></a>ASE 的 DNS 配置

如果 ASE 是使用外部 VIP 创建的，则应用将自动置于公共 DNS 中。 如果 ASE 是使用内部 VIP 创建的，则你可能需要为它配置 DNS。 如果你已选择在创建 ASE 期间自动配置 Azure DNS 专用区域，则会在 ASE VNet 中配置 DNS。 如果你已选择“手动配置 DNS”，则需要使用自己的 DNS 服务器或配置Azure DNS专用区域。 若要查找 ASE 的入站地址，请转到“ASE 门户”>“IP 地址”UI。 

如果要使用自己的 DNS 服务器，则需要添加以下记录：

1. 为 &lt;ASE 名称&gt;.appserviceenvironment.net 创建一个区域
1. 在该区域中，创建一条将 * 指向 ASE 所用入站 IP 地址的 A 记录
1. 在该区域中，创建一条将 @ 指向 ASE 所用入站 IP 地址的 A 记录
1. 在 &lt;ASE 名称&gt;.appserviceenvironment.net 中创建名为 scm 的区域
1. 在 scm 区域中创建 A 记录，该记录将 * 指向 ASE 专用终结点使用的 IP 地址

在 Azure DNS 专用区域中配置 DNS：

1. 创建名为 `<ASE-name>.appserviceenvironment.net` 的 Azure DNS 专用区域
1. 在该区域中，创建一条将 * 指向入站 IP 地址的 A 记录
1. 在该区域中，创建一条将 @ 指向入站 IP 地址的 A 记录
1. 在该区域中，创建一条将 *.scm 指向入站 IP 地址的 A 记录

ASE 默认域后缀的 DNS 设置不会将你的应用限制为只能由这些名称访问。 可在 ASE 中设置自定义域名而无需对应用进行任何验证。 如果随后想要创建名为 contoso.net 的区域，可执行此操作并将其指向入站 IP 地址。 自定义域名适用于应用请求，但不适用于 scm 站点。 scm 站点仅在 &lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net 中可用。 

#### <a name="dns-configuration-from-your-ase"></a>ASE 中的 DNS 配置

ASE 中的应用将使用配置了 VNet 的 DNS。 如果你希望某些应用使用与你的 VNet 配置的 DNS 服务器不同的 DNS 服务器，则可以使用应用设置 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER 手动设置每个应用。 应用设置 WEBSITE_DNS_ALT_SERVER 配置辅助 DNS 服务器。 仅当主 DNS 服务器没有响应时，才使用辅助 DNS 服务器。 

## <a name="limitations"></a>限制

尽管 ASE 确实会部署到客户 VNet 中，但有几项网络功能在 ASE 中不可用。 

* 发送 SMTP 流量。 你仍可以创建电子邮件触发的警报，但应用无法通过端口 25 发送出站流量
* 使用网络观察程序或 NSG 流监视出站流量

## <a name="more-resources"></a>更多资源

- [环境变量和应用设置参考](../reference-app-settings.md)