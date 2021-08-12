---
title: 包含文件
description: include 文件
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: 4792245dff3784d1aa72120c6be1412bf6659c35
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113027425"
---
Azure Policy 的每个对象类型都有一个最大计数。 对于定义，“范围”条目是指[管理组](../articles/governance/management-groups/overview.md)或订阅。
对于分配和例外情况，“范围”条目是指[管理组](../articles/governance/management-groups/overview.md)、订阅、资源组或单个资源。

| 其中 | 对象 | 最大计数 |
|---|---|---|
| 范围 | 策略定义 | 500 |
| 范围 | 计划定义 | 200 |
| 租户 | 计划定义 | 2,500 |
| 范围 | 策略或计划分配 | 200 |
| 范围 | 豁免 | 1000 |
| 策略定义 | 参数 | 20 |
| 计划定义 | 策略 | 1000 |
| 计划定义 | 参数 | 250 |
| 策略或计划分配 | 排除项 (notScopes) | 400 |
| 策略规则 | 嵌套式条件语句 | 512 |
| 修正任务 | 资源 | 500 |
