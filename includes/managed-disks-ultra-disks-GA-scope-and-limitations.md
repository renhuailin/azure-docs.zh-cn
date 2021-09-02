---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d0bed89f56d41c6613080e84d743d245243bb507
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322011"
---
目前，超级磁盘有其他限制，如下所示：

对于超级磁盘，目前唯一可用的基础结构冗余选项是可用性区域。 使用任何其他冗余选项的 VM 无法附加超级磁盘。

下表概述了可使用超级磁盘的区域以及相应的可用性选项：

> [!NOTE]
> 如果以下列表中的某个区域没有支持超级磁盘的可用性区域，则该区域中的 VM 必须在没有任何基础结构冗余选项的情况下部署，才能附加超级磁盘。

|区域  |冗余选项  |
|---------|---------|
|巴西南部     |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|印度中部     |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|东亚     |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|德国中西部     |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|韩国中部     |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|美国中北部    |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|美国中南部    |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|US Gov 亚利桑那州     |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|US Gov 弗吉尼亚州     |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|US Gov 德克萨斯州     |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|美国西部     |仅限单个 VM（不支持可用性集和虚拟机规模集）        |
|澳大利亚中部    |仅限单个 VM（不支持可用性集和虚拟机规模集）|
|澳大利亚东部     |三个可用性区域         |
|东南亚    |三个可用性区域        |
|加拿大中部     |三个可用性区域          |
|Central US     |三个可用性区域          |
|美国东部     |三个可用性区域          |
|美国东部 2     |三个可用性区域         |
|法国中部    |两个可用性区域        |
|日本东部    |三个可用性区域        |
|北欧    |三个可用性区域        |
|英国南部    |三个可用性区域        |
|西欧    | 三个可用性区域|
|美国西部 2    |三个可用性区域|

- 仅在以下 VM 系列上受支持：
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/m-series.md)
    - [Mv2](../articles/virtual-machines/mv2-series.md)
    - [HBv2](../articles/virtual-machines/hbv2-series.md)
    - [HB](../articles/virtual-machines/hb-series.md)
    - [HC](../articles/virtual-machines/hc-series.md)
    - [NDv2](../articles/virtual-machines/ndv2-series.md)
    - [ND](../articles/virtual-machines/nd-series.md)
    - [NC_T4_v3](../articles/virtual-machines/nct4-v3-series.md)
    - [NCv2](../articles/virtual-machines/ncv2-series.md)
    - [NCv3](../articles/virtual-machines/ncv3-series.md)
    - [NVv3](../articles/virtual-machines/nvv3-series.md)
    - [NVv4](../articles/virtual-machines/nvv4-series.md)
    
- 在每个具有超级磁盘的受支持区域中，并非每个 VM 大小都可用。
- 仅作为数据磁盘提供。 
- 默认情况下支持 4k 物理扇区大小。 512E 扇区大小作为一种正式发布的产品/服务提供（无需注册）。 大多数应用程序都与 4k 扇区大小兼容，但某些应用程序需要 512 字节扇区大小。 另一个示例为 Oracle Database，它需要 12.2 版或更高版本才能支持 4k 本机磁盘。 对于较旧版本的 Oracle DB，需要 512 字节扇区大小。
- 只能以空磁盘的形式创建。
- 当前不支持磁盘快照、磁盘导出、更改磁盘类型、VM 映像、可用性集、Azure 专用主机或 Azure 磁盘加密。
- 当前不支持与 Azure 备份或 Azure Site Recovery 的集成。
- 仅支持未缓存的读取和未缓存的写入。
- 采用正式发布大小的单个 VM 的 IOPS 当前最大限制为 80,000。 具有更高 IOPS 的超级磁盘可以用作共享磁盘，以支持多个 VM。

默认情况下，Azure 超级磁盘在每个区域的每个订阅中提供高达 32 TiB 的容量，但超级磁盘支持更高的容量请求。 若要请求增加容量，可请求配额增加或联系 Azure 支持。
