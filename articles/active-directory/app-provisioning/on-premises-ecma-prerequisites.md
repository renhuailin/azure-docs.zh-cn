---
title: Azure AD ECMA 连接器主机的先决条件
description: 本文介绍使用 Azure AD ECMA 连接器主机所需的先决条件和硬件要求。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50bb95b5362902eed2ce297350e9fb2760530e22
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449566"
---
# <a name="prerequisites-for-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 连接器主机的先决条件

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 如需请求功能访问权限，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)。 我们正在准备正式发布版，将在接下来的几个月内向更多客户和连接器开放该预览版。

本文提供有关使用 Azure Active Directory (Azure AD) ECMA 连接器主机所需的先决条件指导。

此流程将指导你完成 Azure AD ECMA 连接器主机的安装和配置过程。

 ![图片显示了安装流程。](./media/on-premises-ecma-prerequisites/flow-1.png)

有关详细的安装和配置信息，请参阅：

   - [Azure AD ECMA 连接器主机的安装](on-premises-ecma-install.md)
   - [配置 Azure AD ECMA 连接器主机和预配代理](on-premises-ecma-configure.md)
   - [Azure AD ECMA 连接器主机通用 SQL 连接器配置](on-premises-sql-connector-configure.md)

## <a name="on-premises-prerequisites"></a>本地先决条件

 - 可在其中创建、更新和删除用户的目标系统（如 SQL 数据库）。
 - 用于该目标系统的 ECMA 2.0 或更高版本连接器，支持导出、架构检索和可选的完全导入或增量导入操作。 如果在配置过程中没有准备好 ECMA 连接器，可以验证端到端流程（如果环境中有 SQL Server 实例），并使用通用 SQL 连接器。
 - Windows Server 2016 或更高版本的计算机，其中包含可通过 Internet 访问的 TCP/IP 地址、与目标系统的连接，以及与 login.microsoftonline.com 的出站连接。 例如，托管在 Azure IaaS 或代理后面的 Windows Server 2016 虚拟机。 服务器应至少有 3 GB 的 RAM。
 - 具有 .NET Framework 4.7.1 的计算机。

## <a name="cloud-requirements"></a>云要求

 - 具有 Azure AD Premium P1 或 Premium P2（或 EMS E3 或 E5）的 Azure AD 租户。 
 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
 - 用于配置预配代理的混合管理员角色，以及用于在 Azure 门户中配置预配的应用程序管理员或云管理员角色。

## <a name="next-steps"></a>后续步骤

- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [通用 SQL 连接器](on-premises-sql-connector-configure.md)
- [教程 - ECMA 连接器主机通用 SQL 连接器](tutorial-ecma-sql-connector.md)
