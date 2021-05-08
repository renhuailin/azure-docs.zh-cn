---
title: 应用服务环境网络
description: 应用服务环境网络详细信息
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0a1221a8de10fd18768a1a0f0ac08277dc2901d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735027"
---
# <a name="app-service-environment-networking"></a>应用服务环境网络

> [!NOTE]
> 本文介绍应用服务环境 v3（预览版）
> 

应用服务环境 (ASE) 是托管 web 应用、api 应用和函数应用的 Azure 应用服务的单个租户部署。 安装 ASE 时，需要选择要在其中部署的 Azure 虚拟网络 (VNet)。 所有入站和出站流量应用程序都将位于你指定的 VNet 中。  

ASEv3 使用两个子网。  一个子网用于处理入站流量的专用终结点。 这可以是预先存在的子网，也可以是新的子网。  入站子网可用于除专用终结点之外的其他目的。 出站子网仅可用于来自 ASE 的出站流量。 ASE 出站子网中不能有其他内容。

## <a name="addresses"></a>地址 
在创建时，ASE 具有以下地址：

| 地址类型 | description |
|--------------|-------------|
| 入站地址 | 入站地址是 ASE 使用的专用终结点地址。 |
| 出站子网 | 出站子网也是 ASE 子网。 预览期间，此子网仅用于出站流量。 |
| Windows 出站地址 | 默认情况下，此 ASE 中的 Windows 应用将使用此地址，并对 internet 进行出站调用。 |
| Linux 出站地址 | 默认情况下，此 ASE 中的 Linux 应用将使用此地址，并对 internet 进行出站调用。 |

ASEv3 详细介绍了 ASE 在 ASE 门户的“IP 地址”部分中使用的地址。

![ASE 地址 UI](./media/networking/networking-ip-addresses.png)

如果删除 ASE 使用的专用终结点，则无法访问 ASE 中的应用。  

ASE 使用出站子网中的地址来支持 ASE 使用的基础结构。 在 ASE 中缩放应用服务计划时，将使用更多地址。 ASE 中的应用在出站子网中没有专用地址。 应用程序在出站子网中使用的地址会随时间而改变。

## <a name="ports"></a>端口

ASE 接收端口 80 和 443 上的应用程序流量。  ASE 没有入站或出站端口要求。 

## <a name="extra-configurations"></a>额外配置

与 ASEv2 不同的是，通过 ASEv3，你可以根据需要任意设置网络安全组 (NSG) 和路由表 (UDR)，而不会受到限制。 如果希望将所有出站流量的隧道强制设置为从 ASE 传输到 NVA 设备。 可以将 WAF 设备置于 ASE 的所有入站流量之前。 

## <a name="dns"></a>DNS

ASE 中的应用将使用配置了 VNet 的 DNS。 按照[使用应用服务环境](./using.md#dns-configuration)中的说明进行操作，将 DNS 服务器配置为指向 ASE。 如果你希望某些应用使用与你的 VNet 配置的 DNS 服务器不同的 DNS 服务器，则可以使用应用设置 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER 手动设置每个应用。 应用设置 WEBSITE_DNS_ALT_SERVER 配置辅助 DNS 服务器。 仅当主 DNS 服务器没有响应时，才使用辅助 DNS 服务器。 

## <a name="preview-limitation"></a>预览限制

ASEv3 未提供某些网络功能。  ASEv3 中不可用的内容包括：

• FTP • 远程调试 • 外部负载平衡器部署 • 能够访问专用容器注册表以进行容器部署 • 能够调用全局对等 Vnet • 通过服务终结点或私有终结点保护的存储进行备份/还原的能力 • 能够在服务终结点或私有终结点保护的 keyvault 帐户上拥有应用设置 keyvault 引用 • 能够使用 BYOS 到服务终结点或专用终结点保护的存储帐户 • 对出站流量使用网络观察程序或 NSG 流
    
    