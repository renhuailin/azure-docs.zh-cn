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
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "101750360"
---
Azure 提供了提升磁盘存储 IOPS 和 MB/s 性能的功能，这称为虚拟机 (VM) 和磁盘突发。 可以有效地利用 VM 和磁盘突发，以在 VM 和磁盘上实现更好的突发性能。

Azure Vm 和磁盘资源的突发不会相互依赖。 你不需要使用具有突发功能的 VM 来为与该 VM 相连接且同样具有突发功能的磁盘进行突发。 同样，不需要使用与具有突发功能的 VM 相连接且同样具有突发功能的磁盘为该 VM 进行突发。