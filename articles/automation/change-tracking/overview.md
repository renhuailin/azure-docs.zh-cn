---
title: Azure 自动化 - 更改跟踪和库存概述
description: 本文介绍了更改跟踪和清单功能，可帮助你在环境中识别软件和 Microsoft 服务的更改。
services: automation
ms.subservice: change-inventory-management
ms.date: 11/10/2020
ms.topic: conceptual
ms.openlocfilehash: b5390e4b3dc6d77390c3fca6323cbd52544c638a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445415"
---
# <a name="change-tracking-and-inventory-overview"></a>更改跟踪和库存概述

本文介绍了 Azure 自动化中的“更改跟踪和库存”功能。 此功能跟踪 Azure、本地和其他云环境中托管的虚拟机中的更改，以帮助你找出分发包管理器管理的软件的操作和环境问题。 更改跟踪和库存功能跟踪的项包括：

- Windows 软件
- Linux 软件（包）
- Windows 和 Linux 文件
- Windows 注册表项
- Microsoft 服务
- Linux 守护程序

> [!NOTE]
> 若要跟踪 Azure 资源管理器的属性更改，请参阅 Azure Resource Graph [更改历史记录](../../governance/resource-graph/how-to/get-resource-changes.md)。

更改跟踪和清单使用 [Azure 安全中心文件完整性监视 (FIM) ](../../security-center/security-center-file-integrity-monitoring.md) 来检查操作系统和应用程序文件以及 Windows 注册表。 虽然 FIM 会监视这些实体，更改跟踪和清单本机跟踪：

- 软件更改
- Microsoft 服务
- Linux 守护程序

启用更改跟踪和清单中包含的所有功能可能会产生额外费用。 在继续之前，请查看 [自动化定价](https://azure.microsoft.com/pricing/details/automation/) 并 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。 

更改跟踪和清单将数据转发到 Azure Monitor 日志，并且收集的数据存储在 Log Analytics 工作区中。 仅当启用了 **适用于服务器的 Azure Defender** 时，才能 (FIM) 功能进行文件完整性监视。 有关详细信息，请参阅 Azure 安全中心 [定价](../../security-center/security-center-pricing.md) 。 FIM 将数据上载到与所创建的工作区相同的 Log Analytics 工作区，该工作区用于存储更改跟踪和清单中的数据。 建议你监视链接的 Log Analytics 工作区，以跟踪你的实际使用情况。 有关分析 Azure Monitor 日志数据使用情况的详细信息，请参阅[管理使用情况和成本](../../azure-monitor/platform/manage-cost-storage.md)。

连接到 Log Analytics 工作区的计算机使用 [Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md) 收集有关受监视服务器上已安装的软件、Microsoft 服务、Windows 注册表和文件和 Linux 守护程序的更改的数据。 数据可用时，代理会将其发送到 Azure Monitor 日志进行处理。 Azure Monitor 日志会将逻辑应用到接收的数据，记录该数据，并使其可供分析。

> [!NOTE]
> 更改跟踪和清单要求将 Log Analytics 工作区链接到自动化帐户。 有关受支持区域的明确列表，请参阅 [Azure 工作区映射](../how-to/region-mappings.md)。 区域映射不会影响在单独的区域中管理自动化帐户内 VM 的功能。

## <a name="current-limitations"></a>当前限制

更改跟踪和库存不支持或具有以下限制：

- Windows 注册表跟踪递归
- 网络文件系统
- 不同的安装方法
- *Windows 上存储的 *_.exe_* 文件
- 未在当前实现中使用“最大文件大小”列和值。
- 如果尝试在30分钟的收集周期内收集超过2500个文件，更改跟踪和清单性能可能会下降。
- 如果网络流量较高，则可能需要长达6小时的时间才能显示更改记录。
- 如果在计算机或服务器关闭的情况下修改配置，则可能会发布属于以前配置的更改。
- 正在收集 Windows Server 2016 Core RS3 计算机上的修补程序更新。
- 即使未发生任何更改，Linux 守护程序也可能会显示已更改的状态。 出现此问题的原因在于 `SvcRunLevels` Azure Monitor [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) 表中数据的写入方式。

## <a name="supported-operating-systems"></a>支持的操作系统

满足 Log Analytics 代理要求的所有操作系统都支持更改跟踪和库存。 有关 Log Analytics 代理当前支持的 Windows 和 Linux 操作系统版本的列表，请参阅 [支持的操作系统](../../azure-monitor/platform/agents-overview.md#supported-operating-systems) 。

若要了解 TLS 1.2 的客户端要求，请参阅[强制 Azure 自动化执行 TLS 1.2](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

### <a name="python-requirement"></a>Python 要求

更改跟踪和清单仅支持 Python2。 如果你的计算机使用的是默认情况下不包括 Python 2 的发行版，则必须安装它。 以下示例命令将在不同的发行版上安装 Python 2。

- Red Hat、CentOS、Oracle：`yum install -y python2`
- Ubuntu、Debian：`apt-get install -y python2`
- SUSE: `zypper install -y python2`

Python2 可执行文件必须化名为 *python* 。

## <a name="network-requirements"></a>网络要求

以下地址是专用于更改跟踪和清单的地址。 与以下地址的通信通过端口 443 进行。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\* .blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|\* .azure-automation.net | *.azure-automation.us|

创建网络组安全规则或配置 Azure 防火墙以允许流量流向自动化服务和 Log Analytics 工作区时，请使用 [服务标记](../../virtual-network/service-tags-overview.md#available-service-tags) GuestAndHybridManagement 和 AzureMonitor 。 这样可简化网络安全规则的日常管理。 若要安全且私下地从 Azure Vm 连接到自动化服务，请参阅 [使用 Azure 专用链接](../how-to/private-link-security.md)。 若要获取当前服务标记和范围信息，并将其包含为本地防火墙配置的一部分，请参阅[可下载的 JSON 文件](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

可以通过以下方式启用更改跟踪和清单：

- 从你的 [自动化帐户](enable-from-automation-account.md) ，为一个或多个 azure 和非 azure 计算机。

- 为非 Azure 计算机手动执行，包括注册到 [启用了 Azure Arc 的服务器](../../azure-arc/servers/overview.md)的计算机或服务器。 对于混合计算机，我们建议通过首先将计算机连接到 [启用了 Azure arc 的服务器](../../azure-arc/servers/overview.md)，然后使用 Azure 策略将 [部署 Log Analytics 代理分配到 *Linux* 或 *Windows* Azure arc 计算机](../../governance/policy/samples/built-in-policies.md#monitoring) 内置策略，来安装适用于 Windows 的 Log Analytics 代理。 如果还计划使用用于 VM 的 Azure Monitor 监视计算机，请改用 [启用用于 VM 的 Azure Monitor](../../governance/policy/samples/built-in-initiatives.md#monitoring) 计划。

- 对于 "Azure 门户中的" [虚拟机 "页](enable-from-vm.md) 上的单个 Azure VM。 此方案适用于 Linux 和 Windows VM。

- 对于[多个 Azure VM](enable-from-portal.md)，从 Azure 门户的“虚拟机”页中选择它们进行启用。

## <a name="tracking-file-changes"></a>跟踪文件更改

若要跟踪 Windows 和 Linux 上文件中的更改，更改跟踪和库存功能会使用文件的 MD5 哈希。 此功能使用哈希算法来检测自上次清点后是否进行了更改。

## <a name="tracking-file-content-changes"></a>跟踪文件内容更改

更改跟踪和清单允许您查看 Windows 或 Linux 文件的内容。 对于文件的每个更改，更改跟踪和库存功能将文件内容存储在 [Azure 存储帐户](../../storage/common/storage-account-create.md)中。 跟踪文件时，可以在更改前后查看其内容。 文件内容可以是内联的，也可以并排查看。

![查看文件中的更改](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>跟踪注册表项

更改跟踪和清单允许监视 Windows 注册表项的更改。 此监视操作可以确定第三方代码和恶意软件可以激活的扩展点。 下表列出了预配置（但未启用）的注册表项。 若要跟踪这些注册表项，必须将它们都启用。

> [!div class="mx-tdBreakAll"]
> |注册表项 | 目的 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 监视启动时运行的脚本。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 监视关机时运行的脚本。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 监视用户登录 Windows 帐户之前加载的注册表项。 该注册表项用于在 64 位计算机上运行的 32 位应用程序。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 监视应用程序设置的更改。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 监视直接挂钩到 Windows 资源管理器中的上下文菜单处理程序，并且通常与 **explorer.exe** 在进程内运行。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | 监视直接挂钩到 Windows 资源管理器并在 **explorer.exe** 中运行的复制挂钩处理程序。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 监视图标覆盖处理程序注册。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 监视在 64 位计算机上运行的 32 位应用程序的图标覆盖处理程序注册。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | 监视 Internet Explorer 的新浏览器帮助程序对象插件。 用于访问当前页的文档对象模型 (DOM) 并控制导航。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | 监视 Internet Explorer 的新浏览器帮助程序对象插件。 对于在 64 位计算机上运行的 32 位应用程序，用于访问当前页的文档对象模型 (DOM) 并控制导航。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 对于在 64 位计算机上运行的 32 位应用程序，监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | 监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 system.ini 文件中的 [drivers] 部分。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 对于在 64 位计算机上运行的 32 位应用程序，监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 system.ini 文件中的 [drivers] 部分。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 监视已知或常用的系统 DLL 列表。 监视可通过删除系统 Dll 的特洛伊木马版本来防止人们利用弱应用程序目录权限。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | 监视可从 winlogon.exe（适用于 Windows 的交互式登录支持模型）接收事件通知的包列表。

## <a name="recursion-support"></a>递归支持

更改跟踪和库存功能支持递归，这样你就可以指定通配符来简化跨目录的跟踪。 递归还提供了环境变量，使你能够跨具有多个或动态驱动器名称的环境跟踪文件。 下面的列表包含在配置递归时应了解的常见信息：

- 跟踪多个文件需要使用通配符。

- 只能在文件路径的最后一段中使用通配符，例如， **c:\folder \\ file** _ 或 _ */etc/* * *。

- 如果环境变量具有无效路径，验证会成功，但在执行过程中路径会失败。

- 设置路径时应避免常规路径名，因为此类型的设置可能会导致遍历太多文件夹。

## <a name="change-tracking-and-inventory-data-collection"></a>更改跟踪和库存数据收集

下表显示更改跟踪和库存功能支持的更改类型的数据收集频率。 对于每种类型，当前状态的数据快照也至少每 24 小时刷新一次。

| **更改类型** | **频率** |
| --- | --- |
| Windows 注册表 | 50 分钟 |
| Windows 文件 | 30 分钟 |
| Linux 文件 | 15 分钟 |
| Microsoft 服务 | 10 秒到 30 分钟</br> 默认值：30 分钟 |
| Linux 守护程序 | 5 分钟 |
| Windows 软件 | 30 分钟 |
| Linux 软件 | 5 分钟 |

下表显示了用于更改跟踪和库存功能的每台计算机的跟踪项限制。

| **资源** | **限制** |
|---|---|---|
|文件|500|
|注册表|250|
|Windows 软件（不包括修补程序） |250|
|Linux 包|1250|
|服务|250|
|守护程序|250|

使用更改跟踪和库存的计算机的平均 Log Analytics 数据用量为每月大约 40 MB，具体取决于环境。 使用 "Log Analytics" 工作区的 "使用情况和估计成本" 功能，可以在使用情况图表中查看更改跟踪和库存的数据引入。 使用此数据视图可以评估你的数据使用情况，并确定它如何影响你的帐单。 请参阅[了解自己的使用情况和预估成本](../../azure-monitor/platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs)

### <a name="microsoft-service-data"></a>Microsoft 服务数据

Windows 服务的默认收集频率为 30 分钟。 可以使用“编辑设置”下“Microsoft 服务”选项卡上的滑块配置频率。

![Microsoft 服务滑块](./media/overview/windowservices.png)

为了优化性能，Log Analytics 代理只跟踪更改。 如果服务恢复到其原始状态，则设置较高的阈值可能会丢失更改。 将频率设置为较小的值可以捕获可能会错过的更改。

> [!NOTE]
> 虽然代理可以按 10 秒的间隔跟踪更改，但数据仍要在几分钟后才能显示在 Azure 门户中。 代理仍会跟踪和记录在门户中显示期间发生的更改。

## <a name="support-for-alerts-on-configuration-state"></a>支持有关配置状态的警报

更改跟踪和库存的主要功能是就混合环境的配置状态更改发出警报。 许多有用的操作可触发，以响应警报。 例如，对 Azure 函数、自动化 runbook、webhook 等执行的操作。 对计算机的 **c:\windows\system32\drivers\etc\hosts** 文件进行更改时发出警报是更改跟踪和清单数据的一个良好的警报。 还有更多的警报方案，包括下表中定义的查询方案。

|查询  |说明  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|用于跟踪对系统关键文件的更改。|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|用于跟踪对关键配置文件的修改。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|用于跟踪对系统关键服务的更改。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|用于跟踪对系统关键服务的更改。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|用于需锁定软件配置的环境。|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|用于查看哪些计算机安装了过时的或不符合标准的软件版本。 该查询会报告最新报告的配置状态，而不报告更改。|
|ConfigurationChange <br>&#124; where RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 用于跟踪对重要的防病毒密钥的更改。|
|ConfigurationChange <br>&#124; where RegistryKey contains @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| 用于跟踪对防火墙设置的更改。|

## <a name="next-steps"></a>后续步骤

- 若要从自动化帐户启用，请参阅 [从自动化帐户启用更改跟踪和清单](enable-from-automation-account.md)。

- 若要从 Azure 门户中启用，请参阅 [从 Azure 门户启用更改跟踪和清单](enable-from-portal.md)。

- 若要从 runbook 启用，请参阅 [从 Runbook 启用更改跟踪和清单](enable-from-runbook.md)。

- 若要从 Azure VM 启用，请参阅 [从 AZURE Vm 启用更改跟踪和清单](enable-from-vm.md)。
