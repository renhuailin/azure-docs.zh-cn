---
title: 密码保护代理发行历史记录 - Azure Active Directory
description: 记录了版本发行和行为更改历史记录
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 06/04/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa4c7625637c84404b5ed7074c078717127ee1f
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545352"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Azure AD 密码保护代理版本历史记录

## <a name="121760"></a>1.2.176.0

发布日期：2021 年 6 月 4 日

* 针对阻止代理和 DC 代理在某些环境中成功运行的问题进行的小型 Bug 修复。

## <a name="121720"></a>1.2.172.0

发布日期：2021 年 2 月 22 日

自推出本地 Azure AD 密码保护代理的 GA 版本以来，已经有将近两年的时间。 现已推出新的更新 - 请参阅下面的更改描述。 感谢向我们提供产品相关反馈的每位用户。 

* DC 代理和代理程序软件现在需要安装 .NET 4.7.2。
  * 如果尚未安装 .NET 4.7.2，请下载并运行在[适用于 Windows 的 .NET Framework 4.7.2 脱机安装程序](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)中找到的安装程序。
* AzureADPasswordProtection PowerShell 模块现在也由 DC 代理软件进行安装。
* 添加了两个与运行状况相关的新 PowerShell cmdlet：Test-AzureADPasswordProtectionDCAgent 和 Test-AzureADPasswordProtectionProxy。
* AzureADPasswordProtection DC 代理密码筛选器 dll 现在将在 lsass.exe 被配置为以 PPL 模式运行的计算机上加载并运行。
* 密码 bug 修复算法，允许长度小于 5 个字符的禁止密码被错误接受。
  * 此 bug 仅适用于以下情况：本地 AD 最小密码长度策略已配置为首次允许小于 5 个字符的密码。
* 其他小 bug 修复。

新的安装程序将自动升级较旧版本的软件。 如果在一台计算机上同时安装了 DC 代理和代理软件（仅建议用于测试环境），则必须同时升级这两个代理。

虽然我们建议将所有代理作为最佳做法升级到最新版本，但也支持在域或林中运行较旧版本和较新版本的 DC 代理和代理软件。 支持对代理升级进行任何排序 - 新的 DC 代理可通过较旧的代理程序进行通信，较旧的 DC 代理可通过较新的代理程序进行通信。

## <a name="121250"></a>1.2.125.0

发布日期：2019 年 3 月 2 日

* 修复事件日志消息中的拼写小错误
* 更新 EULA 协议至最终正式发布版本

> [!NOTE]
> 版本 1.2.125.0 是正式发布版本。 再次感谢向我们提供产品相关反馈的每位用户！

## <a name="121160"></a>1.2.116.0

发布日期：2019 年 3 月 3 日

* Get-AzureADPasswordProtectionProxy and Get-AzureADPasswordProtectionDCAgent 现在会报告软件版本和当前的 Azure 租户，但有以下限制：
  * 软件版本和 Azure 租户数据仅适用于运行版本1.2.116.0 或更高版本的 DC 代理和代理程序。
  * 在重新注册（或续订）代理或林之前，可能不会报告 Azure 租户数据。
* 代理服务现在要求安装 .NET 4.7。
  * 如果尚未安装 .NET 4.7，请下载并运行在[适用于 Windows 的 .NET Framework 4.7 脱机安装程序](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)中找到的安装程序。
  * 在服务器核心系统上，可能需要将 /q 标记传递到 .NET 4.7 安装程序以使其成功。
* 代理服务现在支持自动升级。 自动升级使用与代理服务并行安装的 Microsoft Azure AD Connect 代理更新程序服务。 自动升级默认已启用。
* 可以使用 Set-AzureADPasswordProtectionProxyConfiguration 启用或禁用自动升级。 可以使用 Get-AzureADPasswordProtectionProxyConfiguration 查询当前设置。
* DC 代理服务的服务二进制文件已重命名为 AzureADPasswordProtectionDCAgent.exe。
* 该代理服务的服务二进制文件已重命名为 AzureADPasswordProtectionProxy.exe。 如果使用第三方防火墙，可能需要相应地修改防火墙规则。
  * 注意：如果在以前的代理安装中使用 http 代理配置文件，则需要在此次升级后将其从 proxyservice.exe.config 重命名为 AzureADPasswordProtectionProxy.exe.config。
* 所有受时间限制的功能检查已从 DC 代理中删除。
* 小 bug 修复和日志记录改进。

## <a name="12650"></a>1.2.65.0

发布日期：2019 年 2 月 1 日

更改：

* Server Core 现在支持 DC 代理和代理服务。 与之前相比，操作系统最低要求保持不变：Windows Server 2012（对于 DC 代理）和 Windows Server 2012 R2 （对于代理程序）。
* Register-AzureADPasswordProtectionProxy 和 Register-AzureADPasswordProtectionForest cmdlet 现在支持基于设备代码的 Azure 身份验证模式。
* Get-AzureADPasswordProtectionDCAgent cmdlet 会忽略损坏和/或无效的服务连接点。 此次更改修复了域控制器有时会在输出中多次显示的 bug。
* Get-AzureADPasswordProtectionSummaryReport cmdlet 会忽略损坏和/或无效的服务连接点。 此次更改修复了域控制器有时会在输出中多次显示的 bug。
* 代理 PowerShell 模块现在是通过 %ProgramFiles%\WindowsPowerShell\Modules 注册。 计算机的 PSModulePath 环境变量不再遭修改。
* 新增了 Get-AzureADPasswordProtectionProxy cmdlet，有助于发现林或域中的已注册代理。
* DC 代理使用 sysvol 共享中的新文件夹来复制密码策略和其他文件。

   旧文件夹位置：

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   新文件夹位置：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   （此更改是为了避免误报的“孤立 GPO”警告。）

   > [!NOTE]
   > 旧文件夹和新文件夹之间不会迁移或共享任何数据。 旧版 DC 代理将继续使用旧位置，除非升级到此版本或更高版本。 当所有 DC 代理运行版本 1.2.65.0 或更高版本后，可以手动删除旧 sysvol 文件夹。

* DC 代理和代理服务现在会检测并删除各自服务连接点的受损副本。
* 每个 DC 代理会定期删除自己域中的受损和过时服务连接点，即 DC 代理和代理服务连接点。 如果检测信号时间戳已过去 7 天，DC 代理和代理服务连接点被视为过时。
* DC 代理现在会根据需要续订林证书。
* 代理服务现在会根据需要续订代理证书。
* 更新密码验证算法：在验证密码前，结合使用全局禁止密码列表和客户专用禁止密码列表（若已配置的话）。 如果给定密码包含全局禁止密码列表和客户专用禁止密码列表中的令牌，现在可能会遭拒（失败或仅审核）。 事件日志文档已更新，其中反映了这一点；请参阅[监视 Azure AD 密码保护](howto-password-ban-bad-on-premises-monitor.md)。
* 性能和可靠性修复
* 改进了日志记录

> [!WARNING]
> 限时功能：此版本 (1.2.65.0) 中的 DC 代理服务将自 2019 年 9 月 1 日起停止处理密码验证请求。  旧版本（见以下列表）中的 DC 代理服务将自 2019 年 7 月 1 日起停止处理。 所有版本中的 DC 代理服务将在截止日期前提前两个月将 10021 个事件记录到管理事件日志中。 即将推出的 GA 版本中将删除所有时间限制。 代理服务在所有版本中都不限时，但仍应升级到最新版本，以便充分利用所有后续 bug 修复和其他改进。

## <a name="12250"></a>1.2.25.0

发布日期：2018 年 11 月 1 日

修复项：

* DC 代理和代理服务应该不再因证书信任失败而失败。
* DC 代理和代理服务为符合 FIPS 标准的计算机提供了修补程序。
* 代理服务现在可以在只支持 TLS 1.2 的网络环境中正常工作。
* 进行了微小的性能和稳定性修复
* 改进了日志记录

更改：

* 代理服务所需的最低 OS 级别现在是 Windows Server 2012 R2。 DC 代理服务所需的最低 OS 级别仍然是 Windows Server 2012。
* 代理服务现在需要 .NET 版本 4.6.2。
* 密码验证算法使用扩展的字符规范化表。 此次更改可能导致在以前版本中接受的密码被拒绝。

## <a name="12100"></a>1.2.10.0

发布日期：2018 年 8 月 17 日

修复项：

* Register-AzureADPasswordProtectionProxy 和 Register-AzureADPasswordProtectionForest 现在支持多重身份验证
* Register-AzureADPasswordProtectionProxy 要求域中具有 WS2012 或更高版本的域控制器以避免加密错误。
* DC 代理服务在启动时可以更可靠地从 Azure 请求新的密码策略。
* 如果需要，DC 代理服务将每隔一小时从 Azure 请求新的密码策略，但现在将按随机选择的开始时间执行此操作。
* 如果在服务器提升为副本之前已安装于服务器上，DC 代理服务在新 DC 播发中将不再会导致无限期延迟。
* DC 代理服务现在将遵循“在 Windows Server Active Directory 上启用密码保护”配置设置
* 升级到将来的版本时，DC 代理和代理服务器安装程序现在都支持就地升级。

> [!WARNING]
> 从版本 1.1.10.3 进行就地升级不受支持并且会导致安装错误。 若要升级到版本 1.2.10 或更高版本，必须先完全卸载 DC 代理和代理服务器服务软件，然后再从头安装新版本。 必须重新注册 Azure AD 密码保护代理服务器服务。  不需要重新注册林。

> [!NOTE]
> DC 代理软件的就地升级将需要重启。

* DC 代理和代理服务器服务现在支持在配置为仅使用符合 FIPS 标准的算法的服务器上运行。
* 进行了微小的性能和稳定性修复
* 改进了日志记录

## <a name="11103"></a>1.1.10.3

发布日期：2018 年 6 月 15 日

初始的公开预览版

## <a name="next-steps"></a>后续步骤

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
