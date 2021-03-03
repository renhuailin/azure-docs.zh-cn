---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 0f85f2770b98507618d5cbfdec1b55f397c7f514
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750360"
---
Azure 提供了提升磁盘存储 IOPS 和 MB/s 性能的功能，这称为 (VM) 和磁盘的虚拟机的突发。 你可以有效地利用 VM 和磁盘突发，在 Vm 和磁盘上实现更好的突发性能。

Azure Vm 和磁盘资源的突发会不相互依赖。 对于连接到突发功能的磁盘，无需使用突发功能的 VM 来突发。 同样，不需要将支持突发功能的磁盘连接到突发功能的 VM 以使 VM 突发。