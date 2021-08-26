---
title: 快速入门：使用 Azure CLI 创建 Azure 数据工厂
description: 本快速入门将创建一个 Azure 数据工厂，其中包括链接服务、数据集和管道。 可以运行管道来执行文件复制操作。
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom: template-quickstart, devx-track-azurecli
ms.openlocfilehash: f12b9f785063b8fa6fec4bc8cd4011d65110b7fe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749876"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure 数据工厂

本快速入门介绍如何使用 Azure CLI 创建 Azure 数据工厂。 在此数据工厂中创建的管道会将数据从 Azure Blob 存储中的一个文件夹复制到另一个文件夹。 有关如何使用 Azure 数据工厂转换数据的信息，请参阅[在 Azure 数据工厂中转换数据](transform-data.md)。

有关 Azure 数据工厂服务的介绍，请参阅 [Azure 数据工厂简介](introduction.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> 若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于参与者或所有者角色，或者是 Azure 订阅的管理员。    有关详细信息，请参阅 [Azure 角色](quickstart-create-data-factory-powershell.md#azure-roles)。

## <a name="prepare-a-container-and-test-file"></a>准备容器和测试文件

本快速入门使用 Azure 存储帐户，其中包含一个带有文件的容器。

1. 若要创建名为 `ADFQuickStartRG` 的资源组，请使用 [az group create](/cli/azure/group#az_group_create) 命令：

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. 使用 [az storage account create](/cli/azure/storage/container#az_storage_container_create) 命令创建存储帐户：

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. 使用 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 命令创建名为 `adftutorial` 的容器：

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. 在本地目录中，创建要上传的名为 `emp.txt` 的文件。 如果你在 Azure Cloud Shell 中操作，可以使用 `echo $PWD` Bash 命令查找当前工作目录。 可以使用标准 Bash 命令（如 `cat` ）创建文件：

   ```console
   cat > emp.txt
   This is text.
   ```

   按 **Ctrl+D** 保存新文件。

1. 若要将新文件上传到 Azure 存储容器，请使用 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 命令：

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   此命令将文件上传到名为 `input` 的新文件夹。

## <a name="create-a-data-factory"></a>创建数据工厂

若要创建 Azure 数据工厂，请运行 [az datafactory factory create](/cli/azure/datafactory#az_datafactory_create) 命令：

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> 请将 `ADFTutorialFactory` 替换为全局唯一的数据工厂名称，例如 ADFTutorialFactorySP1127。

可以使用 [az datafactory factory show](/cli/azure/datafactory#az_datafactory_factory_show) 命令查看创建的数据工厂：

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>创建链接服务和数据集

接下来，创建一个链接服务和两个数据集。

1. 使用 [az storage account show-connection-string](/cli/azure/datafactory#az_datafactory_factory_show) 命令获取存储帐户的连接字符串：

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. 在工作目录中，创建包含以下内容的 JSON 文件，这些内容包括上一步骤中获取的你自己的连接字符串。 将文件命名为 `AzureStorageLinkedService.json`：

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. 使用 [az datafactory linked-service create](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create) 命令创建名为 `AzureStorageLinkedService` 的链接服务：

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. 在工作目录中，创建包含以下内容的名为 `InputDataset.json` 的 JSON 文件：

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. 使用 [az datafactory dataset create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) 命令创建名为 `InputDataset` 的输入数据集：

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. 在工作目录中，创建包含以下内容的名为 `OutputDataset.json` 的 JSON 文件：

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. 使用 [az datafactory dataset create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) 命令创建名为 `OutputDataset` 的输出数据集：

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>创建并运行管道

最后，创建并运行管道。

1. 在工作目录中，创建包含以下内容的名为 `Adfv2QuickStartPipeline.json` 的 JSON 文件：

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. 使用 [az datafactory pipeline create](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create) 命令创建名为 `Adfv2QuickStartPipeline` 的管道：

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. 使用 [az datafactory pipeline create-run](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run) 命令运行管道：

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   此命令将返回运行 ID。 请复制此 ID 以便在下一命令中使用。

1. 使用 [az datafactory pipeline-run show](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show) 命令验证管道运行是否成功：

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

也可以使用 [Azure 门户](https://portal.azure.com/)验证管道是否按预期方式运行。 有关详细信息，请参阅[查看已部署的资源](quickstart-create-data-factory-powershell.md#review-deployed-resources)。

## <a name="clean-up-resources"></a>清理资源

本快速入门中的所有资源都是同一资源组的一部分。 若要删除所有这些资源，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令：

```azurecli
az group delete --name ADFQuickStartRG
```

如果你还要将此资源组用在别处，请删除单个资源。 例如，若要删除链接服务，请使用 [az datafactory linked-service delete](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete) 命令。

在本快速入门中，你创建了以下 JSON 文件：

- AzureStorageLinkedService.json
- InputDataset.json
- OutputDataset.json
- Adfv2QuickStartPipeline.json

使用标准 Bash 命令删除这些文件。

## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂中的管道和活动](concepts-pipelines-activities.md)
- [Azure 数据工厂中的链接服务](concepts-linked-services.md)
- [Azure 数据工厂中的数据集](concepts-datasets-linked-services.md)
- [在 Azure 数据工厂中转换数据](transform-data.md)
