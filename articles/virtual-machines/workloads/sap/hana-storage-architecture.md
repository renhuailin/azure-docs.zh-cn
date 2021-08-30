---
title: Azure SAP HANA（大型实例）的存储体系结构 | Microsoft Docs
description: 了解 Azure SAP HANA（大型实例）的存储体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f53eb460d6b131dce607c0dd99f97babd758de35
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603405"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA（大型实例）存储体系结构

本文将介绍用于部署 Azure SAP HANA 大型实例的存储体系结构（也称为 BareMetal 基础结构）。 

SAP HANA 根据 SAP 建议的指导原则在经典部署模型中配置 Azure 上的 SAP HANA（大型实例）的存储布局。 有关准则的详细信息，请参阅 [SAP HANA 存储要求](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)。

I 类 HANA 大型实例附带有四倍内存卷作为存储卷， 而 II 类 HANA 大型实例却附带用于存储 HANA 事务日志备份的卷。 有关详细信息，请参阅[安装和配置 Azure 上的 SAP HANA（大型实例）](hana-installation.md)。

有关存储分配，请参阅下表。 此表大致列出了各卷的容量，这些卷是随不同的 HANA 大型实例单元一起提供的。

| HANA 大型实例 SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1,024 GB | 1,536 GB | 1,024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2,048 GB | 4,096 GB |
| S768m | 28,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4,096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |
| S896m | 33,792 GB | 512 GB | 1,024 GB | 512 GB |

HANA 大型实例的最新 SKU 随以下存储配置一起提供。

| HANA 大型实例 SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4,224 GB | 512 GB | 1,024 GB | 512 GB |
| S224oo | 6,336 GB | 512 GB | 1,024 GB | 512 GB |
| S224m | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S224om | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S224ooo | 10,560 GB | 512 GB | 1,024 GB | 512 GB |
| S224oom | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S448 | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S448oo | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S448m | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S448om | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S448ooo | 21,120 GB | 512 GB | 1,024 GB | 512 GB |
| S448oom | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672 | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S672oo | 19,008 GB | 512 GB | 1,024 GB | 512 GB |
| S672m | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672om | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672ooo | 31,680 GB | 512 GB | 1,024 GB | 512 GB |
| S672oom | 38,016 GB | 512 GB | 1,024 GB | 512 GB |
| S896 | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S896oo | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S896om | 33,792 GB | 512 GB | 1,024 GB | 512 GB |
| S896ooo | 42,240 GB | 512 GB | 1,024 GB | 512 GB |
| S896oom | 50,688 GB | 512 GB | 1,024 GB | 512 GB |


实际部署的卷可能会稍有不同，具体取决于部署以及用于显示卷大小的工具。

如果对 HANA 大型实例 SKU 进行细分，则请参阅以下示例，了解可能的分区块：

| 内存分区 (GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


这些大小为大致的卷数字，可能会因部署和用来查看卷的工具而稍有不同。 还可以考虑其他分区大小，例如 2.5 TB。 这些存储大小在计算时使用的公式类似于那些用于上述分区的公式。 “分区”一词并不表示操作系统、内存或 CPU 资源也可以分区。 它只是表示适用于不同 HANA 实例的存储分区，这些实例可能需要部署到单个 HANA 大型实例单位。 

如果需要更多存储，可以 1 TB 为单位购买更多。 额外存储可作为更多卷添加或用于扩展一个或多个现有卷。 不能减小最初部署的卷的大小，也不能减小前面表记录的卷的大小。 此外，无法更改卷名或装载名称。 如上所述的存储卷是作为 NFS4 卷附加到 HANA 大型实例单位的。

可以使用存储快照来实现备份、还原和灾难恢复目的。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)

有关你的方案的存储布局详细信息，请参阅 [HLI 支持的方案](hana-supported-scenario.md)。

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>在一个 HANA 大型实例单位上运行多个 SAP HANA 实例

可以在 HANA 大型实例单位上托管多个活动的 SAP HANA 实例。 此类配置需要按实例进行卷设置，这样就仍然能够提供存储快照和灾难恢复的功能。 目前，HANA 大型实例单位可以细分如下：

- **S72、S72m、S96、S144、S192**：以 256 GB 为增量，且以 256 GB 为最小起始单位。 可以组合使用不同的增量（例如 256 GB、512 GB 等），但不得超出该单位的最大内存。
- **S144m 和 S192m**：以 256 GB 为增量，且以 512 GB 为最小单位。 可以组合使用不同的增量（例如 512 GB、768 GB 等），但不得超出该单位的最大内存。
- **II 类**：以 512 GB 为增量，最小起始单位为 2 TB。 可以组合使用不同的增量（例如 512 GB、1 TB 和 1.5 TB 等），但不得超出该单位的最大内存。

以下示例显示了运行多个 SAP HANA 实例的情况。

| SKU | 内存大小 | 存储大小 | 使用多个数据库时的大小 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA 实例<br /> 或 1 x 512 GB 实例 + 1 x 256 GB 实例<br /> 或 3 x 256 GB 实例 | 
| S72m | 1.5 TB | 6 TB | 3x512GB HANA 实例<br />或 1 x 512 GB 实例 + 1 x 1 TB 实例<br />或 6 x 256 GB 实例<br />或 1 x 1.5 TB 实例 | 
| S192m | 4 TB | 16 TB | 8 x 512 GB 实例<br />或 4 x 1 TB 实例<br />或 4 x 512 GB 实例 + 2 x 1 TB 实例<br />或 4 x 768 GB 实例 + 2 x 512 GB 实例<br />或 1 x 4 TB 实例 |
| S384xm | 8 TB | 22 TB | 4 x 2 TB 实例<br />或 2 x 4 TB 实例<br />或 2 x 3 TB 实例 + 1 x 2 TB 实例<br />或 2 x 2.5 TB 实例 + 1 x 3 TB 实例<br />或 1 x 8 TB 实例 |


还有其他组合变化。 

## <a name="encryption-of-data-at-rest"></a>静态数据加密
用于 HANA 大型实例的存储使用透明数据加密，因为数据存储在磁盘上。 在 2018 年底之前的部署中，可以对卷进行加密。 如果决定不使用该选项，可以联机对卷进行加密。 将从非加密移动到加密卷是透明的并且不需要停机时间。 

使用 I 类 HANA 大型实例的 SKU 时，会加密存储启动 LUN 的卷。 在使用 II 类 SKU 的修订版 3 HANA 大型实例缩放单元中，需要使用 OS 方法对启动 LUN 进行加密。 在使用 II 类 SKU 的修订版 4 HANA 大型实例缩放单元中，默认情况下会对存储启动 LUN 的卷进行静态加密。 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>HANA 大型实例上较大的 HANA 实例的必需设置

HANA 大型实例中使用的存储有文件大小限制。 每个文件的[大小限制为 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)。 与 EXT3 文件系统中文件大小限制不同的是，HANA 不会隐式识别 HANA 大型实例存储强制执行的存储限制。 因此，在达到 16 TB 的文件大小限制时，HANA 不会自动创建新数据文件。 当 HANA 尝试将文件增长到 16 TB 以上时，HANA 会报告错误，并且索引服务器最终会发生故障。

> [!IMPORTANT]
> 为了防止 HANA 尝试将数据文件增大到超过 HANA 大型实例存储的 16 TB 文件大小限制，需要在 HANA 的 global.ini 配置文件中设置以下参数：
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - 另请参阅 SAP 说明 [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - 请注意 SAP 说明 [#2631285](https://launchpad.support.sap.com/#/notes/2631285)

## <a name="next-steps"></a>后续步骤

了解如何部署 SAP HANA（大型实例）。

> [!div class="nextstepaction"]
> [SAP HANA（大型实例）部署](hana-overview-infrastructure-connectivity.md)
