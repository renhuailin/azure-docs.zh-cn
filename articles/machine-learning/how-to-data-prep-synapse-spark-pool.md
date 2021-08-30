---
title: 使用 Apache Spark 池进行数据整理（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何通过 Azure Synapse Analytics 和 Azure 机器学习附加和启动 Apache Spark 池以进行数据整理。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: 424e1908df6d3e92d365e2dd3e10a42f7d1ddb2a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296415"
---
# <a name="data-wrangling-with-apache-spark-pools-preview"></a>使用 Apache Spark 池进行数据整理（预览版） 

本文介绍如何使用 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/) 在 Jupyter 笔记本中以交互方式执行专用 Synapse 会话中的数据整理任务，该会话由 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 提供支持。 

如果你更喜欢使用 Azure 机器学习管道，请参阅[如何在机器学习管道中使用 Apache Spark（由 Azure Synapse Analytics 提供支持）（预览版）](how-to-use-synapsesparkstep.md)。

有关如何将 Azure Synapse Analytics 与 Synapse 工作区配合使用的指导，请参阅 [Azure Synapse Analytics 入门系列](../synapse-analytics/get-started.md)。

>[!IMPORTANT]
> Azure 机器学习与 Azure Synapse Analytics 的集成以预览版提供。 本文中介绍的功能采用 `azureml-synapse` 包，其中包含[试验性](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，这些功能可能会随时更改。

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration"></a>Azure 机器学习与 Azure Synapse Analytics 的集成

通过 Azure Synapse Analytics 与 Azure 机器学习的集成（预览版），你可以附加由 Azure Synapse 提供支持的 Apache Spark 池，以进行交互式数据探索和准备。 借助这种集成，你可在用于训练机器学习模型的同一 Python 笔记本中，使用专用计算大规模进行数据整理。

## <a name="prerequisites"></a>先决条件

* [已安装 Azure 机器学习 Python SDK](/python/api/overview/azure/ml/install)。 

* [创建 Azure 机器学习工作区](how-to-manage-workspace.md?tabs=python)。

* [在 Azure 门户中创建 Azure Synapse Analytics 工作区](../synapse-analytics/quickstart-create-workspace.md)。

* [使用 Azure 门户、Web 工具或 Synapse Studio 创建 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)。

* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK，或使用已经安装了 SDK 的 [Azure 机器学习计算实例](concept-compute-instance.md#create)。 

* 使用以下代码安装 `azureml-synapse` 包（预览版）：

  ```python
  pip install azureml-synapse
  ```

* 使用 [Azure 机器学习 Python SDK](how-to-link-synapse-ml-workspaces.md#link-sdk) 或 [Azure 机器学习工作室](how-to-link-synapse-ml-workspaces.md#link-studio)链接 Azure 机器学习工作区和 Azure Synapse Analytics 工作区

* [将 Synapse Spark 池附加](how-to-link-synapse-ml-workspaces.md#attach-synapse-spark-pool-as-a-compute)为计算目标。

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>启动 Synapse Spark 池以执行数据整理任务

若要开始通过 Apache Spark 池进行数据准备，请指定附加的 Spark Synapse 计算的名称。 可以通过 Azure 机器学习工作室在“附加的计算”选项卡下找到此名称。 

![获取附加的计算的名称](media/how-to-data-prep-synapse-spark-pool/attached-compute.png)

> [!IMPORTANT]
> 若要继续使用 Apache Spark 池，必须指示要在整个数据整理任务中使用的计算资源，并将 `%synapse` 用于单行代码以及将 `%%synapse` 用于多行代码。 

```python
%synapse start -c SynapseSparkPoolAlias
```

会话启动后，可以检查会话的元数据。

```python
%synapse meta
```

你可以指定要在 Apache Spark 会话期间使用的 [Azure 机器学习环境](concept-environments.md)。 只有在环境中指定的 Conda 依赖项才会生效。 不支持 Docker 映像。

>[!WARNING]
>  Apache Spark 池不支持在环境 Conda 依赖项中指定的 Python 依赖项。 目前仅支持固定 Python 版本。 通过在脚本中包含 `sys.version_info` 来检查 Python 版本。

下面的代码用于创建环境 `myenv`，该环境在会话开始之前安装 `azureml-core` 版本 1.20.0 和 `numpy` 版本1.17.0。 然后，你可以将此环境包含在 Apache Spark 会话 `start` 语句中。

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

要通过 Apache Spark 池和自定义环境开始准备数据，请指定 Apache Spark 池名称和在 Apache Spark 会话期间使用的环境。 此外，还可以提供订阅 ID、机器学习工作区资源组和机器学习工作区的名称。

>[!IMPORTANT]
> 确保在链接的 Synapse 工作区中启用了[允许会话级别包](../synapse-analytics/spark/apache-spark-manage-python-packages.md#session-scoped-packages-preview)。
>
>![启用会话级别包](media/how-to-data-prep-synapse-spark-pool/enable-session-level-package.png)

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```

## <a name="load-data-from-storage"></a>从存储加载数据

Apache Spark 会话启动后，请读取要准备的数据。 Azure Blob 存储和 Azure Data Lake Storage Gen 1 和 Gen 2 支持数据加载。

可以通过两种方法从这些存储服务加载数据： 

* 使用 Hadoop 分布式文件系统 (HDFS) 路径直接从存储中加载数据。

* 从现有 [Azure 机器学习数据集](how-to-create-register-datasets.md)中读取数据。

若要访问这些存储服务，需要具有“存储 Blob 数据读取者”权限。 如果打算将数据写回这些存储服务，则需要具有“存储 Blob 数据参与者”权限。 [详细了解存储权限和角色](../storage/blobs/assign-azure-role-data-access.md)。

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>使用 Hadoop 分布式文件系统 (HDFS) 路径加载数据

要使用相应的 HDFS 路径从存储中加载和读取数据，你需要具有现成的数据访问身份验证凭据。 这些凭据因存储类型而异。  

以下代码演示如何使用共享访问签名 (SAS) 令牌或访问密钥将数据从 Azure Blob 存储读取到 Spark 数据帧中。 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

以下代码演示如何使用服务主体凭据从 Azure Data Lake Storage Generation 1 (ADLS Gen 1) 中读取数据。 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

以下代码演示如何使用服务主体凭据从 Azure Data Lake Storage Generation 2 (ADLS Gen 2) 中读取数据。 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>从已注册的数据集中读取数据

还可以在工作区中获取现有的已注册数据集，并通过将其转换为 Spark 数据帧执行数据准备任务。

以下示例在通过工作区身份验证后，获取一个已注册的 TabularDataset `blob_dset`，该数据集引用 Blob 存储中的文件，并将其转换为 Spark 数据帧。 将数据集转换为 Spark 数据帧时，你可以利用 `pyspark` 数据探索和准备库。  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>执行数据整理任务

检索并探索数据之后，即可以执行数据整理任务。

以下代码扩展了上一节中的 HDFS 示例，并根据“Survivor”列和按“Age”列出的组筛选了 Spark 数据帧 `df` 中的数据 

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>将数据保存到存储并停止 Spark 会话

数据探索和准备工作完成后，将准备好的数据存储在 Azure 上的存储帐户中，以供以后使用。

在以下示例中，会将准备好的数据写回到 Azure Blob 存储，并覆盖 `training_data` 目录中的原始 `Titanic.csv` 文件。 若要写回到存储，需要具有“存储 Blob 数据参与者”权限。 [详细了解存储权限和角色](../storage/blobs/assign-azure-role-data-access.md)。

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

完成数据准备并将准备好的数据保存到存储后，请通过以下命令停止使用 Apache Spark 池。

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>创建用于表示准备好的数据的数据集

可以使用准备好的数据进行模型训练后，请使用 [Azure 机器学习数据存储](how-to-access-data.md)连接到存储，并指定要与 [Azure 机器学习数据集](how-to-create-register-datasets.md)一起使用的文件。

下面是代码示例：

* 假设你已经创建了一个数据存储，该数据存储可连接到保存了准备好的数据的存储服务。  
* 使用 get() 方法从工作区 `ws` 获取该现有数据存储 `mydatastore`。
* 创建一个 [FileDataset](how-to-create-register-datasets.md#filedataset) `train_ds`，该数据集引用位于 `mydatastore` 中 `training_data` 目录中的准备好的数据文件。  
* 创建变量 `input1`，该变量可在以后用于使 `train_ds` 数据集的数据文件可供计算目标用于训练任务。

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>使用 `ScriptRunConfig` 将试验运行提交到 Synapse Spark 池

如果数据整理任务的自动化和生产化已准备就绪，可利用 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 对象将试验运行提交到[附加的 Synapse Spark 池](how-to-link-synapse-ml-workspaces.md#attach-a-pool-with-the-python-sdk)。  

同样，如果有 Azure 机器学习管道，则可使用 [SynapseSparkStep 将 Synapse Spark 池指定为管道中数据准备步骤的计算目标](how-to-use-synapsesparkstep.md)。

使数据对 Synapse Spark 池可用，这取决于数据集类型。 

* 对于 FileDataset，可以使用 [`as_hdfs()`](/python/api/azureml-core/azureml.data.filedataset#as-hdfs--) 方法。 提交运行后，数据集将作为 Hadoop 分布式文件系统提供给 Synapse Spark 池。 
* 对于 [TabularDataset](how-to-create-register-datasets.md#tabulardataset)，可以使用 [`as_named_input()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#as-named-input-name-) 方法。 

以下代码 

* 从上一个代码示例中创建的 FileDataset `train_ds` 中创建变量 `input2`。
* 用 HDFSOutputDatasetConfiguration 类创建变量 `output`。 运行完成后，此类允许我们将运行的输入保存为数据存储 `mydatastore` 中的数据集 `test`。 在 Azure 机器学习工作区中，`test` 数据集在名称 `registered_dataset` 下进行注册。 
* 配置运行在 Synapse Spark 池上执行应使用的设置。 
* 定义 ScriptRunConfig 参数，以便 
  * 将 `dataprep.py` 用于运行。 
  * 指定用作输入的数据，以及使数据对 Synapse Spark 池可用的方法。
  * 指定存储输出数据 `output` 的位置。  

```Python
from azureml.core import Dataset, HDFSOutputDatasetConfig
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

input2 = train_ds.as_hdfs()
output = HDFSOutputDatasetConfig(destination=(datastore, "test").register_on_complete(name="registered_dataset")

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

设置 `ScriptRunConfig` 对象后，可以提交运行。

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```

有关更多信息，如本例中使用的 `dataprep.py` 脚本，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb)。

准备好数据后，可将其用作训练作业的输入。 在上述代码示例中，`registered_dataset` 被指定为训练作业的输入数据。 

## <a name="example-notebooks"></a>示例笔记本

有关 Azure Synapse Analytics 和 Azure 机器学习集成功能的更多概念和演示，请参阅示例笔记本。
* [从 Azure 机器学习工作区的笔记本中运行交互式 Spark 会话](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb)。
* [在 Synapse Spark 池作为计算目标的情况下提交 Azure 机器学习体验运行](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb)。

## <a name="next-steps"></a>后续步骤

* [训练模型](how-to-set-up-training-targets.md)。
* [使用 Azure 机器学习数据集进行训练](how-to-train-with-datasets.md)。