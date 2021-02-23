---
title:上传媒体:Azure 媒体服务说明:了解如何上传媒体以进行流式处理或编码。
services: media-services documentationcenter: '' author:IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: how-to ms.date:08/31/2020 ms.author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>上传媒体以进行流式处理或编码

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在媒体服务中，可以将数字文件（媒体）上传到与资产关联的 blob 容器中。 [资产](/rest/api/media/operations/asset)实体可以包含视频、音频、图片、缩略图集合、文本轨道和隐藏式字幕文件（以及这些文件的相关元数据）。 将文件上传到资产的容器中后，相关内容即安全地存储在云中供后续处理和流式传输。

不过，在开始之前，你需要收集或考虑一些值。

1. 想要上传的文件的本地文件路径
1. 资产（容器）的资产 ID
1. 想要用于已上传文件（包括扩展）的名称
1. 要使用的存储帐户的名称
1. 存储帐户的访问密钥

## <a name="portal"></a>[门户](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

假设你的代码已建立身份验证，并且你已创建输入资产，那么请使用以下代码片段将本地文件上传到该资产 (in_container)。

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
有关其他方法，请参阅 [Azure 存储文档](../../storage/blobs/index.yml)以了解如何在 [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)、[Java](../../storage/blobs/storage-quickstart-blobs-java.md)、[Python](../../storage/blobs/storage-quickstart-blobs-python.md) 和 [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md) 中使用 blob。

## <a name="next-steps"></a>后续步骤

> [媒体服务概述](media-services-overview.md)
