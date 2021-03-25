---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93325276"
---
入口脚本接收提交到已部署 Web 服务的数据，并将此数据传递给模型。 然后，该脚本接收模型返回的响应，并将该响应返回给客户端。 该脚本特定于你的模型。 它必须能够识别模型需要和返回的数据。

需要在入口脚本中完成以下两项操作：

1. 加载模型（使用名为 `init()` 的函数）
1. 对输入数据运行模型（使用名为 `run()` 的函数）

下面将详细介绍这些步骤。

### <a name="writing-init"></a>编写 init() 

#### <a name="loading-a-registered-model"></a>加载已注册的模型

已注册的模型将存储在名为 `AZUREML_MODEL_DIR` 的环境变量所指向的路径上。 有关确切目录结构的详细信息，请参阅[在入口脚本中查找模型文件](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>加载本地模型

如果你在选择不注册模型的情况下将模型作为源目录的一部分传递，则可以通过传递模型的相对于评分脚本的路径，像在本地一样读入它。 例如，如果目录结构如下：

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

可以使用以下 Python 代码加载模型：

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>编写 run()

`run()` 在模型每次收到评分请求时执行，并预期请求的正文是一个结构如下的 JSON 文档：

```json
{
    "data": <model-specific-data-structure>
}

```

`run()` 的输入是一个 Python 字符串，其中包含“data”键后面的任何内容。

以下示例演示如何加载已注册的 scikit-learn 模型并使用 numpy 数据对其进行评分：

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

有关更高级的示例，包括 Swagger 架构的自动生成和二进制（即映像）数据，请阅读[有关高级入口脚本创作的文章](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)