---
title: 部署本地 Azure AD 密码保护
description: 了解如何在本地 Active Directory 域服务环境中计划和部署 Azure AD 密码保护
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81e40a295d9572fb38a1289d4bdf661095b9ca7a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449116"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>计划和部署本地 Azure Active Directory 密码保护

用户经常创建包含本地常见字词（例如学校、运动团队或名人）的密码。 这些密码很容易猜出，对基于字典的攻击的抵御能力很弱。 若要在组织中强制实施强密码，Azure Active Directory (Azure AD) 密码保护可以提供全局、自定义受禁密码列表。 如果与受禁密码列表中的某个密码匹配，则密码更改请求将会失败。

若要保护本地 Active Directory 域服务 (AD DS) 环境，可以安装并配置 Azure AD 密码保护，使其与本地 DC 一起工作。 本文介绍如何在本地环境中安装和注册 Azure AD 密码保护代理服务以及 Azure AD 密码保护 DC 代理。

有关 Azure AD 密码保护在本地环境中的工作原理的详细信息，请参阅[如何为 Windows Server Active Directory 强制实施 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="deployment-strategy"></a>部署策略

下图显示了 Azure AD 密码保护的基本组件如何在本地 Active Directory 环境中配合工作：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

在部署软件之前，最好先了解软件的工作原理。 有关详细信息，请参阅 [Azure AD 密码保护的概念性概述](concept-password-ban-bad-on-premises.md)。

建议在审核模式下开始部署。 审核模式是默认初始设置，可在其中继续设置密码。 事件日志中会记录将被阻止的密码。 在审核模式下部署代理服务器和 DC 代理后，在强制执行策略时监视密码策略对用户的影响。

在审核阶段，许多组织发现以下情况适用：

* 需要改进现有的操作过程，以使用更安全的密码。
* 用户经常使用不安全的密码。
* 需要告知用户即将对安全措施做出的更改、这些更改可能对用户造成的影响以及如何选择更安全的密码。

更强的密码验证也有可能影响现有 Active Directory 域控制器部署自动化。 建议在审核期评估过程中至少进行一次 DC 升级和一次 DC 降级，以帮助发现此类问题。 有关详细信息，请参阅下列文章：

* [Ntdsutil.exe 无法设置目录服务修复模式弱密码](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [域控制器副本升级因目录服务修复模式弱密码而失败](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [域控制器降级因本地管理员弱密码而失败](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

该功能在审核模式下运行了一段合理的时间后，可以将配置从“审核”切换到“强制执行”，以要求使用更安全的密码。 在此期间最好执行额外监视。

请务必注意，Azure AD 密码保护只能在密码更改或设置操作期间验证密码。 将永不验证部署 Azure AD 密码保护之前 Active Directory 中已接受并存储的密码，并且它们将继续如常工作。 随着时间的推移，所有用户和帐户的现有密码都会正常过期，其最终会开始使用经 Azure AD 密码保护验证过的密码。 配置了“密码永不过期”的帐户不受此影响。

### <a name="multiple-forest-considerations"></a>多林注意事项

跨多个林部署 Azure AD 密码保护不需要满足其他要求。

将每个林独立配置为[部署本地 Azure AD 密码保护](#download-required-software)，如以下部分中所述。 每个 Azure AD 密码保护代理只能支持其加入的林中的域控制器。

不管 Active Directory 信任配置如何，任何林中的 Azure AD 密码保护软件都不知道其他林中部署的密码保护软件。

### <a name="read-only-domain-controller-considerations"></a>只读域控制器注意事项

不会在只读域控制器 (RODC) 上处理和保持密码更改或设置事件。 相反，它们会被转发到可写域控制器。 不必在 RODC 上安装 Azure AD 密码保护 DC 代理软件。

此外，不支持在只读域控制器上运行 Azure AD 密码保护代理服务。

### <a name="high-availability-considerations"></a>高可用性注意事项

密码保护的主要关注点是，当林中的 DC 尝试从 Azure 下载新策略或其他数据时，Azure AD 密码保护代理服务器的可用性。 每个 Azure AD 密码保护 DC 代理在决定调用哪个代理服务器时都使用简单的轮循机制式算法。 代理会跳过未响应的代理服务器。

对于大多数完全连接的 Active Directory 部署，如果其目录和 sysvol 文件夹复制状况正常，则两个 Azure AD 密码保护代理服务器足以确保可用性。 通过此配置可及时下载新策略和其他数据。 如果需要，可以部署其他 Azure AD 密码保护代理服务器。

Azure AD 密码保护 DC 代理软件的设计缓解了与高可用性相关的常见问题。 Azure AD 密码保护 DC 代理维护最近下载的密码策略的本地缓存。 即使所有注册的代理服务器都不可用，Azure AD 密码保护 DC 代理仍继续执行其缓存密码策略。

在大型部署中，密码策略的合理更新频率通常是几天，而不是几小时或更短的时间更新一次。 因此，代理服务器的短暂中断不会对 Azure AD 密码保护产生重大影响。

## <a name="deployment-requirements"></a>部署要求

有关许可的信息，请参阅 [Azure AD 密码保护许可要求](concept-password-ban-bad.md#license-requirements)。

以下核心要求适用：

* 安装了 Azure AD 密码保护组件的所有计算机（包括域控制器）都必须安装通用 C 运行时。
    * 确保从 Windows 更新获取了所有更新，从而获取运行时。 也可以在特定于 OS 的更新包中获取它。 有关详细信息，请参阅 [Windows 中的通用 C 运行时更新](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)。
* 你需要一个在林根域中具有 Active Directory 域管理员权限的帐户，以便向 Azure AD 注册 Windows Server Active Directory 林。
* 运行 Windows Server 2012 及更高版本的域中的所有域控制器上必须启用密钥分发服务。 默认情况下，此服务通过手动触发器启动来启用。

* 每个域中至少一个域控制器和至少一个托管 Azure AD 密码保护代理服务的服务器之间必须存在网络连接。 此连接必须允许域控制器访问代理服务上的 RPC 终结点映射程序端口 135 和 RPC 服务器端口。
    * 默认情况下，RPC 服务器端口是范围 (49152 - 65535) 内的一个动态 RPC 端口，但可将其配置为[使用静态端口](#static)。
* 将安装 Azure AD 密码保护代理服务的所有计算机必须具有对以下终结点的网络访问权限：

    |**终结点**|**用途**|
    | --- | --- |
    |`https://login.microsoftonline.com`|身份验证请求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密码保护功能|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD 密码保护 DC 代理

以下要求适用于 Azure AD 密码保护 DC 代理：

* 将安装 Azure AD 密码保护 DC 代理软件的所有计算机都必须运行 Windows Server 2012 或更高版本，包括 Windows Server 核心版本。
    * Active Directory 域或林不需要处于 Windows Server 2012 域功能级别 (DFL) 或林功能级别 (FFL)。 如[设计原则](concept-password-ban-bad-on-premises.md#design-principles)中所述，要运行 DC 代理或代理软件，没有最低 DFL 或 FFL 要求。
* 将安装 Azure AD 密码保护代理服务的所有计算机都必须安装 .NET 4.7.2。
    * 如果尚未安装 .NET 4.7.2，请下载并运行在[适用于 Windows 的 .NET Framework 4.7.2 脱机安装程序](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)中找到的安装程序。
* 运行 Azure AD 密码保护 DC 代理服务的所有 Active Directory 域都必须使用分布式文件系统复制 (DFSR) 进行 sysvol 复制。
   * 如果你的域尚未使用 DFSR，则必须先迁移，然后才能安装 Azure AD 密码保护。 有关详细信息，请参阅 [SYSVOL 复制迁移指南：FRS 到 DFS 复制](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD 密码保护 DC 代理软件当前将安装在仍使用 FRS（DFSR 的前身技术）进行 sysvol 复制的域中的域控制器上，但该软件在此环境中将无法正常运行。
    >
    > 其他负面影响包括未能复制单个文件，sysvol 还原过程显示成功，但未能复制所有文件且无任何提示。
    >
    > 尽快迁移域以使用 DFSR，从而利用 DFSR 的内在优势并取消阻止 Azure AD 密码保护的部署。 在仍使用 FRS 的域中运行此软件的未来版本时，会将其自动禁用。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密码保护代理服务

以下要求适用于 Azure AD 密码保护代理服务：

* 将安装 Azure AD 密码保护代理服务的所有计算机都必须运行 Windows Server 2012 R2 或更高版本，包括 Windows Server 核心版本。

    > [!NOTE]
    > Azure AD 密码保护代理服务部署是部署 Azure AD 密码保护的必需要求，即使域控制器可能具有出站直接 Internet 连接也是如此。

* 将安装 Azure AD 密码保护代理服务的所有计算机都必须安装 .NET 4.7.2。
    * 如果尚未安装 .NET 4.7.2，请下载并运行在[适用于 Windows 的 .NET Framework 4.7.2 脱机安装程序](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)中找到的安装程序。
* 必须配置所有托管 Azure AD 密码保护代理服务的计算机，以授予域控制器登录代理服务的能力。 此能力通过“从网络访问此计算机”特权分配进行控制。
* 必须配置所有托管 Azure AD 密码保护代理服务的计算机，以允许出站 TLS 1.2 HTTP 流量。
* 首次在给定租户中注册 Azure AD 密码保护代理服务时需要全局管理员帐户。 后续向 Azure AD 进行的代理和林注册可能会使用具有全局管理员或安全管理员凭据的帐户 。
* 必须为[应用程序代理环境设置过程](../app-proxy/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)中指定的一系列端口和 URL 启用网络访问。 这是上述两个终结点的新增功能。

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD Connect 代理更新程序先决条件

Microsoft Azure AD Connect 代理更新程序服务与 Azure AD 密码保护代理服务并行安装。 为了使 Microsoft Azure AD Connect 代理更新程序服务能够正常工作，还需要进行其他配置：

* 如果你的环境使用 HTTP 代理服务器，请遵循[使用现有的本地代理服务器](../app-proxy/application-proxy-configure-connectors-with-proxy-servers.md)中指定的准则。
* 针对 Microsoft Azure AD Connect 代理更新程序服务，还需要执行 [TLS 要求](../app-proxy/application-proxy-add-on-premises-application.md#tls-requirements)中指定的 TLS 1.2 步骤。

> [!WARNING]
> Azure AD 密码保护代理和 Azure AD 应用程序代理安装不同版本的 Microsoft Azure AD Connect Agent Updater 服务，这就是为什么说明指的是应用程序代理内容。 并行安装时，这些不同版本将不兼容，这会阻止代理更新程序服务联系 Azure 获取软件更新，因此绝不应在同一台计算机上安装 Azure AD 密码保护代理和应用程序代理。

## <a name="download-required-software"></a>下载所需软件

部署本地 Azure AD 密码保护需要两个安装程序：

* Azure AD 密码保护 DC 代理 (AzureADPasswordProtectionDCAgentSetup.msi)
* Azure AD 密码保代理 (AzureADPasswordProtectionProxySetup.exe)

从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)下载这两个安装程序。

## <a name="install-and-configure-the-proxy-service"></a>安装和配置代理服务

Azure AD 密码保护代理服务通常位于你本地 AD DS 环境中的成员服务器上。 安装后，Azure AD 密码保护代理服务会与 Azure AD 通信，以维护 Azure AD 租户的全局和自定义禁止的密码列表副本。

在下一部分中，你将在本地 AD DS 环境中的域控制器上安装 Azure AD 密码保护 DC 代理。 这些 DC 代理将与代理服务进行通信，以获取在域中处理密码更改事件时要使用的最新受禁密码列表。

选择用于托管 Azure AD 密码保护代理服务的一个或多个服务器。 以下注意事项适用于此类服务器：

* 每个此类服务只能为一个林提供密码策略。 必须将主机联接到该林中的任何域。
* 可以将代理服务安装在根域、子域或两者的组合。
* 林的每个域中至少有一台 DC 和一台密码保护代理服务器需要建立网络连接。
* 可以在域控制器上运行 Azure AD 密码保护代理服务用于测试，但该域控制器需要建立 Internet 连接。 此连接可能存在安全隐患。 建议此配置仅用于测试。
* 建议为每个林至少提供两台 Azure AD 密码保护代理服务器以实现冗余，如上一部分[高可用性注意事项](#high-availability-considerations)中所述。
* 不支持在只读域控制器上运行 Azure AD 密码保护代理服务。

若要安装 Azure AD 密码保护代理服务，请完成以下步骤：

1. 若要安装 Azure AD 密码保护代理服务，请运行 `AzureADPasswordProtectionProxySetup.exe` 软件安装程序。

    软件安装不需要重新启动，并可以使用标准 MSI 过程自动完成，如以下示例所示：
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > 安装 `AzureADPasswordProtectionProxySetup.exe` 包之前，必须运行 Windows 防火墙服务，以避免出现安装错误。
    >
    > 如果 Windows 防火墙配置为不运行，则解决方法是在安装过程中暂时启用并运行防火墙服务。 代理软件在安装后对 Windows 防火墙没有特定的依赖关系。
    >
    > 如果要使用第三方防火墙，也仍必须配置它以满足部署要求。 其中包括允许对端口 135 和代理 RPC 服务器端口进行入站访问。 有关详细信息，请参阅上一部分[部署要求](#deployment-requirements)。

1. Azure AD 密码保护代理软件包含新的 PowerShell 模块 `AzureADPasswordProtection`。 以下步骤将从此 PowerShell 模块运行各种 cmdlet。

    若要使用此模块，请以管理员身份打开 PowerShell 窗口并按如下所示导入新模块：
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```
    
    > [!WARNING]
    > 必须使用 64 位版本的 PowerShell。 某些 cmdlet 可能不适用于 PowerShell (x86)。

1. 若要检查 Azure AD 密码保护代理服务是否正在运行，请使用以下 PowerShell 命令：

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    结果应在“状态”处显示“正在运行”。

1. 代理服务正在计算机上运行，但没有凭据与 Azure AD 通信。 使用 `Register-AzureADPasswordProtectionProxy` cmdlet 向 Azure AD 注册 Azure AD 密码保护代理服务器。

    首次为给定租户注册任何代理时，此 cmdlet 需要全局管理员凭据。 该租户中的后续代理注册，无论是针对相同的还是不同的代理，都可以使用全局管理员或安全管理员凭据 。

    此命令成功后，其他调用也会成功，但没有必要。

    `Register-AzureADPasswordProtectionProxy` cmdlet 支持以下三种身份验证模式。 前两种模式支持 Azure AD 多重身份验证，但第三种模式不支持。

    > [!TIP]
    > 在首次为特定 Azure 租户运行此 cmdlet 时，完成前可能会出现明显延迟。 除非报告失败，否则不必担心这种延迟。

     * 交互式身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式不适用于 Server Core 操作系统。 请改用以下身份验证模式之一。 此外，如果启用了 Internet Explorer 增强的安全性配置，则此模式还可能会失败。 解决方法是禁用该配置，注册代理，然后重新启用它。

     * 设备代码身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        出现提示时，请按照链接打开 Web 浏览器并输入验证码。

     * 无提示（基于密码）身份验证模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果你的帐户需要进行 Azure AD 多重身份验证，则此模式会失败。 在这种情况下，请使用上述两种身份验证模式之一，或者改用不需要 MFA 的其他帐户。
        >
        > 如果已将 Azure 设备注册（由 Azure AD 密码保护在后台使用）配置为全局需要 MFA，则也会看到需要进行 MFA。 若要应对这一要求，你可以使用另一个帐户，该帐户支持使用上述两种身份验证模式之一进行 MFA，或者你也可以暂时放宽 Azure 设备注册 MFA 要求。
        >
        > 若要进行此更改，请在 Azure 门户中搜索并选择“Azure Active Directory”，然后选择“设备”>“设备设置”。 将“需要进行多重身份验证才能联接设备”设置为“否”。 完成注册后，请确保将此设置重新配置为“是”。
        >
        > 建议仅用于测试目的时绕过 MFA 要求。

    目前不必指定 -ForestCredential 参数，该参数是为将来的功能保留的。

    在 Azure AD 密码保护代理服务的生存期，只需注册该服务一次。 之后，Azure AD 密码保护代理服务将自动执行其他任何必要的维护。

1. 现在运行 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet，使用与 Azure 通信所需的凭据注册本地 Active Directory 林。

    > [!NOTE]
    > 如果环境中安装了多个 Azure AD 密码保护代理服务器，使用哪个代理服务器注册林并不重要。

    此 cmdlet 需要 Azure 租户的“全局管理员”或“安全管理员”凭据。 它还需要本地 Active Directory 企业管理员特权。 此外，还必须使用具有本地管理员特权的帐户来运行此 cmdlet。 用于注册林的 Azure 帐户可能不同于本地 Active Directory 帐户。
    
    针对每个林运行此步骤一次。

    `Register-AzureADPasswordProtectionForest` cmdlet 支持以下三种身份验证模式。 前两种模式支持 Azure AD 多重身份验证，但第三种模式不支持。

    > [!TIP]
    > 在首次为特定 Azure 租户运行此 cmdlet 时，完成前可能会出现明显延迟。 除非报告失败，否则不必担心这种延迟。

     * 交互式身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式将不适用于 Server Core 操作系统。 请改用以下两种身份验证模式之一。 此外，如果启用了 Internet Explorer 增强的安全性配置，则此模式还可能会失败。 解决方法是禁用该配置，注册林，然后重新启用它。  

     * 设备代码身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        出现提示时，请按照链接打开 Web 浏览器并输入验证码。

     * 无提示（基于密码）身份验证模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果你的帐户需要进行 Azure AD 多重身份验证，则此模式会失败。 在这种情况下，请使用上述两种身份验证模式之一，或者改用不需要 MFA 的其他帐户。
        >
        > 如果已将 Azure 设备注册（由 Azure AD 密码保护在后台使用）配置为全局需要 MFA，则也会看到需要进行 MFA。 若要应对这一要求，你可以使用另一个帐户，该帐户支持使用上述两种身份验证模式之一进行 MFA，或者你也可以暂时放宽 Azure 设备注册 MFA 要求。
        >
        > 若要进行此更改，请在 Azure 门户中搜索并选择“Azure Active Directory”，然后选择“设备”>“设备设置”。 将“需要进行多重身份验证才能联接设备”设置为“否”。 完成注册后，请确保将此设置重新配置为“是”。
        >
        > 建议仅用于测试目的时绕过 MFA 要求。

       仅当当前登录的用户也是根域的 Active Directory 域管理员时，这些示例才会成功。 否则，可以通过 -ForestCredential 参数提供替代的域凭据。

    在 Active Directory 林的生存期内，只需注册该林一次。 之后，林中 Azure AD 密码保护 DC 代理将自动执行其他任何必要的维护。 针对林运行 `Register-AzureADPasswordProtectionForest` 成功后，cmdlet 的其他调用将成功，但没有必要。
    
    若要成功运行 `Register-AzureADPasswordProtectionForest`，Azure AD 密码保护代理服务器的域中必须至少有一台运行 Windows Server 2012 或更高版本的 DC。 在执行此步骤之前，不必在任何域控制器上安装 Azure AD 密码保护 DC 代理软件。

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>将代理服务配置为通过 HTTP 代理通信

如果你的环境需要使用特定的 HTTP 代理与 Azure 通信，请使用以下步骤来配置 Azure AD 密码保护服务。

在 `%ProgramFiles%\Azure AD Password Protection Proxy\Service` 文件夹中创建 AzureADPasswordProtectionProxy.exe.config 文件。 包括以下内容：

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

如果 HTTP 代理需要身份验证，请添加 useDefaultCredentials 标记：

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

在这两种情况下，将 `http://yourhttpproxy.com:8080` 替换为特定 HTTP 代理服务器的地址和端口。

如果将 HTTP 代理配置为使用授权策略，则必须授予对托管密码保护代理服务的计算机的 Active Directory 计算机帐户的访问权限。

建议在创建或更新 AzureADPasswordProtectionProxy.exe.config 文件后，停止并重启 Azure AD 密码保护代理服务。

代理服务不支持使用特定凭据连接到 HTTP 代理。

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>将代理服务配置为侦听特定的端口。

Azure AD 密码保护 DC 代理软件使用基于 TCP 的 RPC 与代理服务通信。 默认情况下，Azure AD 密码保护代理服务侦听任何可用的动态 RPC 终结点。 出于环境中网络拓扑或防火墙要求，可根据需要将服务配置为侦听特定的 TCP 端口。 配置静态端口时，必须打开端口 135 和所选的静态端口。

<a id="static" /></a>若要将服务配置为在静态端口下运行，请使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet，如下所示：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> 必须停止并重启 Azure AD 密码保护代理服务，才能让这些更改生效。

若要将服务配置为在动态端口下运行，请使用相同的过程，但要将 StaticPort 重新设置为 0：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> 必须停止并重启 Azure AD 密码保护代理服务，才能让这些更改生效。

在端口配置中进行任何更改后，需要手动重启 Azure AD 密码保护代理服务。 进行这些配置更改后，无需在域控制器上重启 Azure AD 密码保护 DC 代理服务。

若要查询当前的服务配置，可以使用 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet，如以下示例所示

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

以下示例输出显示 Azure AD 密码保护代理服务正在使用动态端口：

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>安装 DC 代理服务

若要安装 Azure AD 密码保护 DC 代理服务，请运行 `AzureADPasswordProtectionDCAgentSetup.msi` 包。

你可以使用标准的 MSI 过程自动化软件安装，如下所示：

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

如果希望安装程序自动重新启动计算机，则可以省略 `/norestart` 标记。

软件安装或卸载需要重启。 此要求是因为密码筛选器 DLL 仅通过重启加载或卸载。

在域控制器上安装 DC 代理软件并重新启动计算机后，本地 Azure AD 密码保护的安装即完成。 无需（也无法）进行其他配置。 针对本地 DC 的密码更改事件使用 Azure AD 中已配置的受禁密码列表。

若要从 Azure 门户启用本地 Azure AD 密码保护或配置自定义受禁密码，请参阅[启用本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-operations.md)。

> [!TIP]
> 你可以在尚未成为域控制器的计算机上安装 Azure AD 密码保护 DC 代理。 在这种情况下，服务将启动并运行，但在将计算机提升为域控制器之前服务会保持非活动状态。

## <a name="upgrading-the-proxy-service"></a>升级代理服务

Azure AD 密码保护代理服务支持自动升级。 自动升级使用与代理服务并排安装的 Microsoft Azure AD Connect Agent Updater 服务。 默认情况下启用自动升级，可以使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet 启用或禁用它。

可以使用 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet 查询当前设置。 建议始终启用自动升级设置。

`Get-AzureADPasswordProtectionProxy` cmdlet 可用于查询当前在林中安装的所有 Azure AD 密码保护代理服务器的软件版本。

### <a name="manual-upgrade-process"></a>手动升级过程

手动升级是通过运行最新版本的 `AzureADPasswordProtectionProxySetup.exe` 软件安装程序完成的。 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)提供了该软件的最新版本。

不需要卸载当前版本的 Azure AD 密码保护代理服务 - 安装程序会执行就地升级。 升级代理服务时，不需要重启。 可以使用标准的 MSI 过程（如 `AzureADPasswordProtectionProxySetup.exe /quiet`）自动化软件升级。

## <a name="upgrading-the-dc-agent"></a>升级 DC 代理

当 Azure AD 密码保护 DC 代理软件的较新版本可用时，通过运行最新版本的 `AzureADPasswordProtectionDCAgentSetup.msi` 软件包来完成升级。 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)提供了该软件的最新版本。

不需要卸载当前版本的 DC 代理软件 - 安装程序会执行就地升级。 升级 DC 代理软件时，始终需要重新启动 - 此项要求是核心 Windows 行为造成的。

可以使用标准的 MSI 过程（如 `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`）自动化软件升级。

如果你希望安装程序自动重启计算机，则可以省略 `/norestart` 标志。

`Get-AzureADPasswordProtectionDCAgent` cmdlet 可用于查询当前在林中安装的所有 Azure AD 密码保护 DC 代理的软件版本。

## <a name="next-steps"></a>后续步骤

现在，本地服务器上已安装 Azure AD 密码保护所需的服务，请[在 Azure 门户中启用本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-operations.md)，以完成部署。
