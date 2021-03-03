---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734010"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 指标警报（经典） |每个订阅 100 个活动警报规则。 | 致电支持人员 |
| 指标警报 |Azure 公用云、Azure 中国世纪互联云和 Azure 政府云中的每个订阅 5,000 个活动警报规则。 如果达到此限制，请查看你是否可使用[同类型多资源警报](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。<br/>每条警报规则 5,000 个指标时序。 | 致电支持人员。 |
| 活动日志警报 | 每个订阅 100 个活动预警规则（无法增加数量）。 | 与默认值相同 |
| 日志警报 | 每个订阅 512 个活动警报规则。 每项资源 200 个活动警报规则。 | 致电支持人员 |
| 警报规则和操作规则说明长度| 日志搜索警报 4096 个字符<br/>所有其他警报 2048 个字符 | 与默认值相同 |