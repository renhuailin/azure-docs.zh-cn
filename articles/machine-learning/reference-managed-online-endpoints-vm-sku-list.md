---
title: 托管联机终结点 VM SKU 列表（预览版）
titleSuffix: Azure Machine Learning
description: 列出了可用于 Azure 机器学习中托管联机终结点（预览版）的 VM SKU。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: laobri
ms.author: seramasu
author: rsethur
ms.custom: devplatv2
ms.date: 05/10/2021
ms.openlocfilehash: 79711c803decdb2397701de7fd064639799e11aa
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445375"
---
# <a name="managed-online-endpoints-sku-list-preview"></a>托管联机终结点 SKU 列表（预览版）

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

下表显示 Azure 机器学习托管联机终结点（预览版）支持的 VM SKU。

* 用于部署的 `instance_type` 特性必须以“Standard_F4s_v2”格式指定。 下表列出了实例名称，例如 F2s v2。 应以指定的格式 (`Standard_{name}`) 输入这些名称，使 Azure CLI 或 Azure 资源管理器模板（ARM 模板）请求能够创建和更新部署。 

* 有关 CPU 和 RAM 等配置详细信息，请参阅 [Azure 机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning/)。

| 大小 | 常规用途 | 计算优化 |  GPU |
| --- | --- | --- | --- | --- | 
| V.Small | DS2 v2 | F2s v2 | NC4as_T4_v3 |
| 小型 | DS3 v2 | F4s v2 | NC6s v2 <br/> NC6s v3 <br/> NC8as_T4_v3 |
| 中型 | DS4 v2 | F8s v2 | NC12s v2 <br/> NC12s v3 <br/> NC16as_T4_v3 |
| 大型 | DS5 v2 | F16s v2 | NC24s v2 <br/> NC24s v3 <br/> NC64as_T4_v3 |
| X-大| - | F32s v2 <br/> F48s v2 <br/> F64s v2 <br/> F72s v2 | - |


