---
title: 教程：使用 Notebook 创建预测模型（第 1 部分，共 2 部分）
titleSuffix: Azure Machine Learning
description: 了解如何使用 Jupyter Notebook 中的代码生成和部署机器学习模型，以便可以使用它来预测 Microsoft Power BI 中的结果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370581"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>教程：Power BI 集成 - 使用 Notebook 创建预测模型（第 1 部分，共 2 部分）

在本教程的第一部分中，你将使用 Jupyter Notebook 中的代码训练和部署预测机器学习模型。 在第 2 部分中，你将使用该模型来预测 Microsoft Power BI 中的结果。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Jupyter Notebook
> * 创建 Azure 机器学习计算实例
> * 使用 scikit-learn 训练回归模型
> * 将模型部署到实时评分终结点

有三种不同的方法可用于创建和部署要在 Power BI 中使用的模型。  本文介绍选项 A：使用 Notebook 训练和部署模型。  此选项显示使用 Azure 机器学习工作室中托管的 Jupyter 笔记本的代码优先创作体验。 

可以改用：

* [选项 B：使用设计器训练和部署模型](tutorial-power-bi-designer-model.md) - 使用设计器（拖放式用户界面）的低代码创作体验。
* [选项 C：使用自动化 ML 训练和部署模型](tutorial-power-bi-automated-model.md) - 可完全自动执行数据准备和模型训练的无代码创作体验。


## <a name="prerequisites"></a>先决条件

- Azure 订阅（[已推出免费试用版](https://aka.ms/AMLFree)）。 
- Azure 机器学习工作区。 如果还没有工作区，请按照[如何创建 Azure 机器学习工作区](./how-to-manage-workspace.md#create-a-workspace)所述进行操作。
- Python 语言和机器学习工作流的入门知识。

## <a name="create-a-notebook-and-compute"></a>创建笔记本和计算

在 [Azure 机器学习工作室](https://ml.azure.com)主页中，选择“新建”，然后选择“笔记本” ：

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="显示如何创建笔记本的屏幕截图":::
 
随即显示一个用于创建新文件的对话框，输入：

1. 笔记本的文件名（例如 `my_model_notebook`）
1. 将“文件类型”改为“笔记本” 

选择“创建”  。 接下来，需要创建一些计算，并将其附加到笔记本以便运行代码单元格。 为此，请选择笔记本顶部的加号图标：

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="显示如何创建计算实例的屏幕截图":::

接下来，在“创建计算实例”页中：

1. 选择 CPU 虚拟机大小 - 对于本教程，选择 Standard_D11_v2（双核，14-GB RAM）就可以了。
1. 选择“**下一页**”。 
1. 在“配置设置”页上，提供有效的“计算名称”（有效字符包括大小写字母、数字和 - 字符） 。
1. 选择“创建”  。

你可能会注意到，笔记本上“计算”旁边的圆圈已变为蓝绿色，指示正在创建计算实例：

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="显示正在创建计算的屏幕截图":::

> [!NOTE]
> 预配计算大约需要 2-4 分钟。

预配计算后，可以使用笔记本来执行代码单元格。 例如，在单元格中键入：

```python
import numpy as np

np.sin(3)
```

接下来，按“Shift-Enter”（或“Control-Enter”或选择单元格旁边的播放按钮） 。 应会看到以下输出：

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="显示单元格执行的屏幕截图":::

现在，你可以构建机器学习模型！

## <a name="build-a-model-using-scikit-learn"></a>使用 scikit-learn 构建模型

本教程中将使用[糖尿病数据集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)，[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)中提供了该数据集。 


### <a name="import-data"></a>导入数据

若要导入数据，请将以下代码复制并粘贴到笔记本中的新“代码单元格”：

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df` pandas 数据帧包含 10 个基线输入变量（例如年龄、性别、体重指数、平均血压和六项血清度量）。 `y_df` pandas 数据帧是目标变量，其中包含基线后一年疾病进展的定量度量值。 共有 442 条记录。

### <a name="train-model"></a>定型模型

在笔记本中创建新的“代码单元格”，并复制粘贴以下代码片段，这将构建一个岭回归模型并使用 Python 的 pickle 格式对模型进行序列化：

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>注册模型

除了模型文件本身的内容，注册模型还将存储模型元数据（模型说明、标记和框架信息），在工作区中管理和部署模型时，这些元数据非常有用。 例如，通过使用标记，可以对模型进行分类，并在工作区中列出模型时应用筛选器。 此外，使用 scikit-learn 框架标记此模型可简化将其部署为 Web 服务的过程，稍后会对这一点进行介绍。

将以下代码复制并粘贴到笔记本中的新“代码单元格”：

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

还可以通过导航到左侧菜单中的“终结点”来查看 Azure 机器学习工作室中的模型：

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="显示模型的屏幕截图":::

### <a name="define-the-scoring-script"></a>定义评分脚本

部署要集成到 Microsoft Power BI 的模型时，需要定义 Python 评分脚本和自定义环境。 评分脚本包含两个函数：

- `init()` - 服务启动后，将执行此函数。 此函数会加载模型（请注意，将自动从模型注册表下载模型），并对其进行反序列化。
- `run(data)` - 使用需要评分的一些输入数据调用服务时，将执行此函数。 

>[!NOTE]
> 我们使用 Python 修饰器来定义输入和输出数据的架构，这对于 Microsoft Power BI 集成发挥作用非常重要。

将以下代码复制并粘贴到笔记本中的新“代码单元格”。 下面的代码片段具有一个单元格魔术，它将代码写入名为 score.py 的文件中。

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
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>定义自定义环境

接下来，我们需要定义对模型进行评分的环境 - 我们需要在此环境中定义上面定义的评分脚本 (score.py) 所需的 Python 包（如 pandas、scikit-learn 等）。

若要定义环境，请将以下代码复制并粘贴到笔记本中的新“代码单元格”：

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

若要部署模型，请将以下代码复制并粘贴到笔记本中的新“代码单元格”：

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> 部署服务大约需要 2-4 分钟。

应会看到已成功部署服务的以下输出：

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

还可以通过导航到左侧菜单中的“终结点”来查看 Azure 机器学习工作室中的服务：

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="显示终结点的屏幕截图":::

建议你测试 Web 服务，以确保其按预期运行。 通过在 Azure 机器学习工作室的左侧菜单中选择“笔记本”导航回笔记本。 将以下代码复制并粘贴到笔记本中的新“代码单元格”以测试服务：

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

输出应类似于以下 json 结构：`{'predict': [[205.59], [68.84]]}`。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何生成和部署模型，使其可供 Microsoft Power BI 使用。 在下一部分中，你将了解如何在 Power BI 报表中使用此模型。

> [!div class="nextstepaction"]
> [教程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
