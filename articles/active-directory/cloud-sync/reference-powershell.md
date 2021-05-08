---
title: 用于 Azure AD Connect 云同步的 AADCloudSyncTools PowerShell 模块
description: 本文介绍如何安装 Azure AD Connect 云预配代理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa358b0c9d7747584deabe761160d3bcbcde8feb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99593174"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>用于 Azure AD Connect 云同步的 AADCloudSyncTools PowerShell 模块

AADCloudSyncTools 模块提供了一组有用的工具，可用于帮助管理 Azure AD Connect 云同步部署。

## <a name="pre-requisites"></a>先决条件
需要以下先决条件：

- 此模块的所有必备组件都可以使用 `Install-AADCloudSyncToolsPrerequisites` 来自动安装
- 此模块使用 MSAL 身份验证，因此需要安装 MSAL.PS 模块。 若要验证，请在 PowerShell 窗口中执行 `Get-module MSAL.PS -ListAvailable`。 如果正确安装了该模块，你会收到响应。 可以使用 `Install-AADCloudSyncToolsPrerequisites` 来安装最新版本的 MSAL.PS
- 虽然 AzureAD PowerShell 模块不是此模块的任何功能的先决条件，但有该模块也是很有用的，所以它也会通过使用 `Install-AADCloudSyncToolsPrerequisites` 来自动安装。
- 从 PowerShell 手动安装模块需要强制实施 TLS 1.2。 若要确保可以安装模块，请先在 PowerShell 会话中完成以下设置，然后再使用
  ```
   Install-Module:
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>安装 AADCloudSyncTools PowerShell 模块
若要安装和使用 AADCloudSyncTools 模块，请使用以下步骤：

1. 使用管理员权限打开 Windows PowerShell
2. 键入或复制并粘贴以下内容：`Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. 按 Enter。
4. 若要验证是否已导入该模块，请输入或复制并粘贴以下内容：`Get-module AADCloudSyncTools`
5. 现在应会看到有关模块的信息。
6. 接下来，若要安装 AADCloudSyncTools 模块必备组件，请运行：`Install-AADCloudSyncToolsPrerequisites`
7. 在首次运行时，将会安装 PoweShellGet 模块（如果尚不存在）。 若要加载新的 PowershellGet 模块，请关闭 PowerShell 窗口，并使用管理员权限打开新的 PowerShell 会话。 
8. 再次使用步骤 3 导入该模块。
9. 运行 `Install-AADCloudSyncToolsPrerequisites` 以安装 MSAL 和 AzureAD 模块
11. 所有预请求应该已成功安装![安装模块](media/reference-powershell/install-1.png)


## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools Cmdlet
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
使用 MSAL.PS 模块为 Azure AD 管理员访问 Microsoft Graph 请求令牌 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
导出所有故障排除数据并将它们打包到压缩文件中，如下所示：
 1. 使用 Start-AADCloudSyncToolsVerboseLogs 来启动详细跟踪。  可以在 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 文件夹中找到这些跟踪日志。
 2. 收集一个时长为 3 分钟的跟踪日志。
   可以使用 -TracingDurationMins 来指定不同的时间，或者可以使用 -SkipVerboseTrace 来跳过详细跟踪
 3. 使用 Stop-AADCloudSyncToolsVerboseLogs 来停止详细跟踪
 4. 收集过去 24 小时内的事件查看器日志
 5. 将所有的代理日志、详细日志和事件查看器日志都压缩到“用户的文档”文件夹下的压缩 zip 文件中。 
 </br>可以使用 -OutputPath \<folder path\> 来指定不同的输出文件夹

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
显示 Azure AD 租户详细信息和内部变量状态

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
使用 Graph 来获取 AD2AAD 服务主体并返回同步作业信息。
还可以使用特定的同步作业 ID 作为参数来调用。

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
使用 Graph 来获取 AD2AAD 服务主体并返回同步作业的计划。
还可以使用特定的同步作业 ID 作为参数来调用。

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
使用 Graph 来获取 AD2AAD 服务主体并返回同步作业的架构。

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
使用 Graph 来获取所提供的同步作业 ID 的同步作业的架构，并输出所有筛选器组的范围。

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
使用 Graph 来获取 AD2AAD 服务主体并返回同步作业的设置。
还可以使用特定的同步作业 ID 作为参数来调用。

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
使用 Graph 来获取 AD2AAD 服务主体并返回同步作业的状态。
还可以使用特定的同步作业 ID 作为参数来调用。

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
使用 Graph 来获取 AD2AAD 和/或 SyncFabric 的服务主体。
如果不带参数，将只返回 AD2AAD 服务主体。

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
检查是否存在 PowerShellGet v2.2.4.1 或更高版本以及 Azure AD 和 MSAL.PS 模块，并安装这些模块（如果缺少）。

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
为指定为参数的 URI、方法和正文调用 Web 请求

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
使用 Azure AD PowerShell 来删除当前帐户（如果存在），并使用 Azure AD 中的新同步帐户重置同步帐户身份验证。

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
重启一个完整的同步。

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
从前一个水印继续同步。

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
修改“AADConnectProvisioningAgent.exe.config”，以启用详细跟踪并重启 AADConnectProvisioningAgent 服务，你可以使用 -SkipServiceRestart 来防止服务重启，但任何配置更改都不会生效。  可以在 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 文件夹中找到这些跟踪日志。

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
修改“AADConnectProvisioningAgent.exe.config”，以禁用详细跟踪并重启 AADConnectProvisioningAgent 服务。 你可以使用 -SkipServiceRestart 来防止服务重启，但任何配置更改都不会生效。

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
暂停同步。

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)

