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
ms.openlocfilehash: 963e49ea0e5536be0fca6d565b439aef4a08793d
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605237"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| Azure 应用推送 | 每个操作组 10 个 Azure 应用操作。 | 与默认值相同 |
| 电子邮件 | 一个操作组中有 1,000 个电子邮件操作。<br>一小时内最多 100 个电子邮件。<br>另请参阅[速率限制信息](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 与默认值相同 |
| ITSM | 一个操作组中有 10 个 ITSM 操作。 | 与默认值相同 | 
| 逻辑应用 | 一个操作组中有 10 个逻辑应用操作。 | 与默认值相同 |
| Runbook | 一个操作组中有 10 个 runbook 操作。 | 与默认值相同 |
| SMS | 一个操作组中有 10 个短信操作。<br>每 5 分钟最多 1 条短信。<br>另请参阅[速率限制信息](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 与默认值相同 |
| 语音 | 一个操作组中有 10 个语音操作。<br>每 5 分钟最多 1 条语音呼叫。<br>另请参阅[速率限制信息](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 与默认值相同 |
| Webhook | 一个操作组中有 10 个 Webhook 操作。  每个订阅最大 Webhook 调用次数为每分钟 1500 次。 其他限制可在[特定于操作的信息](../articles/azure-monitor/platform/action-groups.md#action-specific-information)中找到。  | 与默认值相同 |
