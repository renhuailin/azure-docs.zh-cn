---
title: Azure 虚拟桌面中有哪些新增功能？ - Azure
description: Azure 虚拟桌面的新增功能和产品更新。
author: Heidilohr
ms.topic: overview
ms.date: 09/27/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 438a3f4091afbfb885e2f585196193ce91f13f4a
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154539"
---
# <a name="whats-new-in-azure-virtual-desktop"></a>Azure 虚拟桌面中有哪些新增功能？

Azure 虚拟桌面会定期更新。 本文介绍了以下内容：

- 最新更新
- 新增功能
- 对现有功能的改进
- Bug 修复

本文每月更新一次。 请务必时常查看本文，以及时了解最新更新。

## <a name="client-updates"></a>客户端更新

要了解 Azure 虚拟桌面和远程桌面服务的客户端更新，请参阅以下文章：

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="azure-virtual-desktop-agent-updates"></a>Azure 虚拟桌面代理更新

Azure 虚拟桌面代理每月至少更新一次。

请参阅 Azure 虚拟桌面代理的更改：

- 版本 1.0.3373.2600：此更新发布于 2021 年 9 月，包含以下更改：
    - 常规代理改进。
    - 修复了在 Windows 7 VM 上重启代理时出现的问题。
    - 修复了 WVDAgentHealthStatus 表中的字段未正确显示的问题。
- 版本 1.0.3130.2900：此更新发布于 2021 年 7 月，包含以下更改：
    - 一般改进和 bug 修复。
    - 修复了获取 Intune 注册的主机池路径时出现的问题。
    - 添加了日志记录，以便更好地诊断代理问题。
    - 修复了业务流程超时问题。
- 版本 1.0.3050.2500：此更新发布于 2021 年 7 月，包含以下更改：
    - 更新了代理运行状况的内部监视器。
    - 更新了堆栈运行状况的重试逻辑。
- 版本 1.0.2990.1500：此更新发布于 2021 年 4 月，包含以下更改：
    - 已更新代理错误消息。
    - 添加了阻止在 Windows 7 VM 上安装非 Windows 7 代理的例外。
    - 已更新检测信号服务逻辑。
- 版本 1.0.2944.1400：此更新发布于 2021 年 4 月，包含以下更改：
    - 在代理错误的事件查看器日志中放置了 Azure 虚拟桌面代理故障排除指南的链接。
    - 添加了额外的例外以实现更好的错误处理。
    - 添加了 WVDAgentUrlTool.exe，让客户可以检查其能访问的必需 URL。
-   版本 1.0.2866.1500：此更新发布于 2021 年 3 月，修复了堆栈运行状况检查的一个问题。
-   版本 1.0.2800.2802：此更新发布于 2021 年 3 月，包含常规改进和错误修复。
-   版本 1.0.2800.2800：此更新发布于 2021 年 3 月，修复了反向连接问题。
-   版本 1.0.2800.2700：此更新发布于 2021 年 2 月，修复了访问遭拒的业务流程问题。

## <a name="fslogix-updates"></a>FSLogix 更新

想知道 FSLogix 的最新更新吗？ 请查看 [FSLogix 的新增功能](/fslogix/whats-new)。

## <a name="september-2021"></a>2021 年 9 月

以下是 2021 年 9 月的更改。

### <a name="azure-portal-updates"></a>Azure 门户更新

现在可以将 Azure 资源管理器模板用于要在部署后应用于会话主机的任何更新。 可以通过在创建主机池时选择“虚拟机”选项卡来访问此功能。

现在还可以在创建主机池时（而不是之后）设置主机池、应用组和工作区诊断设置。 在主机池创建过程中配置这些设置时，还会自动为 Azure 虚拟桌面见解设置报告数据。

### <a name="azure-active-directory-domain-join"></a>Azure Active Directory 域加入

Azure Active Directory 域加入现已正式发布。 此服务允许你将会话主机加入 Azure Active Directory。 域加入还允许你作为 Microsoft Endpoint Manager 的一部分自动注册到 Intune。 你可以在 Azure 公有云中访问此功能，但不能在政府云或 Azure 中国访问。 有关详细信息，请参阅[我们的博客文章](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-azure-ad-joined-vms-support/ba-p/2751083)。

### <a name="azure-china"></a>Azure 中国

Azure 虚拟桌面现已在 Azure 中国云中正式发布。 有关详细信息，请参阅[我们的博客文章](https://azure.microsoft.com/updates/general-availability-azure-virtual-desktop-is-now-available-in-the-azure-china-cloud/)。

### <a name="automatic-migration-module-tool"></a>自动迁移模块工具

借助自动迁移工具，只需几个 PowerShell 命令，就可以将组织从 Azure 虚拟桌面（经典）迁移到 Azure 虚拟桌面。 此功能目前为公共预览版，你可以在[自动迁移](automatic-migration.md)中了解更多信息。

## <a name="august-2021"></a>2021 年 8 月

以下是 2021 年 8 月的更改：

### <a name="windows-11-preview-for-azure-virtual-desktop"></a>适用于 Azure 虚拟桌面的 Windows 11（预览版）

Windows 11（预览版）映像现已在 Azure 市场中提供，供客户使用 Azure 虚拟桌面进行测试和验证。 有关详细信息，请参阅[我们的公告](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/windows-11-preview-is-now-available-on-azure-virtual-desktop/ba-p/2666468)。

### <a name="multimedia-redirection-is-now-in-public-preview"></a>多媒体重定向现在提供公共预览版

多媒体重定向可让你在 Azure 虚拟桌面 Web 浏览器中观看视频时流畅地播放视频，并且可与 Microsoft Edge 和 Google Chrome 配合使用。 有关详细信息，请参阅[我们的博客文章](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/public-preview-announcing-public-preview-of-multimedia/m-p/2663244#M7692)。

### <a name="windows-defender-application-control-and-azure-disk-encryption-support"></a>Windows Defender 应用程序控制和 Azure 磁盘加密支持

Azure 虚拟桌面现在支持使用 Windows Defender 应用程序控制来控制哪些驱动程序和应用程序可在 Windows 虚拟机 (VM) 上运行，并支持通过使用 Windows BitLocker 的 Azure 磁盘加密为 VM 的操作系统和数据磁盘提供卷加密。 有关详细信息，请参阅[我们的公告](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/support-for-windows-defender-application-control-and-azure-disk/m-p/2658633#M7685)。
 
### <a name="signing-into-azure-ad-using-smart-cards-are-now-supported-in-azure-virtual-desktop"></a>Azure 虚拟桌面现在支持使用智能卡登录 Azure AD

虽然这不是 Azure AD 的新功能，但 Azure 虚拟桌面现在支持将 Active Directory 联合身份验证服务配置为使用智能卡登录。 有关详细信息，请参阅[我们的公告](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/signing-in-to-azure-ad-using-smart-cards-now-supported-in-azure/m-p/2654209#M7671)。

### <a name="screen-capture-protection-is-now-generally-available"></a>屏幕捕获保护功能现已正式发布

使用 Azure 虚拟桌面中的屏幕捕获保护功能，防止客户端终结点上运行的软件从屏幕捕获敏感信息。 有关详细信息，请参阅我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-screen-capture-protection-for/m-p/2699684)。

## <a name="july-2021"></a>2021 年 7 月

以下是 2021 年 7 月的更改：

### <a name="azure-virtual-desktop-images-now-include-optimized-teams"></a>Azure 虚拟桌面映像现在包含优化的 Teams

Azure 虚拟桌面映像库中的所有可用映像（包括 Microsoft 365 企业应用版）现在都预安装了适用于 Azure 虚拟桌面的 Teams 媒体优化版本。 有关详细信息，请参阅[我们的公告](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/media-optimization-for-microsoft-teams-now-part-of-win10/m-p/2550054#M7442)。

### <a name="azure-active-directory-domain-join-for-session-hosts-is-in-public-preview"></a>适用于会话主机的 Azure Active Directory 域加入功能提供公共预览版

现在可以将 Azure 虚拟桌面虚拟机 (VM) 直接加入 Azure Active Directory (Azure AD)。 此功能允许你使用基本凭据从任何设备连接到 VM。 你还可以向 Microsoft Endpoint Manager 自动注册 VM。 在某些情况下，这有助于消除对域控制器的需求、降低成本和简化部署。 若要了解详细信息，请参阅[在 Azure 虚拟桌面中部署加入 Azure AD 的虚拟机](deploy-azure-ad-joined-vm.md)。

### <a name="fslogix-version-2105-is-now-available"></a>FSLogix 2105 版现已推出

FSLogix 2105 版现已正式发布。 此版本包括改进的登录次数以及公共预览版（2105 版）未提供的 bug 修复。 有关更多详细信息，可以参阅 [FSLogix 发行说明](/fslogix/whats-new)和[我们的博客文章](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-fslogix-2105-2-9-7838-44263/m-p/2539491#M7412)。

### <a name="azure-virtual-desktop-in-china-has-entered-public-preview"></a>Azure 虚拟桌面已在中国推出公共预览版

随着 Azure 虚拟桌面在中国的推出，我们的全球覆盖率进一步扩大，从而可以帮助组织通过改进的性能和延迟为该区域的客户提供支持。 若要了解详细信息，请参阅[我们的公告页](https://azure.microsoft.com/updates/azure-virtual-desktop-is-now-available-in-the-azure-china-cloud-in-preview/)。
 
### <a name="the-getting-started-feature-for-azure-virtual-desktop"></a>Azure 虚拟桌面的入门功能

此功能在 Azure 门户中提供简化的载入体验，以设置 Azure 虚拟桌面环境。 你可以使用此功能，以简单轻松的方式创建满足自动化 Azure Active Directory 域服务系统需求的部署。 有关详细信息，请查看我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/getting-started-wizard-in-azure-virtual-desktop/m-p/2451385)。

### <a name="start-vm-on-connect-is-now-generally-available"></a>“连接时启动 VM”现已正式发布

“连接时启动 VM”功能现已正式发布。 此功能允许你关闭已解除分配或停止的 VM，让部署能够灵活满足用户需求，从而帮助你优化成本。 有关详细信息，请参阅[连接时启动虚拟机](start-virtual-machine-connect.md)。

### <a name="remote-app-streaming-documentation"></a>远程应用流式传输文档

针对使用 Azure 虚拟桌面将应用作为服务交付给客户和业务合作伙伴，我们最近推出了一个新的远程应用流式传输定价选项。 例如，软件供应商可以使用远程应用流式传输，将应用作为客户可访问的软件即服务 (SaaS) 解决方案交付。 若要详细了解远程应用流式传输，请查看[我们的文档](./remote-app-streaming/overview.md)。

从 2021 年 7 月 14 日到 2021 年 12 月 31 日，我们将为使用远程应用流式传输的客户提供促销优惠，允许其业务合作伙伴和客户免费访问 Azure 虚拟桌面。 此优惠仅适用于外部用户访问权限。 常规计费将于 2022 年 1 月 1 日恢复。 同时，你可以继续使用 Microsoft 365 E3 或 Windows E3 等许可证中现有的 Windows 许可证权利。 若要详细了解此优惠，请参阅 [Azure 虚拟桌面定价页](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

### <a name="new-azure-virtual-desktop-handbooks"></a>新的 Azure 虚拟桌面手册

我们最近发布了四本新手册，可帮助你在不同场景中设计和部署 Azure 虚拟桌面： 

- [应用程序管理](https://azure.microsoft.com/resources/azure-virtual-desktop-handbook-application-management/)介绍如何使应用程序交付现代化并简化 IT 管理。  
- [灾难恢复](https://azure.microsoft.com/resources/azure-virtual-desktop-handbook-disaster-recovery/)介绍如何通过制定灾难恢复策略来增强业务复原能力。  
- 借助[结合使用 Citrix Cloud 和 Azure 虚拟桌面](https://azure.microsoft.com/resources/migration-guide-citrix-cloud-with-azure-virtual-desktop/)迁移指南，从 Citrix 投资中获得更多价值。
- 借助[结合使用 VMware Horizo​​n 和 Azure 虚拟桌面](https://azure.microsoft.com/resources/migration-guide-vmware-horizon-cloud-and-azure-virtual-desktop/)迁移指南，从现有的 VMware 投资中获得更多价值。

## <a name="june-2021"></a>2021 年 6 月

以下是 2021 年 6 月的变更：

### <a name="windows-virtual-desktop-is-now-azure-virtual-desktop"></a>Windows 虚拟桌面现在是 Azure 虚拟桌面

为了更好地符合我们对灵活的云桌面和远程应用程序平台的愿景，我们已将 Windows 虚拟桌面更名为 Azure 虚拟桌面。 有关详细信息，请参阅[我们博客中的公告文章](https://azure.microsoft.com/blog/azure-virtual-desktop-the-desktop-and-app-virtualization-platform-for-the-hybrid-workplace/)。

### <a name="eu-uk-and-canada-geographies-are-now-generally-available"></a>在欧盟、英国和加拿大的地理区域现已正式发布

欧盟、英国和加拿大的元数据服务现已正式发布。 这些新地点对于美国以外的数据主权非常重要。 有关详细信息，请参阅[我们的博客文章](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-public-preview-of-azure-virtual-desktop-service/m-p/2478401#M7314)。

### <a name="the-getting-started-tool-is-now-in-public-preview"></a>入门工具现已提供公共预览版

我们创建了 Azure 虚拟桌面入门工具，使首次使用的用户能够更轻松地完成部署过程。 通过简化和自动化部署过程，我们希望此工具能帮助更多用户更快速地采用 Azure 虚拟桌面，并且使他们的访问更轻松。 有关详细信息，请参阅我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/getting-started-wizard-in-azure-virtual-desktop/m-p/2451385)。

### <a name="azure-virtual-desktop-pricing-calculator-updates"></a>Azure 虚拟桌面定价计算器更新

我们进行了一些重大更新，以改进 Azure 虚拟桌面的 Azure 定价计算器上的定价体验，其中包括：  
  
- 我们已将服务名称更新为 Azure 虚拟桌面  
- 我们还使用以下新项更新了布局：  
   - 具有托管磁盘和文件存储带宽的“存储”部分  
   - 显示每用户成本的自定义部分

可以从[本页面](https://azure.microsoft.com/pricing/calculator/)访问定价计算器。

### <a name="single-sign-on-sso-using-active-directory-federation-services-ad-fs"></a>使用 Active Directory 联合身份验证服务 (AD FS) 的单一登录 (SSO)

AD FS 单一登录功能现已正式发布。 借助此功能，客户可以使用 AD FS 为 Windows 和 Web 客户端上的用户提供单一登录体验。 有关详细信息，请参阅[为 Azure 虚拟桌面配置 AD FS 单一登录](configure-adfs-sso.md)。

## <a name="may-2021"></a>2021 年 5 月

2021 年 5 月新增功能：

### <a name="smart-card-authentication"></a>智能卡身份验证

我们现已正式发布密钥发行中心 (KDC) 代理远程桌面协议 (RDP) 属性。 这些属性对 Azure 虚拟桌面会话的 RDP 部分启用 Kerberos 身份验证，这包括允许在没有密码的情况下进行网络级别身份验证。 有关详细信息，请参阅我们的[博客文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/new-feature-smart-card-authentication-for-windows-virtual/m-p/2323226)。

### <a name="the-web-client-now-supports-file-transfer"></a>Web 客户端现在支持文件传输

从 Web 客户端的公共预览版本（版本 1.0.24.7，预览版）开始，用户现在可以在其远程会话和本地计算机之间传输文件。 若要将文件上传到远程会话，请选择 Web 客户端页顶部菜单中的“上传”图标。 若要下载文件，请在远程会话的“开始”菜单中搜索“远程桌面虚拟驱动器”。 打开虚拟驱动器后，只需将文件拖放到“下载”文件夹，浏览器就会开始将文件下载到本地计算机。

### <a name="start-vm-on-connect-support-updates"></a>“连接后启动 VM”支持更新

“连接后启动 VM”（预览）现在支持共用主机池和 Azure 政府云。 要了解详细信息，请阅读我们的[博客文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/leverage-start-vm-on-connect-for-pooled-host-pools-and-azure-gov/m-p/2349866)。

### <a name="latency-improvements-for-the-united-arab-emirates-region"></a>针对阿拉伯联合酋长国的延迟改进

我们已经在阿拉伯联合酋长国（阿联酋）部署了 Azure 控制平面，因此该地区的客户现在可以体验到更低的延迟。 有关详细信息，请参阅 [Azure 虚拟桌面路线图](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop&searchterms=64545)。

### <a name="ending-internet-explorer-11-support"></a>Internet Explorer 11 支持结束

从 2021 年 9 月 30 日开始，Azure 虚拟桌面 Web 客户端将不再支持 Internet Explorer 11。 建议你改为使用 [Microsoft Edge](https://www.microsoft.com/edge?form=MY01R2&OCID=MY01R2&r=1) 浏览器来启动 Web 客户端和远程会话。 有关详细信息，请参阅[此博客文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-web-client-to-end-support-for-internet/m-p/2369007)中的公告。

### <a name="microsoft-endpoint-manager-public-preview"></a>Microsoft Endpoint Manager 公共预览版

我们已开始在 Windows 10 企业版多会话中提供对 Microsoft Endpoint Manager 的支持，目前为公共预览版。 这项新功能使你能够用与本地设备相同的工具来管理 Windows 10 VM。 有关详细信息，请参阅 [Microsoft Endpoint Manger文档](/mem/intune/fundamentals/windows-virtual-desktop-multi-session)。

### <a name="fslogix-agent-public-preview"></a>FSLogix 代理公共预览版

我们已发布 FSLogix 代理的最新版本的公共预览版。 请查看我们的[博客文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/public-preview-fslogix-release-2105-is-now-available-in-public/m-p/2380996/thread-id/7105)，了解详细信息，要提交该表单，需要访问预览版。

### <a name="may-2021-updates-for-teams-for-azure-virtual-desktop"></a>适用于 Azure 虚拟桌面的 Teams 的 2021 年 5 月更新

对于此更新，我们解决了在共享视频时导致屏幕持续黑屏的问题。 我们还修复了会话客户端与 Teams 服务器之间的视频分辨率不匹配问题。 Azure 虚拟机上的 Teams 现在应该会根据 Teams 服务器的输入来更改分辨率和比特率。

### <a name="azure-portal-deployment-updates"></a>Azure 门户部署更新

已经对 Azure 门户中的部署过程进行了以下更新：

- 在创建新的 Azure 虚拟机会话主机 VM 时，在“映像”的下拉列表框中增加了新映像（包括 GEN2）。
- 现在可以在创建主机池时配置虚拟机的引导诊断。
- 在“高级主机池 RDP 属性”选项卡上的 RDP 代理中增加了一条工具提示。
- 在从 MSIX 包中添加应用程序时，增加了一个表示图标路径的信息气泡。
- 不能再对非托管磁盘进行托管式引导诊断。
- 更新了在 Azure 资源管理器中创建主机池的模板，因此 Azure 门户现在支持使用第三方市场的映像来创建主机池。

### <a name="single-sign-on-using-active-directory-federation-services-public-preview"></a>使用 Active Directory 联合身份验证服务公共预览版进行单点登录

我们已经开始让 Active Directory 联合服务 (AD FS) 支持按主机池的单点登录，这一功能目前处于公共预览版。 有关详细信息，请参阅[为 Azure 虚拟桌面配置 AD FS 单点登录](configure-adfs-sso.md)。 

### <a name="enterprise-scale-support"></a>企业级支持

我们发布了云采用框架的更新部分，可以实现对 Azure 虚拟桌面的企业级支持。 有关详细信息，请参阅 [Azure 虚拟桌面构造集的企业级支持](/azure/cloud-adoption-framework/scenarios/wvd/enterprise-scale-landing-zone)。

### <a name="customer-adoption-kit"></a>客户采用工具包

我们最近发布了 Azure 虚拟桌面客户采用工具包，可帮助客户和合作伙伴为其客户设置 Azure 虚拟桌面。 可在[此处](https://www.microsoft.com/azure/partners/resources/customer-adoption-kit-windows-virtual-desktop)下载工具包。

## <a name="april-2021"></a>2021 年 4 月

5 月新增功能：

### <a name="use-the-start-vm-on-connect-feature-preview-in-the-azure-portal"></a>在 Azure 门户中使用“连接后启动 VM”功能（预览版）

现在可以在 Azure 门户中配置“连接后启动 VM”功能（预览版）。 通过此更新，用户可以从 Android 和 macOS 客户端访问其 VM。 若要了解详细信息，请参阅[连接后启动 VM](start-virtual-machine-connect.md#use-the-azure-portal)。

### <a name="required-url-check-tool"></a>所需 URL 检查工具 

Azure 虚拟桌面代理版本 1.0.2944.400 包括一个用于验证 URL 的工具，并显示虚拟机是否可以访问正常运行所需的 URL。 如果任何所需的 URL 均可访问，则该工具将列出这些 URL，以便在需要的情况下取消阻止这些 URL。 详细了解我们的[安全 URL 列表](safe-url-list.md#required-url-check-tool)。

### <a name="updates-to-the-azure-portal-ui-for-azure-virtual-desktop"></a>Azure 虚拟桌面的 Azure 门户 UI 更新

下面是适用于 Azure 虚拟桌面的 Azure 门户 UI 的最新更改：

- 修复了在启用排出模式的情况下检索会话主机时出现错误的问题。
- 已将门户 SDK 升级到版本 7.161.0。
- 修复了导致“用户会话”选项卡中出现“资源 ID 丢失”错误消息的问题。
- Azure 门户现在显示会话主机的详细子状态消息。

### <a name="april-2021-updates-for-teams-on-azure-virtual-desktop"></a>2021 年 4 月 Azure 虚拟桌面上的 Teams 更新

Azure 虚拟桌面上的 Teams 的新功能：

- 增加了硬件加速，用于对基于 Windows 10 的客户端的传出视频流进行视频处理。
- 当同时使用前置摄像头和后置摄像头或外部摄像头加入会议时，默认情况下会选择前置摄像头。
- 解决了导致 Teams 在基于 x86 的计算机上崩溃的问题。
- 修复了在屏幕共享过程中导致条纹的问题。
- 解决了导致会议成员无法观看传入视频或屏幕共享的问题。

### <a name="msix-app-attach-is-now-generally-available"></a>MSIX 应用现已推出正式版

适用于 Azure 虚拟桌面的 MSIX 应用附加现已推出公共预览版，可供所有用户使用。 在[我们的 TechCommunity 公告](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-is-now-generally-available/m-p/2270468)中了解有关 MSIX 应用附加的详细信息。

### <a name="the-macos-client-now-supports-apple-silicon-and-big-sur"></a>macOS 客户端现在支持 Apple Silicon 和 Big Sur

macOS Azure 虚拟桌面客户端现在支持 Apple Silicon 和 Big Sur。 [macOS 客户端中新增功能](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)中提供更新的完整列表。

## <a name="march-2021"></a>2021 年 3 月

以下是 2021 年 3 月的更改。

### <a name="updates-to-the-azure-portal-ui-for-azure-virtual-desktop"></a>Azure 虚拟桌面的 Azure 门户 UI 更新

我们对 Azure 虚拟桌面 Azure 门户进行了一些更新：

- 为工作流启用了新的可用性选项（可用性集和区域）来创建主机池和添加 VM。
- 修复了处于“需要帮助”状态的主机显示为不可用的问题。 现在，主机旁边会出现一个警告图标。
- 已对活动会话启用排序。
- 你现在可以在“主机详细信息”选项卡上向特定用户发送消息或将其注销。
- 已更改最大会话限制字段。
- 已将 OU 验证路径添加到工作流以创建主机池。
- 你现在可以在创建个人主机池时使用最新版本的 Windows 10 映像。

### <a name="generation-2-images-and-trusted-launch"></a>第 2 代映像和受信任启动

Azure 市场现在提供适用于 Windows 10 企业版和 Windows 10 企业版多会话的第 2 代映像。 这些映像将允许使用受信任启动 VM。 若要详细了解第 2 代 VM，请参阅[我应该创建第 1 代还是第 2 代虚拟机](../virtual-machines/generation-2.md)。 若要了解如何预配 Azure 虚拟桌面受信任启动 VM，请参阅[我们的 TechCommunity 文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170)。

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix 现在预安装在 Windows 10 企业版多会话映像上

根据客户反馈，我们设置了新版本的 Windows 10 企业版多会话映像，该映像已安装了未配置的 FSLogix 的版本。 我们希望这能够帮助你更轻松地部署 Azure 虚拟桌面。

### <a name="azure-monitor-for-azure-virtual-desktop-is-now-in-general-availability"></a>Azure Monitor for Azure Virtual Desktop 现已正式发布

Azure Monitor for Azure Virtual Desktop 现在已面向公众正式发布。 此功能是一项自动化服务，可监视你的部署，使你能够在单个位置查看事件、运行状况和故障排除建议。 有关详细信息，请参阅[我们的文档](azure-monitor.md)或查看[我们的 TechCommunity 文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861)。

### <a name="march-2021-updates-for-teams-on-azure-virtual-desktop"></a>2021 年 3 月 Azure 虚拟桌面上的 Teams 更新

我们对 Azure 虚拟桌面上的 Teams 进行了以下更新：

- 提高了通话和 2x2 模式的视频质量性能。
- 使用视频处理 (XVP) 的硬件卸载，将 CPU 利用率降低了 5-10%（具体取决于 CPU 代系）。
- 旧版本的计算机现在可以使用 XVP 和硬件解码，以 2x2 模式流畅地显示更多传入的视频流。
- 已将 WebRTC 堆栈从 M74 更新为 M88，以提高 AV 同步性能并减少暂时性问题。
- 已将软件 H264 编码器替换为 OpenH264（Web 上 Teams 中使用的 OSS），这会提高传出相机的视频质量。
- 我们在 3 月 30 日为 Teams 服务器启用了 2x2 模式，供公众使用。 2x2 模式同时可显示最多四个传入视频流。

### <a name="start-vm-on-connect-public-preview"></a>连接时启动 VM 公共预览版

公共预览版中现在提供了“连接时启动 VM”这一主机池设置。 使用此设置能够根据需要随时启用 VM。 如果要节省成本，需要通过配置 Azure 计算设置来解除分配 VM。 有关详细信息，请查看[我们的博客文章](https://aka.ms/wvdstartvmonconnect)和[我们的文档](start-virtual-machine-connect.md)。

### <a name="azure-virtual-desktop-specialty-certification"></a>Azure 虚拟桌面专用认证

我们发布了 AZ-140 考试的 beta 版本，使你能够证明在 Azure 中的 Azure 虚拟桌面的专业知识。 要了解更多信息，请查看[我们的 文章](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107)。

## <a name="february-2021"></a>2021 年 2 月

以下是 2021 年 2 月的更改内容。

### <a name="portal-experience"></a>门户体验

我们通过以下方式改进了 Azure 门户体验：

- “会话主机网格”选项卡中主机上的批量排出模式。 
- MSIX 应用附加现已提供公共预览版。
- 已修复深色模式的主机池概述信息。

### <a name="eu-metadata-storage-now-in-public-preview"></a>EU 元数据存储现已提供公共预览版

我们现在正在托管作为 Azure 虚拟桌面中服务元数据的存储选项的欧洲 (EU) 地理的公共预览版。 客户在创建服务对象时可以在欧洲西部或欧洲北部之间进行选择。 主机池的服务对象和元数据将存储在与每个区域关联的 Azure 地理中。 若要了解详细信息，请参阅[我们宣布推出公共预览版的博客文章](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)。

### <a name="teams-on-azure-virtual-desktop-plugin-updates"></a>Azure 虚拟桌面上的 Teams 插件更新

我们通过解决最常见的报告问题（例如屏幕突然变暗或视频和声音不同步），提高了 Azure 虚拟桌面插件上的视频通话质量。 这些改进应能提高带有处于活动状态的发言人切换的单视频视图的性能。 我们还解决了以下问题：Teams 中无法使用具有特殊字符的硬件设备。

## <a name="january-2021"></a>2021 年 1 月

以下是 2021 年 1 月的更改内容：

### <a name="new-azure-virtual-desktop-offer"></a>新增的 Azure 虚拟桌面套餐

如果使用原生 Microsoft 解决方案，则对于 D 系列和 B 系列虚拟机，新客户可节省 30% 的 Azure 虚拟桌面计算成本（此优惠只提供 90 天）。 你可于 2021 年 3 月 31 日之前在 Azure 门户中兑换此优惠。 有关详细信息，请参阅 [Azure 虚拟桌面套餐页面](https://azure.microsoft.com/services/virtual-desktop/offer/)。

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules 值更改 

在 Azure 资源管理器嵌套模板中，我们将 networkSecurityGroupRules 的默认值从对象更改为了数组。 这将避免在未指定 networkSecurityGroupRules 的值的情况下使用 managedDisks-customimagevm.json 时出现任何错误。 这不是一项中断性变更，而且向后兼容。

### <a name="fslogix-hotfix-update"></a>FSLogix 修补程序更新

我们发布了版本 2009 HF_01 (2.9.7654.46150) 的 FSLogix，用于解决上一版本 (2.9.7621.30127) 中的问题。 建议你尽快停止使用上一版本并更新 FSLogix。

有关详细信息，请查看 [FSLogix 中的新增功能](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)中的发行说明。

### <a name="azure-portal-experience-improvements"></a>Azure 门户体验改进

我们对 Azure 门户体验进行了以下改进：

- 现在，你可直接添加本地虚拟机 (VM) 管理员凭据，无需添加使用 Active Directory 域加入帐户凭据创建的本地帐户。
- 现在，用户可在单独的选项卡中为单个用户和组列出个人和组分配情况。
- 现在，可在主机池的虚拟机概述中查看 Azure 虚拟桌面代理的版本号。
- 对主机池和应用程序组添加了批量删除功能。
- 现在，你可为主机池中的多个会话主机启用或禁用排出模式。
- 从 VM 详细信息页面中删除了公共 IP 字段。

### <a name="azure-virtual-desktop-agent-troubleshooting"></a>Azure 虚拟桌面代理故障排除

我们近来设定了 [Azure 虚拟桌面代理故障排除指南](troubleshoot-agent.md)，来帮助遇到常见问题的客户。

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender for Endpoint 集成

Microsoft Defender for Endpoint 集成现已正式发布。 此功能为 Azure 虚拟桌面 VM 提供了与本地 Windows 10 计算机相同的调查体验。 如果你使用 Windows 10 企业版多会话，Microsoft Defender for Endpoint 将支持多达 50 个并发用户连接，从而为你节省 Windows 10 企业版多会话成本并增强你使用 Microsoft Defender for Endpoint 的信心。 有关详细信息，请查看我们的[博客文章](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)。

### <a name="azure-security-baseline-for-azure-virtual-desktop"></a>Azure 虚拟桌面的 Azure 安全基线

我们最近发布了[一篇有关 Azure 虚拟桌面的 Azure 安全基线的文章](security-baseline.md)，请注意查看。 可在这些指南中了解如何将 2.0 版的 Azure 安全基准应用于 Azure 虚拟桌面。 Azure 安全基准介绍了我们建议的用于保护 Azure 上的云解决方案的设置和做法。

## <a name="december-2020"></a>2020 年 12 月

以下是 2020 年 12 月的更改内容： 

### <a name="azure-monitor-for-azure-virtual-desktop"></a>适用于 Azure 虚拟桌面的 Azure Monitor

适用于 Azure 虚拟桌面的 Azure Monitor 现在提供公共预览版。 这项新功能包括一个在 Azure Monitor 工作簿基础上构建的强大仪表板，可帮助 IT 专业人员了解其 Azure 虚拟桌面环境。 有关详细信息，请查看[博客上的公告](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587)。 

### <a name="azure-resource-manager-template-change"></a>Azure 资源管理器模板更改 

在最新更新中，我们已从 Azure 资源管理器模板中删除了用于创建和预配主机池的所有公共 IP 地址参数。 强烈建议避免对 Azure 虚拟桌面使用公共 IP，以确保部署安全。 如果你的部署依赖于公共 IP，则需要将其重新配置为使用专用 IP，否则部署将无法正常工作。

### <a name="msix-app-attach-public-preview"></a>MSIX 应用附加公共预览版 

MSIX 应用附加是另一项服务，其公共预览版已在本月推出。 .MSIX 应用附加是一项服务，它可在 Azure 虚拟桌面会话主机 VM 中动态显示 MSIX 应用程序。 有关详细信息，请查看[博客上的公告](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231)。 

### <a name="screen-capture-protection"></a>屏幕捕获保护 

本月还标志着屏幕捕获保护公共预览版的开始。 可使用此功能防止在客户端终结点上捕获敏感信息。 转到[此页面](https://aka.ms/WVDScreenCaptureProtection)来试用屏幕捕获保护。  

### <a name="built-in-roles"></a>内置角色

我们为 Azure 虚拟桌面添加了新的内置角色来实现管理权限。 有关详细信息，请参阅 [Azure 虚拟桌面的内置角色](rbac.md)。 

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

RDP Shortpath 使用点到站点和站点到站点 VPN 和 ExpressRoute 引入与 Azure 虚拟桌面会话主机的直接连接。 它还引入了 URCP 传输协议。 RDP Shortpath 旨在减少延迟和网络跃点，以改善用户体验。 有关详细信息，请参阅 [Azure 虚拟桌面 RDP Shortpath](shortpath.md)。

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization，版本 2.0.1

我们发布了 Azure 虚拟桌面 cmdlet 版本 2.0.1。 此更新包括支持管理 MSIX 应用附加的 cmdlet。 可以在 [PowerShell 库](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)中下载新版本。

### <a name="azure-advisor-updates"></a>Azure 顾问更新

现在，Azure 顾问对 Azure 虚拟桌面中的邻近性指导有新的建议，而对于深度优先负载均衡主机池中的性能优化也有新的建议。 有关详细信息，请访问 [Azure 网站](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)。

## <a name="october-2020"></a>2020 年 10 月

以下是 2020 年 10 月的更改内容：

### <a name="improved-performance"></a>提高了性能

- 我们通过减少以下 Azure 地域的连接延迟优化了性能：
    - 瑞士
    - 加拿大

现在可以使用[体验评估器](https://azure.microsoft.com/services/virtual-desktop/assessment/)来评估这些区域的用户体验质量。

### <a name="azure-government-cloud-availability"></a>Azure 政府云可用性

Azure 政府云现已正式发布。 有关详细信息，请参阅[我们的博客文章](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)。

### <a name="azure-virtual-desktop-azure-portal-updates"></a>Azure 虚拟桌面 Azure 门户更新

我们已对 Azure 虚拟桌面 Azure 门户做了一些更新：

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

- 我们为 Azure 虚拟桌面发布了 Windows 桌面客户端版本 1.2.1364。 在此更新中，我们进行了以下更改：
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

- Microsoft Store 远程桌面客户端 (v10.2.1522+) 现已正式发布！ 此版本的 Microsoft Store 远程桌面客户端与 Azure 虚拟桌面兼容。 为了改进用户体验，我们还引入了更新的 UI 流。 此更新包括流畅的设计、浅色和深色模式，以及许多其他激动人心的更改。 我们还重新将客户端编写为使用与 iOS、macOS 和 Android 客户端相同的基础远程桌面协议 (RDP) 引擎。 这样一来，我们就能以更快的速度跨所有平台提供新功能。 [下载客户端](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab)，试一试！

- 我们修复了 Teams 桌面客户端（版本 1.3.00.21759）中的问题，即客户端在聊天、频道和日历中仅显示 UTC 时区。 更新后的客户端现将显示远程会话的时区。

- Azure 虚拟桌面现包含 Azure 顾问。 通过 Azure 门户访问 Azure 虚拟桌面时，你可以查看关于优化 Azure 虚拟桌面环境的建议。 有关详细信息，请参阅 [Azure 顾问](azure-advisor.md)。

- Azure CLI 现支持 Azure 虚拟桌面 (`az desktopvirtualization`)，可帮助你自动执行 Azure 虚拟桌面部署。 如需查看扩展命令的列表，请查看 [desktopvirtualization](/cli/azure/desktopvirtualization)。

- 我们更新了部署模板，更新后的模板与 Azure 虚拟桌面 Azure 资源管理器接口完全兼容。 你可在 [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) 上找到这些模板。

- Azure 虚拟桌面 US Gov 门户现提供公共预览版。 若要了解详细信息，请参阅[公告](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)。

## <a name="july-2020"></a>2020 年 7 月  

7 月，Azure 虚拟桌面与 Azure 资源管理的集成正式发布。

以下是此新版本中所做的更改： 

- “2019 年秋季版本”现在称为“Azure 虚拟桌面（经典版）”，而“2020 年春季版本”现在称为“Azure 虚拟桌面”。 有关详细信息，请查看[此博客文章](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)。 

要详细了解新功能，请查看[此博客文章](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)。 

### <a name="autoscaling-tool-update"></a>自动缩放工具更新

自动缩放工具的最新版本（之前处于预览状态）现已正式发布。 此工具使用 Azure 自动化帐户和 Azure 逻辑应用来自动关闭和重启主机池中的会话主机 VM，从而降低基础结构成本。 有关详细信息，请参阅[使用 Azure 自动化来改变会话主机规模](set-up-scaling-script.md)。

### <a name="azure-portal"></a>Azure 门户

现在，你可以在 Windows 虚拟桌面中使用 Azure 门户执行以下操作： 

- 直接将用户分配给个人桌面会话主机  
- 更改主机池的验证环境设置 

### <a name="diagnostics"></a>诊断

我们为 Log Analytics 工作区发布了一些新的预构建查询。 要访问这些查询，请转到“类别”下的“日志”，选择“Azure 虚拟桌面”  。 有关详细信息，请参阅[将 Log Analytics 用于诊断功能](diagnostics-log-analytics.md)。

### <a name="update-for-remote-desktop-client-for-android"></a>适用于 Android 的远程桌面客户端更新

[适用于 Android 的远程桌面客户端](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)现支持 Azure 虚拟桌面连接。 从版本 10.0.7 开始，Android 客户端采用新的 UI，可改进用户体验。 客户端还与 Android 设备上的 Microsoft Authenticator 集成，以在订阅 Azure 虚拟桌面工作区时启用条件访问。  

早期版本的远程桌面客户端现在称为“远程桌面 8”。 客户端早期版本中的任何现有连接都将无缝转移到新客户端。 新客户端已重写为使用与 iOS 和 macOS 客户端相同的基础 RDP 核心引擎，可跨所有平台更快地发布新功能。 

### <a name="teams-update"></a>Teams 更新

我们对适用于 Azure 虚拟桌面的 Microsoft Teams 进行了改进。 最重要的是，Azure 虚拟桌面现在支持适用于 Windows 桌面客户端的音频和视频优化。 用户使用音频或视频进行通话和会议时，重定向可在用户之间创建直接路径，从而改善延迟。 距离越短意味着跃点越少，因此通话的视频和音频都会更流畅。 有关详细信息，请参阅[在 Azure 虚拟桌面上使用 Teams](./teams-on-avd.md)。

## <a name="june-2020"></a>2020 年 6 月

在上一个月，我们引入了带 Azure 资源管理器集成的 Azure 虚拟桌面预览版。 此更新有许多令人兴奋的新增功能，我们很乐意向你介绍。 下面是此 Azure 虚拟桌面版本的新增功能。

### <a name="azure-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Azure 虚拟桌面现已与 Azure 资源管理器集成

Azure 虚拟桌面现已集成到 Azure 资源管理器中。 在最新更新中，所有 Azure 虚拟桌面对象现均为 Azure 资源管理器资源。 此更新还集成了 Azure 基于角色的访问控制 (Azure RBAC)。 请参阅[什么是 Azure 资源管理器？](../azure-resource-manager/management/overview.md)以了解详细信息。

下面是此更改的作用：

- Azure 虚拟桌面现已与 Azure 门户集成。 这意味着可以直接在门户中管理所有内容，而无需 PowerShell、Web 应用或第三方工具。 首先，请查看教程：[使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

- 在进行此更新之前，只能将 RemoteApp 和桌面应用发布到独立用户。 使用 Azure 资源管理器，现在可以将资源发布到 Azure Active Directory 组。

- Azure 虚拟桌面的早期版本具有四个内置管理员角色，你可以将其分配给租户或主机池。 这些角色现在受限于 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。 可以将这些角色应用于每个 Azure 虚拟桌面 Azure 资源管理器对象，从而拥有完整而丰富的委派模型。

- 在此更新中，不再需要重复运行 Azure 市场或 GitHub 模板来扩展主机池。 要扩展主机池，只需在 Azure 门户中转到该主机池，然后选择“+ 添加”以部署其他会话主机即可。

- 主机池部署现已与 [Azure 共享映像库](../virtual-machines/shared-image-galleries.md)完全集成。 共享映像库是一项单独的 Azure 服务，用于存储 VM 映像定义，包括映像版本控制。 还可以使用全球复制将映像复制并发送到其他 Azure 区域，以进行本地部署。

- 过去通过 PowerShell 或诊断服务 Web 应用完成的监视功能现已移至 Azure 门户中的 Log Analytics。 现在还可以通过两个选项可视化报表。 可以运行 Kusto 查询并使用工作簿创建视觉对象报表。

- 不再需要完成 Azure Active Directory (Azure AD) 同意即可使用 Azure 虚拟桌面。 在此更新中，Azure 订阅上的 Azure AD 租户会对用户进行身份验证，并为管理员提供 Azure RBAC 控制权限。

### <a name="powershell-support"></a>PowerShell 支持

我们已在此更新中向 Azure PowerShell Az 模块添加了新的 AzWvd cmdlet。 在 .NET Core 上运行的 PowerShell Core 支持此新模块。

要安装该模块，请按照[为 Azure 虚拟桌面设置 PowerShell 模块](powershell-module.md)中的说明操作。

还可以在 [AzWvd PowerShell 参考](/powershell/module/az.desktopvirtualization/#desktopvirtualization)处查看可用命令的列表。

有关新增功能的详细信息，请参阅[我们的博客文章](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)。

### <a name="additional-gateways"></a>其他网关

我们在南非添加了新的网关群集，以降低连接延迟。

### <a name="microsoft-teams-on-azure-virtual-desktop-preview"></a>Azure 虚拟桌面上的 Microsoft Teams（预览版）

我们对适用于 Azure 虚拟桌面的 Microsoft Teams 进行了一些改进。 最重要的是，Azure 虚拟桌面现在支持通话音频和视觉对象重定向。 用户使用音频或视频进行通话时，重定向可在用户之间创建直接路径，从而改善延迟。 距离越短意味着跃点越少，因此通话的视频和音频都会更流畅。

要了解详细信息，请参阅[我们的博客文章](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)。

## <a name="next-steps"></a>后续步骤

请在 [Microsoft 365 Azure 虚拟桌面路线图](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)了解未来的计划。
