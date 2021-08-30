---
title: Azure 虚拟桌面安全最佳做法 - Azure
description: 保持 Azure 虚拟桌面环境安全的最佳做法。
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5c9421397b6e5fbfe8688e5ceeff6056de25674a
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862330"
---
# <a name="security-best-practices"></a>安全最佳做法

Azure 虚拟桌面是一种托管虚拟桌面服务，其中包括用于保护组织安全的许多安全功能。 在 Azure 虚拟桌面部署中，Microsoft 代表客户管理部分服务。 此服务具有许多内置的高级安全功能，如“反向连接”，其可降低从任意位置访问远程桌面的潜在风险。

本文介绍了为保护客户的 Azure 虚拟桌面部署安全，以管理员身份可以采取的其他步骤。

## <a name="security-responsibilities"></a>安全责任

云服务与传统本地虚拟桌面基础结构 (VDI) 的不同之处在于其处理安全责任的方式。 例如，在传统本地 VDI 中，客户将负责安全的方方面面。 但在大多数云服务中，客户和公司共担这些责任。

使用 Azure 虚拟桌面时，务必要了解的是，尽管某些组件已经为你的环境提供了保护，但你需要自行配置其他区域以满足组织的安全需求。

在 Azure 虚拟桌面部署中，需要满足以下安全需求：

| 安全要求 | 是否客户负责？ |
|---------------|:-------------------------:|
|标识|是|
|用户设备（移动和电脑）|是|
|应用安全|是|
|会话主机操作系统|是|
|部署配置|是|
|网络控制措施|是|
|虚拟化控制平面|否|
|物理主机|否|
|物理网络|否|
|物理数据中心|否|

客户不负责的安全要求由 Microsoft 处理。

## <a name="azure-security-best-practices"></a>Azure 安全最佳做法

Azure 虚拟桌面是一项 Azure 服务。 若要最大程度地提高 Azure 虚拟桌面部署的安全性，应确保同时保护周围的 Azure 基础结构和管理平面。 若要保护你的基础结构，请考虑 Azure 虚拟桌面如何适应更大的 Azure 生态系统。 若要了解有关 Azure 生态系统的详细信息，请参阅 [Azure 安全最佳做法和模式](../security/fundamentals/best-practices-and-patterns.md)。

本部分介绍保护 Azure 生态系统的最佳实践。

### <a name="enable-azure-security-center"></a>启用 Azure 安全中心

建议为订阅、虚拟机、密钥保管库和存储帐户启用 Azure 安全中心标准。

通过 Azure 安全中心标准层，你可以：

* 管理漏洞。
* 评估与 PCI 等常见框架的符合性。
* 增强环境的总体安全性。

若要了解更多信息，请参阅[将 Azure 订阅加入安全中心标准层](../security-center/security-center-get-started.md)。

### <a name="improve-your-secure-score"></a>提高安全分数

安全评分提供有关如何提高总体安全性的建议和最佳做法建议。 这些建议的优先顺序可帮助你选择最重要的建议，而快速修复选项可帮助你快速解决潜在漏洞。 这些建议还会随着时间的推移进行更新，使你可以获取保持环境安全的最新的最佳做法。 若要了解更多信息，请参阅[提高 Azure 安全中心中的安全评分](../security-center/secure-score-security-controls.md)。

## <a name="azure-virtual-desktop-security-best-practices"></a>Azure 虚拟桌面安全最佳做法

Azure 虚拟桌面具有许多内置的安全控制。 在本部分中，你将了解可用于保持用户和数据安全的安全控制。

### <a name="require-multi-factor-authentication"></a>需要多重身份验证

Azure 虚拟桌面要求所有用户和管理员进行多重身份验证，这可以提高整个部署的安全性。 若要了解更多信息，请参阅[为 Azure 虚拟桌面启用 Azure AD 多重身份验证](set-up-mfa.md)。

### <a name="enable-conditional-access"></a>启用条件访问

启用[条件访问](../active-directory/conditional-access/overview.md)可让你在向用户授予对 Azure 虚拟桌面环境的访问权限之前管理风险。 在决定要授予访问权限的用户时，我们建议你同时考虑用户是谁、他们登录的方式以及他们使用的设备。

### <a name="collect-audit-logs"></a>收集审核日志

启用审核日志收集可查看与 Azure 虚拟桌面相关的用户和管理员活动。 重要审核日志的一些示例如下：

-   [Azure 活动日志](../azure-monitor/essentials/activity-log.md)
-   [Azure Active Directory 活动日志](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   Azure Active Directory
-   [会话主机](../azure-monitor/agents/agent-windows.md)
-   [Azure 虚拟桌面诊断日志](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault 日志](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>使用 RemoteApp

选择部署模型时，可以为远程用户提供对整个虚拟桌面的访问权限，也可以仅选择应用程序。 用户在虚拟桌面上使用应用时，远程应用程序（或 RemoteApp）可提供无缝体验。 RemoteApp 仅让用户使用应用程序公开的一小部分远程计算机以降低风险。

### <a name="monitor-usage-with-azure-monitor"></a>使用 Azure Monitor 监视使用情况

使用 [Azure Monitor](https://azure.microsoft.com/services/monitor/) 监视 Azure 虚拟桌面服务的使用情况和可用性。 考虑为 Azure 虚拟桌面服务创建[服务运行状况警报](../service-health/alerts-activity-log-service-notifications-portal.md)，以便在出现服务影响事件时接收通知。

## <a name="session-host-security-best-practices"></a>会话主机安全最佳做法

会话主机是在 Azure 订阅和虚拟网络中运行的虚拟机。 Azure 虚拟桌面部署的总体安全性取决于在会话主机上设置的安全控制。 本部分介绍保持会话主机安全的最佳做法。

### <a name="enable-endpoint-protection"></a>启用终结点保护

为了防止部署被已知恶意软件攻击，我们建议在所有会话主机上启用终结点保护。 可以使用 Windows Defender 防病毒，也可以使用第三方程序。 若要了解更多信息，请参阅 [VDI 环境中的 Windows Defender 防病毒部署指南](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus)。

对于 FSLogix 等配置文件解决方案或装载 VHD 文件的其他解决方案，建议排除 VHD 文件扩展名。

### <a name="install-an-endpoint-detection-and-response-product"></a>安装终结点检测和响应产品

建议安装终结点检测和响应 (EDR) 产品，以提供高级检测和响应功能。 对于启用了 [Azure 安全中心](../security-center/security-center-services.md)的服务器操作系统，安装 EDR 产品将部署 Defender ATP。 对于客户端操作系统，可以将 [DEFENDER ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) 或第三方产品部署到这些终结点。

### <a name="enable-threat-and-vulnerability-management-assessments"></a>启用威胁和漏洞管理评估

确定操作系统和应用程序中存在的软件漏洞对于保持环境安全至关重要。 Azure 安全中心可通过服务器操作系统漏洞评估来帮助你确定问题点。 你还可以使用 Defender ATP，为桌面操作系统提供威胁和漏洞管理。 尽管我们建议使用 Azure 安全中心和 Defender ATP，但如果你倾向于使用第三方产品，你也可以使用第三方产品。

### <a name="patch-software-vulnerabilities-in-your-environment"></a>修补环境中的软件漏洞

确定漏洞后，你必须对其进行修补。 这也适用于虚拟环境，其中包括正在运行的操作系统、在其中部署的应用程序，以及从中创建新计算机的映像。 遵循供应商修补通知通信并及时应用修补程序。 建议每月修补基本映像，以确保新部署的计算机尽可能安全。

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>建立最大非活动时间和断开连接策略

当用户处于非活动状态时将其注销会保留资源并防止未经授权的用户访问。 我们建议超时在用户工作效率和资源使用情况之间取得平衡。 对于与无状态应用程序交互的用户，请考虑更严格的策略，这些策略会关闭计算机并保留资源。 断开在用户空闲时继续运行的长时间运行的应用程序（例如模拟或 CAD 渲染）会中断用户的工作，甚至可能需要重启计算机。

### <a name="set-up-screen-locks-for-idle-sessions"></a>设置空闲会话的屏幕锁定

可以通过将 Azure 虚拟桌面配置为在空闲时锁定计算机屏幕且需要身份验证才能解锁，来防止意外的系统访问。

### <a name="establish-tiered-admin-access"></a>建立分层管理员访问权限

建议你不要向用户授予对虚拟桌面的管理员访问权限。 如果需要软件包，我们建议通过 Microsoft Endpoint Manager 等配置管理实用程序提供。 在多会话环境下，建议不要允许用户直接安装软件。

### <a name="consider-which-users-should-access-which-resources"></a>考虑哪些用户应该有权访问哪些资源

将会话主机视为现有桌面部署的扩展。 建议使用与环境中的其他桌面相同的方式（如使用网络分段和筛选）来控制对网络资源的访问。 默认情况下，会话主机可以连接到 Internet 上的任何资源。 可以通过多种方式来限制流量，包括使用 Azure 防火墙、网络虚拟设备或代理。 如果需要限制流量，请确保添加适当的规则，以便 Azure 虚拟桌面可以正常工作。

### <a name="manage-office-pro-plus-security"></a>管理 Office Pro Plus 安全性

除了保护会话主机以外，还必须确保在其中运行的应用程序的安全。 Office Pro Plus 是在会话主机中部署的最常见的应用程序之一。 为了改进 Office 部署安全性，我们建议对 Microsoft 365 企业应用版使用[安全策略顾问](/DeployOffice/overview-of-security-policy-advisor)。 此工具标识可应用于部署以提高安全性的策略。 安全策略顾问还根据对安全性和工作效率的影响来推荐策略。

### <a name="other-security-tips-for-session-hosts"></a>会话主机的其他安全提示

通过限制操作系统功能，可以增强会话主机的安全性。 下面是可以执行的几个操作：

- 通过在远程桌面会话中将驱动器、打印机和 USB 设备重定向到用户的本地设备来控制设备重定向。 建议你评估安全要求，并检查是否应禁用这些功能。

- 通过隐藏本地和远程驱动器映射来限制 Windows 资源管理器访问权限。 这可以防止用户发现有关系统配置和用户的意外信息。

- 避免对环境中的会话主机进行直接 RDP 访问。 如果需要直接 RDP 访问以便进行管理或故障排除，请启用[即时](../security-center/security-center-just-in-time.md)访问以限制会话主机上的潜在攻击面。

- 授予用户访问本地和远程文件系统时的有限权限。 可以通过确保本地和远程文件系统使用具有最低权限的访问控制列表来限制权限。 这样，用户只能访问他们需要的内容，而不能更改或删除关键资源。

- 防止意外软件在会话主机上运行。 可以启用 App Locker 以提高会话主机的安全性，确保只有允许的应用可在主机上运行。

## <a name="azure-virtual-desktop-support-for-trusted-launch"></a>Azure 虚拟桌面支持受信任启动

受信任启动是第 2 代 Azure VM，其增强的安全功能旨在防止通过攻击途径（如 Rootkit、Bootkit和内核级恶意软件）的“堆栈底层”威胁。 下面是受信任启动的增强安全功能，所有这些功能在 Azure 虚拟桌面中均受支持。 若要了解有关受信任启动的更多信息，请访问 [Azure 虚拟机的受信任启动（预览版）](../virtual-machines/trusted-launch.md)。

### <a name="secure-boot"></a>安全启动

安全启动是平台固件支持的一种模式，可保护固件免受基于恶意软件的 Rootkit 和 Bootkit 的侵害。 此模式仅允许已签名的操作系统和驱动程序启动计算机。 

### <a name="monitor-boot-integrity-using-remote-attestation"></a>使用远程证明监视启动完整性

远程证明是检查 VM 运行状况的好方法。 远程证明将验证测量启动记录是否存在、是否是正版，以及是否源自虚拟受信任的平台模块 (vTPM)。 作为一项运行状况检查，它以加密方式确定平台正确启动。 

### <a name="vtpm"></a>vTPM

vTPM 是硬件受信任平台模块 (TPM) 的虚拟化版本，每个 VM 有一个 TPM 虚拟实例。 vTPM 通过对 VM 的整个启动链（UEFI、OS、系统和驱动程序）执行完整性测量来实现远程证明。 

建议在 VM 上启用 vTPM 以使用远程证明。 启用 vTPM 后，你还可以启用 BitLocker 功能，其提供全卷加密，以保护静态数据。 使用 vTPM 的任何功能都将导致机密绑定到特定 VM。 当用户在共用情况下连接到 Azure 虚拟桌面服务时，可以将用户重定向到主机池中的任何 VM。 根据功能的设计方式，这可能会产生影响。

>[!NOTE]
>不应使用 BitLocker 对存储 FSLogix 配置文件数据的特定磁盘进行加密。

### <a name="virtualization-based-security"></a>基于虚拟化的安全性

基于虚拟化的安全性 (VBS) 使用虚拟机监控程序来创建和隔离操作系统不可访问的安全内存区域。 虚拟机监控程序保护的代码完整性 (HVCI) 和 Windows Defender Credential Guard 均使用 VBS 来提供增强的漏洞防护。 

#### <a name="hypervisor-protected-code-integrity"></a>虚拟机监控程序保护的代码完整性

HVCI 是功能强大的系统缓解机制，其使用 VBS 防止在 Windows 内核模式进程中注入和执行恶意代码或未经验证的代码。

#### <a name="windows-defender-credential-guard"></a>Windows Defender Credential Guard

Windows Defender 凭据保护使用 VBS 来隔离和保护机密，以便只有特权系统软件可以访问它们。 这样可以防止未经授权访问这些机密和凭据窃取攻击，如传递哈希攻击。

### <a name="deploy-trusted-launch-in-your-azure-virtual-desktop-environment"></a>在 Azure 虚拟桌面环境中部署受信任启动

Azure 虚拟桌面目前不支持在主机池设置过程中自动配置受信任启动。 若要在 Azure 虚拟桌面环境中使用受信任启动，需要正常部署受信任启动，然后手动将虚拟机添加到所需的主机池。

## <a name="nested-virtualization"></a>嵌套虚拟化

以下操作系统支持在 Azure 虚拟桌面上运行嵌套虚拟化：

- Windows Server 2016
- Windows Server 2019
- Windows 10 企业版
- Windows 10 Enterprise 多会话

## <a name="windows-defender-application-control"></a>Windows Defender 应用程序控制

以下操作系统支持将“Windows Defender 应用程序控制”与“Azure 虚拟桌面”结合使用：

- Windows Server 2016
- Windows Server 2019
- Windows 10 企业版
- Windows 10 Enterprise 多会话

>[!NOTE]
>使用 Windows Defender 访问控制时，建议仅在设备级别以策略为目标。 尽管策略可以面向单个用户，但应用策略后，它会对设备上所有用户产生同等影响。

## <a name="ip-virtualization"></a>IP 虚拟化

如果要在 Windows Server 2019 上使用 IP 虚拟化，请执行以下步骤：

1. 在管理员 Windows PowerShell 窗口中，重命名以下密钥： 
```powershell
Rename-Item HKLM:\SYSTEM\ControlSet001\Services\WinSock2\Parameters\AppId_Catalog\2C69D9F1 Backup_2C69D9F1
```
>[!NOTE]
>删除密钥可实现相同的结果，但重命名提供了一种可以更轻松还原的方法（如果需要）。 这是默认情况下存在的数据：
>       
>HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\WinSock2\Parameters\AppId_Catalog\2C69D9F1\
>AppFullPath: C:\Windows\System32\svchost.exe\
>PermittedLspCategories：0x40000000

2. 重启 VM。

3. 通过打开“gpedit.msc”，然后转到“计算机配置” > “管理模板” > “Windows 组件” > “远程桌面服务” > “远程桌面会话主机” > “应用程序兼容性”      ，启用 IP 虚拟化功能。 启用“打开远程桌面 IP 虚拟化”策略，然后指定想要策略使用的 IP 地址。

4. 重启 VM。

## <a name="next-steps"></a>后续步骤

若要了解如何启用多重身份验证，请参阅[设置多重身份验证](set-up-mfa.md)。
