---
title: Azure 计算单位概述
description: Azure 计算单位的概念概述。 ACU 提供了一种在 Azure SKU 中比较 CPU 性能的方法。
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: cfcad9ed109e6f12ca5e15b75e41b842c330dc8a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216438"
---
# <a name="azure-compute-unit-acu"></a>Azure 计算单元 (ACU)

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Azure 计算单位 (ACU) 这一概念提供一种比较 Azure SKU 的计算 (CPU) 性能的方法。 这有助于轻松确定最有可能满足性能需求的 SKU。 ACU 目前在数量为 100 的小型 (Standard_A1) VM 上标准化，而所有其他 SKU 则表示该 SKU 在运行标准基准测试时大约会快多少

*ACU 使用 Intel® Turbo 技术来增加 CPU 频率和提升性能。  性能提升程度可能因 VM 大小、工作负荷和同一主机上运行的其他工作负荷而有所不同。

**ACU 使用 AMD® Boost 技术来增加 CPU 频率和提升性能。  性能提升程度可能因 VM 大小、工作负荷和同一主机上运行的其他工作负荷而有所不同。

***超线程，能够运行嵌套虚拟化

****AMD 多线程并行处理技术

> [!IMPORTANT]
> ACU 只是一种规则。 工作负荷的结果可能会有所不同。
<br>

| SKU 系列 | ACU\vCPU | vCPU：核心 |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [B](sizes-b-series-burstable.md) |多种多样 | 1:1 |
| [D1 - D14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230 - 260** | 2:1\*\*\*\* |
| [Dasv4](dav4-dasv4-series.md) |230 - 260** | 2:1\*\*\*\* |
| [Dv4](dv4-dsv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195 -210* | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195 - 210* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230 - 260** | 2:1\*\*\*\* |
| [Easv4](eav4-easv4-series.md) | 230 - 260** | 2:1\*\*\*\* |
| [Ev4](ev4-esv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195 - 210* | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195 - 210* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195 - 210* | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [FX4 - FX48](fx-series.md) | 310 - 340* | 2:1\*\*\* | 
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 - 216** | 1:1 |
| [HC](hc-series.md) |297 - 315* | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1\*\*\*\* |
| [M](m-series.md) | 160 - 180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230 - 260** | 2:1\*\*\*\* |

有关各种大小的详细信息，请访问以下链接：

- [通用](sizes-general.md)
- [内存优化](sizes-memory.md)
- [计算优化](sizes-compute.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [存储优化](sizes-storage.md)
