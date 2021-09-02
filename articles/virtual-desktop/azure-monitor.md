---
title: 使用监视器 Azure 虚拟桌面监视器 - Azure
description: 如何使用 Azure Monitor for Azure Virtual Desktop。
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b985ec9e88f4285da326fcf41e19141e61a0dd10
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113564187"
---
# <a name="use-azure-monitor-for-azure-virtual-desktop-to-monitor-your-deployment"></a>使用 Azure Monitor for Azure Virtual Desktop 监视部署

Azure Monitor for Azure Virtual Desktop 是基于 Azure Monitor 工作簿的仪表板，可帮助 IT 专业人员了解其 Azure 虚拟桌面环境。 本主题将指导你设置 Azure Monitor for Azure Virtual Desktop，以监视 Azure 虚拟桌面环境。

## <a name="requirements"></a>要求

在开始使用 Azure Monitor for Azure Virtual Desktop 之前，需要完成以下设置：

- 要监视的所有 Azure 虚拟桌面环境必须基于与 Azure 资源管理器兼容的最新版本的 Azure 虚拟桌面。
- 至少配置了一个 Log Analytics 工作区。 使用 Azure 虚拟桌面会话主机的指定 Log Analytics 工作区，以确保仅从 Azure 虚拟机部署中的会话主机收集性能计数器和事件。
- 在 Log Analytics 工作区中启用以下各项的数据收集：
    - 来自 Azure 虚拟桌面环境的诊断
    - 来自 Azure 虚拟桌面会话主机的建议性能计数器
    - 来自 Azure 虚拟桌面会话主机的建议 Windows 事件日志

 本文中所述的数据设置过程是监视 Azure 虚拟桌面所需的唯一步骤。 可以禁用向 Log Analytics 工作区发送数据的所有其他项以节省成本。

对适用于环境的 Azure Monitor for Azure Virtual Desktop 进行监视的任何人还需要以下读取访问权限：

- 对保存 Azure 虚拟桌面资源的 Azure 订阅的读取访问权限
- 对保存 Azure 虚拟桌面会话主机的订阅的资源组的读取访问权限
- 对一个或多个 Log Analytics 工作区的读取访问权限

>[!NOTE]
> 拥有读取访问权限的管理员只能查看数据。 管理员需使用不同的权限在 Azure 虚拟桌面门户中管理资源。

## <a name="open-azure-monitor-for-azure-virtual-desktop"></a>打开 Azure Monitor for Azure Virtual Desktop

可通过以下方法之一打开 Azure Monitor for Azure Virtual Desktop：

- 转到 [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi)。
- 在 Azure 门户中搜索并选择“Azure 虚拟桌面”，然后选择“见解” 。
- 在 Azure 门户中搜索并选择“Azure Monitor”。 选择“见解”下的“见解中心”，然后选择“Azure 虚拟桌面”。
打开页面后，请输入要监视的环境的“订阅”、“资源组”、“主机池”和“时间范围”。

>[!NOTE]
>Azure 虚拟桌面目前仅支持每次监视一个订阅、资源组和主机池。 如果你找不到要监视的环境，请参阅[故障排除文档](troubleshoot-azure-monitor.md)了解已知的功能请求和问题。

## <a name="log-analytics-settings"></a>Log Analytics 设置

若要开始使用 Azure Monitor for Azure Virtual Desktop，至少需要一个 Log Analytics 工作区。 使用 Azure 虚拟桌面会话主机的指定 Log Analytics 工作区，以确保仅从 Azure 虚拟机部署中的会话主机收集性能计数器和事件。 如果已设置了工作区，请直接跳到[使用配置工作簿进行设置](#set-up-using-the-configuration-workbook)。 若要设置工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。

>[!NOTE]
>这会产生 Log Analytics 的标准数据存储费用。 我们建议先选择即用即付模型，以后在扩展部署和引入更多数据时再做出调整。 有关详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="set-up-using-the-configuration-workbook"></a>使用配置工作簿进行设置

如果这是你第一次打开 Azure Monitor for Azure Virtual Desktop，则需要为 Azure 虚拟桌面环境设置 Azure Monitor。 若要配置资源，请执行以下操作：

1. 在 Azure 门户 [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi) 中打开 Azure Monitor for Azure Virtual Desktop，然后选择“配置工作簿”。
2. 在“订阅”、“资源组”和“主机池”下选择要配置的环境  。

配置工作簿将设置监视环境，并可让你在完成设置过程后检查配置。 如果仪表板中的项不正常显示，或者产品组发布了需要新设置的更新，则必须检查配置。

### <a name="resource-diagnostic-settings"></a>资源诊断设置

若要收集有关 Azure 虚拟桌面基础结构的信息，需要在 Azure 虚拟桌面主机池和工作区（这是 Azure 虚拟桌面工作区，而不是 Log Analytics 工作区）上启用多项诊断设置。 若要了解有关主机池、工作区和其他 Azure 虚拟桌面资源对象的详细信息，请参阅我们的[环境指南](environment-setup.md)。

有关 Azure 虚拟桌面诊断和支持的诊断表的详细信息，请参阅[将 Azure 虚拟桌面诊断发送到 Log Analytics](diagnostics-log-analytics.md)。

若要在配置工作簿中设置资源诊断设置，请执行以下操作： 

1. 在配置工作簿中选择“资源诊断设置”选项卡。 
2. 选择“Log Analytics 工作区”以发送 Azure 虚拟桌面诊断。 

#### <a name="host-pool-diagnostic-settings"></a>主机池诊断设置

若要使用配置工作簿中的“资源诊断设置”部分设置主机池诊断，请执行以下操作：

1. 在“主机池”下，查看是否已启用 Azure 虚拟桌面诊断。 如果未启用，将显示一条错误消息，指出“找不到所选主机池的现有诊断配置”。 需要启用以下受支持的诊断表：

    - 检查点
    - 错误
    - 管理
    - 连接
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > 如果未出现该错误消息，则无需执行步骤 2-4。

2. 选择“配置主机池”。
3. 选择“部署”。
4. 刷新配置工作簿。

#### <a name="workspace-diagnostic-settings"></a>工作区诊断设置 

若要使用配置工作簿中的“资源诊断设置”部分设置工作区诊断，请执行以下操作：

 1. 在“工作区”下，查看 Azure 虚拟桌面工作区是否启用了 Azure 虚拟桌面诊断。 如果未启用，将显示一条错误消息，指出“找不到所选工作区的现有诊断配置”。 需要启用以下受支持的诊断表：
 
    - 检查点
    - 错误
    - 管理
    - 源
    
    >[!NOTE]
    > 如果未出现该错误消息，则无需执行步骤 2-4。

2. 选择“配置工作区”。
3. 选择“部署”。
4. 刷新配置工作簿。

### <a name="session-host-data-settings"></a>会话主机数据设置

若要收集有关 Azure 虚拟桌面会话主机的信息，需在主机池中的所有会话主机上安装 Log Analytics 代理，确保会话主机发送到 Log Analytics 工作区，并配置 Log Analytics 代理设置以收集性能数据和 Windows 事件日志。

你向其发送会话主机数据的 Log Analytics 工作区不一定要与你向其发送诊断数据的工作区相同。 如果 Azure 会话主机位于 Azure 虚拟桌面环境之外，我们建议为 Azure 虚拟桌面会话主机提供指定的 Log Analytics 工作区。 

若要设置希望在其中收集会话主机数据的 Log Analytics 工作区，请执行以下操作： 

1. 在配置工作簿中选择“会话主机数据设置”选项卡。 
2. 选择要将会话主机数据发送到的“Log Analytics 工作区”。 

#### <a name="session-hosts"></a>会话主机

需要在主机池中的所有会话主机上安装 Log Analytics 代理，并将数据从这些主机发送到所选 Log Analytics 工作区。 如果没有为主机池中的所有会话主机配置 Log Analytics，将在“会话主机数据设置”顶部看到“会话主机”部分，并显示“主机池上的某些主机未向所选 Log Analytics 工作区发送数据。”消息。

>[!NOTE]
> 如果看不到“会话主机”部分或错误消息，则所有会话主机均已正确设置。 直接跳到设置[工作区性能计数器](#workspace-performance-counters)的说明。

若要使用配置工作簿设置剩余会话主机，请执行以下操作：

1. 选择“向工作区添加主机”。 
2. 刷新配置工作簿。

>[!NOTE]
>主机需处于运行状态才能安装 Log Analytics 扩展。 如果自动部署不起作用，则可以改为在主机上手动安装扩展。 若要了解如何手动安装该扩展，请参阅[适用于 Windows 的 Log Analytics 虚拟机扩展](../virtual-machines/extensions/oms-windows.md)。

#### <a name="workspace-performance-counters"></a>工作区性能计数器

你将需要启用特定性能计数器，以从会话主机收集性能信息，并将其发送到 Log Analytics 工作区。

如果你已启用性能计数器，现在想要删除它们，请按照[配置性能计数器](../azure-monitor/agents/data-sources-performance-counters.md)中的说明进行操作。 可以在同一位置添加和删除性能计数器。

若要使用配置工作簿设置性能计数器，请执行以下操作： 

1. 在配置工作簿中的“工作区性能计数器”下，检查“已配置的计数器”，查看已启用发送到 Log Analytics 工作区的计数器。 检查“缺少的计数器”以确保已启用所有所需的计数器。
2. 如果缺少计数器，请选择“配置性能计数器”。
3. 选择“应用配置”。
4. 刷新配置工作簿。
5. 通过选中“缺少的计数器”列表确保所有必需的计数器都已启用。 

#### <a name="configure-windows-event-logs"></a>配置 Windows 事件日志

还需要启用特定的 Windows 事件日志来收集会话主机中的错误、警告和信息，并将其发送到 Log Analytics 工作区。

如果已启用 Windows 事件日志，现在想要删除它们，请按照[配置 Windows 事件日志](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)中的说明进行操作。  可以在同一位置添加和删除 Windows 事件日志。

若要使用配置工作簿设置 Windows 事件日志，请执行以下操作：

1. 在“Windows 事件日志配置”下，检查“已配置的事件日志”以查看已启用发送到 Log Analytics 工作区的事件日志。 检查“缺少的事件日志”，确保已启用所有 Windows 事件日志。
2. 如果有缺少的 Windows 事件日志，请选择“配置事件”。
3. 选择“部署”。
4. 刷新配置工作簿。
5. 通过选中“缺少的事件日志”列表，确保所有必需的 Windows 事件日志均已启用。 

>[!NOTE]
>如果自动事件部署失败，请在配置工作簿中选择“打开代理配置”以手动添加任何缺少的 Windows 事件日志。 

## <a name="optional-configure-alerts"></a>可选：配置警报

Azure Monitor for Azure Virtual Desktop 允许你在 Azure 虚拟桌面数据的上下文中监视所选订阅中发生的 Azure Monitor 警报。 Azure Monitor 警报是 Azure 订阅上的可选功能，需要从 Azure Monitor for Azure Virtual Desktop 中单独对其进行设置。 可以使用 Azure Monitor 警报框架对 Azure 虚拟桌面事件、诊断和资源设置自定义警报。 有关警报的详细信息，请参阅 [Azure Monitor 日志警报](../azure-monitor/alerts/alerts-log.md)。

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据来改进服务的质量、安全性和完整性。

为了提供准确高效的故障排除功能，收集的数据包括门户会话 ID、Azure Active Directory 用户 ID，以及发生事件的门户选项卡的名称。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://privacy.microsoft.com/privacystatement)。

>[!NOTE]
>若要了解如何查看或删除服务收集的个人数据，请参阅[针对 GDPR 的 Azure 数据主体请求](/microsoft-365/compliance/gdpr-dsr-azure)。 有关 GDPR 的详细信息，请参阅[服务信任门户的 GDPR 部分](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)。

## <a name="next-steps"></a>后续步骤

现在，你已为 Azure 虚拟桌面环境配置了 Azure Monitor，下面提供了一些资源，可帮助你开始监视环境：

- 查看[词汇表](azure-monitor-glossary.md)，详细了解与 Azure Monitor for Azure Virtual Desktop 相关的术语和概念。
- 若要估计、度量和管控数据存储成本，请参阅[估计 Azure Monitor 成本](azure-monitor-costs.md)。
- 如果遇到问题，请查看[故障排除指南](troubleshoot-azure-monitor.md)，获取帮助并了解已知问题。
- 若要查看每个版本更新的新增功能，请参阅 [Azure 虚拟桌面 Azure Monitor 的新增功能](whats-new-azure-monitor.md)。