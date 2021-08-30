---
title: 导出 Microsoft Identity Manager 连接器以与 Azure AD ECMA 连接器主机一起使用
description: 介绍如何从 MIM Sync 创建并导出连接器，以便与 Azure AD ECMA 连接器主机一起使用。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0731e35eab3a2872cd3c07e5401577fd51c33323
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437290"
---
# <a name="export-a-microsoft-identity-manager-connector-for-use-with-the-azure-ad-ecma-connector-host"></a>导出 Microsoft Identity Manager 连接器以与 Azure AD ECMA 连接器主机一起使用

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 如需请求功能访问权限，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)”。 未来几个月在我们准备正式发布版的过程中，我们会向更多客户和连接器开放该预览版。

可以从 Forefront Identity Manager Synchronization Service 或 Microsoft Identity Manager Synchronization Service (MIM Sync) 安装，将特定连接器的配置导入到 Azure Active Directory (Azure AD) ECMA 连接器主机。 MIM Sync 安装仅用于配置，而不是用于从 Azure AD 持续同步。

>[!IMPORTANT]
>目前，只有支持泛型 SQL 连接器与 Azure AD ECMA 连接器主机一起使用。

## <a name="create-and-export-a-connector-configuration-in-mim-sync"></a>在 MIM Sync 中创建和导出连接器配置
如果你已经配置好 MIM Sync 与 ECMA 连接器，请跳到步骤 10。

 1. 准备 Windows Server 2016 服务器，此服务器与将用于运行 Azure AD ECMA 连接器主机的服务器不同。 此主机服务器应该有共置的 SQL Server 2016 数据库，或者具有与 SQL Server 2016 数据库的网络连接。 设置此服务器的一种方式是在 Windows Server 2016 上部署带有 SQL Server 2016 SP1 Standard 映像的 Azure 虚拟机。 除了用于设置目的的远程桌面访问外，此服务器不需要 Internet 连接。
 1. 创建一个帐户，以便在 MIM Sync 安装期间使用。 该帐户可以是此 Windows Server 实例上的本地帐户。 要创建本地帐户，请打开“控制面板” > “用户帐户”，然后添加用户帐户“mimsync”  。
 1. 将上一步中创建的帐户添加到本地管理员组。
 1. 授予先前创建的帐户运行服务的能力。 启动“本地安全策略”，然后选择“本地策略” > “用户权限分配” > “以服务身份登录”   。 添加前面提到的帐户。
 1. 在此主机上安装 MIM Sync。 如果没有 MIM Sync 二进制文件，则可以从 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=48244)下载 zip 文件、装载 ISO 映像并将文件夹“Synchronization Service”复制到 Windows Server 主机，以安装测试版软件。 然后，运行该文件夹中包含的安装程序。 测试版软件有时间限制并且会过期。 它不用于生产用途。
 1. MIM Sync 安装完成后，请注销并重新登录。
 1. 在 MIM Sync 所在的服务器上安装连接器。（为了便于说明，本测试实验室指南将介绍如何使用 Microsoft 提供的其中一种连接器。该连接器可从 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=51495)下载）。
 1. 启动 Synchronization Service UI。 选择“管理代理”。 选择“创建”，并指定连接器管理代理。 请确保选择基于 ECMA 的连接器管理代理。
 1. 为连接器命名，并配置将数据导入和导出到连接器所需的参数。 请确保配置连接器可以导入和导出用户或 person 对象类型的单值字符串属性。
 1. 在 MIM Sync 服务器计算机上，启动 Synchronization Service UI（如果尚未运行）。 选择“管理代理”。
 1. 选择连接器，然后选择“导出管理代理”。 将 XML 文件以及连接器的 DLL 和相关软件保存到将保留 ECMA 连接器主机的 Windows Server。

此时不再需要 MIM Sync 服务器。

 1. 以运行 Azure AD ECMA 连接器主机的帐户登录 Windows Server。
 1. 更改为目录 C:\Program Files\Microsoft ECMA2host\Service\ECMA。 确保该目录中已存在一个或多个 DLL。 这些 DLL 对应于 Microsoft 提供的连接器。
 1. 将连接器的 MA DLL 及其任何必备的 DLL 复制到服务目录的同一 ECMA 子目录。
 1. 更改为目录 C:\Program Files\Microsoft ECMA2Host\Wizard。 运行程序 Microsoft.ECMA2Host.ConfigWizard.exe，以设置 ECMA 连接器主机配置。
 1. 此时会显示包含连接器列表的新窗口。 默认情况下，不存在任何连接器。 选择“新建连接器”。
 1. 指定先前从 MIM Sync 导出的管理代理 XML 文件。 继续查看“配置连接器”部分中的配置和架构映射说明。

## <a name="next-steps"></a>后续步骤

- [应用预配](user-provisioning.md)
- [Azure AD ECMA 连接器主机先决条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [泛型 SQL 连接器](on-premises-sql-connector-configure.md)
