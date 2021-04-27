---
title: Windows 虚拟桌面中有哪些新增功能？ - Azure
description: Windows 虚拟桌面的新增功能和产品更新。
author: Heidilohr
ms.topic: overview
ms.date: 04/08/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 242dd2476783a3d1db6b74452d35e87c5cd189b6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516546"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows 虚拟桌面中有哪些新增功能？

Windows 虚拟桌面会定期更新。 本文介绍了以下内容：

- 最新更新
- 新增功能
- 对现有功能的改进
- Bug 修复

本文每月更新一次。 请务必时常查看本文，以及时了解最新更新。

## <a name="client-updates"></a>客户端更新

要了解 Windows 虚拟桌面和远程桌面服务的客户端更新，请参阅以下文章：

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Windows 虚拟桌面代理更新

Windows 虚拟桌面代理每月至少更新一次。

请参阅 Windows 虚拟桌面代理的更改：

- 版本 1.0.2990.800：此更新发布于 2021 年 4 月 13 日，包含以下更改：
    - 已更新代理错误消息。
    - 添加例外，阻止在 Windows 7 VM 上安装非 Windows 7 代理。
    - 已更新检测信号服务逻辑。
- 版本 1.0.2944.1400：此更新发布于 2021 年 4 月 7 日，包含以下更改：
    - 在代理错误的事件查看器日志中放置了 Windows 虚拟桌面代理故障排除指南的链接。
    - 添加了额外的例外以实现更好的错误处理。
    - 添加了 WVDAgentUrlTool.exe，让客户可以检查其能访问的必需 URL。
- 版本 1.0.2866.1500：此更新发布于 2021 年 3 月 26 日，修复了堆栈运行状况检查的一个问题。
- 版本 1.0.2800.2802：此更新发布于 2021 年 3 月 10 日，其中包含常规改进和错误修复。
- 版本 1.0.2800.2800：此更新发布于 2021 年 3 月 2 日，修复了反向连接问题。
- 版本 1.0.2800.2700：此更新发布于 2021 年 2 月 10 日，其中包含常规改进和错误修复。
- 版本 1.0.2800.2700：此更新发布于 2021 年 2 月 4 日，修复了访问遭拒的业务流程问题。

## <a name="fslogix-updates"></a>FSLogix 更新

想知道 FSLogix 的最新更新吗？ 请查看 [FSLogix 的新增功能](/fslogix/whats-new)。

## <a name="march-2021"></a>2021 年 3 月

以下是 2021 年 3 月的更改。

### <a name="updates-to-the-azure-portal-ui-for-windows-virtual-desktop"></a>Windows 虚拟桌面的 Azure 门户 UI 更新

我们对 Windows 虚拟桌面 Azure 门户进行了一些更新：

- 为工作流启用了新的可用性选项（可用性集和区域）来创建主机池和添加 VM。
- 修复了处于“需要帮助”状态的主机显示为不可用的问题。 现在，主机旁边会出现一个警告图标。
- 已对活动会话启用排序。
- 你现在可以在“主机详细信息”选项卡上向特定用户发送消息或将其注销。
- 已更改最大会话限制字段。
- 已将 OU 验证路径添加到工作流以创建主机池。
- 你现在可以在创建个人主机池时使用最新版本的 Windows 10 映像。

### <a name="generation-2-images-and-trusted-launch"></a>第 2 代映像和受信任启动

Azure 市场现在提供适用于 Windows 10 企业版和 Windows 10 企业版多会话的第 2 代映像。 这些映像将允许使用受信任启动 VM。 若要详细了解第 2 代 VM，请参阅[我应该创建第 1 代还是第 2 代虚拟机](../virtual-machines/generation-2.md)。 若要了解如何预配 Windows 虚拟桌面受信任启动 VM，请参阅[我们的 TechCommunity 文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170)。

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix 现在预安装在 Windows 10 企业版多会话映像上

根据客户反馈，我们设置了新版本的 Windows 10 企业版多会话映像，该映像已安装了未配置的 FSLogix 的版本。 我们希望这能够帮助你更轻松地部署 Windows 虚拟桌面。

### <a name="azure-monitor-for-windows-virtual-desktop-is-now-in-general-availability"></a>Azure Monitor for Windows Virtual Desktop 现已正式发布

Azure Monitor for Windows Virtual Desktop 现在已面向公众正式发布。 此功能是一项自动化服务，可监视你的部署，使你能够在单个位置查看事件、运行状况和故障排除建议。 有关详细信息，请参阅[我们的文档](azure-monitor.md)或查看[我们的 TechCommunity 文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861)。

### <a name="march-2021-updates-for-teams-on-windows-virtual-desktop"></a>2021 年 3 月 Windows 虚拟桌面上的 Teams 更新

我们对 Windows 虚拟桌面上的 Teams 进行了以下更新：

- 提高了通话和 2x2 模式的视频质量性能。
- 使用视频处理 (XVP) 的硬件卸载，将 CPU 利用率降低了 5-10%（具体取决于 CPU 代系）。
- 旧版本的计算机现在可以使用 XVP 和硬件解码，以 2x2 模式流畅地显示更多传入的视频流。
- 已将 WebRTC 堆栈从 M74 更新为 M88，以提高 AV 同步性能并减少暂时性问题。
- 已将软件 H264 编码器替换为 OpenH264（Web 上 Teams 中使用的 OSS），这会提高传出相机的视频质量。
- 我们在 3 月 30 日为 Teams 服务器启用了 2x2 模式，供公众使用。 2x2 模式同时可显示最多四个传入视频流。

### <a name="start-vm-on-connect-public-preview"></a>连接时启动 VM 公共预览版

公共预览版中现在提供了“连接时启动 VM”这一主机池设置。 使用此设置能够根据需要随时启用 VM。 如果要节省成本，需要通过配置 Azure 计算设置来解除分配 VM。 有关详细信息，请查看[我们的博客文章](https://aka.ms/wvdstartvmonconnect)和[我们的文档](start-virtual-machine-connect.md)。

### <a name="windows-virtual-desktop-specialty-certification"></a>Windows 虚拟桌面专用认证

我们发布了 AZ-140 考试的 beta 版本，使你能够证明在 Azure 中的 Windows 虚拟桌面的专业知识。 要了解更多信息，请查看[我们的 文章](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107)。

## <a name="february-2021"></a>2021 年 2 月

以下是 2021 年 2 月的更改内容。

### <a name="portal-experience"></a>门户体验

我们通过以下方式改进了 Azure 门户体验：

- “会话主机网格”选项卡中主机上的批量排出模式。 
- MSIX 应用附加现已提供公共预览版。
- 已修复深色模式的主机池概述信息。

### <a name="eu-metadata-storage-now-in-public-preview"></a>EU 元数据存储现已提供公共预览版

我们现在正在托管作为 Windows 虚拟桌面中服务元数据的存储选项的欧洲 (EU) 地理的公共预览版。 客户在创建服务对象时可以在欧洲西部或欧洲北部之间进行选择。 主机池的服务对象和元数据将存储在与每个区域关联的 Azure 地理中。 若要了解详细信息，请参阅[我们宣布推出公共预览版的博客文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)。

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Windows 虚拟桌面上的 Teams 插件更新

我们通过解决最常见的报告问题（例如屏幕突然变暗或视频和声音不同步），提高了 Windows 虚拟桌面插件上的视频通话质量。 这些改进应能提高带有处于活动状态的发言人切换的单视频视图的性能。 我们还解决了以下问题：Teams 中无法使用具有特殊字符的硬件设备。

## <a name="january-2021"></a>2021 年 1 月

以下是 2021 年 1 月的更改内容：

### <a name="new-windows-virtual-desktop-offer"></a>Windows 虚拟桌面新优惠

如果使用原生 Microsoft 解决方案，则对于 D 系列和 B 系列虚拟机，新客户可节省 30% 的 Windows 虚拟桌面计算成本（此优惠只提供 90 天）。 你可于 2021 年 3 月 31 日之前在 Azure 门户中兑换此优惠。 有关详细信息，请参阅 [Windows 虚拟桌面优惠页面](https://azure.microsoft.com/services/virtual-desktop/offer/)。

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules 值更改 

在 Azure 资源管理器嵌套模板中，我们将 networkSecurityGroupRules 的默认值从对象更改为了数组。 这将避免在未指定 networkSecurityGroupRules 的值的情况下使用 managedDisks-customimagevm.json 时出现任何错误。 这不是一项中断性变更，而且向后兼容。

### <a name="fslogix-hotfix-update"></a>FSLogix 修补程序更新

我们发布了版本 2009 HF_01 (2.9.7654.46150) 的 FSLogix，用于解决上一版本 (2.9.7621.30127) 中的问题。 建议你尽快停止使用上一版本并更新 FSLogix。

有关详细信息，请查看 [FSLogix 中的新增功能](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)中的发行说明。

### <a name="azure-portal-experience-improvements"></a>Azure 门户体验改进

我们对 Azure 门户体验进行了以下改进：

- 现在，你可直接添加本地虚拟机 (VM) 管理员凭据，无需添加使用 Active Directory 域加入帐户凭据创建的本地帐户。
- 现在，用户可在单独的选项卡中为单个用户和组列出个人和组分配情况。
- 现在，可在主机池的虚拟机概述中查看 Windows 虚拟桌面代理的版本号。
- 对主机池和应用程序组添加了批量删除功能。
- 现在，你可为主机池中的多个会话主机启用或禁用排出模式。
- 从 VM 详细信息页面中删除了公共 IP 字段。

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Windows 虚拟桌面代理故障排除

我们近来设定了 [Windows 虚拟桌面代理故障排除指南](troubleshoot-agent.md)，来帮助遇到常见问题的客户。

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender for Endpoint 集成

Microsoft Defender for Endpoint 集成现已正式发布。 此功能为 Windows 虚拟桌面 VM 提供了与本地 Windows 10 计算机相同的调查体验。 如果你使用 Windows 10 企业版多会话，Microsoft Defender for Endpoint 将支持多达 50 个并发用户连接，从而为你节省 Windows 10 企业版多会话成本并增强你使用 Microsoft Defender for Endpoint 的信心。 有关详细信息，请查看我们的[博客文章](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)。

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Windows 虚拟桌面的 Azure 安全基线

我们最近发布了[一篇有关 Windows 虚拟桌面的 Azure 安全基线的文章](security-baseline.md)，请注意查看。 可在这些指南中了解如何将 2.0 版的 Azure 安全基准应用于 Windows 虚拟桌面。 Azure 安全基准介绍了我们建议的用于保护 Azure 上的云解决方案的设置和做法。

## <a name="december-2020"></a>2020 年 12 月

以下是 2020 年 12 月的更改内容： 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>适用于 Windows 虚拟桌面的 Azure Monitor

适用于 Windows 虚拟桌面的 Azure Monitor 现在提供公共预览版。 这项新功能包括一个在 Azure Monitor 工作簿基础上构建的强大仪表板，可帮助 IT 专业人员了解其 Windows 虚拟桌面环境。 有关详细信息，请查看[博客上的公告](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587)。 

### <a name="azure-resource-manager-template-change"></a>Azure 资源管理器模板更改 

在最新更新中，我们已从 Azure 资源管理器模板中删除了用于创建和预配主机池的所有公共 IP 地址参数。 强烈建议避免对 Windows 虚拟桌面使用公共 IP，以确保部署安全。 如果你的部署依赖于公共 IP，则需要将其重新配置为使用专用 IP，否则部署将无法正常工作。

### <a name="msix-app-attach-public-preview"></a>MSIX 应用附加公共预览版 

MSIX 应用附加是另一项服务，其公共预览版已在本月推出。 .MSIX 应用附加是一项服务，它可在 Windows 虚拟桌面会话主机 VM 中动态显示 MSIX 应用程序。 有关详细信息，请查看[博客上的公告](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231)。 

### <a name="screen-capture-protection"></a>屏幕捕获保护 

本月还标志着屏幕捕获保护公共预览版的开始。 可使用此功能防止在客户端终结点上捕获敏感信息。 转到[此页面](https://aka.ms/WVDScreenCaptureProtection)来试用屏幕捕获保护。  

### <a name="built-in-roles"></a>内置角色

我们为 Windows 虚拟桌面添加了新的内置角色来实现管理权限。 有关详细信息，请参阅 [Windows 虚拟桌面的内置角色](rbac.md)。 

### <a name="application-group-limit-increase"></a>应用程序组限额已提高

我们已将每个 Azure Active Directory 租户的默认应用程序组限额增加到 200 个组。

### <a name="client-updates-for-december-2020"></a>2020 年 12 月的客户端更新

我们已发布以下客户端的新版本： 

- Android
- macOS
- Windows

有关客户端更新的详细信息，请参阅[客户端更新](whats-new.md#client-updates)。

## <a name="november-2020"></a>2020 年 11 月

### <a name="azure-portal-experience"></a>Azure 门户体验

我们已经修复了 Azure 门户用户体验中的两个 bug：

- 桌面应用程序易记名称不会再在“添加 VM”工作流中被覆盖。
- 如果会话主机是规模集的一部分，现在将加载会话主机选项卡。

### <a name="fslogix-client-version-2009"></a>FSLogix 客户端，版本 2009 

我们发布了 FSLogix 客户端的新版本，其中包括许多修补程序和改进。 有关详细信息，请参阅[我们的博客文章](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)。

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath 公共预览版

RDP Shortpath 使用点到站点和站点到站点 VPN 和 ExpressRoute 引入与 Windows 虚拟桌面会话主机的直接连接。 它还引入了 URCP 传输协议。 RDP Shortpath 旨在减少延迟和网络跃点，以改善用户体验。 有关详细信息，请参阅 [Windows 虚拟桌面 RDP Shortpath](shortpath.md)。

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization，版本 2.0.1

我们发布了 Windows 虚拟桌面 cmdlet 版本 2.0.1。 此更新包括支持管理 MSIX 应用附加的 cmdlet。 可以在 [PowerShell 库](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)中下载新版本。

### <a name="azure-advisor-updates"></a>Azure 顾问更新

现在，Azure 顾问对 Windows 虚拟桌面中的邻近性指导有新的建议，而对于深度优先负载均衡主机池中的性能优化也有新的建议。 有关详细信息，请访问 [Azure 网站](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)。

## <a name="october-2020"></a>2020 年 10 月

以下是 2020 年 10 月的更改内容：

### <a name="improved-performance"></a>提高了性能

- 我们通过减少以下 Azure 地域的连接延迟优化了性能：
    - 瑞士
    - 加拿大

现在可以使用[体验评估器](https://azure.microsoft.com/services/virtual-desktop/assessment/)来评估这些区域的用户体验质量。

### <a name="azure-government-cloud-availability"></a>Azure 政府云可用性

Azure 政府云现已正式发布。 有关详细信息，请参阅[我们的博客文章](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)。

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows 虚拟桌面 Azure 门户更新

我们已对 Windows 虚拟桌面 Azure 门户做了一些更新：

- 已修复阻止用户打开“会话”选项卡的 resourceID 错误。
- 已简化“会话主机”选项卡上的 UI。
- 已修复 RDP 属性下的“默认值”、“可用性”和“还原默认值”设置。
- 已使“移除”和“删除”功能在所有选项卡上保持一致。
- 门户现在可验证“添加应用”工作流中的应用名称。
- 已修复会话主机导出数据在列中没有对齐的问题。
- 已修复门户无法检索用户会话的问题。
- 已修复在其他资源组中创建虚拟机时发生的会话主机检索问题。
- 已更新“会话主机”选项卡以列出活动会话和断开连接的会话。
- “应用程序”选项卡现在包含页面。
- 已修复“需要命令行”文本在“应用程序列表”选项卡中无法正确显示的问题。
- 已修复门户在使用德语版共享映像库时无法部署主机池或虚拟机的问题。

### <a name="client-updates-for-october-2020"></a>2020 年 10 月的客户端更新

我们已发布新版本的客户端。 请查看以下文章了解详细信息：

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

有关其他客户端的详细信息，请参阅[客户端更新](#client-updates)。

## <a name="september-2020"></a>2020 年 9 月

以下是 2020 年 9 月的更改：

- 我们通过减少以下 Azure 地域的连接延迟优化了性能：
    - 德国
    - 南非（仅限验证环境）

现在可以使用[体验评估器](https://azure.microsoft.com/services/virtual-desktop/assessment/)来评估这些区域的用户体验质量。

- 我们为 Windows 虚拟桌面发布了 Windows 桌面客户端版本 1.2.1364。 在此更新中，我们进行了以下更改：
    - 修复了 Windows 7 上单一登录 (SSO) 失效的问题。
    - 修复了在以下情况下导致客户端断开连接的问题：为 Teams 启用了媒体优化的用户在另一个应用以独占模式打开音频流时，尝试呼叫或加入 Teams 会议。
    - 修复了为 Teams 启用媒体优化后，Teams 不枚举音频或视频设备的问题。
    - 向桌面设置页面添加了一个 “需要设置方面的帮助吗？”链接。
    - 解决了在使用高对比度深色主题时“订阅”按钮发生的问题。
    
- 感谢用户的大力帮助，我们修复了 Microsoft Store 远程桌面客户端的两个关键问题。 随着全球越来越多用户使用该客户端的分阶段发行版，我们将继续审查反馈并解决问题。
    
- 我们添加了一项新功能，可用于更改 VM 位置、映像、资源组、前缀名称和网络配置，作为在 Azure 门户中向部署添加 VM 的工作流的一部分。

- IT 专业人员现在可以使用 Microsoft Endpoint Manager 管理已加入混合 Azure Active Directory 的 Windows 10 企业版 VM。 要了解详细信息，请参阅[我们的博客文章](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)。

## <a name="august-2020"></a>2020 年 8 月

以下是 2020 年 8 月的更改：

- 我们对性能进行了改进，缩短了以下 Azure 区域中的连接延迟： 

    - 英国
    - 法国
    - 挪威
    - 韩国

   你可以使用[体验评估器](https://azure.microsoft.com/services/virtual-desktop/assessment/)来大致了解这些更改将如何影响你的用户。

- Microsoft Store 远程桌面客户端 (v10.2.1522+) 现已正式发布！ 此版本的 Microsoft Store 远程桌面客户端与 Windows 虚拟桌面兼容。 为了改进用户体验，我们还引入了更新的 UI 流。 此更新包括流畅的设计、浅色和深色模式，以及许多其他激动人心的更改。 我们还重新将客户端编写为使用与 iOS、macOS 和 Android 客户端相同的基础远程桌面协议 (RDP) 引擎。 这样一来，我们就能以更快的速度跨所有平台提供新功能。 [下载客户端](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab)，试一试！

- 我们修复了 Teams 桌面客户端（版本 1.3.00.21759）中的问题，即客户端在聊天、频道和日历中仅显示 UTC 时区。 更新后的客户端现将显示远程会话的时区。

- Windows 虚拟桌面现包含 Azure 顾问。 通过 Azure 门户访问 Windows 虚拟桌面时，你可以查看关于优化 Windows 虚拟桌面环境的建议。 有关详细信息，请参阅 [Azure 顾问](azure-advisor.md)。

- Azure CLI 现支持 Windows 虚拟桌面 (`az desktopvirtualization`)，可帮助你自动执行 Windows 虚拟桌面部署。 如需查看扩展命令的列表，请查看 [desktopvirtualization](/cli/azure/ext/desktopvirtualization/)。

- 我们更新了部署模板，更新后的模板与 Windows 虚拟桌面 Azure 资源管理器接口完全兼容。 你可在 [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) 上找到这些模板。

- Windows 虚拟桌面 US Gov 门户现提供公共预览版。 若要了解详细信息，请参阅[公告](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)。

## <a name="july-2020"></a>2020 年 7 月  

7 月，Windows 虚拟桌面与 Azure 资源管理的集成正式发布。

以下是此新版本中所做的更改： 

- “2019 年秋季版本”现在称为“Windows 虚拟桌面（经典版）”，而“2020 年春季版本”现在称为“Windows 虚拟桌面”。 有关详细信息，请查看[此博客文章](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)。 

要详细了解新功能，请查看[此博客文章](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)。 

### <a name="autoscaling-tool-update"></a>自动缩放工具更新

自动缩放工具的最新版本（之前处于预览状态）现已正式发布。 此工具使用 Azure 自动化帐户和 Azure 逻辑应用来自动关闭和重新启动主机池中的会话主机虚拟机 (VM)，从而降低基础结构成本。 有关详细信息，请参阅[使用 Azure 自动化来改变会话主机规模](set-up-scaling-script.md)。

### <a name="azure-portal"></a>Azure 门户

现在，你可以在 Windows 虚拟桌面中使用 Azure 门户执行以下操作： 

- 直接将用户分配给个人桌面会话主机  
- 更改主机池的验证环境设置 

### <a name="diagnostics"></a>诊断

我们为 Log Analytics 工作区发布了一些新的预构建查询。 要访问这些查询，请转到“类别”下的“日志”，选择“Windows 虚拟桌面”  。 有关详细信息，请参阅[将 Log Analytics 用于诊断功能](diagnostics-log-analytics.md)。

### <a name="update-for-remote-desktop-client-for-android"></a>适用于 Android 的远程桌面客户端更新

[适用于 Android 的远程桌面客户端](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)现支持 Windows 虚拟桌面连接。 从版本 10.0.7 开始，Android 客户端采用新的 UI，可改进用户体验。 客户端还与 Android 设备上的 Microsoft Authenticator 集成，以在订阅 Windows 虚拟桌面工作区时启用条件访问。  

早期版本的远程桌面客户端现在称为“远程桌面 8”。 客户端早期版本中的任何现有连接都将无缝转移到新客户端。 新客户端已重写为使用与 iOS 和 macOS 客户端相同的基础 RDP 核心引擎，可跨所有平台更快地发布新功能。 

### <a name="teams-update"></a>Teams 更新

我们对适用于 Windows 虚拟桌面的 Microsoft Teams 进行了改进。 最重要的是，Windows 虚拟桌面现在支持适用于 Windows 桌面客户端的音频和视频优化。 用户使用音频或视频进行通话和会议时，重定向可在用户之间创建直接路径，从而改善延迟。 距离越短意味着跃点越少，因此通话的视频和音频都会更流畅。 有关详细信息，请参阅[在 Windows 虚拟桌面上使用 Teams](teams-on-wvd.md)。

## <a name="june-2020"></a>2020 年 6 月

在上一个月，我们引入了带 Azure 资源管理器集成的 Windows 虚拟桌面预览版。 此更新有许多令人兴奋的新增功能，我们很乐意向你介绍。 下面是此 Windows 虚拟桌面版本的新增功能。

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows 虚拟桌面现已与 Azure 资源管理器集成

Windows 虚拟桌面现已集成到 Azure 资源管理器中。 在最新更新中，所有 Windows 虚拟桌面对象现均为 Azure 资源管理器资源。 此更新还集成了 Azure 基于角色的访问控制 (Azure RBAC)。 请参阅[什么是 Azure 资源管理器？](../azure-resource-manager/management/overview.md)以了解详细信息。

下面是此更改的作用：

- Windows 虚拟桌面现已与 Azure 门户集成。 这意味着可以直接在门户中管理所有内容，而无需 PowerShell、Web 应用或第三方工具。 首先，请查看教程：[使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

- 在进行此更新之前，只能将 RemoteApp 和桌面应用发布到独立用户。 使用 Azure 资源管理器，现在可以将资源发布到 Azure Active Directory 组。

- Windows 虚拟桌面的早期版本具有四个内置管理员角色，你可以将其分配给租户或主机池。 这些角色现在受限于 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。 可以将这些角色应用于每个 Windows 虚拟桌面 Azure资源管理器对象，从而拥有完整而丰富的委派模型。

- 在此更新中，不再需要重复运行 Azure 市场或 GitHub 模板来扩展主机池。 要扩展主机池，只需在 Azure 门户中转到该主机池，然后选择“+ 添加”以部署其他会话主机即可。

- 主机池部署现已与 [Azure 共享映像库](../virtual-machines/shared-image-galleries.md)完全集成。 共享映像库是一项单独的 Azure 服务，用于存储虚拟机 (VM) 映像定义，包括映像版本控制。 还可以使用全球复制将映像复制并发送到其他 Azure 区域，以进行本地部署。

- 过去通过 PowerShell 或诊断服务 Web 应用完成的监视功能现已移至 Azure 门户中的 Log Analytics。 现在还可以通过两个选项可视化报表。 可以运行 Kusto 查询并使用工作簿创建视觉对象报表。

- 不再需要完成 Azure Active Directory (Azure AD) 同意即可使用 Windows 虚拟桌面。 在此更新中，Azure 订阅上的 Azure AD 租户会对用户进行身份验证，并为管理员提供 Azure RBAC 控制权限。

### <a name="powershell-support"></a>PowerShell 支持

我们已在此更新中向 Azure PowerShell Az 模块添加了新的 AzWvd cmdlet。 在 .NET Core 上运行的 PowerShell Core 支持此新模块。

要安装该模块，请按照[为 Windows 虚拟桌面设置 PowerShell 模块](powershell-module.md)中的说明操作。

还可以在 [AzWvd PowerShell 参考](/powershell/module/az.desktopvirtualization/#desktopvirtualization)处查看可用命令的列表。

有关新增功能的详细信息，请参阅[我们的博客文章](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)。

### <a name="additional-gateways"></a>其他网关

我们在南非添加了新的网关群集，以降低连接延迟。

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Windows 虚拟桌面上的 Microsoft Teams（预览版）

我们对适用于 Windows 虚拟桌面的 Microsoft Teams 进行了一些改进。 最重要的是，Windows 虚拟桌面现在支持通话音频和视觉对象重定向。 用户使用音频或视频进行通话时，重定向可在用户之间创建直接路径，从而改善延迟。 距离越短意味着跃点越少，因此通话的视频和音频都会更流畅。

要了解详细信息，请参阅[我们的博客文章](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)。

## <a name="next-steps"></a>后续步骤

请在 [Microsoft 365 Windows 虚拟桌面路线图](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)了解未来的计划。
