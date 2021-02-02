---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/09/2020
ms.author: alkohli
ms.openlocfilehash: ae7cb05aeda296ffb3aa9d7f6e2c88fe59364975
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475028"
---
VM 大小决定了可供 VM 使用的计算资源量 (如 CPU、GPU 和内存) 。 应使用适合工作负荷的 VM 大小创建虚拟机。 即使所有计算机都将在同一硬件上运行，计算机大小也对磁盘访问具有不同的限制。 这可以帮助你管理跨 Vm 的总体磁盘访问。 如果工作负荷增加，还可以调整现有虚拟机的大小。

支持在 Azure Stack Edge 设备上创建以下 Vm。

### <a name="dv2-series"></a>Dv2 系列
|大小     |vCPU     |内存 (GiB) | 资源磁盘大小 (GiB)   | OS 磁盘大小 (GiB)  | 最大数据磁盘数 | 最大 NIC 数 |
|-------------------|----|----|-----|----|------|------------|
|**Standard_D1_v2** |1   |3.5 |50   |1000| 4    |2 |
|**Standard_D2_v2** |2   |7   |100  |1000| 8    |4 |
|**Standard_D3_v2** |4   |14  |200  |1000| 16  |4 |
|**Standard_D4_v2** |8   |28  |400  |1000| 32  |8 |
|**Standard_D5_v2** |16  |56  |800  |1000| 64  |8 |
|**Standard_D11_v2** |2   |14  |100  |1000| 8     |2 |
|**Standard_D12_v2** |4   |28  |200  |1000| 16   |4 |
|**Standard_D13_v2** |8   |56  |400  |1000| 32  |8 |

### <a name="dsv2-series"></a>DSv2 系列
|大小     |vCPU     |内存 (GiB) |  资源磁盘大小 (GiB)   | OS 磁盘大小 (GiB)  | 最大数据磁盘数| 最大 NIC 数 |
|--------------------|----|----|----|-----|------|-------------|
|**Standard_DS1_v2** |1   |3.5 |7  |4000  |1000 |4  |2 |
|**Standard_DS2_v2** |2   |7   |14 |8000  |1000 |8  |4 |
|**Standard_DS3_v2** |4   |14  |28 |16000 |1000 |16 |4 |
|**Standard_DS4_v2** |8   |28  |56 |32000 |1000 |32 |8 |
|**Standard_DS5_v2** |16  |56  |112|64000 |1000 |64 |8 |
|**Standard_DS11_v2**|2   |14  |28 |8000  |1000 |4  |2 |
|**Standard_DS12_v2**|4   |28  |56 |16000 |1000 |8  |4 |
|**Standard_DS13_v2**|8   |56  |112|32000 |1000 |16 |8 |


有关详细信息，请参阅 [Dv2 和 DSv2 系列](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series)。

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3 系列 (预览版) 

设备上的 GPU Vm 支持这些大小，并已针对计算密集型 GPU 加速应用程序进行了优化。 此系列重点介绍了具有 Nvidia Tesla T4 GPU 的推理工作负荷。 

|大小     |vCPU     |内存 (GiB) | 资源磁盘大小 (GiB)   |OS 磁盘大小 (GiB) | GPU | GPU memory (GiB)  | 最大 NIC 数 |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1000|1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1000|1 |16  |8 |

有关详细信息，请参阅 [NCasT4_v3 系列](../articles/virtual-machines/nct4-v3-series.md)。

### <a name="f-series"></a>F 系列

这些系列针对计算工作负荷进行了优化，并在 Intel 强处理器上运行。 

| 大小 | vCPU | 内存:GiB |资源磁盘大小 (GiB)  |OS 磁盘大小 (GiB) |  最大数据磁盘数 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      |1000| 4  |  2 |
| Standard_F2 | 2  | 4 |32      |1000| 8  |  4 |
| Standard_F4  | 4  | 8 |64   |1000| 16 |  4 |
| Standard_F8 | 8 | 16  |132    |1000| 32 |  8 |
| Standard_F16 | 16 | 32  |262   |1000| 64 |  8 |
| Standard_F1s | 1 | 2  | 4  |1000| 4 | 1 |
| Standard_F2s | 2 | 4 |8   |1000| 8 | 4 |
| Standard_F4s | 4 | 8 |16 |1000| 16 |  4 |
| Standard_F8s | 8 | 16 |32 |1000| 32 |  8 |
| Standard_F16s | 16 | 32 |64 |1000| 64 |  8 |

有关详细信息，请参阅 [Fsv2 系列](../articles/virtual-machines/fsv2-series.md)。

