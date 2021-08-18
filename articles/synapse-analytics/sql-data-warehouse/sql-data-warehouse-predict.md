---
title: 使用 PREDICT 对机器学习模型进行评分
description: 了解如何在专用 SQL 池中使用 T-SQL PREDICT 函数对机器学习模型进行评分。
services: synapse-analytics
author: rothja
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: jroth
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 11e5f2e3ea46794367247ef9a1b4a0a43f7d6c1e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438303"
---
# <a name="score-machine-learning-models-with-predict"></a>使用 PREDICT 对机器学习模型进行评分

专用 SQL 池为你提供了使用熟悉的 T-SQL 语言对机器学习模型进行评分的功能。 利用 T-SQL [PREDICT](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)，可以引入已使用历史数据训练的现有机器学习模型，并在数据仓库的安全边界内对这些模型进行评分。 PREDICT 函数使用 [ONNX (Open Neural Network Exchange)](https://onnx.ai/) 模型和数据作为输入。 此功能消除了将有价值的数据移到数据仓库外部进行评分的步骤。 它的目标是使数据专业人员能够使用熟悉的 T-SQL 接口轻松地部署机器学习模型，并对其任务使用正确的框架，以便与数据科学家无缝地协作。

> [!NOTE]
> 无服务器 SQL 池目前不支持此功能。

此功能要求在 Synapse SQL 外部训练模型。 在构建模型后，将模型加载到数据仓库中，然后使用 T-SQL Predict 语法对该模型进行评分，以从数据中获取见解。

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>训练模型

专用 SQL 池需要一个预先训练的模型。 在训练用于在专用 SQL 池中执行预测的机器学习模型时，请牢记以下因素。

- 专用 SQL 池仅支持 ONNX 格式模型。 ONNX 是一种开源模型格式，可用于在各种框架之间交换模型以实现互操作性。 你可以将现有模型转换为 ONNX 格式（使用本身就支持此格式的框架或使用提供转换包的框架）。 例如，[sklearn-onnx](https://github.com/onnx/sklearn-onnx) 包将 scikit-learn 模型转换为 ONNX。 [ONNX GitHub 存储库](https://github.com/onnx/tutorials#converting-to-onnx-format)提供了一系列受支持的框架和示例。

   如果你使用[自动化 ML](../../machine-learning/concept-automated-ml.md) 进行训练，请确保将 enable_onnx_compatible_models 参数设置为 TRUE，以生成 ONNX 格式模型。 [自动化机器学习笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)显示了一个示例，该示例展示了如何使用自动化 ML 创建 ONNX 格式的机器学习模型。

- 支持将以下数据类型用于输入数据：
    - int、bigint、real、float
    - char、varchar、nvarchar

- 评分数据需要采用与训练数据相同的格式。 PREDICT 不支持复杂数据类型（例如多维数组）。 因此，对于训练，请确保模型的每个输入对应于评分表的单个列，而不是传递包含所有输入的单个数组。

- 请确保模型输入的名称和数据类型与新预测数据的列的名称和数据类型匹配。 使用在线提供的各种开源工具将 ONNX 模型可视化可以进一步帮助你进行调试。

## <a name="loading-the-model"></a>加载模型

模型以十六进制字符串的形式存储在专用 SQL 池用户表中。 可以在模型表中添加 ID 和说明等其他列，以便标识模型。 将 varbinary(max) 用作模型列的数据类型。 下面是可用于存储模型的表的代码示例：

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

将模型转换为十六进制字符串并指定表定义后，使用 [COPY 命令](/sql/t-sql/statements/copy-into-transact-sql?preserve-view=true&view=azure-sqldw-latest)或 Polybase 将此模型加载到专用 SQL 池表中。 下面的代码示例使用 Copy 命令来加载模型。

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>对模型评分

将模型和数据加载到数据仓库中后，使用 T-SQL PREDICT 函数对模型进行评分。 请确保新的输入数据采用的格式与用于构建模型的训练数据采用的格式相同。 T-SQL PREDICT 采用两种输入数据：模型输入数据和新评分输入数据，并为输出生成新列。可以将模型指定为变量、文本或标量 sub_query。 使用 [WITH common_table_expression](/sql/t-sql/queries/with-common-table-expression-transact-sql?preserve-view=true&view=azure-sqldw-latest) 指定数据参数的已命名结果集。

下面的示例展示了一个使用预测函数的示例查询。 创建了一个名称为 Score 且数据类型为 float 的附加列，其中包含预测结果。 所有输入数据列和输出预测列都可以通过 Select 语句进行显示。 如需更多详细信息，请参阅 [PREDICT (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)。

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>后续步骤

若要详细了解 PREDICT 函数，请参阅 [PREDICT (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)。