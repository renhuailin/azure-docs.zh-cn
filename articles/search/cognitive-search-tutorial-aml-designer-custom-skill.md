---
title: 示例：使用 Azure 机器学习设计器创建和部署自定义技能
titleSuffix: Azure Cognitive Search
description: 本示例将演示如何使用 Azure 机器学习设计器为 Azure 认知搜索的 AI 扩充管道构建和部署自定义 AML 技能。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 5af4110d1092c2e0e916d1404e9177b64e650e0b
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728103"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning-designer"></a>示例：使用 Azure 机器学习设计器构建和部署自定义技能

[Azure 机器学习设计器](../machine-learning/concept-designer.md)是一种易于使用的交互式画布，可为回归和分类等任务创建机器学习模型。 若要在认知搜索扩充管道中调用由设计器创建的模型，需要先完成一些附加步骤。 在此示例中，你将创建一个简单的回归模型来预测汽车的价格，并将推理终结点作为 AML 技能进行调用。 

按照[示例管道与数据集](../machine-learning/concept-designer.md)文档页面的[回归 - 汽车价格预测（高级）](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md)教程创建模型，以预测给定不同功能的汽车的价格。

> [!IMPORTANT] 
> 按照实时推理过程部署模型将会生成有效终结点，但不会生成可与认知搜索中的 AML 技能配合使用的终结点。 

## <a name="register-model-and-download-assets"></a>注册模型并下载资产

训练模型后，请[注册已训练的模型](../machine-learning/how-to-deploy-model-designer.md)，然后按照相关步骤下载 `trained_model_outputs` 文件夹中的所有文件，或仅从模型项目页面下载 `score.py` 和 `conda_env.yml` 文件。 在将模型部署为实时推理终结点之前，应先编辑评分脚本。


## <a name="edit-the-scoring-script-for-use-with-cognitive-search"></a>编辑与认知搜索配合使用的评分脚本 

认知搜索扩充管道适用于单个文档，并会生成包含单个预测输入的请求。 下载的 `score.py` 可接受记录列表，并将预测列表作为序列化的 JSON 字符串返回。 你将对 `score.py` 作出两次更改

* 编辑脚本以使用单个输入记录，而不是列表
* 编辑脚本以返回具有单个属性（预测价格）的 JSON 对象。

打开下载的 `score.py` 并编辑 `run(data)` 函数。 该函数当前设置为期望包含以下输入，如模型的 `_samples.json` 文件中所述。

```json
[
  {
    "symboling": 2,
    "make": "mitsubishi",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "hatchback",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 93.7,
    "length": 157.3,
    "width": 64.4,
    "height": 50.8,
    "curb-weight": 1944,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 2.97,
    "stroke": 3.23,
    "compression-ratio": 9.4,
    "horsepower": 68.0,
    "peak-rpm": 5500.0,
    "city-mpg": 31,
    "highway-mpg": 38,
    "price": 6189.0
  },
  {
    "symboling": 0,
    "make": "toyota",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "four",
    "body-style": "wagon",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 95.7,
    "length": 169.7,
    "width": 63.6,
    "height": 59.1,
    "curb-weight": 2280,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 3.05,
    "stroke": 3.03,
    "compression-ratio": 9.0,
    "horsepower": 62.0,
    "peak-rpm": 4800.0,
    "city-mpg": 31,
    "highway-mpg": 37,
    "price": 6918.0
  },
  {
    "symboling": 1,
    "make": "honda",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "sedan",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 96.5,
    "length": 169.1,
    "width": 66.0,
    "height": 51.0,
    "curb-weight": 2293,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 110,
    "fuel-system": "2bbl",
    "bore": 3.15,
    "stroke": 3.58,
    "compression-ratio": 9.1,
    "horsepower": 100.0,
    "peak-rpm": 5500.0,
    "city-mpg": 25,
    "highway-mpg": 31,
    "price": 10345.0
  }
]
```

你所做的更改将确保模型可以接受在编制索引期间由认知搜索生成的输入（即单个记录）。

```json
{
    "symboling": 2,
    "make": "mitsubishi",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "hatchback",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 93.7,
    "length": 157.3,
    "width": 64.4,
    "height": 50.8,
    "curb-weight": 1944,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 2.97,
    "stroke": 3.23,
    "compression-ratio": 9.4,
    "horsepower": 68.0,
    "peak-rpm": 5500.0,
    "city-mpg": 31,
    "highway-mpg": 38,
    "price": 6189.0
}
```

将第 27 行至第 30 行替换为
```python

    for key, val in data.items():
        input_entry[key].append(decode_nan(val))
```
你还需要编辑输出，即从字符串生成到 JSON 对象的脚本。 编辑原始文件中的 return 语句（第 37 行）：
```python
    output = result.data_frame.values.tolist()
    return {
        "predicted_price": output[0][-1]
    }
```

以下是更新后的 `run` 函数，其中输入格式和预测输出已作更改，因此该函数将接受单个记录作为输入，并会返回包含预测价格的 JSON 对象。

```python
def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    # data is now a JSON object not a list of JSON objects
    for key, val in data.items():
        input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)
    score_module = ScoreModelModule()
    result, = score_module.run(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        append_or_result_only=True)
    #return json.dumps({"result": result.data_frame.values.tolist()})
    output = result.data_frame.values.tolist()
    # return the last column of the the first row of the dataframe
    return  {
        "predicted_price": output[0][-1]
    }
```
## <a name="register-and-deploy-the-model"></a>注册和部署模型

保存更改后，你现在便可在门户中注册模型。 选择“注册模型”，并为其提供有效名称。 为模型框架选择 `Other`，为框架名称选择 `Custom`，为框架版本选择 `1.0`。 选择 `Upload folder` 选项，然后选择当中包含已更新的 `score.py` 和 `conda_env.yaml` 的文件夹。

选择模型，然后选择 `Deploy` 操作。 部署步骤会假设你已经预配 AKS 推理群集。 认知搜索当前不支持容器实例。
 1. 提供有效的云终结点名称
2. 选择“`Azure Kubernetes Service`”计算类型
3. 选择推理群集的计算名称
4. 将 `enable authentication` 切换为“开”
5. 为此类型选择 `Key-based authentication`
6. 为 `score.py` 选择更新后的 `entry script file`
7. 为 `conda_env.yaml` 选择 `conda dependencies file`
8. 选择“部署”按钮以部署新的终结点。

## <a name="integrate-with-cognitive-search"></a>与认知搜索集成

将新创建的终结点与认知搜索集成
1. 将包含单个汽车记录的 JSON 文件添加到 blob 容器
2. 使用[导入数据工作流](cognitive-search-quickstart-blob.md)配置 AI 扩充管道。 请务必选择 `JSON` 作为 `parsing mode`
3. 在“`Add Enrichments`”选项卡上，选择单项技能“`Extract people names`”作为占位符。
4. 将新字段添加到类型为 `Edm.Double` 名为 `predicted_price` 的索引中，并将可检索属性设置为 true。
5. 完成导入数据过程

### <a name="add-the-aml-skill-to-the-skillset"></a>将 AML 技能添加到技能组

从技能组列表中，选择你创建的技能组。 现在，你将编辑技能组，以将人员识别技能替换为 AML 技能以预测价格。 在“技能组定义 (JSON)”选项卡上，从“技能”下拉列表中选择 `Azure Machine Learning (AML)`。 选择工作区，若要通过 AML 技能发现终结点，则工作区和搜索服务需在同一 Azure 订阅中。
选择之前在教程中创建的终结点。
验证技能是否按照部署终结点时配置的 URI 和身份验证信息进行了填充。 复制技能模板并替换技能组中的技能。
编辑以下技能：
1. 将名称设为有效名称
2. 添加描述
3. 将 degreesOfParallelism 设为 1
4. 将上下文设为 `/document`
5. 将输入设为所有必需输入（参阅下方的示例技能定义）
6. 设置输出以捕获返回的预测价格。

```json
{
      "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
      "name": "AMLdemo",
      "description": "AML Designer demo",
      "context": "/document",
      "uri": "Your AML endpoint",
      "key": "Your AML endpoint key",
      "resourceId": null,
      "region": null,
      "timeout": "PT30S",
      "degreeOfParallelism": 1,
      "inputs": [
        {
          "name": "symboling",
          "source": "/document/symboling"
        },
        {
          "name": "make",
          "source": "/document/make"
        },
        {
          "name": "fuel-type",
          "source": "/document/fuel-type"
        },
        {
          "name": "aspiration",
          "source": "/document/aspiration"
        },
        {
          "name": "num-of-doors",
          "source": "/document/num-of-doors"
        },
        {
          "name": "body-style",
          "source": "/document/body-style"
        },
        {
          "name": "drive-wheels",
          "source": "/document/drive-wheels"
        },
        {
          "name": "engine-location",
          "source": "/document/engine-location"
        },
        {
          "name": "wheel-base",
          "source": "/document/wheel-base"
        },
        {
          "name": "length",
          "source": "/document/length"
        },
        {
          "name": "width",
          "source": "/document/width"
        },
        {
          "name": "height",
          "source": "/document/height"
        },
        {
          "name": "curb-weight",
          "source": "/document/curb-weight"
        },
        {
          "name": "engine-type",
          "source": "/document/engine-type"
        },
        {
          "name": "num-of-cylinders",
          "source": "/document/num-of-cylinders"
        },
        {
          "name": "engine-size",
          "source": "/document/engine-size"
        },
        {
          "name": "fuel-system",
          "source": "/document/fuel-system"
        },
        {
          "name": "bore",
          "source": "/document/bore"
        },
        {
          "name": "stroke",
          "source": "/document/stroke"
        },
        {
          "name": "compression-ratio",
          "source": "/document/compression-ratio"
        },
        {
          "name": "horsepower",
          "source": "/document/horsepower"
        },
        {
          "name": "peak-rpm",
          "source": "/document/peak-rpm"
        },
        {
          "name": "city-mpg",
          "source": "/document/city-mpg"
        },
        {
          "name": "highway-mpg",
          "source": "/document/highway-mpg"
        },
        {
          "name": "price",
          "source": "/document/price"
        }
      ],
      "outputs": [
        {
          "name": "predicted_price",
          "targetName": "predicted_price"
        }
      ]
    }
```
### <a name="update-the-indexer-output-field-mappings"></a>更新索引器输出字段映射

索引器输出字段映射将确定保存到索引中的扩充。 将索引器的输出字段映射部分替换为以下代码片段：

```json
"outputFieldMappings": [
    {
      "sourceFieldName": "/document/predicted_price",
      "targetFieldName": "predicted_price"
    }
  ]
```

你现在可以运行索引器，并验证 `predicted_price` 属性是否以来自 AML 技能输出的结果填充到了索引中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看自定义技能 web api](./cognitive-search-custom-skill-web-api.md)

> [详细了解如何将自定义技能添加到扩充管道](./cognitive-search-custom-skill-interface.md)

> [详细了解 AML 技能](./cognitive-search-tutorial-aml-custom-skill.md)