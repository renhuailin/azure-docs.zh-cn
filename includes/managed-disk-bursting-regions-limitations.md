---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102178306"
---
- 低于 512 GiB 的高级 SSD 上无法启用按需突发。 低于 512 GiB 的高级 SSD 将始终使用基于额度的突发。
- 仅高级 SSD 支持按需突发。 如果启用了按需突发的高级 SSD 切换到另一种磁盘类型，则将禁用磁盘突发。
- 更改性能层时，按需突发不会自动禁用其自身。 如果要更改性能层，但不希望保留磁盘突发，则必须将磁盘突发禁用。
- 仅当从 VM 分离磁盘或停止 VM 时，才能启用按需突发。 启用按需突发后，可以在 12 小时后禁用它。