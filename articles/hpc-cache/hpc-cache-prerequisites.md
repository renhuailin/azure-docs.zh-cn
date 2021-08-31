---
title: Azure HPC 缓存先决条件
description: 使用 Azure HPC 缓存所要满足的先决条件
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/06/2021
ms.author: v-erkel
ms.openlocfilehash: 202f942bc4fb47b2c71802667e31e08de7cfe13e
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588806"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC 缓存的先决条件

在使用 Azure 门户创建新的 Azure HPC 缓存之前，请确保你的环境满足这些要求。

## <a name="video-overviews"></a>视频概述

请观看这些视频，其中简要概述了系统的组件，以及这些组件需要满足哪些条件才能一起协作。

（单击视频图像或链接即可观看。）

* [工作原理](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) - 介绍 Azure HPC 缓存如何与存储和客户端交互

  [![视频缩略图：Azure HPC 缓存：工作原理（单击可访问视频页）](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [先决条件](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) - 说明 NAS 存储、Azure Blob 存储、网络访问和客户端访问的要求

  [![视频缩略图：Azure HPC 缓存：先决条件（单击可访问视频页）](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

如需具体的建议，请阅读本文的余下内容。

## <a name="azure-subscription"></a>Azure 订阅

建议使用付费订阅。

## <a name="network-infrastructure"></a>网络基础结构

应该先设置以下两个与网络相关的必备组件，然后才能使用缓存：

* Azure HPC 缓存实例的专用子网
* DNS 支持，使缓存能够访问存储和其他资源

### <a name="cache-subnet"></a>缓存子网

Azure HPC 缓存需要具有以下特质的专用子网：

* 该子网必须至少提供 64 个可用 IP 地址。
* 该子网不能托管任何其他 VM，即使是客户端计算机等相关服务的 VM。
* 如果使用多个 Azure HPC 缓存实例，则每个实例都需要其自身的子网。

最佳做法是为每个缓存创建一个新子网。 可以在创建缓存的过程中创建新的虚拟网络和子网。

### <a name="dns-access"></a>DNS 访问

缓存需要通过 DNS 访问其虚拟网络外部的资源。 根据所用的资源，可能需要设置一个自定义的 DNS 服务器，并在该服务器与 Azure DNS 服务器之间配置转发：

* 若要访问 Azure Blob 存储终结点和其他内部资源，需要使用基于 Azure 的 DNS 服务器。
* 若要访问本地存储，需要配置一个可解析存储主机名的自定义 DNS 服务器。 必须在创建缓存之前完成此配置。

如果只使用 Blob 存储，则可以使用 Azure 为缓存提供的默认 DNS 服务器。 但是，如果需要访问 Azure 外部的存储或其他资源，则应创建一个自定义 DNS 服务器，并将其配置为向 Azure DNS 服务器转发任何特定于 Azure 的解析请求。

若要使用自定义 DNS 服务器，需要在创建缓存之前执行以下设置步骤：

* 创建用于托管 Azure HPC 缓存的虚拟网络。
* 创建 DNS 服务器。
* 将 DNS 服务器添加到缓存的虚拟网络。

  在 Azure 门户中遵循以下步骤将 DNS 服务器添加到虚拟网络：

  1. 在 Azure 门户中打开虚拟网络。
  1. 从边栏上的“设置”菜单中选择“DNS 服务器”。 
  1. 选择“自定义”
  1. 在字段中输入 DNS 服务器的 IP 地址。

也可以使用一个简单的 DNS 服务器对所有可用缓存装入点中的客户端连接进行负载均衡。

请在 [Azure 虚拟网络中的资源的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)中详细了解 Azure 虚拟网络和 DNS 服务器配置。

## <a name="permissions"></a>权限

在开始创建缓存之前，请检查这些与权限相关的先决条件。

* 缓存实例需要能够创建虚拟网络接口 (NIC)。 创建缓存的用户必须在订阅中拥有可创建 NIC 的足够特权。

* 如果使用 Blob 存储，Azure HPC 缓存需要获得访问你的存储帐户的授权。 使用 Azure 基于角色的访问控制 (Azure RBAC) 向缓存授予对 Blob 存储的访问权限。 需要两个角色：“存储帐户参与者”和“存储 Blob 数据参与者”。

  按照[添加存储目标](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的说明添加角色。

## <a name="storage-infrastructure"></a>存储基础结构
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

缓存支持 Azure Blob 容器、NFS 硬件存储导出和已装载 NFS 的 ADLS Blob 容器。 创建缓存后添加存储目标。

缓存的大小决定其可支持的存储目标数，大多数缓存最多可支持 10 个存储目标，最大的缓存最多可支持 20 个存储目标。 有关详细信息，请参阅[正确调整缓存大小以支持存储目标](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)。

每种存储类型都有特定的先决条件。

### <a name="blob-storage-requirements"></a>Blob 存储要求

若要将 Azure Blob 存储与缓存配合使用，需要一个兼容的存储帐户，以及一个空的 Blob 容器，或者一个已按照[将数据移到 Azure Blob 存储](hpc-cache-ingest.md)中所述填充了 Azure HPC 缓存格式的数据的容器。

> [!NOTE]
> 已装载 NFS 的 Blob 存储适用不同的要求。 有关详细信息，请阅读 [ADLS-NFS 存储要求](#nfs-mounted-blob-adls-nfs-storage-requirements)。

在尝试添加存储目标之前创建帐户。 添加目标时可以创建新容器。

若要创建兼容的存储帐户，请使用以下组合之一：

| 性能 | 类型 | 复制 | 访问层 |
|--|--|--|--|
| 标准 | StorageV2（常规用途 v2）| 本地冗余存储 (LRS) 或区域冗余存储 (ZRS) | 热 |
| Premium | 块 Blob | 本地冗余存储 (LRS) | 热 |

必须可以从缓存的专用子网访问存储帐户。 如果帐户使用专用终结点或者仅限于特定虚拟网络的公共终结点，请确保支持从缓存的子网进行访问。 （不建议使用开放的公共端点。）

最好是使用与缓存位于同一 Azure 区域的存储帐户。

此外，必须按照前面的[权限](#permissions)中所述，向缓存应用程序授予对你的 Azure 存储帐户的访问权限。 遵循[添加存储目标](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的过程为缓存提供所需的访问角色。 如果你不是存储帐户所有者，请让所有者执行此步骤。

### <a name="nfs-storage-requirements"></a>NFS 存储要求
<!-- linked from configuration.md and add storage -->

如果使用 NFS 存储系统（例如本地硬件 NAS 系统），请确保该系统满足这些要求。 可能需要与存储系统（或数据中心）的网络管理员或防火墙管理者协作来验证这些设置。

> [!NOTE]
> 如果缓存对 NFS 存储系统的访问权限不足，创建存储目标将会失败。

[排查 NAS 配置和 NFS 存储目标问题](troubleshoot-nas.md)中提供了详细信息。

* 网络连接： Azure HPC 缓存需要在缓存子网与 NFS 系统的数据中心之间进行高带宽网络访问。 建议使用 [ExpressRoute](../expressroute/index.yml) 或类似的访问方式。 如果使用 VPN，可能需要对其进行配置，以将 TCP MSS 钳制在 1350，确保不会阻止较大的数据包。 如需排查 VPN 设置问题方面的更多帮助，请阅读 [VPN 数据包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)。

* 端口访问： 缓存需要访问存储系统上的特定 TCP/UDP 端口。 不同类型的存储具有不同的端口要求。

  若要检查存储系统的设置，请遵循以下过程。

  * 向存储系统发出 `rpcinfo` 命令以检查所需的端口。 以下命令在表格中列出了这些端口以及相关结果的格式。 （请使用你的系统 IP 地址替代 *<storage_IP>* 一词。）

    可以从装有 NFS 基础结构的任何 Linux 客户端发出此命令。 如果在群集子网内部使用客户端，该客户端也可以帮助验证子网与存储系统之间的连接。

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  确保 ``rpcinfo`` 查询返回的所有端口允许来自 Azure HPC 缓存子网的无限制流量。

  * 如果无法使用 `rpcinfo` 命令，请确保这些常用端口允许入站和出站流量：

    | 协议 | 端口  | 服务  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | 状态   |

    某些系统对这些服务使用不同的端口号 - 请查阅存储系统的文档来确认端口号。

  * 检查防火墙设置，确保它们允许所有这些必需端口上的流量。 请务必检查 Azure 中使用的防火墙以及数据中心内的本地防火墙。

* Root 访问权限（读/写）：缓存以用户 ID 0 的身份连接到后端系统。 在存储系统上检查以下设置：
  
  * 启用 `no_root_squash`。 此选项确保远程 root 用户可以访问 root 拥有的文件。

  * 检查导出策略，确保其中不包含针对 root 从缓存子网进行访问的限制。

  * 如果存储中的任何导出是另一个导出的子目录，请确保缓存对最低路径段拥有 root 访问权限。 有关详细信息，请阅读 NFS 存储目标故障排除文章中的[对目录路径的 Root 访问权限](troubleshoot-nas.md#allow-root-access-on-directory-paths)。

* NFS 后端存储必须是兼容的硬件/软件平台。 如需详细信息，请联系 Azure HPC 缓存团队。

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements"></a>已装载 NFS 的 Blob (ADLS-NFS) 存储要求

Azure HPC 缓存还可以使用已装载 NFS 协议的 Blob 容器作为存储目标。

有关此功能的详细信息，请阅读 [Azure Blob 存储中的 NFS 3.0 协议支持](../storage/blobs/network-file-system-protocol-support.md)。

ADLS-NFS Blob 存储目标和标准 Blob 存储目标的存储帐户要求不同。 请仔细按照[使用网络文件系统 (NFS) 3.0 协议装载 Blob 存储](../storage/blobs/network-file-system-protocol-support-how-to.md)中的说明创建并配置支持 NFS 的存储帐户。

下面是步骤的一般性概述。 这些步骤可能有变化，因此请始终参阅 [ADLS-NFS 说明](../storage/blobs/network-file-system-protocol-support-how-to.md)了解最新详细信息。

1. 确保所需的功能在你计划的工作区域中可用。

1. 为订阅启用 NFS 协议功能。 请在创建存储帐户之前执行此操作。

1. 为存储帐户创建安全虚拟网络 (VNet)。 应该对支持 NFS 的存储帐户和 Azure HPC 缓存使用同一个虚拟网络。 （不要使用缓存所用的同一个子网。）

1. 创建存储帐户。

   * 不要使用标准 Blob 存储帐户的存储帐户设置，而是按照[操作指南文档](../storage/blobs/network-file-system-protocol-support-how-to.md)中的说明操作。 支持的存储帐户类型可能根据 Azure 区域而异。

   * 在“网络”部分，选择创建的安全虚拟网络中的某个专用终结点（建议），或者选择该安全 VNet 中某个访问权限受限的公共终结点。

   * 请记得完成“高级”部分，需在其中启用 NFS 访问。

   * 按照前面的[权限](#permissions)中所述，向缓存应用程序授予对你的 Azure 存储帐户的访问权限。 可以在首次创建存储目标时执行此操作。 遵循[添加存储目标](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的过程为缓存提供所需的访问角色。

     如果你不是存储帐户所有者，请让所有者执行此步骤。

若要详细了解如何将 ADLS-NFS 存储目标与 Azure HPC 缓存配合使用，请参阅[将 NFS 装载的 Blob 存储与 AZURE HPC 缓存配合使用](nfs-blob-considerations.md)。

## <a name="set-up-azure-cli-access-optional"></a>设置 Azure CLI 访问（可选）

若要通过 Azure 命令行接口 (Azure CLI) 创建或管理 Azure HPC 缓存，需要安装 CLI 软件和 hpc-cache 扩展。 请按照[安装用于 Azure HPC 缓存的 Azure CLI](az-cli-prerequisites.md) 中的说明操作。

## <a name="next-steps"></a>后续步骤

* 从 Azure 门户[创建 Azure HPC 缓存实例](hpc-cache-create.md)
