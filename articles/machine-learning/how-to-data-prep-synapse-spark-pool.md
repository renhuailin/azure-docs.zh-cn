---
title: 'Apache Spark 池 (预览的数据准备) '
titleSuffix: Azure Machine Learning
description: 了解如何附加 Apache Spark 池以便通过 Azure Synapse Analytics 进行数据准备和 Azure 机器学习
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 22945cdaff2696a15d5b119bd0f32fd0a179ebf7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202087"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-preparation-preview"></a>将 Apache Spark 池附加 (Azure Synapse Analytics) 提供支持的数据准备 (预览) 

本文介绍如何附加和启动由 [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) 提供支持的用于数据准备的 Apache Spark 池。 

>[!IMPORTANT]
> Azure 机器学习和 Azure Synapse Analytics 集成处于预览阶段。 本文中介绍的功能采用包， `azureml-synapse` 其中包含 [实验](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) 预览功能，这些功能随时可能会更改。

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure 机器学习和 Azure Synapse Analytics integration (预览版) 

使用 Azure Synapse Analytics 与 Azure 机器学习 (预览版) ，可以附加由 Azure Synapse 支持的 Apache Spark 池，以实现交互式数据浏览和准备。 通过这种集成，你可以针对大规模的数据准备进行专用计算，这一切都在用于训练机器学习模型的同一 Python 笔记本中进行。

## <a name="prerequisites"></a>先决条件

* [创建 Azure 机器学习工作区](how-to-manage-workspace.md?tabs=python)。

* [在 Azure 门户中创建 Azure Synapse Analytics 工作区](../synapse-analytics/quickstart-create-workspace.md)。

* [使用 Azure 门户、web 工具或 Synapse Studio 创建 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [安装](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) 包括 `azureml-synapse` 包 (预览版) Azure 机器学习 Python SDK。 
    * 您也可以自行安装，但它仅与 SDK 版本1.20 或更高版本兼容。 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-analytics-assets"></a>链接机器学习工作区和 Synapse 分析资产

在附加用于数据准备的 Apache Synapse Spark 池之前，Azure 机器学习工作区必须与 Azure Synapse Analytics 工作区链接。 

可以通过 [PYTHON SDK](#link-sdk) 或 [Azure 机器学习 studio](#link-studio)链接机器学习工作区和 Synapse 分析工作区。 

> [!IMPORTANT]
> 若要成功链接到 Azure Synapse Analytics 工作区，你必须被授予 Azure Synapse Analytics 工作区的 **所有者** 角色。 查看 [Azure 门户](https://ms.portal.azure.com/)中的访问权限。
>
> 如果你不是 Azure Synapse Analytics 工作区的 **所有者** ，但想要使用现有的链接服务，请参阅 [获取现有链接服务](#get-an-existing-linked-service)。


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>通过 Python SDK 链接工作区

下面的代码将 [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) 和 [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) 类用于， 

* 将 Azure 机器学习工作区链接 `ws` 到 Azure Synapse Analytics 工作区。 
* 将 Azure 机器学习的 Azure Synapse Analytics 工作区注册为链接服务。

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> 为 `system_assigned_identity_principal_id` 每个链接服务创建一个托管标识。 在开始 Apache Spark 会话之前，必须向此托管标识授予 Azure Synapse Analytics 工作区的 **Synapse Apache Spark 管理员** 角色。 [将 Synapse Apache Spark 管理员角色分配给 Synapse Studio 中的托管标识](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)。
>
> 若要查找 `system_assigned_identity_principal_id` 特定链接服务的，请使用 `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` 。

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>通过 studio 链接工作区

通过以下步骤将 Azure 机器学习工作区和 Azure Synapse 分析工作区链接到 Azure 机器学习 studio： 

1. 登录到 [Azure 机器学习 studio](https://ml.azure.com/)。
1. 在左侧窗格的 "**管理**" 部分中选择 "**链接服务**"。
1. 选择 " **添加集成**"。
1. 在 " **链接工作区** " 窗体中填充字段

   |字段| 说明    
   |---|---
   |名称| 提供链接服务的名称。 此名称将用于引用此特定链接服务。
   |订阅名称 | 选择与你的机器学习工作区关联的订阅的名称。 
   |Synapse 工作区 | 选择要链接到的 "Synapse" 工作区。 
   
1. 选择 " **下一步** " 以打开 " **选择 Spark 池 (可选)** 窗体。 在此窗体中，选择要附加到工作区 Apache Spark 池的 Synapse

1. 选择 " **下一步** " 以打开 **审阅** 窗体并检查你的选择。 
1. 选择 " **创建** " 以完成链接服务的创建过程。

## <a name="get-an-existing-linked-service"></a>获取现有链接服务

若要检索和使用现有链接服务，需要具有 Azure Synapse Analytics 工作区的 " **用户" 或 "参与者** " 权限。

此示例 `synapselink1` 使用方法从工作区检索现有链接服务 `ws` [`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-) 。
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>管理链接服务

若要取消链接工作区，请使用 `unregister()` 方法

``` python
linked_service.unregister()
```

查看与机器学习工作区关联的所有链接服务。 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>将 Synapse Spark 池附加为计算

链接工作区后，将 Synapse Apache Spark 池附加为你的数据准备任务的专用计算资源。 

可以通过附加 Apache Spark 池。
* Azure 机器学习工作室
* [Azure 资源管理器 (ARM) 模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 

按照以下步骤使用工作室附加 Apache Spark 池。 

1. 登录到 [Azure 机器学习 studio](https://ml.azure.com/)。
1. 在左侧窗格的 "**管理**" 部分中选择 "**链接服务**"。
1. 选择 Synapse 工作区。
1. 在左上角选择 " **附加的 Spark 池** "。 
1. 选择“附加”。 
1. 从列表中选择 Apache Spark 池，并提供名称。  
    1. 此列表标识可附加到计算的可用 Synapse Spark 池。 
    1. 若要创建新的 Synapse Spark 池，请参阅 [使用 Synapse Studio 创建 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. 选择 " **附加选定** 的"。 


你还可以使用 **PYTHON SDK** 附加 Apache Spark 池。 

以下代码 
1. 将 SynapseCompute 配置为，

   1. 在 `linked_service` 上一步中创建或检索到的 LinkedService。 
   1. 要附加的计算目标的类型， `SynapseSpark`
   1. Apache Spark 池的名称。 这必须与 Azure Synapse Analytics 工作区中的现有 Apache Spark 池匹配。
   
1. 通过传入来创建机器学习 ComputeTarget， 
   1. 要使用的机器学习工作区， `ws`
   1. 要在 Azure 机器学习工作区中引用计算的名称。 
   1. 配置 Synapse 计算时指定的 attach_configuration。
       1. 对 ComputeTarget () 的调用是异步的，因此在调用完成之前，示例将会阻塞。

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

验证是否已附加 Apache Spark 池。

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>为数据准备任务启动 Synapse Spark 池

你可以指定要在 Apache Spark 会话期间使用的 [Azure 机器学习环境](concept-environments.md) 。 只有在环境中指定的 Conda 依赖关系才会生效。 不支持 Docker 映像。

>[!WARNING]
>  Apache Spark 池中不支持在环境 Conda 依赖项中指定的 Python 依赖项。 目前仅支持固定 Python 版本。 通过在脚本中加入来检查 Python 版本  `sys.version_info` 。

下面的代码创建环境，该环境在 `myenv` `azureml-core` 会话开始之前安装版本1.20.0 和 `numpy` 版本1.17.0。 然后，你可以将此环境包含在 Apache Spark session `start` 语句中。

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

若要开始使用 Apache Spark Spark 池进行数据准备，请指定 Apache Spark 池名称，并提供订阅 ID、机器学习工作区资源组、机器学习工作区的名称，以及 Apache Spark 会话期间要使用的环境。 

> [!IMPORTANT]
> 若要继续使用 Apache Spark 池，你必须指定要将哪些计算资源用于 `%synapse` 单个代码行和多行的数据准备任务 `%%synapse` 。 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

会话启动后，可以检查会话的元数据。

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>从存储加载数据

Apache Spark 会话启动后，请阅读要准备的数据。 Azure Blob 存储支持数据加载，Azure Data Lake Storage 第1代和第2代。

可以通过两种方法从这些存储服务加载数据： 

* 使用 Hadoop 分布式文件系统 (HDFS) 路径直接从存储中加载数据。

* 读入现有 [Azure 机器学习数据集中](how-to-create-register-datasets.md)的数据。

若要访问这些存储服务，需要 **存储 Blob 数据读取器** 权限。 如果打算将数据写回这些存储服务，则需要 **存储 Blob 数据参与者** 权限。 [详细了解存储权限和角色](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)。

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>用 Hadoop 分布式文件系统加载数据 (HDFS) 路径

若要从具有相应 HDFS 路径的存储中加载和读取数据，需要使数据访问身份验证凭据随时可用。 根据您的存储类型，这些凭据会有所不同。  

以下代码演示了如何使用共享访问签名 (SAS) 令牌或访问密钥，将数据从 **Azure Blob 存储** 读入 Spark 数据帧。 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

下面的代码演示如何从 **Azure Data Lake Storage 第1代 (ADLS 第1代)** 将数据读入服务主体凭据。 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

下面的代码演示了如何从 **Azure Data Lake Storage 第2代 (ADLS 第2代)** 将数据读入服务主体凭据。 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>读取已注册数据集中的数据

你还可以获取工作区中的现有已注册数据集，并通过将其转换为 spark 数据帧来对其执行数据准备。  

下面的示例获取一个已注册的 TabularDataset， `blob_dset` 它引用 blob 存储中的文件，并将其转换为 spark 数据帧。 将数据集转换为 spark 数据帧时，可以利用 `pyspark` 数据浏览和准备库。  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-preparation-tasks"></a>执行数据准备任务

检索并浏览数据后，可以执行数据准备任务。

下面的代码扩展了上一节中的 HDFS 示例，并 `df` 根据 **存活** 列和按 **Age** 列出的组对 spark 数据帧中的数据进行筛选

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>将数据保存到存储和停止 spark 会话

数据浏览和准备完成后，请将已准备的数据存储在 Azure 上的存储帐户中供以后使用。

在下面的示例中，准备好的数据写回 Azure Blob 存储，并覆盖 `Titanic.csv` 目录中的原始文件 `training_data` 。 若要写回到存储，需要 **存储 Blob 数据参与者** 权限。 [详细了解存储权限和角色](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)。

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

完成数据准备并将准备好的数据保存到存储后，请使用以下命令停止使用 Apache Spark 池。

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>创建数据集以表示已准备的数据

准备好使用准备好的数据进行模型定型时，请使用 [Azure 机器学习数据](how-to-access-data.md)存储连接到存储，并指定要用于 [Azure 机器学习数据集](how-to-create-register-datasets.md) (s) 。

下面的代码示例

* 假设你已创建了一个用于连接到存储服务的数据存储，该存储空间保存已准备的数据。  
* 从工作区获取现有的数据存储， `mydatastore` `ws` 并获取 () 方法。
* 创建一个 [FileDataset](how-to-create-register-datasets.md#filedataset)， `train_ds` 它引用位于中的目录中的准备好的数据文件 `training_data` `mydatastore` 。  
* 创建变量，该变量 `input1` 可在以后用于使数据集的数据文件 `train_ds` 可供计算目标使用。

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="next-steps"></a>后续步骤

* [为模型定型](how-to-set-up-training-targets.md)。
* [Azure 机器学习数据集定型](how-to-train-with-datasets.md)
* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。