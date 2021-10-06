---
title: 连接到 Azure 上的存储服务
titleSuffix: Azure Machine Learning
description: 了解如何在使用 Azure 机器学习训练期间使用数据存储安全地连接到 Azure 存储服务
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 07/06/2021
ms.custom: contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 7a009cdebf686a79679b7987b3a9071ae3c9421c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427811"
---
# <a name="connect-to-storage-services-on-azure"></a>连接到 Azure 上的存储服务

在本文中，了解如何使用 Azure 机器学习数据存储和 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro) 连接到 Azure 上的数据存储服务。

数据存储可安全地连接到 Azure 上的存储服务，而不会损害你的身份验证凭据以及原始数据源的完整性。 它们将连接信息（如订阅 ID 和令牌授权）存储在与工作区关联的 [Key Vault](https://azure.microsoft.com/services/key-vault/) 中，这样你就可以安全地访问存储，而无需在脚本中对其进行硬编码。 可以创建连接到[这些 Azure 存储解决方案](#matrix)的数据存储。

若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据存储，请参阅[安全地访问数据](concept-data.md#data-workflow)一文。

有关低代码体验，请参阅如何使用 [Azure 机器学习工作室创建和注册数据存储](how-to-connect-data-ui.md#create-datastores)。

>[!TIP]
> 本文假设你想要使用基于凭据的身份验证凭据（如服务主体或共享访问签名 (SAS) 令牌）连接到存储服务。 请记住，如果凭据已注册到数据存储中，所有具有工作区读者角色的用户都能够检索这些凭据。 [了解有关工作区读者角色的详细信息。](how-to-assign-roles.md#default-roles) <br><br>如果这是个问题，请了解如何[使用基于标识的访问连接到存储服务](how-to-identity-based-data-access.md)。 <br><br>此功能是一个[试验性](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，可能会随时更改。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

- 一个使用[支持的存储类型](#matrix)的 Azure 存储帐户。

- [适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro)。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)或通过 Python SDK 使用现有工作区。 

    导入 `Workspace` 和 `Datastore` 类，并使用函数 `from_config()` 从文件 `config.json` 中加载订阅信息。 默认情况下，这会查找当前目录中的 JSON 文件，但你也可以使用 `from_config(path="your/file/path")` 指定一个路径参数，使之指向该文件。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    创建工作区时，会将 Azure Blob 容器和 Azure 文件共享作为数据存储自动注册到工作区。 它们分别命名为 `workspaceblobstore` 和 `workspacefilestore`。 `workspaceblobstore` 用于存储工作区项目和机器学习试验日志。 它也已设为 **默认数据存储**，无法从工作区中删除。 `workspacefilestore` 用于存储通过[计算实例](./concept-compute-instance.md#accessing-files)授权的笔记本和 R 脚本。
    
    > [!NOTE]
    > Azure 机器学习设计器会在你打开设计器主页中的示例时自动创建一个名为 azureml_globaldatasets 的数据存储。 此数据存储仅包含示例数据集。 请不要将此数据存储用于任何机密数据访问。

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>支持的数据存储服务类型

数据存储目前支持将连接信息存储到下表中列出的存储服务。 

> [!TIP]
> 对于不支持的存储解决方案，为了在 ML 试验期间节省数据出口成本，请[将数据移到](#move)支持的 Azure 存储解决方案。 

| 存储类型 | 身份验证类型 | [Azure 机器学习工作室](https://ml.azure.com/) | [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro) |  [Azure 机器学习 CLI](reference-azure-machine-learning-cli.md) | [Azure 机器学习 Rest API](/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;存储](../storage/blobs/storage-blobs-overview.md)| 帐户密钥 <br> SAS 令牌 | ✓ | ✓ | ✓ |✓ |✓
[Azure 文件共享](../storage/files/storage-files-introduction.md)| 帐户密钥 <br> SAS 令牌 | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](../data-lake-store/index.yml)| 服务主体| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](../storage/blobs/data-lake-storage-introduction.md)| 服务主体| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;SQL&nbsp;数据库](../azure-sql/database/sql-database-paas-overview.md)| SQL 身份验证 <br>服务主体| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](../postgresql/overview.md) | SQL 身份验证| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Database&nbsp;for&nbsp;MySQL](../mysql/overview.md) | SQL 身份验证|  | ✓* | ✓* |✓*|
[Databricks&nbsp;文件系统](/azure/databricks/data/databricks-file-system)| 无身份验证 | | ✓** | ✓ ** |✓** |

\* 仅管道 [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) 支持 MySQL<br />
\*\* 仅管道 [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep) 支持 Databricks


### <a name="storage-guidance"></a>存储指导原则

建议为 [Azure Blob 容器](../storage/blobs/storage-blobs-introduction.md)创建数据存储。 标准和高级存储都可用于 Blob。 尽管高级存储费用更高，但其吞吐速度也更快，可加速训练运行，特别是在针对大型数据集进行训练时。 要了解存储帐户的成本，请参阅 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 基于 Azure Blob 存储而构建，专为企业大数据分析设计。 Data Lake Storage Gen2 的一个基本部分是向 Blob 存储添加[分层命名空间](../storage/blobs/data-lake-storage-namespace.md)。 分层命名空间将对象/文件组织到目录层次结构中，以便进行有效的数据访问。

## <a name="storage-access-and-permissions"></a>存储访问和权限

为了确保安全连接到 Azure 存储服务，Azure 机器学习会要求你具有相应数据存储容器的访问权限。 此访问权限依赖用于注册数据存储的身份验证凭据。 

> [!NOTE]
> 本指南也适用于[使用基于标识的数据访问创建的数据存储（预览版）](how-to-identity-based-data-access.md)。 

### <a name="virtual-network"></a>虚拟网络 

Azure 机器学习需要额外的配置步骤才能与受防火墙保护或虚拟网络中的存储帐户通信。 如果你的存储帐户受防火墙保护，则可[允许通过 Azure 门户列出 IP 地址](../storage/common/storage-network-security.md#managing-ip-network-rules)。

Azure 机器学习可以从虚拟网络外部的客户端接收请求。 为确保从服务请求数据的实体是安全的，请[对工作区使用专用终结点](how-to-configure-private-link.md)。

**对于 Python SDK 用户**，若要通过计算目标上的训练脚本访问数据，计算目标需要位于存储的同一虚拟网络和子网中。 

**对于 Azure 机器学习工作室用户**，有几个功能依赖于从数据集读取数据的能力；例如数据集预览、概要文件和自动化机器学习。 若要使这些功能与虚拟网络后面的存储一起工作，请使用[工作室中的工作区托管标识](how-to-enable-studio-virtual-network.md)，以允许 Azure 机器学习从虚拟网络外部访问存储帐户。 

> [!NOTE]
> 如果数据存储是虚拟网络后面的 Azure SQL 数据库，请确保通过 [Azure 门户](https://ms.portal.azure.com/)将“拒绝公共访问”设置为“否”，以允许 Azure 机器学习访问存储帐户。

### <a name="access-validation"></a>访问验证

在初始的数据存储创建和注册过程中，Azure 机器学习会自动验证基础存储服务是否存在，以及用户提供的主体（用户名、服务主体或 SAS 令牌）是否有权访问指定的存储。

创建数据存储后，此验证只针对要求访问基础存储容器的方法执行，而不是每次检索数据存储对象时都执行 。 例如，如果要从数据存储中下载文件，则会进行验证，但如果只想更改默认数据存储，则不会进行验证。

若要在访问基础存储服务时进行身份验证，可在要创建的数据存储类型的相应 `register_azure_*()` 方法中提供帐户密钥、共享访问签名 (SAS) 令牌或服务主体。 [存储类型矩阵](#matrix)列出了与各种数据存储类型对应的受支持的身份验证类型。

可在 [Azure 门户](https://portal.azure.com)上查找帐户密钥、SAS 令牌和服务主体信息。

* 如果计划使用帐户密钥或 SAS 令牌进行身份验证，请在左窗格中选择“存储帐户”，然后选择要注册的存储帐户。 
  * “概述”页面提供了帐户名称、容器和文件共享名称等信息。 
      1. 对于帐户密钥，请转到“设置”窗格中的“访问密钥” 。 
      1. 对于 SAS 令牌，请转到“设置”窗格中的“共享访问签名” 。

* 如果计划使用服务主体进行身份验证，请转到“应用注册”，然后选择要使用的应用。 
    * 其对应的“概览”页面将包含租户 ID 和客户端 ID 之类的必需信息。

> [!IMPORTANT]
> * 如果需要更改 Azure 存储帐户的访问密钥（帐户密钥或 SAS 令牌），请确保将新凭据与工作区以及与其连接的数据存储同步。 了解如何[同步更新的凭据](how-to-change-storage-access-key.md)。 
### <a name="permissions"></a>权限

对于 Azure Blob 容器和 Azure Data Lake Gen2 存储，请确保身份验证凭据具有“存储 Blob 数据读取者”访问权限。 详细了解[存储 Blob 数据读取器](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)。 帐户 SAS 令牌默认为无权限。 
* 如需进行数据读取访问，你的身份验证凭据必须至少具有容器和对象的“列出”和“读取”权限。 

* 若需进行数据写入访问，还需要“写入”和“添加”权限。

<a name="python"></a>

## <a name="create-and-register-datastores"></a>创建并注册数据存储

将 Azure 存储解决方案注册为数据存储时，会自动创建数据存储并将其注册到特定的工作区。 请查看[存储访问和权限](#storage-access-and-permissions)部分，以获取有关虚拟网络方案以及在何处查找所需身份验证凭据的指南。 

本部分中的示例演示了如何通过 Python SDK 为以下存储类型创建和注册数据存储。 这些示例中提供的参数是创建和注册数据存储所必需的。

* [Azure Blob 容器](#azure-blob-container)
* [Azure 文件共享](#azure-file-share)
* [Azure Data Lake Storage Gen2](#azure-data-lake-storage-generation-2)

 若要为其他受支持的存储服务创建数据存储，请参阅[适用的 `register_azure_*` 方法的参考文档](/python/api/azureml-core/azureml.core.datastore.datastore#methods)。

如果你更喜欢低代码体验，请参阅[使用 Azure 机器学习工作室连接到数据](how-to-connect-data-ui.md)。
>[!IMPORTANT]
> 如果你注销了一个数据存储并重新注册一个同名数据存储，但注册失败，则表示工作区的 Azure Key Vault 可能未启用软删除。 默认情况下，将为工作区创建的密钥保管库实例启用软删除，但如果使用的是现有密钥保管库或是在 2020 年 10 月之前创建的工作区，则可能无法启用软删除。 有关如何启用软删除的信息，请参阅[对现有的密钥保管库启用软删除](../key-vault/general/soft-delete-change.md#turn-on-soft-delete-for-an-existing-key-vault)。


> [!NOTE]
> 数据存储名称应仅包含小写字母、数字和下划线。 

### <a name="azure-blob-container"></a>Azure blob 容器

若要将 Azure blob 容器注册为数据存储，请使用 [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

以下代码会创建 `blob_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的帐户访问密钥访问 `my-account-name` 存储帐户上的 `my-container-name` Blob 容器。 请查看[存储访问和权限](#storage-access-and-permissions)部分，以获取有关虚拟网络方案以及在何处查找所需身份验证凭据的指南。 

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

### <a name="azure-file-share"></a>Azure 文件共享

若要将 Azure 文件共享注册为数据存储，请使用 [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

以下代码会创建 `file_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的帐户访问密钥访问 `my-account-name` 存储帐户上的 `my-fileshare-name` 文件共享。 请查看[存储访问和权限](#storage-access-and-permissions)部分，以获取有关虚拟网络方案以及在何处查找所需身份验证凭据的指南。 

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen2

对于 Azure Data Lake Storage Gen2 (ADLS Gen 2) 数据存储，请使用 [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 通过[服务主体权限](../active-directory/develop/howto-create-service-principal-portal.md)注册连接到 Azure DataLake Gen 2 存储的凭据数据存储。  

若要使用服务主体，需要[注册应用程序](../active-directory/develop/app-objects-and-service-principals.md)，并通过 Azure 基于角色的访问控制 (Azure RBAC) 或访问控制列表 (ACL) 向服务主体授予数据访问权限。 详细了解 [ADLS Gen2 的访问控制设置](../storage/blobs/data-lake-storage-access-control-model.md)。 

以下代码会创建 `adlsgen2_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的服务主体凭据访问 `account_name` 存储帐户中的文件系统 `test`。 请查看[存储访问和权限](#storage-access-and-permissions)部分，以获取有关虚拟网络方案以及在何处查找所需身份验证凭据的指南。 

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```



## <a name="create-datastores-with-other-azure-tools"></a>使用其他 Azure 工具创建数据存储
除了使用 Python SDK 和工作室创建数据存储外，还可以使用 Azure 资源管理器模板或 Azure 机器学习 VS Code 扩展。 

<a name="arm"></a>
### <a name="azure-resource-manager"></a>Azure 资源管理器

[https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices) 上有许多模板可用于创建数据存储。

若要了解如何使用这些模板，请参阅[使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。

### <a name="vs-code-extension"></a>VS Code 扩展

如果更喜欢使用 Azure 机器学习 VS Code 扩展来创建和管理数据存储，请访问 [VS Code 资源管理操作指南](how-to-manage-resources-vscode.md#datastores)以了解详细信息。
<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>使用数据存储中的数据

创建数据存储后，请[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)，以便与数据进行交互。 数据集可将数据打包成一个延迟计算的可供机器学习任务（例如训练）使用的对象。 

使用数据集，你可以从 Azure 存储服务[下载或装载](how-to-train-with-datasets.md#mount-vs-download)任何格式的文件，以便在计算目标上进行模型训练。 [详细了解如何使用数据集训练 ML 模型](how-to-train-with-datasets.md)。

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>从工作区获取数据存储

若要获取在当前工作区中注册的特定数据存储，请在 `Datastore` 类上使用 [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29#get-workspace--datastore-name-) 静态方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要获取在给定工作区中注册的数据存储的列表，可对工作区对象使用 [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29#datastores) 属性：

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

若要获取工作区的默认数据存储，请使用此行：

```Python
datastore = ws.get_default_datastore()
```
还可通过以下代码更改默认数据存储。 仅支持通过 SDK 使用此功能。 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>在评分过程中访问数据

Azure 机器学习提供多种方法来使用模型进行评分。 其中一些方法不提供对数据存储的访问权限。 使用下表了解允许在评分期间访问数据存储的方法：

| 方法 | 数据存储访问 | 说明 |
| ----- | :-----: | ----- |
| [批量预测](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | 以异步方式对大量数据进行预测。 |
| [Web 服务](how-to-deploy-and-where.md) | &nbsp; | 将模型部署为 Web 服务。 |

对于 SDK 不提供对数据存储的访问权限的情况，也许可以通过使用相关 Azure SDK 访问数据以创建自定义代码。 例如，[适用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python) 是可用于访问 Blob 或文件中存储的数据的客户端库。

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>将数据移到支持的 Azure 存储解决方案

Azure 机器学习支持从 Azure Blob 存储、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 访问数据。 如果你正在使用不受支持的存储，则建议使用 [Azure 数据工厂和这些步骤](../data-factory/quickstart-create-data-factory-copy-data-tool.md)将数据移动到受支持的 Azure 存储解决方案。 将数据移动到受支持的存储可帮助你在机器学习试验期间节省数据传出成本。 

Azure 数据工厂具有超过 80 个预生成的连接器，可提供高效且可复原的数据传输，无需额外付费。 这些连接器包括 Azure 数据服务、本地数据源、Amazon S3 和 Redshift，以及 Google BigQuery。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [定型模型](how-to-set-up-training-targets.md)
* [部署模型](how-to-deploy-and-where.md)