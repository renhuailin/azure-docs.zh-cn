---
title: 推荐的性能基准测试 - Azure NetApp 文件
description: 了解使用 Azure NetApp 文件的卷性能和指标的基准测试建议。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: e5d5104e0f2215e81b5539296ff18572375a8a28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362386"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Azure NetApp 文件的性能基准测试建议

本文提供了有关使用 Azure NetApp 文件的卷性能和指标的基准测试建议。

## <a name="overview"></a>概述

若要了解 Azure NetApp 文件卷的性能特征，可使用开源工具 [FIO](https://github.com/axboe/fio) 运行一系列基准，以模拟各种工作负载。 FIO 可安装在基于 Linux 和 Windows 的操作系统上。  它是一个出色的工具，用于获取卷的 IOPS 和吞吐量的快速快照。

Azure NetApp 文件不建议使用 `dd` 实用工具作为基准测试工具。 应使用实际的应用程序工作负载、工作负载模拟以及基准测试和分析工具（例如，Oracle 的 Oracle AWR，或 IBM 的等效 DB2 工具）来建立和分析最佳基础结构性能。 例如，FIO、vdbench 和 iometer 等工具可以有效地确定虚拟机的存储限制，并可以将测试参数与实际应用程序工作负载混合体进行匹配，以得出最有用的结果。 不过，始终最好在实际应用程序上进行测试。  

### <a name="vm-instance-sizing"></a>VM 实例大小

若要获得最佳结果，请确保使用大小适当的虚拟机 (VM) 实例来执行测试。 以下示例使用 Standard_D32s_v3 实例。 有关 VM 实例大小的详细信息，请参阅 [Azure 中 Windows 虚拟机的大小](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（针对基于 Windows 的 VM）和 [Azure 中 Linux 虚拟机的大小](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（针对基于 Linux 的 VM）。

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp 文件卷大小

确保针对预期的性能级别选择了正确的服务级别和卷配额大小。 有关详细信息，请参阅 [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)。

### <a name="virtual-network-vnet-recommendations"></a>虚拟网络 (VNet) 建议

应在与 Azure NetApp 文件相同的 VNet 中执行基准测试。 以下示例演示了该建议：

![VNet 建议](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO 的安装

对于 Linux 和 Windows，FIO 以二进制格式提供。 按照 [FIO](https://github.com/axboe/fio) 中“二进制包”部分的说明，针对所选择的平台进行安装。

## <a name="fio-examples-for-iops"></a>IOPS 的 FIO 示例 

本部分的 FIO 示例使用以下设置：
* VM 实例大小：D32s_v3
* 容量池服务级别和大小：高级/50 TiB
* 卷配额大小：48 TiB

以下示例显示了 FIO 随机读取和写入。

### <a name="fio-8k-block-size-100-random-reads"></a>FIO：8k 块大小 100% 随机读取

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>输出：显示 68k 读取 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO：8k 块大小 100% 随机写入

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>输出：显示 73k 写入 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>带宽的 FIO 示例

本部分中的示例显示了 FIO 顺序读取和写入。

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO：64k 块大小100% 顺序读取

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>输出：显示 11.8 Gbit/秒的吞吐量

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO：64k 块大小100% 顺序写入

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>输出：显示 12.2 Gbit/秒的吞吐量

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>卷指标

Azure NetApp 文件性能数据通过 Azure Monitor 计数器提供。 计数器可通过 Azure 门户和 REST API GET 请求获取。 

可查看历史数据以了解以下信息：
* 平均读取延迟 
* 平均写入延迟 
* 读取 IOPS（平均）
* 写入 IOPS（平均）
* 卷逻辑大小（平均）
* 卷快照大小（平均）

### <a name="using-azure-monitor"></a>使用 Azure Monitor 

可通过“指标”页在每个卷的基础上访问 Azure NetApp 文件计数器，如下所示：

![Azure Monitor 指标](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

也可通过转到“指标”页、筛选 NetApp 并指定相关的卷计数器，在 Azure Monitor 为 Azure NetApp 文件创建仪表板： 

![Azure Monitor 仪表板](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API 访问

可通过使用 REST API 调用来访问 Azure NetApp 文件计数器。 有关容量池和卷的计数器，请参阅 [Azure Monitor 支持的指标：Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes)。

以下示例显示用于查看逻辑卷大小的 GET URL：

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Linux 性能基准](performance-benchmarks-linux.md)
