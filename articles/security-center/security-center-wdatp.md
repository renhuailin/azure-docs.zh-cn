---
title: 使用 Azure 安全中心随附的 Microsoft Defender for Endpoint license
description: 了解 Microsoft Defender for Endpoint 并从 Azure 安全中心部署。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: memildin
ms.openlocfilehash: 519285f2bad352aa16bdc8d9a1db7a63c2eb04e5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98876387"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>利用安全中心的集成 EDR 解决方案保护终结点： Microsoft Defender for Endpoint

Microsoft Defender for Endpoint 是提供的一个全面的云终结点安全解决方案。 它的主要功能包括：

- 基于风险的漏洞管理和评估 
- 攻击面减少
- 基于行为和云驱动保护
- Endpoint 检测和响应 (EDR) 
- 自动调查和修正
- 托管搜寻服务

> [!TIP]
> 作为 **Windows DEFENDER atp** 最初启动，此终结点检测和响应 (EDR) 产品在2019中被重命名为 **Microsoft Defender atp**。
>
> 在 Ignite 2020 中，我们启动了 [Microsoft DEFENDER XDR suite](https://www.microsoft.com/security/business/threat-protection) ，此 EDR 组件已被重命名 **为 "Microsoft defender for Endpoint**"。


## <a name="availability"></a>可用性

| 方面                          | 详细信息                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：                  | 正式发布 (GA)                                                                                                                                                                                                                                                                                      |
| 定价：                        | 需要[用于服务器的 Azure Defender](security-center-pricing.md)                                                                                                                                                                                                                                             |
| 支持的平台：            | 运行 Windows 的 Azure 计算机<br>运行 Windows 的 Azure Arc 计算机|
| 受支持的 Windows 版本：  |  •安全中心支持在 Windows Server 2016、2012 R2 和 2008 R2 SP1 上检测<br> •使用此集成的服务器终结点监视已为 Office 365 GCC 客户禁用|
| 不受支持的操作系统：  |  • Windows Server 2019<br> • Windows 10<br> • Linux|
| 所需角色和权限： | 启用/禁用集成： **安全管理员** 或 **所有者**<br>在安全中心内查看 MDATP 警报： **安全读者**、 **读者**、 **资源组参与者**、 **资源组所有者**、 **安全管理员**、 **订阅所有者** 或 **订阅参与者**|
| 云：                         | ![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) China Gov，其他 Gov<br>![否](./media/icons/no-icon.png) 在全球 Azure 云中运行工作负荷的 GCC 客户                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |


## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>安全中心的 Microsoft Defender for Endpoint 功能

Microsoft Defender for Endpoint 提供：

- **高级破坏后检测传感器**。 Defender for Windows 计算机的传感器会收集大量行为信号。

- **基于分析、云供电、入侵后检测**。 Defender for Endpoint 可快速适应不断变化的威胁。 它使用高级分析和大数据。 它通过使用跨 Windows、Azure 和 Office 的信号来检测未知的威胁，从而放大了 Intelligent Security Graph 的能力。 它提供可以采取措施的警报，并可让你快速做出响应。

- **威胁智能**。 Defender for Endpoint 在识别攻击者的工具、技术和过程时生成警报。 它使用 Microsoft 威胁猎人和安全团队生成的，并由合作伙伴提供的情报补充的数据。

通过将 Defender for Endpoint 与安全中心集成，你将受益于以下附加功能：

- **自动载入**。 安全中心会自动为安全中心监视的所有 Windows 服务器启用 Microsoft Defender for Endpoint 传感器。 除了运行 Windows Server 2019 的那些服务器（必须通过本地脚本进行载入），组策略对象 (GPO) 或 [Microsoft 终结点 Configuration Manager](/mem/configmgr/) (以前的 SCCM) 。

- **单一的玻璃窗格**。 安全中心控制台显示 Microsoft Defender for Endpoint 警报。 若要进一步调查，请使用 Microsoft Defender 作为终结点的门户页面，你可以在其中看到其他信息，如警报流程树和事件图。 此外，还可以看到详细的机器时间线，其中显示了最长六个月的历史时段的每种行为。

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender for Endpoint 的安全中心" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender for Endpoint 租户位置

使用 Azure 安全中心监视你的服务器时，将自动创建 Microsoft Defender for Endpoint 租户。 由 Defender for Endpoint 收集的数据存储在租户的地理位置，如预配期间所标识。 客户数据-以 pseudonymized 的形式，也可以存储在美国中央存储和处理系统中。 

配置位置后，无法对其进行更改。 如果你有自己的 Microsoft Defender for Endpoint 许可证，并且需要将数据移至另一个位置，请联系 Microsoft 支持部门重置租户。


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>启用 Microsoft Defender for Endpoint integration

1. **为服务器启用 Azure Defender**。 请参阅 [Azure 安全中心的定价](security-center-pricing.md#enable-azure-defender)。

    > [!NOTE]
    > 若要保护启用了 Azure Arc 的计算机，请按照 [快速入门：使用启用了 Azure arc 的服务器连接混合计算机](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)中的说明进行操作。

1. 如果已在服务器上授权并部署了 Microsoft Defender for 终结点，请使用 [下架 Windows server](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)中所述的过程将其删除。
1. 在安全中心的菜单中，选择“定价和设置”。
1. 选择想要更改的订阅。
1. 选择“威胁检测”。
1. 选择 " **允许 Microsoft Defender For Endpoint 访问我的数据**"，然后选择 " **保存**"。

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="启用 Azure 安全中心与 Microsoft 的 EDR 解决方案之间的集成，Microsoft Defender for Endpoint":::

    Azure 安全中心会自动将你的服务器载入 Microsoft Defender for Endpoint。 加入过程可能最多需要 24 小时。


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>访问 Microsoft Defender for Endpoint 门户

1. 确保用户帐户具有所需的权限。 [了解详细信息](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)。

1. 检查你的代理或防火墙是否正在阻止匿名通信。 Defender for Endpoint 传感器从系统上下文进行连接，因此必须允许匿名通信。 若要确保受阻碍访问 Defender for Endpoint portal，请按照在 [代理服务器中启用对服务 url 的访问](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)中的说明进行操作。

1. 打开 [Microsoft Defender 安全中心门户](https://securitycenter.windows.com/)。 有关该门户的功能和图标的详细信息，请参阅 [Microsoft Defender 安全中心门户概述](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)。 

## <a name="send-a-test-alert"></a>发送测试警报

生成良性 Microsoft Defender for Endpoint 测试警报：

1. 创建文件夹“C:\test-MDATP-test”。
1. 使用远程桌面访问计算机。
1. 打开命令行窗口。
1. 在提示符下，复制并运行以下命令。 命令提示符窗口将自动关闭。

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="带有用于生成测试警报的命令的命令提示符窗口。":::

1. 如果此命令成功，你将在 Azure 安全中心仪表板和 Microsoft Defender for Endpoint 门户上看到新警报。 此警报可能要在几分钟之后才显示。
1. 若要在安全中心查看该警报，请转到“安全警报” > “可疑的 PowerShell 命令行” 。
1. 从调查窗口中，选择要前往 Microsoft Defender for Endpoint 门户的链接。

    > [!TIP]
    > 此警报由 **信息** 严重性触发。

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>安全中心集成的 Microsoft Defender for Endpoint 常见问题解答

- [Microsoft Defender for Endpoint 有哪些许可要求？](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [如果我已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [如何实现从第三方 EDR 工具切换？](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint 有哪些许可要求？
**Azure defender for server** 附带的 Defender for Endpoint 无需额外付费。 或者，可以单独为50或更多计算机购买。

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>如果我已有 Microsoft Defender for Endpoint 许可证，能否获得 Azure Defender 的折扣？
如果你已获得 Microsoft Defender for Endpoint 的许可证，则无需为 Azure Defender 许可证的相应部分付费。

若要确认折扣，请联系安全中心的支持团队，并提供相关工作区 ID、区域和每个相关许可证的许可证信息。

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>如何实现从第三方 EDR 工具切换？
Microsoft Defender for Endpoint 文档中提供了有关从非 Microsoft 终结点解决方案切换的完整说明： [迁移概述](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)。
  


## <a name="next-steps"></a>后续步骤

- [Platforms and features supported by Azure Security Center](security-center-os-coverage.md)（Azure 安全中心支持的平台和功能）
- [管理 Azure 安全中心的安全建议](security-center-recommendations.md)：了解建议如何帮助你保护 Azure 资源。