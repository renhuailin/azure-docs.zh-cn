---
title: NP 系列 - Azure 虚拟机
description: NP 系列 VM 的规格。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: e009f24f1ce7d05c8890b197ea1208f2d01666d4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697955"
---
# <a name="np-series"></a>NP 系列 

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

NP 系列虚拟机由 [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA 提供支持，以加速工作负载，包括机器学习推理、视频转码以及数据库搜索和分析。 NP 系列 VM 还由全核 Turbo 时钟频率为 3.2GHz 的 Intel Xeon 8171M (Skylake) CPU 提供支持。

[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
VM 代系支持：第 1 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持（[预览版](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks)）<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | FPGA | FPGA 内存：GiB | 最大数据磁盘数 | 最大 NIC 数/预期网络带宽 (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>常见问题

问：如何请求 NP VM 的配额？

答：请按照此页面[按 VM 系列增加限制](../azure-portal/supportability/per-vm-quota-requests.md)。 NP VM 在美国东部、美国西部 2、西欧和东南亚提供。

问：我应使用哪个 Vitis 版本？ 

答：Xilinx 推荐 [Vitis 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)，你也可以使用开发 VM 市场选项（Vitis 2020.2 Development VM for Ubuntu 18.04 和 Centos 7.8）

问：是否需要使用 NP VM 来开发解决方案？ 

答：否，可以在本地开发并部署到云中！ 要在 NP VM 上部署，请确保按[证明文档](./field-programmable-gate-arrays-attestation.md)操作。 

问：在 NP VM 中对 FPGA 进行编程时，应使用从证明返回的哪个文件？

答：证明返回两个 xclbin，即 design.bit.xclbin 和 design.azure.xclbin  。 请使用 design.azure.xclbin。

问：在哪里可以获取所有 XRT/Platform 文件？

答：请访问 Xilinx 的 [Microsoft-Azure](https://www.xilinx.com/microsoft-azure.html) 网站获取所有文件。

问：我应使用哪个 XRT 版本？

答：xrt_202020.2.8.832 

问：目标部署平台是什么？

答：使用以下平台。
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

问：开发时应面向哪个平台？

答：xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

问：支持哪些 OS（操作系统）？ 

答：Xilinx 和 Microsoft 已验证 Ubuntu 18.04 LTS 和 CentOS 7.8。

 Xilinx 创建了以下市场映像用于简化这些 VM 的部署。 

[Xilinx Alveo U250 Deployment VM – Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Deployment VM – CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

问：我是否可以部署自己的 Ubuntu/CentOS VM，并安装 XRT/部署目标平台？ 

**答:** 是的。

问：如果我部署自己的 Ubuntu18.04 VM，那么需要哪些包并执行哪些步骤？

答：根据 [Xilinx XRT 文档](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)使用内核 4.1X
       
安装以下包。
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

问：在 Ubuntu 上重新启动 VM 后，我找不到我的 FPGA： 

答：请验证你的内核是否未升级 (uname -a)。 如果已升级，请降级到内核 4.1X。 

问：如果我部署自己的 CentOS7.8 VM，那么需要哪些包并执行哪些步骤？

答：使用内核版本：3.10.0-1160.15.2.el7.x86_64

 安装以下包。
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

问：在 CentOS 上运行 xbutil validate 时我收到此警告：“警告: 内核版本 3.10.0-1160.15.2.el7.x86_64 不受官方支持。 4.18.0-193 是最新的受支持版本。” 

答：可放心忽略此警告。 

问：本地 VM 与 NP VM 有哪些差异？

答： 
<br>
<b>- 关于 XOCL/XCLMGMT：</b>
<br>
在 Azure NP VM 上，仅存在使用 XOCL 驱动程序的角色终结点（设备 ID 5005）。

OnPrem FPGA 存在管理终结点（设备 ID 5004）和角色终结点（设备 ID 5005），它们分别使用 XCLMGMT 和 XOCL 驱动程序。

<br>
<b>- 关于 XRT：</b>
<br>
在 Azure NP VM 上，XDMA 2.1 平台仅支持 Host_Mem(SB) 和 DDR 数据保留功能。 
<br>
若要启用 Host_Mem(SB)（高达 1Gb RAM），请运行：sudo xbutil host_mem --enable --size 1g 

若要禁用 Host_Mem(SB)，请运行：sudo xbutil host_mem --disable 

问：是否可以运行 xbmgmt 命令？ 

答：否，Azure VM 不直接提供管理支持。 

 问：是否需要加载 PLP？ 

答：不需要，系统会自动为你加载 PLP，因此无需通过 xbmgmt 命令加载。 

 
问：Azure 是否支持不同的 PLP？ 

答：目前不支持。 我们仅支持部署平台包中提供的 PLP。 

问：如何查询 PLP 信息？ 

答：需要运行 xbutil 查询并查看下半部分。 



## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。