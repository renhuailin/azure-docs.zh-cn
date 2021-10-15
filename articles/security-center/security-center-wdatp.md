---
title: 使用 Azure 安全中心随附的 Microsoft Defender for Endpoint 许可
description: 了解 Microsoft Defender for Endpoint 并从 Azure 安全中心进行部署。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/16/2021
ms.author: memildin
ms.openlocfilehash: a57a94b8ee0bb91deb0b15a3da0265af15dbc3a3
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536295"
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

| 方面                                       | 详细信息                                                                                                                                                                                                                                                                               |
|----------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：                               | • 与适用于 Windows 的 Defender for Endpoint 集成 - 正式发布 (GA)<br> • 与适用于 Linux 的 Defender for Endpoint 集成 - 预览版                                                                                                                                     |
| 定价：                                     | 需要[用于服务器的 Azure Defender](defender-for-servers-introduction.md)                                                                                                                                                                                                           |
| 支持的环境：                      | :::image type="icon" source="./media/icons/yes-icon.png"::: 运行 Windows/Linux 的 Azure Arc 计算机<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 运行 Linux 的 Azure VM（[支持的版本](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux)）<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 运行 Windows Server 2022、2019、2016、2012 R2、2008 R2 SP1、[Windows 虚拟桌面 (WVD)](../virtual-desktop/overview.md)、[Windows 10 企业版多会话](../virtual-desktop/windows-10-multisession-faq.yml)（前身为“企业版虚拟桌面”(EVD)）的 Azure VM<br>:::image type="icon" source="./media/icons/no-icon.png"::: 运行 Windows 10（除 EVD 或 WVD 以外）的 Azure VM           |
| 所需角色和权限：              | • 启用/禁用集成：“安全管理员”或“所有者” <br>• 查看安全中心内的 Defender for Endpoint 警报：“安全读取者”、“读取者”、“资源组参与者”、“资源组所有者”、“安全管理员”、“订阅所有者”或“订阅参与者”       |
| 云：                                      | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 政府<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure 中国世纪互联                                                         |
|                                              |                                                                                                                                                                                                                                                                                       |

## <a name="benefits-of-integrating-microsoft-defender-for-endpoint-with-security-center"></a>将 Microsoft Defender for Endpoint 与安全中心集成的优势

Microsoft Defender for Endpoint 提供：

- 高级入侵后检测传感器。 Defender for Endpoint 传感器可收集计算机中的大量行为信号。

- 基于分析的、由云提供支持的入侵后检测。 Defender for Endpoint 可快速应对不断变化的威胁。 它使用高级分析和大数据。 Defender for Endpoint 借助 Intelligent Security Graph 的强大功能得以增强，并结合 Windows、Azure 和 Office 中的信号来检测未知威胁。 它提供可以采取措施的警报，并可让你快速做出响应。

- **威胁智能**。 Defender for Endpoint 在识别攻击者工具、方法和过程时生成警报。 它使用 Microsoft 威胁猎人和安全团队生成的，并由合作伙伴提供的情报补充的数据。

通过将 Defender for Endpoint 与安全中心集成，可受益于以下额外功能：

- 自动加入。 安全中心会在连接安全中心的所有支持的计算机上自动启用 Defender for Endpoint 传感器。

- 单一虚拟管理平台。 安全中心门户页面会显示 Defender for Endpoint 警报。 若要进一步调查，请使用 Microsoft Defender for Endpoint 本身的门户页面，其中提供其他信息，如警报流程树和事件图。 此外，还可以看到详细的机器时间线，其中显示了最长六个月的历史时段的每种行为。

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender for Endpoint 自带安全中心" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>对 Microsoft Defender for Endpoint 租户有哪些要求？

使用安全中心监视计算机时，系统会自动创建 Defender for Endpoint 租户。

- **位置：** Defender for Endpoint 收集的数据存储在租户所在的地理位置（在预配期间确定）。 客户数据（采用假名）也可能存储在美国的中央存储和处理系统中。 配置位置后，无法对其进行更改。 如果自己有 Microsoft Defender for Endpoint 许可，并且需要将数据移动到其他位置，请[联系 Microsoft 支持部门](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)重置租户。
- **移动订阅：** 如果在 Azure 租户之间移动了 Azure 订阅，还需要执行一些手动预备步骤，然后安全中心才会部署 Defender for Endpoint。 有关完整的详细信息，请[联系 Microsoft 支持人员](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>启用 Microsoft Defender for Endpoint 集成

### <a name="prerequisites"></a>先决条件

确认计算机满足 Defender for Endpoint 的必需要求：

1. 确保已根据需要将计算机连接到 Azure 和 Internet：

    - **Azure 虚拟机（Windows 或 Linux）** ：请按照配置设备代理和 Internet 连接设置：[Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet) 或 [Linux](/microsoft-365/security/defender-endpoint/linux-static-proxy-configuration) 中所述配置网络设置。

    - **本地计算机**：请将目标计算机连接到 Azure Arc，如 [将混合计算机连接到已启用 Azure Arc 的服务器](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)中所述。

1. 启用适用于服务器的 Azure Defender。 请参阅[快速入门：启用 Azure Defender](enable-azure-defender.md)。

1. 如果已在 Azure 租户之间移动订阅，还需要执行一些手动预备步骤。 有关完整的详细信息，请[联系 Microsoft 支持人员](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。




### <a name="enable-the-integration"></a>启用集成

### <a name="windows"></a>[**Windows**](#tab/windows)

1. 在安全中心的菜单中，选择“定价与设置”，然后选择包含要接收 Defender for Endpoint 的 Windows 计算机的订阅。

1. 选择“集成”。

1. 选择“允许 Microsoft Defender for Endpoint 访问我的数据”，然后选择“保存” 。

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="启用 Azure 安全中心与 Microsoft 的 EDR 解决方案 Microsoft Defender for Endpoint 的集成":::

    Azure 安全中心会自动将你的计算机加入 Microsoft Defender for Endpoint。 加入过程可能最多需要 24 小时。

### <a name="linux-preview"></a>[**Linux**（预览版）](#tab/linux)

在预览期间，你将通过以下两种方式之一将 Defender for Endpoint 部署到 Linux 计算机 - 具体取决于是否已将其部署到你的 Windows 计算机：

- [适用于 Windows 的 Azure Defender 和 Microsoft Defender for Endpoint 现有用户](#existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows)
- [从未启用过与适用于 Windows 的 Microsoft Defender for Endpoint 的集成的新用户](#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)


### <a name="existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows"></a>适用于 Windows 的 Azure Defender 和 Microsoft Defender for Endpoint 现有用户

如果已启用与适用于 Windows 的 Defender for Endpoint 的集成，可以完全控制何时以及是否将 Defender for Endpoint 部署到 Linux 计算机 。

1. 在安全中心的菜单中，选择“定价与设置”，然后选择包含要接收 Defender for Endpoint 的 Linux 计算机的订阅。

1. 选择“集成”。 如果按下图所示已选择“允许 Microsoft Defender for Endpoint 访问我的数据”复选框，则可知集成已启用：

    :::image type="content" source="./media/security-center-wdatp/integration-enabled.png" alt-text="已启用 Azure 安全中心与 Microsoft Defender for Endpoint 的 Microsoft 的 EDR 解决方案的集成":::

    > [!NOTE]
    > 如果未选择，请按[从未启用过与适用于 Windows 的 Microsoft Defender for Endpoint 的集成的新用户](#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)中的说明操作。

    在此预览版中，以下复选框下面新增了一个按钮“为 Linux 计算机启用”：

    :::image type="content" source="./media/security-center-wdatp/deploy-to-linux.png" alt-text="预览版引入了一个按钮，用于手动控制部署适用于 Linux 的 Defender for Endpoint 的时间":::

1. 将 Linux 计算机添加到集成

    1. 选择“为 Linux 计算机启用”。
    1. 选择“保存”。
    1. 在确认提示中，验证信息，如果想继续操作，可选择“启用”。 

    :::image type="content" source="./media/security-center-wdatp/enable-for-linux-result.png" alt-text="确认 Azure 安全中心与适用于 Linux 的 Microsoft Defender for Endpoint 的 Microsoft 的 EDR 解决方案的集成":::

    Azure 安全中心将执行以下操作：

    - 自动将 Linux 计算机载入 Defender for Endpoint
    - 忽略运行其他基于 fanotify 的解决方案的任何计算机（详见 [Linux 系统要求中](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux#system-requirements)规定的 `fanotify` 内核选项）
    - 检测任何以前安装的 Defender for Endpoint，并将其重新配置为与安全中心集成

    加入过程可能最多需要 24 小时。

    > [!NOTE]
    > 下次返回到 Azure 门户的此页面时，不会显示“为 Linux 计算机启用”按钮。 若要对 Linux 禁用集成，需要同时对 Windows 禁用集成，方法是清除“允许 Microsoft Defender for Endpoint 访问我的数据”复选框并选择“保存” 。


1. 若要验证 Linux 计算机上的 Defender for Endpoint 安装情况，请在计算机上运行以下 shell 命令：

    `mdatp health`

    如果安装了 Microsoft Defender for Endpoint，会显示其运行状况：

    `healthy : true`

    `licensed: true`

    此外，在 Azure 门户中，名为 `MDE.Linux` 的计算机上会显示新的 Azure 扩展。

### <a name="new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows"></a>从未启用过与适用于 Windows 的 Microsoft Defender for Endpoint 的集成的新用户

如果从未为 Windows 启用过集成，则“允许 Microsoft Defender for Endpoint 访问我的数据”选项将启用安全中心，将 Defender for Endpoint 同时部署到 Windows 和 Linux 计算机。

1. 在安全中心的菜单中，选择“定价与设置”，然后选择包含要接收 Defender for Endpoint 的 Linux 计算机的订阅。

1. 选择“集成”。

1. 选择“允许 Microsoft Defender for Endpoint 访问我的数据”，然后选择“保存” 。

    Azure 安全中心将执行以下操作：

    - 自动将 Windows 和 Linux 计算机载入 Defender for Endpoint
    - 忽略运行其他基于 fanotify 的解决方案的任何 Linux 计算机（详见 [Linux 系统要求中](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux#system-requirements)规定的 `fanotify` 内核选项）
    - 检测任何以前安装的 Defender for Endpoint，并将其重新配置为与安全中心集成

    加入过程可能最多需要 24 小时。

1. 若要验证 Linux 计算机上的 Defender for Endpoint 安装情况，请在计算机上运行以下 shell 命令：

    `mdatp health`

    如果安装了 Microsoft Defender for Endpoint，会显示其运行状况：

    `healthy : true`

    `licensed: true`

    此外，在 Azure 门户中，名为 `MDE.Linux` 的计算机上会显示新的 Azure 扩展。
--- 

## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>访问 Microsoft Defender for Endpoint 门户

1. 确保用户帐户具有必需权限。 有关详细信息，请参阅[向 Microsoft Defender 安全中心分配用户访问权限](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)。

1. 检查代理或防火墙是否阻止匿名流量。 Defender for Endpoint 传感器从系统上下文进行连接，因此必须允许匿名流量。 若要确保访问 Defender for Endpoint 门户不受阻碍，请按照[在代理服务器中启用对服务 URL 的访问](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)中的说明进行操作。

1. 打开 [Microsoft Defender 安全中心门户](https://securitycenter.windows.com/)。 有关该门户的功能和图标的详细信息，请参阅 [Microsoft Defender 安全中心门户概述](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)。 






## <a name="send-a-test-alert"></a>发送测试警报

若要从 Defender for Endpoint 中生成良性测试警报，请选择相关的终结点操作系统的选项卡：

### <a name="windows"></a>[**Windows**](#tab/windows)

对于运行 Windows 的终结点，请执行以下操作：

1. 创建文件夹“C:\test-MDATP-test”。
1. 使用远程桌面访问计算机。
1. 打开命令行窗口。
1. 在提示符下，复制并运行以下命令。 命令提示符窗口将自动关闭。

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="命令提示符窗口，其中显示用于生成测试警报的命令。":::

    如果该命令成功，Azure 安全中心仪表板和 Microsoft Defender for Endpoint 门户中会显示一条新警报。 此警报可能要在几分钟之后才显示。
1. 若要在安全中心查看该警报，请转到“安全警报” > “可疑的 PowerShell 命令行” 。
1. 在调查窗口中，选择相应的链接转到 Microsoft Defender for Endpoint 门户。

    > [!TIP]
    > 此警报由“信息”严重性触发。


### <a name="linux"></a>[**Linux**](#tab/linux)

对于运行 Windows 的终结点，请执行以下操作：

1. 从 https://aka.ms/LinuxDIY 下载测试警报工具
1. 提取 zip 文件的内容并执行以下 shell 脚本：

    `./mde_linux_edr_diy`

    如果该命令成功，Azure 安全中心仪表板和 Microsoft Defender for Endpoint 门户中会显示一条新警报。 此警报可能要在几分钟之后才显示。

1. 若要在安全中心查看警报，请转到“安全警报” > “枚举包含敏感数据的文件” 。
1. 在调查窗口中，选择相应的链接转到 Microsoft Defender for Endpoint 门户。

    > [!TIP]
    > 此警报触发时显示为“低”严重性。

--- 



## <a name="faq---security-centers-integration-with-microsoft-defender-for-endpoint"></a>常见问题解答 - 安全中心与 Microsoft Defender for Endpoint 的集成

- [计算机上运行的这个 “MDE.Windows”/“MDE.Linux”扩展是什么？](#whats-this-mdewindows--mdelinux-extension-running-on-my-machine)
- [Microsoft Defender for Endpoint 有哪些许可要求？](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [如果已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [如何从第三方 EDR 工具进行切换？](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="whats-this-mdewindows--mdelinux-extension-running-on-my-machine"></a>计算机上运行的这个“MDE.Windows”/“MDE.Linux”扩展是什么？

过去，Microsoft Defender for Endpoint 由 Log Analytics 代理预配。 [扩展支持以包括 Windows Server 2019](release-notes-archive.md#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga) 和 Linux 时，我们还添加了一个扩展来执行自动载入。 

安全中心会自动将扩展部署到运行以下内容的计算机：

- Windows Server 2019。
- Windows 10 虚拟桌面 (WVD)。
- 其他版本的 Windows Server，前提是安全中心无法识别操作系统版本（例如，使用自定义 VM 映像时）。 在这种情况下，Microsoft Defender for Endpoint 仍由 Log Analytics 代理预配。
- Linux。

> [!IMPORTANT]
> 如果你删除 MDE。Windows 扩展，系统不会删除 Microsoft Defender for Endpoint。 有关登出，请参阅[登出 Windows 服务器](/microsoft-365/security/defender-endpoint/configure-server-endpoints)。

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint 有哪些许可要求？
适用于服务器的 Azure Defender 附带的 Defender for Endpoint 无需额外付费。 或者，可以为 50 台及以上数量的计算机单独购买许可。

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>如果已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？
如果你已获得 Microsoft Defender for Endpoint 的许可证，则无需为 Azure Defender 许可证的相应部分付费。

若要请求折扣，请[联系安全中心的支持团队](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 需要提供相关的工作区 ID、区域以及为给定工作区中的计算机应用的 Microsoft Defender for Endpoint 许可证数。

该折扣将从批准之日起生效，且不具追溯效力。

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>如何从第三方 EDR 工具进行切换？
有关从非 Microsoft 终结点解决方案进行切换的完整说明，请参阅 Microsoft Defender for Endpoint 文档：[迁移概述](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)。


## <a name="next-steps"></a>后续步骤

- [Platforms and features supported by Azure Security Center](security-center-os-coverage.md)（Azure 安全中心支持的平台和功能）
- [了解建议是如何帮助你保护 Azure 资源的](security-center-recommendations.md)
