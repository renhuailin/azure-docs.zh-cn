---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "67172759"
---
### <a name="cacheskuname"></a>cacheSKUName

新 Azure Redis 缓存的定价层。

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

模板定义此参数允许的值（基本、标准或高级），如果未指定任何值，则分配默认值（基本）。 Basic 提供单个节点，该节点具有多种大小，最大大小为 53 GB。 Standard 提供“主/副本”两个节点，这些节点具有多种大小（最大 53 GB）并提供 99.9% SLA。

### <a name="cacheskufamily"></a>cacheSKUFamily

SKU 的系列。

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

新 Azure Redis 缓存实例的大小。

对于“基本”和“标准”系列：

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

“高级”值缓存容量的定义相同，不同之处在于允许的值为 1-5，而不是 0-6。

模板定义此参数允许的整数值（“基本”和“标准”系列为 0-6，“高级”系列为 1-5）。 如果未指定任何值，则模板将为“基本”和“标准”系列分配默认值 0，为“高级”系列分配 1。

这些值对应于以下缓存大小：

| 值 | “基本”和“标准”<br>缓存大小 | 高级<br>缓存大小 |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB（默认值）                 | 不适用                   |
| 1     | 1GB                             | 6 GB（默认值）        |
| 2     | 2.5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/a                   |
