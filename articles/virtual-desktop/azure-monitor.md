---
title: 使用 Windows Virtual Desktop Monitor 预览版 - Azure
description: 如何使用 Azure Monitor for Windows Virtual Desktop。
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e9da1071686dafa003a5a49d0864b77644493344
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594456"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>使用 Azure Monitor for Windows Virtual Desktop 监视部署（预览版）

>[!IMPORTANT]
>Azure Monitor for Windows Virtual Desktop 目前以公共预览版提供。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Monitor for Windows Virtual Desktop（预览版）是基于 Azure Monitor 工作簿的仪表板，可帮助 IT 专业人员了解其 Windows 虚拟桌面环境。 本主题将指导你设置 Azure Monitor for Windows Virtual Desktop，以监视 Windows 虚拟桌面环境。

## <a name="requirements"></a>要求

在开始使用 Azure Monitor for Windows Virtual Desktop 之前，需要完成以下设置：

- 要监视的所有 Windows 虚拟桌面环境必须基于与 Azure 资源管理器兼容的最新版本的 Windows 虚拟桌面。

- 至少配置了一个 Log Analytics 工作区。

- 在 Log Analytics 工作区中启用以下各项的数据收集：
    - 任何所需的性能计数器
    - 在 Azure Monitor for Windows Virtual Desktop 中使用的任何性能计数器或事件
    - 诊断工具针对要监视的环境中所有对象提供的数据。
    - 要监视的环境中的虚拟机 (VM)。

对适用于你环境的 Azure Monitor for Windows Virtual Desktop 进行监视的任何人还需要以下读取访问权限：

- 对环境资源所在的资源组的读取访问权限。

- 对环境会话主机所在的资源组的读取访问权限

>[!NOTE]
> 拥有读取访问权限的管理员只能查看数据。 管理员需使用不同的权限在 Windows 虚拟桌面门户中管理资源。

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>打开 Azure Monitor for Windows Virtual Desktop

可通过以下方法之一打开 Azure Monitor for Windows Virtual Desktop：

- 转到 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)。

- 在 Azure 门户中搜索并选择“Windows 虚拟桌面”，然后选择“见解” 。

- 在 Azure 门户中搜索并选择“Azure Monitor”。 在“见解”下选择“见解中心”，然后在“其他”下，选择“Windows 虚拟桌面”在“Azure Monitor”页中打开仪表板   。

打开 Azure Monitor for Windows Virtual Desktop 后，根据你要监视的环境，选中一个或多个标有“订阅”、“资源组”、“主机池”和“时间范围”的复选框   。

>[!NOTE]
>Windows 虚拟桌面目前仅支持每次监视一个订阅、资源组和主机池。 如果你找不到要监视的环境，请参阅[故障排除文档](troubleshoot-azure-monitor.md)了解已知的功能请求和问题。

## <a name="set-up-with-the-configuration-workbook"></a>使用配置工作簿进行设置

如果这是你第一次打开 Azure Monitor for Windows Virtual Desktop，则需要配置 Azure Monitor for Windows Virtual Desktop 资源。 若要配置资源，请执行以下操作：

1. 在 Azure 门户中打开你的工作簿。
2. 选择“打开配置工作簿”。

配置工作簿将设置监视环境，并可让你在完成设置过程后检查配置。 如果仪表板中的项不正常显示，或者产品组发布了需要更多数据点的更新，则必须检查配置。

## <a name="host-pool-diagnostic-settings"></a>主机池诊断设置

需要针对 Windows 虚拟桌面环境中支持此功能的所有对象启用 Azure Monitor 诊断设置。

1. 在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) 中打开 Azure Monitor for Windows Virtual Desktop，然后选择“配置工作簿”。

2. 在“订阅”、“资源组”和“主机池”下选择要监视的环境  。

3. 在“主机池诊断设置”下，检查是否为主机池启用了 Windows 虚拟桌面诊断。 如果未启用，将显示一条错误消息，指出“找不到所选主机池的现有诊断配置”。 
   
   应启用下表中的设置：

    - 检查点
    - 错误
    - 管理
    - 连接
    - HostRegistration

    >[!NOTE]
    > 如果未出现该错误消息，则无需执行步骤 4。

4. 选择“配置主机池”。

5. 选择“部署”。

6. 刷新工作簿。

可以在[将 Windows 虚拟桌面诊断数据发送到 Log Analytics](diagnostics-log-analytics.md) 中详细了解如何针对 Windows 虚拟桌面环境中的所有对象启用诊断，或如何访问 Log Analytics 工作区。

## <a name="configure-log-analytics"></a>配置 Log Analytics

若要开始使用 Azure Monitor for Windows Virtual Desktop，还至少需要设置一个 Log Analytics 工作区，用于从你要监视的环境中收集数据并将其提供给工作簿。 如果已设置一个工作区，请直接转到[设置性能计数器](#set-up-performance-counters)。 若要为包含你的 Windows 虚拟桌面环境的 Azure 订阅设置新的 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。

>[!NOTE]
>这会产生 Log Analytics 的标准数据存储费用。 我们建议先选择即用即付模型，以后在扩展部署和引入更多数据时再做出调整。 有关详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="set-up-performance-counters"></a>设置性能计数器

需要在 Log Analytics 工作区中启用要按相应采样间隔收集的特定性能计数器。 监视 Windows 虚拟桌面只需使用这些性能计数器。 可以禁用所有其他性能计数器以节省成本。

如果你已启用性能计数器，现在想要删除它们，请按照[配置性能计数器](../azure-monitor/agents/data-sources-performance-counters.md)中的说明重新配置性能计数器。 尽管本文介绍的是如何添加计数器，但你也可以在同一位置删除它们。

如果你尚未设置性能计数器，可遵循以下步骤为 Azure Monitor for Windows Virtual Desktop 配置性能计数器：

1. 转到 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)，然后在窗口底部选择“配置工作簿”。

2. 在“Log Analytics 配置”下，选择你为自己的订阅设置的工作区。

3. 在“工作区性能计数器”中，将会看到监视时所需的计数器列表。 在该列表的右侧，选中“缺少的计数器”列表中的项来启用所需的计数器，以便能够开始监视工作区。

4. 选择“配置性能计数器”。

5. 选择“应用配置”。

6. 刷新配置工作簿，并继续设置环境。

每当服务已更新并需要新的监视工具时，你还可以在完成初始配置后添加新的性能计数器。 可以验证是否已启用全部所需的计数器，方法是在“缺少的计数器”列表中将其选中。

>[!NOTE]
>输入延迟性能计数器仅与 Windows 10 RS5 和更高版本或者 Windows Server 2019 和更高版本兼容。

若要详细了解如何手动添加要收集的但尚未启用的性能计数器，请参阅[配置性能计数器](../azure-monitor/agents/data-sources-performance-counters.md)。

### <a name="set-up-windows-events"></a>设置 Windows 事件

接下来，需要在 Log Analytics 工作区中启用要收集的特定 Windows 事件。 Azure Monitor for Windows Virtual Desktop 只需要本部分所述的事件。 可以禁用所有其他事件以节省成本。

若要设置 Windows 事件，请执行以下操作：

1. 如果你已启用 Windows 事件，现在想要删除它们，请在使用配置工作簿启用监视时所需的设置之前，删除不需要的事件。

2. 在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) 中转到“Azure Monitor for Windows Virtual Desktop”，然后选择窗口底部的“配置工作簿”。

3. 在“Windows 事件配置”中，提供了监视时所需的 windows 事件列表。 该列表的右侧是“缺少的事件”列表，在其中可以找到当前未为工作区启用的所需事件名称和事件类型。 请记下其中每个名称供稍后使用。

4. 选择“打开工作区配置”。

5. 选择“**数据**”。

6. 选择“Windows 事件日志”。

7. 添加在步骤 3 中记下的缺失事件名称，以及每个事件的所需事件类型。

8. 刷新配置工作簿，并继续设置环境。

如果监视工具更新需要启用新事件，可以在完成初始配置后添加新的 Windows 事件。 为确保已启用全部所需事件，请返回到“缺少的事件”列表，并启用其中列出的所有缺失事件。

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>在所有主机上安装 Log Analytics 代理

最后，需要在主机池中的所有主机上安装 Log Analytics 代理，以将主机中的数据发送到所选的工作区。

若要安装 Log Analytics 代理，请执行以下操作：

1. 在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) 中转到“Azure Monitor for Windows Virtual Desktop”，然后选择窗口底部的“配置工作簿”。

2. 如果不为主机池中的所有主机配置 Log Analytics，则 Log Analytics 配置部分的底部会出现错误，并显示消息“主机池中的某些主机未向所选 Log Analytics 工作区发送数据”。 选择“将主机添加到工作区”以添加所选的主机。 如果未出现该错误消息，请到此停止。

3. 刷新配置工作簿。

>[!NOTE]
>主机需处于运行状态才能安装 Log Analytics 扩展。 如果自动部署在某台主机上失败，你始终可以在主机上手动安装该扩展。 若要了解如何手动安装该扩展，请参阅[适用于 Windows 的 Log Analytics 虚拟机扩展](../virtual-machines/extensions/oms-windows.md)。

## <a name="optional-configure-alerts"></a>可选：配置警报

可将 Azure Monitor for Windows Virtual Desktop 配置为当所选订阅中发生任何严重 Azure Monitor 警报时向你发送通知。 为此，请按照[通过 Azure Monitor 警报对事件做出响应](../azure-monitor/alerts/tutorial-response.md)中的说明进行操作。

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据来改进服务的质量、安全性和完整性。

为了提供准确高效的故障排除功能，收集的数据包括门户会话 ID、Azure Active Directory 用户 ID，以及发生事件的门户选项卡的名称。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://privacy.microsoft.com/privacystatement)。

>[!NOTE]
>若要了解如何查看或删除服务收集的个人数据，请参阅[针对 GDPR 的 Azure 数据主体请求](/microsoft-365/compliance/gdpr-dsr-azure)。 有关 GDPR 的详细信息，请参阅[服务信任门户的 GDPR 部分](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)。

## <a name="next-steps"></a>后续步骤

在 Azure 门户中配置 Windows 虚拟桌面后，接下来可以参阅以下可能对你有用的资源：

- 查看[词汇表](azure-monitor-glossary.md)，详细了解与 Azure Monitor for Windows Virtual Desktop 相关的术语和概念。
- 如果遇到问题，请查看[故障排除指南](troubleshoot-azure-monitor.md)以获取帮助。