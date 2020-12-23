---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027424"
---
`deploymentconfig.json` 文档中的条目对应于 [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py) 的参数。 下表描述了 JSON 文档中的实体与方法参数之间的映射：

| JSON 实体 | 方法参数 | 说明 |
| ----- | ----- | ----- |
| `computeType` | NA | 计算目标。 对于本地目标，值必须是 `local`。 |
| `port` | `port` | 用于公开服务的 HTTP 终结点的本地端口。 |

此 JSON 是用于 CLI 的部署配置示例：

```json
{
    "computeType": "local",
    "port": 32267
}
```