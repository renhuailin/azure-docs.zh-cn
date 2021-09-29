---
title: Azure Migrate 设备
description: 提供对 Azure Migrate 设备的支持摘要。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 89cfaadb7d490c1d6ebf9cac21c08a402c9d8997
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565752"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 设备

本文总结了使用 Azure Migrate 设备的先决条件和支持要求。

## <a name="deployment-scenarios"></a>部署方案

Azure Migrate 设备用于以下应用场景。

**方案** | **工具** | **用于**
--- | --- | ---
发现和评估 VMware 环境中运行的服务器 | Azure Migrate：发现和评估 | 发现在你的 VMware 环境中运行的服务器<br/><br/> 发现已安装的软件清单、ASP.NET Web 应用、SQL Server 实例和数据库，并执行无代理依赖项分析。<br/><br/> 收集服务器配置和性能元数据以进行评估。
在 VMware 环境中运行的服务器的无代理迁移 | Azure Migrate:服务器迁移 | 发现在你的 VMware 环境中运行的服务器。 <br/><br/> 复制服务器，不在其上安装任何代理。
发现和评估在 Hyper-V 环境中运行的服务器 | Azure Migrate：发现和评估 | 发现在你的 Hyper-V 环境中运行的服务器。<br/><br/> 收集服务器配置和性能元数据以进行评估。
发现和评估本地的物理服务器或虚拟化服务器 |  Azure Migrate：发现和评估 |  发现本地的物理服务器或虚拟化服务器。<br/><br/> 收集服务器配置和性能元数据以进行评估。

## <a name="deployment-methods"></a>部署方法

可以使用几种方法来部署设备：

- 可以使用适用于在 VMware 或 Hyper-V 环境中运行的服务器的模板（[用于 VMware 的 OVA 模板](how-to-set-up-appliance-vmware.md)或[用于 Hyper-V 的 VHD](how-to-set-up-appliance-hyper-v.md)）来部署设备。
- 如果你不想使用模板，则可以使用 [PowerShell 安装程序脚本](deploy-appliance-script.md)为 VMware 或 Hyper-V 环境部署该设备。
- 在 Azure 政府版中，应该使用 PowerShell 安装程序脚本来部署该设备。 请参考[此处](deploy-appliance-script-government.md)的部署步骤。
- 对于本地或任何其他云中的物理服务器或虚拟化服务器，请始终使用 PowerShell 安装程序脚本来部署设备。请参考[此处](how-to-set-up-appliance-physical.md)的部署步骤。
- 下表提供了下载链接。

## <a name="appliance---vmware"></a>设备 - VMware

下表总结了 VMware 的 Azure Migrate 设备要求。

要求 | **VMware**
--- | ---
**权限** | 若要在本地或以远程方式访问设备配置管理器，则设备服务器上需要有具备管理权限的本地或域用户帐户。
**设备服务** | 设备包括以下服务：<br/><br/> - 设备配置管理器：这是一个 Web 应用程序，可以为其配置源详细信息以启动服务器的发现和评估。<br/> - VMware 发现代理：该代理收集可用于创建本地评估的服务器配置元数据。<br/>- VMware 评估代理：该代理收集可用于创建基于性能的评估的服务器性能元数据。<br/>- 自动更新服务：该服务使所有在设备上运行的代理都保持最新状态。 它每 24 小时自动运行一次。<br/>- **DRA 代理**：协调服务器复制，并协调复制的服务器与 Azure 之间的通信。 只有在使用无代理迁移将服务器复制到 Azure 时，才会使用它。<br/>- 网关：将复制的数据发送到 Azure。 只有在使用无代理迁移将服务器复制到 Azure 时，才会使用它。<br/>- **SQL 发现和评估代理**：将 SQL Server 实例和数据库的配置和性能元数据发送到 Azure。<br/>- Web 应用发现和评估代理：将 Web 应用配置数据发送到 Azure。
**项目限制** |  一个设备只能在单个项目中注册。<br/> 单个项目可以有多个已注册的设备。
**发现限制** | 一个设备最多可在 vCenter Server 上发现 10000 个运行的服务器。<br/> 设备可以连接到单个 vCenter Server。
**支持的部署** | 使用 OVA 模板部署为在 vCenter Server 上运行的新服务器。<br/><br/> 使用 PowerShell 安装程序脚本在运行 Windows Server 2016 的现有服务器上部署。
**OVA 模板** | 从项目或从[此处](https://go.microsoft.com/fwlink/?linkid=2140333)下载<br/><br/> 下载大小为 11.9 GB。<br/><br/> 下载的设备模板附带有效期为 180 天的 Windows Server 2016 评估许可证。<br/>如果评估期临近过期，建议使用 OVA 模板下载并部署新设备，或者激活设备服务器的操作系统许可证。
**OVA 验证** | 通过检查哈希值[验证](tutorial-discover-vmware.md#verify-security)从项目下载的 OVA 模板。
**PowerShell 脚本** | 请参阅[本文](./deploy-appliance-script.md#set-up-the-appliance-for-vmware)了解如何使用 PowerShell 安装程序脚本部署设备。<br/><br/> 
**硬件和网络要求** |  设备应在具有 Windows Server 2016、32-GB RAM、8 个 vCPU、约 80 GB 磁盘存储和一个外部虚拟交换机的服务器上运行。<br/> 设备需要直接访问或通过代理访问 Internet。<br/><br/> 如果使用 OVA 模板部署设备，则 vCenter Server 上需要足够的资源来创建满足硬件要求的服务器。<br/><br/> 如果在现有服务器上运行该设备，请确保该服务器运行的是 Windows Server 2016 并且符合硬件要求。<br/>_（目前只有 Windows Server 2016 支持设备部署。）_
**VMware 要求** | 如果将设备部署为 vCenter Server 上的服务器，则必须将其部署在运行 5.5、6.0、6.5、6.7 或 7.0 的 vCenter Server 上，以及运行 5.5 或更高版本的 ESXi 主机上。<br/><br/> 
**VDDK（无代理迁移）** | 若要使用该设备进行无代理的服务器迁移，则必须在设备服务器上安装 VMware vSphere VDDK。

## <a name="appliance---hyper-v"></a>设备 - Hyper-V

要求 | **Hyper-V**
--- | ---
**权限** | 若要在本地或以远程方式访问设备配置管理器，则设备服务器上需要有具备管理权限的本地或域用户帐户。
**设备服务** | 设备包括以下服务：<br/><br/> - 设备配置管理器：这是一个 Web 应用程序，可以为其配置源详细信息以启动服务器的发现和评估。<br/> - 发现代理：该代理收集可用于创建本地评估的服务器配置元数据。<br/>- 评估代理：该代理收集可用于创建基于性能的评估的服务器性能元数据。<br/>- 自动更新服务：该服务使所有在设备上运行的代理都保持最新状态。 它每 24 小时自动运行一次。
**项目限制** |  一个设备只能在单个项目中注册。<br/> 单个项目可以有多个已注册的设备。
**发现限制** | 设备最多可以发现 5000 个在 Hyper-V 环境中运行的服务器。<br/> 一个设备最多可连接到 300 个 Hyper-V 主机。
**支持的部署** | 使用 VHD 模板部署在 Hyper-V 主机上运行的服务器。<br/><br/> 使用 PowerShell 安装程序脚本在运行 Windows Server 2016 的现有服务器上部署。
**VHD 模板** | 包含 VHD 的 Zip 文件。 从项目或从[此处](https://go.microsoft.com/fwlink/?linkid=2140422)下载。<br/><br/> 下载大小为 8.91 GB。<br/><br/> 下载的设备模板附带有效期为 180 天的 Windows Server 2016 评估许可证。<br/> 如果评估期临近过期，建议下载并部署新设备，或者激活设备服务器的操作系统许可证。
**VHD 验证** | 通过检查哈希值[验证](tutorial-discover-hyper-v.md#verify-security)从项目下载的 VHD 模板。
**PowerShell 脚本** | 请参阅[本文](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)了解如何使用 PowerShell 安装程序脚本部署设备。<br/>
**硬件和网络要求**  |  设备应在具有 Windows Server 2016、16-GB RAM、8 个 vCPU、约 80 GB 磁盘存储和一个外部虚拟交换机的服务器上运行。<br/> 设备需要静态或动态 IP 地址，并且需要直接访问或通过代理访问 Internet。<br/><br/> 如果你将设备作为运行在 Hyper-V 主机上的服务器运行，则在主机上需要足够的资源来创建满足硬件要求的服务器。<br/><br/> 如果在现有服务器上运行该设备，请确保该服务器运行的是 Windows Server 2016 并且符合硬件要求。<br/>_（目前只有 Windows Server 2016 支持设备部署。）_
**Hyper-V 要求** | 如果使用 VHD 模板部署设备，那么 Azure Migrate 提供的设备是 Hyper-V VM 版本 5.0。<br/><br/> Hyper-V 主机必须运行 Windows Server 2012 R2 或更高版本。

## <a name="appliance---physical"></a>设备 - 物理服务器

要求 | **物理服务器**
--- | ---
**权限** | 若要在本地或以远程方式访问设备配置管理器，则设备服务器上需要有具备管理权限的本地或域用户帐户。
**设备服务** | 设备包括以下服务：<br/><br/> - 设备配置管理器：这是一个 Web 应用程序，可以为其配置源详细信息以启动服务器的发现和评估。<br/> - 发现代理：该代理收集可用于创建本地评估的服务器配置元数据。<br/>- 评估代理：该代理收集可用于创建基于性能的评估的服务器性能元数据。<br/>- 自动更新服务：该服务使所有在设备上运行的代理都保持最新状态。 它每 24 小时自动运行一次。
**项目限制** |  一个设备只能在单个项目中注册。<br/> 单个项目可以有多个已注册的设备。<br/>
**发现限制** | 一个设备最多可发现 1000 个物理服务器。
**支持的部署** | 使用 PowerShell 安装程序脚本在运行 Windows Server 2016 的现有服务器上部署。
**PowerShell 脚本** | 从项目或从[此处](https://go.microsoft.com/fwlink/?linkid=2140334)下载 zip 文件中的脚本 (AzureMigrateInstaller.ps1)。 [了解详细信息](tutorial-discover-physical.md)。<br/><br/> 下载大小为 85.8 MB。
**脚本验证** | 通过检查哈希值来[验证](tutorial-discover-physical.md#verify-security)从项目下载的 PowerShell 安装程序脚本。
**硬件和网络要求** |  设备应在具有 Windows Server 2016、16-GB RAM、8 个 vCPU、约 80 GB 磁盘存储的服务器上运行。<br/> 设备需要静态或动态 IP 地址，并且需要直接访问或通过代理访问 Internet。<br/><br/> 如果在现有服务器上运行该设备，请确保该服务器运行的是 Windows Server 2016 并且符合硬件要求。<br/>_（目前只有 Windows Server 2016 支持设备部署。）_

## <a name="url-access"></a>URL 访问

Azure Migrate 设备需要连接到 Internet。

- 部署设备时，Azure Migrate 会对所需的 URL 执行连接性检查。
- 你需要允许访问列表中的所有 URL。 如果只是执行评估，可以跳过对 VMware 无代理迁移标记为“必需”的 URL。
- 如果使用基于 URL 的代理连接到 Internet，请确保代理在查找 URL 时会解析接收到的任何 CNAME 记录。

### <a name="public-cloud-urls"></a>公有云 URL

**URL** | **详细信息**  
--- | --- |
*.portal.azure.com  | 导航到 Azure 门户。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com  <br/> *.microsoftazuread-sso.com | 由 Azure Active Directory 用于访问控制和标识管理
management.azure.com | 用于资源部署和管理操作
*.services.visualstudio.com | 上传用于内部监视的设备日志。
*.vault.azure.net | 在 Azure Key Vault 中管理机密。<br/> 注意：请确保要复制的服务器有权访问它。
aka.ms/* | 允许访问这些链接；用于下载和安装设备服务的最新更新。
download.microsoft.com/download | 允许从 Microsoft 下载中进行下载。
*.servicebus.windows.net | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 连接到 Azure Migrate 服务 URL。
*.hypervrecoverymanager.windowsazure.com | 用于 VMware 无代理迁移<br/><br/> 连接到 Azure Migrate 服务 URL。
\* .blob.core.windows.net |  用于 VMware 无代理迁移<br/><br/>将数据上传到存储以供迁移。

### <a name="government-cloud-urls"></a>政府云 URL

**URL** | **详细信息**  
--- | --- |
*.portal.azure.us  | 导航到 Azure 门户。
graph.windows.net | 登录到 Azure 订阅。
login.microsoftonline.us  | 由 Azure Active Directory 用于访问控制和标识管理
management.usgovcloudapi.net |  用于资源部署和管理操作
*.services.visualstudio.com | 上传用于内部监视的设备日志。
*.vault.usgovcloudapi.net | 在 Azure Key Vault 中管理机密。
aka.ms/* | 允许访问这些链接；用于下载和安装设备服务的最新更新。
download.microsoft.com/download | 允许从 Microsoft 下载中进行下载。
*.servicebus.usgovcloudapi.net  | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | 连接到 Azure Migrate 服务 URL。
*.hypervrecoverymanager.windowsazure.us | 用于 VMware 无代理迁移<br/><br/> 连接到 Azure Migrate 服务 URL。
*.blob.core.usgovcloudapi.net  |  用于 VMware 无代理迁移<br/><br/>将数据上传到存储以供迁移。
*.applicationinsights.us | 上传用于内部监视的设备日志。  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>用于专用链接连接的公有云 URL

除了专用链接访问之外，设备还需要访问以下 URL（直接访问或通过代理进行访问）。

**URL** | **详细信息**  
--- | --- |
*.portal.azure.com  | 导航到 Azure 门户。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> *.microsoftazuread-sso.com  | 由 Azure Active Directory 用于访问控制和标识管理
management.azure.com |  用于资源部署和管理操作
*.services.visualstudio.com（可选） | 上传用于内部监视的设备日志。
aka.ms/*（可选） | 允许访问这些链接；用于下载和安装设备服务的最新更新。
download.microsoft.com/download | 允许从 Microsoft 下载中进行下载。
*.servicebus.windows.net | 用于 VMware 无代理迁移<br/><br/> 设备与 Azure Migrate 服务之间的通信。
*.hypervrecoverymanager.windowsazure.com | 用于 VMware 无代理迁移<br/><br/> 连接到 Azure Migrate 服务 URL。
\* .blob.core.windows.net |  用于 VMware 无代理迁移<br/><br/>将数据上传到存储以供迁移。 <br/>此 URL 是可选的，如果存储帐户（缓存存储帐户和网关存储帐户）附加了专用终结点，则不需要此 URL。

## <a name="collected-data---vmware"></a>收集的数据 - VMware

设备收集配置元数据、性能元数据和服务器依赖项数据（如果使用无代理[依赖项分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>元数据

Azure Migrate 设备发现的元数据有助于确定务器是否准备好迁移到 Azure、适当调整服务器大小、计划好成本以及分析应用程序的依赖性。 Microsoft 不会在任何许可证合规性审核中使用此数据。

下面是设备收集并发送到 Azure 的服务器元数据的完整列表。

**数据** | **计数器**
--- | ---
**服务器详细信息** |
服务器 ID | vm.Config.InstanceUuid
服务器名称 | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
服务器说明 | vm.Summary.Config.Annotation
许可证产品名称 | vm.Client.ServiceContent.About.LicenseProductName
操作系统类型 | vm.SummaryConfig.GuestFullName
启动类型 | vm.Config.Firmware
内核数 | vm.Config.Hardware.NumCPU
内存 (MB) | vm.Config.Hardware.MemoryMB
磁盘数目 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁盘大小列表 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
网络适配器列表 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
内存利用率 |mem.usage.average
**每磁盘详细信息** |
磁盘密钥值 | disk.Key
Dikunit 数量 | disk.UnitNumber
磁盘控制器密钥值 | disk.ControllerKey.Value
预配量 (GB) | virtualDisk.DeviceInfo.Summary
磁盘名称 | 使用 disk.UnitNumber, disk.Key 和 disk.ControllerKey.VAlue 生成此值
每秒读取操作数 | virtualDisk.numberReadAveraged.average
每秒写入操作数 | virtualDisk.numberWriteAveraged.average
读取吞吐量（MB/秒） | virtualDisk.read.average
写入吞吐量（MB/秒） | virtualDisk.write.average
**每 NIC 详细信息** |
网络适配器名称 | nic.Key
MAC 地址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 地址 | vm.Guest.Net
IPv6 地址 | vm.Guest.Net
读取吞吐量（MB/秒） | net.received.average
写入吞吐量（MB/秒） | net.transmitted.average
**库存路径详细信息** |
名称 | container.GetType().Name
子对象类型 | container.ChildType
引用详细信息 | container.MoRef
父级详细信息 | Container.Parent
每个服务器的文件夹详细信息 | ((Folder)container).ChildEntity.Type
每个服务器的数据中心详细信息 | ((Datacenter)container).VmFolder
每个主机文件夹的数据中心详细信息 | ((Datacenter)container).HostFolder
每个主机的群集详细信息 | ((ClusterComputeResource)container).Host
每个服务器的主机详细信息 | ((HostSystem)container).VM

### <a name="performance-data"></a>性能数据

下面是设备为在 VMware 上运行的服务器收集并发送到 Azure 的性能数据。

**数据** | **计数器** | **评估影响**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建议的服务器大小/成本
内存利用率 | mem.usage.average | 建议的服务器大小/成本
磁盘读取吞吐量（MB/秒） | virtualDisk.read.average | 计算磁盘大小、存储成本和服务器大小
磁盘写入吞吐量（MB/秒） | virtualDisk.write.average | 计算磁盘大小、存储成本和服务器大小
每秒的磁盘读取操作次数 | virtualDisk.numberReadAveraged.average | 计算磁盘大小、存储成本和服务器大小
每秒的磁盘写入操作次数 | virtualDisk.numberWriteAveraged.average  | 计算磁盘大小、存储成本和服务器大小
NIC 读取吞吐量（MB/秒） | net.received.average | 计算服务器大小
NIC 写入吞吐量（MB/秒） | net.transmitted.average  |计算服务器大小

### <a name="installed-software-inventory"></a>安装的软件清单

设备收集有关服务器上已安装软件清单的数据。

#### <a name="windows-server-software-inventory-data"></a>Windows Server 软件清单数据

以下是设备从 VMware 环境中发现的每个 Windows Server 收集的软件清单数据。

数据 | **注册表位置** | **键**
--- | --- | ---
应用程序名称  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
版本  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
提供程序  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | 发布服务器

#### <a name="windows-server-features-data"></a>Windows Server 功能数据

以下是设备从 VMware 环境中发现的每个 Windows Server 收集的功能数据。

数据  | **PowerShell Cmdlet** | **属性**
--- | --- | ---
名称  | Get-WindowsFeature  | 名称
特征类型 | Get-WindowsFeature  | FeatureType
Parent  | Get-WindowsFeature  | Parent

#### <a name="sql-server-metadata"></a>SQL Server 元数据

下面是设备从 VMware 环境中发现的每个 Windows Server 收集的 SQL Server 数据。

数据  | **注册表位置**  | **键**
--- | --- | ---
名称  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
版本  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | 版本
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | SP
版本  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | 版本

#### <a name="aspnet-web-apps-data"></a>ASP.NET Web 应用数据

以下是该设备从 VMware 环境中发现的每个 Windows Server 中收集的 Web 应用配置数据。

**实体** | 数据
--- | ---
Web 应用 | 应用程序名称 <br/>配置路径 <br/>前端绑定 <br/>已启用的框架 <br/>承载 Web 服务器<br/>Sub-Applications 和虚拟应用程序 <br/>应用程序池名称 <br/>运行时版本 <br/>托管管道模式
Web 服务器 | 服务器名称 <br/>服务器类型（当前仅限 IIS） <br/>配置位置 <br/>版本 <br/>FQDN <br/>用于发现的凭据 <br/>应用程序的列表

#### <a name="windows-server-operating-system-data"></a>Windows Server 操作系统数据

以下是设备从 VMware 环境中发现的每个 Windows Server 收集的操作系统数据。

**数据**  | **WMI 类**  | **WMI 类属性**
--- | --- | ---
名称  | Win32_operatingsystem  | 标题
版本  | Win32_operatingsystem  | 版本
体系结构  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Linux Server 软件清单数据

以下是设备从 VMware 环境中发现的每个 Linux Server 收集的软件清单数据。 根据服务器的操作系统，运行一个或多个命令。

数据  | **命令**
--- | ---
名称 | rpm，dpkg-query，snap
版本 | rpm，dpkg-query，snap
提供程序 | rpm，dpkg-query，snap

#### <a name="linux-server-operating-system-data"></a>Linux Server 操作系统数据

以下是设备从 VMware 环境中发现的每个 Linux Server 收集的操作系统数据。

数据  | **命令**
--- | ---
名称 <br/> 版本 | 从以下一个或多个文件收集：<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
体系结构 | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server 实例和数据库数据

设备收集 SQL Server 实例和数据库上的数据。

#### <a name="sql-database-metadata"></a>SQL 数据库元数据

**数据库元数据** | **视图/ SQL Server 属性**
--- | ---
数据库的唯一标识符 | sys.databases
服务器定义的数据库 ID | sys.databases
数据库的名称 | sys.databases
数据库的兼容级别 | sys.databases
数据库的排序规则名称 | sys.databases
数据库的状态 | sys.databases
数据库的大小（以 MB 为单位） | sys.master_files
包含数据文件的位置的驱动器号 | SERVERPROPERTY 和 Software\Microsoft\MSSQLServer\MSSQLServer
数据库文件列表 | sys.databases, sys.master_files
Service Broker 是否被启用 | sys.databases
是否对数据库启用变更数据捕获 | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server 元数据

**Server 元数据** | **视图/ SQL Server 属性**
--- | ---
服务器名称 |SERVERPROPERTY
FQDN | 从安装的应用程序的发现派生的连接字符串
安装 ID | sys.dm_server_registry
服务器版本 | SERVERPROPERTY
服务器版本 | SERVERPROPERTY
服务器主机平台 (Windows/Linux) | SERVERPROPERTY
服务器的产品级别 (RTM SP CTP) | SERVERPROPERTY
默认备份路径 | SERVERPROPERTY
数据文件的默认路径 | SERVERPROPERTY 和 Software\Microsoft\MSSQLServer\MSSQLServer
日志文件的默认路径 | SERVERPROPERTY 和 Software\Microsoft\MSSQLServer\MSSQLServer
否。 服务器上的内核数 | sys.dm_os_schedulers, sys.dm_os_sys_info
服务器排序规则名称 | SERVERPROPERTY
否。 具有可见联机状态的服务器上的内核数 | sys.dm_os_schedulers
唯一服务器 ID | sys.dm_server_registry
是否启用 HA | SERVERPROPERTY
是否启用缓冲池扩展 | sys.dm_os_buffer_pool_extension_configuration
是否配置故障转移群集 | SERVERPROPERTY
仅使用 Windows 身份验证模式的服务器 | SERVERPROPERTY
服务器安装 PolyBase | SERVERPROPERTY
否。 系统的逻辑 CPU 数 | sys.dm_server_registry, sys.dm_os_sys_info
一个物理处理器包公开的逻辑内核数或物理内核数的比率 | sys.dm_os_schedulers, sys.dm_os_sys_info
系统上的物理 CPU 数 | sys.dm_os_schedulers, sys.dm_os_sys_info
服务器上次启动的日期和时间 | sys.dm_server_registry
服务器最大内存使用量（以 MB 为单位） | sys.dm_os_process_memory
所有数据库中的 用户总数 | sys.databases, sys.logins
所有用户数据库的总大小 | sys.databases
临时数据库的大小 | sys.master_files, sys.configurations, sys.dm_os_sys_info
否。 登录名 | sys.logins
链接服务器列表 | sys.servers
代理作业列表 | [msdb].[dbo].[sysjobs], [sys].[syslogins], [msdb].[dbo].[syscategories]

### <a name="performance-metadata"></a>性能元数据

**“性能”** | **视图/ SQL Server 属性** | **评估影响**
--- | --- | ---
SQL Server CPU 使用率| sys.dm_os_ring_buffers| 推荐的 SKU 大小（CPU 维度）
SQL 逻辑 CPU 计数| sys.dm_os_sys_info| 推荐的 SKU 大小（CPU 维度）
正在使用的 SQL 物理内存| sys.dm_os_process_memory| 未使用
SQL 内存使用率百分比| sys.dm_os_process_memory | 未使用
数据库 CPU 使用率| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| 推荐的 SKU 大小（CPU 维度）
使用的数据库内存（缓冲池）| sys.dm_os_buffer_descriptors| 推荐的 SKU 大小（内存维度）
文件读取/写入 IO| sys.dm_io_virtual_file_stats, sys.master_files| 推荐的 SKU 大小（IO 维度）
读/写文件数| sys.dm_io_virtual_file_stats, sys.master_files| 推荐的 SKU 大小（吞吐量维度）
文件 IO 停止读取/写入 (ms)| sys.dm_io_virtual_file_stats, sys.master_files| 推荐的 SKU 大小（IO 延迟维度）
文件大小| sys.master_files| 推荐的 SKU 大小（存储维度）


### <a name="application-dependency-data"></a>应用程序依赖关系数据

无代理依赖项分析收集连接和进程数据。

#### <a name="windows-server-dependencies-data"></a>Windows Server 依赖关系数据

下面是设备从已为无代理依赖项分析启用的每个 Windows Server 收集的连接数据。

**数据** | **命令**
--- | ---
本地端口 | netstat
本地 IP 地址 | netstat
远程端口 | netstat
远程 IP 地址 | netstat
TCP 连接状态 | netstat
进程 ID | netstat
活动连接数 | netstat

下面是设备从已为无代理依赖项分析启用的每个 Windows Server 收集的连接数据。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
进程名称 | Win32_Process | ExecutablePath
进程参数 | Win32_Process | CommandLine
应用程序名称 | Win32_Process | ExecutablePath 属性的 VersionInfo.ProductName 参数

#### <a name="linux-server-dependencies-data"></a>Linux Server 依赖关系数据

下面是设备从已为无代理依赖项分析启用的每个 Linux Server 收集的连接数据。

数据 | **命令**
--- | ---
本地端口 | netstat
本地 IP 地址 | netstat
远程端口 | netstat
远程 IP 地址 | netstat
TCP 连接状态 | netstat
活动连接数 | netstat
进程 ID  | netstat
进程名称 | ps
进程参数 | ps
应用程序名称 | dpkg 或 rpm

## <a name="collected-data---hyper-v"></a>收集的数据 - Hyper-V

设备从 Hyper-v 环境中运行的服务器收集配置和性能元数据。

### <a name="metadata"></a>元数据
Azure Migrate 设备发现的元数据有助于确定务器是否准备好迁移到 Azure、适当调整服务器大小以及计划好成本。 Microsoft 不会在任何许可证合规性审核中使用此数据。

下面是设备收集并发送到 Azure 的服务器元数据的完整列表。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
**服务器详细信息** | 
BIOS 的序列号 | Msvm_BIOSElement | BIOSSerialNumber
服务器类型（第 1 代或第 2 代） | Msvm_VirtualSystemSettingData | VirtualSystemSubType
服务器显示名称 | Msvm_VirtualSystemSettingData | ElementName
服务器版本 | Msvm_ProcessorSettingData | VirtualQuantity
内存（字节） | Msvm_MemorySettingData | VirtualQuantity
服务器可使用的最大内存 | Msvm_MemorySettingData | 限制
已启用动态内存 | Msvm_MemorySettingData | DynamicMemoryEnabled
操作系统名称/版本/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 名称数据
服务器电源状态 | Msvm_ComputerSystem | EnabledState
**每磁盘详细信息** |
磁盘标识符 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
虚拟硬盘类型 | Msvm_VirtualHardDiskSettingData | 类型
虚拟硬盘大小 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
虚拟硬盘父级 | Msvm_VirtualHardDiskSettingData | ParentPath
**每 NIC 详细信息** |
IP 地址（综合 NIC） | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
已启用 DHCP（综合 NIC） | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID（综合 NIC） | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 地址（综合 NIC） | Msvm_SyntheticEthernetPortSettingData | 地址
NIC ID（旧 NIC） | MsvmEmulatedEthernetPortSetting 数据 | InstanceID
NIC MAC ID（旧 NIC） | MsvmEmulatedEthernetPortSetting 数据 | 地址

### <a name="performance-data"></a>性能数据

下面是设备收集并发送到 Azure 的服务器性能数据。

**性能计数器类** | **计数器** | **评估影响**
--- | --- | ---
Hyper-V 虚拟机监控程序虚拟处理器 | % 来宾运行时间 | 建议的服务器大小/成本
Hyper-V 动态内存服务器 | 当前压力 (%)<br/> 来宾可见物理内存 (MB) | 建议的服务器大小/成本
Hyper-V 虚拟存储设备 | 读取的字节数/秒 | 计算磁盘大小、存储成本和服务器大小
Hyper-V 虚拟存储设备 | 写入的字节数/秒 | 计算磁盘大小、存储成本和服务器大小
Hyper-V 虚拟网络适配器 | 接收的字节数/秒 | 计算服务器大小
Hyper-V 虚拟网络适配器 | 发送的字节数/秒 | 计算服务器大小

- CPU 使用率是所有附加到服务器的虚拟处理器的所有使用量之和。
- 内存使用率为（当前压力 * 来宾可见物理内存）/100。
- 从列出的 Hyper-V 性能计数器中可收集磁盘和网络利用率值。

## <a name="collected-data---physical"></a>收集的数据 - 物理服务器

设备从本地运行的物理或虚拟服务器收集配置和性能元数据。

### <a name="metadata"></a>元数据

Azure Migrate 设备发现的元数据有助于确定务器是否准备好迁移到 Azure、适当调整服务器大小以及计划好成本。 Microsoft 不会在任何许可证合规性审核中使用此数据。

### <a name="windows-server-metadata"></a>Windows Server 元数据

下面是设备收集并发送到 Azure 的 Windows 服务器元数据的完整列表。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain、Name、PartOfDomain
处理器核心计数 | Win32_PRocessor | NumberOfCores
分配的内存 | Win32_ComputerSystem | TotalPhysicalMemory
BIOS 序列号 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
启动类型 | Win32_DiskPartition | 检查 EFI/BIOS 类型 = GPT:System 的分区
OS 名称 | Win32_OperatingSystem | Caption
OS 版本 |Win32_OperatingSystem | 版本
OS 体系结构 | Win32_OperatingSystem | OSArchitecture
磁盘计数 | Win32_DiskDrive | Model、Size、DeviceID、MediaType、Name
磁盘大小 | Win32_DiskDrive | 大小
NIC 列表 | Win32_NetworkAdapterConfiguration | Description、Index
NIC IP 地址 | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC 地址 | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Linux Server 元数据

下面是设备收集并发送到 Azure 的 Linux 服务器元数据的完整列表。

数据 | **命令**
--- | ---
FQDN | cat /proc/sys/kernel/hostname, hostname -f
处理器核心计数 |  cat/proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
分配的内存 | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS 序列号 | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
启动类型 | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
OS 名称/版本 | 可以访问以下文件获取 OS 版本和名称：<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
OS 体系结构 | uname -m
磁盘计数 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
启动磁盘 | df /boot \| sed -n 2p \| awk '{print $1}'
磁盘大小 | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC 列表 | ip -o -4 addr show \| awk '{print $2}'
NIC IP 地址 | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
NIC MAC 地址 | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows 性能数据

下面是设备收集并发送到 Azure 的 Windows 服务器性能数据。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
CPU 使用率 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
内存使用率 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 计数 | Win32_PerfFormattedData_Tcpip_NetworkInterface | 获取网络设备计数。
每 NIC 接收的数据 | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
每 NIC 传输的数据 | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
磁盘计数 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | 磁盘计数
磁盘详细信息 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec、DiskWriteBytesPerSec、DiskReadsPerSec、DiskReadBytesPerSec。

### <a name="linux-performance-data"></a>Linux 性能数据

下面是设备收集并发送到 Azure 的 Linux 服务器性能数据。

| **数据** | **命令** |
| --- | --- |
| CPU 使用率 | cat /proc/stat/ \| grep 'cpu' /proc/stat |
| 内存使用率 | free \| grep Mem \| awk '{print $3/$2 * 100.0}' |
| NIC 计数 | lshw -class network \| grep eth[0-60] \| wc -l |
| 每 NIC 接收的数据 | cat /sys/class/net/eth$nic/statistics/rx_bytes |
| 每 NIC 传输的数据 | cat /sys/class/net/eth$nic/statistics/tx_bytes |
| 磁盘计数 | fdisk -l \| egrep 'Disk.\*bytes' \| awk '{print $2}' \| cut -f1 -d ':' |
| 磁盘详细信息 | cat /proc/diskstats |

## <a name="appliance-upgrades"></a>设备升级

当设备上运行的 Azure Migrate 服务更新时，设备也会随之升级。 升级会自动执行，因为设备上默认启用了自动更新。 可以更改此默认设置，变为手动更新设备服务。

### <a name="turn-off-auto-update"></a>禁用自动更新

1. 在运行设备的服务器上，打开注册表编辑器。
2. 导航到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance。
3. 若要禁用自动更新，请创建一个注册表项 AutoUpdate，将 DWORD 值设为 0。

    ![设置注册表项](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>启用自动更新

可以使用以下任一方法启用自动更新：

- 从 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance 删除 AutoUpdate 注册表项。
- 在“设置先决条件”面板中，从最新的更新检查中单击“查看设备服务”，以打开自动更新 。

删除注册表项的步骤：

1. 在运行设备的服务器上，打开注册表编辑器。
2. 导航到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance。
3. 删除先前创建的用于禁用自动更新的注册表项 AutoUpdate。

若要从设备配置管理器中启用，在发现完成后，执行以下操作：

1. 在设备配置管理器上，请转到“设置先决条件”面板
2. 在最新更新检查中，单击“查看设备服务”，然后单击链接以打开自动更新。

    ![启用自动更新](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>查看设备服务版本

可以使用以下方法之一查看设备服务版本：

- 在设备配置管理器中，请转到“设置先决条件”面板。
- 在设备的“控制面板” > “程序和功能”中查看 。

若要在设备配置管理器中查看，请执行以下操作：

1. 在设备配置管理器上，请转到“设置先决条件”面板
2. 在最新更新检查中，单击“查看设备服务”。

    ![检查版本](./media/migrate-appliance/versions.png)

若要在控制面板中查看，请执行以下操作：

1. 在设备上，单击“启动” > “控制面板” > “程序和功能”
2. 查看列表中的设备服务版本。

    ![在控制面板中查看版本](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>手动更新旧版本

如果你运行的是任何服务的旧版本，则必须卸载该服务，并手动更新到最新版本。

1. 若要查看最新的设备服务版本，请[下载](https://aka.ms/latestapplianceservices) LatestComponents.json 文件。

2. 下载后，在记事本中打开 LatestComponents.json 文件。

3. 在文件中找到最新服务版本，并找到相应的下载链接。 例如：

   `"Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"`

4. 使用文件中的下载链接下载已过时服务的最新版本。

5. 下载后，在管理员命令窗口中运行以下命令，验证已下载的 MSI 的完整性。

   `C:\> Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]`

    例如：

    `C:\> CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5`

5. 检查命令输出是否与文件中服务的哈希值条目匹配（例如，上述 MD5 哈希值）。

6. 现在，运行 MSI 以安装服务。 此安装是无提示安装，完成后安装窗口将关闭。

7. 安装完成后，可以在“控制面板” > “程序和功能”中查看服务的版本。 现在，服务版本应已升级到 JSON 文件中显示的最新版本。

## <a name="next-steps"></a>后续步骤

- [了解如何](how-to-set-up-appliance-vmware.md)为 VMware 设置设备。
- [了解如何](how-to-set-up-appliance-hyper-v.md)为 Hyper-V 设置设备。
- [了解如何](how-to-set-up-appliance-physical.md)为物理服务器设置设备。
