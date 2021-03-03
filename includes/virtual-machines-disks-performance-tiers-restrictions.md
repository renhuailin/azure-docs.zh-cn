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
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750699"
---
- 目前仅高级 SSD 支持此功能。
- 必须先对 VM 解除分配或从正在运行的 VM 中拆离磁盘，然后才能更改磁盘层级。
- P60、P70 和 P80 性能层只能由大于 4,096 GiB 的磁盘使用。
- 磁盘的性能层每 12 小时只能降级一次。

## <a name="change-performance-tier-without-downtime-preview"></a>更改性能层，无需停机 (预览) 

通常，必须释放 VM 或分离磁盘，才能更改性能层。 但如果启用此预览功能，则无需释放 VM 或分离磁盘来更改层。 你可以在 [此处](https://aka.ms/liveperftiersignup)注册预览。

预览具有以下限制：
- 仅在 EastUS2EUAP 区域中可用。
- 当前不适用于共享磁盘
- 必须通过 API 使用 Azure 资源管理器模板 `2020-12-01` 来更改性能层，而不会造成停机。