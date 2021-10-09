---
title: 针对与 Azure HPC 缓存的客户端连接进行负载均衡
description: 如何配置 DNS 服务器以实现 Azure HPC 缓存的循环负载均衡
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/20/2021
ms.author: v-erkel
ms.openlocfilehash: 0aa704b44a7a61472b3d10c3b7cc94f5e95dd9ff
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698950"
---
# <a name="load-balance-hpc-cache-client-traffic"></a>对 HPC 缓存客户端流量进行负载均衡

本文介绍一些基本方法，用于均衡客户端到 Azure HPC 缓存上所有安装点的流量。

每个 HPC 缓存至少有 3 个不同的 IP 地址，吞吐量值较大的缓存最多可以有 12 个。 请务必使用所有 IP 地址以充分发挥 Azure HPC 缓存的优势。

有多种选项可用于对客户端装载进行负载平衡：

* 手动为每个客户端选择不同的装载 IP
* 在客户端装载脚本中包含 IP 地址轮换
* 配置 DNS 系统以在所有可用地址之间自动路由客户端请求（循环 DNS）

哪种负载均衡系统最适合则取决于工作流的复杂性、缓存中的 IP 地址数量以及大量其他因素。 如果需要帮助决定哪种方法最适合，请咨询 Azure 顾问。

## <a name="assign-ip-addresses-manually"></a>手动分配 IP 地址

缓存的装载 IP 地址显示在 Azure 门户的缓存“概述”和“装载说明”页上，并且使用 Azure CLI 或 PowerShell 创建缓存时打印的成功消息中也会显示。 

可以使用“装载说明”页为每个客户端生成自定义装载命令。 创建多个命令时选择所有缓存装载地址值。

有关详细信息，请阅读[装载 Azure HPC 缓存](hpc-cache-mount.md)。

## <a name="use-scripted-load-balancing"></a>使用脚本式负载均衡

有多种方法能够以编程方式在可用 IP 地址之间轮换客户端装载。

此示例装载命令使用散列函数 ``cksum`` 和客户端主机名在 HPC 缓存上的所有可用 IP 地址之间自动分配客户端连接。 如果所有客户端计算机都有唯一的主机名，则可以在每个客户端上运行此命令以确保使用所有可用的装载点。

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.0.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/${NAMESPACE} /mnt
```

要在工作流中使用此示例，请自定义以下术语：

* 在 ```X=``` 表达式中，按排序顺序使用所有缓存的装载地址的空格分隔列表。

  表达式 ``(X=(10.0.0.{7..9})`` 将变量 X 设置为这组装载地址：{10.0.0.7, 10.0.0.8, 10.0.0.9}。 使用缓存的基本 IP 地址和缓存的“概览”页中显示的确切地址。 如果地址不连续，请按数字顺序列出所有地址。

* 在 ```%3``` 术语中，使用你的缓存拥有的实际装载 IP 地址数量（通常为 3、6、9 或 12）。

  例如，如果你的缓存公开了 9 个客户端装载 IP 地址，请使用 ``%9``。

* 对于表达式 ``${NAMESPACE}``，使用客户端将访问的存储目标命名空间路径。

  可以使用已定义的变量（示例中的 NAMESPACE），或者改为传递文字值。
  
  本节末尾的命令示例使用名称空间路径的文字值 ``/blob-target-1``。

* 如果要在客户端计算机上使用自定义本地路径，请将值 ``/mnt`` 更改为所需的路径。

以下是填充的客户端装载命令的示例：

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.7.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/blob-target-1 /hpc-cache/blob1 
```

## <a name="use-dns-load-balancing"></a>使用 DNS 负载均衡

本部分介绍了配置 DNS 系统以将客户端连接分发到 Azure HPC 缓存上的所有装载点的基础知识。 此方法不考虑每个客户端生成的流量，但的确确保客户端均匀分布在所有缓存的接口上，而不是仅使用一两个接口。

本文档不包含有关在 Azure 环境中针对你的客户端设置和管理 DNS 服务器的说明。

使用 NFS 协议和 IP 地址装载客户端不需要 DNS。 如果想使用域名而不是 IP 地址来访问硬件 NAS 系统，或者你的工作流包括某些高级协议设置，则需要 DNS。

用于向客户端分发地址的 DNS 系统不需要由 HPC 缓存访问。 在某些情况下，你可能希望为缓存本身使用自定义 DNS 系统，但配置该系统比设置此类客户端循环系统复杂得多。 如果正在考虑[将 HPC 缓存的 DNS 服务器更改为自定义系统](configuration.md#set-a-custom-dns-configuration)，则必须咨询 Azure 支持。

### <a name="configure-round-robin-distribution-for-cache-mount-points"></a>为缓存装载点配置循环分配

循环 DNS (RRDNS) 系统将自动在多个地址之间路由客户端请求。

要设置此系统，需要自定义 DNS 服务器的配置文件，使其在收到对 HPC Cache 主域地址的装载请求时，在所有 HPC 缓存系统的装载点之间分配流量。 客户端使用其域名作为服务器参数来装载 HPC 缓存，并自动路由到下一个挂载 IP。

配置 RRDNS 有两个主要步骤：

1. 修改 DNS 服务器的 ``named.conf`` 文件，以设置查询到 HPC 服务器的循环顺序。 此选项会导致服务器循环使用所有可用 IP 值。 添加如下语句：

   ```bash
   options {
       rrset-order {
           class IN A name "hpccache.contoso.com" order cyclic;
       };
   };
   ```

1. 为每个可用 IP 地址配置 A 记录和指针 (PTR) 记录，如下例所示。

   ``nsupdate`` 命令提供了一个示例，演示如何为具有域名 hpccache.contoso.com 和三个装载地址（10.0.0.10、10.0.0.11 和 10.0.0.12）的 HPC 缓存正确配置 DNS：

   ```bash
   update add hpccache.contoso.com. 86400 A 10.0.0.10
   update add hpccache.contoso.com. 86400 A 10.0.0.11
   update add hpccache.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   这些命令为每个 HPC 缓存的安装地址创建一个 A 记录，并设置指针记录以适当支持反向 DNS 检查。

   下图显示了此配置的基本结构。

   :::image type="complex" source="media/round-robin-dns-diagram-hpc.png" alt-text="显示客户端装载点 DNS 配置的关系图。":::
   <该关系图显示了三类元素之间的连接：单个 HPC 缓存域名（左侧）、三个 IP 地址（中间列）和三个内部使用反向 DNS 客户端接口（右侧列）。 左侧标记为“hpccache.contoso.com”的单个椭圆通过箭头连接，箭头指向标有 IP 地址 10.0.0.10、10.0.0.11 和 10.0.0.12 的三个椭圆。 从 hpccache.contoso.com 椭圆到三个 IP 椭圆的箭头标记为“A”。 每个 IP 地址椭圆都通过两个箭头连接到标记为客户端接口的椭圆 - IP 为 10.0.0.10 的椭圆连接到“client-IP-10.contoso.com”，IP 为“10.0.0.11”的椭圆连接到“client-IP-11.contoso.com”，IP 为 10.0.0.12 的椭圆连接到“client-IP-11.contoso.com”。 IP 地址椭圆和客户端接口椭圆之间的连接是两个箭头：一个箭头标记为“PTR”，从 IP 地址椭圆指向客户端接口椭圆，另一个箭头标记为“A”，从客户端接口椭圆指向 IP 地址椭圆。>:::image-end:::

配置完 RRDNS 系统后，告知你的客户端计算机使用该系统来解析其装载命令中的 HPC 缓存地址。

## <a name="next-steps"></a>后续步骤

* 如需客户端负载均衡方面的帮助，[请联系支持人员](hpc-cache-support-ticket.md)。
* 若要将数据移到缓存的存储目标，请参阅[填充新的 Azure Blob 存储](hpc-cache-ingest.md)。
