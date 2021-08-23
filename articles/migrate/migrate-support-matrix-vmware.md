---
title: Azure Migrate 中的 VMware 服务器评估支持
description: 了解适用于 VMware 服务器环境的 Azure Migrate 发现和评估支持。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: de4d66f3ef8195e13ff8b67538901d1ebc7d88aa
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971066"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 评估支持矩阵 

本文汇总了使用 [Azure Migrate：发现和评估](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)工具来对用于 Azure 迁移的 VMware 服务器执行发现与评估的先决条件和支持要求。

若要评估服务器，请先创建 Azure Migrate 项目。 “Azure Migrate：发现和评估”工具会自动添加到改项目。 然后，部署 Azure Migrate 设备。 此设备会持续发现本地服务器，并向 Azure 发送配置和性能元数据。 发现完成后，将发现的服务器收集到组中，并按组运行评估。

在计划将 VMware 服务器迁移到 Azure 时，请查看[迁移支持矩阵](migrate-support-matrix-vmware-migration.md)。

## <a name="limitations"></a>限制

要求 | 详细信息
--- | ---
**项目限制** | 可以在一个 Azure 订阅中创建多个 Azure Migrate 项目。<br /><br /> 可以在单个[项目](migrate-support-matrix.md#project)中最多发现和评估 VMware 环境中的 50,000 个服务器。 项目中还可以包含物理服务器和来自 Hyper-V 环境的服务器，只要不超过评估限制即可。
**发现** | Azure Migrate 设备最多可以在 vCenter Server 上发现 10,000 个服务器。
**评估** | 在一个组中最多可以添加 35000 台服务器。<br /><br /> 在一次评估中最多可以评估 35000 台服务器。

了解有关[评估](concepts-assessment-calculation.md)的详细信息。

## <a name="vmware-requirements"></a>VMware 要求

VMware | 详细信息
--- | ---
**vCenter Server** | 要发现和评估的服务器必须由 vCenter Server 7.0、6.7、6.5、6.0 或 5.5 管理。<br /><br /> 目前不支持通过在设备中提供 ESXi 主机详细信息来发现服务器。
**权限** | “Azure Migrate：发现和评估”工具需要 vCenter Server 只读帐户。<br /><br /> 如果要使用该工具执行软件清单和无代理依赖关系分析，则帐户必须具有在 VMware VM 上执行来宾操作的权限。

## <a name="server-requirements"></a>服务器要求

VMware | 详细信息
--- | ---
**操作系统** | 可以对所有 Windows 和 Linux 操作系统进行迁移评估。
**存储** | 支持附加到 SCSI、IDE 和基于 SATA 的控制器的磁盘。

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用 [Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 可以使用导入到 vCenter Server 的 VMware 开放式虚拟化设备 (OVA) 模板，或者使用 [PowerShell 脚本](deploy-appliance-script.md)将设备部署为 VMware 环境中的服务器。 了解 [VMware 的设备要求](migrate-appliance.md#appliance---vmware)。

以下介绍设备的更多要求：

- 在 Azure 政府中，必须使用[脚本](deploy-appliance-script-government.md)部署设备。
- 设备必须能够访问[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)中的特定 URL。

## <a name="port-access-requirements"></a>端口访问要求

设备 | 连接
--- | ---
Azure Migrate 设备 | TCP 端口 3389 上的入站连接，可便于通过远程桌面连接来连接到设备。<br /><br /> 端口 44368 上的入站连接，使用 URL `https://<appliance-ip-or-name>:44368` 远程访问设备管理应用。 <br /><br />端口 443 (HTTPS) 上的出站连接，用于向 Azure Migrate 发送发现和性能元数据。
**vCenter Server** | TCP 端口 443 上的入站连接，可便于设备收集用于评估的配置和性能元数据。 <br /><br /> 默认情况下，设备在端口 443 上连接到 vCenter。 如果 vCenter Server 在不同端口上侦听，可以在设置发现时修改端口。
**ESXi 主机** | 若要[发现软件清单](how-to-discover-applications.md)或[无代理依赖关系分析](concepts-dependency-visualization.md#agentless-analysis)，可以将设备连接到 TCP 端口 443 上的 ESXi 主机，以发现服务器上的软件清单和依赖关系。

## <a name="application-discovery-requirements"></a>应用程序发现要求

除发现服务器之外，“Azure Migrate：发现和评估”工具还可以针对在服务器上运行的软件完成软件清单。 通过应用程序发现，可以确定和计划为本地工作负载定制的迁移路径。

支持 | 详细信息
--- | ---
**支持的服务器** | 目前仅支持 VMware 环境中的服务器。 最多可从每台 Azure Migrate 设备的 10,000 个服务器上完成应用程序发现。
**操作系统** | 支持运行所有 Windows 和 Linux 版本的服务器。
**VM 要求** | 若要发现软件清单，必须在服务器上安装并运行 VMware 工具。 <br /><br /> VMware 工具版本必须为 10.2.1 或更高版本。<br /><br /> 在 Windows 服务器上必须安装 PowerShell 2.0 或更高版本。
**发现** | 使用服务器上安装的 VMware 工具，从 vCenter Server 执行应用程序发现。 设备通过 vSphere API 从运行 vCenter Server 的服务器收集有关软件清单的信息。 应用程序发现是无代理的。 服务器上未安装代理，并且设备不会直接连接到服务器。 WMI 和 SSH 必须分别在 Windows 和 Linux 服务器上启用及使用。
vCenter Server 用户帐户 | 若要与服务器交互以发现应用程序，用于评估的 vCenter Server 只读帐户必须具有在 VMware VM 上执行来宾操作的权限。
**服务器访问** | 可以在用于发现应用程序的设备配置管理器中添加多个域和非域 (Windows/Linux) 凭据。<br /><br /> 你必须拥有适用于 Windows 服务器的来宾用户帐户和适用于所有 Linux 的标准用户帐户（访问 non-`sudo`）。
**端口访问** | Azure Migrate 设备必须能够连接到运行要在其上执行应用程序发现的服务器的 ESXi 主机上的 TCP 端口 443。 运行 vCenter Server 的服务器将返回 ESXi 主机连接，以下载包含软件清单详细信息的文件。

## <a name="sql-server-instance-and-database-discovery-requirements"></a>SQL Server 实例和数据库发现要求

[应用程序发现](how-to-discover-applications.md)会标识 SQL Server 实例。 设备会使用此信息，尝试通过设备配置管理器中提供的 Windows 身份验证或 SQL Server 身份验证凭据连接到相应的 SQL Server 实例。 连接设备后，其会收集 SQL Server 实例和数据库的配置及性能数据。 SQL Server 配置数据每 24 小时更新一次。 每 30 秒捕获一次性能数据。

支持 | 详细信息
--- | ---
**支持的服务器** | 目前仅支持在 VMware 环境中运行 SQL Server 的服务器。 可发现最多 300 个 SQL Server 实例或 6,000 个 SQL 数据库（以较小者为准）。
**Windows 服务器** | 支持 Windows Server 2008 及更高版本。
**Linux 服务器** | 当前不支持。
身份验证机制 | 同时支持 Windows 和 SQL Server 身份验证。 可以在设备配置管理器中提供这两种身份验证类型的凭据。
**SQL Server 访问** | Azure Migrate 需要 Windows 用户帐户，该帐户属于其中一个 sysadmin 服务器角色。
**SQL Server 版本** | 支持 SQL Server 2008 及更高版本。
**SQL Server 版本** | 支持 Enterprise、Standard、Developer 和 Express 版本。
支持的 SQL 配置 | 目前仅支持发现独立 SQL Server 实例及相应数据库。<br /><br /> 不支持故障转移群集和 Always On 可用性组的识别。
支持的 SQL 服务 | 仅支持 SQL Server 数据库引擎。 <br /><br /> 不支持 SQL Server Reporting Services (SSRS)、SQL Server Integration Services (SSIS) 和 SQL Server Analysis Services (SSAS) 的发现。

> [!NOTE]
> [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) 属性设置为 `true` 时，Azure Migrate 会加密 Azure Migrate 设备和源 SQL Server 实例之间的通信。 传输层使用 SSL 加密通道并绕过证书链来验证信任。 必须将设备服务器设置为[信任证书的根颁发机构](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。
>
> 如果服务器在启动时未预配有任何证书，SQL Server 将生成可用于加密登录数据包的自签名证书。 [了解详细信息](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。
>

## <a name="dependency-analysis-requirements-agentless"></a>依赖关系分析要求（无代理）

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地服务器之间的依赖关系。 下表总结了设置无代理依赖关系分析所需满足的要求：

支持 | 详细信息
--- | --- 
**支持的服务器** | 目前仅支持 VMware 环境中的服务器。
**Windows 服务器** | Windows Server 2016<br /> Windows Server 2012 R2<br /> Windows Server 2012<br /> Windows Server 2008 R2（64 位）<br />Microsoft Windows Server 2008（32 位）
**Linux 服务器** | Red Hat Enterprise Linux 7、6、5<br /> Ubuntu Linux 16.04、14.04<br /> Debian 8、7<br /> Oracle Linux 7、6<br /> CentOS 7、6、5<br /> SUSE Linux Enterprise Server 11 及更高版本
**服务器要求** | 必须在要分析的服务器上安装并运行 VMware 工具（10.2.1 及更高版本）。<br /><br /> 服务器必须安装 PowerShell 版本 2.0 或更高版本。
**发现方法** |  通过使用在运行 vCenter Server 的服务器上安装的 VMware 工具，即可收集服务器之间的依赖关系信息。 设备使用 vSphere API 收集服务器上的信息。 不会在服务器上安装代理，并且设备不会直接连接到服务器。 应在 Windows 和 Linux 服务器上分别启用和使用 WMI 和 SSH。
vCenter 帐户 | 用于评估的 Azure Migrate 只读帐户必须具有在 VMware VM 上执行来宾操作的权限。
Windows 服务器权限 |  具有服务器管理权限的用户帐户（本地或域）。
**Linux 帐户** | 根用户帐户，或对 /bin/netstat 和 /bin/ls 文件具有以下权限的帐户： <br />CAP_DAC_READ_SEARCH<br /> CAP_SYS_PTRACE<br /><br /> 使用以下命令设置这些功能：<br /><code>sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br /> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat</code>
**端口访问** | Azure Migrate 设备必须能够连接到运行要发现其依赖关系的服务器的 ESXi 主机上的 TCP 端口 443。 运行 vCenter Server 的服务器将返回 ESXi 主机连接，以下载包含依赖关系数据的文件。

## <a name="dependency-analysis-requirements-agent-based"></a>依赖关系分析要求（基于代理）

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地服务器之间的依赖关系。 下表总结了设置基于代理的依赖关系分析所需满足的要求：

要求 | 详细信息
--- | ---
**部署前** | 应有项目，并已将 Azure Migrate：发现和评估工具添加到项目中。<br /><br />在设置 Azure Migrate 设备来发现本地服务器后，即可部署依赖关系可视化。<br /><br />了解如何[首次创建项目](create-manage-projects.md)。<br /> 了解如何[将发现和评估工具添加到现有项目](how-to-assess.md)。<br /> 了解如何设置 Azure Migrate 设备来评估 [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md) 或物理服务器。
**支持的服务器** | 支持本地环境中的所有服务器。
**Log Analytics** | Azure Migrate 在 [Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md)中使用[服务映射](../azure-monitor/vm/service-map.md)解决方案来进行依赖关系可视化。<br /><br /> 你将新的或现有的 Log Analytics 工作区与项目相关联。 无法修改已添加的项目工作区。 <br /><br /> 工作区必须与项目位于同一订阅中。<br /><br /> 工作区必须位于美国东部、东南亚或欧洲西部区域。 其他区域中的工作区无法与项目相关联。<br /><br /> 工作区必须位于[支持服务映射的区域](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)。<br /><br /> 在 Log Analytics 中，与 Azure Migrate 关联的工作区会标记项目密钥和项目名称。
**必需代理** | 在要分析的所有服务器上，安装以下代理：<br />- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)<br />- [依赖关系代理](../azure-monitor/agents/agents-overview.md#dependency-agent)<br /><br /> 如果本地服务器没有连接到 Internet，需要下载并在服务器上安装 Log Analytics 网关。<br /><br /> 详细了解如何安装[依赖关系代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作区** | 工作区必须与项目位于同一订阅中。<br /><br /> Azure Migrate 支持位于美国东部、东南亚和欧洲西部区域的工作区。<br /><br />  工作区必须位于[支持服务映射](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)的区域中。<br /><br /> 无法修改已添加的项目工作区。
**成本** | 服务映射解决方案在前 180 天内（自 Log Analytics 工作区与项目关联之日起计）不收取任何费用。<br /><br /> 180 天后，将收取标准 Log Analytics 费用。<br /><br /> 在关联的 Log Analytics 工作区中使用除服务映射以外的其他任何解决方案都会产生 Log Analytics [标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br /><br /> 删除项目时，工作区不会自动删除。 删除项目后，需付费使用服务映射，每个节点将按照 Log Analytics 工作区的付费层进行收费。<br /><br />如果你有在正式发布 Azure Migrate（2018 年 2 月 28 日）之前创建过项目，可能需要支付额外的服务映射费用。 为确保你仅在 180 天后开始付费，我们建议你创建新项目。 正式发布前创建的工作区仍可计费。
**管理** | 将代理注册到工作区时，请使用项目提供的 ID 和密钥。<br /><br /> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br /><br /> 如果你删除关联的项目，工作区不会自动删除。 [请手动删除它](../azure-monitor/logs/manage-access.md)。<br /><br /> 不要删除由 Azure Migrate 创建的工作区，除非你删除项目。 否则，依赖关系可视化功能将不会按预期工作。
**Internet 连接** | 如果服务器没有连接到 Internet，需要下载并在服务器上安装 Log Analytics 网关。
**Azure Government** | 不支持基于代理的依赖关系分析。

## <a name="next-steps"></a>后续步骤

- 查看[最佳评估做法](best-practices-assessment.md)。
- 了解如何[做好 VMware 评估准备](./tutorial-discover-vmware.md)。