---
title: 包含文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d5915d8628254f24343571c1adc254c548558415
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077692"
---
- 目前仅高级 SSD 支持此功能。
- 必须先对 VM 解除分配或从正在运行的 VM 中拆离磁盘，然后才能更改磁盘层级。
- P60、P70 和 P80 性能层只能由大于 4,096 GiB 的磁盘使用。
- 磁盘的性能层每 12 小时只能降级一次。

## <a name="change-performance-tier-without-downtime-preview"></a>在无故障时间的情况下更改性能层（预览版）

通常，必须解除分配 VM 或拆离磁盘，才能更改性能层。 但是，如果启用此预览功能，则无需解除分配 VM 或拆离磁盘即可更改层。

预览版设有以下限制：
- 仅在美国西部地区提供。
- 当前不适用于共享磁盘。
- 必须使用以下方法之一来更改层级，以避免故障时间：
    - 结合使用 Azure 资源管理器模板和 `2020-12-01` API，无需停机即可更改性能层。
    - 通过以下链接访问 Azure 门户：[https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview)。
    - 最新 Azure CLI。
- 目前不适用于 Azure PowerShell 模块。