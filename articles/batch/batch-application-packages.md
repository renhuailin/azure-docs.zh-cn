---
title: 将应用程序包部署到计算节点
description: 使用 Azure Batch 的应用程序包功能轻松管理要安装在 Batch 计算节点上的多个应用程序和版本。
ms.topic: how-to
ms.date: 09/24/2020
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: b4c6ab99b0fa93e5fdea4256d6f232cc0fe4218e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033725"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>使用 Batch 应用程序包将应用程序部署到计算节点

应用程序包可以简化 Azure Batch 解决方案中的代码，使你可以更轻松地管理你的任务运行的应用程序。 使用应用程序包可以上传和管理任务运行的多个应用程序版本，包括其支持文件。 然后，可以将一个或多个此类应用程序自动部署到池中的计算节点。

用于创建和管理应用程序包的 API 属于 [Batch Management .NET](/dotnet/api/overview/azure/batch/management) 库。 用于在计算节点上安装应用程序包的 API 属于 [Batch .NET](/dotnet/api/overview/azure/batch/client) 库。 其他语言的可用 Batch API 中提供了对应的功能。

本文介绍如何在 Azure 门户中上传和管理应用程序包。 另外还介绍如何使用 [Batch .NET](/dotnet/api/overview/azure/batch/client) 库将应用程序包安装到池的计算节点上。

## <a name="application-package-requirements"></a>应用程序包要求

要使用应用程序包，需要[将 Azure 存储帐户链接](#link-a-storage-account)到批处理帐户。

批处理帐户中的应用程序和应用程序包数目，以及应用程序包的大小上限有其限制。 有关详细信息，请参阅 [Azure Batch 服务的配额和限制](batch-quota-limit.md)。

> [!NOTE]
> 在 2017 年 7 月 5 日以前创建的 Batch 池不支持应用程序包（除非这些池是在 2016 年 3 月 10 日之后使用云服务配置创建的）。 此处所述的应用程序包功能替换了旧版服务中的“批处理应用”功能。

## <a name="understand-applications-and-application-packages"></a>了解应用程序和应用程序包

在 Azure Batch 中，应用程序是指一组已创建版本的二进制文件，这些文件可自动下载到池中的计算节点。 一个应用程序包含一个或多个应用程序包，这些包代表应用程序的不同版本。

每个应用程序包都是一个 .zip 文件，其中包含应用程序二进制文件和任何支持文件。 只支持 .zip 格式。

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="此图显示了应用程序和应用程序包的概要视图。":::

可以在池或任务级别指定应用程序包。

- **池应用程序包** 部署到池中的每个节点。 当节点加入池以及重新启动或重置映像时，就会部署应用程序。
  
    当池中的所有节点都将执行作业的任务时，便适合使用池应用程序包。 在创建池时，可以指定一个或多个要部署的应用程序包。 还可以添加或更新现有池的包。 若要将新包安装到现有池，必须重启其节点。

- 在运行任务的命令行之前，**任务应用程序包** 只部署到计划要运行任务的计算节点。 如果节点上已有指定的应用程序包和版本，则不会重新部署，而是使用现有包。
  
    任务应用程序包在共享池环境（其中，不同的作业在一个池中运行，一个作业完成时并不删除该池）中很有用。 如果作业中的任务数小于池中的节点数，任务应用程序包可以尽量减少数据传输，因为应用程序只部署到运行任务的节点。
  
    其他可受益于任务应用程序包的方案为，运行大型应用程序但只执行少数任务的作业。 例如，任务应用程序可以用于工作负荷较重的预处理阶段或合并任务。

有了应用程序包，池的启动任务不需要指定在节点上安装一长串的单个资源文件。 不需要在 Azure 存储中或在节点上手动管理应用程序的多个版本。 而且，也不必费心生成 [SAS URL](../storage/common/storage-sas-overview.md) 来提供对你的存储帐户中的文件的访问权限。 Batch 在后台与 Azure 存储协作来存储应用程序包，并将其部署到计算节点。

> [!NOTE]
> 启动任务的总大小必须小于或等于 32768 个字符，其中包括资源文件和环境变量。 如果你的启动任务超出此限制，备选方案就是使用应用程序包。 还可以创建一个包含资源文件的 .zip 文件，将它作为 blob 上传到 Azure 存储，然后从启动任务的命令行中对该文件进行解压缩。

## <a name="upload-and-manage-applications"></a>上传和管理应用程序

可以使用 [Azure 门户](https://portal.azure.com)或 Batch 管理 API 来管理 Batch 帐户中的应用程序包。 以下部分介绍如何在 Azure 门户中关联存储帐户，以及如何添加和管理应用程序和应用程序包。

### <a name="link-a-storage-account"></a>关联存储帐户

若要使用应用程序包，必须将 [Azure 存储帐户](accounts.md#azure-storage-accounts)关联到 Batch 帐户。 Batch 服务将使用关联的存储帐户存储应用程序包。 建议专门创建一个与 Batch 帐户配合使用的存储帐户。

如果尚未配置存储帐户，第一次选择 Batch 帐户中的“应用程序”时，Azure 门户会显示警告。 若要将存储帐户关联到 Batch 帐户，请选择“警告”窗口中的“存储帐户”，然后再次选择“存储帐户”。  

链接两个帐户后，Batch 便能将存储在链接之存储帐户中的包自动部署到计算节点。

> [!IMPORTANT]
> 不能将应用程序包与配置了[防火墙规则](../storage/common/storage-network-security.md)的 Azure 存储帐户配合使用，或与已设置为“启用”的“分层命名空间”配合使用。 

Batch 服务使用 Azure 存储将应用程序包存储为块 blob。 块 blob 数据[正常收费](https://azure.microsoft.com/pricing/details/storage/)，且每个包的大小不能超过最大块 blob 大小。 有关详细信息，请参阅[存储帐户的 Azure 存储可伸缩性和性能目标](../storage/blobs/scalability-targets.md)。 为了最大限度地降低成本，请务必考虑应用程序包的大小和数目，并定期删除弃用的包。

### <a name="view-current-applications"></a>查看当前应用程序

若要查看 Batch 帐户中的应用程序，请在左侧导航菜单中选择“应用程序”。

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Azure 门户中的“应用程序”菜单项的屏幕截图。":::

选择此菜单选项可打开“应用程序”窗口。 此窗口显示帐户中每个应用程序的 ID，以及以下属性：

- **包**：与此应用程序关联的版本号。
- **默认版本**：如果在部署应用程序时未指定版本，则此项为将要安装的应用程序版本（如果适用）。
- **允许更新**：指定是否允许更新和删除包。

若要查看计算节点上应用程序包的[文件结构](files-and-directories.md)，请导航到 Azure 门户中的 Batch 帐户。 选择“池”。 然后选择包含计算节点的池。 选择在其上安装应用程序包的计算节点，打开 **applications** 文件夹。

### <a name="view-application-details"></a>查看应用程序详细信息

若要查看某个应用程序的详细信息，请在“应用程序”窗口中选择它。 可以配置应用程序的以下设置。

- **允许更新**：指示能否 [更新或删除](#update-or-delete-an-application-package)应用程序包。 默认值为“是”。 如果设置为“否”，则不能更新或删除现有的应用程序包，但仍可添加新的应用程序包版本。
- **默认版本**：部署应用程序时要使用的默认应用程序包（如果未指定任何版本）。
- **显示名称**：Batch 解决方案在显示应用程序相关信息时可以使用的易记名称。 例如，在通过 Batch 提供给客户的服务的 UI 中使用的名称。

### <a name="add-a-new-application"></a>添加新的应用程序

若要创建新应用程序，请添加应用程序包并指定唯一的应用程序 ID。

在 Batch 帐户中，选择“应用程序”，然后选择“添加”。 

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="在 Azure 门户中新建应用程序的过程的屏幕截图。":::

输入以下信息：

- **应用程序 ID**：新应用程序的 ID。
- **版本**：要上传的应用程序包的版本。
- 应用程序包：.zip 文件，其中包含应用程序二进制文件和执行应用程序所需的支持文件。

输入的 **应用程序 ID** 和 **版本** 必须满足以下要求：

- 在 Windows 节点上，ID 可以包含字母数字字符、连字符和下划线的任意组合。 在 Linux 节点上，仅允许使用字母数字字符和下划线。
- 包含的字符不能超过 64 个。
- 在 Batch 帐户内必须唯一。
- ID 保留大小写，但不区分大小写。

准备就绪后，请选择“提交”。 将 .zip 文件上传到 Azure 存储帐户后，门户会显示一个通知。 这可能需要一些时间，具体取决于上传文件的大小和网络连接的速度。

### <a name="add-a-new-application-package"></a>添加新应用程序包

若要为现有应用程序添加应用程序包版本，请在 Batch 帐户的“应用程序”部分选择该应用程序，然后选择“添加”。

如前面针对新应用程序所做的那样，指定新包的“版本”，在“应用程序包”字段中上传 .zip 文件，然后选择“提交”  。

### <a name="update-or-delete-an-application-package"></a>更新或删除应用程序包

若要更新或删除现有的应用程序包，请在 Batch 帐户的“应用程序”部分选择该应用程序。 选择要修改的应用程序包行中的省略号，然后选择要执行的操作。

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="屏幕截图，显示 Azure 门户中应用程序包的更新和删除选项。":::

如果选择“更新”，则可以上传新的 .zip 文件。 这将替换已为该版本上传的上一个 .zip 文件。

如果你选择“删除”，系统会提示你确认是否要删除该版本。 选择“确定”后，Batch 会从你的 Azure 存储帐户中删除该 .zip 文件。 如果删除应用程序的默认版本，则该应用程序的“默认版本”设置会被删除。

## <a name="install-applications-on-compute-nodes"></a>将应用程序安装在计算节点上

你了解如何在 Azure 门户中管理应用程序包之后，我们就可以介绍如何将应用程序包部署到计算节点并使用 Batch 任务运行它们。

### <a name="install-pool-application-packages"></a>安装池应用程序包

要将应用程序包安装在池中的所有计算节点上，需要为池指定一个或多个应用程序包引用。 为池指定的应用程序包会安装在每个加入池的计算节点上，以及已重启或重置映像的任何节点上。

在 Batch.NET 中，在创建新池时指定一个或多个 [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences)，或者为现有池这样做。 [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) 类指定要安装在池的计算节点上的应用程序 ID 和版本。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> 如果应用程序包部署失败，Batch 服务会将该节点标记为[不可用](/dotnet/api/microsoft.azure.batch.computenode.state)，并且不会在该节点上计划执行任何任务。 如果发生这种情况，请重启节点以重新启动包部署。 重启节点也会在节点上再次启用任务计划。

### <a name="install-task-application-packages"></a>安装任务应用程序包

类似于池，可以为任务指定应用程序包引用。 在节点上计划要运行的任务时，请先下载并解压缩包，然后执行任务的命令行。 如果节点上已安装指定的包和版本，则不会下载包，而是使用现有包。

若要安装任务应用程序包，请配置任务的 [CloudTask.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) 属性：

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>执行安装的应用程序

为池或任务指定的包下载并解压缩到节点的 `AZ_BATCH_ROOT_DIR` 中的命名目录。 Batch 还会创建包含命名目录路径的环境变量。 在引用节点上的应用程序时，任务的命令行使用此环境变量。

在 Windows 节点上，变量格式如下：

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

在 Linux 节点上，该格式略有不同。 句点 (.)、连字符 (-) 和数字符号 (#) 在环境变量中都会转换为下划线。 另请注意，请保留应用程序 ID 的大小写。 例如：

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` 和 `version` 是对应于为部署指定的应用程序和包版本的值。 例如，如果指定应在 Windows 节点上安装 2.7 版的 blender 应用程序，任务命令行可使用此环境变量来访问其文件：

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

在 Linux 节点上，按以下格式指定环境变量。 将句点 (.)、连字符 (-) 和数字符号 (#) 平展为下划线，并保留应用程序 ID 的大小写：

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

在上传应用程序包时，可以指定要部署到计算节点的默认版本。 如果已指定应用程序的默认版本，可以在引用该应用程序时省略版本后缀。 可以在 Azure 门户的“应用程序”窗口中指定默认的应用程序版本，如[上传和管理应用程序](#upload-and-manage-applications)中所述。

例如，如果设置“2.7”作为 blender 应用程序的默认版本，并且任务引用以下环境变量，则 Windows 节点将会执行 2.7 版：

`AZ_BATCH_APP_PACKAGE_BLENDER`

以下代码片段显示任务命令行示例，其可启动 blender 应用程序的默认版本：

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> 有关计算节点环境设置的详细信息，请参阅[任务的环境设置](jobs-and-tasks.md#environment-settings-for-tasks)。

## <a name="update-a-pools-application-packages"></a>更新池的应用程序包

如果已配置现有池的应用程序包，可以指定池的新包。 这意味着：

- Batch 服务在联接池的所有新节点上以及任何重新启动或重置映像的现有节点上都会安装新指定的包。
- 在更新包引用时已存在池中的计算节点不自动安装新应用程序包。 这些计算节点必须重新启动或重置映像才能接收新包。
- 部署新包后，创建的环境变量将反映新的应用程序包引用。

在此示例中，现有池已将 blender 应用程序的 2.7 版配置为其 [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) 之一。 要将池的节点更新为 2.76b 版，请将 [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) 指定为新版本并提交更改。

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

配置新版本后，Batch 服务将在任何联接池的新节点上安装 2.76b 版。 要将 2.76b 安装到已在池中的节点上，请重新启动或重置映像这些节点。 请注意，重启的节点会保留前面的包部署中的文件。

## <a name="list-the-applications-in-a-batch-account"></a>列出 Batch 帐户中的应用程序

可以使用 [ApplicationOperations.ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) 方法列出 Batch 帐户中的应用程序及其包。

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>后续步骤

- [Batch REST API](/rest/api/batchservice) 还提供应用程序包的使用支持。 有关示例，请参阅 [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) 元素以了解如何指定要安装的包，并参阅[应用程序](/rest/api/batchservice/application)以了解如何获取应用程序信息。
- 了解如何以编程方式[使用 Batch Management .NET 管理 Azure Batch 帐户和配额](batch-management-dotnet.md)。 [Batch Management .NET](/dotnet/api/overview/azure/batch/management) 库可以启用 Batch 应用程序或服务的帐户创建和删除功能。
