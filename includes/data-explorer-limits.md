---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: f9143f3220ad5f90345436c3c1f97419e4d26fc4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114593415"
---
下表介绍了 Azure 数据资源管理器群集的最大限制。

| 资源 | 限制 |
| --- | --- |
| 每个订阅每个区域的群集数 | 20 |
| 每个群集的实例数 | 1000 | 
| 一个群集中的数据库数 | 10,000 |
| 每个领导群集（数据共享生产者）的关注者群集（数据共享使用者）数 | 70 |

下表介绍了对在 Azure 数据资源管理器群集上执行的管理操作的限制。

| 作用域 | 操作 | 限制 |
| --- | --- | --- |
| 群集 | 读取（例如，获取群集） | 每 5 分钟 500 次 |
| 群集 | 写入（例如，创建数据库） | 每小时 1000 次 |

