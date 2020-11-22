---
title: AADCloudSyncTools PowerShell 模块 Azure AD Connect 云同步
description: 本文介绍如何安装 Azure AD Connect 云预配代理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3ce5a7a313ab0f0680558aa60b34e3ebb9b51c9
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255092"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>AADCloudSyncTools PowerShell 模块 Azure AD Connect 云同步

随着公共预览版刷新2的发布，Microsoft 引入了 AADCloudSyncTools PowerShell 模块。  此模块提供一组有用的工具，可用于帮助管理 Azure AD Connect 云同步部署。

## <a name="pre-requisites"></a>先决条件
需要以下先决条件：
- 此模块使用 MSAL authentication，因此需要安装 MSAL.PS 模块。 它不再依赖于 Azure AD 或 Azure AD 预览。   若要验证，请在管理 PowerShell 窗口中执行 `Get-module MSAL.PS` 。 如果正确安装了模块，则会收到响应。  你可以使用 `Install-AADCloudSyncToolsPrerequisites` 来安装最新版本的 MSAL.PS
- AzureAD PowerShell 模块。  某些 cmdlet 依赖于 AzureAD PowerShell 模块的各个部分来完成其任务。  若要验证，请在管理 PowerShell 窗口中执行 `Get-module AzureAD` 。 你应收到响应。  你可以使用 `Install-AADCloudSyncToolsPrerequisites` 来安装最新版本的 AzureAD PowerShell 模块。
- 从 PowerShell 安装模块可能会强制使用 TLS 1.2。  若要确保可以安装模块，请设置以下各项： \
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 `

## <a name="install-the-aadcloudsynctools-powershell-module"></a>安装 AADCloudSyncTools PowerShell 模块
若要安装和使用 AADCloudSyncTools 模块，请使用以下步骤：

1.  用管理权限打开 Windows PowerShell
2.  键入 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` ，然后按 enter。
3.  键入或复制并粘贴以下内容： 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
    ```
3.  按 Enter。
4.  若要验证模块是否已安装，请输入或复制并粘贴以下内容“
    ```powershell
    Get-module AADCloudSyncTools
    ```
5.  现在应会看到有关模块的信息。
6.  下次运行
    ``` powershell
    Install-AADCloudSyncToolsPrerequisites
    ```
7.  这将安装 PowerShell 获取模块。  关闭 PowerShell 窗口。
8.  用管理权限打开 Windows PowerShell
9.  再次使用步骤3导入模块。
10. 运行 `Install-AADCloudSyncToolsPrerequisites` 以安装 MSAL 和 AzureAD 模块
11. 所有预请求应该已成功安装 ![ 安装模块](media/reference-powershell/install-1.png)

## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools Cmdlet
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
使用 AzureAD 模块连接到 Azure AD，并使用 MSAL.PS 模块为请求标记 Microsoft Graph


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
导出并打包压缩文件中的所有疑难解答数据，如下所示：
 1. 使用 AADCloudSyncToolsVerboseLogs 启动详细跟踪。  可以在 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 文件夹中找到这些跟踪日志。
 2. 收集3分钟的跟踪日志。
   可以使用-TracingDurationMins 指定不同的时间，也可以使用-SkipVerboseTrace 来跳过详细跟踪
 3. 停止带有 Stop-AADCloudSyncToolsVerboseLogs 的详细跟踪
 4. 收集最近24小时内事件查看器日志
 5. 将所有代理日志、详细日志和事件查看器日志压缩为用户的 "文档" 文件夹下的压缩 zip 文件。 
 </br>可以使用-OutputPath 指定一个不同的输出文件夹。 \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
显示 Azure AD 租户详细信息和内部变量状态

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
使用关系图获取 AD2AAD 的服务主体并返回同步作业信息。
还可以使用特定的同步作业 ID 作为参数来调用。

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
使用关系图获取 AD2AAD 的服务主体并返回同步作业的计划。
还可以使用特定的同步作业 ID 作为参数来调用。

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
使用关系图获取 AD2AAD 的服务主体并返回同步作业的架构。

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
使用关系图获取所提供的同步作业 ID 的同步作业的架构，并输出所有筛选器组的作用域。

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
使用关系图获取 AD2AAD 的服务主体并返回同步作业的设置。
还可以使用特定的同步作业 ID 作为参数来调用。

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
使用关系图获取 AD2AAD 的服务主体并返回同步作业的状态。
还可以使用特定的同步作业 ID 作为参数来调用。

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
使用关系图获取 AD2AAD 和/或 SyncFabric 的服务主体 (s) 。
如果没有参数，将只返回) 的 AD2AAD 服务主体 (。

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
检查是否存在 PowerShellGet v 2.2.4.1 或更高版本以及 Azure AD 和 MSAL.PS 模块，如果缺少，则安装这些模块。

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
调用指定为参数的 URI、方法和正文的 Web 请求

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
使用 Azure AD PowerShell 删除当前帐户 (如果存在) ，并使用 Azure AD 中的新同步帐户重置同步帐户身份验证。

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
重新启动完全同步。

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
继续与先前水印的同步。

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
修改 "AADConnectProvisioningAgent.exe.config" 以启用详细跟踪，并重新启动 AADConnectProvisioningAgent 服务，您可以使用 SkipServiceRestart 来防止服务重新启动，但任何配置更改都不会生效。  可以在 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 文件夹中找到这些跟踪日志。

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
修改 "AADConnectProvisioningAgent.exe.config" 以禁用详细跟踪并重启 AADConnectProvisioningAgent 服务。 可以使用-SkipServiceRestart 来防止服务重新启动，但任何配置更改都不会生效。

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
暂停同步。

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)

