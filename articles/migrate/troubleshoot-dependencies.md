---
title: 排查无代理和基于代理的依赖项分析中的问题
description: 获取关于 Azure Migrate 中依赖项可视化的帮助。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 671820e4c3ca3a4b8069f6be6d2a884a97da0739
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535628"
---
# <a name="troubleshoot-dependency-visualization"></a>排查依赖项可视化问题

本文可帮助你排查基于代理和无代理依赖项分析中的问题（仅适用于 VMware 服务器）。 [详细了解](concepts-dependency-visualization.md) Azure Migrate 中支持的依赖项可视化类型。

## <a name="visualize-dependencies-for-1-hour-with-agentless-dependency-analysis"></a>使用无代理依赖项分析以可视化方式呈现依赖项超过 1 小时

使用无代理依赖项分析时，可以可视化依赖项或将其导出到地图中，持续时间最长为 30 天。

## <a name="visualize-dependencies-for-10-servers-with-agentless-dependency-analysis"></a>使用无代理依赖项分析以可视化方式呈现超过 10 台服务器的依赖项

Azure Migrate 提供了一个 Power BI 模板，可以使用该模块一次可视化多个服务器的网络连接，并按进程和服务器进行筛选。 [详细了解](how-to-create-group-machine-dependencies-agentless.md#visualize-network-connections-in-power-bi)如何一起以可视化方式呈现多个服务器的依赖项。

## <a name="dependencies-export-csv-shows-unknown-process-with-agentless-dependency-analysis"></a>依赖项导出 CSV 显示使用无代理依赖项分析的“未知进程”
在无代理依赖项分析中，会尽力地捕获进程名称。 在某些情况下，尽管可以捕获源和目标服务器的名称以及目标端口，但无法在依赖项的两端确定进程名称。 在这种情况下，系统会将该进程标记为“未知进程”。

## <a name="unable-to-export-dependency-data-in-a-csv-due-to-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>由于错误“403：此请求无权执行此操作”，无法在 CSV 中导出依赖项数据
如果 Azure Migrate 项目具有专用终结点连接，则导出依赖项数据的请求应从通过专用网络连接到 Azure 虚拟网络的客户端启动。 若要解决此错误，请在本地网络或设备服务器上打开 Azure 门户，然后再次尝试导出。

## <a name="common-agentless-dependency-analysis-errors"></a>常见的无代理依赖项分析错误

Azure Migrate 支持使用“Azure Migrate：发现和评估”功能来执行无代理依赖项分析。 目前仅 VMware 支持无代理依赖项分析。 [详细了解](how-to-create-group-machine-dependencies-agentless.md)无代理依赖项分析的要求。

下表汇总了无代理依赖项分析错误列表。

> [!Note]
> 软件清单采用与无代理依赖项分析相同的方法收集所需数据，因而也可能会遇到相同的错误。

| **错误** | **原因** | **操作** |
|--|--|--|
| 9000：无法检测到服务器上 VMware 工具的状态。 | 服务器上可能未安装 VMware 工具，或者安装的版本已损坏。 | 请确保在服务器上安装并运行版本高于 10.2.1 的 VMware 工具。 |
| 9001：服务器上未安装 VMware 工具。 | 服务器上可能未安装 VMware 工具，或者安装的版本已损坏。 | 请确保在服务器上安装并运行版本高于 10.2.1 的 VMware 工具。 |
| 9002：服务器上未运行 VMware 工具。 | 服务器上可能未安装 VMware 工具，或者安装的版本已损坏。 | 请确保在服务器上安装并运行版本高于 10.2.0 的 VMware 工具。 |
| 9003：不支持服务器上运行的操作系统类型。 | 服务器上运行的操作系统不是 Windows 或 Linux。 | 目前仅支持 Windows 和 Linux 操作系统类型。 如果服务器确实运行的是 Windows 或 Linux 操作系统，请检查 vCenter Server 中指定的操作系统类型。 |
| 9004：服务器未处于运行状态。 | 服务器处于关机状态。 | 请确保服务器处于正在运行状态。 |
| 9005：不支持服务器上运行的操作系统类型。 | 服务器上运行的操作系统不是 Windows 或 Linux。 | 目前仅支持 Windows 和 Linux 操作系统类型。 目前不支持 \<FetchedParameter> 操作系统。 |
| 9006：从服务器下载发现元数据文件所需的 URL 为空。 | 这可能是暂时性问题，因为设备上的发现代理未如期运行。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9007：服务器中找不到为收集元数据而运行相关脚本的进程。 | 这可能是暂时性问题，因为设备上的发现代理未如期运行。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9008：无法检索到为收集元数据而在服务器上运行的相关进程的状态。 | 此问题可能是暂时性的，因为发生了内部错误。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9009：Windows 用户帐户控制 (UAC) 阻止程序在服务器上执行发现操作。 | Windows UAC 设置限制程序从服务器上发现已安装的应用程序。 | 在受影响的服务器上，降低控制面板中“用户帐户控制”设置的级别。 |
| 9010：服务器已关机。 | 服务器处于关机状态。 | 请确保服务器处于开机状态。 |
| 9011：无法在服务器上找到包含所发现元数据的文件。 | 此问题可能是暂时性的，因为发生了内部错误。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9012：服务器上包含所发现元数据的文件为空。 | 此问题可能是暂时性的，因为发生了内部错误。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9013：每次登录服务器时都会新建一个临时用户配置文件。 | 每次登录服务器时都会新建一个临时用户配置文件。 | 请提交 Microsoft 支持案例，让我们帮助你解决此问题。 |
| 9014：因为 ESXi 主机发生错误，无法检索包含所发现元数据的文件。 错误代码：%ErrorCode；详细信息：%ErrorMessage | 在 ESXi 主机 \<HostName> 上遇到错误。 错误代码：%ErrorCode；详细信息：%ErrorMessage。 | 请确保运行服务器的 ESXi 主机上的端口 443 为打开状态。<br/><br/> [详细了解](troubleshoot-dependencies.md#error-9014-httpgetrequesttoretrievefilefailed)如何修正此问题。|
| 9015：为服务器发现提供的 vCenter Server 用户帐户未启用来宾操作权限。 | 尚未在 vCenter Server 用户帐户上启用来宾操作所需的权限。 | 请确保 vCenter Server 用户帐户启用了对“虚拟机” > “来宾操作”的权限，以便与服务器进行交互并拉取所需的数据。  <br/><br/> [详细了解](tutorial-discover-vmware.md#prepare-vmware)如何设置具有所需权限的 vCenter Server 帐户。 |
| 9016：无法发现元数据，因为服务器上的来宾操作代理已过时。 | 服务器上未安装 VMware 工具，或者安装的不是最新版本。 | 请确保在服务器上安装并运行最新版 VMware 工具。 VMware 工具版本必须为 10.2.1 或更高版本。 |
| 9017：无法在服务器上找到包含所发现元数据的文件。 | 这可能是暂时性问题，因为发生了内部错误。 | 请提交 Microsoft 支持案例，让我们帮助你解决此问题。 |
| 9018：服务器上未安装 PowerShell。 | 在服务器上找不到 PowerShell。 | 请确保服务器上安装了 PowerShell 2.0 或更高版本。 <br/><br/> [详细了解](troubleshoot-dependencies.md#error-9018-powershellnotfound)如何修正此问题。|
| 9019：无法发现元数据，因为服务器上的来宾操作失败。 | 无法在服务器上执行 VMware 来宾操作。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 请确保设备上的服务器凭据有效，并且凭据中的用户名采用了用户主体名称 (UPN) 格式。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） |
| 9020：无法在服务器上创建需要包含所发现元数据的文件。 | 与设备或本地组策略中提供的凭据关联的角色限制在所需文件夹中创建文件。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 检查设备上提供的凭据是否对服务器中的文件夹 \<folder path/folder name> 具有创建文件的权限。 <br/>2. 如果设备上提供的凭据没有所需权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） |
| 9021：无法在服务器的正确路径下创建需要包含所发现元数据的文件。 | VMware 工具报告创建文件时的文件路径不正确。 | 请确保在服务器上安装并运行版本高于 10.2.0 的 VMware 工具。 |
| 9022：拒绝访问，无法在服务器上运行 Get-WmiObject cmdlet。 | 与设备或本地组策略中所提供凭据关联的角色限制对 WMI 对象的访问。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 检查设备上提供的凭据是否具有创建文件的管理员权限并启用了 WMI。 <br/>2. 如果设备上提供的凭据没有所需权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。）<br/><br/> [详细了解](troubleshoot-dependencies.md#error-9022-getwmiobjectaccessdenied)如何修正此问题。|
| 9023：无法运行 PowerShell，因为 %SystemRoot% 环境变量值为空。 | 对于服务器，%SystemRoot% 环境变量的值是空的。 | 1. 在受影响的服务器上运行 echo %systemroot% 命令，以检查环境变量是否返回空值。 <br/>2. 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9024：无法执行发现，因为 %TEMP% 环境变量值为空。 | 对于服务器，%TEMP% 环境变量的值是空的。 | 1. 在受影响的服务器上运行 echo %temp% 命令，以检查环境变量是否返回空值。 <br/>2. 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9025：无法执行发现，因为服务器上的 PowerShell 已损坏。 | 服务器上的 PowerShell 已损坏。 | 请重新安装 PowerShell，并验证它是否在受影响的服务器上运行。 |
| 9026：无法在服务器上运行来宾操作。 | 服务器的当前状态不允许运行来宾操作。 | 1. 确保受影响的服务器已启动并正在运行。<br/>2. 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9027：无法发现元数据，因为未在服务器上运行来宾操作代理。 | 无法联系服务器上的来宾操作代理。 | 请确保在服务器上安装并运行版本高于 10.2.0 的 VMware 工具。 |
| 9028：由于服务器上的存储空间不足，无法创建需要包含所发现元数据的文件。 | 服务器磁盘上的存储空间不足。 | 请确保受影响服务器的磁盘存储有足够的可用空间。 |
| 9029：设备上提供的凭据没有运行 PowerShell 所需的访问权限。 | 设备上的凭据没有运行 PowerShell 所需的访问权限。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 确保设备上的凭据可以访问服务器上的 PowerShell。<br/>2. 如果设备上的凭据没有所需的访问权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） |
| 9030：无法收集所发现的元数据，因为托管服务器的 ESXi 主机已断开连接。 | 服务器所驻留的 ESXi 主机已断开连接。 | 请确保运行服务器的 ESXi 主机处于已连接状态。 |
| 9031：无法收集所发现的元数据，因为托管服务器的 ESXi 主机未响应。 | 服务器所驻留的 ESXi 主机处于无效状态。 | 请确保运行服务器的 ESXi 主机正在运行且处于已连接状态。 |
| 9032：无法执行发现，因为发生了内部错误。 | 如果发生内部错误，则会出现此问题。 | 请按照[此站点](troubleshoot-dependencies.md#error-9032-invalidrequest)中的步骤来修正此问题。 如果问题仍然存在，请打开 Microsoft 支持案例。 |
| 9033：无法执行发现，因为设备上提供的服务器凭据的用户名中包含无效字符。 | 设备上的凭据在用户名中包含无效字符。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 请确保设备上的凭据在用户名中没有任何无效字符。 你可以返回到设备配置管理器来编辑凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） |
| 9034：无法执行发现，因为设备上提供的服务器凭据的用户名不是 UPN 格式。 | 设备上的凭据所用的用户名不是 UPN 格式。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 请确保设备上的凭据采用了 UPN 格式的用户名。 你可以返回到设备配置管理器来编辑凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） |
| 9035：无法执行发现，因为未正确设置 PowerShell 语言模式。 | PowerShell 语言模式未设置为“全语言”。 | 请确保 PowerShell 语言模式设置为“全语言”。 |
| 9036：无法执行发现，因为设备上提供的服务器凭据的用户名不是 UPN 格式。 | 设备上的凭据所用的用户名不是 UPN 格式。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 请确保设备上的凭据采用了 UPN 格式的用户名。 你可以返回到设备配置管理器来编辑凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） |
| 9037：由于服务器的响应时间较长，已暂停收集元数据。 | 服务器的响应时间过长。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 10000：不支持服务器上运行的操作系统类型。 | 服务器上运行的操作系统不是 Windows 或 Linux。 | 目前仅支持 Windows 和 Linux 操作系统类型。 目前不支持 \<GuestOSName> 操作系统。 |
| 10001：在服务器上找不到收集发现元数据所需的脚本。 | 执行发现所需的脚本可能已从预期位置中删除或移除。 | 请提交 Microsoft 支持案例，让我们帮助你解决此问题。 |
| 10002：服务器上的发现操作已超时。 | 这可能是暂时性问题，因为设备上的发现代理未如期运行。 | 此问题应在下一个周期的 24 小时内自动解决。 如果未解决，请按照[此站点](troubleshoot-dependencies.md#error-10002-scriptexecutiontimedoutonvm)中的步骤来修正此问题。 如果问题仍然存在，请打开 Microsoft 支持案例。|
| 10003：执行发现操作的进程因错误而退出。 | 由于出现错误，执行发现操作的进程突然退出。| 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 10004：设备上没有为服务器操作系统类型提供凭据。 | 设备上没有为服务器操作系统类型添加凭据。 | 1. 确保为设备中受影响服务器的操作系统类型添加凭据。<br/> 2. 现在可以在设备中添加多个服务器凭据。 |
| 10005：设备上提供的服务器凭据无效。 | 设备上提供的凭据无效。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 确保设备上提供的凭据有效，而且可使用这些凭据访问服务器。<br/> 2. 现在可以在设备中添加多个服务器凭据。<br/> 3. 返回到设备配置管理器，以提供另一组凭据或编辑现有凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） <br/><br/> [详细了解](troubleshoot-dependencies.md#error-10005-guestcredentialnotvalid)如何修正此问题。|
| 10006：不支持服务器上运行的操作系统类型。 | 服务器上运行的操作系统不是 Windows 或 Linux。 | 目前仅支持 Windows 和 Linux 操作系统类型。 目前不支持 \<GuestOSName> 操作系统。 |
| 10007：无法从服务器处理所发现的元数据。 | 解析包含所发现元数据的文件内容时出错。 | 请提交 Microsoft 支持案例，让我们帮助你解决此问题。 |
| 10008：无法在服务器上创建需要包含所发现元数据的文件。 | 与设备或本地组策略中提供的凭据关联的角色限制在所需文件夹中创建文件。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 检查设备上提供的凭据是否对服务器中的文件夹 \<folder path/folder name> 具有创建文件的权限。<br/>2. 如果设备上提供的凭据没有所需权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） |
| 10009：无法将所发现元数据写入服务器上的文件。 | 与设备或本地组策略上提供的凭据关联的角色限制在服务器上的文件中写入。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 检查设备上提供的凭据是否对服务器中的文件夹 \<folder path/folder name> 具有写入文件的权限。<br/>2. 如果设备上提供的凭据没有所需权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记凭据名称。） |
| 10010：无法执行发现，因为服务器上缺少收集某些元数据所需的 %CommandName 命令。 | 服务器上未安装包含 %CommandName; 命令的包。 | 确保服务器上已安装包含命令 %CommandName; 的包。 |
| 10011：使用设备上提供的凭据来登录和注销交互式会话。 | 交互式登录和注销会强制在正在使用的帐户的配置文件中卸载注册表项。 此条件导致密钥不可供日后使用。 | 请使用[此站点](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error)中介绍的解决方法。 |
| 10012：尚未在设备中提供服务器凭据。 | 未为服务器提供凭据，或者在设备中提供的域凭据使用了不正确域名。 [详细了解](troubleshoot-dependencies.md#error-10012-credentialnotprovided)引发此错误的原因。 | 1. 确保设备上提供了服务器凭据，而且可使用这些凭据访问服务器。 <br/>2. 现在可以在设备中为服务器添加多个凭据。 返回设备配置管理器，为服务器提供凭据。|


## <a name="error-970-dependencymapinsufficientprivilegesexception"></a>错误 970：DependencyMapInsufficientPrivilegesException

### <a name="cause"></a>原因
如果未在设备上提供具有所需权限的凭据，则 Linux 服务器通常会出现此错误。

### <a name="remediation"></a>补救
可以使用两个选项：

- 确保你已提供根用户帐户。
- 确保帐户对 /bin/netstat 和 /bin/ls 文件具有以下权限：
   - CAP_DAC_READ_SEARCH
   - CAP_SYS_PTRACE

若要检查设备上提供的用户帐户是否具有所需权限：

1. 使用错误消息中所述的相同用户帐户登录到遇到此错误的服务器。
1. 在 Azure Shell 中运行以下命令。 如果没有无代理依赖项分析所需权限，则会出现错误。

   ````
   ps -o pid,cmd | grep -v ]$
   netstat -atnp | awk '{print $4,$5,$7}'
   ````
1. 通过运行以下命令，设置对 /bin/netstat 和 /bin/ls 文件的必要权限：

   ````
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
   ````
1. 你可以验证上述命令是否向用户帐户分配了所需权限。

   ````
   getcap /usr/bin/ls
   getcap /usr/bin/netstat
   ````
1. 重新运行步骤 2 中提供的命令，以获取成功的输出。


## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>错误 9014：HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>原因
当设备中的 VMware 发现代理尝试通过托管服务器的 ESXi 主机从服务器文件系统下载包含依赖项数据的输出文件时，会发生此问题。

### <a name="remediation"></a>补救
- 可以在设备的 443 端口（需要在 ESXi 主机上打开以拉取依赖项数据）上测试 ESXi 主机（错误消息中提供的名称）的 TCP 连接。 在设备服务器中打开 PowerShell 并运行以下命令：
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````

- 如果命令返回成功连接，请转到“Azure Migrate 项目” > “发现和评估” > “概述” > “管理” > “设备”，选择设备名称，然后选择“刷新服务”。

## <a name="error-9018-powershellnotfound"></a>错误 9018：PowerShellNotFound

### <a name="cause"></a>原因
运行 Windows Server 2008 或更低版本的服务器通常会发生此错误。

### <a name="remediation"></a>补救
需要在服务器的以下位置安装所需的 PowerShell 版本（2.0 或更高版本）：($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe。 [详细了解](/powershell/scripting/windows-powershell/install/installing-windows-powershell)如何在 Windows Server 中安装 PowerShell。

安装所需的 PowerShell 版本后，可以按照[此站点](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)中的步骤验证此错误是否得到解决。

## <a name="error-9022-getwmiobjectaccessdenied"></a>错误 9022：GetWMIObjectAccessDenied

### <a name="remediation"></a>补救
确保设备中提供的用户帐户有权访问 WMI 命名空间和子命名空间。 若要设置访问权限，请按以下步骤操作：

1.  转到报告此错误的服务器。
1.  在“开始”菜单中搜索并选择“运行” 。 在“运行”对话框的“打开”文本框中，键入“wmimgmt.msc”，然后选择“进入”。   
1.  此时会打开 wmimgmt 控制台，然后可在左侧窗格中找到“WMI 控件(本地)”。 右键单击它，然后从菜单中选择“属性”。
1.  在“WMI 控件(本地)属性”对话框中，选择“安全性”选项卡 。
1.  在“安全性”选项卡中选择“安全性”以打开“根的安全性”对话框。  
1.  选择“高级”以打开“根的高级安全设置”对话框。 
1.  选择“添加”以打开“根的权限条目”对话框。
1.  单击“选择主体”以打开“选择用户、计算机、服务帐户或组”对话框。
1. 选择要向其授予 WMI 访问权限的用户名或组，然后选择“确定”。
1. 请确保授予执行权限，并在“应用于”下拉列表中选择“此命名空间和子命名空间”。
1. 选择“应用”以保存设置并关闭所有对话框。

获取所需访问权限后，可按照[此站点](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)中的步骤验证是否已解决此错误。

## <a name="error-9032-invalidrequest"></a>错误 9032：InvalidRequest

### <a name="cause"></a>原因
此问题可能由多个原因导致。 原因之一是，设备配置管理器上提供的用户名（服务器凭据）包含无效的 XML 字符。 无效字符会导致分析 SOAP 请求时出现错误。

### <a name="remediation"></a>补救
- 请确保服务器凭据的用户名不包含无效的 XML 字符，并且采用了 username@domain.com 格式。 此格式通常称为 UPN 格式。
- 在设备上编辑凭据后，可按照[此站点](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)中的步骤验证是否已解决此错误。


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>错误 10002：ScriptExecutionTimedOutOnVm

### <a name="cause"></a>原因
- 当服务器速度缓慢或无响应，并且当为拉取依赖项数据而执行的脚本开始超时，则会发生此错误。
- 服务器上的发现代理发生此错误后，设备不会在服务器上尝试无代理依赖项分析，以免无响应的服务器发生重载。
- 在服务器上检查此问题并重启发现服务之前，此错误会一直出现。

### <a name="remediation"></a>补救
1. 登录到遇到此错误的服务器。
1. 请在 PowerShell 上运行以下命令：
   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
1. 如果命令在几秒钟后输出结果，请前往“Azure Migrate 项目” > “发现和评估” > “概述” > “管理” > “设备”，选择设备名称，然后选择“刷新服务”以重新启动发现服务。
1. 如果在未提供任何输出的情况下命令超时，则你需要：

   - 找出在服务器上占用大量 CPU 或内存的进程。
   - 尝试向该服务器提供更多核心或内存，然后再次运行命令。

## <a name="error-10005-guestcredentialnotvalid"></a>错误 10005：GuestCredentialNotValid

### <a name="remediation"></a>补救
- 通过选择设备配置管理器上的“重新验证凭据”，确保凭据（错误消息中提供的易记名称）有效。
- 确保可以使用设备中提供的相同凭据登录到受影响的服务器。
- （如果服务器已加入域，针对同一个域）可以尝试使用另一个用户帐户，而不是管理员帐户。
- 如果“全局目录”与“域控制器”的通信中断，则会发生此问题。 可以通过在域控制器中创建新的用户帐户并在设备中提供相同的帐户来检查此问题。 可能还需要重启域控制器。
- 在采取修正步骤之后，可以通过[此站点](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)中的步骤来验证是否已解决此错误。

## <a name="error-10012-credentialnotprovided"></a>错误 10012：CredentialNotProvided

### <a name="cause"></a>原因
如果在设备配置管理器上提供了具有错误域名的域凭据，则会出现此错误。 例如，如果已提供具有用户名 user@abc.com 的域凭据，但提供的域名为 def.com，则服务器连接到 def.com 时，将不会尝试这些凭据，而你会收到此错误消息。

### <a name="remediation"></a>补救
- 按照原因中所述，转到设备配置管理器，添加服务器凭据或编辑现有凭据。
- 在采取修正步骤之后，可以通过[此站点](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)中的步骤来验证是否已解决此错误。

## <a name="mitigation-verification-by-using-vmware-powercli"></a>使用 VMware PowerCLI 进行缓解措施验证

在对上述错误执行缓解步骤之后，可通过从设备服务器运行几个 PowerCLI 命令来验证缓解措施是否有效。 如果命令成功，这意味着问题已解决。 否则，请再次检查并按照修正步骤执行操作。

1. 运行以下命令，以便在设备服务器上设置 PowerCLI：
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
1. 通过在命令中提供 vCenter Server IP 地址并在提示符下提供凭据，从设备连接至 vCenter Server：
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
1. 通过提供服务器名称和服务器凭据（与设备上提供的相同），从设备连接至目标服务器：
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
1. 对于无代理依赖项分析，请运行以下命令来查看是否获得成功输出。

      - 对于 Windows 服务器：
    
          ```` 
          Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WmiObject Win32_Process'" -GuestCredential $credential 
      
          Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'netstat -ano -p tcp'" -GuestCredential $credential 
          ```` 
      - 对于 Linux 服务器：
          ````
          Invoke-VMScript -VM $vm -ScriptText "ps -o pid,cmd | grep -v ]$" -GuestCredential $credential
    
          Invoke-VMScript -VM $vm -ScriptText "netstat -atnp | awk '{print $4,$5,$7}'" -GuestCredential $credential
          ````
1. 确认缓解措施有效后，请前往“Azure Migrate 项目” > “发现和评估” > “概述” > “管理” > “设备”，选择设备名称，然后选择“刷新服务”以开始新的发现周期。

## <a name="my-log-analytics-workspace-isnt-listed-when-you-try-to-configure-the-workspace-in-azure-migrate-for-agent-based-dependency-analysis"></a>在 Azure Migrate 中尝试针对基于代理的依赖项分析配置工作区时，未列出我的 Log Analytics 工作区
Azure Migrate 目前支持在“美国东部”、“东南亚”和“西欧”区域创建 OMS 工作区。 即使在 Azure Migrate 外部任何其他区域中创建工作区，目前也不能将它与项目关联。

## <a name="agent-based-dependency-visualization-in-azure-government"></a>Azure 政府中基于代理的依赖项可视化

Azure 政府不支持基于代理的依赖关系分析。 请使用无代理依赖项分析“（仅适用于 VMware 服务器）”。

## <a name="agent-based-dependencies-dont-show-after-agent-installation"></a>安装代理后不显示基于代理的依赖项

在本地 VM 上安装了依赖项可视化代理后，Azure Migrate 通常需要 15-30 分钟的时间在门户中显示依赖项。 如果等待超过 30 分钟，请确保 Microsoft Monitoring Agent (MMA) 可以连接到 Log Analytics 工作区。

对于 Windows VM：
1. 在控制面板中，启动 MMA。
1. 在“Microsoft Monitoring Agent 属性” > “Azure Log Analytics (OMS)”中，确保工作区的“状态”为绿色。  
1. 如果状态不是绿色，请尝试删除工作区，然后再次将其添加到 MMA。

    ![屏幕截图：显示 MMA 状态。](./media/troubleshoot-assessment/mma-properties.png)

对于 Linux VM，请确保 MMA 和依赖项代理的安装命令已成功运行。 请参阅[此站点](../azure-monitor/vm/service-map.md#post-installation-issues)的更多故障排除指南。

## <a name="supported-operating-systems-for-agent-based-dependency-analysis"></a>基于代理的依赖项分析支持的操作系统

- “MMS 代理”：查看支持的 [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 和 [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 操作系统。
- “依赖项代理”：查看支持的 [Windows 和 Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) 操作系统。

## <a name="visualize-dependencies-for-1-hour-with-agent-based-dependency-analysis"></a>使用基于代理的依赖项分析以可视化方式呈现依赖项超过 1 小时

使用基于代理的依赖项分析时，尽管 Azure Migrate 允许返回到上个月的特定的一天，但可视化依赖项的最长持续时间为一小时。 例如，你可以使用依赖项映射中的持续时间功能来查看昨天的依赖项，但只能查看一小时。 可以使用 Azure Monitor 日志在更长的持续时间内[查询依赖项数据](./how-to-create-group-machine-dependencies.md)。

## <a name="visualize-dependencies-for-10-servers-with-agent-based-dependency-analysis"></a>使用基于代理的依赖项分析为超过 10 台服务器以可视化方式呈现依赖项

在 Azure Migrate 中，通过基于代理的依赖项分析，可以[可视化组的依赖项](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)（最多包含 10 个 VM）。 对于较大的组，将 VM 拆分为较小的组来以可视化方式呈现依赖项。

## <a name="servers-show-install-agent-for-agent-based-dependency-analysis"></a>针对基于代理的依赖项分析，服务器显示“安装代理”

将启用了依赖项可视化的服务器迁移到 Azure 后，服务器可能会显示“安装代理”操作，而不是“查看依赖项”，原因如下： 

- 迁移到 Azure 后，本地服务器将关闭，并在 Azure 中启动等效 VM。 这些服务器将获得一个不同的 MAC 地址。
- 服务器还可以具有不同的 IP 地址，具体取决于是否保留了本地 IP 地址。
- 如果 MAC 和 IP 地址与本地不同，Azure Migrate 不会将本地服务器与任何服务映射依赖项数据相关联。 在这种情况下，它将显示安装代理的选项，而不是查看依赖项的选项。
- 在测试迁移到 Azure 后，本地服务器仍按预期方式开启。 在 Azure 中启动的等效服务器将获取不同的 MAC 地址，并且可能会获取不同的 IP 地址。 除非阻止来自这些服务器的传出 Azure Monitor 日志流量，否则 Azure Migrate 不会将本地服务器与任何服务映射依赖项数据相关联。 在这种情况下，它将显示安装代理的选项，而不是查看依赖项的选项。

## <a name="capture-network-traffic"></a>捕获网络流量

若要收集网络流量日志，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择 F12 键启动开发人员工具。 如果需要，请清除“清除导航上的条目”设置。
1. 单击“网络”选项卡，然后开始捕获网络流量：
   - 在 Chrome 中，选择“保留日志”。 记录应自动启动。 红色圆圈指示正在捕获流量。 如果未显示红色圆圈，请选择黑色圆圈来启动。
   - 在 Microsoft Edge 和 Internet Explorer 中，记录应自动启动。 如果未启动，请单击绿色播放按钮。
1. 尝试再现该错误。
1. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中，右键单击并选择“将内容另存为 HAR”。 此操作会将日志压缩并导出为 HTTP 存档 (har) 文件。
   - 在 Microsoft Edge 或 Internet Explorer 中，选择“导出捕获的流量”选项。 此操作会将日志压缩并导出。
1. 选择“控制台”选项卡以查看任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择“另存为”以导出和压缩日志。
   - 在 Microsoft Edge 或 Internet Explorer 中，右键单击错误并选择“全部复制”。
1. 关闭“开发人员工具”。


## <a name="next-steps"></a>后续步骤

[创建](how-to-create-assessment.md)或[自定义](how-to-modify-assessment.md)评估。
