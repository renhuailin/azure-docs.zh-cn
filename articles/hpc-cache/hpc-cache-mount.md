---
title: 装载 Azure HPC 缓存
description: 如何将客户端连接到 Azure HPC 缓存服务
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/27/2021
ms.author: v-erkel
ms.openlocfilehash: ab66fc29a29461e6808686aa53b520144b39a1e1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274190"
---
# <a name="mount-the-azure-hpc-cache"></a>装载 Azure HPC 缓存

创建缓存后，NFS 客户端可以通过一个简单的 `mount` 命令来访问该缓存。 该命令会将 Azure HPC 缓存上的特定存储目标路径连接到客户端计算机上的本地目录。

mount 命令由以下元素构成：

* 一个缓存装载地址（已在缓存概述页上列出）
* 为存储目标设置的一个虚拟命名空间路径（已在缓存命名空间页上列出）
* 要在客户端上使用的本地路径
* 用于优化这种 NFS 装载的成功结果的命令参数

缓存的“装载说明”页将为你收集信息和建议的选项，并创建一个可复制的原型 mount 命令。 有关详细信息，请参阅[使用装载说明实用工具](#use-the-mount-instructions-utility)。

## <a name="prepare-clients"></a>准备客户端

确保客户端能够遵循本部分中的指导原则装载 Azure HPC 缓存。

### <a name="provide-network-access"></a>提供网络访问权限

客户端计算机必须能够通过网络访问缓存的虚拟网络和专用子网。

例如，在同一虚拟网络中创建客户端 VM，或者在虚拟网络中使用终结点、网关或其他解决方案以便从外部访问。 （请记住，只应在缓存的子网中托管该缓存本身，此外不要托管其他任何组件。）

### <a name="install-utilities"></a>安装实用工具

安装适当的 Linux 实用工具软件来支持 NFS mount 命令：

* 对于 Red Hat Enterprise Linux 或 SuSE：`sudo yum install -y nfs-utils`
* 对于 Ubuntu 或 Debian：`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>创建本地路径

在要连接到缓存的每个客户端上创建一个本地目录路径。 为要装载的每个命名空间路径创建一个路径。

示例：`sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure 门户中的“[装载说明](#use-the-mount-instructions-utility)”页包含一个可复制的原型命令。

将客户端计算机连接到缓存时，会将此路径关联到表示存储目标导出的虚拟命名空间路径。 为客户端要使用的每个虚拟命名空间路径创建目录。

## <a name="use-the-mount-instructions-utility"></a>使用装载说明实用工具

可以使用 Azure 门户中的“装载说明”页来创建可复制的 mount 命令。 从门户中缓存视图的“配置”部分打开该页。

在客户端上使用该命令之前，请确保客户端满足先决条件，并且装有所需的软件，可以使用 NFS `mount` 命令，具体请参阅前面的[准备客户端](#prepare-clients)。

![门户中 Azure HPC 缓存实例的屏幕截图，其中加载了“配置”>“装载说明”页](media/mount-instructions.png)

遵循以下过程创建 mount 命令。

1. 自定义“客户端路径”字段。 此字段提供了一个示例命令，你可以使用该命令在客户端上创建本地路径。 客户端将在此目录本地访问 Azure HPC 缓存中的内容。

   单击该字段，并编辑示例命令以包含所需的目录名称。 该名称将显示在 `sudo mkdir -p` 后面的字符串末尾

   ![“客户端路径”字段的屏幕截图，其中的光标定位在末尾](media/mount-edit-client.png)

   编辑完字段后，页面底部的 mount 命令将会更新为包含新的客户端路径。

1. 从列表中选择“缓存装载地址”。 此菜单列出了缓存的所有[客户端装入点](#find-mount-command-components)。

   请平衡所有可用装载地址上的客户端负载，以获得更好的缓存性能。

   ![“缓存装载地址”字段的屏幕截图，其中的选择器显示了三个可供选择的 IP 地址](media/mount-select-ip.png)

1. 选择要用于客户端的“虚拟命名空间路径”。 这些路径链接到后端存储系统上的导出。

   ![显示“虚拟命名空间路径”字段的屏幕截图，其中的选择器已打开。](media/mount-select-target.png)

   可以在“命名空间”门户页上查看和更改虚拟命名空间路径。 有关操作方法，请参阅[设置聚合命名空间](add-namespace-paths.md)。

   若要详细了解 Azure HPC 缓存的聚合命名空间功能，请参阅[规划聚合命名空间](hpc-cache-namespace.md)。

1. “Mount 命令”字段自动填充了一个自定义的 mount 命令，该命令使用你在前面的字段中设置的装载地址、虚拟命名空间路径和客户端路径。

   单击该字段右侧的复制符号，将其自动复制到剪贴板。

   ![原型 mount 命令字段的屏幕截图，其中显示了“复制到剪贴板”按钮的鼠标悬停文本](media/mount-command-copy.png)

   它会在下面显示备用装载命令，这些命令具有相同的客户端路径和命名空间路径，但使用不同的缓存装载地址。 为了获得最佳性能，必须在 HPC 缓存上所有可用的地址中均匀地装载客户端。

1. 在客户端计算机上使用复制的 mount 命令以将其连接到 Azure HPC 缓存。 可以直接从客户端命令行发出该命令，也可以在客户端设置脚本或模板中包含该 mount 命令。

## <a name="understand-mount-command-syntax"></a>了解 mount 命令语法

mount 命令采用以下格式：

> sudo mount {options} cache_mount_address:/namespace_path local_path   

示例：

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

此命令成功后，存储导出的内容将在客户端上的 ``hpccache`` 目录中可见。

### <a name="mount-command-options"></a>Mount 命令选项

若要进行可靠的客户端装载，请在 mount 命令中传递以下设置和参数：

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| 建议的 mount 命令设置 | 说明 |
--- | ---
``hard`` | Azure HPC 缓存的软装载与应用程序故障和可能的数据丢失相关联。
``proto=tcp`` | 此选项支持适当处理 NFS 网络错误。
``mountproto=tcp`` | 此选项支持对装载操作的网络错误进行适当处理。
``retry=<value>`` | 设置 ``retry=30`` 以避免瞬时装载失败。 （建议在前景装载中使用不同的值。）

### <a name="find-mount-command-components"></a>查找 mount 命令组件

如果你希望在不使用“装载说明”页的情况下创建 mount 命令，可以在缓存的“概述”页上找到装载地址，并在“命名空间”页上找到虚拟命名空间路径  。

![Azure HPC 缓存实例的“概述”页的屏幕截图，其右下角的装载地址列表周围有一个突出显示框](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 缓存装载地址对应于缓存子网内部的网络接口。 在资源组中，将使用以 `-cluster-nic-` 结尾的名称和一个编号列出这些 NIC。 请不要更改或删除这些接口，否则缓存将不可用。

虚拟命名空间路径显示在缓存的“命名空间”设置页上。

![门户中“设置”>“命名空间”页的屏幕截图，其中，表的第一列“命名空间路径”周围有一个突出显示框](media/view-namespace-paths.png)

## <a name="use-all-available-mount-addresses"></a>使用所有可用的装载地址

必须在为该缓存列出的所有 IP 地址间分散客户端流量。 如果只在一个地址装载所有客户端，该缓存的性能将会受到影响。

可以通过手动或创建脚本的方式来为不同客户端选择不同的装载地址。 还可以使用为轮循 DNS (RRDNS) 配置的 DNS 服务器，在所有可用地址间自动轮换客户端装载。 请阅读[对 HPC 缓存流量进行负载均衡](client-load-balancing.md)，以了解详情。

## <a name="next-steps"></a>后续步骤

* 详细了解如何通过[均衡客户端负载](client-load-balancing.md)来使用所有缓存吞吐量。
* 若要将数据移到缓存的存储目标，请参阅[填充新的 Azure Blob 存储](hpc-cache-ingest.md)。
