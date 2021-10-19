---
title: 使用工作室部署在设计器中训练的模型
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习工作室部署机器学习模型，而无需编写任何代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/08/2021
ms.topic: how-to
ms.custom: deploy, studio, designer
ms.openlocfilehash: d2553ddde013c43b9ce4d23d2cc0b4541150e6f3
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740464"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>使用工作室部署在设计器中训练的模型

本文介绍如何在 Azure 机器学习工作室中将设计器模型部署为实时终结点。

注册或下载后，就可以像使用任何其他模型一样使用设计器训练的模型。 导出的模型可以在用例（例如物联网 (IoT) 和本地部署）中进行部署。

工作室中的部署包括以下步骤：

1. 注册已训练的模型。
1. 下载模型的输入脚本和 conda 依赖项文件。
1. （可选）配置入口脚本。
1. 将模型部署到计算目标。

还可以直接在设计器中部署模型，以跳过模型注册和文件下载步骤。 这对于快速部署非常有用。 有关详细信息，请参阅[通过设计器部署模型](tutorial-designer-automobile-price-deploy.md)。

在设计器中训练的模型也可以通过 SDK 或命令行接口 (CLI) 进行部署。 有关详细信息，请参阅[使用 Azure 机器学习部署现有模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>先决条件

* [一个 Azure 机器学习工作区](how-to-manage-workspace.md)

* 已完成的包含下列模块之一的训练管道：
    - [“定型模型”模块](./algorithm-module-reference/train-model.md)
    - [“训练异常情况检测模型”模块](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [“训练聚类分析模型”模块](./algorithm-module-reference/train-clustering-model.md)
    - [“训练 Pytorch 模型”模块](./algorithm-module-reference/train-pytorch-model.md)
    - [“训练 SVD 推荐器”模块](./algorithm-module-reference/train-svd-recommender.md)
    - [“训练 Vowpal Wabbit 模型”模块](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [“训练宽度和深度模型”模块](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>注册模型

训练管道完成后，将训练的模型注册到 Azure 机器学习工作区，以在其他项目中访问该模型。

1. 选择[“训练模型”模块](./algorithm-module-reference/train-model.md)。
1. 在有窗口中选择“输出 + 日志”选项卡。
1. 选择“注册模型”图标![齿轮图标的屏幕截图](./media/how-to-deploy-model-designer/register-model-icon.png)。

    ![右窗格“训练模型”模块的屏幕截图](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. 输入模型的名称，然后选择“保存”。

注册模型后，该模型将显示在工作室中的“模型”资产页面。
    
![“模型”资产页中的已注册模型的屏幕截图](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>下载输入口脚本文件和 conda 依赖项文件

需要以下文件才能在 Azure 机器学习工作室中部署模型：

- **入口脚本文件** - 加载训练的模型、处理来自请求的输入数据、执行实时推理，并返回结果。 当“训练模型”模块完成时，设计器将自动生成 `score.py` 入口脚本文件。

- **Conda 依赖项文件** - 指定 Web 服务依赖的 pip 和 conda 包。 当“训练模型”模块完成时，设计器将自动创建 `conda_env.yaml` 文件。

可以在右窗格“训练模型”模块中下载这两个文件：

1. 选择 **训练模型** 模块。
1. 在“输出 + 日志”选项卡中，选择文件夹 `trained_model_outputs`。
1. 下载 `conda_env.yaml` 文件和 `score.py` 文件。

    ![用于在右窗格中进行部署的下载文件的屏幕截图](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

或者，可以在注册模型后，从“模型”资产页中下载这些文件：

1. 导航到“模型”资产页。
1. 选择要部署的模型。
1. 选择“项目”选项卡。
1. 选择 `trained_model_outputs` 文件夹。
1. 下载 `conda_env.yaml` 文件和 `score.py` 文件。  

    ![用于在模型详细信息页中进行部署的下载文件的屏幕截图](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> `score.py` 文件提供与“评分模型”模块几乎相同的功能。 但是，某些模块（如[为 SVD 推荐器评分](./algorithm-module-reference/score-svd-recommender.md)、[为 Wide and Deep 推荐器评分](./algorithm-module-reference/score-wide-and-deep-recommender.md)、[对 Vowpal Wabbit 模型评分](./algorithm-module-reference/score-vowpal-wabbit-model.md)）具有不同计分模式的参数。 你还可以在入口脚本中更改这些参数。
>
>有关在 `score.py` 文件中设置参数的详细信息，请参阅[配置入口脚本](#configure-the-entry-script)。

## <a name="deploy-the-model"></a>部署模型

下载所需的文件后，就可以部署该模型了。

1. 在“模型”资产页，选择已注册的模型。
1. 选择“部署”，然后选择“部署到 Web 服务”。 
    ![模型资产页面中“部署”按钮的屏幕截图](./media/how-to-deploy-model-designer/open-deploy-wizard.png)
1. 在“配置”菜单中，输入以下信息：

    - 输入终结点的名称。
    - 选择将模型部署到 [Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)或 [Azure 容器实例](how-to-deploy-azure-container-instance.md)。
    - 为入口脚本文件上传 `score.py`。
    - 为 Conda 依赖项文件上传 `conda_env.yml`。 

    >[!TIP]
    > 在高级设置中，可以为部署设置 CPU/内存容量和其他参数。 这些设置对于某些模型（如 PyTorch 模型）非常重要，它们消耗大量内存（约 4 GB）。

1. 选择“部署”将模型部署为实时终结点。

    ![在模型资产页中部署模型的屏幕截图](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>使用实时终结点

部署成功后，可以在“终结点”资产页中找到实时终结点。 找到 REST 终结点之后，客户端可以使用该终结点将请求提交到实时终结点。 

> [!NOTE]
> 设计器还会生成用于测试的示例数据 json 文件，你可以在 trained_model_outputs 文件夹中下载 `_samples.json`。

使用以下代码示例使用实时终结点。

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>使用与计算机视觉相关的实时终结点

使用与计算机视觉相关的实时终结点时，需要将图像转换为字节，因为 Web 服务仅接受字符串作为输入。 以下是示例代码：

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>配置入口脚本

设计器中的某些模块（如[为 SVD 推荐器评分](./algorithm-module-reference/score-svd-recommender.md)、[为 Wide and Deep 推荐器评分](./algorithm-module-reference/score-wide-and-deep-recommender.md)、[为 Vowpal Wabbit 模型评分](./algorithm-module-reference/score-vowpal-wabbit-model.md)）具有不同计分模式的参数。 

本部分介绍如何在条目脚本文件中更新这些参数。

以下示例更新训练的“Wide & Deep 推荐器”模型的默认行为。 默认情况下，`score.py` 文件指示 Web 服务预测用户和项目之间的评级。 

可以修改入口脚本文件以进行项目建议，并通过更改 `recommender_prediction_kind` 参数返回建议的项目。


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

对于“Wide & Deep 推荐器”和“Vowpal Wabbit”模型，可以使用以下方法配置评分模式参数 ：

- 参数名称是[为 Vowpal Wabbit 模型评分](./algorithm-module-reference/score-vowpal-wabbit-model.md)和[为 Wide and Deep 推荐器评分](./algorithm-module-reference/score-wide-and-deep-recommender.md)的小写和下划线组合的参数名称；
- 模式类型参数值是相应选项名称的字符串。 以上述代码中的“推荐器预测类型”为例，该值可以是 `'Rating Prediction'` 或 `'Item Recommendation'`。 不允许其他值。

对于 SVD 推荐器训练模型，参数名称和值可能不太明显，你可以查找下表以决定如何设置参数。

| [为 SVD 推荐器评分](./algorithm-module-reference/score-svd-recommender.md)中的参数名称                           | 入口脚本文件中的参数名称 |
| ------------------------------------------------------------ | --------------------------------------- |
| 推荐器预测类型                                  | prediction_kind                         |
| 推荐的项选择内容                                   | recommended_item_selection              |
| 单个用户的建议池的最小大小    | min_recommendation_pool_size            |
| 要推荐给用户的最大项目数               | max_recommended_item_count              |
| 是否返回项目的预测评级以及标签 | return_ratings                          |

以下代码演示了如何为 SVD 推荐器设置参数，该推荐器使用所有六个参数来推荐附加了预测评级的评级项目。

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>后续步骤

* [在设计器中训练模型](tutorial-designer-automobile-price-train-score.md)
* [使用 Azure 机器学习 SDK 部署模型](how-to-deploy-and-where.md)
* [排查部署失败问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
