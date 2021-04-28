---
title: 使用 NFS 3.0 协议装载 Azure Blob 存储（预览版）| Microsoft Docs
description: 了解如何使用 NFS 3.0 协议从 Azure 虚拟机 (VM) 或本地运行的客户端在 Blob 存储中装载容器。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: bf6b06ba7cc7f547f752ffa7877fca186ba4465e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713779"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>使用网络文件系统 (NFS) 3.0 协议（预览版）装载 Blob 存储

可以使用 NFS 3.0 协议从基于 Linux 的 Azure 虚拟机 (VM) 或在本地运行的 Linux 系统，在 Blob 存储中装载容器。 本文提供分步指南。 若要了解有关 Blob 存储中 NFS 3.0 协议支持的详细信息，请参阅 [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持（预览版）](network-file-system-protocol-support.md)。

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>步骤 1：将 NFS 3.0 协议功能注册到你的订阅中

1. 打开 PowerShell 命令窗口。 

2. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

3. 如果你的标识关联到多个订阅，请设置你的活动订阅。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

4. 使用以下命令注册 `AllowNFSV3` 功能。

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. 使用以下命令注册资源提供程序。
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>步骤 2：验证是否已注册功能 

注册审批可能需要一个小时。 若要验证注册是否完成，请使用以下命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>步骤 3：创建 Azure 虚拟网络 (VNet)

存储帐户必须包含在 VNet 中。 VNet 使客户端可以安全地连接到存储帐户。 若要详细了解 VNet 以及如何创建 VNet，请参阅[虚拟网络文档](../../virtual-network/index.yml)。

> [!NOTE]
> 同一 VNet 中的客户端可以在你的帐户中装载容器。 你还可以从在本地网络中运行的客户端装载容器，但必须先将本地网络连接到 VNet。 请参阅[受支持的网络连接](network-file-system-protocol-support.md#supported-network-connections)。

## <a name="step-4-configure-network-security"></a>步骤 4：配置网络安全

保护帐户中数据的唯一方法是使用 VNet 和其他网络安全设置。 用于保护数据的任何其他工具（包括帐户密钥授权、Azure Active Directory [AD] 安全性）以及访问控制列表 (ACL) 在其上启用了 NFS 3.0 协议支持的帐户中不受支持。

若要保护帐户中的数据，请参阅以下建议：[针对 Blob 存储的网络安全建议](security-recommendations.md#networking)。

## <a name="step-5-create-and-configure-a-storage-account"></a>步骤 5：创建和配置存储帐户

若要使用 NFS 3.0 装载容器，必须在将该功能注册到订阅后创建存储帐户。 你无法启用注册该功能之前已存在的帐户。

在此功能的预览版本中，标准常规用途 v2 存储帐户和高级块 blob 存储帐户支持 NFS 3.0 协议。 有关这些类型的存储帐户的详细信息，请参阅[存储帐户概述](../common/storage-account-overview.md)。

配置帐户时，请选择以下值：

|设置 | 高级性能层 | 标准性能  
|----|---|---|
|位置|所有可用区域 |以下区域之一：澳大利亚东部、韩国中部、美国东部和美国中南部   
|性能|高级| 标准
|帐户种类|BlockBlobStorage| 常规用途 V2
|复制|本地冗余存储 (LRS)| 本地冗余存储 (LRS)
|连接方法|公共终结点（选定网络）或专用终结点 |公共终结点（选定网络）或专用终结点
|需要安全传输|已禁用|已禁用
|分层命名空间|Enabled|Enabled
|NFS V3|Enabled |Enabled 

对于所有其他设置，可接受默认值。 

## <a name="step-6-create-a-container"></a>步骤 6：创建容器

使用以下任意工具或 SDK 在存储帐户中创建容器：

|工具|SDK|
|---|---|
|[Azure 门户](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>步骤 7：装载容器

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

## <a name="resolve-common-issues"></a>解决常见问题

|问题/错误 | 解决方法|
|---|---|
|`Access denied by server while mounting`|请确保客户端在支持的子网中运行。 请查看[受支持的网络位置](network-file-system-protocol-support.md#supported-network-connections)。|
|`No such file or directory`| 请确保在验证功能已注册后创建要装载的容器。 请参阅[步骤 2：验证是否已注册功能](#step-2-verify-that-the-feature-is-registered)。另外，请确保将装载命令及其参数直接键入终端中。 如果你将该命令的任何部分从另一个应用程序复制并粘贴到终端，粘贴的信息中的隐藏字符可能会导致出现此错误。|

## <a name="see-also"></a>请参阅

[Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持（预览版）](network-file-system-protocol-support.md)
