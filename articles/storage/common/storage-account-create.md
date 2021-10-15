---
title: 创建存储帐户
titleSuffix: Azure Storage
description: 了解如何创建存储帐户以存储 blob、文件、队列和表。 Azure 存储帐户在 Microsoft Azure 中提供唯一的命名空间，用于读取和写入数据。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/18/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a843952e05d71a13a26d56e1588fd10bea0e50c8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275860"
---
# <a name="create-a-storage-account"></a>创建存储帐户

Azure 存储帐户包含所有 Azure 存储数据对象：Blob、文件、队列和表。 存储帐户为你的 Azure 存储数据提供了一个唯一的命名空间，可以从世界上的任何位置通过 HTTP 或 HTTPS 访问该命名空间。 有关 Azure 存储帐户的详细信息，请参阅[存储帐户概述](storage-account-overview.md)。

本操作指南文章介绍如何使用 [Azure 门户](https://portal.azure.com/)、[Azure PowerShell](/powershell/azure/)、[Azure CLI](/cli/azure) 或 [Azure 资源管理器模板](../../azure-resource-manager/management/overview.md)创建存储帐户。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

无。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 创建 Azure 存储帐户，请确保已安装 [Az PowerShell 模块](https://www.powershellgallery.com/packages/Az)（0.7 或更高版本）。 有关详细信息，请参阅 [Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

若要查找当前版本，请运行以下命令：

```powershell
Get-InstalledModule -Name "Az"
```

若要安装或升级 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以登录到 Azure，然后采用以下两种方式之一运行 Azure CLI 命令：

- 可以在 Azure 门户的 Azure Cloud Shell 中运行 CLI 命令。
- 可以安装 CLI 并在本地运行 CLI 命令。

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 已预安装 Azure CLI 并将其配置为与帐户一起使用。 单击 Azure 门户右上角部分菜单上的“Cloud Shell”按钮：

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

该按钮会启动交互式 shell，可以使用它来运行本文中概述的步骤：

[![显示门户中 Cloud Shell 窗口的屏幕截图](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>在本地安装 CLI

也可在本地安装和使用 Azure CLI。 本文中的示例需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 来查找已安装的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

# <a name="template"></a>[模板](#tab/template)

无。

---

接下来，登录到 Azure。

# <a name="portal"></a>[门户](#tab/azure-portal)

登录 [Azure 门户](https://portal.azure.com)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要启动 Azure Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。

若要登录到本地安装的 CLI，请运行 [az login](/cli/azure/reference-index#az_login) 命令：

```azurecli-interactive
az login
```

# <a name="template"></a>[模板](#tab/template)

空值

---

## <a name="create-a-storage-account"></a>创建存储帐户

存储帐户是 Azure 资源管理器资源。 资源管理器是 Azure 的部署和管理服务。 有关详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/management/overview.md)。

每个资源管理器资源（包括 Azure 存储帐户）都必须属于某个 Azure 资源组。 资源组是对 Azure 资源进行分组的逻辑容器。 在创建存储帐户时，可以选择创建新的资源组，也可以使用现有资源组。 此操作说明显示了如何创建新资源组。

# <a name="portal"></a>[门户](#tab/azure-portal)

若要通过 Azure 门户创建 Azure 存储帐户，请执行以下步骤：

1. 在左侧门户菜单中，选择“存储帐户”以显示存储帐户的列表。
1. 在“存储帐户”页上，选择“创建” 。

新存储帐户的选项都被组织到“创建存储帐户”页中的各个选项卡内。 以下各部分介绍了每个选项卡及其选项。

### <a name="basics-tab"></a>“基本信息”选项卡

“基本信息”选项卡上提供了存储帐户的基本信息。 完成“基本信息”选项卡后，可选择设置其他选项卡上的选项来进一步自定义你的新存储帐户，也可以选择“查看 + 创建”以接受默认选项，然后继续验证并创建帐户 。

下表描述了“基本信息”选项卡上的字段。

| 部分 | 字段 | 必需还是可选 | 说明 |
|--|--|--|--|
| 项目详细信息 | 订阅 | 必需 | 选择新存储帐户的订阅。 |
| 项目详细信息 | 资源组 | 必需 | 为此存储帐户创建新的资源组，或选择现有的资源组。 有关详细信息，请参阅[资源组](../../azure-resource-manager/management/overview.md#resource-groups)。 |
| 实例详细信息 | 存储帐户名称 | 必需 | 为存储帐户选择唯一的名称。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 |
| 实例详细信息 | 区域 | 必需 | 为存储帐户选择正确的区域。 有关详细信息，请参阅 [Azure 中的区域和可用性区域](../../availability-zones/az-overview.md)。<br /><br />对于所有类型的存储帐户或冗余配置，并非所有区域都受支持。 有关详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。<br /><br />区域的选择可能会对计费产生影响。 有关详细信息，请参阅[存储帐户计费](storage-account-overview.md#storage-account-billing)。 |
| 实例详细信息 | 性能 | 必需 | 为常规用途 v2 存储帐户选择“标准”性能（默认值）。 在大多数情况下，Microsoft 建议使用此类型帐户。 有关详细信息，请参阅[存储帐户的类型](storage-account-overview.md#types-of-storage-accounts)。<br /><br />对于需要低延迟的情况，请选择“高级”。 选择“高级”后，选择要创建的高级存储帐户的类型。 可以使用以下类型的高级存储帐户： <ul><li>[块 Blob](../blobs/storage-blob-performance-tiers.md)</li><li>[文件共享](../files/storage-files-planning.md#management-concepts)</li><li>[页 Blob](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| 实例详细信息 | 冗余 | 必需 | 选择所需的冗余配置。 并非所有冗余选项都适用于所有区域中的所有存储帐户类型。 有关冗余配置的详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。<br /><br />如果选择异地冗余配置（GRS 或 GZRS），则会将数据复制到不同区域中的数据中心。 对于次要区域中数据的读取访问权限，请选择“在区域不可用的情况下，提供对数据的读取访问”。 |

下图显示了新存储帐户的标准配置。

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="显示新存储帐户的标准配置 -“基本信息”选项卡的屏幕截图":::

### <a name="advanced-tab"></a>高级选项卡

在“高级”选项卡上，可以为新的存储帐户配置其他选项并修改默认设置。 其中的某些选项也可在创建存储帐户后进行配置，而其他选项必须在创建时配置。

下表描述了“高级”选项卡上的字段。

| 部分 | 字段 | 必需还是可选 | 描述 |
|--|--|--|--|
| 安全 | 启用安全传输 | 可选 | 启用安全传输，要求仅通过 HTTPS（默认值）发出对此存储帐户的传入请求。 推荐此设置以获取最佳安全性。 有关详细信息，请参阅[要求采用安全传输以确保安全连接](storage-require-secure-transfer.md)。 |
| 安全性 | 启用基础结构加密 | 可选 | 默认情况下，未启用基础结构加密。 启用基础结构加密，可在服务级别和基础结构级别对数据进行加密。 有关详细信息，请参阅[创建启用了基础结构加密的存储帐户，以便对数据进行双重加密](infrastructure-encryption-enable.md)。 |
| 安全性 | 启用 Blob 公共访问 | 可选 | 启用此设置后，具有相应权限的用户可以启用对存储帐户中容器的匿名公共访问（默认值）。 禁用此设置将阻止对存储帐户进行所有匿名公共访问。 有关详细信息，请参阅[阻止对容器和 Blob 的匿名公共读取访问](../blobs/anonymous-read-access-prevent.md)。<br> <br> 启用 blob 公共访问不会使 blob 数据可供公共访问，除非用户采取额外步骤显式配置了容器的公共访问设置。 |
| 安全性 | 启用存储帐户密钥访问（预览版） | 可选 | 启用此设置后，客户端可使用帐户访问密钥或 Azure Active Directory (Azure AD) 帐户（默认值）向存储帐户授予请求。 禁用此设置将阻止使用帐户访问密钥进行授权。 有关详细信息，请参阅[阻止对 Azure 存储帐户进行共享密钥授权](shared-key-authorization-prevent.md)。 |
| 安全性 | 最低 TLS 版本 | 必需 | 对于存储帐户的传入请求，请选择最低版本的传输层安全性 (TLS)。 默认值为 TLS 版本 1.2。 当设置为默认值时，使用 TLS 1.0 或 TLS 1.1 发出的传入请求将被拒绝。 有关详细信息，请参阅[针对发送到存储帐户的请求强制实施必需的最低版本的传输层安全性 (TLS)](transport-layer-security-configure-minimum-version.md)。 |
| Data Lake Storage Gen2 | 启用分层命名空间 | 可选 | 若要将此存储帐户用于 Azure Data Lake Storage Gen2 工作负载，请配置分层命名空间。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 简介](../blobs/data-lake-storage-introduction.md)。 |
| Blob 存储 | 启用网络文件共享 (NFS) v3 | 可选 | NFS v3 在对象存储规模上提供 Linux 文件系统兼容性，使 Linux 客户端能够从 Azure 虚拟机 (VM) 或本地计算机将容器装载在 Blob 存储中。 有关详细信息，请参阅 [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持](../blobs/network-file-system-protocol-support.md)。 |
| Blob 存储 | 访问层 | 必需 | 使用 Blob 访问层可以根据使用情况以最经济高效的方式存储 blob 数据。 对经常访问的数据选择热层（默认值）。 对不常访问的数据选择冷层。 有关详细信息，请参阅 [Blob 数据的热访问层、冷访问层和存档访问层](../blobs/access-tiers-overview.md)。 |
| Azure 文件 | 启用大型文件共享 | 可选 | 仅适用于使用 LRS 或 ZRS 冗余的标准文件共享。 |
| 表和队列 | 启用对客户管理的密钥的支持 | 可选 | 若要为表和队列启用客户管理的密钥支持，必须在创建存储帐户时选择此设置。 有关详细信息，请参阅[为表和队列创建一个支持客户管理密钥的帐户](account-encryption-key-create.md)。 |

### <a name="networking-tab"></a>“网络”选项卡

在“网络”选项卡上，可以为新的存储帐户配置网络连接和路由首选项设置。 也可在创建存储帐户后配置这些选项。

下表描述了“网络”选项卡上的字段。

| 部分 | 字段 | 必需还是可选 | 说明 |
|--|--|--|--|
| 网络连接 | 连接方法 | 必需 | 默认情况下，传入网络流量会路由到存储帐户的公共终结点。 可以指定必须通过 Azure 虚拟网络将流量路由到公共终结点。 你还可以为你的存储帐户配置专用终结点。 有关详细信息，请参阅[对 Azure 存储使用专用终结点](storage-private-endpoints.md)。 |
| 网络路由 | 路由首选项 | 必需 | 网络路由首选项指定如何通过 Internet 从客户端将网络流量路由到存储帐户的公共终结点。 默认情况下，新的存储帐户使用 Microsoft 网络路由。 还可以选择通过最靠近存储帐户的 POP 路由网络流量，这可能会降低网络成本。 有关详细信息，请参阅 [Azure 存储的网络路由首选项](network-routing-preference.md)。 |

### <a name="data-protection-tab"></a>“数据保护”选项卡

在“数据保护”选项卡上，可以为新存储帐户中的 blob 数据配置数据保护选项。  也可在创建存储帐户后配置这些选项。 有关 Azure 存储中的数据保护选项的概述，请参阅[数据保护概述](../blobs/data-protection-overview.md)。

下表描述了“数据保护”选项卡上的字段。

| 部分 | 字段 | 必需还是可选 | 说明 |
|--|--|--|--|
| 恢复 | 为容器启用时间点还原 | 可选 | 时间点还原支持将块 blob 数据还原到之前的状态，以防止意外删除或损坏。 有关详细信息，请参阅[块 blob 的时间点还原](../blobs/point-in-time-restore-overview.md)。<br /><br />启用时间点还原还会启用 blob 版本控制、blob 软删除和 blob 更改源。 这些必备功能可能会对成本产生影响。 有关详细信息，请参阅时间点还原的[定价和计费](../blobs/point-in-time-restore-overview.md#pricing-and-billing)。 |
| 恢复 | 为 blob 启用软删除 | 可选 | Blob 软删除会于指定的保持期内在系统中保留已删除的数据，以保护单个 blob、快照或版本不被意外删除或覆盖。 在保持期内，可以将软删除的对象还原到删除时的状态。 有关详细信息，请参阅 [blob 软删除](../blobs/soft-delete-blob-overview.md)。<br /><br />Microsoft 建议为你的存储帐户启用 blob 软删除，并将最短保持期设置为七天。 |
| 恢复 | 为容器启用软删除（预览版） | 可选 | 容器软删除会于指定的保持期内在系统中保留已删除的数据，以保护容器及其内容不被意外删除。 在保持期内，可以将软删除的容器还原到删除时的状态。 有关详细信息，请参阅[容器软删除（预览版）](../blobs/soft-delete-container-overview.md)。<br /><br />Microsoft 建议为你的存储帐户启用容器软删除，并将最短保持期设置为七天。 |
| 恢复 | 为文件共享启用软删除 | 可选 | 文件共享软删除会于指定的保持期内在系统中保留已删除的数据，以保护文件共享及其内容不被意外删除。 在保持期内，可以将软删除的文件共享还原到删除时的状态。 有关详细信息，请参阅[防止意外删除 Azure 文件共享](../files/storage-files-prevent-file-share-deletion.md)。<br /><br />Microsoft 建议对 Azure 文件存储工作负载启用文件共享软删除，并将最短保持期设置为七天。 |
| 跟踪 | 为 blob 启用版本控制 | 可选 | 当覆盖 blob 时，blob 版本控制会自动保存之前版本的 blob 的状态。 有关详细信息，请参阅 [Blob 版本控制](../blobs/versioning-overview.md)。<br /><br />Microsoft 建议为存储帐户启用 blob 版本控制以实现最佳数据保护。 |
| 跟踪 | 为 blob 启用更改源 | 可选 | Blob 更改源提供存储帐户中所有 blob 及其元数据的所有更改的事务日志。 有关详细信息，请参阅 [Azure Blob 存储中的更改源支持](../blobs/storage-blob-change-feed.md)。 |

### <a name="tags-tab"></a>“标记”选项卡

在“标记”选项卡上，可以指定资源管理器标记以帮助整理 Azure 资源。 有关详细信息，请参阅[标记资源、资源组和订阅以合理进行整理](../../azure-resource-manager/management/tag-resources.md)。

### <a name="review--create-tab"></a>“查看 + 创建”选项卡

导航到“查看 + 创建”选项卡时，Azure 会对你选择的存储帐户设置运行验证。 如果验证通过，则可以继续创建存储帐户。

如果验证失败，门户将指示需要修改哪些设置。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 创建常规用途 v2 存储帐户，请先调用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建新的资源组：

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

如果不确定为 `-Location` 参数指定哪个区域，可使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 命令检索订阅支持的区域的列表：

```azurepowershell-interactive
Get-AzLocation | select Location
```

接下来，使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 命令通过读取访问异地冗余存储 (RA-GRS) 创建标准常规用途 v2 存储帐户。 请记住，存储帐户的名称必须在 Azure 中唯一，因此请将括号中的占位符值替换为你自己的唯一值：

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

若要为存储帐户启用分层命名空间以使用 [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)，请在调用 New-AzStorageAccount 命令时设置 `EnableHierarchicalNamespace' parameter to `$True`。

下表显示了用于 `SkuName` 和 `Kind` 参数的值，以创建具有所需冗余配置的特定类型的存储帐户。

| 存储帐户的类型 | 受支持的冗余配置 | Kind 参数支持的值 | SkuName 参数支持的值 | 支持分层命名空间 |
|--|--|--|--|--|
| 标准常规用途 v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/Standard_GRS/Standard_RAGRS/Standard_ZRS/Standard_GZRS/Standard_RAGZRS | 是 |
| 高级块 blob | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | 是 |
| 高级文件共享 | LRS/ZRS | FileStorage | Premium_LRS/Premium_ZRS | 否 |
| 高级页 Blob | LRS | StorageV2 | Premium_LRS | 否 |
| 旧的标准常规用途 v1 | LRS/GRS/RA-GRS | 存储 | Standard_LRS/Standard_GRS/Standard_RAGRS | 否 |
| 旧的 blob 存储 | LRS/GRS/RA-GRS | BlobStorage | Standard_LRS/Standard_GRS/Standard_RAGRS | 否 |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建常规用途 v2 存储帐户，请先调用 [az group create](/cli/azure/group#az_group_create) 命令创建新的资源组。

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

如果不确定为 `--location` 参数指定哪个区域，可使用 [az account list-locations](/cli/azure/account#az_account_list) 命令检索订阅支持的区域的列表。

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

接下来，使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令通过读取访问异地冗余存储创建标准常规用途 v2 存储帐户。 请记住，存储帐户的名称必须在 Azure 中唯一，因此请将括号中的占位符值替换为你自己的唯一值：

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

若要为存储帐户启用分层命名空间以使用 [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)，请在调用 az storage account create 命令时将 `enable-hierarchical-namespace` 参数设置为 `true`。 创建分层命名空间需要 Azure CLI 2.0.79 或更高版本。

下表显示了用于 `sku` 和 `kind` 参数的值，以创建具有所需冗余配置的特定类型的存储帐户。

| 存储帐户的类型 | 受支持的冗余配置 | kind 参数支持的值 | sku 参数支持的值 | 支持分层命名空间 |
|--|--|--|--|--|
| 标准常规用途 v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/Standard_GRS/Standard_RAGRS/Standard_ZRS/Standard_GZRS/Standard_RAGZRS | 是 |
| 高级块 blob | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | 是 |
| 高级文件共享 | LRS/ZRS | FileStorage | Premium_LRS/Premium_ZRS | 否 |
| 高级页 Blob | LRS | StorageV2 | Premium_LRS | 否 |
| 旧的标准常规用途 v1 | LRS/GRS/RA-GRS | 存储 | Standard_LRS/Standard_GRS/Standard_RAGRS | 否 |
| 旧的 blob 存储 | LRS/GRS/RA-GRS | BlobStorage | Standard_LRS/Standard_GRS/Standard_RAGRS | 否 |

# <a name="template"></a>[模板](#tab/template)

可以使用 Azure PowerShell 或 Azure CLI 来部署资源管理器模板以创建存储帐户。 本操作指南文章中使用的模板来自 [Azure 资源管理器快速入门模板](https://azure.microsoft.com/resources/templates/storage-account-create/)。 若要运行脚本，请选择“试用”打开 Azure Cloud Shell。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

> [!NOTE]
> 此模板仅用作示例。 有许多存储帐户设置未在此模板中进行配置。 例如，如果想要使用 [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)，可将 `StorageAccountPropertiesCreateParameters` 对象的 `isHnsEnabledad` 属性设为 `true` 来修改此模板。

若要了解如何修改此模板或创建新模板，请参阅：

- [Azure 资源管理器文档](../../azure-resource-manager/index.yml)。
- [存储帐户模板参考](/azure/templates/microsoft.storage/allversions)。
- [其他存储帐户模板示例](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)。

---

## <a name="delete-a-storage-account"></a>删除存储帐户

删除存储帐户将删除整个帐户，包括该帐户中的所有数据。 在删除帐户之前请务必备份要保存的任何数据。

在某些情况下，可以恢复已删除的存储帐户，但不能保证所有情况下都可以恢复已删除的存储帐户。 有关详细信息，请参阅[恢复已删除的存储帐户](storage-account-recover.md)。

如果尝试删除与 Azure 虚拟机关联的存储帐户，则会显示一条错误消息，指出存储帐户仍在使用。 有关排除此错误的帮助信息，请参阅[删除存储帐户时排除错误](/troubleshoot/azure/virtual-machines/storage-resource-deletion-errors)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)中导航到存储帐户。
1. 单击 **“删除”** 。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要删除存储帐户，请使用 [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) 命令：

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要删除存储帐户，请使用 [az storage account delete](/cli/azure/storage/account#az_storage_account_delete) 命令：

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[模板](#tab/template)

若要删除存储帐户，请使用 Azure PowerShell 或 Azure CLI。

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

或者，你可以删除资源组，该操作将删除该资源组中的存储帐户和其他任何资源。 有关删除资源组的详细信息，请参阅[删除资源组和资源](../../azure-resource-manager/management/delete-resource-group.md)。

## <a name="next-steps"></a>后续步骤

- [存储帐户概述](storage-account-overview.md)
- [升级到常规用途 v2 存储帐户](storage-account-upgrade.md)
- [将存储帐户移到另一个区域](storage-account-move.md)
- [恢复删除的存储帐户](storage-account-recover.md)
