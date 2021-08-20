---
ms.service: hpc-cache
ms.topic: include
ms.date: 06/17/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 37f2895ed476cc0dbe0bdb3def034a17448d8dca
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587206"
---
| 使用模型 | 缓存模式 | 后端验证 | 最大写回延迟 |
|--|--|--|--|
| 读取操作频繁，写入操作罕见 <!--read_heavy_infreq-->| 读取 | 从不 | 无 |
| 超过 15% 的写入操作 <!--write_workload_15-->| 读取/写入 | 8 小时 | 1 小时 |
| 客户端绕过缓存 <!--write_around-->| 读取 | 30 秒 | 无 |
| 超过 15% 的写入操作，频繁的后端检查（30 秒） <!--write_workload_check_30-->| 读取/写入 | 30 秒 | 1 小时 |
| 超过 15% 的写入操作，频繁的后端检查（60 秒） <!--write_workload_check_60-->| 读取/写入 | 60 秒 | 1 小时 |
| 超过 15% 的写入操作，频繁写回 <!--write_workload_cloudws-->| 读取/写入 | 30 秒 | 30 秒 |
| 读取操作频繁，每 3 小时检查一次备份服务器 <!--read_heavy_check_180-->| 读取 | 3 小时 | 无 |
