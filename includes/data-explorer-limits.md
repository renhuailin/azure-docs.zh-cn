---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: 86b2353109d4005594ee08e27283d6aa9c654120
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078460"
---
下表介绍了 Azure 数据资源管理器群集的最大限制。

| 资源 | 限制 |
| --- | --- |
| 每个订阅每个区域的群集数 | 20 |
| 每个群集的实例数 | 1000 | 
| 一个群集中的数据库数 | 10,000 |
| 每个领导群集（数据共享生产者）的关注者群集（数据共享使用者）数 | 100 |

下表介绍了对在 Azure 数据资源管理器群集上执行的管理操作的限制。

| 作用域 | 操作 | 限制 |
| --- | --- | --- |
| 群集 | 读取（例如，获取群集） | 每 5 分钟 500 次 |
| 群集 | 写入（例如，创建数据库） | 每小时 1000 次 |

