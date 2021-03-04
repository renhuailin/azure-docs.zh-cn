---
title: Azure Migrate 中的 VMware 评估支持
description: 了解支持 Azure Migrate Server 评估的 VMware VM 评估
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 7e0bc21fde2c030de7a836d82384c09c78d993ad
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047819"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 评估支持矩阵 

本文总结了在使用 [Azure Migrate： Server 评估](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具发现和评估 VMware 环境中运行的服务器迁移到 Azure 时的先决条件和支持要求。 若要评估服务器，请创建一个 Azure Migrate 项目，该项目将服务器评估工具添加到项目。 添加评估工具后，部署 Azure Migrate 设备。 设备持续发现本地服务器，并将配置和性能元数据发送到 Azure。 发现完成后，可将发现的服务器收集到组中，并对组运行评估。

如果要将 VMware 服务器迁移到 Azure，请参阅 [迁移支持矩阵](migrate-support-matrix-vmware-migration.md)。



## <a name="limitations"></a>限制

**要求** | **详细信息**
--- | ---
**项目限制** | 可以在一个 Azure 订阅中创建多个项目。<br/><br/> 你可以在单个 [项目](migrate-support-matrix.md#azure-migrate-projects)中发现并评估多达50000的 VMware 环境的服务器。 项目还可以包括物理服务器，以及 Hyper-v 环境中的服务器，直至评估限制。
**发现** | Azure Migrate 设备在 vCenter Server 上最多可以发现10000服务器。
**评估** | 最多可以在一个组中添加35000服务器。<br/><br/> 在单个评估中，最多可以评估35000服务器。

[详细了解](concepts-assessment-calculation.md)评估。


## <a name="vmware-requirements"></a>VMware 要求

**VMware** | **详细信息**
--- | ---
**vCenter Server** | 要发现和评估的服务器必须通过 vCenter Server 5.5、6.0、6.5、6.7 或7.0 版本来管理。<br/><br/> 当前不支持通过在设备中提供 ESXi 主机详细信息来发现服务器。
**权限** | 服务器评估需要 vCenter Server 只读帐户进行发现和评估。<br/><br/> 如果要执行已安装应用程序的发现和无代理依赖项分析，则帐户需要为 **虚拟机**  >  **来宾操作** 启用特权。

## <a name="server-requirements"></a>服务器要求
**VMware** | **详细信息**
--- | ---
**支持的操作系统** | 所有 Windows 和 Linux 操作系统都可以进行迁移评估。
**存储** | 支持附加到 SCSI、IDE 和基于 SATA 的控制器的磁盘。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用 [Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 你可以使用 .OVA 模板将设备部署为 VMware 环境中的服务器，并将其导入 vCenter Server 或使用 [PowerShell 脚本](deploy-appliance-script.md)。

- 了解 VMware 的[设备要求](migrate-appliance.md#appliance---vmware)。
- 在 Azure 政府中，必须[使用脚本](deploy-appliance-script-government.md)部署设备。
- 查看设备需要在 [公共](migrate-appliance.md#public-cloud-urls) 和 [政府](migrate-appliance.md#government-cloud-urls) 云中访问的 url。


## <a name="port-access-requirements"></a>端口访问要求

**设备** | **Connection**
--- | ---
**设备** | TCP 端口 3389 上的入站连接，可便于通过远程桌面连接来连接到设备。<br/><br/> 端口 44368 上的入站连接，用于使用 URL ```https://<appliance-ip-or-name>:44368``` 远程访问设备管理应用 <br/><br/>端口 443 (HTTPS) 上的出站连接，用于将发现和性能元数据发送到 Azure Migrate。
**vCenter 服务器** | TCP 端口 443 上的入站连接，可便于设备收集用于评估的配置和性能元数据。 <br/><br/> 默认情况下，设备在端口 443 上连接到 vCenter。 如果 vCenter 服务器在不同的端口上侦听，你可以在设置发现时修改端口。
**ESXi 主机** | 如果要对 [已安装的应用程序](how-to-discover-applications.md)或 [无代理依赖项分析](concepts-dependency-visualization.md#agentless-analysis)执行发现，则设备会连接到 TCP 端口443上的 ESXi 主机，以发现服务器上的应用程序和依赖项。

## <a name="application-discovery-requirements"></a>应用程序发现要求

除了发现服务器以外，服务器评估还可以发现服务器上运行的应用程序、角色和功能。 应用程序发现可用于识别和规划为本地工作负荷定制的迁移路径。

**支持** | **详细信息**
--- | ---
**支持的服务器** | 目前仅支持 VMware 环境中的服务器。 从每个 Azure Migrate 设备，最多可以在10000服务器上执行应用程序发现。
**操作系统** | 支持运行所有 Windows 和 Linux 版本的服务器。
**VM 要求** | 若要执行安装的应用程序的发现，必须在服务器上安装并运行 VMware 工具。 <br/><br/> VMware 工具版本必须高于 10.2.0。<br/><br/> Windows server 必须安装 PowerShell 2.0 或更高版本。
**发现** | 服务器上的应用程序发现使用安装在服务器上的 VMware 工具从 vCenter Server 执行。 设备使用 vSphere Api 从 vCenter Server 收集有关安装的应用程序的信息。 应用程序发现是无代理的。 未在服务器上安装代理，并且设备不会直接连接到服务器。 WMI 和 SSH 应该分别在 Windows 和 Linux 服务器上启用和使用。
**vCenter Server 用户帐户** | 用于评估的 vCenter Server 只读帐户，需要为 **虚拟机**  >  **来宾操作** 启用特权，以便与服务器进行应用程序发现的交互。
**服务器访问** | 在应用程序发现的设备配置管理器上，你可以添加多个域和非域 (Windows/Linux) 凭据。<br/><br/> 你需要为 Windows server 使用来宾用户帐户，并且需要为所有 Linux 服务器 (非 sudo 访问) 的常规/普通用户帐户。
**端口访问** | Azure Migrate 设备必须能够连接到运行要在其上执行应用程序发现的服务器的 ESXi 主机上的 TCP 端口443。 VCenter Server 返回 ESXi 主机连接，以下载包含已安装应用程序的详细信息的文件。

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>SQL Server 实例和数据库的发现要求

> [!Note]
> 在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估现在为预览版。 若要试用此功能，请使用 [**此链接**](https://aka.ms/AzureMigrate/SQL) 在 **澳大利亚东部** 区域中创建一个项目。 如果你已有一个澳大利亚东部的项目，并且想要尝试此功能，请确保已在门户上完成这些 [**先决条件**](how-to-discover-sql-existing-project.md) 。

[应用程序发现](how-to-discover-applications.md) 标识了 SQL Server 的实例。 通过使用此信息，设备会尝试通过 Windows 身份验证或设备上提供 SQL Server 身份验证凭据连接到相应的 SQL Server 实例。 连接后，设备会收集 SQL Server 实例和数据库的配置和性能数据。 SQL Server 配置数据每24小时更新一次，每隔30秒捕获一次性能数据。

**支持** | **详细信息**
--- | ---
**支持的服务器** | 目前仅支持 VMware 环境中的 SQL Server。 最多可以发现 300 SQL Server 实例或 6000 SQL 数据库，以较低者为准。
**Windows 服务器** | 支持 Windows Server 2008 及更高版本。
**Linux 服务器** | 当前不支持。
**身份验证机制** | 同时支持 Windows 和 SQL Server 身份验证。 你可以在设备配置管理器上提供这两种身份验证类型的凭据。
**SQL Server 访问** | Azure Migrate 需要作为 sysadmin 服务器角色成员的 Windows 用户帐户。
**SQL Server 版本** | 支持 SQL Server 2008 及更高版本。
**SQL Server 版本** | 支持 Enterprise edition、Developer edition 和速成版。
**支持的 SQL 配置** | 目前仅支持独立 SQL Server 实例和相应数据库的发现。<br/> 不支持故障转移群集和 Always On 可用性组的标识。
**支持的 SQL 服务** | 仅支持 SQL Server 数据库引擎。 <br/> 不支持 SQL Server Reporting Services (SSRS) 、SQL Server Integration Services (SSIS) 和 SQL Server Analysis Services (SSAS) 的发现。

> [!Note]
> Azure Migrate 会对 Azure Migrate 设备与源 SQL Server 实例之间的通信进行加密， (将 "加密连接" 属性设置为 "TRUE) "。 这些连接通过 [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (设置为 TRUE) 进行加密;传输层将使用 SSL 来加密通道并跳过证书链以验证信任。 设备服务器必须设置为 [**信任证书的根证书颁发机构**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。<br/>
如果在服务器启动时未在服务器上预配证书，SQL Server 将生成用于对登录数据包进行加密的自签名证书。 [**了解详细信息**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。

## <a name="dependency-analysis-requirements-agentless"></a>依赖关系分析要求 (无代理) 

[依赖关系分析](concepts-dependency-visualization.md) 有助于确定要评估并迁移到 Azure 的本地服务器之间的依赖关系。 下表总结了设置无代理依赖关系分析所需满足的要求。

**支持** | **详细信息**
--- | --- 
**支持的服务器** | 目前仅支持 VMware 环境中的服务器。
**Windows 服务器** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2（64 位）。<br/>Microsoft Windows Server 2008 (32) 。
**Linux 服务器** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。<br/> SUSE Linux Enterprise Server 11 及更高版本。
**服务器要求** | 必须在要分析的服务器上安装和运行 VMware Tools (10.2.0) 。<br/><br/> 服务器必须安装 PowerShell 2.0 或更高版本。
**发现方法** |  服务器之间的依赖关系信息将使用 VM 上安装的 VMware 工具从 vCenter Server 中收集。 设备使用 vSphere Api 从 vCenter Server 中收集信息。 未在服务器上安装代理，并且设备不会直接连接到服务器。 WMI 和 SSH 应该分别在 Windows 和 Linux 服务器上启用和使用。
**vCenter 帐户** | 用于评估 Azure Migrate 的只读帐户需要为 **虚拟机启用 > 来宾操作** 的特权。
**Windows server 权限** |  使用服务器上的管理权限 (本地或域) 的用户帐户。
**Linux 帐户** | 根用户帐户或对/bin/netstat 和/bin/ls 文件具有以下权限的帐户： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。<br/><br/> 使用以下命令设置这些功能： <br/><br/> sudo setcap CAP_DAC_READ_SEARCH，CAP_SYS_PTRACE = ep/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH，CAP_SYS_PTRACE = ep/bin/netstat
**端口访问** | Azure Migrate 设备必须能够连接到运行要发现其依赖关系的服务器的 ESXi 主机上的 TCP 端口443。 VCenter Server 返回 ESXi 主机连接，以下载包含依赖关系数据的文件。


## <a name="dependency-analysis-requirements-agent-based"></a>基于代理的)  (依赖关系分析需求

[依赖关系分析](concepts-dependency-visualization.md) 有助于确定要评估并迁移到 Azure 的本地服务器之间的依赖关系。 下表总结了设置基于代理的依赖关系分析所需满足的要求。

要求 | **详细信息** 
--- | --- 
**部署前** | 应有 Azure Migrate 项目（使用 Azure Migrate）：已将服务器评估工具添加到项目中。<br/><br/>  设置 Azure Migrate 设备后，请部署依赖关系可视化，以发现本地服务器。<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md) 向现有项目添加发现和评估工具。<br/> 了解如何设置 Azure Migrate 设备来评估 [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md) 或物理服务器。
**支持的服务器** | 对于本地环境中的所有服务器均受支持。
**Log Analytics** | Azure Migrate 在 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)中使用[服务映射](../azure-monitor/insights/service-map.md)解决方案来进行依赖关系可视化。<br/><br/> 你将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。 添加项目后，不能修改该工作区。 <br/><br/> 工作区必须位于与项目相同的订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或欧洲西部区域。 其他区域中的工作区无法与项目相关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的区域中。<br/><br/> 在 Log Analytics 中，与 Azure Migrate 关联的工作区标记有迁移项目密钥和项目名称。
**必需代理** | 在要分析的每个服务器上安装以下代理：<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)。<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地服务器未连接到 internet，则需要下载并安装 Log Analytics 网关。<br/><br/> 详细了解如何安装 [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 和 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作区** | 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和欧洲西部区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的区域中。<br/><br/> Azure Migrate 项目的工作区在添加后就无法修改了。
**成本** | 服务映射解决方案在前 180 天内（自你将 Log Analytics 工作区与 Azure Migrate 项目关联之日起）不收取任何费用。<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 在关联的 Log Analytics 工作区中使用除服务映射以外的其他任何解决方案都会产生 Log Analytics [标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射的使用不是免费的，每个节点将按照 Log Analytics 工作区的付费层收费。<br/><br/>如果你有在 Azure Migrate 正式发布（2018 年 2 月 28 日正式发布）之前创建的项目，可能需要支付额外的服务映射费用。 为了确保只在 180 天后付款，建议新建项目，因为在 Azure Migrate 正式发布之前的现有工作区仍需要收费。
**管理** | 将代理注册到工作区时，你使用 Azure Migrate 项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果你删除关联的 Azure Migrate 项目，工作区不会自动删除。 [请手动删除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非删除 Azure Migrate 项目，否则请不要删除由 Azure Migrate 创建的工作区。 否则，依赖项可视化功能将不会按预期工作。
**Internet 连接** | 如果服务器未连接到 internet，则需要在这些服务器上安装 Log Analytics 的网关。
**Azure Government** | 不支持基于代理的依赖关系分析。


## <a name="next-steps"></a>后续步骤

- [查阅](best-practices-assessment.md)关于创建评估的最佳做法。
- [为 VMware VM 评估做好准备](./tutorial-discover-vmware.md)。