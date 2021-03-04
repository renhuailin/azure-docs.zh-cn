---
title: 提供服务器凭据以发现应用程序、依赖项和 SQL Server 实例和数据库
description: 了解如何在设备配置管理器上提供服务器凭据
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 2359855ce3949eb022a03f6e8e2dbc05f98907db
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054516"
---
# <a name="provide-server-credentials-to-discover-applications-dependencies-and-sql-server-instances-and-databases"></a>提供服务器凭据以发现应用程序、依赖项和 SQL Server 实例和数据库

请参阅本文，了解如何在设备配置管理器上添加多个服务器凭据，以执行软件清单 (发现安装的应用程序) 、无代理依赖项分析和发现 SQL Server 实例和数据库。

> [!Note]
> 在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估现在为预览版。 若要试用此功能，请使用 [**此链接**](https://aka.ms/AzureMigrate/SQL) 在 **澳大利亚东部** 区域中创建一个项目。 如果你已有一个澳大利亚东部的项目，并且想要尝试此功能，请确保已在门户上完成这些 [**先决条件**](how-to-discover-sql-existing-project.md) 。

[Azure Migrate 设备](migrate-appliance.md)是一种轻型设备，由 Azure Migrate：服务器评估用于发现在 VMware 环境中运行的本地服务器，并将服务器配置和性能元数据发送到 Azure。 设备还可用于执行软件清单、无代理依赖项分析和发现 SQL Server 实例和数据库。

如果要利用这些功能，可以通过执行以下步骤来提供服务器凭据。 设备将尝试自动将凭据映射到服务器来执行发现功能。

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>为 VMware 环境中运行的服务器添加凭据

### <a name="types-of-server-credentials-supported"></a>支持的服务器凭据类型

可以在设备配置管理器上添加多个服务器凭据，该管理器可以是域、非域 (Windows 或 Linux) 或 SQL Server 身份验证凭据。

下表列出了支持的服务器凭据类型：

凭据的类型 | 说明
--- | ---
**域凭据** | 您可以通过在 "**添加凭据**" 模式下的下拉选项中选择相应的选项来添加 **域凭据**。 <br/><br/> 若要提供域凭据，需要指定域名，该 **域名** 必须以 FQDN 格式提供 (例如 prod.corp.contoso.com) 。 <br/><br/> 还需要指定凭据的友好名称、用户名和密码。 <br/><br/> 添加的域凭据将根据域的 Active Directory 自动进行身份验证。 这是为了防止在设备尝试将域凭据映射到发现的服务器时进行任何帐户锁定。 <br/><br/> 设备不会尝试映射验证失败的域凭据。 需要至少一个已成功验证的域凭据或至少一个非域凭据才能继续软件清单。<br/><br/>_如果已在 SQL server) 上配置 windows 身份验证模式_，则将使用针对 Windows 服务器自动映射的域凭据来执行软件清单，还可用于发现 SQL Server 实例和数据库 (。<br/> [详细了解](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) SQL server 上支持的身份验证模式的类型。
**Windows/Linux (的非域凭据)** | 你可以通过在 "**添加凭据**" 模式下的下拉选项中选择所需的选项来添加 **Windows (非域)** 或 **Linux (非域)** 。 <br/><br/> 需要为凭据、用户名和密码指定友好名称。
**SQL Server 身份验证凭据** | 你可以通过在 "**添加凭据**" 模式下的下拉选项中选择相应的选项来添加 **SQL Server 身份验证** 凭据。 <br/><br/> 需要为凭据、用户名和密码指定友好名称。 <br/><br/> 如果已在 SQL Server 上配置 SQL Server 身份验证模式，则可以添加此类型的凭据，以发现在 VMware 环境中运行的 SQL Server 实例和数据库。<br/> [详细了解](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) SQL server 上支持的身份验证模式的类型。<br/><br/> 需要至少提供一个已成功验证的域凭据或至少一个 Windows (非域) 凭据，使设备能够完成软件清单来发现服务器上安装的 SQL，然后再使用 SQL Server 身份验证凭据来发现 SQL Server 实例和数据库。

检查 Windows/Linux 凭据上所需的权限，以执行软件清单、无代理依赖项分析和发现 SQL Server 实例和数据库。

### <a name="required-permissions"></a>所需的权限

下表列出了在设备上提供的用于执行各自功能的服务器凭据所需的权限：

功能 | Windows 凭据 | Linux 凭据
---| ---| ---
**软件清单** | 来宾用户帐户 | 常规/普通用户帐户 (sudo 访问权限) 
**SQL Server 实例和数据库的发现** | 作为 sysadmin 服务器角色成员的用户帐户。 | _当前不支持_
**无代理依赖项分析** | 具有管理权限的域或非域 (本地) 帐户 | 根用户帐户，或 <br/> 对/bin/netstat 和/bin/ls 文件具有以下权限的帐户： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。<br/><br/> 使用以下命令设置这些功能： <br/><br/> sudo setcap CAP_DAC_READ_SEARCH，CAP_SYS_PTRACE = ep/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH，CAP_SYS_PTRACE = ep/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>提供凭据的建议做法

- 建议创建一个拥有 [所需权限](add-server-credentials.md#required-permissions)的专用域用户帐户，该帐户的作用域为对所需服务器上的 SQL Server 实例和数据库执行软件清单、无代理依赖项分析和发现。
- 建议至少提供一个已成功验证的域凭据或至少一个非域凭据来启动软件清单。
- 如果已在 SQL Server 上配置 Windows 身份验证模式，则可以在 SQL Server 上配置 Windows 身份验证模式，以便发现 SQL Server 实例和数据库。
-  如果已在 SQL Server 上配置了 SQL Server 身份验证模式，还可以提供 SQL Server 的身份验证凭据，但建议至少提供一个已成功验证的域凭据或至少一个 Windows (非域) 凭据，以便设备能够首先完成软件清单。

## <a name="credentials-handling-on-appliance"></a>设备上的凭据处理

- 在设备服务器上提供的所有凭据都存储在本地设备服务器上，而不会发送到 Azure。
- 使用数据保护 API (DPAPI) 对存储在设备服务器上的凭据进行加密。
- 添加凭据后，设备会尝试自动映射凭据，以在相应的服务器上执行发现。
- 设备使用自动在服务器上映射的凭据来执行所有后续的发现循环，直到凭据能够获取所需的发现数据。 如果凭据停止工作，则设备将再次尝试从添加的凭据列表中进行映射，并在服务器上继续执行正在进行的发现。
- 添加的域凭据将根据域的 Active Directory 自动进行身份验证。 这是为了防止在设备尝试将域凭据映射到发现的服务器时进行任何帐户锁定。 设备不会尝试映射验证失败的域凭据。
- 如果设备无法为服务器映射任何域或非域凭据，你将看到你的项目中服务器的 "凭据不可用" 状态。

## <a name="next-steps"></a>后续步骤

查看有关[在 VMware 环境中运行的服务器的发现](tutorial-discover-vmware.md)教程