---
title: 在 HPC VM 上启用 InfiniBand - Azure 虚拟机 | Microsoft Docs
description: 了解如何在 Azure HPC VM 上启用 InfiniBand。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/28/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 563cbf412fa8bb522b835fe41849f8358f5303fb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689239"
---
# <a name="enable-infiniband"></a>启用 InfiniBand

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

[支持 RDMA 的](../../sizes-hpc.md#rdma-capable-instances) [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 通过低延迟的高带宽 InfiniBand 网络进行通信。 此类互连上的 RDMA 功能对于提高分布式节点 HPC 和 AI 工作负载的可伸缩性和性能至关重要。 支持 InfiniBand 的 H 系列和 N 系列 VM 采用非阻塞性胖树进行连接，使用的小直径旨在实现一致的优化 RDMA 性能。

可以通过多种方法在各种 VM 大小上启用 InfiniBand。

## <a name="vm-images-with-infiniband-drivers"></a>具有 InfiniBand 驱动程序的 VM 映像
请参阅 [VM 映像](configure.md#vm-images)，了解市场中支持的一系列 VM 映像。InfiniBand 驱动程序（适用于 SR-IOV 或非 SR-IOV VM）中预加载了这些映像，也可以使用适用于[支持 RDMA 的 VM](../../sizes-hpc.md#rdma-capable-instances) 的适当驱动程序配置这些映像。  市场中的 [CentOS-HPC](configure.md#centos-hpc-vm-images) 和 [Ubuntu-HPC](configure.md#ubuntu-hpc-vm-images) VM 映像是最简单的入门方法。

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand 驱动程序 VM 扩展
在 Linux 上，可以使用 [InfiniBandDriverLinux VM 扩展](../../extensions/hpc-compute-infiniband-linux.md)安装 Mellanox OFED 驱动程序，并在已启用 SR-IOV 的 H 和 N 系列 VM 上启用 InfiniBand。

在 Windows 上，[InfiniBandDriverWindows VM 扩展](../../extensions/hpc-compute-infiniband-windows.md)可安装 Windows Network Direct 驱动程序（在非 SR-IOV VM 上）或 Mellanox OFED 驱动程序（在 SR-IOV VM 上），以建立 RDMA 连接。 在 A8 和 A9 实例的某些部署中，会自动添加 HpcVmDrivers 扩展。 请注意，HpcVmDrivers VM 扩展即将弃用；它不再会更新。

若要将 VM 扩展添加到 VM，可以使用 [Azure PowerShell](/powershell/azure/) cmdlet。 有关详细信息，请参阅[虚拟机扩展和功能](../../extensions/overview.md)。 还可使用[经典部署模型](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 扩展。

## <a name="manual-installation"></a>手动安装
可以在[启用了 SR-IOV](../../sizes-hpc.md#rdma-capable-instances) 的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 上手动安装 [Mellanox OpenFabrics 驱动程序 (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software)。

### <a name="linux"></a>Linux
可以按照以下示例安装[适用于 Linux 的 OFED 驱动程序](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)。 尽管此处的示例适用于 RHEL/CentOS，但步骤是通用的，可用于任何兼容的 Linux 操作系统，如 Ubuntu（16.04、18.04、19.04 和 20.04）和 SLES（12 SP4 和 15）。 有关其他发行版的更多示例，请参阅 [azhpc-images 存储库](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh)。 也可以使用收件箱驱动程序，但 Mellanox OFED 驱动程序提供了更多功能。

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
对于 Windows，请下载并安装[适用于 Windows 驱动程序的 Mellanox OFED](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)。

## <a name="enable-ip-over-infiniband-ib"></a>启用基于 InfiniBand (IB) 的 IP
如果计划运行 MPI 作业，则通常不需要 IPoIB。 MPI 库将使用谓词接口进行 IB 通信（除非你明确使用 MPI 库的 TCP/IP 通道）。 但如果你的应用使用 TCP/IP 进行通信，并且你想要通过 IB 运行，则你可以通过 IB 接口使用 IPoIB。 使用以下命令（适用于 RHEL/CentOS）启用基于 InfiniBand 的 IP。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>后续步骤

- 详细了解如何在 VM 上安装和运行各种[受支持的 MPI 库](setup-mpi.md)。
- 查看 [HBv3 系列概述](hbv3-series-overview.md)和 [HC 系列概述](hc-series-overview.md)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
