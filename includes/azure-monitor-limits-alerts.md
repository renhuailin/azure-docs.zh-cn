---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 05/03/2021
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 1d9ec2d05374412c17493b47f3bb71524f8cf93f
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110630559"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 指标警报（经典） |每个订阅 100 个活动警报规则。 | 致电支持人员 |
| 指标警报 |Azure 公用云、Azure 中国世纪互联云和 Azure 政府云中的每个订阅 5,000 个活动警报规则。 如果达到此限制，请查看你是否可使用[同类型多资源警报](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。<br/>每条警报规则 5,000 个指标时序。 | 致电支持人员。 |
| 活动日志警报 | 每个订阅 100 个活动预警规则（无法增加数量）。 | 与默认值相同 |
| 日志警报 | 每个订阅 1000 个活动警报规则。 每项资源 1000 个活动警报规则。 | 致电支持人员 |
| 警报规则和操作规则说明长度| 日志搜索警报 4096 个字符<br/>所有其他警报 2048 个字符 | 与默认值相同 |

### <a name="alerts-api"></a>警报 API
Azure Monitor 警报有几个限制，可防止用户进行过多的调用。 这种行为可能会重载系统后端资源，并危害服务响应能力。 以下限制旨在防止客户中断，并确保服务级别一致。 用户限制的设计决定其仅影响极端使用方案，不应与典型使用情况相关。

| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| GET alertsSummary | 每个订阅每分钟 50 次调用 | 与默认值相同 | 
|   GET alerts（不指定警报 ID） | 每个订阅每分钟 100 次调用 | 与默认值相同 | 
|   所有其他调用 | 每个订阅每分钟 1000 次调用 | 与默认值相同 | 

