---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992883"
---
- 增量快照当前无法在订阅之间转移。
- 当前，在任何给定时间，只能为某个特定系列的最多 5 个快照生成 SAS URI。
- 不能在磁盘的订阅之外为该磁盘创建增量快照。
- 每五分钟最多可为每个磁盘创建 7 个增量快照。
- 总共可以为单个磁盘创建 200 个增量快照。
- 如果父磁盘的大小调整超过 4 TB 限额，则无法获取分别在磁盘大小调整之前和之后拍摄的快照之间的差异。 例如，你在磁盘大小为 2 TB 时拍摄了一张增量快照 snapshot-a。 现在，你已将磁盘大小增加到 6 TB，然后拍摄了另一张增量快照 snapshot-b。 你无法获取 snapshot-a 与 snapshot-b 之间的差异。 需要再次下载调整大小后创建的 snapshot-b 的完整副本。 随后，可获取 snapshot-b 与其之后创建的快照之间的差异。 
