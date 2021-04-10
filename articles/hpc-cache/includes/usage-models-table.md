---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563227"
---
| 使用模型 | 缓存模式 | 后端验证 | 最大写回延迟 |
|--|--|--|--|
| 读取操作频繁，写入操作罕见 | 读取 | 从不 | 无 |
| 超过 15% 的写入操作 | 读取/写入 | 8 小时 | 20 分钟 |
| 客户端绕过缓存 | 读取 | 30 秒 | 无 |
| 超过 15% 的写入操作，频繁的后端检查（30 秒） | 读取/写入 | 30 秒 | 20 分钟 |
| 超过 15% 的写入操作，频繁的后端检查（60 秒） | 读取/写入 | 60 秒 | 20 分钟 |
| 超过 15% 的写入操作，频繁写回 | 读取/写入 | 30 秒 | 30 秒 |
| 读取操作频繁，每 3 小时检查一次备份服务器 | 读取 | 3 小时 | 无 |
