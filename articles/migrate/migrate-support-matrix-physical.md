---
title: Azure Migrate 中的物理发现和评估支持
description: 了解使用 Azure Migrate 发现和评估功能时的物理发现和评估支持
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 2d68a74332ef77694d44597e6f879858fa0051bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726226"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>物理服务器发现与评估的支持矩阵 

本文总结了在使用 [Azure Migrate：发现与评估](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)工具评估要迁移到 Azure 的物理服务器时的先决条件和支持要求。 若要将物理服务器迁移到 Azure，请查阅[迁移支持矩阵](migrate-support-matrix-physical-migration.md)。

若要评估物理服务器，需要创建一个项目，然后将“Azure Migrate：发现和评估”工具添加到项目中。 添加评估工具后，部署 [Azure Migrate 设备](migrate-appliance.md)。 此设备持续发现本地服务器，并向 Azure 发送服务器元数据和性能数据。 完成发现后，你将发现的服务器收集到组中，然后对组运行评估。

## <a name="limitations"></a>限制

**支持** | **详细信息**
--- | ---
**评估限制** | 最多可以在一个[项目](migrate-support-matrix.md#project)中发现和评估 35000 个物理服务器。
**项目限制** | 可以在一个 Azure 订阅中创建多个项目。 除物理服务器外，项目还可以包括 VMware 和 Hyper-V 上的服务器，只要不超过每个项目的评估限制即可。
**发现** | Azure Migrate 设备最多可发现 1000 个物理服务器。
**评估** | 在一个组中最多可以添加 35000 台服务器。<br/><br/> 在一次评估中最多可以评估 35000 台服务器。

[详细了解](concepts-assessment-calculation.md)评估。

## <a name="physical-server-requirements"></a>物理服务器要求

**物理服务器部署：** 物理服务器可以是独立服务器，也可以部署到群集中。

**操作系统：** 可以对所有 Windows 和 Linux 操作系统进行迁移评估。

**权限：**

设置一个可供设备用于访问物理服务器的帐户。

**Windows 服务器**

- 对于 Windows 服务器，针对已加入域的服务器使用域帐户，针对未加入域的服务器使用本地帐户。 
- 应将用户帐户添加到这些组：远程管理用户、性能监视器用户和性能日志用户。 
- 如果远程管理用户组不存在，请将用户帐户添加到以下组：WinRMRemoteWMIUsers_。
- 该帐户需要拥有这些权限才能让设备创建与服务器的 CIM 连接，并从[此处](migrate-appliance.md#collected-data---physical)列出的 WMI 类中拉取所需的配置和性能元数据。
- 在某些情况下，将帐户添加到这些组可能不会从 WMI 类返回所需的数据，因为该帐户可能由 [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) 筛选。 要绕过 UAC 筛选，用户帐户需在目标服务器上的 CIMV2 命名空间和子命名空间中拥有所需的权限。 可按[此处](troubleshoot-appliance.md#access-is-denied-when-connecting-to-physical-servers-during-validation)所述的步骤启用所需的权限。

    > [!Note]
    > 对于 Windows Server 2008 和 2008 R2，请确保在服务器上安装了 WMF 3.0。

**Linux 服务器**

- 在要发现的服务器上需有一个根帐户。 也可提供拥有 sudo 权限的用户帐户。
- 2021 年 7 月 20 日之后从门户下载的新设备安装程序脚本默认支持添加具有 sudo 访问权限的用户帐户。
- 对于较旧的设备，可按照以下步骤来启用该功能：
    1. 在运行设备的服务器上，打开注册表编辑器。
    1. 导航到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance。
    1. 创建 DWORD 值为 1 的注册表项“isSudo”。

    :::image type="content" source="./media/tutorial-discover-physical/issudo-reg-key.png" alt-text="显示如何启用 sudo 支持的屏幕截图。":::

- 要发现目标服务器中的配置和性能元数据，需要为[此处](migrate-appliance.md#linux-server-metadata)列出的命令启用 sudo 访问权限。 确保已启用“NOPASSWD”，使帐户能够运行所需的命令，且不会在每次调用 sudo 命令时都提示用户输入密码。
- 以下 Linux OS 发行版支持使用拥有 sudo 访问权限的帐户通过 Azure Migrate 进行发现：

    操作系统 | 版本 
    --- | ---
    Red Hat Enterprise Linux | 6、7、8
    Cent OS | 6.6、8.2
    Ubuntu | 14.04、16.04、18.04
    SUSE Linux | 11.4、12.4
    Debian | 7、10
    Amazon Linux | 2.0.2021
    CoreOS 容器 | 2345.3.0

- 如果无法提供具有 sudo 访问权限的根帐户或用户帐户，则可以在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance 注册表中将“isSudo”注册表项的值设置为“0”，并使用以下命令为非根帐户提供所需的功能：

**命令** | **用途**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(if /usr/sbin/fdisk is not present)_ | 收集磁盘配置
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | 收集磁盘性能数据
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | 收集 BIOS 序列号
chmod a+r /sys/class/dmi/id/product_uuid | 收集 BIOS GUID

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 设备要求

Azure Migrate 使用 [Azure Migrate 设备](migrate-appliance.md)进行发现和评估。 适用于物理服务器的设备可在 VM 或物理服务器上运行。

- 了解物理服务器的[设备要求](migrate-appliance.md#appliance---physical)。
- 了解设备需要在[公有](migrate-appliance.md#public-cloud-urls)云和[政府](migrate-appliance.md#government-cloud-urls)云中访问的 URL。
- 使用从 Azure 门户下载的 [PowerShell 脚本](how-to-set-up-appliance-physical.md)设置设备。
在 Azure 政府中，请[使用此脚本](deploy-appliance-script-government.md)部署设备。

## <a name="port-access"></a>端口访问

下表汇总了评估的端口要求。

**设备** | **Connection**
--- | ---
**设备** | TCP 端口 3389 上的入站连接，可便于通过远程桌面连接来连接到设备。<br/><br/> 端口 44368 上的入站连接，用于使用 URL ``` https://<appliance-ip-or-name>:44368 ``` 远程访问设备管理应用<br/><br/> 端口 443 (HTTPS) 上的出站连接，用于将发现和性能元数据发送到 Azure Migrate。
**物理服务器** | **Windows：** WinRM 端口 5985 (HTTP) 上的入站连接，用于从 Windows 服务器上拉取配置和性能元数据。 <br/><br/> **Linux：** 端口 22 (TCP) 上的入站连接，用于从 Linux 服务器上拉取配置和性能元数据。 |

## <a name="agent-based-dependency-analysis-requirements"></a>基于代理的依赖关系分析要求

[依赖关系分析](concepts-dependency-visualization.md)有助于确定要评估并迁移到 Azure 的本地服务器之间的依赖关系。 下表总结了设置基于代理的依赖关系分析所需满足的要求。 物理服务器目前仅支持基于代理的依赖关系分析。

**要求** | **详细信息**
--- | ---
**部署前** | 应有项目，并已将 Azure Migrate：发现和评估工具添加到项目中。<br/><br/>  在设置 Azure Migrate 设备来发现本地服务器后，就可以部署依赖关系可视化了<br/><br/> [了解如何](create-manage-projects.md)首次创建项目。<br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/> 了解如何设置 Azure Migrate 设备来评估 [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md) 或物理服务器。
**Azure Government** | 依赖关系可视化在 Azure 政府中不可用。
**Log Analytics** | Azure Migrate 在 [Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md)中使用[服务映射](../azure-monitor/vm/service-map.md)解决方案来进行依赖关系可视化。<br/><br/> 你将新的或现有的 Log Analytics 工作区与项目相关联。 项目的工作区在添加后就无法修改了。 <br/><br/> 工作区必须与项目位于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或欧洲西部区域。 其他区域中的工作区无法与项目相关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)的区域中。<br/><br/> 在 Log Analytics 中，与 Azure Migrate 关联的工作区标记有迁移项目密钥和项目名称。
**必需代理** | 在要分析的每个服务器上，安装以下代理：<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)。<br/> [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent)。<br/><br/> 如果本地服务器没有连接到 Internet，你需要在服务器上下载并安装 Log Analytics 网关。<br/><br/> 详细了解如何安装 [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 和 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作区** | 工作区必须与项目位于同一订阅中。<br/><br/> Azure Migrate 支持位于美国东部、东南亚和欧洲西部区域的工作区。<br/><br/>  工作区必须位于[支持服务映射](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)的区域中。<br/><br/> 项目的工作区在添加后就无法修改了。
**成本** | 服务映射解决方案在前 180 天内（自你将 Log Analytics 工作区与项目关联之日起）不收取任何费用/<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 在关联的 Log Analytics 工作区中使用除服务映射以外的其他任何解决方案都会产生 Log Analytics [标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除项目时，工作区不会随之一起删除。 删除项目后，服务映射的使用不是免费的，每个节点将按照 Log Analytics 工作区的付费层收费。<br/><br/>如果你有在 Azure Migrate 正式发布（2018 年 2 月 28 日正式发布）之前创建的项目，可能需要支付额外的服务映射费用。 为了确保只在 180 天后付款，建议新建项目，因为在 Azure Migrate 正式发布之前的现有工作区仍需要收费。
**管理** | 将代理注册到工作区时，你使用项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果你删除关联的项目，工作区不会自动删除。 [请手动删除它](../azure-monitor/logs/manage-access.md)。<br/><br/> 不要删除由 Azure Migrate 创建的工作区，除非你删除项目。 否则，依赖项可视化功能将不会按预期工作。
**Internet 连接** | 如果服务器没有连接到 Internet，你需要在服务器上下载并安装 Log Analytics 网关。
**Azure Government** | 不支持基于代理的依赖关系分析。

## <a name="next-steps"></a>后续步骤

[为物理发现和评估作准备](./tutorial-discover-physical.md)。