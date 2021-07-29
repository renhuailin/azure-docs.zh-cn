---
title: 使用 Azure 安全中心随附的 Microsoft Defender for Endpoint 许可
description: 了解 Microsoft Defender for Endpoint 并从 Azure 安全中心进行部署。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/10/2021
ms.author: memildin
ms.openlocfilehash: df7d3d880161895b6cc883a15f7adf2def839a53
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112062255"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>通过安全中心的集成式 EDR 解决方案 Microsoft Defender for Endpoint 来保护终结点

Microsoft Defender for Endpoint 是一种整体的、云交付的终结点安全解决方案。 主要功能如下：

- 基于风险的漏洞管理和评估 
- 攻击面减少
- 基于行为的、由云提供支持的保护
- 终结点检测和响应 (EDR)
- 自动调查和补救
- 托管搜寻服务

> [!TIP]
> 这款终结点检测和响应 (EDR) 产品最初以 Windows Defender ATP 的名称推出，在 2019 年重命名为 Microsoft Defender ATP 。
>
> 在 Ignite 2020，我们推出了 [Microsoft Defender XDR 套件](https://www.microsoft.com/security/business/threat-protection)，并将这款 EDR 组件重命名为 Microsoft Defender for Endpoint。


## <a name="availability"></a>可用性

| 方面                          | 详细信息                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：                  | 正式发布版 (GA)                                                                                                                                                                                                                                                                                      |
| 定价：                        | 需要[用于服务器的 Azure Defender](defender-for-servers-introduction.md)                                                                                                                                                                                                                                             |
| 支持的平台：            |  • 运行 Windows 的 Azure 计算机<br> • 运行 Windows 的 Azure Arc 计算机|
| 用于检测的受支持 Windows 版本：  |  • Windows Server 2019、2016、2012 R2 和 2008 R2 SP1<br> • [Windows 虚拟桌面 (WVD)](../virtual-desktop/overview.md)<br> • [Windows 10 企业版多会话](../virtual-desktop/windows-10-multisession-faq.yml)（前身为企业版虚拟桌面 (EVD)）|
| 不支持的操作系统：  |  • Windows 10（EVD 或 WVD 除外）<br> • Linux|
| 所需角色和权限： | 启用/禁用集成：“安全管理员”或“所有者” <br>查看安全中心内的 MDATP 警报：“安全读取者”、“读取者”、“资源组参与者”、“资源组所有者”、“安全管理员”、“订阅所有者”或“订阅参与者”      |
| 云：                         | ![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) China Gov，其他 Gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>安全中心内的 Microsoft Defender for Endpoint 功能

Microsoft Defender for Endpoint 提供：

- 高级入侵后检测传感器。 适用于 Windows 计算机的 Defender for Endpoint 传感器可收集大量行为信号。

- 基于分析的、由云提供支持的入侵后检测。 Defender for Endpoint 可快速应对不断变化的威胁。 它使用高级分析和大数据。 Defender for Endpoint 借助 Intelligent Security Graph 的强大功能得以增强，并结合 Windows、Azure 和 Office 中的信号来检测未知威胁。 它提供可以采取措施的警报，并可让你快速做出响应。

- **威胁智能**。 Defender for Endpoint 在识别攻击者工具、方法和过程时生成警报。 它使用 Microsoft 威胁猎人和安全团队生成的，并由合作伙伴提供的情报补充的数据。

通过将 Defender for Endpoint 与安全中心集成，可受益于以下附加功能：

- 自动加入。 安全中心会自动为安全中心监视的所有 Windows 服务器启用 Microsoft Defender for Endpoint 传感器。

- 单一虚拟管理平台。 安全中心控制台显示 Microsoft Defender for Endpoint 警报。 若要进一步调查，请使用 Microsoft Defender for Endpoint 本身的门户页面，其中提供其他信息，如警报流程树和事件图。 此外，还可以看到详细的机器时间线，其中显示了最长六个月的历史时段的每种行为。

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender for Endpoint 自带安全中心" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>对 Microsoft Defender for Endpoint 租户有哪些要求？

使用 Azure 安全中心监视服务器时，系统会自动创建 Microsoft Defender for Endpoint 租户。 

- **位置：** Defender for Endpoint 收集的数据存储在租户所在的地理位置（在预配期间确定）。 客户数据（采用假名）也可能存储在美国的中央存储和处理系统中。 配置位置后，无法对其进行更改。 如果自己有 Microsoft Defender for Endpoint 许可，并且需要将数据移动到其他位置，请联系 Microsoft 支持部门重置租户。
- **移动订阅：** 如果在 Azure 租户之间移动了 Azure 订阅，还需要执行一些手动预备步骤，然后安全中心才会部署 Defender for Endpoint。 有关完整的详细信息，请[联系 Microsoft 支持人员](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>启用 Microsoft Defender for Endpoint 集成

### <a name="prerequisites"></a>先决条件

确认计算机满足 Defender for Endpoint 的必需要求：

1. 确保已根据需要将计算机连接到 Azure：

    - 对于 Windows 服务器，请按照[配置设备代理和 Internet 连接设置](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)中所述配置网络设置
    - 对于本地计算机，请将其连接到 Azure Arc，如[将混合计算机连接到已启用 Azure Arc 的服务器](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)中所述
    - 对于 Windows Server 2019 和 [Windows 虚拟桌面 (WVD)](../virtual-desktop/overview.md) 计算机，请确认计算机具有 MicrosoftMonitoringAgent 扩展。
    
1. 启用适用于服务器的 Azure Defender。 请参阅[快速入门：启用 Azure Defender](enable-azure-defender.md)。
1. 如果已在服务器上许可并部署了 Microsoft Defender for Endpoint，请使用[脱离 Windows 服务器](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)中所述的过程将其删除。
1. 如果已在 Azure 租户之间移动订阅，还需要执行一些手动预备步骤。 有关完整的详细信息，请[联系 Microsoft 支持人员](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。


### <a name="enable-the-integration"></a>启用集成
1. 从安全中心的菜单中选择“定价和设置”，然后选择要更改的订阅。
1. 选择“集成”。
1. 选择“允许 Microsoft Defender for Endpoint 访问我的数据”，然后选择“保存” 。

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="启用 Azure 安全中心与 Microsoft 的 EDR 解决方案 Microsoft Defender for Endpoint 的集成":::

    Azure 安全中心会自动将服务器加入 Microsoft Defender for Endpoint。 加入过程可能最多需要 24 小时。


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>访问 Microsoft Defender for Endpoint 门户

1. 确保用户帐户具有必需权限。 有关详细信息，请参阅[向 Microsoft Defender 安全中心分配用户访问权限](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)。

1. 检查代理或防火墙是否阻止匿名流量。 Defender for Endpoint 传感器从系统上下文进行连接，因此必须允许匿名流量。 若要确保访问 Defender for Endpoint 门户不受阻碍，请按照[在代理服务器中启用对服务 URL 的访问](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)中的说明进行操作。

1. 打开 [Microsoft Defender 安全中心门户](https://securitycenter.windows.com/)。 有关该门户的功能和图标的详细信息，请参阅 [Microsoft Defender 安全中心门户概述](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)。 

## <a name="send-a-test-alert"></a>发送测试警报

若要生成良性的 Microsoft Defender for Endpoint 测试警报，请执行以下操作：

1. 创建文件夹“C:\test-MDATP-test”。
1. 使用远程桌面访问计算机。
1. 打开命令行窗口。
1. 在提示符下，复制并运行以下命令。 命令提示符窗口将自动关闭。

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="命令提示符窗口，其中显示用于生成测试警报的命令。":::

1. 如果该命令成功，Azure 安全中心仪表板和 Microsoft Defender for Endpoint 门户中会显示一条新警报。 此警报可能要在几分钟之后才显示。
1. 若要在安全中心查看该警报，请转到“安全警报” > “可疑的 PowerShell 命令行” 。
1. 在调查窗口中，选择相应的链接转到 Microsoft Defender for Endpoint 门户。

    > [!TIP]
    > 此警报由“信息”严重性触发。

## <a name="faq---security-centers-integration-with-microsoft-defender-for-endpoint"></a>常见问题解答 - 安全中心与 Microsoft Defender for Endpoint 的集成

- [我的计算机上运行的“MDE.Windows”扩展是什么？](#whats-this-mdewindows-extension-running-on-my-machine)
- [Microsoft Defender for Endpoint 有哪些许可要求？](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [如果我已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [如何从第三方 EDR 工具进行切换？](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="whats-this-mdewindows-extension-running-on-my-machine"></a>我的计算机上运行的“MDE.Windows”扩展是什么？

过去，Microsoft Defender for Endpoint 由 Log Analytics 代理预配。 [扩展支持以包括 Windows Server 2019](release-notes.md#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga) 时，我们还添加了一个扩展来执行自动载入。 

安全中心会自动将 MDE.Windows 扩展部署到运行以下内容的计算机：

- Windows Server 2019 
- Windows 10 虚拟桌面 (WVD)
- 其他版本的 Windows Server，前提是安全中心无法识别操作系统版本（例如，使用自定义 VM 映像时）。 在这种情况下，Microsoft Defender for Endpoint 仍由 Log Analytics 代理预配。

> [!TIP]
> 如果你删除 MDE。Windows 扩展，系统不会删除 Microsoft Defender for Endpoint。 有关登出，请参阅[登出 Windows 服务器](/microsoft-365/security/defender-endpoint/configure-server-endpoints?view=o365-worldwide)。

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint 有哪些许可要求？
适用于服务器的 Azure Defender 附带的 Defender for Endpoint 无需额外付费。 或者，可以为 50 台及以上数量的计算机单独购买许可。

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>如果我已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？
如果你已获得 Microsoft Defender for Endpoint 的许可证，则无需为 Azure Defender 许可证的相应部分付费。

若要请求折扣，请与安全中心的支持团队联系，并提供相关的工作区 ID、区域以及为给定工作区中的计算机应用的 Microsoft Defender for Endpoint 许可证数。

该折扣将从批准之日起生效，不具追溯效力。

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>如何从第三方 EDR 工具进行切换？
有关从非 Microsoft 终结点解决方案进行切换的完整说明，请参阅 Microsoft Defender for Endpoint 文档：[迁移概述](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)。
  


## <a name="next-steps"></a>后续步骤

- [Platforms and features supported by Azure Security Center](security-center-os-coverage.md)（Azure 安全中心支持的平台和功能）
- [管理 Azure 安全中心的安全建议](security-center-recommendations.md)：了解建议如何帮助你保护 Azure 资源。
