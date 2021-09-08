---
title: 提供服务器凭据以发现软件清单、依赖项、Web 应用以及 SQL Server 实例和数据库
description: 了解如何在设备配置管理器上提供服务器凭据
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 2128145e0f2efa7a443c7a0c972117f29de73765
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965408"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-web-apps-and-sql-server-instances-and-databases"></a>提供服务器凭据以发现软件清单、依赖项、Web 应用以及 SQL Server 实例和数据库

请参阅本文，了解如何在设备配置管理器上添加多个服务器凭据，以执行软件清单（发现已安装的应用程序）、无代理依赖项分析，以及发现 Web 应用、SQL Server 实例和数据库。

[Azure Migrate 设备](migrate-appliance.md)是“Azure Migrate: 发现和评估”使用的一种轻型设备，可发现在 VMware 环境中运行的本地服务器，并将服务器配置和性能元数据发送到 Azure。 该设备还可用于执行软件清单、无代理依赖项分析以及发现 Web 应用程序、SQL Server 实例和数据库。

若要使用这些功能，可遵循以下步骤提供服务器凭据。 设备会尝试自动将凭据映射到服务器以执行发现功能。

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>为 VMware 环境中运行的服务器添加凭据

### <a name="types-of-server-credentials-supported"></a>支持的服务器凭据类型

可以在设备配置管理器上添加多个服务器凭据，这些凭据可以是域、非域（Windows 或 Linux）或 SQL Server 身份验证凭据。

下表列出了支持的服务器凭据类型：

凭据类型 | 说明
--- | ---
域凭据 | 在“添加凭据”模式下，你可以通过从下拉列表中选择相应选项来添加“域凭据”。  <br/><br/> 若要提供域凭据，你需要指定域名，该域名必须以 FQDN 格式提供（例如 prod.corp.contoso.com）。 <br/><br/> 你还需要为凭据、用户名和密码指定一个易记名称。 <br/><br/> 添加的域凭据将根据域的 Active Directory 自动验证其真实性。 这是为了防止在设备尝试根据所发现的服务器映射域凭据时发生帐户锁定的情况。 <br/><br/>若要让设备通过域控制器验证域凭据，该设备应能够解析域名。 请确保在添加凭据时提供了正确的域名，否则验证将失败。<br/><br/> 设备不会尝试映射验证失败的域凭据。 你需要至少有一个已成功验证的域凭据或至少一个非域凭据才能继续执行软件清单。<br/><br/>针对 Windows 服务器自动映射的域凭据将用来执行软件清单，并且可以用来发现 Web 应用、SQL Server 实例和数据库（如果你已在 SQL Server 上配置了 Windows 身份验证模式）。<br/> [详细了解](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) SQL Server 上支持的身份验证模式的类型。
非域凭据 (Windows/Linux) | 在“添加凭据”模式下，你可以通过从下拉列表中选择相应选项来添加 Windows（非域）或 Linux（非域）凭据。   <br/><br/> 你需要为凭据、用户名和密码指定一个易记名称。
SQL Server 身份验证凭据 | 在“添加凭据”模式下，你可以通过从下拉列表中选择相应选项来添加“SQL Server 身份验证”凭据。  <br/><br/> 你需要为凭据、用户名和密码指定一个易记名称。 <br/><br/> 如果你已在 SQL Server 上配置了 SQL Server 身份验证模式，则可以添加此类型的凭据，以发现在 VMware 环境中运行的 SQL Server 实例和数据库。<br/> [详细了解](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) SQL Server 上支持的身份验证模式的类型。<br/><br/> 你需要提供至少一个已成功验证的域凭据或至少一个 Windows（非域）凭据，以便使设备能够完成软件清单来发现服务器上安装的 SQL，然后再使用 SQL Server 身份验证凭据来发现 SQL Server 实例和数据库。

检查 Windows/Linux 凭据所需的权限，以执行软件清单、无代理依赖项分析以及发现 Web 应用、SQL Server 实例和数据库。

### <a name="required-permissions"></a>所需的权限

下表列出了使用设备上提供的服务器凭据执行相应功能所需的权限：

功能 | Windows 凭据 | Linux 凭据
---| ---| ---
**软件清单** | 来宾用户帐户 | 常规/普通用户帐户（非 sudo 访问权限）
发现 SQL Server 实例和数据库 | 作为 sysadmin 服务器角色成员的用户帐户。 | _当前不支持_
**ASP.NET Web 应用的发现** | 具有管理权限的域或非域（本地）帐户 | _当前不支持_
**无代理依赖项分析** | 具有管理权限的域或非域（本地）帐户 | 根用户帐户，或 <br/> 对 /bin/netstat 和 /bin/ls 文件具有以下权限的帐户：CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。<br/><br/> 使用以下命令设置这些功能： <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>有关如何提供凭据的建议做法

- 建议创建一个拥有[必需权限](add-server-credentials.md#required-permissions)的专用域用户帐户，该帐户的权限范围是对所需服务器上的 Web 应用、SQL Server 实例和数据库执行软件清单、无代理依赖项分析和发现。
- 建议提供至少一个已成功验证的域凭据或至少一个非域凭据，以便开始执行软件清单。
- 如果你已在 SQL Server 上配置了 Windows 身份验证模式，则若要发现 SQL Server 实例和数据库，你可以提供域凭据。
- 如果你已在 SQL Server 上配置了 SQL Server 身份验证模式，则还可以提供 SQL Server 身份验证凭据，但建议提供至少一个已成功验证的域凭据或至少一个 Windows（非域）凭据，以便设备可以先完成软件清单。

## <a name="credentials-handling-on-appliance"></a>设备上的凭据处理

- 在设备配置管理器上提供的所有凭据都存储在设备服务器本地，不会发送到 Azure。
- 存储在设备服务器上的凭据使用数据保护 API (DPAPI) 进行加密。
- 添加凭据后，设备会尝试自动映射凭据，以在相应的服务器上执行发现操作。
- 设备使用服务器上自动映射的凭据来完成所有后续的发现循环，直到凭据能够获取所需的发现数据为止。 如果凭据失效，则设备会再次尝试从已添加的凭据的列表中进行映射，并在服务器上继续执行正在进行的发现操作。
- 添加的域凭据将根据域的 Active Directory 自动验证其真实性。 这是为了防止在设备尝试根据所发现的服务器映射域凭据时发生帐户锁定的情况。 设备不会尝试映射验证失败的域凭据。
- 如果设备无法针对某个服务器映射任何域或非域凭据，你会在项目中看到针对该服务器显示的“凭据不可用”状态。

## <a name="next-steps"></a>后续步骤

查看与[发现在 VMware 环境中运行的服务器](tutorial-discover-vmware.md)相关的教程