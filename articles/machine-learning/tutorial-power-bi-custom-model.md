---
title: Tutorial:使用 Notebook 创建预测模型（第 1 部分，共 2 部分）
titleSuffix: Azure Machine Learning
description: 了解如何使用 Jupyter Notebook 中的代码来构建和部署机器学习模型。 另外，创建一个评分脚本，该脚本定义输入和输出，以便轻松集成到 Microsoft Power BI 中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 409936f98dab0fa975c1314a84096b7b46df7613
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459399"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Tutorial:Power BI 集成 - 使用 Jupyter Notebook 创建预测模型（第 1 部分，共 2 部分）

在本教程的第 1 部分中，你将使用 Jupyter Notebook 中的代码训练和部署预测机器学习模型。 你还将创建一个评分脚本，以定义模型的输入和输出架构以集成到 Power BI。  在第 2 部分中，你将使用该模型来预测 Microsoft Power BI 中的结果。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Jupyter Notebook。
> * 创建 Azure 机器学习计算实例。
> * 使用 scikit-learn 训练回归模型。
> * 编写一个评分脚本，该脚本定义输入和输出，以便轻松集成到 Microsoft Power BI 中。
> * 将模型部署到实时评分终结点。

可以通过三种方法创建和部署要在 Power BI 中使用的模型。  本文介绍“选项 A：使用笔记本训练和部署模型。”  此选项是代码优先的创作体验。 这会使用 Azure 机器学习工作室中托管的 Jupyter Notebook。 

但你可改用其他选项之一：

* [选项 B：使用 Azure 机器学习设计器训练和部署模型](tutorial-power-bi-designer-model.md)。 这种少用代码的创作体验使用拖放式用户界面。
* [选项 C：使用自动化机器学习训练和部署模型](tutorial-power-bi-automated-model.md)。 这种不用代码的创作体验可完全自动执行数据准备和模型训练。


## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有订阅，可使用[免费试用版](https://azure.microsoft.com/free/)。 
- Azure 机器学习工作区。 如果没有工作区，请参阅[创建和管理 Azure 机器学习工作区](./how-to-manage-workspace.md#create-a-workspace)。
- Python 语言和机器学习工作流的入门知识。

## <a name="create-a-notebook-and-compute"></a>创建笔记本和计算

在 [Azure 机器学习工作室](https://ml.azure.com)主页中，选择“新建” > “笔记本”  ：

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="显示如何创建笔记本的屏幕截图。":::
 
在“创建新文件”页上，执行以下操作：

1. 为笔记本命名（例如 my_model_notebook）。
1. 将“文件类型”改为“笔记本” 。
1. 选择“创建”  。 
 
接下来，若要运行代码单元格，请创建一个计算实例并将其附加到笔记本。 首先选择笔记本顶部的加号图标：

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="显示如何创建计算实例的屏幕截图。":::

在“创建计算实例”页上，执行以下操作：

1. 选择 CPU 虚拟机大小。 对于本教程，可以选择 Standard_D11_v2，其中包含 2 个核心和 14 GB 的 RAM。
1. 选择“**下一页**”。 
1. 在“配置设置”页上，提供有效的“计算名称” 。 有效字符包括大写字母、小写字母、数字和连字符 (-)。
1. 选择“创建”  。

在笔记本中，你可能会注意到“计算”旁边的圆圈变为了蓝绿色。 此颜色更改表明正在创建计算实例：

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="显示正在创建计算的屏幕截图。":::

> [!NOTE]
> 计算实例可能需要 2 到 4 分钟时间才能完成预配。

预配计算后，可以使用笔记本来运行代码单元格。 例如，在单元格中，可以键入以下代码：

```python
import numpy as np

np.sin(3)
```

然后按 Shift + Enter（或按 Control + Enter 或选择单元格旁边的“播放”按钮）。 应会看到以下输出：

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="显示单元格输出的屏幕截图。":::

现在，你可以构建机器学习模型。

## <a name="build-a-model-by-using-scikit-learn"></a>使用 scikit-learn 构建模型

在本教程中，将使用[糖尿病数据集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。 [Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)中提供了该数据集。


### <a name="import-data"></a>导入数据

若要导入数据，请复制以下代码并将其粘贴到笔记本中的新“代码单元格”。

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df` pandas 数据帧包含 10 个基线输入变量。 这些变量包括年龄、性别、体重指数、平均血压和六个血清测定值。 `y_df` pandas 数据帧是目标变量。 其中包含基线后一年疾病进展的量化度量值。 该数据帧包含 442 条记录。

### <a name="train-the-model"></a>定型模型

在笔记本中创建新的代码单元格。 然后复制下面的代码并将其粘贴到单元格中。 此代码片段可构造岭回归模型，并使用 Python pickle 格式对其进行序列化。

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>注册模型

除了模型文件本身的内容之外，注册的模型还将存储元数据。 元数据包括模型说明、标记和框架信息。 

在工作区中管理和部署模型时，元数据很有用。 例如，通过使用标记，可以对模型进行分类，并在工作区中列出模型时应用筛选器。 此外，如果使用 scikit-learn 框架标记此模型，则可简化将其部署为 Web 服务的过程。

复制以下代码，然后将其粘贴到笔记本中的新“代码单元格”。

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

你还可以在 Azure 机器学习工作室中查看模型。 在左侧菜单中选择“模型”：

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="显示如何查看模型的屏幕截图。":::

## <a name="define-the-scoring-script"></a>定义评分脚本

部署要集成到 Power BI 的模型时，需要定义 Python 评分脚本和自定义环境。 评分脚本包含两个函数：

- 当服务启动时，`init()` 函数将运行。 它会加载模型（从模型注册表自动下载），并对其进行反序列化。
- 当对服务的调用包含需要评分的输入数据时，`run(data)` 函数将运行。 

>[!NOTE]
> 下方代码中的 Python 修饰器可定义输入和输出数据的架构，这对于到 Power BI 的集成非常重要。

复制以下代码并将其粘贴到笔记本中的新“代码单元格”。 以下代码片段具有单元格 magic，可将代码写入名为 score.py 的文件中。

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>定义自定义环境

接下来，定义环境以对模型进行评分。 在环境中，定义评分脚本 (score.py) 所需的 Python 包，例如 pandas 和 scikit-learn。

若要定义环境，请复制以下代码并将其粘贴到笔记本中的新“代码单元格”。

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>部署模型

若要部署模型，请复制以下代码并将其粘贴到笔记本中的新“代码单元格”：

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> 该服务可能需要 2 到 4 分钟才能完成部署。

如果服务成功部署，则应会看到以下输出：

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

你还可以在 Azure 机器学习工作室中查看服务。 在左侧菜单中选择“终结点”：

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="显示如何查看服务的屏幕截图。":::

建议你测试 Web 服务，以确保其按预期运行。 若要返回笔记本，请在 Azure 机器学习工作室中的左侧菜单中，选择“Notebooks”。 然后复制以下代码并将其粘贴到笔记本中的新“代码单元格”以测试服务。

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

输出应类似于此 JSON 结构：`{'predict': [[205.59], [68.84]]}`。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何生成和部署模型，使其可供 Power BI 使用。 在下一部分中，你将了解如何在 Power BI 报表中使用此模型。

> [!div class="nextstepaction"]
> [教程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
