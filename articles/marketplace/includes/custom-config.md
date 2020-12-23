---
title: include 文件
description: 文件
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283689"
---
如果需要其他配置，请使用在启动时运行的计划任务，以便在部署 VM 后对其进行最终更改。 也请考虑以下要求：

- 对于一次性运行的任务，在成功完成该任务后，它应自行删除。
- 配置不应依赖于 C 或 D 以外的驱动器，因为只有这两个驱动器始终存在（驱动器 C 是操作系统磁盘，驱动器 D 是临时本地磁盘）。

有关 Linux 自定义项的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](../../virtual-machines/extensions/features-linux.md)。