---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/14/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ed6b2b47ead64eac664f789aa8eba7887e68e7
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228784"
---
Azure 文件共享的 SMB 多通道当前具有以下限制：
- 仅在使用 SMB 3.1.1 的 [Windows](../articles/storage/files/storage-how-to-use-files-windows.md) 和 [Linux](../articles/storage/files/storage-how-to-use-files-linux.md) 客户端上受支持
- 最大通道数为四，有关详细信息，请参阅[此处](../articles/storage/files/storage-troubleshooting-files-performance.md#cause-4-number-of-smb-channels-exceeds-four)。
- 不支持 SMB Direct。
- 不支持使用存储帐户的专用终结点。