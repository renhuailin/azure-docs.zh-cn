---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 04/21/2021
ms.author: gopalv
ms.openlocfilehash: 0bac8d2b70ea1dc0dd624cae669f6b2f4c2e1c6e
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107952133"
---
入口脚本接收提交到已部署 Web 服务的数据，并将此数据传递给模型。 然后，其将模型的响应返回给客户端。 该脚本特定于你的模型。 入口脚本必须能够理解模型期望和返回的数据。

需要在入口脚本中完成以下两项操作：

1. 加载模型（使用名为 `init()` 的函数）
1. 对输入数据运行模型（使用名为 `run()` 的函数）

对于初始部署，请使用虚拟入口脚本来打印接收到的数据。

```python
import json

def init():
    print('This is init')

def run(data):
    test = json.loads(data)
    print(f'received data {test}')
    return(f'test is {test}')

```
将此文件以 `echo_score.py` 形式保存到名为 `source_dir` 的目录中。

因此，例如如果用户使用以下操作调用模型：

```bash
curl -X POST -d '{"this":"is a test"}' -H "Content-Type: application/json" http://localhost:6789/score
```

以下值将会返回：

```bash
"test is {'this': 'is a test'}"
```