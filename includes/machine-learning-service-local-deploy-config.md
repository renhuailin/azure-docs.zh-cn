---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508089"
---
`deploymentconfig.json` 文档中的条目对应于 [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration) 的参数。 下表描述了 JSON 文档中的实体与方法参数之间的映射：

| JSON 实体 | 方法参数 | 说明 |
| ----- | ----- | ----- |
| `computeType` | 不可用 | 计算目标。 对于本地目标，值必须是 `local`。 |
| `port` | `port` | 用于公开服务的 HTTP 终结点的本地端口。 |

此 JSON 是用于 CLI 的部署配置示例：

```json
{
    "computeType": "local",
    "port": 32267
}
```