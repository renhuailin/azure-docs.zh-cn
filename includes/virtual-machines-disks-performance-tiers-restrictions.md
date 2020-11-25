---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/20/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 241a2be834d7828cdb56f59313c0c1a9c478ff77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016503"
---
- 目前仅高级 SSD 支持此功能。
- 必须先对 VM 解除分配或从正在运行的 VM 中拆离磁盘，然后才能更改磁盘层级。
- P60、P70 和 P80 性能层只能由大于 4096 GiB 的磁盘使用。
- 磁盘的性能层只能每12小时降级一次。