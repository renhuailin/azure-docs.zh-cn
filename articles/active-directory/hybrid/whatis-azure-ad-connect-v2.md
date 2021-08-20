---
title: 什么是 Azure AD Connect v2.0？ | Microsoft Docs
description: 了解 Azure AD Connect 的下一版本。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/24/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c117e7f1a5d605e1abe42474b24b8fbf6491c43
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481222"
---
# <a name="introduction-to-azure-ad-connect-v20"></a>Azure AD Connect V2.0 简介 

Azure AD Connect 于几年前发布。  从那时起，Azure AD Connect 使用的一些组件已被计划弃用并更新到新版本。  若要尝试单独更新所有这些组件，需要花费时间和计划。  

为了解决这个问题，我们希望将尽可能多的这些新组件捆绑到一个新的版本中，这样只需更新一次。 这个版本就是 Azure AD Connect V2.0。  这是用于完成使用最新基础组件生成的混合标识目标的同一软件的新版本。 

## <a name="what-are-the-major-changes"></a>主要更改有哪些？ 

### <a name="sql-server-2019-localdb"></a>SQL Server 2019 LocalDB 

Azure AD Connect 先前版本随 SQL Server 2012 LocalDB 一同提供。 V2.0 附带有 SQL Server 2019 LocalDB，该版本可实现增强的稳定性和性能，并提供多个与安全性相关的 bug 修复。 SQL Server 2012 将于 2022 年 7 月停止外延支持。 有关详细信息，请参阅 [Microsoft SQL 2019](https://www.microsoft.com/sql-server/sql-server-2019)。

### <a name="msal-authentication-library"></a>MSAL 身份验证库 

Azure AD Connect 先前版本随 ADAL 身份验证库一同提供。 该库将于 2022 年 6 月弃用。 V2.0 版本随更新的 MSAL 库一起提供。 有关详细信息，请参阅 [MSAL 库概述](../../active-directory/develop/msal-overview.md)。

### <a name="visual-c-redist-14"></a>Visual C++ Redist 14 

SQL Server 2019 需要 Visual C++ Redist 14 运行时，因此我们将更新 C++ 运行时库以使用此版本。 这将随 Azure AD Connect V2.0 包一起安装，因此无需为 C++ 运行时更新执行任何操作。 

### <a name="tls-12"></a>TLS 1.2 

TLS1.0 和 TLS 1.1 被视为不安全的协议，Microsoft 不推荐使用。 Azure AD Connect 的这一版本仅支持 TLS 1.2。 如果你的服务器不支持 TLS 1.2，则需要先启用此功能，然后才能部署 Azure AD Connect V2.0。 有关详细信息，请参阅[对 Azure AD Connect 强制实施 TLS 1.2](reference-connect-tls-enforcement.md)。

### <a name="all-binaries-signed-with-sha2"></a>所有用 SHA2 签名的二进制文件 

我们注意到，某些组件包含 SHA1 签名的二进制文件。 我们不再支持 SHA1 用于可下载的二进制文件，并将所有二进制文件升级为 SHA2 签名。 数字签名用于确保更新直接来自 Microsoft，并且在交付过程中未被篡改。 由于 SHA-1 算法存在缺陷，为了符合行业标准，我们已将 Windows 更新的签名更改为使用更安全的 SHA-2 算法。”  

你方无需执行任何操作。 

### <a name="windows-server-2012-and-windows-server-2012-r2-are-no-longer-supported"></a>Windows Server 2012 和 Windows Server 2012 R2 不再受支持 

SQL Server 2019 需要 Windows Server 2016 或更高版本的服务器操作系统。 由于 AAD Connect v2 包含 SQL Server 2019 组件，我们不再支持较旧的 Windows Server 版本。  

无法在较旧的 Windows Server 版本上安装此版本。 建议将 Azure AD Connect 服务器升级到 Windows Server 2019，这是 Windows Server 操作系统的最新版本。 

这篇[文章](https://docs.microsoft.com/windows-server/get-started-19/install-upgrade-migrate-19)介绍了从旧版 Windows Server 升级到 Windows Server 2019 的过程。 

### <a name="powershell-50"></a>PowerShell 5.0 

此版本的 Azure AD Connect 包含多个需要 PowerShell 5.0 的 cmdlet，所以此要求是 Azure AD Connect 的新先决条件。  

在[此处](https://docs.microsoft.com/powershell/scripting/windows-powershell/install/windows-powershell-system-requirements?view=powershell-7.1#windows-powershell-50)可以找到关于 PowerShell 先决条件的更多详细信息。

 >[!NOTE]
 >PowerShell 5 已经是 Windows Server 2016 的一部分，所以只要你使用的是最新的 Windows Server 版本，可能就不需要执行任何操作。 

## <a name="what-else-do-i-need-to-know"></a>还需要了解什么？ 


为什么这次升级对我很重要？ </br>
明年，当前 Azure AD Connect 服务器安装中的几个组件将不再受支持。 如果使用不受支持的产品，我们的支持团队将很难为你提供组织所需的支持体验。 因此，建议所有客户尽快升级到这一较新版本。 

这次升级尤其重要，因为我们必须更新 Azure AD Connect 的先决条件，你可能需要额外的时间来规划和更新服务器到这些先决条件的新版本 

是否有我需要了解的新功能？ </br>
没有 - 此版本不包含任何新功能。 此版本仅包含 Azure AD Connect 上一些基础组件的更新。 

能否从以前的版本升级到 V2.0？ </br>
能 - 支持从任何早期版本的 Azure AD Connect 升级到 Azure AD Connect V2.0。 请按照本文中的指导确定最适合你的升级策略。 

能否导出当前服务器的配置并将其导入 Azure AD Connect V2.0？ </br>
是的，你可以这样做，而且这是迁移到 Azure AD Connect V2.0 的一个很好的方法，尤其是在升级到新的操作系统版本时。 可以在这篇[文章](how-to-connect-import-export-config.md)中详细了解导入/导出配置功能以及如何使用它。 

我已为 Azure AD Connect 启用自动升级 - 我会自动获得此新版本吗？ </br> 不能 - Azure AD Connect V2.0 目前还不能自动升级。 

我还没有准备好升级 - 我还有多少时间？ </br>
应尽快升级到 Azure AD Connect V2.0。 目前，我们将继续支持旧版本的 Azure AD Connect，但如果 Azure AD Connect 中的某些组件不再支持，则可能难以提供良好的支持体验。 此升级对于 ADAL 和 TLS1.0/1.1 尤为重要，因为这些服务在弃用后可能会意外停止工作。 

我使用外部 SQL 数据库并且不使用 SQL 2012 LocalDb - 是否仍需升级？ </br>
是的，即使不使用 SQL Server 2012，仍然需要升级以保持受支持的状态，因为 TLS1.0/1.1 和 ADAL 已弃用。 

如果我不升级，会发生什么情况？ </br>
直到即将停用的某个组件被实际弃用，你才会看到影响。 Azure AD Connect 将继续工作。 

我们预计于 2022 年 1 月弃用 TLS 1.0/1.1，你需要确保届时不再使用这些协议，否则你的服务可能会意外失效。 不过，你可以为 TLS 1.2 手动配置服务器，并且不需要将 Azure AD Connect 更新到 V2.0 

2022 年 6 月，ADAL 将不再受支持。 当 ADAL 不再受支持时，身份验证可能会意外失效，这将使 Azure AD Connect 服务器无法正常工作。 强烈建议在 2022 年 6 月之前升级到 Azure AD Connect V2.0。 无法使用当前的 Azure AD Connect 版本升级到受支持的身份验证库。 

升级到 2.0 后，ADSync PowerShell cmdlet 不再工作？ </br>
这是一个已知问题。  要解决此问题，请在安装或升级到版本 2.0 后重启 PowerShell 会话，然后重新导入模块。  使用以下说明导入模块。
 
 1.  使用管理员权限打开 Windows PowerShell
 2.  键入或复制并粘贴以下内容： 
    ``` powershell
              Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync"
    ```
 

## <a name="license-requirements-for-using-azure-ad-connect-v20"></a>使用 Azure AD Connect V2.0 的许可证要求

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 的许可证要求
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>后续步骤

- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)

本文介绍了从旧版 Windows Server 升级到 Windows Server 2019 的过程。 