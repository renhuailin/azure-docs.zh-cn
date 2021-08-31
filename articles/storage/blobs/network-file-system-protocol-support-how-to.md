---
title: 使用 NFS 3.0 协议装载 Azure Blob 存储 | Microsoft Docs
description: 了解如何使用 NFS 3.0 协议从 Azure 虚拟机 (VM) 或本地运行的客户端在 Blob 存储中装载容器。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7173477e01faddb3c8aa3c18dc2781ef21dee012
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740160"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol"></a>使用网络文件系统 (NFS) 3.0 协议装载 Blob 存储

可以使用 NFS 3.0 协议从基于 Linux 的 Azure 虚拟机 (VM) 或在本地运行的 Linux 系统，在 Blob 存储中装载容器。 本文提供分步指南。 若要详细了解 Blob 存储中的 NFS 3.0 协议支持，请参阅 [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持](network-file-system-protocol-support.md)。

## <a name="step-1-create-an-azure-virtual-network-vnet"></a>步骤 1：创建 Azure 虚拟网络 (VNet)

存储帐户必须包含在 VNet 中。 VNet 使客户端可以安全地连接到存储帐户。 若要详细了解 VNet 以及如何创建 VNet，请参阅[虚拟网络文档](../../virtual-network/index.yml)。

> [!NOTE]
> 同一 VNet 中的客户端可以在你的帐户中装载容器。 你还可以从在本地网络中运行的客户端装载容器，但必须先将本地网络连接到 VNet。 请参阅[受支持的网络连接](network-file-system-protocol-support.md#supported-network-connections)。

## <a name="step-2-configure-network-security"></a>步骤 2：配置网络安全

保护帐户中数据的唯一方法是使用 VNet 和其他网络安全设置。 用于保护数据的任何其他工具（包括帐户密钥授权、Azure Active Directory [AD] 安全性）以及访问控制列表 (ACL) 在其上启用了 NFS 3.0 协议支持的帐户中不受支持。

若要保护帐户中的数据，请参阅以下建议：[针对 Blob 存储的网络安全建议](security-recommendations.md#networking)。

## <a name="step-3-create-and-configure-a-storage-account"></a>步骤 3：创建和配置存储帐户

若要使用 NFS 3.0 装载容器，必须创建一个存储帐户。 不能启用现有帐户。

标准常规用途 v2 存储帐户和高级块 blob 存储帐户支持 NFS 3.0 协议。 有关这些类型的存储帐户的详细信息，请参阅[存储帐户概述](../common/storage-account-overview.md)。

配置帐户时，请选择以下值：

|设置 | 高级性能层 | 标准性能  
|----|---|---|
|位置|所有可用区域 |所有可用区域    
|性能|高级| 标准
|帐户种类|BlockBlobStorage| 常规用途 V2
|复制|本地冗余存储 (LRS)、区域冗余存储 (ZRS)| 本地冗余存储 (LRS)、区域冗余存储 (ZRS)
|连接方法|公共终结点（选定网络）或专用终结点 |公共终结点（选定网络）或专用终结点
|分层命名空间|Enabled|Enabled
|NFS V3|Enabled |Enabled 

对于所有其他设置，可接受默认值。 

## <a name="step-4-create-a-container"></a>步骤 4：创建容器

使用以下任意工具或 SDK 在存储帐户中创建容器：

|工具|SDK|
|---|---|
|[Azure 门户](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

> [!NOTE]
> 默认情况下，新容器的根 Squash 选项为 `no root squash`。 但可以将其更改为 `root squash` 或 `all squash`。 有关这些 Squash 选项的信息，请参阅操作系统文档。

下图展示了在 Azure 门户中显示的 Squash 选项。

> [!div class="mx-imgBorder"]
> ![Azure 门户中的 Squash 选项](./media/network-file-system-protocol-how-to/squash-options-azure-portal.png)

## <a name="step-5-mount-the-container"></a>步骤 5：装载容器

在 Linux 系统上创建一个目录，然后在存储帐户中装载一个容器。

1. 在 Linux 系统上，创建一个目录。

   ```
   mkdir -p /mnt/test
   ```

2. 使用以下命令装载容器。

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - 将此命令中出现的 `<storage-account-name>` 占位符替换为存储帐户的名称。  

   - 请将 `<container-name>` 占位符替换为容器的名称。

---

## <a name="resolve-common-errors"></a>解决常见错误

|错误 | 原因/解决方法|
|---|---|
|`Access denied by server while mounting`|请确保客户端在支持的子网中运行。 请查看[受支持的网络位置](network-file-system-protocol-support.md#supported-network-connections)。|
|`No such file or directory`| 确保将装载命令及其参数直接键入终端。 如果你将该命令的任何部分从另一个应用程序复制并粘贴到终端，粘贴的信息中的隐藏字符可能会导致出现此错误。|
|`Permision denied`| 新创建的 NFS v3 容器的默认模式为 0750。 非根用户无法访问卷。 如果需要从非根用户访问，根用户必须将模式更改为 0755。 示例命令：`sudo chmod 0755 /mnt/<newcontainer>`|
|`EINVAL ("Invalid argument"`) |当客户端尝试执行以下操作时，可能会出现此错误：<li>写入从 blob 终结点创建的 blob。<li>删除具有快照或位于具有活动 WORM（一次写入，多次读取）策略的容器中的 blob。|
|`EROFS ("Read-only file system"`) |当客户端尝试执行以下操作时，可能会出现此错误：<li>写入 blob 或删除具有活动租约的 blob。<li>写入 blob 或删除具有活动 WORM（一次写入，多次读取）策略的容器中的 blob。 |
|`NFS3ERR_IO/EIO ("Input/output error"`) |当客户端尝试读取、写入或设置存储在存档访问层中的 blob 的属性时，可能会出现此错误。 |
|`OperationNotSupportedOnSymLink` 个错误| 在通过 blob 或 Azure Data Lake Storage Gen2 API 执行写入操作时，可能会返回此错误。 不允许使用这些 API 写入或删除使用 NFS 3.0 创建的符号链接。 请确保使用 NFS v3 终结点处理符号链接。 |

## <a name="see-also"></a>另请参阅

- [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持](network-file-system-protocol-support.md)
- [Azure Blob 存储中网络文件系统 (NFS) 3.0 协议支持的已知问题](network-file-system-protocol-known-issues.md)
