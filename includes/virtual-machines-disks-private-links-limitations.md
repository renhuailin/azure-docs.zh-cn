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
ms.openlocfilehash: 28717deadd8d842a68ab6ae6b52093f0117ad2c7
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630017"
---
- 只有一个虚拟网络可以链接到磁盘访问对象。
- 虚拟网络必须与磁盘访问对象处于同一订阅中才能链接它们。
- 使用同一磁盘访问对象最多可以同时导入或导出 10 个磁盘或快照。
- 无法请求手动批准将虚拟网络链接到磁盘访问对象。
- 增量快照与磁盘访问对象关联时，将无法导出。

