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
ms.openlocfilehash: 32fc672bf41c35881baf082b5694d1800084889d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745967"
---
Azure 指标顾问是一种用于时序异常检测、诊断和分析的服务。 作为由 AI 提供支持的服务，它使用你的数据来训练所使用的模型。 服务接受具有以下各列的聚合数据表：

* 度量值（必需）：度量值是一个基本术语或单位特定的术语，是指标的可量化值。 它指一个或多个包含数值的列。
* **时间戳**（可选）：零个或一个类型为 `DateTime` 或 `String` 的列。 如果未设置此列，则时间戳将设置为每个引入周期的开始时间。 设置时间戳的格式，如下所示：`yyyy-MM-ddTHH:mm:ssZ`。 
* 维度（可选）：维度是一个或多个分类值。 这些值的组合标识特定的单变量时序（例如国家/地区、语言、租户等）。 维度列可以是任意数据类型。 处理大量的列和值时要格外小心，应避免处理过多的维度。

如果使用数据源（如 Azure Data Lake Storage 或 Azure Blob 存储），则可将数据聚合在一起，使其与预期的指标架构一致。 这是因为这些数据源使用文件作为指标输入。

如果使用的是 Azure SQL 或 Azure 数据资源管理器等数据源，则可使用聚合函数将数据聚合到预期的架构中。 这是因为这些数据源支持通过运行查询来从源获取指标数据。

