---
title: Azure AD Connect 云同步故障排除
description: 本文介绍如何排查云预配代理可能出现的问题。
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 174ec8c42ea17ccae04769d7c0baaa91b8e7025b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517865"
---
# <a name="cloud-sync-troubleshooting"></a>云同步故障排除

云同步涉及许多不同的方面，并具有许多不同的依赖项。 这种广泛的作用域会引起各种问题。 本文将帮助用户解决这些问题。 它介绍了需要重点关注的典型领域、如何收集其他信息，以及可以用来跟踪问题的各种技术。


## <a name="common-troubleshooting-areas"></a>常见故障排除领域

|名称|说明|
|-----|-----|
|[代理问题](#agent-problems)|验证是否已正确安装代理且该代理与 Azure Active Directory (Azure AD) 通信。|
|[对象同步问题](#object-synchronization-problems)|使用预配日志排查对象同步问题。|
|[预配隔离问题](#provisioning-quarantined-problems)|了解预配隔离问题以及如何修复这些问题。|


## <a name="agent-problems"></a>代理问题
想要使用代理验证的一些首要事项是：

-  是否安装了？
-  代理是否在本地运行？
-  代理是否在门户中？
-  代理是否标记为正常？

可以在 Azure 门户中以及在运行该代理的本地服务器上验证这些项。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证

若要验证代理是否能被 Azure 识别且正常，请执行以下步骤。

1. 登录到 Azure 门户。
1. 在左侧选择“Azure Active Directory” > “Azure AD Connect”。 在中心位置选择“管理同步”。
1. 在“Azure AD Connect 云同步”屏幕上，选择“查看所有代理”。

   ![查看所有代理](media/how-to-install/install-7.png)</br>
 
1. 在“本地预配代理”屏幕上，将看到已安装的代理。 验证相关的代理是否在该屏幕上，并且标记为“正常”。

   ![“本地预配代理”屏幕](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>验证端口

验证 Azure 是否正在侦听端口 443，以及代理是否可与其通信。 

此测试验证代理是否可以通过端口 443 与 Azure 通信。 打开浏览器，然后通过安装有代理的服务器转到上一个 URL。

![验证端口可访问性](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>在本地服务器上

若要验证代理是否正在运行，请执行以下步骤。

1. 在安装有代理的服务器上，导航到“服务”或者转到“开始” > “运行” > “Services.msc”来打开“服务”。
1. 确保“Microsoft Azure AD Connect 代理更新程序”和“Microsoft Azure AD Connect 预配代理”包含在“服务”中，并且其状态为“正在运行”。

   ![“服务”屏幕](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>常见代理安装问题

以下部分介绍了一些常见代理安装问题和典型解决方案。

#### <a name="agent-failed-to-start"></a>代理无法启动

你可能会收到以下错误消息：

“Microsoft Azure AD Connect 预配代理”服务无法启动。请确保有足够的权限启动系统服务。 

出现此问题的原因通常是组策略阻止将权限应用于安装程序创建的本地 NT 服务登录帐户 (NT SERVICE\AADConnectProvisioningAgent)。 这些权限是启动服务所必需的。

若要解决该问题，请执行以下步骤。

1. 使用管理员帐户登录到服务器。
1. 导航到“服务”或者转到“开始” > “运行” > “Services.msc”来打开“服务”。
1. 在“服务”下，双击“Microsoft Azure AD Connect 预配代理”。
1. 在“登录”选项卡上，将此帐户更改为域管理员。然后重启服务。 

   ![“登录”选项卡](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>代理超时或证书无效

尝试注册代理时，可能会收到以下错误消息。

![超时错误消息](media/how-to-troubleshoot/troubleshoot-4.png)

此问题通常是由于代理无法连接到混合标识服务所导致，并且需要你配置 HTTP 代理。 若要解决此问题，请配置出站代理。 

预配代理支持使用出站代理。 可通过编辑代理配置文件“C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config”对其进行配置。在文件的末尾添加以下行，恰好添加在尾随的 `</configuration>` 标记前面。
将变量 `[proxy-server]` 和 `[proxy-port]` 替换为代理服务器名称和端口值。

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>代理注册失败，出现安全错误

安装云预配代理时可能会收到错误消息。

出现此问题的原因通常是由于本地 PowerShell 执行策略，代理无法执行 PowerShell 注册脚本。

若要解决此问题，请更改服务器上的 PowerShell 执行策略。 需要将“计算机和用户策略”设置为“Undefined”或“RemoteSigned”。 如果设置为“Unrestricted”，则会看到此错误。 有关详细信息，请参阅 [PowerShell 执行策略](/powershell/module/microsoft.powershell.core/about/about_execution_policies)。 

### <a name="log-files"></a>日志文件

默认情况下，代理发出最少的错误消息和堆栈跟踪信息。 可以在 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 文件夹中找到这些跟踪日志。

若要收集用于排查代理相关问题的其他详细信息，请执行以下步骤。

1.  安装 AADCloudSyncTools PowerShell 模块，如[此处](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)所述。
2. 使用 `Export-AADCloudSyncToolsLogs` PowerShell cmdlet 捕获信息。  可以使用以下开关来微调数据收集。
      - SkipVerboseTrace 用于仅导出当前日志而不捕获详细日志（默认值 = false）
      - TracingDurationMins 用于指定不同的捕获持续时间（默认值 = 3 分钟）
      - OutputPath 用于指定不同的输出路径（默认值 = 用户的文档）


## <a name="object-synchronization-problems"></a>对象同步问题

以下部分包含有关如何对对象同步进行故障排除的信息。

### <a name="provisioning-logs"></a>“预配”日志

在 Azure 门户中，预配日志可用于帮助跟踪对象同步问题并进行故障排除。 若要查看日志，请选择“日志”。

![“日志”按钮](media/how-to-troubleshoot/log-1.png)

预配日志提供了有关在本地 Active Directory 环境与 Azure 之间发生同步的对象的状态的详细信息。

![“预配日志”屏幕](media/how-to-troubleshoot/log-2.png)

可以使用页面顶部的下拉框在特定问题（如日期）中将视图筛选为零。 双击单个事件可查看其他信息。

![“预配日志”下拉框信息](media/how-to-troubleshoot/log-3.png)

此信息提供了详细步骤以及出现同步问题的位置。 通过这种方式，可以查明问题的确切位置。


## <a name="provisioning-quarantined-problems"></a>预配隔离问题

云同步监视配置的运行状况，并将不正常的对象置于隔离状态。 如果某个错误（例如管理员凭据无效）导致针对目标系统发出的大部分或所有调用持续失败，则同步作业将标记为“处于隔离状态”。

![隔离状态](media/how-to-troubleshoot/quarantine-1.png)

通过选择该状态，可以看到有关隔离的其他信息。 还可以获取错误代码和消息。

![显示有关隔离的其他信息的屏幕截图。](media/how-to-troubleshoot/quarantine-2.png)

右键单击该状态将显示其他选项：
    
   - 查看预配日志
   - 查看代理
   - 清除隔离

![显示“右键单击”菜单选项的屏幕截图。](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>解析隔离
解析隔离有两种不同方式。  它们是：

  - 清除隔离 - 清除水印并运行增量同步
  - 重新启动预配作业 - 清除水印并运行初始同步

#### <a name="clear-quarantine"></a>清除隔离
若要清除水印并在验证后对预配作业运行增量同步，只需右键单击状态，然后选择“清除隔离”。

应会看到正在清除隔离的通知。

![显示正在清除隔离的通知的屏幕截图。](media/how-to-troubleshoot/quarantine-5.png)

然后，应会看到代理上的状态为“正常”。

![隔离状态信息](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>重新启动预配作业
使用 Azure 门户重新启动预配作业。 在“代理配置”页上，选择“重新启动预配”。

  ![重新启动预配](media/how-to-troubleshoot/quarantine-3.png)

- 使用 Microsoft Graph [重新启动预配作业](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)。 你可以完全控制需要重新启动的内容。 你可以选择清除：
  - 托管，用于重新启动增加为隔离状态的托管计数器。
  - 隔离，用于从隔离区中删除应用程序。
  - 水印。 
  
  使用以下请求：
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>修复云同步服务帐户
如果需要修复云同步服务帐户，可以使用 `Repair-AADCloudSyncToolsAccount`。  


   1.  使用[此处](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)所述的安装步骤以开始，然后继续执行剩余的步骤。
   2.  从具有管理权限的 Windows PowerShell 会话中，键入或复制并粘贴以下内容： 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. 输入 Azure AD 全局管理员凭据。
   4. 键入或复制并粘贴以下内容： 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. 完成此操作后，应显示该帐户已成功修复。

## <a name="next-steps"></a>后续步骤 

- [已知的限制](how-to-prerequisites.md#known-limitations)
- [错误代码](reference-error-codes.md)
