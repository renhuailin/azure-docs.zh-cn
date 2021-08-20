---
title: 数据架构要求
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: f00c25396405678312e4c18a345840d628c15848
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114340809"
---
指标顾问是一种用于时序异常检测、诊断和分析的服务。 作为由 AI 提供支持的服务，它使用你的数据来训练所使用的模型。 服务接受具有以下各列的聚合数据表：

* 度量值（必需）：度量值是一个基本术语或单位特定的术语，是指标的可量化值。 它指一个或多个包含数值的列。
* **时间戳**（可选）：零个或一个类型为 `DateTime` 或 `String` 的列。 如果未设置此列，则时间戳将设置为每个引入周期的开始时间。 将时间戳格式设置为：`yyyy-MM-ddTHH:mm:ssZ`。 
* 维度（可选）：维度是一个或多个分类值。 这些值的组合标识特定的单变量时序，例如国家/地区、语言、租户等等。 维度列可以是任意数据类型。 处理大量的列和值时要格外小心，应避免处理过多的维度。

下面是预期指标架构的示例： 

<!-- ![Screenshot of metrics schema example](../media/tutorial/metric-schema.png) -->

如果你的数据源（例如 ADLS、Azure Blob 等）使用文件作为指标输入，请提前聚合数据，以便与预期的指标架构保持一致。 但是，如果使用的数据源（例如 Azure SQL 服务器、Azure 数据资源管理器或其他源）支持运行查询来从源获取指标数据，则你可以使用聚合函数将数据聚合到预期的架构中。
