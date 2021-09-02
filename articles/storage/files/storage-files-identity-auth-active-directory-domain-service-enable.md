---
title: 使用 Azure AD 域服务通过 SMB 授权对文件数据的访问
description: 了解如何借助 Azure Active Directory 域服务，通过服务器消息块 (SMB) 为 Azure 文件存储启用基于标识的身份验证。 然后，已加入域的 Windows 虚拟机 (VM) 可以通过使用 Azure AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f995b4b17330f6469f05c5399c2129fa7abc33bf
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462537"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>在 Azure 文件存储上启用 Azure Active Directory 域服务身份验证

[Azure 文件存储](storage-files-introduction.md) 借助以下两种类型的域服务，通过服务器消息块 (SMB) 支持基于标识的身份验证：本地 Active Directory 域服务 (AD DS) 和 Azure Active Directory (Azure AD DS)。强烈建议查看[“工作原理”部分](./storage-files-active-directory-overview.md#how-it-works)，以选择适当的域服务进行身份验证。 选择域服务不同，设置也会不同。 本文重点介绍如何启用和配置 Azure AD DS，以便通过 Azure 文件共享进行身份验证。

如果你不熟悉 Azure 文件共享，建议在阅读以下系列文章之前阅读我们的[规划指南](storage-files-planning.md)。

> [!NOTE]
> Azure 文件存储支持通过具有 RC4-HMAC 和 AES-256 加密的 Azure AD DS 进行 Kerberos 身份验证。
>
> Azure 文件存储支持通过与 Azure AD 完全同步对 Azure AD DS 进行身份验证。 如果已在 Azure AD DS 中启用仅同步 Azure AD 的一组有限标识的作用域内同步，则不支持身份验证和授权。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>先决条件

在通过 SMB 为 Azure 文件共享启用 Azure AD 之前，请确保满足以下先决条件：

1.  **选择或创建 Azure AD 租户。**

    可以使用新的或现有的租户进行通过 SMB 的 Azure AD 身份验证。 要访问的租户和文件共享必须与同一订阅相关联。

    若要创建一个新的 Azure AD 租户，可以[添加 Azure AD 租户和 Azure AD 订阅](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果已有一个 Azure AD 租户，但想要创建新租户以便与 Azure 文件共享一同使用，请参阅[创建 Azure Active Directory 租户](/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

1.  **启用 Azure AD 租户上的 Azure AD 域服务。**

    若要支持使用 Azure AD 凭据进行身份验证，必须启用 Azure AD 租户的 Azure AD 域服务。 如果你不是 Azure AD 租户的管理员，请与管理员联系并按照分步指南操作，以[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance.md)。

    Azure AD DS 部署通常需要大约 15 分钟才能完成。 在继续执行下一步之前，请验证 Azure AD DS 的运行状况状态是否显示“正在运行”，以及是否启用了密码哈希同步。

1.  **使用 Azure AD DS 将 Azure VM 加入域。**

    要使用来自 VM 的 Azure AD 凭据访问文件共享，你的 VM 必须已通过 Azure AD DS 加入域。 有关如何将 VM 加入域的详细信息，请参阅[将 Windows Server 虚拟机加入托管域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > 只有在运行于高于 Windows 7 或 Windows Server 2008 R2 版本的 OS 上的 Azure VM 上才支持通过 SMB 为 Azure 文件共享启用 Azure AD DS 身份验证。

1.  **选择或创建 Azure 文件共享。**

    选择与 Azure AD 租户相同订阅关联的新的或现有文件共享。 有关创建新的文件共享的信息，请参阅[在 Azure 文件中创建文件共享](storage-how-to-create-file-share.md)。
    为获得最佳性能，建议将文件共享与计划访问共享的 VM 放置在同一区域。

1.  **通过使用存储帐户密钥装载 Azure 文件共享来验证 Azure 文件连接。**

    要验证是否已正确配置 VM 和文件共享，请尝试使用存储帐户密钥装载文件共享。 有关详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>区域可用性

使用 Azure AD DS 的 Azure 文件存储身份验证在[所有 Azure 公共区域、Gov 区域和中国区域](https://azure.microsoft.com/global-infrastructure/locations/)都可用。

## <a name="overview-of-the-workflow"></a>工作流概述

通过 SMB 为 Azure 文件共享启用 Azure AD DS 身份验证之前，请验证已正确配置 Azure AD 和 Azure 存储环境。 建议逐步完成[先决条件](#prerequisites)，确保已完成所有必需步骤。

接下来，执行以下操作授予使用 Azure AD 凭据访问 Azure 文件存储资源的权限：

1. 通过 SMB 为存储帐户启用 Azure AD DS 身份验证，使用关联的 Azure AD DS 部署注册存储帐户。
2. 将共享的访问权限分配给 Azure AD 标识（用户、组或服务主体）。
3. 通过 SMB 为目录和文件配置 NTFS 权限。
4. 从加入域的 VM 装载 Azure 文件共享。

下图说明了通过 SMB 为 Azure 文件存储启用 Azure AD DS 身份验证的端到端工作流。

![显示通过 SMB 为 Azure 文件启用 Azure AD 的工作流的图表](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>为帐户启用 Azure AD DS 身份验证

若要通过 SMB 为 Azure 文件存储启用 Azure AD DS 身份验证，可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 为存储帐户设置属性。 设置此属性会通过关联的 Azure AD DS 部署隐式“域加入”存储帐户。 然后，为存储帐户中的所有新文件和现有文件共享启用通过 SMB 的 Azure AD DS 身份验证。

请注意，只有在将 Azure AD DS 成功部署到 Azure AD 租户后，才能通过 SMB 启用 Azure AD DS 身份验证。 有关详细信息，请参阅[先决条件](#prerequisites)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 [Azure 门户](https://portal.azure.com)通过 SMB 启用 Azure AD DS 身份验证，请执行以下步骤：

1. 在 Azure 门户中，转到现有的存储帐户或[创建存储帐户](../common/storage-account-create.md)。
1. 在“文件共享”部分中，选择“Active Directory：未配置”。

    :::image type="content" source="media/storage-files-active-directory-enable/files-azure-ad-enable-storage-account-identity.png" alt-text="屏幕截图：存储帐户中的“文件共享”窗格，其中突出显示了“Active Directory”。" lightbox="media/storage-files-active-directory-enable/files-azure-ad-enable-storage-account-identity.png":::

1. 选择“Azure Active Directory 域服务”，然后切换到“已启用”。
1. 选择“保存”。

    :::image type="content" source="media/storage-files-active-directory-enable/files-azure-ad-highlight.png" alt-text="屏幕截图：“Active Directory”窗格，其中 Azure Active Directory 域服务已启用。" lightbox="media/storage-files-active-directory-enable/files-azure-ad-highlight.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 通过 SMB 启用 Azure AD DS 身份验证，请安装最新的 Az 模块（2.4 或更高版本）或 Az.Storage 模块（1.5 或更高版本）。 有关如何安装 PowerShell 的详细信息，请参阅[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](/powershell/azure/install-Az-ps)。

若要创建新的存储帐户，请调用 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)，然后将 EnableAzureActiveDirectoryDomainServicesForFile 参数设置为 true。 在下面的示例中，请务必将占位符值替换为你自己的值。 （如果你使用的是以前的预览模块，则用于启用该功能的参数为 EnableAzureFilesAadIntegrationForSMB。）

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

若要在现有存储帐户上启用此功能，请使用以下命令：

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 通过 SMB 启用 Azure AD 身份验证，请安装最新的 CLI 版本（版本 2.0.70 或更高版本）。 有关安装 Azure CLI 的详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要创建新的存储帐户，请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_create)，并将 `--enable-files-aadds` 属性设置为 true。 在下面的示例中，请务必将占位符值替换为你自己的值。 （如果你使用的是以前的预览模块，则用于功能启用的参数为 file-aad。）

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

若要在现有存储帐户上启用此功能，请使用以下命令：

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

现在，你已经成功地通过 SMB 启用了 Azure AD DS 身份验证，并分配了一个自定义角色，该角色提供对具有 Azure AD 标识的 Azure 文件共享的访问。 若要授予其他用户对文件共享的访问权限，请按照[分配访问权限](#assign-access-permissions-to-an-identity)中的说明使用标识，并[通过 SMB 部分配置 NTFS 权限](#configure-ntfs-permissions-over-smb)。

## <a name="next-steps"></a>后续步骤

有关 Azure 文件存储以及如何通过 SMB 使用 Azure AD 的详细信息，请参阅以下资源：

- [支持 SMB 访问的 Azure 文件存储基于标识的身份验证概述](storage-files-active-directory-overview.md)
- [常见问题解答](storage-files-faq.md)
