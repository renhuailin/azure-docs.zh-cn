---
title: 创建和使用资源文件
description: 了解如何从各种输入源创建 Batch 资源文件。 本文介绍有关如何创建这些文件并将其置于 VM 上的一些常用方法。
ms.date: 05/25/2021
ms.topic: how-to
ms.openlocfilehash: 1ef8cde8c345cebeb166cddd67a1951d71eea810
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110467686"
---
# <a name="creating-and-using-resource-files"></a>创建和使用资源文件

Azure Batch 任务通常需要处理某种形式的数据。 使用资源文件，可以通过任务将此数据提供给 Batch 虚拟机 (VM)。 所有类型的任务都支持资源文件：任务、启动任务、作业准备任务、作业发布任务等。本文介绍有关如何创建资源文件并将其置于 VM 上的一些常用方法。  

资源文件将数据放置在 Batch VM 上，但数据类型和使用方式是灵活的。 有一些常见的用例：

- 使用启动任务上的资源文件在每个 VM 上预配公共文件。
- 预配要由任务处理的输入数据。

例如，公共文件可以是启动任务上用于安装任务运行的应用程序的文件。 输入数据可以是原始图像或视频数据，也可以是 Batch 要处理的任何信息。

## <a name="types-of-resource-files"></a>资源文件的类型

有几个不同的选项可用于生成资源文件，每个都有其自己的[方法](/dotnet/api/microsoft.azure.batch.resourcefile#methods)。 资源文件的创建过程取决于存储原始数据的位置以及是否应该创建多个文件。

- [存储容器 URL](#storage-container-url)：从 Azure 中的任何存储容器中生成资源文件。
- [存储容器名称](#storage-container-name-autostorage)：根据链接到 Batch 帐户（自动存储帐户）的 Azure 存储帐户中的容器名称生成资源文件。
- [Web 终结点中的单个资源文件](#single-resource-file-from-web-endpoint)：根据任何有效 HTTP URL 生成单个资源文件。

### <a name="storage-container-url"></a>存储容器 URL

使用存储容器 URL 意味着，只要拥有正确的权限，就可以访问 Azure 中任何存储容器中的文件。

在下面的 C# 示例中，文件已作为 blob 存储上传到 Azure 存储容器。 为了访问在创建资源文件时所需的数据，首先需要获取对存储容器的访问权限。

创建一个具有正确权限的共享访问签名 (SAS) URI 来访问存储容器。 设置 SAS 的到期时间和权限。 本例中没有指定开始时间，因此 SAS 会立即生效，并且有效期是生成后的两小时内。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 若要访问容器，必须同时拥有 `Read` 和 `List` 权限；而如果要访问 blob，只需要 `Read` 权限即可。

配置权限后，创建 SAS 令牌并格式化 SAS URL 以访问存储容器。 使用存储容器的格式化 SAS URL，通过 [FromStorageContainerUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl) 生成资源文件。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

如果需要，可以使用 [blobPrefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) 属性将下载项限制为名称以指定前缀开头的 Blob：

```csharp
ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl, blobPrefix = yourPrefix);
```

生成 SAS URL 的替代方法是，启用对 Azure Blob 存储中的容器及其 blob 的匿名公共读取访问。 这样做可以授予对这些资源的只读访问权限，无需共享帐户密钥，也无需 SAS。 如果需要始终允许对某些 blob 进行匿名读取访问，通常是通过启用公共读取访问来实现。 如果这与你的解决方案相匹配，请参阅[匿名访问 blob](../storage/blobs/anonymous-read-access-configure.md) 一文，详细了解如何管理对 blob 数据的访问。

### <a name="storage-container-name-autostorage"></a>存储容器名称（自动存储）

如果不选择配置和创建 SAS URL，可以使用 Azure 存储容器名称访问 blob 数据。 使用的存储容器必须位于关联到 Batch 帐户（有时也称作自动存储帐户）的 Azure 存储帐户。 使用自动存储容器，可以绕过配置和创建 SAS URL 以访问存储容器的步骤。 只需在关联的存储帐户中提供存储容器的名称即可。

如果还没有自动存储帐户，请参阅[创建 Batch 帐户](batch-account-create-portal.md)中的步骤，详细了解如何创建和链接存储帐户。

以下示例使用 [AutoStorageContainer](/dotnet/api/microsoft.azure.batch.resourcefile.fromautostoragecontainer) 根据自动存储帐户中的数据生成文件。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

与存储容器 URL 一样，可以使用 [blobPrefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) 属性指定要下载的 Blob：

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName, blobPrefix = yourPrefix);
```

### <a name="single-resource-file-from-web-endpoint"></a>Web 终结点中的单个资源文件

若要创建单个资源文件，可以指定包含输入数据的有效 HTTP URL。 此 URL 将提供给 Batch API，然后使用数据创建资源文件。 无论用于创建资源文件的数据是位于 Azure 存储，还是位于任何其他 Web 位置（如 GitHub 终结点）中，都可以使用此方法。

以下示例使用 [FromUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromurl) 从包含有效 URL 的字符串中检索文件，然后生成任务要使用的资源文件。 在这种情况下，不需要凭据。 （如果使用 blob 存储，则需要凭据，除非在 blob 容器上启用了公共读取访问。）

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourURL, filePath);
```

还可使用定义为 URL 的字符串（或者共同为你的文件创建完整 URL 的字符串组合）。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourDomain + yourFile, filePath);
```

## <a name="tips-and-suggestions"></a>提示和建议

Azure Batch 任务可以通过多种方式使用文件，这就是 Batch 为管理任务文件提供各种选项的原因。 以下应用场景并不全面，但介绍了几种常见的情况，并提供了建议。

### <a name="many-resource-files"></a>多个资源文件

如果 Batch 作业中的许多任务之间共享常用任务文件，则可能需要使用[应用程序包](batch-application-packages.md)来包含这些文件。 应用程序包为下载速度提供优化，并且应用程序包中的数据在任务之间进行缓存。 借助应用程序包，无需手动管理多个资源文件或生成 SAS URL 即可访问 Azure 存储中的文件。 Batch 在后台与 Azure 存储协作存储应用程序包，并将其部署到计算节点。 如果任务文件不经常更改，应用程序包可能非常适合你的解决方案。

相反，如果每个任务包含许多特定于该任务的文件，则资源文件可能是最佳选项。 使用独有文件的任务通常需要更新或替换，这对于应用程序包内容而言并不简单。 资源文件使更新、添加或编辑单个文件的操作更具灵活性。

### <a name="number-of-resource-files-per-task"></a>每个任务的资源文件数

如果一个任务指定了数百个资源文件，Batch 可能会因为任务太大而拒绝该任务。 最好是将任务本身的资源文件数量减到最小，从而使任务保持较小规模。

如果无法最大程度地减少任务所需的文件数，可以通过创建可引用资源文件存储容器的单个资源文件来优化任务。 为此，将资源文件放入 Azure 存储容器，并使用上述方法之一根据需要生成资源文件。

## <a name="next-steps"></a>后续步骤

- 了解作为资源文件的替代方案的[应用程序包](batch-application-packages.md)。
- 了解如何[将容器用于资源文件](batch-docker-container-workloads.md)。
- 了解如何[收集和保存任务的输出数据](batch-task-output.md)。
- 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
