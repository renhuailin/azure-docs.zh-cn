---
title: 排查正在进行的服务器发现、软件清单和 SQL 发现的问题
description: 获取有关服务器发现、软件清单以及 SQL 和 Web 应用发现的帮助
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 124ca1ad5f8af9ab2dd08382588c8933db8a09eb
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323206"
---
# <a name="troubleshoot-the-ongoing-server-discovery-software-inventory-sql-and-web-app-discovery"></a>排查正在进行的服务器发现、软件清单、SQL 和 Web 应用发现的问题

本文可帮助排查正在进行的服务器发现、软件清单以及 SQL Server 实例和数据库发现的问题。

## <a name="discovered-servers-not-showing-in-portal"></a>已发现服务器未在门户中显示

**错误**

如果尚未在门户中看到服务器，并且发现状态为“正在发现”，则会收到此错误。
 
**修正**

如果服务器未显示在门户中，请等待几分钟，因为发现在 vCenter Server 上运行的服务器大约需要 15 分钟，每个 Hyper-V 主机（在设备中添加以便发现主机上运行的服务器）需要 2 分钟，发现在物理设备中添加的每个服务器需要 1 分钟。

如果状态仍未更改，请执行如下操作：

- 选择“服务器”选项卡上的“刷新”，以在“Azure Migrate: 发现和评估”以及“Azure Migrate: 服务器迁移”中查看已发现服务器的计数 。

如果以上步骤不起作用，并且你要发现 VMware 服务器：

1. 请验证指定的 vCenter 帐户是否已正确设置权限，并且至少有权访问一台服务器。
1. 在 VMware 上检查 vCenter 帐户是否具有按 vCenter VM 文件夹级别授予的访问权限。 Azure Migrate 无法发现 VMware 上的服务器 [详细了解](set-discovery-scope.md)如何限定发现范围。

## <a name="server-data-not-updating-in-portal"></a>服务器数据未在门户中更新

**错误**

如果已发现服务器未显示在门户中，或者服务器数据已过时，则会收到此错误。 

**修正**

请等待几分钟，因为已发现服务器配置数据的更改最多需要 30 分钟才能显示在门户中，而软件清单数据的更改可能需要数小时才能显示。 如果此时间之后未显示任何数据，请刷新并执行如下操作：

1. 在“Windows、Linux 和 SQL Server”、“Azure Migrate: 发现和评估”中，选择“概述”  。
1. 在“管理”下，选择“设备” 。
1. 选择“刷新服务”。
等待刷新操作完成。 现在应会显示最新信息。

## <a name="deleted-servers-appear-in-portal"></a>门户中显示已删除的服务器

**错误**

当已删除的服务器继续显示在门户中时，会收到此错误。

**修正**

如果数据继续显示，请等待 30 分钟并执行如下操作：

在“Windows、Linux 和 SQL Server”、“Azure Migrate: 发现和评估”中，选择“概述”  。
1. 在“管理”下，选择“设备” 。
1. 选择“刷新服务”。
等待刷新操作完成。 现在应会显示最新信息。

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>我导入了 CSV，但系统显示“正在发现”

如果 CSV 因验证失败而不能上传，则会显示此状态。 

**修正**

再次导入 CSV。 你可以下载上一个上传错误报告，并遵循该文件的修正指导来修复错误。 可从“发现服务器”页的“导入详细信息”部分下载错误报告 。

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>即使在更新来宾凭据后，也看不到软件清单详细信息

**修正**

软件清单发现每 24 小时运行一次。 这可能需要几分钟时间，具体取决于发现的服务器数。 如果希望立即查看详细信息，请按如下所示进行刷新。

1. 在“Windows、Linux 和 SQL Server”、“Azure Migrate: 发现和评估”中，选择“概述”  。
1. 在“管理”下，选择“设备” 。
1. 选择“刷新服务”。
等待刷新操作完成。 现在应会显示最新信息。

## <a name="unable-to-export-software-inventory"></a>无法导出软件清单

**错误**

没有参与者权限时，会收到此错误

**修正**

确保从门户下载清单的用户在订阅上拥有“参与者”权限。

## <a name="common-software-inventory-errors"></a>常见软件清单错误

Azure Migrate 支持使用“Azure Migrate: 发现和评估”来生成软件清单。 目前，只有 VMware 才支持软件清单。 [详细了解](how-to-discover-applications.md)软件清单的要求。

下表汇总了软件清单错误的列表。

> [!Note]
> 无代理依赖项分析采用与软件清单相同的方法收集所需数据，因而也可能会遇到相同的错误。

| **错误** | **原因** | **操作** |
|--|--|--|
| 9000：无法检测到服务器上 VMware 工具的状态 | 服务器上可能未安装 VMware 工具，或者安装的版本已损坏。 | 请确保在服务器上安装并运行版本高于 10.2.1 的 VMware 工具。 |
| 9001：服务器上未安装 VMware 工具。 | 服务器上可能未安装 VMware 工具，或者安装的版本已损坏。 | 请确保在服务器上安装并运行版本高于 10.2.1 的 VMware 工具。 |
| 9002：服务器上未运行 VMware 工具。 | 服务器上可能未安装 VMware 工具，或者安装的版本已损坏。 | 请确保在服务器上安装并运行版本高于 10.2.0 的 VMware 工具。 |
| 9003：服务器上运行的操作系统类型不受支持。 | 服务器上运行的操作系统既不是 Windows 也不是 Linux。 | 目前仅支持 Windows 和 Linux 操作系统类型。 如果服务器确实运行的是 Windows 或 Linux 操作系统，请检查 vCenter Server 中指定的操作系统类型。 |
| 9004：服务器未处于正在运行状态。 | 服务器处于关机状态。 | 请确保服务器处于正在运行状态。 |
| 9005：服务器上运行的操作系统类型不受支持。 | 服务器上运行的操作系统既不是 Windows 也不是 Linux。 | 目前仅支持 Windows 和 Linux 操作系统类型。 目前不支持 \<FetchedParameter> 操作系统。 |
| 9006：从服务器下载发现元数据文件所需的 URL 为空。 | 这可能是暂时性问题，因为设备的发现代理未如预期工作。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9007：在服务器中找不到运行脚本来收集元数据的进程。 | 这可能是暂时性问题，因为设备的发现代理未如预期工作。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9008：无法检索到服务器上元数据收集进程的状态。 | 这可能是由于内部错误导致的暂时性问题。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9009：Windows 用户帐户控制 (UAC) 阻止程序在服务器上执行发现操作。 | Windows 用户帐户控制 (UAC) 设置限制程序发现服务器上已安装的应用程序。 | 在受影响的服务器上，降低控制面板中“用户帐户控制”设置的级别。 |
| 9010：服务器关机。 | 服务器处于关机状态。 | 请确保服务器处于开机状态。 |
| 9011：在服务器上找不到包含所发现元数据的文件。 | 这可能是由于内部错误导致的暂时性问题。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9012：服务器上包含所发现元数据的文件为空。 | 这可能是由于内部错误导致的暂时性问题。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9013：每次登录服务器时都会新建一个临时用户配置文件。 | 每次登录服务器时都会新建一个临时用户配置文件。 | 请提交 Microsoft 支持案例，让我们帮助你解决此问题。 |
| 9014：因为 ESXi 主机出错，无法检索包含所发现元数据的文件。  错误代码：%ErrorCode；详细信息：%ErrorMessage | 在 ESXi 主机 \<HostName> 上遇到错误。  错误代码：%ErrorCode；详细信息：%ErrorMessage | 请确保运行服务器的 ESXi 主机上的端口 443 为打开状态。<br/><br/> [详细了解](troubleshoot-discovery.md#error-9014-httpgetrequesttoretrievefilefailed)如何修正此问题。|
| 9015：为服务器发现提供的 vCenter Server 用户帐户没有启用来宾操作权限。 | 尚未在 vCenter Server 用户帐户上启用来宾操作所需的权限。 | 请确保 vCenter Server 用户帐户启用了对“虚拟机”>“来宾操作”的权限，以便与服务器进行交互并请求所需的数据。 <br/><br/> [详细了解](tutorial-discover-vmware.md#prepare-vmware)如何设置具有所需权限的 vCenter Server 帐户。 |
| 9016：无法发现元数据，因为服务器上的来宾操作代理已过时。 | 服务器上未安装 VMware 工具，或者安装的不是最新版本。 | 请确保在服务器上安装并运行最新版 VMware 工具。 VMware 工具版本必须为 10.2.1 或更高版本。 |
| 9017：在服务器上找不到包含所发现元数据的文件。 | 这可能是由于内部错误导致的暂时性问题。 | 请提交 Microsoft 支持案例，让我们帮助你解决此问题。 |
| 9018：服务器上未安装 PowerShell。 | 在服务器上找不到 PowerShell。 | 请确保服务器上安装了 PowerShell 2.0 或更高版本。 <br/><br/> [详细了解](troubleshoot-discovery.md#error-9018-powershellnotfound)如何修正此问题。|
| 9019：无法发现元数据，因为服务器上的来宾操作失败。 | 无法在服务器上执行 VMware 来宾操作。 在服务器上尝试以下凭据时遇到问题：```<FriendlyNameOfCredentials>.``` | 请确保设备上提供的服务器凭据有效，并且凭据中提供的用户名采用 UPN 格式。 （在可能的原因中查找 Azure Migrate 尝试过的易记的凭据名称） |
| 9020：无法在服务器上创建包含已发现的元数据所需的文件。 | 与设备或本地组策略中提供的凭据关联的角色限制在所需文件夹中创建文件。 在服务器上尝试以下凭据时遇到问题：```<FriendlyNameOfCredentials>.``` | 1. 检查设备上提供的凭据是否对服务器中的文件夹 \<folder path/folder name> 具有创建文件的权限。 <br/>2. 如果设备上提供的凭据没有所需的权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中查找 Azure Migrate 尝试过的易记的凭据名称） |
| 9021：无法在服务器的正确路径上创建包含已发现的元数据所需的文件。 | VMware 工具报告创建文件时的文件路径不正确。 | 请确保在服务器上安装并运行版本高于 10.2.0 的 VMware 工具。 |
| 9022：拒绝访问，无法在服务器上运行 Get-WmiObject cmdlet。 | 与设备或本地组策略中提供的凭据关联的角色限制对 WMI 对象的访问。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 检查设备上提供的凭据是否具有创建文件的管理员权限并启用了 WMI。 <br/> 2. 如果设备上提供的凭据没有所需的权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记的凭据名称）。<br/><br/> [详细了解](troubleshoot-discovery.md#error-9022-getwmiobjectaccessdenied)如何修正此问题。|
| 9023：无法运行 PowerShell，因为 %SystemRoot% 环境变量值是空的。 | 对于服务器，%SystemRoot% 环境变量的值是空的。 | 1. 在受影响的服务器上运行 echo %systemroot% 命令，以检查环境变量是否返回空值。 <br/> 2. 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9024：无法执行发现，因为 %TEMP% 环境变量值为空。 | 对于服务器，%TEMP% 环境变量的值是空的。 | 1. 在受影响的服务器上运行 echo %temp% 命令，以检查环境变量是否返回空值。 <br/> 2. 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9025：无法执行发现，服务器上的 PowerShell 已损坏。 | 服务器上的 PowerShell 已损坏。 | 请重新安装 PowerShell，并验证它是否在受影响的服务器上运行。 |
| 9026：无法在服务器上运行来宾操作。 | 服务器的当前状态不允许运行来宾操作。 | 1. 确保受影响的服务器已启动并正在运行。<br/> 2. 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 9027：无法发现元数据，因为未在服务器上运行来宾操作代理。 | 无法联系服务器上的来宾操作代理。 | 请确保在服务器上安装并运行版本高于 10.2.0 的 VMware 工具。 |
| 9028：由于服务器上的存储空间不足，无法创建包含已发现的元数据所需的文件。 | 服务器磁盘上的存储空间不足。 | 请确保受影响服务器的磁盘存储有足够的可用空间。 |
| 9029：设备上提供的凭据没有运行 PowerShell 所需的访问权限。 | 设备上提供的凭据没有运行 PowerShell 所需的访问权限。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 确保设备上提供的凭据可以访问服务器上的 PowerShell。<br/> 2. 如果设备上提供的凭据没有所需的访问权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中查找 Azure Migrate 尝试过的易记的凭据名称） |
| 9030：无法收集已发现的元数据，因为托管服务器的 ESXi 主机已断开连接。 | 服务器所驻留的 ESXi 主机已断开连接。 | 请确保运行服务器的 ESXi 主机处于已连接状态。 |
| 9031：无法收集已发现的元数据，因为托管服务器的 ESXi 主机未响应。 | 服务器所驻留的 ESXi 主机处于无效状态。 | 请确保运行服务器的 ESXi 主机正在运行且处于已连接状态。 |
| 9032：无法执行发现，因为发生了内部错误。 | 如果发生内部错误，就会出现此问题。 | 请按照[此处](troubleshoot-discovery.md#error-9032-invalidrequest)的步骤来解决此问题。 如果问题仍然存在，请打开 Microsoft 支持案例。 |
| 9033：无法执行发现，因为设备上提供的服务器凭据的用户名中包含无效字符。 | 设备上提供的凭据在用户名中包含无效字符。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 请确保设备上提供的凭据在用户名中没有任何无效字符。 你可以返回到设备配置管理器来编辑凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记的凭据名称）。 |
| 9034：无法执行发现，因为设备上提供的服务器凭据的用户名不是 UPN 格式。 | 设备上提供的凭据所用用户名不是 UPN 格式。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 请确保设备上提供的凭据采用用户主体名称 (UPN) 格式的用户名。 你可以返回到设备配置管理器来编辑凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记的凭据名称）。 |
| 9035：无法执行发现，因为 PowerShell 语言模式设置不正确。 | PowerShell 语言模式未设置为“完整语言”。 | 确保 PowerShell 语言模式设置为“完整语言”。 |
| 9036：无法执行发现，因为设备上提供的服务器凭据的用户名不是 UPN 格式。 | 设备上提供的凭据所用用户名不是 UPN 格式。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 请确保设备上提供的凭据采用用户主体名称 (UPN) 格式的用户名。 你可以返回到设备配置管理器来编辑凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记的凭据名称）。 |
| 9037：由于服务器的响应时间较长，已暂停收集元数据。 | 服务器的响应时间过长。 | 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 10000：服务器上运行的操作系统类型不受支持。 | 服务器上运行的操作系统既不是 Windows 也不是 Linux。 | 目前仅支持 Windows 和 Linux 操作系统类型。 目前不支持 \<GuestOSName> 操作系统。 |
| 10001：在服务器上找不到收集发现元数据所需的脚本。 | 执行发现所需的脚本可能已删除或已从预期位置中删除。 | 请提交 Microsoft 支持案例，让我们帮助你解决此问题。 |
| 10002：服务器上的发现操作已过时。 | 这可能是暂时性问题，因为设备的发现代理未如预期工作。 | 此问题应在下一个周期的 24 小时内自动解决。 如果未解决，请按照[此处](troubleshoot-discovery.md#error-10002-scriptexecutiontimedoutonvm)的步骤来修正问题。 如果问题仍然存在，请打开 Microsoft 支持案例。|
| 10003：执行发现操作的进程因错误而退出。 | 由于出现错误，执行发现操作的进程突然退出。| 此问题应在下一个周期的 24 小时内自动解决。 如果问题仍然存在，请提交 Microsoft 支持案例。 |
| 10004：设备上没有为服务器 OS 类型提供凭据。 | 设备上没有为服务器 OS 类型添加凭据。 | 1. 确保为设备中受影响服务器的 OS 类型添加凭据。<br/> 2. 现在可以在设备中添加多个服务器凭据。 |
| 10005：设备上为服务器提供的凭据无效。 | 设备上提供的凭据无效。 在服务器上尝试以下凭据时遇到问题：```\<FriendlyNameOfCredentials>.``` | 1. 确保设备配置管理器上提供的凭据有效，而且可使用这些凭据访问服务器。<br/> 2. 现在可以在设备中添加多个服务器凭据。<br/> 3. 返回到设备配置管理器，以提供另一组凭据或编辑现有凭据。 （在可能的原因中找到 Azure Migrate 尝试过的易记的凭据名称）。 <br/><br/> [详细了解](troubleshoot-discovery.md#error-10005-guestcredentialnotvalid)如何修正此问题。|
| 10006：服务器上运行的操作系统类型不受支持。 | 服务器上运行的操作系统既不是 Windows 也不是 Linux。 | 目前仅支持 Windows 和 Linux 操作系统类型。 目前不支持 \<GuestOSName> 操作系统。 |
| 10007：无法从服务器处理发现的元数据。 | 解析包含所发现元数据的文件内容时出错。 | 请提交 Microsoft 支持案例，让我们帮助你解决此问题。 |
| 10008：无法在服务器上创建包含已发现的元数据所需的文件。 | 与设备或本地组策略中提供的凭据关联的角色限制在所需文件夹中创建文件。 在服务器上尝试以下凭据时遇到问题：```<FriendlyNameOfCredentials>.``` | 1. 检查设备上提供的凭据是否对服务器中的文件夹 \<folder path/folder name> 具有创建文件的权限。<br/> 2. 如果设备上提供的凭据没有所需的权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中查找 Azure Migrate 尝试过的易记的凭据名称） |
| 10009：无法将发现的元数据写入到服务器上的文件。 | 与设备或本地组策略上提供的凭据关联的角色限制在服务器上的文件中写入。 在服务器上尝试以下凭据时遇到问题：\<FriendlyNameOfCredentials>。 | 1. 检查设备上提供的凭据是否对服务器中的文件夹 <文件夹路径/文件夹名称> 具有写入文件的权限。<br/> 2. 如果设备上提供的凭据没有所需的权限，请提供另一组凭据或编辑现有凭据。 （在可能的原因中查找 Azure Migrate 尝试过的易记的凭据名称） |
| 10010：无法执行发现，因为服务器上缺少收集某些元数据所需的命令“%CommandName;”。 | 服务器上未安装包含命令“%CommandName;”的包。 | 确保服务器上已安装包含命令“%CommandName;”的包。 |
| 10011：使用设备上提供的凭据来登录和注销交互式会话。 | 交互式登录和注销会强制在正在使用的帐户的配置文件中卸载注册表项。 此条件导致密钥不可供日后使用。 | 请使用[此处](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error)介绍的解决方法 |
| 10012：尚未在设备中为服务器提供凭据。 | 未为服务器提供凭据，或者你在设备中提供了域名不正确的域凭据。[详细了解](troubleshoot-discovery.md#error-10012-credentialnotprovided)此错误的原因。 | 1. 确保设备上为服务器提供的凭据有效，而且可使用这些凭据访问服务器。 <br/> 2. 现在可以在设备中为服务器添加多个凭据。 返回设备配置管理器，为服务器提供凭据。|

## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>错误 9014：HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>原因
当设备中的 VMware 发现代理尝试通过承载服务器的 ESXi 主机从服务器文件系统下载包含依赖项数据的输出文件时，会发生此问题。

### <a name="remediation"></a>补救
- 可以通过在设备服务器上打开 PowerShell 并执行以下命令，在设备的 443 端口（需要在 ESXi 主机上打开以拉取依赖项数据）上测试 ESXi 主机“（错误消息中提供的名称）”的 TCP 连接：
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````
- 如果命令返回成功连接，可以转到“Azure Migrate 项目”>“发现和评估”>“概述”>“管理”>“设备”，选择设备名称，然后选择“刷新服务”。

## <a name="error-9018-powershellnotfound"></a>错误 9018：PowerShellNotFound

### <a name="cause"></a>原因
运行 Windows Server 2008 或更低版本的服务器通常会发生此错误。

### <a name="remediation"></a>补救
需要在服务器上的以下位置安装所需的 PowerShell 版本（2.0 或更高版本）：($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe。 [详细了解](/powershell/scripting/windows-powershell/install/installing-windows-powershell)如何在 Windows Server 中安装 PowerShell。

安装所需的 PowerShell 版本后，可以按照[此处](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)的步骤验证错误是否得到解决。

## <a name="error-9022-getwmiobjectaccessdenied"></a>错误 9022：GetWMIObjectAccessDenied

### <a name="remediation"></a>补救
确保设备中提供的用户帐户有权访问 WMI 命名空间和子命名空间。 可按照以下步骤设置访问权限：
1.  转到报告此错误的服务器。
1. 在“开始”菜单中搜索并选择“运行”。 在“运行”对话框的“打开:”文本字段中，键入“wmimgmt.msc”并按 Enter。
1. 此时会打开 wmimgmt.msc 控制台，可在左侧面板中找到“WMI 控件(本地)”。 右键单击此项并从菜单中选择“属性”。
1. 在“WMI 控件(本地)属性”对话框中，选择“安全”选项卡。
1. 选择“安全”按钮，“根的安全”对话框随即打开 。
1. 选择“高级”按钮以打开“根的高级安全设置”对话框 。 
1. 选择“添加”按钮，打开“根的权限条目”对话框 。
1. 单击“选择主体”以打开“选择用户、计算机、服务帐户或组”对话框 。
1. 选择要向其授予 WMI 访问权限的用户名或组，然后单击“确定”。
1. 请确保授予执行权限，并在“应用于:”下拉菜单中选择“此命名空间和子命名空间”。
1. 选择“应用”按钮，以保存设置并关闭所有对话框。

获取所需的访问权限后，可以按照[此处](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)的步骤验证是否已解决错误。

## <a name="error-9032-invalidrequest"></a>错误 9032：InvalidRequest

### <a name="cause"></a>原因
造成此问题的原因可能有多个，其中一个原因是设备配置管理器上提供的用户名（服务器凭据）包含无效的 XML 字符，这会导致分析 SOAP 请求时出错。

### <a name="remediation"></a>补救
- 请确保服务器凭据的用户名不包含无效的 XML 字符，并且采用 username@domain.com 格式，即通常所称的 UPN 格式。
- 在设备上编辑凭据后，可以按照[此处](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)的步骤验证是否已解决错误。


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>错误 10002：ScriptExecutionTimedOutOnVm

### <a name="cause"></a>原因
- 当服务器速度缓慢或无响应，并且为拉取依赖项数据执行的脚本开始超时时，会发生此错误。
- 服务器上的发现代理遇到此错误后，设备不会在服务器上尝试无代理依赖项分析，以免服务器停止响应。
- 因此，在查看服务器问题并重新启动发现服务之前，此错误会一直出现。

### <a name="remediation"></a>补救
1. 登录到服务器时遇到此错误。
1. 在 PowerShell 上运行以下命令：
   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
1. 如果命令在几秒钟后输出结果，你可以前往“Azure Migrate 项目”、“发现和评估”、“概述”、“管理”、“设备”，选择设备名称，然后选择“刷新服务”以重新启动发现服务。
1. 如果在未提供任何输出的情况下命令超时，则 
- 需要找出服务器上占用大量 CPU 或内存的进程。
- 你可以尝试将更多的核心/内存提供给该服务器并再次执行这些命令。

## <a name="error-10005-guestcredentialnotvalid"></a>错误 10005：GuestCredentialNotValid

### <a name="remediation"></a>补救
- 通过单击设备配置管理器上的“重新验证凭据”，确保凭据“（错误中提供的易记名称）”有效。
- 确保可以使用设备中提供的相同凭据登录到受影响的服务器。
- （如果服务器已加入域，针对同一个域）可以尝试使用另一个用户帐户，而不是管理员帐户。
- 如果“全局目录”与“域控制器”的通信中断，则会发生此问题。 可以通过在域控制器中创建新的用户帐户并在设备中提供相同的帐户来进行检查。 这可能还需要重启域控制器。
- 在采取修正步骤之后，你可以通过[此处](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)列出的步骤来验证是否已解决错误。

## <a name="error-10012-credentialnotprovided"></a>错误 10012：CredentialNotProvided

### <a name="cause"></a>原因
如果在设备配置管理器上提供了具有错误域名的域凭据，将会出现此错误。 例如，如果已提供具有用户名 user@abc.com 的域凭据，但域名为 def.com，则服务器连接到 def.com 时，将不会尝试这些凭据，并且你会收到此错误消息。

### <a name="remediation"></a>补救
- 按照原因中所述，转到设备配置管理器添加服务器凭据或编辑现有的凭据。
- 在采取修正步骤之后，你可以通过[此处](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)列出的步骤来验证是否已解决错误。

## <a name="mitigation-verification-using-vmware-powercli"></a>使用 VMware PowerCLI 进行缓解措施验证
针对上面列出的错误执行缓解措施后，可以在设备服务器上运行几个 PowerCLI 命令来验证缓解措施是否有效。 如果这些命令成功执行，则表示问题现已得到解决，否则还需要再次检查并执行修正步骤。

1. 运行以下命令，以便在设备服务器上设置 PowerCLI：
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
2. 通过在命令中提供 vCenter Server IP 地址并在提示符下提供凭据，从设备连接至 vCenter Server：
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
3. 提供服务器名称和服务器凭据（与设备上提供的相同），从设备连接至目标服务器：
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
4. 对于软件清单，请运行以下命令来查看是否获得成功输出：

  - 对于 Windows 服务器：

      ```` 
        Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WMIObject win32_operatingsystem'" -GuestCredential $credential

        Invoke-VMScript -VM $vm -ScriptText "powershell.exe Get-WindowsFeature" -GuestCredential $credential
      ```` 
   - 对于 Linux 服务器：
      ````
      Invoke-VMScript -VM $vm -ScriptText "ls" -GuestCredential $credential
      ````
5. 对于无代理依赖项分析，请运行以下命令来查看是否获得成功输出：

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
6. 确认缓解措施有效后，可以前往“Azure Migrate 项目”>“发现和评估”>“概述”>“管理”>“设备”，选择设备名称，然后选择“刷新服务”以开始新的发现周期。

## <a name="discovered-sql-server-instances-and-databases-not-in-portal"></a>发现的 SQL Server 实例和数据库未在门户中显示

在设备上启动发现后，可能需要长达 24 小时才能开始在门户中显示清单数据。

如果尚未在设备配置管理器上提供 Windows 身份验证或 SQL Server 身份验证凭据，请添加凭据，使设备可使用这些凭据连接到相应 SQL Server 实例。

连接后，设备会收集 SQL Server 实例和数据库的配置和性能数据。 SQL Server 配置数据每 24 小时更新一次，性能数据每 30 秒捕获一次。 因此，对 SQL Server 实例和数据库的属性（如数据库状态、兼容级别等）进行的任何更改可能需要长达 24 小时才能在门户中更新。

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>SQL Server 实例在门户上显示为“未连接”状态

若要查看 SQL Server 实例和数据库发现期间遇到的问题，请在项目的“已发现的服务器”页上的连接状态列中单击“未连接”状态。

对于包含未完全发现或处于未连接状态的 SQL 实例的服务器，在其上创建评估可能会导致将就绪状态标记为“未知”。

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>常见的 SQL Server 实例和数据库发现错误

Azure Migrate 支持使用“Azure Migrate：发现和评估”来发现在本地计算机上运行的 SQL Server 实例和数据库。 目前，只有 VMware 才支持 SQL 发现。 请参阅[“发现”教程](tutorial-discover-vmware.md)以进行入门。

下表汇总了典型的 SQL 发现错误。

| **错误** | **原因** | **操作** | **指南**
|--|--|--|--|
|30000：与此 SQL Server 关联的凭据不起作用。|手动关联的凭据无效，或自动关联的凭据无法再访问 SQL Server。|在设备上为 SQL Server 添加凭据，并等到下一个 SQL 发现周期或强制刷新。| - |
|30001：无法从设备连接到 SQL Server。|1. 设备没有在网络上发现 SQL Server。<br/>2. 防火墙阻止 SQL Server 与设备之间的连接。|1. 从 SQL Server 设置为允许从设备访问。<br/>2. 允许从设备到 SQL Server 的传入连接。| - | 
|30003：证书不受信任。|运行 SQL Server 的计算机上未安装受信任的证书。|请在服务器上设置受信任的证书。 [了解详细信息](/troubleshoot/sql/connect/error-message-when-you-connect)| [视图](/troubleshoot/sql/connect/error-message-when-you-connect) |
|30004：权限不足。|出现此错误可能是由于缺少扫描 SQL Server 实例所需的权限。 |将 sysadmin 角色授予设备上提供的凭据/帐户，以用于发现 SQL Server 实例和数据库。 [了解详细信息](/sql/t-sql/statements/grant-server-permissions-transact-sql)| [视图](/sql/t-sql/statements/grant-server-permissions-transact-sql) |
|30005：SQL Server 登录名无法进行连接，原因是其默认的主数据库出现问题。|数据库本身无效或登录名缺少数据库的 CONNECT 权限。|使用 ALTER LOGIN 将默认数据库设置为主数据库。<br/>将 sysadmin 角色授予设备上提供的凭据/帐户，以用于发现 SQL Server 实例和数据库。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error) |
|30006：SQL Server 登录名不能用于 Windows 身份验证。|1. 此登录名可能是 SQL Server 登录名，但服务器仅接受 Windows 身份验证。<br/>2. 你尝试使用 SQL Server 身份验证连接，但 SQL Server 上不存在所用登录名。<br/>3. 登录可能使用 Windows 身份验证，但登录名是无法识别的 Windows 主体。 无法识别的 Windows 主体表示 Windows 无法验证此登录名。 这可能是由于此 Windows 登录名来自不可信的域。|如果尝试使用 SQL Server 身份验证进行连接，请验证是否在混合身份验证模式下配置了 SQL Server 并且存在 SQL Server 登录名。<br/>如果尝试使用 Windows 身份验证进行连接，请验证您是否正确地登录到相应的域。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error) |
|30007：密码已过期。|该帐户的密码已过期。|SQL Server 登录密码可能已过期，请重新设置密码并/或延长密码到期日期。 [了解详细信息](/sql/relational-databases/native-client/features/changing-passwords-programmatically)| [视图](/sql/relational-databases/native-client/features/changing-passwords-programmatically) |
|30008：必须更改密码。|必须更改该帐户的密码。|更改针对 SQL Server 发现提供的凭据的密码。 [了解详细信息](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105))| [视图](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105)) |
|30009：发生内部错误。|发现 SQL Server 实例和数据库时发生内部错误。 |如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30010：找不到数据库|在所选服务器实例中找不到任何数据库。|将 sysadmin 角色授予设备上提供的凭据/帐户，以用于发现 SQL 数据库。| - |
|30011：评估 SQL 实例或数据库时发生内部错误。|执行评估时发生内部错误。|如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30012：SQL 连接失败。|1. 服务器上的防火墙拒绝了此连接。<br/>2. SQL Server Browser 服务 (sqlbrowser) 未启动。<br/>3. SQL Server 未响应客户端请求，可能是因为尚未启动服务器。<br/>4. SQL Server 客户端无法连接到服务器。 发生此错误的原因可能是服务器未配置为接受远程连接。<br/>5. SQL Server 客户端无法连接到服务器。 发生此错误的原因可能是客户端无法解析服务器的名称或服务器的名称不正确。<br/>6. TCP 或 Named Pipes 协议未启用。<br/>7. 指定的 SQL Server 实例名称无效。|请使用[此](https://go.microsoft.com/fwlink/?linkid=2153317)交互式用户指南解决连接问题。 按照指南进行操作之后请等待 24 小时，以便数据在服务中更新。 如果问题依然出现，请联系 Microsoft 支持人员。| [视图](https://go.microsoft.com/fwlink/?linkid=2153317) |
|30013：与 SQL Server 实例建立连接时出错。|1. 无法从设备解析 SQL Server 的名称。<br/>2. SQL Server 不允许远程连接。|如果可以从设备 ping SQL server，请等待 24 小时，然后检查此问题是否自动解决。 如果没有，请联系 Microsoft 支持部门。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error) |
|30014：用户名或密码无效。| 出现此错误的原因可能是身份验证失败，与密码或用户名错误有关。|请提供具有有效用户名和密码的凭据。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) |
|30015：发现 SQL 实例时出现内部错误。|发现 SQL 实例时出现内部错误。|如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30016：由于超时，与实例“%instance;”的连接失败。| 如果服务器上的防火墙拒绝连接，就可能发生这种情况。|验证 SQL Server 上的防火墙是否配置为接受连接。 如果错误仍然存在，请联系 Microsoft 支持部门。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error) |
|30017：出现内部错误。|未经处理的异常。|如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30018：出现内部错误。|收集 SQL 实例的临时数据库大小、文件大小等数据时出现内部错误。|请等待 24 小时，如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30019：出现内部错误。|收集数据库或实例的性能指标（如内存使用率等）时出现内部错误。|请等待 24 小时，如果问题仍然存在，请与 Microsoft 支持部门联系。| - |

## <a name="common-web-apps-discovery-errors"></a>常见 Web 应用发现错误

Azure Migrate 支持使用“Azure Migrate：发现和评估”来发现在本地计算机上运行的 ASP.NET Web 应用。 目前，只有 VMware 才支持 Web 应用发现。 请参阅[“发现”教程](tutorial-discover-vmware.md)以进行入门。

下表汇总了典型的 Web 应用发现错误。

| **错误** | **原因** | **操作** |
|--|--|--|
| 40001：未启用 IIS 管理控制台功能。 | IIS Web 应用发现使用 IIS 本地版本中包含的管理 API 来读取 IIS 配置。 当 IIS 的 IIS 管理控制台功能处于启用状态时，可以使用此 API。 此功能未启用，或者 OS 不是 IIS Web 应用发现支持的版本。| 请确保启用了 Web 服务器 (IIS) 角色（包括 IIS 管理控制台功能（属于管理工具的一部分）），并且服务器 OS 是版本 Windows Server 2008 R2 或更高版本。|
| 40002：无法从设备连接到服务器。 | 由于登录凭据无效或计算机不可用，连接到服务器失败。 | 请确保为服务器提供的登录凭据正确，并且服务器处于联机状态并且接受 WS-Management PowerShell 远程连接。 |
| 40003：由于凭据无效，无法连接到服务器。 | 由于登录凭据无效，连接到服务器失败。 | 请确保为服务器提供的登录凭据正确，并且已启用 WS-Management PowerShell 远程处理。 |
| 40004：无法访问 IIS 配置。 | 没有权限或权限不足。 | 确认为服务器提供的用户凭据是管理员级别凭据，并且用户有权访问 IIS 目录 (%windir%\System32\inetsrv) 和 IIS 服务器配置目录 (%windir%\System32\inetsrv\config) 下的文件。 |
| 40005：无法完成 IIS 发现。 | 未能在 VM 上完成发现。 这可能是由于访问服务器上的配置时出现问题。 错误为：“%detailedMessage;”。 | 确认为服务器提供的用户凭据是管理员级别凭据，并且用户有权访问 IIS 目录 (%windir%\System32\inetsrv) 和 IIS 服务器配置目录 (%windir%\System32\inetsrv\config) 下的文件。 然后与支持人员联系以了解错误详细信息。 |
| 40006：未分类的异常。 | 新的错误场景。 | 请与 Microsoft 支持人员联系并提供错误详细信息和日志。 可以在设备服务器上的 C:\ProgramData\Microsoft Azure\Logs 路径下找到日志。 |
| 40007：找不到 Web 服务器的 Web 应用。 | Web 服务器没有任何托管的应用程序。 | 检查 Web 服务器配置。 |

## <a name="next-steps"></a>后续步骤

为 [VMware](how-to-set-up-appliance-vmware.md)、[Hyper-V](how-to-set-up-appliance-hyper-v.md) 或[物理服务器](how-to-set-up-appliance-physical.md)设置设备。