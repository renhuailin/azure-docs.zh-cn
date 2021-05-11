---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 04/21/2021
ms.author: larryfr
ms.openlocfilehash: a7a6d9eb7e552ee5dededc58516927d0d8e0eef6
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107952131"
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

将此 JSON 保存为名为 `deploymentconfig.json` 的文件。