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
ms.openlocfilehash: 229da477e358a0efd85ea555762443de9859b253
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111569993"
---
# <a name="export-a-microsoft-identity-manager-connector-for-use-with-azure-ad-ecma-connector-host"></a>导出 Microsoft Identity Manager 连接器以与 Azure AD ECMA 连接器主机一起使用

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 可在[此处](https://aka.ms/onpremprovisioningpublicpreviewaccess)请求访问功能。 未来几个月在我们准备发布正式版的过程中，我们会向更多客户和连接者开放该预览版。

可以从 FIM Sync 或 MIM Sync 安装，将特定连接器的配置导入到 Azure AD ECMA 连接器主机。  请注意，MIM Sync 安装仅用于配置，而不是用于从 Azure AD 持续同步。

>[!IMPORTANT]
>目前，只有泛型 SQL (GSQL) 连接器支持与 Azure AD ECMA 连接器主机一起使用。


## <a name="creating-and-exporting-a-connector-configuration-in-mim-sync"></a>在 MIM Sync 中创建和导出连接器配置
如果你已经配置好 MIM Sync 与 ECMA 连接器，请跳到步骤 10。

 1. 准备 Windows Server 2016 服务器，此服务器与将用于运行 Azure AD ECMA 连接器主机的服务器不同。  此主机服务器应该有共置的 SQL Server 2016 数据库，或者具有与 SQL Server 2016 数据库的网络连接。  设置此服务器的一种方式是在 Windows Server 2016 上部署带有 SQL Server 2016 SP1 Standard 映像的 Azure 虚拟机。  请注意，除了用于设置目的的远程桌面访问外，此服务器不需要 Internet 连接。
 2. 创建一个帐户，以便在 MIM Sync 安装期间使用。  可以是此 Windows Server 上的本地帐户。  要创建本地帐户，请启动控制面板，打开用户帐户，然后添加用户帐户“mimsync”。
 3. 将上一步中创建的帐户添加到本地管理员组。
 4. 授予先前创建的帐户运行服务的能力。  启动“本地安全策略”，单击“本地策略”、“用户权限分配”和“以服务身份登录”。  添加前面提到的帐户。
 5. 在此主机上安装 MIM Sync。 如果没有 MIM Sync 二进制文件，则可以从 [https://www.microsoft.com/en-us/download/details.aspx?id=48244](https://www.microsoft.com/en-us/download/details.aspx?id=48244) 下载 ZIP 文件、装载 ISO 映像并将文件夹“Synchronization Service”复制到 Windows Server 主机，以安装测试版软件。  然后，运行该文件夹中包含的安装程序。   请注意，测试版软件有时间限制并且会过期，不适合在生产环境中使用。
 6. MIM Sync 安装完成后，请注销并重新登录。
 7. 在与 MIM Sync 相同的服务器上安装连接器。（为了便于说明，本测试实验室指南将介绍如何使用 Microsoft 提供的其中一种连接器。该连接器可从 [https://www.microsoft.com/en-us/download/details.aspx?id=51495](https://www.microsoft.com/en-us/download/details.aspx?id=51495) 下载）。
 8. 启动 Synchronization Service UI。  单击“管理代理”。  单击“创建”，并指定连接器管理代理。  请确保选择基于 ECMA 的连接器管理代理。
 9. 为连接器命名，并配置将数据导入和导出到连接器所需的参数。  请确保配置连接器可以导入和导出用户或 person 对象类型的单值字符串属性。
 10. 在 MIM Sync 服务器计算机上，启动 Synchronization Service UI（如果尚未运行）。  单击“管理代理”。
 11. 选择连接器，然后单击“导出管理代理”。  将 XML 文件以及连接器的 DLL 和相关软件保存到将保留 ECMA 连接器主机的 Windows Server。

此时不再需要 MIM Sync 服务器。

 1. 以运行 Azure AD ECMA 连接器主机的帐户登录 Windows Server。
 2. 更改为目录 c:\program files\Microsoft ECMA2host\Service\ECMA，并确保该目录中已存在一个或多个 DLL。  （这些 DLL 对应于 Microsoft 提供的连接器）。
 3. 将连接器的 MA DLL 及其任何必备的 DLL 复制到服务目录的同一 ECMA 子目录。
 4. 更改为目录 C:\program files\Microsoft ECMA2Host\Wizard，然后运行程序 Microsoft.ECMA2Host.ConfigWizard.exe，以设置 ECMA 连接器主机配置。
 5. 此时将出现包含连接器列表的新窗口。 默认情况下，不存在任何连接器。  单击“新建连接器”。
 6. 指定先前从 MIM 导出的管理代理 xml 文件。  继续上述“配置连接器”部分中的配置和架构映射说明。



## <a name="next-steps"></a>后续步骤


- [应用预配](user-provisioning.md)
- [Azure AD ECMA 连接器主机先决条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [泛型 SQL 连接器](on-premises-sql-connector-configure.md)