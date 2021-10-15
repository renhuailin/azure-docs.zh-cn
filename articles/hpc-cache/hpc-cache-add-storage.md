---
title: 将存储添加到 Azure HPC 缓存
description: 如何定义存储目标，使 Azure HPC 缓存能够使用本地 NFS 系统或 Azure Blob 容器来长期存储文件
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/22/2021
ms.custom: subject-rbac-steps
ms.author: v-erkel
ms.openlocfilehash: 016a62bc7713b389e8baf7d0a5b5aeefceb81f2b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271949"
---
# <a name="add-storage-targets"></a>添加存储目标

*存储目标* 是适用于通过 Azure HPC 缓存访问的文件的后端存储。 可以添加 NFS 存储（例如本地硬件系统），也可以将数据存储在 Azure Blob 中。

可以为任何缓存定义 10 个不同的存储目标，较大的缓存可以[支持最多 20 个存储目标](#size-your-cache-correctly-to-support-your-storage-targets)。

缓存在一个[聚合命名空间](hpc-cache-namespace.md)中呈现所有存储目标。 命名空间路径是在添加存储目标后单独配置的。

请记住，存储导出必须可供从缓存的虚拟网络访问。 对于本地硬件存储，可能需要设置一个 DNS 服务器，该服务器可以解析用于访问 NFS 存储的主机名。 请阅读 [DNS 访问](hpc-cache-prerequisites.md#dns-access)了解详细信息。

创建缓存后添加存储目标。 请遵循以下过程：

1. [创建缓存](hpc-cache-create.md)
1. 定义存储目标（本文介绍的信息）
1. [创建面向客户端的路径](add-namespace-paths.md)（用于[聚合命名空间](hpc-cache-namespace.md)）

添加存储目标的过程根据存储目标使用的存储类型而略有不同。 下面介绍了每种方法的详细信息。

单击下图观看有关在 Azure 门户中创建缓存和添加存储目标的[视频演示](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)。

[![视频缩略图：Azure HPC 缓存：设置（单击此项可访问视频页）](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="size-your-cache-correctly-to-support-your-storage-targets"></a>正确调整缓存大小以支持存储目标

支持的存储目标数取决于创建缓存时设置的缓存大小。 缓存容量是吞吐量容量 (GB/秒) 和存储容量 (TB) 的组合。

* 最多 10 个存储目标 - 如果所选吞吐量具有最小或中等缓存存储值，标准缓存最多可以有 10 个存储目标。

  例如，如果选择了 2GB/秒的吞吐量且未选择最高缓存存储大小，则缓存最多支持 10 个存储目标。

* 最多 20 个存储目标：

  * 所有高吞吐量缓存（具有预配置的缓存存储大小）最多可支持 20 个存储目标。
  * 如果为所选吞吐量值选择最高可用缓存大小，则标准缓存最多可支持 20 个存储目标。 （如果使用 Azure CLI，则为缓存 SKU 选择最高有效缓存大小。）

请阅读[设置缓存容量](hpc-cache-create.md#set-cache-capacity) ，详细了解吞吐量和缓存大小设置。

## <a name="choose-the-correct-storage-target-type"></a>选择适当的存储目标类型

可以从三种存储目标类型中进行选择：NFS、Blob 和 ADLS-NFS  。 选择与将在此 HPC 缓存项目中用于存储文件的存储系统类型相匹配的类型。

* NFS - 创建 NFS 存储目标以访问网络连接存储 (NAS) 系统上的数据。 这可以是本地存储系统或可通过 NFS 访问的其他存储类型。

  * 要求：[NFS 存储要求](hpc-cache-prerequisites.md#nfs-storage-requirements)
  * 说明：[添加新的 NFS 存储目标](#add-a-new-nfs-storage-target)

* Blob - 使用 Blob 存储目标将工作文件存储在新的 Azure Blob 容器中。 只能从 Azure HPC 缓存读取或写入此容器。

  * 先决条件：[Blob 存储要求](hpc-cache-prerequisites.md#blob-storage-requirements)
  * 说明：[添加新的 Azure Blob 存储目标](#add-a-new-azure-blob-storage-target)

* ADLS-NFS - ADLS-NFS 存储目标访问来自[支持 NFS 的 Blob](../storage/blobs/network-file-system-protocol-support.md) 容器的数据。 可以使用标准 NFS 命令预加载容器，然后可以使用 NFS 读取文件。

  * 先决条件：[ADLS-NFS 存储要求](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)
  * 说明：[添加新的 ADLS-NFS 存储目标](#add-a-new-adls-nfs-storage-target)

## <a name="add-a-new-azure-blob-storage-target"></a>添加新的 Azure Blob 存储目标

新的 Blob 存储目标需要一个空的 Blob 容器，或者以 Azure HPC 缓存云文件系统格式的数据填充的容器。 在[将数据移到 Azure Blob 存储](hpc-cache-ingest.md)中详细了解如何预加载 Blob 容器。

Azure 门户中的“添加存储目标”页提供了用于创建新的 Blob 容器，然后紧接着添加该容器的选项。

> [!NOTE]
>
> * 对于已装载 NFS 的 Blob 存储，请使用 [ADLS-NFS 存储目标](#add-a-new-adls-nfs-storage-target)类型。
> * [高吞吐量缓存配置](hpc-cache-create.md#choose-the-cache-type-for-your-needs)不支持标准 Azure Blob 存储目标。 请改为使用支持 NFS 的 Blob 存储 (ADLS-NFS)。

### <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中打开你的缓存实例，然后单击左侧边栏中的“存储目标”。

![“设置”>“存储目标”页的屏幕截图，其中的表格列出了两个现有的存储目标，表格上方突出显示了“+ 添加存储目标”按钮](media/add-storage-target-button.png)

“存储目标”页将列出所有现有目标，并提供用于添加新目标的链接。

单击“添加存储目标”按钮。

![“添加存储目标”页的屏幕截图，其中填充了新 Azure Blob 存储目标的信息](media/hpc-cache-add-blob.png)

若要定义 Azure Blob 容器，请输入以下信息。

* **存储目标名称** - 设置一个用于在 Azure HPC 缓存中标识此存储目标的名称。
* **目标类型** - 选择“Blob”。
* **存储帐户** - 选择要使用的帐户。

  需要根据[添加访问角色](#add-the-access-control-roles-to-your-account)中所述，授权缓存实例访问存储帐户。

  有关可以使用的存储帐户类型的信息，请参阅 [Blob 存储要求](hpc-cache-prerequisites.md#blob-storage-requirements)。

* **存储容器** - 选择此目标的 Blob 容器，或单击“新建”。

  ![用于指定新容器的名称和访问级别（专用）的对话框屏幕截图](media/add-blob-new-container.png)

完成后，单击“确定”以添加该存储目标。

> [!NOTE]
> 如果你的存储帐户防火墙设置为仅限通过“选定网络”进行访问，请使用[解决 Blob 存储帐户防火墙设置问题](hpc-cache-blob-firewall-fix.md)中所述的临时解决方法。

### <a name="add-the-access-control-roles-to-your-account"></a>将访问控制角色添加到帐户

Azure HPC 缓存使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/index.yml) 来授权缓存服务访问 Azure Blob 存储目标的存储帐户。

存储帐户所有者必须为用户“HPC 缓存资源提供者”显式添加角色“[存储帐户参与者](../role-based-access-control/built-in-roles.md#storage-account-contributor)”和“[存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)”。

可以提前执行此操作，也可以通过单击添加 Blob 存储目标时所在门户页面上的链接来执行此操作。 请记住，在整个 Azure 环境中传播角色设置最长可能需要五分钟，因此，在添加角色之后，请先等待几分钟再创建存储目标。

1. 为存储帐户打开“访问控制(IAM)”。

1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

1. 分配以下角色，一次分配一个。 有关详细步骤，请查看[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | [存储帐户参与者](../role-based-access-control/built-in-roles.md#storage-account-contributor) <br/>  [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) |
    | 将访问权限分配到 | HPC 缓存资源提供程序 |

    ![“添加角色分配”页](../../includes/role-based-access-control/media/add-role-assignment-page.png)

   > [!NOTE]
   > 如果找不到 HPC 缓存资源提供程序，请改为尝试搜索字符串“storagecache”。 这是服务主体的预发行版名称。

<!-- 
Steps to add the Azure roles:

1. Open the **Access control (IAM)** page for the storage account. (The link in the **Add storage target** page automatically opens this page for the selected account.)

1. Click the **+** at the top of the page and choose **Add a role assignment**.

1. Select the role "Storage Account Contributor" from the list.

1. In the **Assign access to** field, leave the default value selected ("Azure AD user, group, or service principal").  

1. In the **Select** field, search for "hpc".  This string should match one service principal, named "HPC Cache Resource Provider". Click that principal to select it.

   > [!NOTE]
   > If a search for "hpc" doesn't work, try using the string "storagecache" instead. Users who participated in previews (before GA) might need to use the older name for the service principal.

1. Click the **Save** button at the bottom.

1. Repeat this process to assign the role "Storage Blob Data Contributor".  

![screenshot of add role assignment GUI](media/hpc-cache-add-role.png) -->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>先决条件：存储帐户访问权限

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)

在添加 Blob 存储目标之前，请检查缓存是否具有可访问存储帐户的正确角色，以及防火墙设置是否允许创建存储目标。

Azure HPC 缓存使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/index.yml) 来授权缓存服务访问 Azure Blob 存储目标的存储帐户。

存储帐户所有者必须为用户“HPC 缓存资源提供者”显式添加角色“[存储帐户参与者](../role-based-access-control/built-in-roles.md#storage-account-contributor)”和“[存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)”。

如果缓存没有这些角色，创建存储目标将会失败。

在整个 Azure 环境中传播角色设置最长可能需要五分钟，因此，在添加角色之后，请先等待几分钟再创建存储目标。

有关详细说明，请阅读[使用 Azure CLI 添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-cli.md)。

另外请检查存储帐户的防火墙设置。 如果防火墙设置为仅限通过“选定网络”进行访问，则创建存储目标可能会失败。 请使用[解决 Blob 存储帐户防火墙设置问题](hpc-cache-blob-firewall-fix.md)中所述的解决方法。

### <a name="add-a-blob-storage-target-with-azure-cli"></a>使用 Azure CLI 添加 Blob 存储目标

使用 [az hpc-cache blob-storage-target add](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) 接口定义 Azure Blob 存储目标。

> [!NOTE]
> Azure CLI 命令目前要求在添加存储目标时创建命名空间路径。 这与 Azure 门户界面中使用的过程不同。

除了标准的资源组和缓存名称参数以外，还必须提供存储目标的以下选项：

* ``--name`` - 设置一个用于在 Azure HPC 缓存中标识此存储目标的名称。

* ``--storage-account`` - 采用以下格式的帐户标识符：/subscriptions/ *<subscription_id>* /resourceGroups/ *<storage_resource_group>* /providers/Microsoft.Storage/storageAccounts/ *<account_name>*

  有关可以使用的存储帐户类型的信息，请参阅 [Blob 存储要求](hpc-cache-prerequisites.md#blob-storage-requirements)。

* ``--container-name`` - 指定用于此存储目标的容器的名称。

* ``--virtual-namespace-path`` - 设置此存储目标的面向客户端的文件路径。 请将路径括在引号中。 若要详细了解虚拟命名空间功能，请阅读[规划聚合命名空间](hpc-cache-namespace.md)。

示例命令：

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>添加新的 NFS 存储目标

NFS 存储目标的设置与 Blob 存储目标不同，包括指示缓存如何存储来自该存储系统的数据的使用情况模型设置。

![定义 NFS 目标后的“添加存储目标”页的屏幕截图](media/add-nfs-target.png)

> [!NOTE]
> 在创建 NFS 存储目标之前，请确保存储系统可供从 Azure HPC 缓存访问，并满足权限要求。 如果缓存无法访问存储系统，则创建存储目标将会失败。 有关详细信息，请阅读 [NFS 存储要求](hpc-cache-prerequisites.md#nfs-storage-requirements)及[排查 NAS 配置和 NFS 存储目标问题](troubleshoot-nas.md)。

### <a name="choose-a-usage-model"></a>选择使用情况模型

创建使用 NFS 来访问其存储系统的存储目标时，需要为该目标选择使用情况模型。 此模型确定数据的缓存方式。

有关所有这些设置的更多详细信息，请阅读[了解使用情况模型](cache-usage-models.md)。

HPC 缓存的内置使用情况模型可让你选择如何在快速响应与所获数据过时的风险之间实现平衡。 如果你想要优化文件读取速度，那么你可能不会在意是否根据后端文件检查缓存中的文件。 另一方面，若要确保你的文件始终与远程存储中的文件一样保持最新状态，请选择一个频繁执行检查的模型。

> [!NOTE]
> [高吞吐量样式缓存](hpc-cache-create.md#choose-the-cache-type-for-your-needs)仅支持读取缓存。

以下三个选项涵盖了大多数情形：

* **读取量大，写入量少** - 加速对静态或极少更改的文件的读取访问。

  此选项缓存客户端读取的文件，但立即将客户端写入内容传递到后端存储。 不会自动将缓存中存储的文件与 NFS 存储卷上的文件进行比较。

  如果存在可能会未首先将文件写入缓存便直接在存储系统上修改该文件的风险，请不要使用此选项。 如果发生这种情况，该文件的缓存版本将与后端文件失去同步。

* **写入率超过 15%** - 此选项既可提高读取性能，也可提高写入性能。

  客户端读取和客户端写入内容都将缓存。 假设缓存中的文件比后端存储系统上的文件更新。 只会每隔 8 小时根据后端存储上的文件自动检查缓存的文件。 缓存中已修改的文件在缓存中保留 20 分钟且未进一步发生更改后，将写入后端存储系统。

  如有任何客户端直接装载后端存储卷，请不要使用此选项，因为这样存在存储卷中文件过时的风险。

* **客户端写入 NFS 目标并绕过缓存** - 如果工作流中的任何客户端不首先将数据写入缓存便将其直接写入存储系统，或者如果你要优化数据一致性，请选择此选项。

  客户端请求的文件将会缓存，但从客户端对这些文件所做的任何更改将立即传递到后端存储系统。 将频繁根据后端版本检查缓存中的文件以了解内容更新。 如果文件是直接在存储系统上更改的而不是通过缓存更改的，这种验证可以保持数据一致性。

有关其他选项的详细信息，请阅读[了解使用情况模型](cache-usage-models.md)。

下表汇总了所有用法模式之间的差异：

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> “后端验证”值显示缓存何时自动将其文件与远程存储中的源文件进行比较。 但是，可以通过在后端存储系统上发送一个包含 readdirplus 操作的客户端请求来触发比较。 readdirplus 是一个标准的 NFS API（也称为扩展读取），它会返回目录元数据，从而使得缓存比较并更新文件。

### <a name="create-an-nfs-storage-target"></a>创建 NFS 存储目标

### <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中打开你的缓存实例，然后单击左侧边栏中的“存储目标”。

![“设置”>“存储目标”页的屏幕截图，其中的表格列出了两个现有的存储目标，表格上方突出显示了“+ 添加存储目标”按钮](media/add-storage-target-button.png)

“存储目标”页将列出所有现有目标，并提供用于添加新目标的链接。

单击“添加存储目标”按钮。

![定义 NFS 目标后的“添加存储目标”页的屏幕截图](media/add-nfs-target.png)

为支持 NFS 的存储目标提供以下信息：

* **存储目标名称** - 设置一个用于在 Azure HPC 缓存中标识此存储目标的名称。

* **目标类型** - 选择“NFS”。

* **主机名** - 输入 NFS 存储系统的 IP 地址或完全限定的域名。 （仅当缓存可以访问可解析名称的 DNS 服务器时才使用域名。）如果存储系统由多个 IP 引用，则可以输入多个 IP 地址。

* **使用情况模型** - 按照前面的 [选择使用情况模型](#choose-a-usage-model)中所述，根据你的工作流选择一个数据缓存配置文件。

完成后，单击“确定”以添加该存储目标。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)

使用 Azure CLI 命令 [az hpc-cache nfs-storage-target add](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add) 创建存储目标。

> [!NOTE]
> Azure CLI 命令目前要求在添加存储目标时创建命名空间路径。 这与 Azure 门户界面中使用的过程不同。

除了缓存名称和缓存资源组以外，再提供以下值：

* ``--name`` - 设置一个用于在 Azure HPC 缓存中标识此存储目标的名称。
* ``--nfs3-target`` - NFS 存储系统的 IP 地址。 （如果缓存能够访问可解析名称的 DNS 服务器，则在此处可以使用完全限定的域名。）
* ``--nfs3-usage-model`` - 如前面的[选择使用情况模型](#choose-a-usage-model)中所述的数据缓存配置文件之一。

  使用命令 [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list) 验证使用情况模型的名称。

* ``--junction`` - 该接合参数将面向客户端的虚拟文件路径链接到存储系统上的导出路径。

  一个 NFS 存储目标可以有多条虚拟路径，前提是每个路径代表同一存储系统上的不同导出或子目录。 在一个存储目标上创建一个存储系统的所有路径。

  可以随时在存储目标上[添加和编辑命名空间路径](add-namespace-paths.md)。

  ``--junction`` 参数使用以下值：

  * ``namespace-path`` - 面向客户端的虚拟文件路径
  * ``nfs-export`` - 与面向客户端的路径关联的存储系统导出
  * ``target-path``（可选）- 导出的子目录（如果需要）

  示例： ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  若要详细了解虚拟命名空间功能，请阅读[配置聚合命名空间](hpc-cache-namespace.md)。

示例命令：

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

输出：
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target"></a>添加新的 ADLS-NFS 存储目标

ADLS-NFS 存储目标使用支持网络文件系统 (NFS) 3.0 协议的 Azure Blob 容器。

若要详细了解此功能，请阅读 [NFS 3.0 协议支持](../storage/blobs/network-file-system-protocol-support.md)。

ADLS-NFS 存储目标与 Blob 存储目标和 NFS 存储目标之间具有一些相似之处。 例如：

* 与 Blob 存储目标一样，需要向 Azure HPC 缓存授予[访问你的存储帐户](#add-the-access-control-roles-to-your-account)的权限。
* 与 NFS 存储目标一样，需要设置缓存[使用情况模型](#choose-a-usage-model)。
* 由于支持 NFS 的 Blob 容器采用 NFS 兼容的层次结构，因此你无需使用缓存来引入数据，并且这些容器可供其他 NFS 系统读取。

  可以在 ADLS-NFS 容器中预加载数据，再将数据添加到用作存储目标的 HPC 缓存，然后从 HPC 缓存外部访问这些数据。 将标准 Blob 容器用作 HPC 缓存存储目标时，将以专用格式写入数据，只能从 Azure HPC 缓存兼容的其他产品访问这些数据。

必须先创建一个支持 NFS 的存储帐户才能创建 ADLS-NFS 存储目标。 请按照 [Azure HPC 缓存的先决条件](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)中的步骤以及[使用 NFS 装载 Blob 存储](../storage/blobs/network-file-system-protocol-support-how-to.md)中的说明进行操作。 如果缓存和存储帐户没有使用相同的虚拟网络，请确保缓存的 VNet 可以访问存储帐户的 VNet。

设置存储帐户后，可以在创建存储目标时创建新容器。

若要详细了解此配置，请参阅[在 Azure HPC 缓存中使用已装载 NFS 的 blob 存储](nfs-blob-considerations.md)。

若要创建 ADLS-NFS 存储目标，请在 Azure 门户中打开“添加存储目标”页。 （其他方法正在开发中。）

![定义 ADLS-NFS 目标后的“添加存储目标”页的屏幕截图](media/add-adls-target.png)

输入以下信息。

* **存储目标名称** - 设置一个用于在 Azure HPC 缓存中标识此存储目标的名称。
* **目标类型** - 选择“ADLS-NFS”。
* **存储帐户** - 选择要使用的帐户。 如果你的支持 NFS 的存储帐户未出现在列表中，请检查它是否符合先决条件，以及缓存是否可以访问它。

  需要根据[添加访问角色](#add-the-access-control-roles-to-your-account)中所述，授权缓存实例访问存储帐户。

* **存储容器** - 为此目标选择支持 NFS 的 Blob 容器，或单击“新建”。

* **使用情况模型** - 按照前面的 [选择使用情况模型](#choose-a-usage-model)中所述，根据你的工作流选择一个数据缓存配置文件。

完成后，单击“确定”以添加该存储目标。

## <a name="view-storage-targets"></a>查看存储目标

可以使用 Azure 门户或 Azure CLI 显示已为缓存定义的存储目标。

### <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中打开你的缓存实例，然后单击左侧边栏上“设置”标题下的“存储目标”。 “存储目标”页列出了所有现有目标，以及用于添加或删除目标的控件。

单击某个存储目标的名称可打开其详细信息页。

请阅读[查看和管理存储目标](manage-storage-targets.md)和[编辑存储目标](hpc-cache-edit-storage.md)，以了解详细信息。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)

使用 [az hpc-cache storage-target list](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) 选项显示缓存的现有存储目标。 提供缓存名称和资源组（除非已全局设置）。

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

使用 [az hpc-cache storage-target show](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) 查看有关特定存储目标的详细信息。 （按名称指定存储目标。）

示例：

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>后续步骤

创建存储目标后，请继续执行以下任务来使缓存随时可供使用：

* [设置聚合命名空间](add-namespace-paths.md)
* [装载 Azure HPC 缓存](hpc-cache-mount.md)
* [将数据移到 Azure Blob 存储](hpc-cache-ingest.md)

如果需要更新任何设置，可以[编辑存储目标](hpc-cache-edit-storage.md)。
