---
author: tfitzmac
ms.service: azure-app-configuration
ms.topic: include
ms.date: 08/19/2021
ms.author: tomfitz
ms.openlocfilehash: 3b04007ef7329231da7de31ec465de2c1585c7e4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122752745"
---
| 资源 | 限制 | 注释 |
| --- | --- | ---|
| 免费层的配置存储 | 每个订阅 1 个存储空间 |
| 标准层的配置存储 | 每个订阅无限制的存储空间 | 
| 免费层的配置存储请求 | 每日 1,000 个请求  | 配额用尽后，将对所有请求返回 HTTP 状态代码 429，直到当天结束为止 |
| 标准层的配置存储请求 | 每小时 30,000 个  |一旦配额用尽，请求可能返回 HTTP 状态代码 429，指示请求过多，直到该小时结束|  
| 免费层的存储 | 10 MB |
| 标准层的存储 | 1GB |
| 键和值 | 10 KB  | 对于单个键值项，包括所有元数据
