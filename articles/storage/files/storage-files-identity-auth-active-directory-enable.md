---
title: 概述 - Azure 文件共享的本地 AD DS 身份验证
description: 了解 Azure 文件共享的 Active Directory 域服务 (AD DS) 身份验证。 本文介绍了支持方案和可用性，并说明了在 AD DS 和 Azure Active Directory 之间权限的工作原理。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 03/15/2021
ms.author: rogarana
ms.openlocfilehash: 585199f1898d3e2857f69c5c62fdfe4d07448bd5
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118082"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>概述 - 通过 SMB 为 Azure 文件共享启用本地 Active Directory 域服务身份验证

[Azure 文件存储](storage-files-introduction.md) 支持通过下面两种类型的域服务经由服务器消息块 (SMB) 进行基于标识的身份验证：本地 Active Directory 域服务 (AD DS) 和 Azure Active Directory 域服务 (Azure AD DS)。 强烈建议查看[工作原理部分](./storage-files-active-directory-overview.md#how-it-works)，选择合适的域服务来进行身份验证。 选择的域服务不同，设置也会不同。 本系列的文章重点介绍如何启用和配置本地 AD DS 来对 Azure 文件共享进行身份验证。

如果不熟悉 Azure 文件共享，建议先阅读[规划指南](storage-files-planning.md)，然后再阅读下述系列的文章。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="supported-scenarios-and-restrictions"></a>支持的方案和限制

- 用于 Azure 文件存储本地 AD DS 身份验证的 AD DS 标识必须同步到 Azure AD。 密码哈希同步是可选项。 
- 支持由 Azure 文件同步管理的 Azure 文件共享。
- 支持通过具有 RC4-HMAC 和 [AES 256 加密](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)的 AD 进行 Kerberos 身份验证。 AES 256 加密当前仅支持名称长度不超过 15 个字符的存储帐户。 尚不支持 AES 128 Kerberos 加密。
- 支持单一登录体验。
- 仅支持在版本高于 Windows 7 或 Windows Server 2008 R2 的操作系统上运行的客户端。
- 仅支持有相应存储帐户注册的 AD 林。 默认情况下，只能使用 AD DS 凭据从单个林访问 Azure 文件共享。 如需从不同的林访问 Azure 文件共享，请确保已配置适当的林信任。有关详细信息，请查看[常见问题解答](storage-files-faq.md#ad-ds--azure-ad-ds-authentication)。
- 不支持对 AD DS 中创建的计算机帐户进行身份验证。
- 不支持对网络文件系统 (NFS) 文件共享进行身份验证。

通过 SMB 为 Azure 文件共享启用 AD DS 后，已加入 AD DS 的计算机可使用现有的 AD DS 凭据装载 Azure 文件共享。 可在本地计算机或 Azure 中托管的 AD DS 环境中启用此功能。

## <a name="videos"></a>视频

为了帮助你对一些常见用例设置 Azure 文件存储 AD 身份验证，我们发布了两个视频，它们对以下场景提供分步指南：

| 将本地文件服务器替换为 Azure 文件存储（包括设置文件专用链接和 AD 身份验证） | 将 Azure 文件存储用作 Windows 虚拟桌面的配置文件容器（包括设置 AD 身份验证和 FsLogix 配置）  |
|-|-|
| [![“替换本地文件服务器”视频的截屏 - 单击播放。](./media/storage-files-identity-auth-active-directory-enable/replace-on-prem-server-thumbnail.png)](https://www.youtube.com/watch?v=jd49W33DxkQ) | [![“将 Azure 文件存储用作配置文件容器”视频的截屏 - 单击播放。](./media/storage-files-identity-auth-active-directory-enable/files-ad-ds-fslogix-thumbnail.png)](https://www.youtube.com/watch?v=9S5A1IJqfOQ) |


## <a name="prerequisites"></a>先决条件 

在为 Azure 文件共享启用 AD DS 身份验证之前，请确保满足以下先决条件： 

- 选择或创建 [AD DS 环境](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)，并通过 Azure AD Connect 将其[同步到 Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。 

    可在新的或现有的本地 AD DS 环境中启用该功能。 用于访问的标识必须同步到 Azure AD。 要访问的 Azure AD 租户和文件共享必须与同一订阅关联。

- 将本地计算机或 Azure VM 通过域加入到本地 AD DS。 若要了解如何加入域，请查看[将计算机加入域](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。

    如果计算机未通过域加入到 AD DS，但属于 AD 域控制器，那么你仍可使用 AD 凭据进行身份验证。

- 选择或创建 Azure 存储帐户。  为了获得最佳性能，建议将存储帐户部署到计划从其访问共享的客户端所在的区域中。 然后，使用存储帐户密钥[装载 Azure 文件共享](storage-how-to-use-files-windows.md)。 如果使用存储帐户密钥进行装载，则会验证连接性。

    确保还没有为包含文件共享的存储帐户配置 Azure AD DS 身份验证。 如果存储帐户已启用 Azure 文件存储 Azure AD DS 身份验证，则需要先将其禁用，然后再改为使用本地 AD DS。 这意味着需要重新配置已在 Azure AD DS 环境中配置的现有 ACL，才能顺利执行权限。


    如果连接到 Azure 文件存储时遇到问题，请查看[我们在 Windows 上发布的 Azure 文件存储装载错误排查工具](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)。 我们还提供了[指南](./storage-files-faq.md#on-premises-access)来解决端口 445 被阻止时的情况。 


- 在为 Azure 文件共享启用和配置 AD DS 身份验证之前，请先完成所有相关网络配置。 有关详细信息，请查看 [Azure 文件存储的网络注意事项](storage-files-networking-overview.md)。

## <a name="regional-availability"></a>区域可用性

[所有 Azure 公共区域、中国区域和政府区域](https://azure.microsoft.com/global-infrastructure/locations/)均可使用采用 AD DS 的 Azure 文件存储身份验证。

## <a name="overview"></a>概述

如果计划对文件共享启用网络配置，建议先阅读[网络注意事项](./storage-files-networking-overview.md)一文并完成相关配置，然后再启用 AD DS 身份验证。

如果为 Azure 文件共享启用 AD DS 身份验证，则可通过本地 AD DS 凭据对 Azure 文件共享进行身份验证。 此外，它还有助于更好地管理权限，实现精细访问控制。 这要求使用 AD Connect 将标识从本地 AD DS 同步到 Azure AD。 使用本地 AD DS 凭据管理文件/共享级别访问时，你通过同步到 Azure AD 的标识来控制共享级别访问。

接下来，请按照以下步骤设置 Azure 文件存储来使用 AD DS 身份验证： 

1. [第 1 部分：在存储帐户上启用 AD DS 身份验证](storage-files-identity-ad-ds-enable.md)

1. [第 2 部分：将共享项的访问权限分配给与目标 AD 标识同步的 Azure AD 标识（用户、组或服务主体）](storage-files-identity-ad-ds-assign-permissions.md)

1. [第 3 部分：通过 SMB 为目录和文件配置 Windows ACL](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [第 4 部分：将 Azure 文件共享装载到已加入 AD DS 的 VM](storage-files-identity-ad-ds-mount-file-share.md)

1. [更新 AD DS 中的存储帐户标识密码](storage-files-identity-ad-ds-update-password.md)

下图说明了通过 SMB 为 Azure 文件共享启用 Azure AD 身份验证的端到端工作流。 

![文件存储 AD 工作流关系图](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

用于访问 Azure 文件共享的标识必须同步到 Azure AD，这样才能通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 模型执行共享级别文件权限。 从现有文件服务器中执行的文件/目录上的 [Windows 样式 DACL](/previous-versions/technet-magazine/cc161041(v=msdn.10)) 将被保留并强制执行。 这实现了与企业 AD DS 环境的无缝集成。 将本地文件服务器替换为 Azure 文件共享后，现有用户可通过单一登录体验从当前客户端访问 Azure 文件共享，无需更改正在使用的凭据。  

## <a name="next-steps"></a>后续步骤

若要为 Azure 文件共享启用本地 AD DS 身份验证，请继续阅读以下文章：

[第 1 部分：为帐户启用 AD DS 身份验证](storage-files-identity-ad-ds-enable.md)
