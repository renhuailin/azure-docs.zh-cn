---
title: 启用了 InfiniBand 的 H 系列和 N 系列 Azure 虚拟机的配置和优化
description: 了解如何针对 HPC 配置和优化启用了 InfiniBand 的 H 系列和 N 系列 VM。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 06/02/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: cd040ef394163113b46f9af46aef0aead9cb37ca
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689275"
---
# <a name="configure-and-optimize-vms"></a>配置和优化 VM

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

本文分享了有关如何针对 HPC 配置和优化启用了 InfiniBand 的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 的指导。

## <a name="vm-images"></a>VM 映像
在启用了 InfiniBand (IB) 的 VM 上，需要安装适当的驱动程序才能启用 RDMA。
- Azure 市场中的 [CentOS-HPC VM 映像](#centos-hpc-vm-images)预先配置了适当的 IB 驱动程序。
  - 另外，CentOS-HPC 版本 7.9 VM 映像还预配置了 Nvidia GPU 驱动程序。 
- Azure 市场中的 [Ubuntu-HPC VM 映像](#ubuntu-hpc-vm-images)预先配置了适当的 IB 驱动程序和 GPU 驱动程序。

这些 VM 映像 (VMI) 基于基础 CentOS 和 Ubuntu 市场 VM 映像。 用于从基础 CentOS 市场映像创建这些 VM 映像的脚本位于 [azhpc-images 存储库](https://github.com/Azure/azhpc-images/tree/master/centos)中。

在启用了 GPU 的 [N 系列](../../sizes-gpu.md) VM 上，需要额外安装适当的 GPU 驱动程序。 可通过以下方法安装这些驱动程序：
- 使用预先配置了 Nvidia GPU 驱动程序和 GPU 计算软件堆栈（CUDA、NCCL）的 [Ubuntu-HPC VM 映像](#ubuntu-hpc-vm-images)和 [CentOS-HPC VM 映像](#centos-hpc-vm-images)版本 7.9。
- 通过 [VM 扩展](../../extensions/hpccompute-gpu-linux.md)添加 GPU 驱动程序。
- [手动](../../linux/n-series-driver-setup.md)安装 GPU 驱动程序。
- Azure 市场中的其他一些 VM 映像也预装了 Nvidia GPU 驱动程序，包括来自 Nvidia 的一些 VM 映像。

根据工作负载的 Linux 发行版和版本需求，可以十分方便地通过 Azure 市场中的 [CentOS-HPC VM 映像](#centos-hpc-vm-images)和 [Ubuntu-HPC VM 映像](#ubuntu-hpc-vm-images)在 Azure 上开始使用 HPC 和 AI 工作负载。
此外，建议使用特定于工作负载的自定义和配置创建[自定义 VM 映像](../../linux/tutorial-custom-images.md)，并重复使用这些映像。

### <a name="vm-sizes-supported-by-the-hpc-vm-images"></a>HPC VM 映像支持的 VM 大小

#### <a name="infiniband-ofed-support"></a>InfiniBand OFED 支持
最新的 Azure HPC 市场映像随附 Mellanox OFED 5.1 和更高版本，它们不支持 ConnectX3-Pro InfiniBand 卡。 这些 VM 映像仅支持 ConnextX-5 和更高版本的 InfiniBand 卡。 这意味着，这些 HPC VM 映像中 InfiniBand OFED 的 VM 大小支持矩阵如下：
- [H 系列](../../sizes-hpc.md)：HB、HC、HBv2、HBv3
- [N 系列](../../sizes-gpu.md)：NDv2、NDv4

#### <a name="gpu-driver-support"></a>GPU 驱动程序支持
目前只有 [Ubuntu-HPC VM 映像](#ubuntu-hpc-vm-images)和 [CentOS-HPC VM 映像](#centos-hpc-vm-images)版本 7.9 预先配置了 Nvidia GPU 驱动程序和 GPU 计算软件堆栈（CUDA、NCCL）。

受支持 HPC VM 映像中 GPU 驱动程序的 VM 大小支持矩阵如下：
- [N 系列](../../sizes-gpu.md)：Nvidia GPU 驱动程序和 GPU 计算软件堆栈（CUDA、NCCL）支持 NDv2、NDv4 VM 大小。
- Nvidia GPU 驱动程序支持 [N 系列](../../sizes-gpu.md)中的其他“NC”和“ND”VM 大小。

另请注意，上述所有 VM 大小都支持“第 2 代”VM，不过，某些早期大小也支持“第 1 代”VM。 VMI URN 或版本的末尾还会以“01”来指示支持“第 2 代”。

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM 映像

#### <a name="sr-iov-enabled-vms"></a>启用了 SR-IOV 的 VM
对于启用了 SR-IOV 的[支持 RDMA 的 VM](../../sizes-hpc.md#rdma-capable-instances)，适合使用 CentOS-HPC VM 映像版本 7.6 和更高版本。 这些 VM 映像已经过优化，并预先加载了用于 RDMA 的 Mellanox OFED 驱动程序以及各种常用的 MPI 库和科学计算包。 请参阅上面的 [VM 大小支持矩阵](#vm-sizes-supported-by-the-hpc-vm-images)。
- 可以使用 [CLI](/cli/azure/vm/image#az_vm_image_list) 或 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview)将 VM 映像的可用版本或最新版本随以下信息一起列出。
   ```bash
   "publisher": "OpenLogic",
   "offer": "CentOS-HPC",
   ```
- 从基础 CentOS 市场映像创建 CentOS-HPC 7.6 版本及更高版本的 VM 映像时所用的脚本位于 [azhpc-images 存储库](https://github.com/Azure/azhpc-images/tree/master/centos)中。
- 此外，此 [TechCommunity 文章](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094)中详细介绍了 CentOS-HPC 版本 7.6 和更高版本的 VM 映像中包含的内容，以及如何部署这些映像。

> [!NOTE] 
> 在 CentOS-HPC VM 映像中，目前仅版本 7.9 VM 映像额外预配置了 Nvidia GPU 驱动程序和 GPU 计算软件堆栈（CUDA、NCCL）。

> [!NOTE] 
> 采用 FDR InfiniBand 的启用了 SR-IOV 的 N 系列 VM 大小（例如 NCv3 及更低版本）将能够使用市场中的以下 CentOS-HPC VM 映像或更低版本：
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>未启用 SR-IOV 的 VM
对于未启用 SR-IOV 的[支持 RDMA 的 VM](../../sizes-hpc.md#rdma-capable-instances)，适合使用 CentOS-HPC 6.5 版本或更高版本（市场中的最高版本为 7.4）。 例如，对于 [H16 系列 VM](../../h-series.md)，建议使用 7.1-7.4 版本。 这些 VM 映像预先加载了用于 RDMA 和 Intel MPI 5.1 版本的 Network Direct 驱动程序。

> [!NOTE]
> 在这些基于 CentOS 的 HPC 映像（适用于未启用 SR-IOV 的 VM）中，内核更新已在 yum 配置文件中禁用。 这是因为 NetworkDirect Linux RDMA 驱动程序以 RPM 包的形式分发，如果更新了内核，驱动程序更新可能无法正常工作。

### <a name="ubuntu-hpc-vm-images"></a>Ubuntu-HPC VM 映像
对于启用了 SR-IOV 的[支持 RDMA 的 VM](../../sizes-hpc.md#rdma-capable-instances)，适合使用 Ubuntu-HPC VM 映像版本 18.04 和 20.04。 这些 VM 映像已经过优化，并预先加载了用于 RDMA 的 Mellanox OFED 驱动程序、Nvidia GPU 驱动程序、GPU 计算软件堆栈（CUDA、NCCL），以及各种常用的 MPI 库和科学计算包。 请参阅上面的 [VM 大小支持矩阵](#vm-sizes-supported-by-the-hpc-vm-images)。
- 可以使用 [CLI](/cli/azure/vm/image#az_vm_image_list) 或 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-hpc?tab=overview)将 VM 映像的可用版本或最新版本随以下信息一起列出。
   ```bash
   "publisher": "Microsoft-DSVM",
   "offer": "Ubuntu-HPC",
   ```
- 用于从基础 Ubuntu 市场映像创建 Ubuntu-HPC VM 映像的脚本位于 [azhpc-images 存储库](https://github.com/Azure/azhpc-images/tree/master/ubuntu)中。
- 此外，此 [TechCommunity 文章](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094)中详细介绍了 Ubuntu-HPC VM 映像中包含的内容，以及如何部署这些映像。

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM 映像
可以对 Azure 市场中基于基础 RHEL 或 CentOS 的非 HPC VM 映像进行配置，以便在启用了 SR-IOV 的[支持 RDMA 的 VM](../../sizes-hpc.md#rdma-capable-instances) 上使用。 详细了解如何在 VM 上[启用 InfiniBand](enable-infiniband.md) 和[设置 MPI](setup-mpi.md)。
- 也可使用 [azhpc-images 存储库](https://github.com/Azure/azhpc-images/tree/master/centos)中用于从基础 CentOS 市场映像创建 CentOS-HPC 7.6 版本及更高版本的 VM 映像的脚本。
 
### <a name="ubuntu-vm-images"></a>Ubuntu VM 映像
启用和未启用 SR-IOV 的[支持 RDMA 的 VM](../../sizes-hpc.md#rdma-capable-instances) 都支持 Azure 市场中的基础 Ubuntu Server 16.04 LTS、18.04 LTS 和 20.04 LTS VM 映像。 详细了解如何在 VM 上[启用 InfiniBand](enable-infiniband.md) 和[设置 MPI](setup-mpi.md)。
- [TechCommunity 一文](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)提供有关如何在 Ubuntu VM 映像中启用 InfiniBand 的说明。
- 从基础 Ubuntu 市场映像创建基于 Ubuntu 18.04 和 20.04 LTS 的 HPC VM 映像时所用的脚本位于 [azhpc-images 存储库](https://github.com/Azure/azhpc-images/tree/master/ubuntu)中。

> [!NOTE]
> Mellanox OFED 5.1 及更高版本在采用 FDR InfiniBand 的启用了 SR-IOV 的 N 系列 VM 大小（例如 NCv3）中不支持 ConnectX3-Pro InfiniBand 卡。 请在带有 ConnectX3-Pro 卡的 N 系列 VM 上使用 LTS Mellanox OFED 4.9-0.1.7.0 版本或更低版本。 有关详细信息，请参阅 [Linux InfiniBand 驱动程序](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)。

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM 映像
支持市场中的 SLES 12 SP3 for HPC、SLES 12 SP3 for HPC（高级版）、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC（高级版）、SLES 12 SP4 和 SLES 15 VM 映像。 这些 VM 映像预先加载了用于 RDMA 的 Network Direct 驱动程序（适用于非 SR-IOV VM 大小）和 Intel MPI 版本 5.1。 详细了解如何在 VM 上[设置 MPI](setup-mpi.md)。

## <a name="optimize-vms"></a>优化 VM

下面是一些可选的优化设置，可用于改进 VM 的性能。

### <a name="update-lis"></a>更新 LIS

如果出于功能或性能考虑必需更新 LIS，可在支持的 OS 发行版上安装或更新 [Linux Integration Services (LIS) 驱动程序](../../linux/endorsed-distros.md)，特别是使用自定义映像或较低的 OS 版本（如 CentOS/RHEL 6.x 或较低的 7.x 版本）部署时。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>回收内存

通过自动回收内存避免远程内存访问，从而提高性能。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

在 VM 重启后保持回收模式：

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>禁用防火墙和 SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>禁用 cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>配置 WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
（可选）请在作业前禁用 WALinuxAgent，然后在作业后重新将其启用，为 HPC 工作负载最大限度提高 VM 资源的可用性。


## <a name="next-steps"></a>后续步骤

- 详细了解如何在启用了 InfiniBand 的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 上[启用 InfiniBand](enable-infiniband.md)。
- 详细了解如何在 VM 上安装和运行各种[受支持的 MPI 库](setup-mpi.md)。
- 查看 [HBv3 系列概述](hbv3-series-overview.md)和 [HC 系列概述](hc-series-overview.md)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
