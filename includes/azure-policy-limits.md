---
title: include 文件
description: include 文件
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: e9ba8efefd2238b1aadc2fbeaf17a286e0714bcd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123484398"
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
| 计划定义 | 参数 | 300 |
| 策略或计划分配 | 排除项 (notScopes) | 400 |
| 策略规则 | 嵌套式条件语句 | 512 |
| 修正任务 | 资源 | 500 |
