---
title: 在池上装载虚拟文件系统
description: 了解如何在 Batch 池上装载虚拟文件系统。
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/18/2021
ms.openlocfilehash: 7057a982fb3a4b59b8716a373c2ed5172e392cb2
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444062"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>在 Batch 池上装载虚拟文件系统

Azure Batch 支持在 Batch 池的 Windows 或 Linux 计算节点上装载云存储或外部文件系统。 计算节点加入池时，将装载虚拟文件系统并将其视为该节点上的本地驱动器。 可以装载文件系统，例如 Azure 文件存储、Azure Blob 存储、网络文件系统 (NFS)，包括 [Avere vFXT 缓存](../avere-vfxt/avere-vfxt-overview.md)或通用 Internet 文件系统 (CIFS)。

在本文中，你将了解如何使用[适用于 .NET 的 Batch 管理库](/dotnet/api/overview/azure/batch)在计算节点池上装载虚拟文件系统。

> [!NOTE]
> 仅 2019 年 8 月 8 日或之后创建的 Batch 池上支持装载虚拟文件系统。 在该日期之前创建的 Batch 池将不支持此功能。

## <a name="benefits-of-mounting-on-a-pool"></a>装载在池上的好处

将文件系统装载到池中，而不是让任务从大型数据集中检索自己的数据，可以使任务能够更轻松且更高效地访问所需的数据。

考虑具有多个任务的场景，这些任务需要访问一组通用数据，例如渲染电影。 每个任务每次渲染场景文件中的一个或多个帧。 通过装载包含场景文件的驱动器，计算节点可以更轻松地访问共享数据。

此外，可以根据同时访问数据的计算节点数量所需的性能和规模（吞吐量和 IOPS）来单独选择基础文件系统以及对其进行缩放。 例如，可使用 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 分布式内存中缓存，通过数千个并发渲染节点，支持大型动画规模级别的渲染，并访问驻留在本地的源数据。 另外，对于已经驻留在基于云的 Blob 存储中的数据，可以使用 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) 将该数据装载为本地文件系统。 但 Blobfuse 仅在 Linux 节点上可用，而 [Azure 文件](../storage/files/storage-files-introduction.md)提供了类似的工作流，并且在 Windows 和 Linux 上均可用。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>在池上装载虚拟文件系统  

在池上装载虚拟文件系统可使文件系统用于池中的每个计算节点。 在计算节点加入池时，或节点重启或重置映像时，将配置文件系统。

要在池上装载文件系统，请创建 `MountConfiguration` 对象。 选择适合虚拟文件系统的对象：`AzureBlobFileSystemConfiguration`、`AzureFileShareConfiguration`、`NfsMountConfiguration` 或 `CifsMountConfiguration`。

所有装载配置对象都需要以下基本参数。 一些装载配置具有特定于所使用的文件系统的参数，本文将通过代码示例进行详细讨论。

- **帐户名或源**：需要提供存储帐户的名称或其源名称才能装载虚拟文件共享。
- **相对装载路径或源**：文件系统安装在计算节点上的位置，相对于可通过 `AZ_BATCH_NODE_MOUNTS_DIR` 在节点上访问的标准 `fsmounts` 目录。 确切位置因节点上使用的操作系统而异。 例如，Ubuntu 节点上的物理位置被映射到 `mnt\batch\tasks\fsmounts`，而 CentOS 节点上的物理位置被映射到 `mnt\resources\batch\tasks\fsmounts`。
- **装载选项或 blobfuse 选项**：这些选项描述了用于装载文件系统的特定参数。

创建 `MountConfiguration` 对象后，在创建池时将对象分配给 `MountConfigurationList` 属性。 在节点加入池时，或节点重启或重置映像时，将装载文件系统。

装载文件系统时，将创建环境变量 `AZ_BATCH_NODE_MOUNTS_DIR`，该变量指向装载文件系统以及日志文件的位置，这对于故障排除和调试很有用。 [诊断装载错误](#diagnose-mount-errors)部分中详细说明了日志文件。  

> [!IMPORTANT]
> 池上可装载的文件系统的最大数量为 10。 有关详细信息和其他限制，请参阅 [Batch 服务配额和限制](batch-quota-limit.md#other-limits)。

## <a name="examples"></a>示例

以下代码示例演示如何将各种文件共享装载到计算节点池中。

### <a name="azure-files-share"></a>Azure 文件存储共享

Azure 文件存储是标准的 Azure 云文件系统产品/服务。 若要详细了解如何获取装载配置代码示例中的任何参数，请查看[使用 Azure 文件存储共享 - SMB](../storage/files/storage-how-to-use-files-windows.md) 或[使用 Azure 文件存储共享 - NFS](../storage/files/storage-files-how-to-create-nfs-shares.md)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-container"></a>Azure Blob 容器

另一种选择方案是通过 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) 使用 Azure Blob 存储。 装载 Blob 文件系统需要使用拥有存储帐户访问权限的 `AccountKey`、`SasKey` 或 `Managed Identity`。

有关获取这些密钥的信息，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)、[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../storage/common/storage-sas-overview.md)以及[在 Batch 池中配置托管标识](managed-identity-pools.md)。 有关使用 Blobfuse 的更多信息和技巧，请参阅 [Blobfuse 项目](https://github.com/Azure/azure-storage-fuse)。

要获得对 blobfuse 装载目录的默认访问权限，请以管理员身份运行任务。 Blobfuse 在用户空间处将装载目录，在创建池时将其作为根装载。 在 Linux 中，所有管理员任务都是根。 [FUSE 参考页](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)中介绍了 FUSE 模块的所有选择方案。

有关使用 blobfuse 的详细信息和使用技巧，请参阅[故障排除常见问题解答](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)。 还可[在 blobfuse 存储库中查看 GitHub 问题](https://github.com/Azure/azure-storage-fuse/issues)，以查看当前 blobfuse 问题和解决方法。

> [!NOTE]
> 下方示例显示 `AccountKey`、`SasKey` 和 `IdentityReference`，但它们之间是互斥的，因此只能指定其中一个。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "SasKey",
                IdentityReference = new ComputeNodeIdentityReference("/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"),
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

> [!TIP]
>如果使用托管标识，请确保已将该标识[分配到池](managed-identity-pools.md)，以使其在执行装载的 VM 上可用。 托管标识需要具有`Storage Blob Data Contributor` 角色，才能正常工作。

### <a name="network-file-system"></a>网络文件系统

可将网络文件系统 (NFS) 装载到池节点，使 Azure Batch 轻松访问传统文件系统。 此类文件系统可能是部署在云中的单个 NFS 服务器，或通过虚拟网络访问的本地 NFS 服务器。 NFS 装载支持 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 分布式内存中高性能计算 (HPC) 任务以及其他标准 NFS 兼容接口，如[适用于 Azure Blob 的 NFS](../storage/blobs/network-file-system-protocol-support.md) 和[适用于 Azure 文件存储的 NFS](../storage/files/storage-files-how-to-mount-nfs-shares.md)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=3.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>通用 Internet 文件系统

将[通用 Internet 文件系统 (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 装载到池节点是访问传统文件系统的另一种方法。 CIFS 是一种文件共享协议，它提供了一种用于请求网络服务器文件和服务的开放和跨平台的机制。 CIFS 基于适用于 Internet 和 Intranet 文件共享的[服务器消息块 (SMB)](/windows-server/storage/file-server/file-server-smb-overview) 协议的增强版本。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>诊断装载错误

若装载配置失败，池中的计算节点将失败，节点状态将设置为“`unusable`”。 要诊断某个安装配置失败，请检查 [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) 属性，获取有关错误的详细信息。

要获取日志文件以进行调试，请使用 [OutputFiles](batch-task-output-files.md) 上传 `*.log` 文件。 `*.log` 文件包含有关在 `AZ_BATCH_NODE_MOUNTS_DIR` 位置装载文件系统的信息。 对于每个装载，装载日志文件格式：`<type>-<mountDirOrDrive>.log`。 例如，名为 `test` 的装载目录中处的 `cifs` 装载将有一个名为 `cifs-test.log` 的装载日志文件。

## <a name="support-matrix"></a>支持矩阵

Azure Batch 支持为节点代理针对其各自的发布服务器和产品/服务生成的以下虚拟文件系统类型。

| OS 类型 | Azure 文件存储共享 | Azure Blob 容器 | NFS 装载 | CIFS 装载 |
|---|---|---|---|---|
| Linux | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>网络要求

在[虚拟网络](batch-virtual-network.md)中使用 Azure Batch 池进行虚拟文件装载时，请记住以下要求并确保所需流量畅通无阻。

- **Azure 文件共享**：
  - 要获得与“存储”服务标记之间的往来流量，需要打开 TCP 端口 445。 有关详细信息，请参阅[将 Azure 文件共享与 Windows 配合使用](../storage/files/storage-how-to-use-files-windows.md#prerequisites)。
- **Azure Blob 容器**：
  - 要获得与“存储”服务标记之间的往来流量，需要打开 TCP 端口 443。
  - 要下载 blobfuse 和 gpg 包，VM 必须有 https://packages.microsoft.com 的访问权限。 根据配置情况，要下载更多包，可能还需要有其他 URL 的访问权限。
- 网络文件系统 (NFS)：
  - 默认情况下，需要有端口 2049（默认情况下，配置可能有其他要求）的访问权限。
  - 要下载 nfs-common（针对 Debian 或 Ubuntu）包或 nfs-utils（针对 CentOS）包，VM 必须有相应包管理器的访问权限。 此 URL 可能因 OS 版本而异。 根据配置情况，要下载更多包，可能还需要有其他 URL 的访问权限。
  - 通过 NFS 装载 Azure Blob 或 Azure 文件存储可能需要额外的网络要求，例如，与存储帐户共享虚拟网络的同一指定子网的计算节点。
- 通用 Internet 文件系统 (CIFS)：
  - 需要有 TCP 端口 445 的访问权限。
  - 要下载 cifs-utils 包，VM 必须有相应包管理器的访问权限。 此 URL 可能因 OS 版本而异。

## <a name="next-steps"></a>后续步骤

- 了解有关将 Azure 文件共享装载到 [Windows](../storage/files/storage-how-to-use-files-windows.md) 或 [Linux](../storage/files/storage-how-to-use-files-linux.md) 的详细信息。
- 了解有关使用和装载 [blobfuse](https://github.com/Azure/azure-storage-fuse) 虚拟文件系统的信息。
- 要了解 NFS 及其应用程序，请参阅[网络文件系统概述](/windows-server/storage/nfs/nfs-overview)。
- 要详细了解 SMB，请参阅 [Microsoft SMB 协议和 CIFS 协议概述](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)。