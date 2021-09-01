---
title: 在 Azure Active Directory 中安装 Connect Health 代理
description: 本 Azure AD Connect Health 文章介绍了 Active Directory 联合身份验证服务 (AD FS) 和同步的代理安装。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31dd21a7c525bd1e5dc659b611f72c2c33c6f043
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725779"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health 代理安装

在本文中，你将了解如何安装和配置 Azure Active Directory (Azure AD) Connect Health 代理。 若要下载代理，请参阅[这些说明](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)。

## <a name="requirements"></a>要求

下表列出使用 Azure AD Connect Health 的要求。

| 要求 | 说明 |
| --- | --- |
| 拥有 Azure AD Premium（P1 或 P2）订阅。  |Azure AD Connect Health 是 Azure AD Premium（P1 或 P2）的一项功能。 有关详细信息，请参阅[注册 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)。 <br /><br />若要开始 30 天免费试用，请参阅[开始试用](https://azure.microsoft.com/trial/get-started-active-directory/)。 |
| 你是 Azure AD 中的全局管理员。 |默认情况下，只有全局管理员才能安装和配置运行状况代理、访问门户，以及在 Azure AD Connect Health 中执行任何操作。 有关详细信息，请参阅 [Administering your Azure AD directory](../fundamentals/active-directory-whatis.md)（管理 Azure AD 目录）。 <br /><br /> 利用基于 Azure 角色的访问控制 (Azure RBAC)，你可以让组织中的其他用户访问 Azure AD Connect Health。 有关详细信息，请参阅[适用于 Azure AD Connect Health 的 Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)。 <br /><br />**重要说明**：使用工作或学校帐户安装代理。 无法使用 Microsoft 帐户。 有关详细信息，请参阅[以组织的身份注册 Azure](../fundamentals/sign-up-organization.md)。 |
| Azure AD Connect Health 代理已安装在每台目标服务器上。 | 必须在目标服务器上安装和配置 Health 代理，以便他们能够接收数据并提供监视和分析功能。 <br /><br />例如，若要从 Active Directory 联合身份验证服务 (AD FS) 基础结构获取数据，必须在 AD FS 服务器和 Web 应用程序代理服务器上安装代理。 同样，若要从本地 Azure AD 域服务 (Azure AD DS) 基础结构获取数据，必须在域控制器上安装代理。  |
| Azure 服务终结点具有出站连接。 | 在安装期间和运行时，代理需要连接到 Azure AD Connect Health 服务终结点。 如果防火墙阻止出站连接，请将[出站连接终结点](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)添加到允许列表中。 |
|出站连接基于 IP 地址。 | 有关基于 IP 地址的防火墙筛选的信息，请参阅 [Azure IP 范围](https://www.microsoft.com/download/details.aspx?id=56519)。|
| 已为出站流量筛选或禁用 TLS 检查。 | 如果在网络层针对出站流量设置了 TLS 检查或终止，则代理注册步骤或数据加载操作可能会失败。 有关详细信息，请参阅[设置 TLS 检查](/previous-versions/tn-archive/ee796230(v=technet.10))。 |
| 服务器上的防火墙端口正在运行代理。 |代理要求打开以下防火墙端口，以便代理可以与 Azure AD Connect Health 服务终结点通信： <br /><li>TCP 端口 443</li><li>TCP 端口 5671</li> <br />最新版本的代理不需要端口 5671。 升级到最新版本，以便只需要端口 443。 有关详细信息，请参阅[混合标识所需的端口和协议](./reference-connect-ports.md)。 |
| 如果启用了 Internet Explorer 增强的安全性，则允许指定的网站。  |如果启用了 Internet Explorer 增强的安全性，则允许在安装代理的服务器上访问以下网站：<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https:\//aadcdn.msftauth.net</li><li>受 Azure AD 信任的组织的联合服务器（例如 https:\//sts.contoso.com）</li> <br />有关详细信息，请参阅[如何配置 Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)。 如果网络中有代理，请参阅此表末尾的注释。|
| 已安装 PowerShell 版本 5.0 或更高版本。 | Windows Server 2016 包括 PowerShell 版本 5.0。 
|已禁用 FIPS（美国联邦信息处理标准）。|Azure AD Connect Health 代理不支持 FIPS。|

> [!IMPORTANT]
> Windows Server Core 不支持安装 Azure AD Connect Health 代理。


> [!NOTE]
> 如果有一个高度锁定和限制的环境，你需要添加的 URL 比该表为 Internet Explorer 增强的安全性列出的 URL 更多。 还要添加下一部分的表中列出的 URL。  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure 服务终结点的出站连接

在安装期间和运行时，代理需要连接到 Azure AD Connect Health 服务终结点。 如果防火墙阻止出站连接，请确保以下表中的 URL 默认情况下不会被阻止。 

不要禁用这些 URL 的安全监视或检查。 相反，像允许其他 Internet 流量一样允许它们。 

这些 URL 允许与 Azure AD Connect Health 服务终结点通信。 在本文的后面部分中，你讲了解如何使用 `Test-AzureADConnectHealthConnectivity` [检查出站连接](#test-connectivity-to-azure-ad-connect-health-service)。

| 域环境 | 所需的 Azure 服务终结点 |
| --- | --- |
| 一般公众 | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net - 端口：5671（此终结点在代理的最新版本中不是必需的。）</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com（此终结点仅用于在注册过程中进行相关发现。）</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure 德国 | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de（此终结点仅用于在注册过程中进行相关发现。）</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com（此终结点仅用于在注册过程中进行相关发现。）</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>安装代理

下载并安装 Azure AD Connect Health 代理： 

* 请务必[满足 Azure AD Connect Health 的要求](how-to-connect-health-agent-install.md#requirements)。
* 适用于 AD FS 的 Azure AD Connect Health 使用入门：
    * [下载适用于 AD FS 的 Azure AD Connect Health 代理](https://go.microsoft.com/fwlink/?LinkID=518973)。
    * 查看[安装说明](#install-the-agent-for-ad-fs)。
* 适用于同步的 Azure AD Connect Health 使用入门：
    * [下载并安装最新版 Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771)。 在安装 Azure AD Connect 的过程中，会安装适用于同步的 Health 代理（1.0.9125.0 或更高版本）。
* 适用于 Azure AD DS 的 Azure AD Connect Health 使用入门：
    * [下载适用于 Azure AD DS 的 Azure AD Connect Health 代理](https://go.microsoft.com/fwlink/?LinkID=820540)。
    * 查看[安装说明](#install-the-agent-for-azure-ad-ds)。

## <a name="install-the-agent-for-ad-fs"></a>为 AD FS 安装代理

> [!NOTE]
> AD FS 服务器应与同步服务器不同。 请勿在同步服务器上安装 AD FS 代理。
>

在安装代理之前，请确保 AD FS 服务器主机名是唯一的，并且未存在于 AD FS 服务中。
若要启动代理安装，请双击下载的 .exe 文件。 在第一个窗口中，选择“安装”。

![该屏幕截图显示了 Azure AD Connect Health AD FS 代理的安装窗口。](./media/how-to-connect-health-agent-install/install1.png)

安装完成后，选择“立即配置”。

![该屏幕截图显示了 Azure AD Connect Health AD FS 代理安装的确认消息。](./media/how-to-connect-health-agent-install/install2.png)

此时将打开 PowerShell 窗口以启动代理注册过程。 出现提示时，请使用具有注册代理权限的 Azure AD 帐户登录。 默认情况下，全局管理员帐户具有权限。

![该屏幕截图显示了 Azure AD Connect Health AD FS 的登录窗口。](./media/how-to-connect-health-agent-install/install3.png)

登录后，PowerShell 将继续运行。 完成后，可以关闭 PowerShell。 配置已完成。

此时，代理服务应自动启动，以允许代理安全地将所需的数据上传到云服务。

如果你尚未满足所有先决条件，PowerShell 窗口中会出现警告。 请务必在安装代理之前满足[要求](how-to-connect-health-agent-install.md#requirements)。 以下屏幕截图显示了这些警告的示例。

![该屏幕截图显示了 Azure AD Connect Health AD FS 配置脚本。](./media/how-to-connect-health-agent-install/install4.png)

若要验证代理是否已安装，请在服务器上查找以下服务。 如果已完成配置，这些服务应已运行。 否则，它们会停止，直到完成配置。

* Azure AD Connect Health AD FS Diagnostics 服务
* Azure AD Connect Health AD FS Insights 服务
* Azure AD Connect Health AD FS Monitoring 服务

![该屏幕截图显示了 Azure AD Connect Health AD FS 服务。](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>为 AD FS 启用审核

> [!NOTE]
> 本部分仅适用于 AD FS 服务器。 不需对 Web 应用程序代理服务器执行以下步骤。
>

使用情况分析功能需要收集和分析数据。 因此，Azure AD Connect Health 代理需要 AD FS 审核日志中的信息。 默认情况下未启用这些日志。 使用以下过程在 AD FS 服务器上启用 AD FS 审核和查找 AD FS 审核日志。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>在 Windows Server 2012 R2 上启用 AD FS 审核的步骤

1. 在“开始”屏幕上，打开“服务器管理器”，然后打开“本地安全策略” 。 或者在任务栏上，打开“服务器管理器”，然后选择“工具/本地安全策略” 。
2. 转到“Security Settings\Local Policies\User Rights Assignment”文件夹。 然后双击“生成安全审核”。
3. 在 **“本地安全设置”** 选项卡上，验证是否列出了 AD FS 服务帐户。 如果未列出，请选择“添加用户或组”，并将其添加到列表。 然后选择“确定”。 
4. 若要启用审核，请使用提升权限打开“命令提示符”窗口。 然后，运行以下命令： 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. 关闭“本地安全策略”。
    >[!Important]
    >仅主 AD FS 服务器需要执行以下步骤。 
1. 打开“AD FS 管理”管理单元。 （在“服务器管理器”中，选择“工具” > “AD FS 管理”  。）
1. 在“操作”窗格中，选择“编辑联合身份验证服务属性” 。
1. 在“联合身份验证服务属性”对话框中，选择“事件”选项卡 。
1. 选择“成功审核”和“失败审核”复选框，并选择“确定” 。
1. 若要通过 PowerShell 启用详细日志记录，请使用以下命令： 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>在 Windows Server 2016 上针对 AD FS 启用审核

1. 在“开始”屏幕上，打开“服务器管理器”，然后打开“本地安全策略” 。 或者在任务栏上，打开“服务器管理器”，然后选择“工具/本地安全策略” 。
2. 转到“Security Settings\Local Policies\User Rights Assignment”文件夹，并双击“生成安全审核”。
3. 在 **“本地安全设置”** 选项卡上，验证是否列出了 AD FS 服务帐户。 如果未列出，请选择“添加用户或组”，然后将 AD FS 服务帐户添加到列表。 然后选择“确定”。 
4. 若要启用审核，请使用提升权限打开“命令提示符”窗口。 然后，运行以下命令： 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. 关闭“本地安全策略”。
    >[!Important]
    >仅主 AD FS 服务器需要执行以下步骤。
1. 打开“AD FS 管理”管理单元。 （在“服务器管理器”中，选择“工具” > “AD FS 管理”  。）
1. 在“操作”窗格中，选择“编辑联合身份验证服务属性” 。
1. 在“联合身份验证服务属性”对话框中，选择“事件”选项卡 。
1. 选择“成功审核”和“失败审核”复选框，并选择“确定” 。 默认情况下应启用成功审核和失败审核。
1. 打开 PowerShell 窗口并运行以下命令： 

    `Set-AdfsProperties -AuditLevel Verbose`

默认情况下启用“基本”审核级别。 有关详细信息，请参阅 [Windows Server 2016 中的 AD FS 审核增强功能](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)。


#### <a name="to-locate-the-ad-fs-audit-logs"></a>查找 AD FS 审核日志的步骤

1. 打开“**事件查看器**”。
2. 转到“Window 日志”，然后选择“安全” 。
3. 在右侧选择“筛选当前日志”。
4. 对于“事件源”，请选择“AD FS 审核” 。

    有关审核日志的详细信息，请参阅[操作问题](/azure/active-directory/hybrid/reference-connect-health-faq#operations-questions)。

    ![该屏幕截图显示了“筛选当前日志”窗口。 在“事件源”字段，已选中“AD FS 审核”。](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> 组策略可以禁用 AD FS 审核。 如果禁用了 AD FS 审核，则不能使用有关登录活动的使用情况分析。 请确保没有组策略禁用 AD FS 审核。
>


## <a name="install-the-agent-for-sync"></a>安装用于同步的代理

最新版本的 Azure AD Connect 中会自动安装用于同步的 Azure AD Connect Health 代理。 若要使用适用于同步的 Azure AD Connect，请[下载最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 并进行安装。

若要验证代理是否已安装，请在服务器上查看以下服务。 如果已完成配置，这些服务应已运行。 否则，这些服务会停止，直到完成配置。

* Azure AD Connect Health Sync Insights 服务
* Azure AD Connect Health Sync 监视服务

![该屏幕截图显示了服务器上正在运行的适用于同步的 Azure AD Connect Health 服务。](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> 记住，必须有 Azure AD Premium（P1 或 P2）才能使用 Azure AD Connect Health。 如果没有 Azure AD Premium，则无法在 Azure 门户中完成配置。 有关详细信息，请参阅[要求](how-to-connect-health-agent-install.md#requirements)。
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>手动注册用于同步的 Azure AD Connect Health

如果用于同步的 Azure AD Connect Health 代理在成功安装 Azure AD Connect 后注册失败，可以使用 PowerShell 命令来手动注册代理。

> [!IMPORTANT]
> 仅当在安装 Azure AD Connect 后代理注册失败时，才使用此 PowerShell 命令。
>
>

使用以下 PowerShell 命令，手动注册用于同步的 Azure AD Connect Health 代理。 成功注册代理之后，Azure AD Connect Health 服务会启动。

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $true -StagingMode $false`

此命令采用以下参数：

* **AttributeFiltering**：`$true`（默认值）如果 Azure AD Connect 未同步默认特性集，且已自定义为使用筛选的属性集。 否则使用 `$false`。
* **StagingMode**：`$false`（默认值）如果 Azure AD Connect 服务器未处于暂存模式。 如果将服务器配置为处于暂存模式，请使用 `$true`。

当系统提示进行身份验证时，请使用用于配置 Azure AD Connect 的同一全局管理员帐户（例如 admin@domain.onmicrosoft.com）。

## <a name="install-the-agent-for-azure-ad-ds"></a>为 Azure AD DS 安装代理

若要启动代理安装，请双击下载的 .exe 文件。 在第一个窗口中，选择“安装”。

![该屏幕截图显示了适用于 AD DS 的 Azure AD Connect Health 代理安装窗口。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

安装完成后，选择“立即配置”。

![该屏幕截图显示了完成适用于 Azure AD DS 的 Azure AD Connect Health 代理安装的窗口。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

命令提示符窗口将打开。 PowerShell 运行 `Register-AzureADConnectHealthADDSAgent`。 根据提示登录到 Azure。

![该屏幕截图显示了适用于 Azure AD DS 的 Azure AD Connect Health 代理的登录窗口。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

登录后，PowerShell 将继续运行。 完成后，可以关闭 PowerShell。 配置已完成。

此时，服务应会自动启动，使代理能够监视和收集数据。 如果不满足前面部分中所述的所有先决条件，则 PowerShell 窗口中会出现警告。 请务必在安装代理之前满足[要求](how-to-connect-health-agent-install.md#requirements)。 以下屏幕截图显示了这些警告的示例。

![该屏幕截图显示了适用于 Azure AD DS 的 Azure AD Connect Health 代理配置的警告。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

若要验证代理是否已安装，请在域控制器上查看以下服务：

* Azure AD Connect Health AD DS Insights 服务
* Azure AD Connect Health AD DS 监视服务

如果已完成配置，这些服务应已运行。 否则，服务会停止，直到完成配置。

![该屏幕截图显示了正在域控制器上运行的服务。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>在多个服务器上快速安装代理

1. 在 Azure AD 中创建用户帐户。 使用密码对其进行保护。
2. 使用门户在 Azure AD Connect Health 中为此本地 Azure AD 帐户分配“所有者”角色。 请执行[这些步骤](how-to-connect-health-operations.md#manage-access-with-azure-rbac)。 将角色分配给所有服务实例。 
3. 下载本地域控制器中的 .exe MSI 文件用于安装。
4. 运行以下脚本。 将参数替换为新用户帐户及其密码。 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

完成后，可以通过执行以下一项或多项任务来删除本地帐户的访问： 
* 删除 Azure AD Connect Health 的本地帐户的角色分配。
* 轮替本地帐户的密码。 
* 禁用 Azure AD 本地帐户。
* 删除 Azure AD 本地帐户。  

## <a name="register-the-agent-by-using-powershell"></a>使用 PowerShell 注册代理

安装相应的代理 setup.exe 文件后，可以使用以下 PowerShell 命令来注册代理，具体取决于角色。 打开 PowerShell 窗口并运行相应的命令：

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> 若要对主权云进行注册，请使用以下命令行：
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


这些命令接受 `Credential` 作为参数，来以非交互式方式完成注册或在运行服务器核心的机器上完成注册。 请记住：
* 可以在作为参数传递的 PowerShell 变量中捕获 `Credential`。
* 可以提供任何 Azure AD 标识，该标识具有注册代理的权限，并且未启用多重身份验证。
* 默认情况下，全局管理员具有注册代理的权限。 还可以允许特权较小的标识执行此步骤。 有关详细信息，请参阅 [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)。

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>将 Azure AD Connect Health 代理配置为使用 HTTP 代理

可以将 Azure AD Connect Health 代理配置为使用 HTTP 代理。

> [!NOTE]
> * 不支持 `Netsh WinHttp set ProxyServerAddress`。 代理使用 System.Net 而不是 Windows HTTP 服务发出 Web 请求。
> * 配置的 HTTP 代理地址用于传递加密的 HTTP 消息。
> * 不支持经过身份验证的代理（使用 HTTPBasic）。
>
>

### <a name="change-the-agent-proxy-configuration"></a>更改代理程序配置

若要将 Azure AD Connect Health 代理配置为使用 HTTP 代理，你可以：
* 导入现有的代理设置。
* 手动指定代理地址。
* 清除现有的代理配置。

> [!NOTE]
> 若要更新代理设置，必须重启所有 Azure AD Connect Health 代理服务。 运行以下命令：
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>导入现有的代理设置

可以导入 Internet Explorer HTTP 代理设置，以便 Azure AD Connect Health 代理可以使用这些设置。 在运行 Health 代理的每台服务器上运行以下 PowerShell 命令：

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

可以导入 WinHTTP 代理设置，以便 Azure AD Connect Health 代理可以使用它们。 在运行 Health 代理的每台服务器上运行以下 PowerShell 命令：

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>手动指定代理地址

可以手动指定代理服务器。 在运行 Health 代理的每台服务器上运行以下 PowerShell 命令：

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

下面是一个示例： 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

在本示例中：
* `address` 设置可以是 DNS 可解析的服务器名称或 IPv4 地址。
* 可以省略 `port`。 如果这样做，443 将为默认端口。

#### <a name="clear-the-existing-proxy-configuration"></a>清除现有的代理配置

可以运行以下命令清除现有的代理配置：

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>读取当前的代理设置

可以运行以下命令读取当前代理设置：

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>测试与 Azure AD Connect Health 服务的连接

有时，Azure AD Connect Health 代理会与 Azure AD Connect Health 服务断开连接。 此连接断开的原因可能包括网络问题、权限问题和各种其他问题。

如果代理无法将数据发送到 Azure AD Connect Health 服务达 2 小时以上，门户中会出现以下警报：“Health 服务数据不是最新的”。 

可以运行以下 PowerShell 命令，确认受影响的 Azure AD Connect Health 代理是否能够将数据上传到 Azure AD Connect Health 服务：

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

role 参数目前可接受以下值：

* ADFS
* 同步
* ADDS

> [!NOTE]
> 若要使用连接工具，必须先完成代理注册。 如果无法完成代理注册，请确保符合 Azure AD Connect Health 的所有[要求](how-to-connect-health-agent-install.md#requirements)。 默认情况下，在代理注册过程中测试连接。
>
>

## <a name="next-steps"></a>后续步骤

请查看以下相关文章：

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health 操作](how-to-connect-health-operations.md)
* [在 AD FS 中使用 Azure AD Connect Health](how-to-connect-health-adfs.md)
* [使用用于同步的 Azure AD Connect Health](how-to-connect-health-sync.md)
* [结合使用 Azure AD Connect Health 和 Azure AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.yml)
* [Azure AD Connect Health 版本历史记录](reference-connect-health-version-history.md)