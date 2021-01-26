---
title: 使用 Azure 数据工厂进行数据引入
titleSuffix: Azure Machine Learning
description: 了解使用 Azure 数据工厂生成数据引入管道的可用选项以及每个选项的优点。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796146"
---
# <a name="data-ingestion-with-azure-data-factory"></a>使用 Azure 数据工厂进行数据引入

本文介绍使用 [Azure 数据工厂](../data-factory/introduction.md)生成数据引入管道的可用选项。 此 Azure 数据工厂管道用于引入用于 [Azure 机器学习](overview-what-is-azure-ml.md)的数据。 通过数据工厂，你可以轻松地提取、转换和加载 (ETL) 数据。 转换数据并将数据加载到存储中后，可以使用它来训练 Azure 机器学习中的机器学习模型。

简单的数据转换可以通过本机数据工厂活动和乐器（[如数据流）进行处理。](../data-factory/control-flow-execute-data-flow-activity.md) 涉及到较复杂的方案时，可以使用一些自定义代码来处理数据。 例如 Python 或 R 代码。

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>比较 Azure 数据工厂数据引入管道 
使用数据工厂在引入期间转换数据的常用方法有多种。 每种方法都有一些优点和缺点，可帮助确定它是否适合于特定用例：

| 方法 | 优点 | 缺点 |
| ----- | ----- | ----- |
| 数据工厂 + Azure Functions | <li> 低延迟，无服务器计算<li>有状态函数<li>可重用函数 | 仅适用于短时间运行的处理 |
| 数据工厂 + 自定义组件 | <li>大规模并行计算<li>适合用于繁重算法 | <li>需要在可执行文件中包装代码<li>处理依赖项和 IO 时存在的复杂性 |
| 数据工厂 + Azure Databricks 笔记本 |<li> Apache Spark<li>本机 Python 环境 |<li>开销可能很高<li>初次创建群集时需要较长时间并会增大延迟

## <a name="azure-data-factory-with-azure-functions"></a>Azure 数据工厂和 Azure 函数

Azure Functions 允许运行小段代码（函数），且不需要担心应用程序基础结构。 使用此选项时，数据将通过包装在 Azure 函数中的自定义 Python 代码进行处理。 

此函数是通过 [Azure 数据工厂 Azure function 活动](../data-factory/control-flow-azure-function-activity.md)调用的。 此方法非常适合轻型数据转换。 

![关系图显示了 azure 数据工厂管道、Azure Function 和运行 ML 管道，以及带有定型模型的 Azure 机器学习管道，以及如何与原始数据和准备好的数据进行交互。](media/how-to-data-ingest-adf/adf-function.png)



* 优点：
    * 以相对较低的延迟在无服务器计算资源中处理数据
    * 数据工厂管道可以调用可实现复杂的数据转换流的[持久 Azure 函数](../azure-functions/durable/durable-functions-overview.md) 
    * 可重复使用且可从其他位置调用的 Azure 函数会抽象掉数据转换的详细信息
* 缺点
    * 在与 ADF 结合使用之前，必须先创建 Azure Functions
    * Azure Functions 仅适用于短时间运行的数据处理

## <a name="azure-data-factory-with-custom-component-activity"></a>具有自定义组件活动的 Azure 数据工厂

使用此选项时，数据将通过包装在可执行文件中的自定义 Python 代码进行处理。 使用 [ Azure 数据工厂自定义组件活动](../data-factory/transform-data-using-dotnet-custom-activity.md)调用它。 与前面的方法相比，此方法更适合较大的数据。

![该图显示了包含“自定义组件”和“运行 ML 管道”的 Azure 数据工厂管道和包含“训练模型”的 Azure 机器学习管道，以及它们与原始数据和准备好的数据进行交互的方式。](media/how-to-data-ingest-adf/adf-customcomponent.png)

* 优点：
    * 数据将在 [Azure Batch](../batch/batch-technical-overview.md) 池中进行处理，该池提供大规模并行和高性能计算
    * 可用于运行繁重的算法并处理大量数据
* 缺点：
    * 在使用数据工厂之前，必须创建 Azure Batch 池
    * 将 Python 代码包装到可执行文件会产生过多的工程工作量。 处理依赖项和输入/输出参数时存在的复杂性

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>具有 Azure Databricks Python 笔记本的 Azure 数据工厂

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 是 Microsoft 云中基于 Apache Spark 的分析平台。

使用此方法时，数据转换将由 Azure Databricks 群集上运行的某个 [Python 笔记本](../data-factory/transform-data-using-databricks-notebook.md)执行。 这也许是全面利用 Azure Databricks 服务的强大之处的最常见方法。 它旨在用于大规模的分布式数据处理。

![该图显示了包含“Azure Databricks Python”和“运行 ML 管道”的 Azure 数据工厂管道和包含“训练模型”的 Azure 机器学习管道，以及它们与原始数据和准备好的数据进行交互的方式。](media/how-to-data-ingest-adf/adf-databricks.png)

* 优点：
    * 在以 Apache Spark 环境为后盾的最强大数据处理 Azure 服务中转换数据
    * 原生支持 Python 以及数据科学框架和库（包括 TensorFlow、PyTorch 和 scikit-learn）
    * 无需将 Python 代码包装到函数或可执行模块中。 代码按原样运行。
* 缺点：
    * 在使用数据工厂之前，必须创建 Azure Databricks 基础结构
    * 开销可能很高，具体取决于 Azure Databricks 配置
    * 从“冷”模式运转计算群集需要一段时间，使解决方案遇到较高的延迟 
    

## <a name="consume-data-in-azure-machine-learning"></a>使用 Azure 机器学习中的数据 

数据工厂管道会将准备好的数据保存到云存储 (例如 Azure Blob 或 Azure Datalake) 。 <br>
使用 Azure 机器学习中的准备好的数据， 

* 从数据工厂管道调用 Azure 机器学习管道。<br>**OR**
* 创建 [Azure 机器学习数据存储](how-to-access-data.md#create-and-register-datastores) 和 [Azure 机器学习数据集](how-to-create-register-datasets.md) 以便以后使用。

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>从数据工厂调用 Azure 机器学习管道

对于 [机器学习操作 (MLOps) 工作流](concept-model-management-and-deployment.md#what-is-mlops)，建议使用此方法。 如果不想设置 Azure 机器学习管道，请参阅 [直接从存储读取数据](#read-data-directly-from-storage)。

每次运行数据工厂管道时， 

1. 数据将保存到存储中的其他位置。 
1. 为了将位置传递到 Azure 机器学习，数据工厂管道调用 [Azure 机器学习管道](concept-ml-pipelines.md)。 调用 ML 管道时，数据位置和运行 ID 将作为参数发送。 
1. 然后，ML 管道可以使用数据位置创建 Azure 机器学习数据存储和数据集。 有关详细信息，请参阅在 [数据工厂中执行 Azure 机器学习管道](../data-factory/transform-data-machine-learning-service.md)。

![该图显示了 Azure 数据工厂管道和 Azure 机器学习管道，以及它们与原始数据和准备好的数据进行交互的方式。 数据工厂管道将数据馈送到“准备好的数据”数据库，该数据库为数据存储提供数据，数据存储又为机器学习工作区中的数据集提供数据。](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> 数据集[支持版本控制](./how-to-version-track-datasets.md)，因此 ML 管道可以注册指向 ADF 管道中最新数据的新数据集版本。

可通过数据存储或数据集访问数据后，可以使用这些数据来训练 ML 模型。 训练过程可能是从 ADF 中调用的同一 ML 管道的一部分。 或者，它可能是一个单独的过程，例如 Jupyter 笔记本中的试验。

由于数据集支持版本控制，并且每次从管道运行都会创建一个新版本，因此，很容易就知道使用了哪个数据版本来训练模型。

### <a name="read-data-directly-from-storage"></a>直接从存储读取数据

如果不想创建 ML 管道，则可以直接从存储帐户访问数据，该存储帐户使用 Azure 机器学习数据存储和数据集保存准备的数据。 

以下 Python 代码演示了如何创建连接到 Azure DataLake 第2代存储的数据存储。 [详细了解数据存储以及在何处查找服务主体权限](how-to-access-data.md#create-and-register-datastores)。

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

接下来，创建一个数据集，以引用要在机器学习任务中使用的) 文件 (。 

下面的代码从 csv 文件创建 TabularDataset `prepared-data.csv` 。 了解有关 [数据集类型和接受的文件格式](how-to-create-register-datasets.md#dataset-types)的详细信息。 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

在这里，使用 `prepared_dataset` 引用已准备好的数据，如训练脚本中所述。 了解如何 [在 Azure 机器学习中用数据集训练模型](./how-to-train-with-datasets.md)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 数据工厂中运行 Databricks 笔记本](../data-factory/transform-data-using-databricks-notebook.md)
* [访问 Azure 存储服务中的数据](./how-to-access-data.md#create-and-register-datastores)
* [Azure 机器学习中的数据集定型模型](./how-to-train-with-datasets.md)。
* [数据引入管道的 DevOps](./how-to-cicd-data-ingestion.md)