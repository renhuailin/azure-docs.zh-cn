---
title: 排查 Azure HPC 缓存 NFS 存储目标的问题
description: 有关在创建 NFS 存储目标时避免和修复配置错误以及其他可能导致失败的问题的提示
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: a8fb169db79281240e2aab854bc6079a60054625
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771443"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>排查 NAS 配置和 NFS 存储目标的问题

本文针对一些常见配置错误，以及其他可能导致 Azure HPC 缓存无法将 NFS 存储系统添加为存储目标的问题提供解决方法。

本文详细说明了如何检查端口，以及如何启用对 NAS 系统的 root 访问权限。 此外，其中还包含了有关可能导致 NFS 存储目标创建失败的不常见问题的详细信息。

> [!TIP]
> 在使用本指南之前，请先阅读 [NFS 存储目标的先决条件](hpc-cache-prerequisites.md#nfs-storage-requirements)。

如果本指南未提供你所遇到的问题的解决方法，请[开具支持票证](hpc-cache-support-ticket.md)，以便 Microsoft 服务和支持人员能够与你协作调查并解决问题。

## <a name="check-port-settings"></a>检查端口设置

Azure HPC 缓存需要对后端 NAS 存储系统上的多个 UDP/TCP 端口拥有读/写访问权限。 请确保 NAS 系统上的这些端口可访问，并且允许流量通过存储系统与缓存子网之间的任何防火墙进入这些端口。 可能需要与数据中心的防火墙和网络管理员协作来验证此配置。

不同的供应商对存储系统使用的端口各不相同，因此在设置存储目标时请检查系统要求。

一般情况下，缓存需要访问以下端口：

| 协议 | 端口  | 服务  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | mountd   |
| TCP/UDP  | 4047  | 状态   |

若要了解系统所需的具体端口，请使用以下 ``rpcinfo`` 命令。 以下命令在表格中列出了这些端口以及相关结果的格式。 （请使用你的系统 IP 地址替代 *<storage_IP>* 一词。）

可以从装有 NFS 基础结构的任何 Linux 客户端发出此命令。 如果在群集子网内部使用客户端，该客户端也可以帮助验证子网与存储系统之间的连接。

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

确保 ``rpcinfo`` 查询返回的所有端口允许来自 Azure HPC 缓存子网的无限制流量。

在 NAS 本身中以及存储系统与缓存子网之间的任何防火墙中检查这些设置。

## <a name="check-root-access"></a>检查 root 访问权限

Azure HPC 缓存需要访问存储系统的导出才能创建存储目标。 具体而言，它会将导出装载为用户 ID 0。

不同的存储系统使用不同的方法来实现这种访问：

* Linux 服务器通常会将 ``no_root_squash`` 添加到 ``/etc/exports`` 中的导出路径。
* NetApp 和 EMC 系统通常使用已关联到特定 IP 地址或网络的导出规则来控制访问。

如果使用导出规则，请记住，缓存可以使用缓存子网中的多个不同 IP 地址。 允许从整个可能的子网 IP 地址范围进行访问。

> [!NOTE]
> 尽管缓存需要对后端存储系统拥有 root 访问权限，但你可以限制通过缓存建立连接的客户端进行的访问。 有关详细信息，请参阅[控制客户端访问](access-policies.md#root-squash)。

与 NAS 存储供应商协作，为缓存启用适当的访问级别。

### <a name="allow-root-access-on-directory-paths"></a>允许对目录路径的 root 访问权限
<!-- linked in prereqs article -->

对于导出分层目录的 NAS 系统，Azure HPC 缓存需要对每个导出级别拥有 root 访问权限。

例如，某个系统可能显示如下所示的三个导出：

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

导出 ``/ifs/accounting/payroll`` 是 ``/ifs/accounting`` 的子级，而 ``/ifs/accounting`` 本身是 ``/ifs`` 的子级。

如果你添加 ``payroll`` 导出作为 HPC 缓存存储目标，则缓存实际上会装载 ``/ifs/`` 并从中访问 payroll 目录。 因此，Azure HPC 缓存需要对 ``/ifs`` 拥有 root 访问权限才能访问 ``/ifs/accounting/payroll`` 导出。

此要求与缓存对文件编制索引的方式有关，使用存储系统提供的文件句柄来避免文件冲突。

如果文件是从不同的导出检索的，则包含分层导出的 NAS 系统可为同一文件提供不同的文件句柄。 例如，某个客户端可能会装载 ``/ifs/accounting`` 并访问文件 ``payroll/2011.txt``。 而另一客户端会装载 ``/ifs/accounting/payroll`` 并访问该文件 ``2011.txt``。 根据存储系统分配文件句柄的方式，这两个客户端可能会收到具有不同文件句柄（一个用于 ``<mount2>/payroll/2011.txt``，一个用于 ``<mount3>/2011.txt``）的同一个文件。

后端存储系统保留文件句柄的内部别名，但 Azure HPC 缓存无法判断其索引中的哪些文件句柄引用了同一个项。 因此，缓存中可能会为同一个文件缓存不同的写入内容，并错误地应用更改，因为它不知道这两个文件是相同的。

为了避免多个导出中的文件发生这种潜在的文件冲突，Azure HPC 缓存会自动装载路径中最浅级别的可用导出（在本示例中为 ``/ifs``），并使用该导出提供的文件句柄。 如果多个导出使用同一基路径，则 Azure HPC 缓存需要对该路径拥有 root 访问权限。

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>调整 VPN 数据包大小限制
<!-- link in prereqs article and configuration article -->

如果在缓存与 NAS 设备之间使用 VPN，VPN 可能会阻止完整大小为 1500 字节的以太网数据包。 如果 NAS 与 Azure HPC 缓存实例之间的较大数据交换无法完成，而较小的更新可按预期方式传输，则可能表示出现了此问题。

除非你知道 VPN 配置的详细信息，否则不容易判断系统是否出现了此问题。 下面是可以帮助你检查此问题的几种方法。

* 在 VPN 两端使用数据包探查器来检测哪些数据包可成功传输。
* 如果 VPN 允许执行 ping 命令，则你可以发送一个完整大小的数据包进行测试。

  结合以下选项通过 VPN 对 NAS 运行 ping 命令。 （请使用你的存储系统 IP 地址替代 *<storage_IP>* 值。）

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  命令中的选项如下：

  * ``-M do`` - 不分段
  * ``-c 1`` - 仅发送一个数据包
  * ``-s 1472`` - 将有效负载的大小设置为 1472 字节。 这是在考虑到以太网开销后一个 1500 字节数据包的最大有效负载。

  成功响应如下所示：

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  如果发送 1472 字节后 ping 失败，则可能表示存在数据包大小问题。

若要解决该问题，可能需要在 VPN 中配置 MSS 钳制，使远程系统能够正确检测最大帧大小。 有关详细信息，请参阅 [VPN 网关 IPsec/IKE 参数文档](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)。

在某些情况下，将 Azure HPC 缓存的 MTU 设置更改为 1400 可能会有帮助。 但是，如果限制缓存中的 MTU，还必须限制与缓存交互的客户端和后端存储系统的 MTU 设置。 有关详细信息，请参阅[配置其他 Azure HPC 缓存设置](configuration.md#adjust-mtu-value)。

## <a name="check-for-acl-security-style"></a>检查 ACL 安全样式

某些 NAS 系统使用结合了访问控制列表 (ACL) 和传统 POSIX 或 UNIX 安全性的混合安全样式。

如果系统报告其安全样式为 UNIX 或 POSIX，但不包括首字母缩写词“ACL”，则此问题不会影响到你。

对于使用 ACL 的系统，Azure HPC 缓存需要跟踪其他特定于用户的值才能控制文件访问。 这是通过启用一个访问缓存来实现的。 没有任何面向用户的控件可用于启用访问缓存，但你可以开具支持票证，请求为缓存系统上的受影响存储目标启用访问缓存。

## <a name="next-steps"></a>后续步骤

如果本文未能解决你遇到的问题，请[联系支持人员](hpc-cache-support-ticket.md)以获得专家帮助。
