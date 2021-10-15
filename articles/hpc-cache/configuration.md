---
title: 配置 Azure HPC 缓存设置
description: 介绍如何为缓存配置其他设置（例如 MTU、自定义 NTP 和 DNS 配置），以及如何从 Azure Blob 存储目标访问快速快照。
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 56aae1a4bce6b4226918b5b34309a8f8dd647e78
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275784"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>配置其他 Azure HPC 缓存设置

Azure 门户中的“网络”页提供了用于自定义多个设置的选项。 大多数用户不需要将这些设置从默认值更改为其他值。

本文还将介绍如何对 Azure Blob 存储目标使用快照功能。 快照功能没有可配置的设置。

若要查看设置，请在 Azure 门户中打开缓存的“网络”页。

![Azure 门户中“网络”页的屏幕截图](media/networking-page.png)

> [!NOTE]
> 此页的旧版本包含缓存级根 squash 设置，但此设置现已迁移到[客户端访问策略](access-policies.md)。

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>调整 MTU 值
<!-- linked from troubleshoot-nas article -->

可以使用标有“MTU 大小”的下拉菜单选择缓存的最大传输单元大小。

默认值为 1500 字节，但可将其更改为 1400。

> [!NOTE]
> 如果降低缓存的 MTU 大小，请确保与缓存通信的客户端和存储系统采用相同的 MTU 设置或更小的值。

降低缓存 MTU 值有助于应对缓存网络中其余组件存在的数据包大小限制。 例如，某些 VPN 无法成功传输完整大小为 1500 字节的数据包。 减小通过 VPN 发送的数据包大小可能会消除这一问题。 但请注意，降低缓存 MTU 设置意味着与缓存通信的任何其他组件（包括客户端和存储系统）也必须降低 MTU 设置，这样才能避免通信问题。

如果你不想要更改其他系统组件上的 MTU 设置，则不应降低缓存的 MTU 设置。 还可以通过其他方法应对 VPN 数据包大小限制。 请参阅 NAS 故障排除文章中的[调整 VPN 数据包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)来详细了解如何诊断和解决此问题。

参阅 [Azure VM 的 TCP/IP 性能优化](../virtual-network/virtual-network-tcpip-performance-tuning.md)来详细了解 Azure 虚拟网络中的 MTU 设置。

## <a name="customize-ntp"></a>自定义 NTP

缓存默认使用基于 Azure 的时间服务器 time.windows.com。 如果你希望缓存使用其他 NTP 服务器，请在“NTP 配置”部分中指定。 请使用完全限定的域名或 IP 地址。

## <a name="set-a-custom-dns-configuration"></a>设置自定义 DNS 配置

> [!CAUTION]
> 如果没有必要，请不要更改缓存 DNS 配置。 配置不当可能导致极其严重的后果。 如果配置无法解析 Azure 服务名称，HPC 缓存实例将永久不可访问。
>
> 在尝试设置自定义 DNS 配置之前，请咨询 Azure 代表。

Azure HPC 缓存已自动配置为使用安全便捷的 Azure DNS 系统。 但是，某些不寻常的配置要求缓存使用单独的本地 DNS 系统而不是 Azure 系统。 “网络”页的“DNS 配置”部分用于指定此类系统。 

请咨询 Azure 代表或 Microsoft 服务和支持人员来确定是否需要使用自定义缓存 DNS 配置。

如果你为要使用的 Azure HPC 缓存配置了自己的本地 DNS 系统，必须确保本地 DNS 服务器能够直接解析 Azure 服务终结点名称。 如果 DNS 服务器受限，无法进行公共名称解析，则 HPC 缓存将无法正常运行。

在将 DNS 配置用于 Azure HPC 缓存之前，请检查它是否能够成功解析以下各项：

* ``*.core.windows.net``
* 证书吊销列表 (CRL) 下载和联机证书状态协议 (OCSP) 验证服务。 此 [Azure TLS 文章](../security/fundamentals/tls-certificate-changes.md)末尾的[防火墙规则项](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me)中提供了部分列表，但你应该咨询 Microsoft 技术代表来了解所有要求。
* NTP 服务器（time.windows.com 或自定义服务器）的完全限定域名

如果需要为缓存设置自定义 DNS 服务器，请使用提供的字段：

* **DNS 搜索域**（可选）- 输入搜索域，例如 ``contoso.com``。 允许指定单个值，也可以将其留空。
* **DNS 服务器** - 最多输入三个 DNS 服务器。 按 IP 地址指定服务器。

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

在生产环境中使用 DNS 设置之前，请考虑使用测试缓存来检查和优化该设置。

### <a name="refresh-storage-target-dns"></a>刷新存储目标 DNS

如果 DNS 服务器更新了 IP 地址，关联的 NFS 存储目标将暂时不可用。 请参阅[查看和管理存储目标](manage-storage-targets.md#update-ip-address-custom-dns-configurations-only)，了解如何更新自定义 DNS 系统 IP 地址。

## <a name="view-snapshots-for-blob-storage-targets"></a>查看 Blob 存储目标的快照

Azure HPC 缓存会自动保存 Azure Blob 存储目标的存储快照。 快照为后端存储容器的内容提供了一个快速参照点。

快照不能取代数据备份，它们不包含有关缓存数据状态的任何信息。

> [!NOTE]
> 此快照功能不同于 NetApp 或 Isilon 存储软件中包含的快照功能。 在创建快照之前，这些快照实现会将缓存中的更改刷新到后端存储系统。
>
> 出于效率原因，Azure HPC 缓存快照不会首先刷新更改，而是仅记录已写入到 Blob 容器的数据。 此快照不代表缓存数据的状态，因此它可能不包含最近的更改。

此功能仅适用于 Azure Blob 存储目标，且其配置无法更改。

每隔 8 小时就会创建快照，时间分别是 UTC 0:00、08:00 和 16:00。

Azure HPC 缓存会持续存储每日、每周和每月快照，直到旧快照被新快照替换。 快照保留限制如下：

* 最多 20 个每日快照
* 最多 8 个每周快照
* 最多 3 个每月快照

从已装载的 Blob 存储目标的根目录中的 `.snapshot` 目录访问快照。
