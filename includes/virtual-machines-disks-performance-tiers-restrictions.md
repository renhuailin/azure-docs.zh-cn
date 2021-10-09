---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 54d6078476d6ebfc7be78f00d61de527dcbcec89
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909355"
---
- 目前仅高级 SSD 支持此功能。
- P60、P70 和 P80 性能层只能由大于 4,096 GiB 的磁盘使用。
- 磁盘的性能层每 12 小时只能降级一次。
- 系统不会返回在 2020 年 6 月之前创建的磁盘的性能层。 你可以通过使用基线层更新旧磁盘来利用旧磁盘的性能层。
