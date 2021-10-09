---
title: 对 Azure 上的存储服务的基于标识的数据访问
titleSuffix: Machine Learning
description: 了解如何使用基于标识的数据访问，通过 Azure 机器学习数据存储和机器学习 Python SDK 连接到 Azure 上的数据存储服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 09/28/2021
ms.custom: contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 1455402a00d8e67a75337c890d10a6c14b98eca2
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424251"
---
# <a name="connect-to-storage-by-using-identity-based-data-access"></a>通过使用基于标识的数据访问连接到存储

本文介绍如何通过 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro)，使用基于标识的数据访问和 Azure 机器学习数据存储连接到 Azure 上的存储服务。  

通常，数据存储使用基于凭据的数据访问来确认你有权访问存储服务。 它们将连接信息（例如，订阅 ID 和令牌授权）存储在与工作区关联的[密钥保管库](https://azure.microsoft.com/services/key-vault/)中。 当创建使用基于标识的数据访问的数据存储时，系统会使用你的 Azure 帐户（[Azure Active Directory 令牌](../active-directory/fundamentals/active-directory-whatis.md)）来确认是否有权访问存储服务。 在此场景下，不会保存身份验证凭据。 只会将存储帐户信息存储在数据存储中。 

若要创建使用基于凭据的身份验证（例如，访问密钥或服务主体）的数据存储，请参阅[连接到 Azure 上的存储服务](how-to-access-data.md)。

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Azure 机器学习中基于标识的数据访问

在以下两种情况下，你可以在 Azure 机器学习中应用基于标识的数据访问。 当使用机密数据且需要更精细的数据访问管理时，这些方案非常适合基于标识的访问：

- 访问存储服务
- 使用专用数据训练机器学习模型

### <a name="accessing-storage-services"></a>访问存储服务

可以使用 Azure 机器学习数据存储库或 [Azure 机器学习数据集](how-to-create-register-datasets.md)通过基于标识的数据访问连接到存储服务。 

通常，身份验证凭据保存在数据存储中，该数据存储用于确保你有权访问存储服务。 如果通过数据存储注册这些凭据，则具有工作区读取者角色的任何用户都可以检索这些凭据。 对于某些组织，这种访问规模可能会成为一个安全问题。 [详细了解工作区读取者角色](how-to-assign-roles.md#default-roles)。 

使用基于标识的数据访问时，Azure 机器学习会提示你输入用于数据访问身份验证的 Azure Active Directory 令牌，而不是将凭据保存在数据存储中。 这样，你就可以在存储级别进行数据访问管理，并让凭据保密。 

这一行为也适用以下情况：

* [直接从存储 URL 创建数据集](#use-data-in-storage)。 
* 通过本地计算机或[计算实例](concept-compute-instance.md)上的 Jupyter Notebook 以交互方式处理数据。

> [!NOTE]
> 使用基于凭据的身份验证来存储的凭据包括：订阅 ID、共享访问签名 (SAS) 令牌、存储访问密钥和服务主体信息，例如，客户端 ID 和租户 ID。

### <a name="model-training-on-private-data"></a>针对专用数据的模型训练

某些机器学习方案涉及使用专用数据训练模型。 在这种情况下，数据科学家需要在不泄露机密输入数据的前提下运行训练工作流。 在此方案中，训练计算的托管标识用于数据访问身份验证。 这样，存储管理员就可以将存储 Blob 数据读取者访问权限授予训练计算用于运行训练作业的托管标识。 不需要为各个数据科学家分别授予访问权限。 有关详细信息，请参阅[在计算群集上设置托管标识](how-to-create-attach-compute-cluster.md#managed-identity)。


## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用 [Azure 机器学习的免费版或付费版](https://azure.microsoft.com/free/)。

- 一个使用支持的存储类型的 Azure 存储帐户。 预览版支持以下存储类型： 
    - [Azure Blob 存储](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)

- [适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/install)。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)或[通过 Python SDK 使用现有工作区](how-to-manage-workspace.md#connect-to-a-workspace)。 

## <a name="storage-access-permissions"></a>存储访问权限

为确保安全地连接到 Azure 上的存储服务，Azure 机器学习会要求你具有相应数据存储的访问权限。

基于标识的数据访问仅支持与以下存储服务的连接：

* Azure Blob 存储
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL 数据库

若要访问这些存储服务，必须至少具有[存储 Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)访问权限。 只有存储帐户所有者可以[通过 Azure 门户更改访问级别](../storage/blobs/assign-azure-role-data-access.md)。

如果要在远程计算目标上训练模型，必须至少为计算标识授予存储服务的存储 Blob 数据读取者角色。 了解如何[在计算群集上设置托管标识](how-to-create-attach-compute-cluster.md#managed-identity)。

## <a name="work-with-virtual-networks"></a>使用虚拟网络

默认情况下，Azure 机器学习无法与防火墙后面或虚拟网络中的存储帐户通信。

可将存储帐户配置为仅允许从特定虚拟网络进行访问。 此配置需要执行额外的步骤来确保不会将数据泄露到网络之外。 基于凭据的数据访问同样具有此行为。 有关详细信息，请参阅[如何配置虚拟网络方案](how-to-access-data.md#virtual-network)。 

## <a name="create-and-register-datastores"></a>创建并注册数据存储

在 Azure 上将存储服务注册为数据存储时，会自动创建该数据存储并将其注册到特定的工作区。 有关所需权限类型的指导，请参阅[存储访问权限](#storage-access-permissions)。 有关如何连接到虚拟网络后面的数据存储的详细信息，请参阅[使用虚拟网络](#work-with-virtual-networks)。

在下面的代码中，请注意缺少身份验证参数，例如，`sas_token`、`account_key`、`subscription_id`，以及服务主体 `client_id`。 这种省略表示 Azure 机器学习将使用基于标识的数据访问来进行身份验证。 数据存储的创建通常在笔记本中或通过 Studio 以交互方式执行。 因此，Azure Active Directory 令牌用于数据访问身份验证。

> [!NOTE]
> 数据存储名称只能包含小写字母、数字和下划线。 

### <a name="azure-blob-container"></a>Azure blob 容器

若要将 Azure blob 容器注册为数据存储，请使用 [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

下面的代码会创建`credentialless_blob`数据存储，并将其注册到`ws`工作区，然后将其分配给变量 `blob_datastore`。 此数据存储访问 `my-account-name` 存储帐户上的 `my_container_name` blob 容器。

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

使用 [register_azure_data_lake()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) 可注册连接到 Azure Data Lake Storage Gen1 的数据存储。

下面的代码会创建`credentialless_adls1`数据存储，并将其注册到`workspace`工作区，然后将其分配给变量 `adls_dstore`。 此数据存储访问 `adls_storage` Azure Data Lake Store 帐户。

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

使用 [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 可注册连接到 Azure Data Lake Storage Gen2 的数据存储。

下面的代码会创建`credentialless_adls2`数据存储，并将其注册到`ws`工作区，然后将其分配给变量 `adls2_dstore`。 此数据存储访问 `myadls2` 存储帐户中的文件系统 `tabular`。  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>使用存储中的数据

通过 Azure 机器学习与存储中的数据进行交互时，建议使用 [Azure 机器学习数据集](how-to-create-register-datasets.md)。 

数据集可将数据打包成一个延迟计算的对象，可供机器学习任务（例如，训练）使用。 此外，利用数据集可从 Azure 存储服务（例如，Azure Blob 存储和 Azure Data Lake Storage）将任何格式的文件[下载或装载](how-to-train-with-datasets.md#mount-vs-download)到计算目标。


若要创建具有基于标识的数据访问权限的数据集，可以使用以下选项。 这种类型的数据集创建会使用 Azure Active Directory 令牌来进行数据访问身份验证。 

*  来自数据存储的引用路径，这些存储也使用基于标识的数据访问。 
<br>在下面的示例中，`blob_datastore` 已存在且使用基于标识的数据访问。   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* 跳过数据存储创建并直接从存储 URL 创建数据集。 目前，此功能仅支持 Azure Blob 以及 Azure Data Lake Storage Gen1 和 Gen2。

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

当提交训练作业时，如果训练作业使用基于标识的数据访问创建的数据集，则该训练计算的托管标识将用于数据访问身份验证。 不会使用 Azure Active Directory 令牌。 对于此方案，请确保至少为计算的托管标识授予存储服务的存储 Blob 数据读取者角色。 有关详细信息，请参阅[在计算群集上设置托管标识](how-to-create-attach-compute-cluster.md#managed-identity)。 

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [使用数据集进行训练](how-to-train-with-datasets.md)
* [使用基于密钥的数据访问创建数据存储](how-to-access-data.md)