---
title: Azure 文件存储中的新增功能
description: 详细了解 Azure 文件存储中的新增功能和增强功能。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bc44ba9bc2cad45ab447b86a580fb505c8abd6d
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273224"
---
# <a name="whats-new-in-azure-files"></a>Azure 文件存储中的新增功能
Azure 文件存储定期更新，以提供新增功能和增强功能。 本文详细介绍 Azure 文件存储中的新增功能。

## <a name="2021-quarter-3-july-august-september"></a>2021 年第 3 季度（7 月、8 月、9 月）
### <a name="smb-multichannel-is-generally-available"></a>SMB 多通道已正式发布
SMB 多通道使 SMB 客户端能够与 Azure 文件共享建立多个并行连接。 这允许 SMB 客户端充分利用所有可用的网络带宽，并使它们能够从网络故障复原，从而降低总拥有成本并通过单个客户端实现 2-3 倍的读取速度和 3-4 倍的写入速度。 SMB 多通道可用于高级文件共享（部署在 FileStorage 存储帐户类型中的文件共享），默认情况下处于禁用状态。 

有关详细信息，请参阅：

- [Azure 文件存储中的 SMB 多通道性能](storage-files-smb-multichannel-performance.md)
- [启用 SMB 多通道](files-smb-protocol.md#smb-multichannel)
- [Windows Server 中的 SMB 多通道概述文档](/azure-stack/hci/manage/manage-smb-multichannel)

### <a name="smb-311-and-smb-security-settings"></a>SMB 3.1.1 和 SMB 安全设置
SMB 3.1.1 是 SMB 协议的最新版本，随 Windows 10 一起发布，其中包含重要的安全更新和性能更新。 除了已经支持的 AES-128-CCM 之外，Azure 文件存储 SMB 3.1.1 还附带了另外两种加密模式：AES-128-GCM 和 AES-256-GCM。 为了实现最佳性能，AES-128-GCM 被协商为默认的 SMB 通道加密选项；AES-128-CCM 仅在不支持 AES-128-GCM 的旧客户端上进行协商。 

根据组织的法规和合规性要求，可以通过限制 SMB 客户端上和/或 Azure 文件存储中允许的 SMB 通道加密选项来协商 AES-256-GCM（而不是 AES-128-GCM）。 在 Windows Server 2022 和 Windows 10 版本 21H1 中添加了对 AES-256-GCM 的支持。

除了 SMB 3.1.1，Azure 文件存储还公开了更改 SMB 协议行为的安全设置。 使用此版本，可以配置允许的 SMB 协议版本、SMB 通道加密选项、身份验证方法和 Kerberos 票证加密选项。 默认情况下，Azure 文件存储启用最兼容的选项，但是这些选项可以随时切换。

有关详细信息，请参阅：

- [SMB 安全设置](files-smb-protocol.md#smb-security-settings)
- [Windows](storage-how-to-use-files-windows.md) 和 [Linux](storage-how-to-use-files-linux.md) SMB 版本信息
- [Windows Server 中的 SMB 功能概述文档](/windows-server/storage/file-server/file-server-smb-overview)

## <a name="2021-quarter-2-april-may-june"></a>2021 年第 2 季度（4 月、5 月、6 月）
### <a name="premium-hot-and-cool-storage-capacity-reservations"></a>高级、热和冷存储容量预留 
Azure 文件存储支持存储容量预留（也称为“预留实例”）。 存储容量预留允许用户通过预先承诺存储利用率来享受存储折扣。 Azure 文件存储支持高级层、热层和冷层中的容量预留。 容量预留以 10 TiB 或 100 TiB 为单位出售，期限为一年或三年。 

有关详细信息，请参阅：

- [了解 Azure 文件存储计费](understanding-billing.md)
- [使用预留容量优化 Azure 文件存储的成本](files-reserve-capacity.md)
- [Azure 文件存储定价](https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="improved-portal-experience-for-domain-joining-to-active-directory"></a>改进了域加入 Active Directory 的门户体验
域加入 Azure 存储帐户的体验已进行改进，可帮助引导首次使用 Azure 文件共享的管理员完成此过程。 当在 Azure 门户的“文件共享”部分的“文件共享设置”下选择 Active Directory 时，将引导你完成域加入所需的步骤 。

:::image type="content" source="media/files-whats-new/ad-domain-join-1.png" alt-text="域加入 Active Directory 存储帐户的新门户体验的屏幕截图" lightbox="media/files-whats-new/ad-domain-join-1.png":::

有关详细信息，请参阅：

- [实现 SMB 访问的 Azure 文件存储基于标识的身份验证选项概述](storage-files-active-directory-overview.md)
- [概述 - 通过 SMB 为 Azure 文件共享启用本地 Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-enable.md)

## <a name="2021-quarter-1-january-february-march"></a>2021 年第 1 季度（1 月、2 月、3 月）
### <a name="azure-files-management-now-available-through-the-control-plane"></a>Azure 文件存储管理现通过控制平面提供
Azure 文件存储资源、文件服务和文件共享的管理 API 现通过控制平面（`Microsoft.Storage` 资源提供程序）提供。 这样就可以使用 Azure 资源管理器或 Bicep 模板创建 Azure 文件共享，以在数据平面（即 FileREST API）不可访问时（如存储帐户的公共终结点被禁用时）对其进行完全管理，并支持基于角色的完整访问控制 (RBAC) 语义。

在大多数情况下，建议通过控制平面管理 Azure 文件存储。 为了支持通过控制平面管理文件服务和文件共享，Azure 门户、Azure 存储 PowerShell 模块和 Azure CLI 已进行更新，从而支持通过控制平面执行大多数管理操作。 

为了保留现有的脚本行为，Azure 存储 PowerShell 模块和 Azure CLI 保留了使用数据平面来管理文件服务和文件共享的现有命令，并添加了新命令来使用控制平面。 门户请求仅通过控制平面资源提供程序。 PowerShell 和 CLI 命令的名称如下所示：

- Az.Storage PowerShell：
    - 控制平面文件共享 cmdlet 的前缀为 `Rm`，例如：`New-AzRmStorageShare`、`Get-AzRmStorageShare`、`Update-AzRmStorageShare` 和 `Remove-AzRmStorageShare`。 
    - 传统数据平面文件共享 cmdlet 没有前缀，例如 `New-AzStorageShare`、`Get-AzStorageShare`、`Set-AzStorageShareQuota` 和 `Remove-AzStorageShare`。
    - 用于管理文件服务的 cmdlet 仅通过控制平面提供，并且没有任何特殊前缀，例如 `Get-AzStorageFileServiceProperty` 和 `Update-AzStorageFileServiceProperty`。
- Azure 存储 CLI：
    - 控制平面文件共享命令在 `az storage share-rm` 命令组下提供，例如：`az storage share-rm create`、`az storage share-rm update` 等。
    - 传统文件共享命令在 `az storage share` 命令组下提供，例如：`az storage share create`、`az storage share update` 等。
    - 用于管理文件服务的命令仅通过控制平面提供，并且可以通过 `az storage account file-service-properties` 命令组提供，例如：`az storage account file-service-properties show` 和 `az storage account file-service-properties update`。

要详细了解 Azure 文件存储管理 API，请参阅：

- [Azure 文件存储 REST API 概述](/rest/api/storageservices/file-service-rest-api)
- Azure 文件存储资源的控制平面（`Microsoft.Storage` 资源提供程序）API： 
    - [`FileService`](/rest/api/storagerp/file-services) 
    - [`FileShare`](/rest/api/storagerp/file-shares) 
- [Azure PowerShell](/powershell/module/az.storage) 和 [Azure CLI](/en-us/cli/azure/storage)

## <a name="2020-quarter-4-october-november-december"></a>2020 年第 4 季度（10 月、11 月、12 月）
### <a name="azure-file-share-soft-delete"></a>Azure 文件共享软删除
Azure 文件共享支持软删除。 启用软删除后，如果在用户定义的保留期内取消删除，可以轻松恢复意外删除的共享。 在保留期内，软删除的共享按共享快照费率产生数据已用容量的费用。

要详细了解软删除，请参阅：

- [软删除概述](storage-files-prevent-file-share-deletion.md)
- [启用软删除](storage-files-enable-soft-delete.md)
- [Azure 文件存储定价](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="see-also"></a>另请参阅
- [什么是 Azure 文件存储？](storage-files-introduction.md)
- [规划 Azure 文件存储部署](storage-files-planning.md)
- [创建 Azure 文件共享](storage-how-to-create-file-share.md)
