---
title: 基于标识的数据访问 Azure 上的存储服务
titleSuffix: Azure Machine Learning
description: 了解如何使用基于标识的数据访问来连接到 Azure 上的存储服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: dbfb4ea729b8360c7065d75cb3efbaf42b82c0da
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662045"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>使用基于标识的数据访问 (预览连接到存储) 

>[!IMPORTANT]
> 本文中介绍的功能处于预览状态，应将其视为 [实验](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) 预览功能，这些功能可能会随时更改。

本文介绍如何使用基于标识的数据访问来连接到 Azure 上的存储服务，以及如何通过 [Azure 机器学习 PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)Azure 机器学习数据存储。  

通常，数据存储使用基于凭据的数据访问来确认您有权访问存储服务。 它们在与工作区关联的 [Key Vault](https://azure.microsoft.com/services/key-vault/) 中保留连接信息，如订阅 ID 和令牌授权。 当你创建使用基于标识的数据访问的数据存储时，你的 Azure 登录 ([Azure Active Directory 令牌](../active-directory/fundamentals/active-directory-whatis.md)) 用于确认你有权访问存储服务。 在此方案中，不会保存任何身份验证凭据，并且只有存储帐户信息存储在数据存储中。 

若要创建使用基于凭据的身份验证（如使用访问密钥或服务主体）的数据存储，请参阅 [连接到 Azure 上的存储服务](how-to-access-data.md)。

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Azure 机器学习中基于标识的数据访问

在 Azure 机器学习中，有两个区域可应用基于标识的数据访问。 尤其是在使用机密数据并需要更精细的数据访问管理时。 

1. 访问存储服务。
1. 用专用数据训练机器学习模型。

### <a name="accessing-storage-services"></a>访问存储服务

可以通过基于标识的数据访问，通过 Azure 机器学习数据存储或 [Azure 机器学习数据集](how-to-create-register-datasets.md)连接到存储服务。 

通常，身份验证凭据保存在数据存储中，用于确保你有权访问存储服务。 如果向数据存储注册了这些凭据，则具有工作区 *读者* 角色的任何用户都可以检索它们，这对于某些组织来说可能是一个安全问题。 [了解有关工作区 *读者* 角色的详细信息](how-to-assign-roles.md#default-roles)。 

使用基于标识的数据访问时，Azure 机器学习会提示你输入用于数据访问身份验证的 Azure Active Directory 令牌，而不是在数据存储中保留凭据。 这允许在存储级别进行数据访问管理，并使凭据保密。 

同样的行为适用于你，

* [直接从存储 Url 创建数据集](#use-data-in-storage)。 
* 在本地计算机或 [计算实例](concept-compute-instance.md)上通过 Jupyter 笔记本以交互方式处理数据。

> [!NOTE]
> 使用基于凭据的身份验证存储的凭据包括：订阅 ID、共享访问签名 (SAS) 令牌、存储访问密钥和服务主体信息，如、客户端 ID 和租户 ID。

### <a name="model-training-on-private-data"></a>专用数据的模型定型

某些机器学习方案涉及专用数据定型模型。 在这种情况下，数据科学家需要运行定型工作流，而不会泄露机密输入数据。 在此方案中，定型计算的托管标识用于数据访问身份验证。 这样，存储管理员就可以授予 **存储 Blob 数据读取器** 访问定型计算用于运行定型作业（而不是单个数据科学家）的托管标识。 了解如何 [在计算上设置托管标识](how-to-create-attach-compute-cluster.md#managed-identity)。


## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)。

- 一个使用支持的存储类型的 Azure 存储帐户。 预览版支持以下存储类型。 
    - [Azure Blob 存储](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake 第1代](../data-lake-store/index.yml)
    - [Azure Data Lake 第2代](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)

- [适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md) [，或者通过 Python SDK 使用现有](how-to-manage-workspace.md#connect-to-a-workspace)工作区。 

## <a name="storage-access-permissions"></a>存储访问权限

若要确保安全连接到 Azure 上的存储服务，Azure 机器学习要求你有权访问相应的数据存储。

基于标识的数据访问仅支持到以下存储服务的连接：

* Azure Blob 存储
* Azure Data Lake 第1代
* Azure Data Lake 第2代
* Azure SQL 数据库

若要访问这些存储服务，你至少必须具有 **存储 Blob 数据读取器** 访问权限。 详细了解[存储 Blob 数据读取器](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)。 只有存储帐户所有者可以 [通过 Azure 门户更改访问级别](../storage/common/storage-auth-aad-rbac-portal.md)。

如果要在远程计算目标上定型模型，则必须至少为计算标识授予存储服务的 **存储 Blob 数据读取** 者角色。 了解如何 [在计算中设置托管标识](how-to-create-attach-compute-cluster.md#managed-identity)。

## <a name="work-with-virtual-networks"></a>使用虚拟网络

默认情况下，Azure 机器学习无法与位于防火墙后面或虚拟网络中的存储帐户通信。

可以将存储帐户配置为仅允许来自特定虚拟网络的访问，这需要额外的配置以确保数据不会泄露到网络之外。 这对于基于凭据的数据访问是相同的行为，请参阅 [所需的配置以及如何将其应用于虚拟网络方案](how-to-access-data.md#virtual-network)。 

## <a name="create-and-register-datastores"></a>创建并注册数据存储

在 Azure 上将存储服务注册为数据存储时，会自动创建数据存储并将其注册到特定的工作区。 查看以下部分：有关所需权限类型指导的 [存储访问权限](#storage-access-permissions) ，并 [使用虚拟网络](#work-with-virtual-networks) ，以了解有关如何连接到虚拟网络后面的数据存储的详细信息。

在下面的代码中，请注意缺少身份验证参数，如 `sas_token` 、 `account_key` 、 `subscription_id` 或服务主体 `client_id` 。 这会省略，指示 Azure 机器学习使用基于标识的数据访问进行身份验证。 由于创建数据存储通常在笔记本中或通过工作室发生交互，因此，Azure Active Directory 令牌用于数据访问身份验证。

> [!NOTE]
> 数据存储名称应仅包含小写字母、数字和下划线。 

### <a name="azure-blob-container"></a>Azure blob 容器

若要将 Azure blob 容器注册为数据存储，请使用 [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

下面的代码创建 `credentialless_blob` 数据存储并将其注册到 `ws` 工作区，并将其分配给变量 `blob_datastore` 。 此数据存储访问 `my_container_name` 存储帐户上的 blob 容器 `my-account-name` 。

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage 第1代

对于 Azure Data Lake Storage 第1代 (ADLS 第1代) 数据存储，请使用 [register_azure_data_lake () ](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) 来注册连接到 Azure DataLake 第1代存储的数据存储。

下面的代码创建 `credentialless_adls1` 数据存储并将其注册到 `workspace` 工作区，并将其分配给变量 `adls_dstore` 。 此数据存储访问 `adls_storage` Azure Data Lake Store 存储帐户。

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen2

对于 Azure Data Lake Storage 第2代 (ADLS 第2代) 数据存储，请使用 [register_azure_data_lake_gen2 () ](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 来注册连接到 Azure DataLake Gen 2 存储的数据存储。

下面的代码创建 `credentialless_adls2` 数据存储并将其注册到 `ws` 工作区，并将其分配给变量 `adls2_dstore` 。 此数据存储访问 `tabular` 存储帐户中的文件系统 `myadls2` 。  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>使用存储中的数据

[Azure 机器学习数据集](how-to-create-register-datasets.md) 是使用 Azure 机器学习与存储中的数据进行交互的建议方法。 

数据集可将数据打包成一个延迟计算的可供机器学习任务（例如训练）使用的对象。 此外，使用数据集，你可以从 Azure 存储服务（如 Azure Blob 存储和 Azure 数据 Lake）将任何格式的文件 [下载或装载](how-to-train-with-datasets.md#mount-vs-download) 到计算目标。


**若要创建具有基于标识的数据访问的数据集**，可以使用以下选项。 这种类型的数据集创建使用 Azure Active Directory 令牌进行数据访问身份验证。 

*  数据存储中还使用基于标识的数据访问的引用路径。 
<br>在下面的示例中， `blob_datastore` 先前是使用基于标识的数据访问创建的。   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* 跳过数据存储创建并直接从存储 url 创建数据集。 目前，此功能仅支持 Azure Blob 和第1代和第2代 Azure Data Lake Storage。

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**但是，当您提交使用通过基于标识的数据访问创建的数据集时**，定型计算的托管标识将用于数据访问身份验证，而不是用于 Azure Active Directory 令牌。 对于这种情况，请确保至少为计算的托管标识授予存储服务的 **存储 Blob 数据读取** 者角色。 了解如何 [在计算中设置托管标识](how-to-create-attach-compute-cluster.md#managed-identity)。 

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。
* [训练数据集](how-to-train-with-datasets.md)。
* [使用基于密钥的数据访问创建数据存储](how-to-access-data.md)。
