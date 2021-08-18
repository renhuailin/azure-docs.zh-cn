---
title: 概述 - Azure 文件存储基于标识授权
description: Azure 文件存储支持使用 Azure Active Directory 域服务 (AD DS) 和 Active Directory 通过 SMB（服务器消息块）进行基于标识的身份验证。 然后，已加入域的 Windows 虚拟机 (VM) 可使用 Azure AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 84934668c36d518e65c8997f72309a5ca3eb8fbe
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118488"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>实现 SMB 访问的 Azure 文件存储基于标识的身份验证选项概述
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

若要了解如何为 Azure 文件共享启用本地 Active Directory 域服务身份验证，请参阅[为 Azure 文件共享启用通过 SMB 实现的本地 Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-enable.md)。

若要了解如何为 Azure 文件共享启用 Azure AD DS 身份验证，请参阅[在 Azure 文件存储上启用 Azure Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="glossary"></a>术语表 
了解与 Azure 文件共享通过 SMB 进行 Azure AD 域服务身份验证相关的一些关键术语非常有用：

-   **Kerberos 身份验证**

    Kerberos 是一种身份验证协议，用于验证用户或主机的标识。 有关 Kerberos 的详细信息，请参阅 [Kerberos 身份验证概述](/windows-server/security/kerberos/kerberos-authentication-overview)。

-  **服务器消息块 (SMB) 协议**

    SMB 是行业标准网络文件共享协议。 也称为通用 Internet 文件系统或 CIFS。 有关 SMB 的详细信息，请参阅 [Microsoft SMB 协议和 CIFS协议概述](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)。

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 Azure AD 将核心目录服务、应用程序访问管理和标识保护融入一个解决方案中。 已联接 Azure AD 的 Windows 虚拟机 (VM) 无法使用 Azure AD 凭据访问 Azure 文件共享。 有关详细信息，请参阅[什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory 域服务 (Azure AD DS)**

    Azure AD DS 提供托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 这些服务与 Active Directory 域服务完全兼容。 有关详细信息，请参阅 [Azure Active Directory 域服务](../../active-directory-domain-services/overview.md)。

- **本地 Active Directory 域服务 (AD DS)**

    通过将本地 Active Directory 域服务 (AD DS) 与 Azure 文件存储集成，可存储目录数据，同时可向网络用户和管理员提供该数据。 安全性通过登录身份验证以及对目录中对象的访问控制与 AD DS 集成。 通过单一网络登录，管理员可以管理其整个网络中的目录数据和组织，获得授权的网络用户可以访问该网络上的任何资源。 本地环境中的企业通常采用 AD DS，并将 AD DS 凭据用作访问控制的标识。 有关详细信息，请参阅 [Active Directory 域服务概述](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

-   **Azure 基于角色的访问控制 (Azure RBAC)**

    Azure 基于角色的访问控制 (Azure RBAC) 可用于对 Azure 进行细致的访问管理。 使用 Azure RBAC，可通过向用户授予执行其作业所需的最少权限来管理对资源的访问权限。 有关 Azure RBAC 的详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)。

## <a name="common-use-cases"></a>常见用例

Azure 文件存储基于标识的身份验证和 Windows ACL 支持最适合用于以下用例：

### <a name="replace-on-premises-file-servers"></a>取代本地文件服务器

弃用和取代分散的本地文件服务器，这是每个企业会在 IT 现代化旅程中遇到的一个共同问题。 可以将数据迁移到 Azure 文件存储时，最恰当的做法是将 Azure 文件共享和本地 AD DS 身份验证结合在一起使用。 通过完全迁移，你可以利用高可用性和可伸缩性的优势，同时最大程度地减少客户端更改。 它可为最终用户提供无缝的迁移体验，这样最终用户就可以继续通过现有的加入域的计算机使用相同的凭据访问其数据。

### <a name="lift-and-shift-applications-to-azure"></a>将应用程序直接迁移到 Azure

将应用程序直接迁移到云时，你可能想要为数据保留同样的身份验证模型。 将基于标识的访问控制体验扩展到 Azure 文件共享时，就无需再将应用程序更改为新式身份验证方式，这样还会加快云采用进程。 Azure 文件共享提供了与 Azure AD DS 或本地 AD DS 集成来进行身份验证的选项。 如果计划实现完全云原生，并最大程度地减少管理云基础结构的工作量，则 Azure AD DS 更加适合作为完全托管的域服务。 如果需要实现与 AD DS 功能完全兼容，可以考虑在 VM 上自承载域控制器，从而将 AD DS 环境扩展到云。 无论采用哪种方法，你都可以灵活选择适合自己业务需求的域服务。

### <a name="backup-and-disaster-recovery-dr"></a>备份和灾难恢复 (DR)

如果你将主文件存储保留在本地，则 Azure 文件共享可以作为备份或灾难恢复的理想存储方式，从而提高业务连续性。 可以使用 Azure 文件共享备份现有文件服务器中的数据，同时保留 Windows DACL。 对于灾难恢复方案，可以配置一个身份验证选项，用于支持在进行故障转移时强制执行适当的访问控制。

## <a name="supported-scenarios"></a>支持的方案

下表总结了支持的 Azure 文件共享 Azure AD DS 和本地 AD DS 身份验证方案。 建议选择对客户端环境采用的域服务，以便与 Azure 文件存储集成。 如果已在本地或 Azure 中设置 AD DS，设备通过加入域联接 AD，则应选择使用 AD DS 进行 Azure 文件共享身份验证。 同样，如果已采用 Azure AD DS，则应使用它来进行 Azure 文件共享身份验证。


|Azure AD DS 身份验证  | 本地 AD DS 身份验证  |
|---------|---------|
|已联接 Azure AD DS 的 Windows 计算机可以通过 SMB 使用 Azure AD 凭据访问 Azure 文件共享。     |已联接本地 AD DS 或已联接 Azure AD DS 的 Windows 计算机可以通过 SMB 使用同步到 Azure AD 的本地 Active Directory 凭据访问 Azure 文件共享。 客户端必须可访问 AD DS。        |

### <a name="restrictions"></a>限制

- Azure AD DS 和本地 AD DS 身份验证不支持对计算机帐户进行身份验证。 你可以考虑改为使用服务登录帐户。
- 不支持对已联接 Azure AD 的设备或已注册 Azure AD 的设备进行 Azure AD DS 身份验证或本地 AD DS 身份验证。
- Azure 文件共享仅支持对以下域服务进行基于标识的身份验证：[Azure Active Directory 域服务 (AZURE AD DS)](#azure-ad-ds) 或[本地 Active Directory 域服务 (AD DS)](#ad-ds)。
- 网络文件系统（NFS，处于预览阶段）不支持这两种基于标识的身份验证方式。

## <a name="advantages-of-identity-based-authentication"></a>基于标识的身份验证的优点
将基于标识的身份验证用于 Azure 文件存储与采用共享密钥身份验证相比，具有以下几个优势：

-   **使用本地 AD DS 和 Azure AD DS 将传统的基于标识的文件共享访问体验扩展到云**  
    如果计划将应用程序直接迁移到云，用 Azure 文件共享替代传统的文件服务器，则可能想要应用程序使用本地 AD DS 或 Azure AD DS 凭据进行身份验证，来访问文件数据。 Azure 文件共享支持使用本地 AD DS 或 Azure AD DS 凭据，来通过 SMB 从本地 AD DS 或 Azure AD DS 已加入域的 VM 访问 Azure 文件共享。

-   **对 Azure 文件共享强制实施粒度访问控制**  
    可以向特定标识授予共享、目录或文件级别权限。 例如，假设多个团队使用一个 Azure 文件共享进行项目协作。 你可以向所有团队授予访问非敏感目录的权限，而仅授权财务团队访问含敏感财务数据的目录。 

-   **备份 Windows ACL（又称为 NTFS）和数据**  
    可使用 Azure 文件共享备份现有的本地文件共享。 将文件共享通过 SMB 备份到 Azure 文件共享时，Azure 文件存储会保留 ACL 和你的数据。

## <a name="how-it-works"></a>工作原理

Azure 文件共享利用 Kerberos 协议，来使用本地 AD DS 或 Azure AD DS 进行身份验证。 当与客户端上运行的用户或应用程序关联的标识尝试访问 Azure 文件共享中的数据时，系统会将请求发送到域服务（AD DS 或 Azure AD DS），以对标识进行身份验证。 如果身份验证成功，则会返回 Kerberos 令牌。 客户端发送一个包含该 Kerberos 令牌的请求，Azure 文件共享使用该令牌对该请求授权​​。 Azure 文件共享仅接收 Kerberos 令牌，而不访问凭据。

必须先设置域环境，然后才能在 Azure 文件共享上启用基于标识的身份验证。

### <a name="ad-ds"></a>AD DS

对于本地 AD DS 身份验证，必须设置 AD 域控制器，并让计算机或 VM 加入域。 可以将域控制器托管在 Azure VM 或本地。 无论采用哪种方法，加入域的客户端必须可访问域服务，因此这些客户端必须在公司网络或域服务的虚拟网络中。

下图说明了 Azure 文件共享通过 SMB 进行本地 AD DS 身份验证的过程。 本地 AD DS 必须使用 Azure AD Connect 同步同步到 Azure AD。对于 Azure 文件共享访问，只可以对同时位于本地 AD DS 和 Azure AD 的混合用户进行身份验证和授权。 这是因为，共享级别权限是针对 Azure AD 中的标识配置的，目录/文件级别权限是对 AD DS 中的标识强制实施的。 请务必为同一个混合用户正确配置权限。

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="说明 Azure 文件共享通过 SMB 进行本地 AD DS 身份验证过程的关系图。":::

### <a name="azure-ad-ds"></a>Azure AD DS

对于 Azure AD DS 身份验证，应启用 Azure AD 域服务，并让计划用于访问文件数据的 VM 加入域。 已加入域的 VM 必须与 Azure AD DS 位于同一虚拟网络 (VNET) 中。 

下图说明了 Azure 文件共享通过 SMB 进行 Azure AD DS 身份验证的工作流。 它遵循与 Azure 文件共享进行本地 AD DS 身份验证类似的模式。 有两个主要的区别：

- 首先，无需在 Azure AD DS 中创建标识来表示存储帐户。 此过程由启用进程在后台完成。

- 其次，Azure AD 中存在的所有用户都可以进行身份验证，并获得授权。 用户可以只是云用户，也可以是混合用户。 由平台管理从 Azure AD 到 Azure AD DS 的同步，无需任何用户配置。 但是，客户端必须通过加入域联接 Azure AD DS，不能已联接或注册 Azure AD。 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="关系图":::

### <a name="enable-identity-based-authentication"></a>启用基于标识的身份验证

你可以使用 Azure AD DS 或本地 AD DS，为新的和现有的存储帐户的 Azure 文件共享启用基于标识的身份验证。 只能将一个域服务用于存储帐户的文件访问身份验证，该验证适用于帐户中的所有文件共享。 有关使用 Azure AD DS 为文件共享设置身份验证的详细指南，请参阅文章[在 Azure 文件存储上启用 Azure Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)；有关使用本地 AD DS 的指南，请参阅另一篇文章[为 Azure 文件共享启用通过 SMB 实现的本地 Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-enable.md)。

### <a name="configure-share-level-permissions-for-azure-files"></a>为 Azure 文件配置共享级别权限

启用 Azure AD DS 或本地 AD DS 身份验证后，即可以使用 Azure 内置角色，或为 Azure AD 标识配置自定义角色，并分配对存储帐户中任意文件共享的访问权限。 获得授权的标识只可以通过分配的权限获得相应共享的访问权限，没有其他权限，甚至没有对根目录的访问权限。 你仍需要单独配置对 Azure 文件共享目录级别或文件级别的权限。

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>配置对 Azure 文件存储目录级别或文件级别的权限

Azure 文件共享在目录级别和文件级别（包括根目录）强制实施标准 Windows 文件权限。 支持通过 SMB 和 REST 配置目录级别或文件级别权限。 请从 VM 装载目标文件共享，并使用 Windows 资源管理器、Windows [icacls](/windows-server/administration/windows-commands/icacls) 或 [Set-ACL](/powershell/module/microsoft.powershell.security/get-acl) 命令配置权限。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>使用存储帐户密钥获取超级用户权限

拥有存储帐户密钥的用户可使用超级用户权限访问 Azure 文件共享。 超级用户权限可绕过所有访问控制限制。

> [!IMPORTANT]
> 为保证安全性，建议采用以下最佳做法：避免共享存储帐户密钥，并尽可能利用基于标识的身份验证。

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>将数据导入 Azure 文件共享时保留目录和文件 ACL

将数据复制到 Azure 文件共享时，Azure 文件存储支持保留目录级别或文件级别 ACL。 可以使用 Azure 文件同步或常见的文件移动工具集，将目录或文件上的 ACL 复制到 Azure 文件共享。 例如，可使用带 `/copy:s` 标志的 [robocopy](/windows-server/administration/windows-commands/robocopy) 将数据和 ACL 复制到 Azure 文件共享。 默认情况下，系统将保留 ACL，你无需在你的存储帐户上启用基于标识的身份验证来保留 ACL。

## <a name="pricing"></a>定价
在存储帐户上启用通过 SMB 进行基于标识的身份验证无需额外的服务费。 有关定价的详细信息，请参阅 [Azure 文件存储定价](https://azure.microsoft.com/pricing/details/storage/files/)和 [Azure AD 域服务定价](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

## <a name="next-steps"></a>后续步骤
有关 Azure 文件存储和通过 SMB 进行基于标识的身份验证的详细信息，请参阅以下资源：

- [规划 Azure 文件存储部署](storage-files-planning.md)
- [为 Azure 文件共享启用通过 SMB 实现的本地 Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-enable.md)
- [在 Azure 文件存储上启用 Azure Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [常见问题解答](storage-files-faq.md)