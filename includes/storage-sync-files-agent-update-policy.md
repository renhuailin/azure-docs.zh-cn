---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "77123232"
---
Azure 文件同步代理将定期更新，以便添加新功能和解决问题。 建议配置 Microsoft 更新，以便在 Azure 文件同步代理更新发布时获得这些更新。

#### <a name="major-vs-minor-agent-versions"></a>主要与次要代理版本
* 主要代理版本通常包含新的功能，并且版本号的第一个组成部分会递增。 例如：\*2.\*.\*\*
* 次要代理版本也称为“修补”，其发布频率高于主要版本。 它们通常包含 bug 修复和小幅的改进，但不包含新功能。 例如：\*\*.3.\*\*

#### <a name="upgrade-paths"></a>升级路径
可以通过四种经过批准和测试的方法来安装 Azure 文件同步代理更新。 
1. **（首选）将 Microsoft 更新配置为自动下载并安装代理更新。**  
    我们始终建议安装每项 Azure 文件同步更新，确保能够访问服务器代理的最新修补程序。 Microsoft 更新会自动下载并安装这些更新，以无缝完成此过程。
2. **使用 AfsUpdater.exe 下载并安装代理更新。**  
    AfsUpdater.exe 位于代理安装目录中。 双击可执行文件可下载并安装代理更新。 
3. **使用 Microsoft 更新修补文件或 .msp 可执行文件修补现有的 Azure 文件同步代理。可以从 [Microsoft 更新目录](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)下载最新的 Azure 文件同步更新包。**  
    运行 .msp 可执行文件将会升级 Azure 文件同步安装，所用的方法与上述升级路径中 Microsoft 更新使用的自动方法相同。 应用 Microsoft 更新修补程序会就地升级 Azure 文件同步安装。
4. 从 [Microsoft](https://go.microsoft.com/fwlink/?linkid=858257) 下载中心下载最新的 Azure 文件同步代理安装程序。  
    若要升级现有的 Azure 文件同步代理安装，请卸载旧版本，然后使用下载的安装程序安装最新版本。 服务器注册、同步组和其他任何设置由 Azure 文件同步安装程序维护。

#### <a name="automatic-agent-lifecycle-management"></a>自动代理生命周期管理
在代理版本 6 中，文件同步团队引入了一个代理自动升级功能。 有两种模式可供选择，你可以指定一个维护时段，将在该维护时段内在服务器上尝试升级。 此功能提供一个护栏，可防止代理过期，或允许无障碍，并保持最新设置，旨在帮助你完成代理生命周期管理。
1. 默认设置将尝试防止代理过期。 在公布了代理的到期日期后，代理将在 21 天内尝试自行升级。 在过期之前的 21 天内，一周内尝试一次升级，也会在选定的维护时段内尝试升级。 **即时采用了该选项，也需要获取定期的 Microsoft 更新修补丁。**
1. 或者，你可以选择让代理在新代理版本可用时自行自动升级（目前不适用于群集服务器）。 此更新将在选定的维护时段内进行，并且一旦新功能和改进正式发布，你服务器便可以立即获得。 这是一种无忧设置，也是建议的设置，将为你的服务器提供主要代理版本以及定期更新补丁。 每个发布的代理都处于 GA 质量。 如果选择此选项，Microsoft 将向你发布最新的代理版本以进行外部测试。 群集服务器不包括在内。 在外部测试完成后，代理将在 [Microsoft 下载中心](https://go.microsoft.com/fwlink/?linkid=858257)（也称为 ms/AFS/agent）中提供。

 ##### <a name="changing-the-auto-upgrade-setting"></a>更改自动升级设置

以下说明介绍了在完成安装程序后如何更改设置（如果需要进行更改）。

打开 PowerShell 控制台，导航到在其中安装了同步代理的目录，然后导入服务器 cmdlet。 默认情况下，如下所示：
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

可以运行 `Get-StorageSyncAgentAutoUpdatePolicy` 来检查当前策略设置并确定是否要对其进行更改。

要将当前策略设置更改为延迟更新跟踪，可以使用：
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

要将当前策略设置更改为立即更新跟踪，可以使用：
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>代理生命周期和变更管理保证
Azure 文件同步是一种云服务，将持续引入新功能和改进。 这意味着，特定的 Azure 文件同步代理版本只在有限的时间内受到支持。 为了简化部署，以下规则可保证用户在变更管理过程中获得足够的时间来适应代理更新/升级并收到相应的通知：

- 至少支持主要代理版本六个月（从初始版本发布日期算起）。
- 我们保证至少提供三个月的缓冲期来支持不同的主要代理版本。 
- 在代理过期之前的至少三个月，我们会在已注册的服务器中使用代理即将过期消息来发出警告。 可以在存储同步服务的“已注册服务器”部分下检查已注册的服务器是否在使用旧版代理。
- 次要代理版本的生存期受限于相关的主要版本。 例如，发布代理版本 3.0 后，代理版本 2.\* 将设置为一起过期。

> [!Note]
> 安装附带过期警告的代理版本时会显示警告，但安装会成功。 不支持且会阻止安装或连接已过期的代理版本。
