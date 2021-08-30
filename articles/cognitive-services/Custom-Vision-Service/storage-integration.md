---
title: 集成用于通知和模型备份的 Azure 存储
titleSuffix: Azure Cognitive Services
description: 了解如何集成 Azure 存储，以便在训练或导出自定义视觉模型时接收推送通知。 还可以保存已导出的模型的备份。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 06/25/2021
ms.author: pafarley
ms.openlocfilehash: f21313a357c5c86087d3505cfdf654d9958d3893
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982359"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>集成用于通知和备份的 Azure 存储

可以将自定义视觉项目与 Azure blob 存储队列集成，以获取项目训练/导出活动的推送通知和已发布模型的备份副本。 运行长时间的操作时，此功能有助于避免不断轮询服务来获取结果。 你可以改为将存储队列通知集成到工作流中。

本指南演示如何将这些 REST API 与 cURL 配合使用。 还可以使用 HTTP 请求服务（如 Postman）来发出请求。

> [!NOTE]
> 推送通知依赖于 **CreateProject** API 中的可选 _notificationQueueUri_ 参数，而模型备份还要求使用可选的 _exportModelContainerUri_ 参数。 本指南将使用这两个参数来演示完整的功能集。

## <a name="prerequisites"></a>先决条件

- Azure 中的一个自定义视觉资源。 如果你没有此资源，请转到 Azure 门户并[创建一个新的自定义视觉资源](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true)。 此功能目前不支持认知服务资源（一体式密钥）。
- 一个带有 Blob 容器的 Azure 存储帐户。 如果在此步骤需要帮助，请按照[存储快速入门](../../storage/blobs/storage-quickstart-blobs-portal.md)进行操作。
- [PowerShell 6.0 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)，或者类似的命令行应用程序。

## <a name="set-up-azure-storage-integration"></a>设置 Azure 存储集成

在 Azure 门户中转到你的自定义视觉训练资源，选择“标识”页，然后启用系统分配的托管标识。

接下来，在 Azure 门户中转到你的存储资源。 转到“访问控制(IAM)”页面并选择“添加角色分配(预览)” 。 然后为集成功能或这两个功能添加角色分配：
* 如果你打算使用模型备份功能，请选择“存储 Blob 数据参与者”角色，然后以成员身份添加你的自定义视觉训练资源。 选择“查看 + 分配”以完成。
* 如果你打算使用通知队列功能，请选择“存储队列数据参与者”角色，然后以成员身份添加你的自定义视觉训练资源。 选择“查看 + 分配”以完成。

有关角色分配的帮助，请参阅[使用 Azure 门户分配 Azure 角色](https://review.docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="get-integration-urls"></a>获取集成 URL

接下来，你将获得可供自定义视觉资源用来访问这些终结点的 URL。

对于通知队列集成 URL，请转到存储帐户的“队列”页，添加新队列，并将其 URL 保存到临时位置。

> [!div class="mx-imgBorder"]
> ![Azure 存储的“队列”页](./media/storage-integration/queue-url.png) 

对于模型备份集成 URL，请转到存储帐户的“容器”页，并创建新容器。 然后选择该容器并转到“属性”页。 将 URL 复制到临时位置。
 
> [!div class="mx-imgBorder"]
> ![Azure 存储容器的“属性”页](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>集成自定义视觉项目

获得集成 URL 后，接下来可以创建一个用于集成 Azure 存储功能的新自定义视觉项目。 还可以更新现有项目以添加功能。

### <a name="create-new-project"></a>创建新项目

调用 [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) API 时，请添加可选参数 _exportModelContainerUri_ 和 _notificationQueueUri_。 分配你在上一部分获取的 URL 值。 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

如果收到 `200/OK` 响应，则表示已成功设置 URL。 在 JSON 响应中应该也能看到 URL 值：

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>更新现有项目

若要通过 Azure 存储功能集成更新现有项目，请使用要更新的项目的 ID 调用 [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) API。 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

将请求正文 (`body`) 设置为以下 JSON 格式，并为 _exportModelContainerUri_ 和 _notificationQueueUri_ 填入适当的值：

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

如果收到 `200/OK` 响应，则表示已成功设置 URL。

## <a name="verify-the-connection"></a>验证连接 

前一部分中的 API 调用应已在 Azure 存储帐户中触发了新的信息。 

在指定容器的 **CustomVision-TestPermission** 文件夹中应有一个测试 Blob。 此 Blob只会暂时存在。

在通知队列中，应会看到如下格式的测试通知：
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>获取事件通知

准备就绪后，对项目调用 [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API 以执行普通的训练操作。

训练完成后，将在存储通知队列中收到一条通知：

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"` 字段可为 `"TrainingCompleted"` 或 `"TrainingFailed"`。 `"iterationId"` 字段是已训练模型的 ID。

## <a name="get-model-export-backups"></a>获取模型导出备份

准备就绪后，调用 [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API 将已训练的模型导出到指定的平台中。

在指定的存储容器中，将显示已导出模型的备份副本。 Blob 名称采用以下格式：

```
{projectId} - {iterationId}.{platformType}
```

导出完成后，在队列中也会收到一条通知。 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"` 字段可为 `"ExportCompleted"` 或 `"ExportFailed"`。 `"modelUri"` 字段将包含容器中存储的备份模型的 URL（假设最初集成的是队列通知）。 如果集成的不是队列通知，则 `"modelUri"` 字段将显示自定义视觉模型 Blob 的 SAS URL。

## <a name="next-steps"></a>后续步骤

在本指南中，你已学习如何在“自定义视觉”资源之间复制和移动项目。 接下来请浏览 API 参考文档，以了解自定义视觉的其他作用。
* [REST API 参考文档（训练）](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [REST API 参考文档（预测）](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)