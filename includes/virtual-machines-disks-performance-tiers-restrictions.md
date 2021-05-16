---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750699"
---
- 目前仅高级 SSD 支持此功能。
- 必须先对 VM 解除分配或从正在运行的 VM 中拆离磁盘，然后才能更改磁盘层级。
- P60、P70 和 P80 性能层只能由大于 4,096 GiB 的磁盘使用。
- 磁盘的性能层每 12 小时只能降级一次。

## <a name="change-performance-tier-without-downtime-preview"></a>在无故障时间的情况下更改性能层（预览版）

通常，必须解除分配 VM 或拆离磁盘，才能更改性能层。 但是，如果启用此预览功能，则无需解除分配 VM 或拆离磁盘即可更改层。 可以在[此处](https://aka.ms/liveperftiersignup)注册预览版。

预览版设有以下限制：
- 仅在 EastUS2EUAP 区域中可用。
- 当前不适用于共享磁盘
- 必须使用 Azure 资源管理器模板和 `2020-12-01` API 来更改性能层，而没有故障时间。