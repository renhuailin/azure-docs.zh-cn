---
title: 使用监视器 Windows 虚拟桌面监视器预览版-Azure
description: 如何对 Windows 虚拟桌面使用 Azure Monitor。
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e0be6decf28fcbb2edacd5019f567d26403b1f31
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466356"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>使用 Windows 虚拟桌面 Azure Monitor 来监视部署 (预览版) 

>[!IMPORTANT]
>Windows 虚拟桌面 Azure Monitor 当前提供公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虚拟桌面 (预览版) 的 Azure Monitor 是一种基于 Azure Monitor 工作簿构建的仪表板，可帮助 IT 专业人员了解其 Windows 虚拟桌面环境。 本主题将指导你如何设置 Windows 虚拟桌面的 Azure Monitor，以监视 Windows 虚拟桌面环境。

## <a name="requirements"></a>要求

开始使用 Windows 虚拟桌面 Azure Monitor 之前，需要设置以下各项：

- 所监视的所有 Windows 虚拟桌面环境都必须基于与 Azure 资源管理器兼容的最新版本的 Windows 虚拟桌面。

- 至少一个配置 Log Analytics 工作区。

- 在 Log Analytics 工作区中启用以下内容的数据收集：
    - 任何所需的性能计数器
    - Windows 虚拟桌面 Azure Monitor 中使用的任何性能计数器或事件
    - 用于监视环境中所有对象的诊断工具的数据。
    - 虚拟机在要监视的环境中)  (Vm。

监视适用于你环境的 Windows 虚拟桌面 Azure Monitor 的任何人还需要以下读取访问权限：

- 对环境资源所在的资源组的读取访问权限。

- ) 环境的会话主机所在的资源组 (的读取权限

>[!NOTE]
> "读取" 访问权限仅允许管理员查看数据。 它们需要不同的权限来管理 Windows 虚拟桌面门户中的资源。

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>打开 Windows 虚拟桌面 Azure Monitor

可以通过以下方法之一打开 Windows 虚拟桌面 Azure Monitor：

- 请参阅 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)。

- 从 "Azure 门户搜索并选择" **Windows 虚拟桌面** "，然后选择" **Insights**"。

- 搜索并从 Azure 门户中选择 **Azure Monitor** 。 在 "**见解**" 下选择 " **insights 中心**"，然后在 "**其他** 选择 **Windows 虚拟桌面**" 下打开 "Azure Monitor" 页中的仪表板。

为 Windows 虚拟桌面打开 Azure Monitor 后，选择一个或多个标记为 " **订阅**"、" **资源组**"、" **主机池**" 的复选框，并根据要监视的环境选择 " **时间范围** "。

>[!NOTE]
>Windows 虚拟桌面目前只支持一次监视一个订阅、资源组和主机池。 如果找不到要监视的环境，请参阅 [我们的疑难解答文档](troubleshoot-azure-monitor.md) 了解已知的功能请求和问题。

## <a name="set-up-with-the-configuration-workbook"></a>设置配置工作簿

如果这是你第一次打开 Windows 虚拟桌面 Azure Monitor，则需要为 Windows 虚拟桌面资源配置 Azure Monitor。 配置资源：

1. 在 Azure 门户中打开工作簿。
2. 选择 **"打开配置工作簿"**。

配置工作簿设置你的监视环境，并允许你在完成设置过程后检查配置。 如果仪表板中的项目显示不正确，或者产品组发布需要其他数据点的更新，则必须检查您的配置。

## <a name="host-pool-diagnostic-settings"></a>主机池诊断设置

需要对支持此功能的 Windows 虚拟桌面环境中的所有对象启用 Azure Monitor 诊断设置。

1. 在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)上打开 Windows 虚拟桌面 Azure Monitor，然后选择 " **配置工作簿**"。

2. 选择要在 " **订阅**"、" **资源组**" 和 " **主机池**" 下监视的环境。

3. 在 " **主机池诊断设置**" 下，查看是否为主机池启用了 Windows 虚拟桌面诊断。 否则，将显示一条错误消息，显示 "未找到所选主机池的现有诊断配置"。 
   
   应启用以下表：

    - 检查点
    - 错误
    - 管理
    - 连接
    - HostRegistration

    >[!NOTE]
    > 如果看不到错误消息，则无需执行步骤4。

4. 选择 " **配置主机池**"。

5. 选择“部署”。

6. 刷新工作簿。

可在 Windows 虚拟桌面环境中的所有对象上了解有关如何启用诊断的详细信息，或访问将 [Windows 虚拟桌面诊断发送到 Log Analytics](diagnostics-log-analytics.md)中的 Log Analytics 工作区。

## <a name="configure-log-analytics"></a>配置 Log Analytics

若要开始使用适用于 Windows 虚拟桌面的 Azure Monitor，你还需要至少一个 Log Analytics 工作区来从你计划监视的环境中收集数据并将其提供给工作簿。 如果已设置了一个，请直接跳到 [设置性能计数器](#set-up-performance-counters)。 若要为包含 Windows 虚拟桌面环境的 Azure 订阅设置新的 Log Analytics 工作区，请参阅 [在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

>[!NOTE]
>适用于 Log Analytics 的标准数据存储费用。 若要开始，我们建议选择 "即用即付" 模型，并在缩放部署时进行调整并拍摄更多数据。 若要了解详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="set-up-performance-counters"></a>设置性能计数器

需要在 Log Analytics 工作区中的相应采样间隔内启用特定于集合的特定性能计数器。 这些性能计数器是监视 Windows 虚拟桌面所需的唯一计数器。 您可以禁用所有其他人来节约成本。

如果已启用性能计数器并且要删除它们，请按照 [配置性能计数器](../azure-monitor/platform/data-sources-performance-counters.md) 中的说明重新配置性能计数器。 尽管本文介绍如何添加计数器，但也可以在同一位置删除它们。

如果尚未设置性能计数器，请参阅以下内容，了解如何为 Windows 虚拟桌面 Azure Monitor 配置它们：

1. 中转到 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)，然后选择该窗口底部的 **配置工作簿** 。

2. 在 " **Log Analytics 配置**" 下，选择已为订阅设置的工作区。

3. 在 **工作区性能计数器** 中，你将看到监视所需的计数器列表。 在该列表的右侧，检查 " **缺少的计数器** " 列表中的项，以启用开始监视工作区所需的计数器。

4. 选择 " **配置性能计数器**"。

5. 选择 " **应用配置**"。

6. 刷新配置工作簿，然后继续设置环境。

你还可以在初始配置后添加新的性能计数器，只要服务更新并需要新的监视工具。 可以通过在 " **缺少的计数器** " 列表中选择所需的所有计数器来验证它们是否已启用。

>[!NOTE]
>输入延迟性能计数器仅与 Windows 10 RS5 和更高版本或 Windows Server 2019 及更高版本兼容。

若要了解有关如何手动添加尚未为收集启用的性能计数器的详细信息，请参阅 [配置性能计数器](../azure-monitor/platform/data-sources-performance-counters.md)。

### <a name="set-up-windows-events"></a>设置 Windows 事件

接下来，需要在 Log Analytics 工作区中启用特定于收集的 Windows 事件。 本部分所述的事件是唯一 Azure Monitor Windows 虚拟桌面需求的事件。 您可以禁用所有其他人来节约成本。

设置 Windows 事件的步骤：

1. 如果已经启用了 Windows 事件，并且想要将其删除，请在使用配置工作簿之前删除不需要的事件，以启用监视所需的设置。

2. 请在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)上的 Windows 虚拟桌面中转到 Azure Monitor，然后选择窗口底部的 " **配置工作簿** "。

3. 在 **Windows 事件配置** 中，有一个用于监视的 windows 事件列表。 此列表的右侧为 " **缺少事件** " 列表，你可以在其中找到工作区当前未启用的所需事件名称和事件类型。 稍后记录其中每个名称。

4. 选择 " **打开工作区配置**"。

5. 选择 **数据**。

6. 选择 " **Windows 事件日志**"。

7. 为每个步骤3中的缺少的事件名称添加所需的事件类型。

8. 刷新配置工作簿，然后继续设置环境。

如果监视工具更新需要启用新事件，则可以在初始配置后添加新的 Windows 事件。 若要确保已启用所有必需事件，请返回到 " **缺失事件** " 列表，并启用在此处看到的任何缺失事件。

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>在所有主机上安装 Log Analytics 代理

最后，需要在主机池中的所有主机上安装 Log Analytics 代理，以将数据从主机发送到所选工作区。

若要安装 Log Analytics 代理，请执行以下操作：

1. 请在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)上的 Windows 虚拟桌面中转到 Azure Monitor，然后选择窗口底部的 " **配置工作簿** "。

2. 如果未为主机池中的所有主机配置 Log Analytics，你将在 "Log Analytics 配置" 部分的底部看到一个错误，其中显示消息 "主机池中的某些主机未向所选 Log Analytics 工作区发送数据"。 选择 " **将主机添加到工作区** " 以添加选定的主机。 如果未看到错误消息，请在此处停止。

3. 刷新配置工作簿。

>[!NOTE]
>需要运行主机才能安装 Log Analytics 扩展。 如果主机上的自动部署失败，你始终可以在主机上手动安装扩展。 若要了解如何手动安装扩展，请参阅 [Log Analytics 适用于 Windows 的虚拟机扩展](../virtual-machines/extensions/oms-windows.md)。

## <a name="optional-configure-alerts"></a>可选：配置警报

你可以将 Windows 虚拟桌面 Azure Monitor 配置为在所选订阅中出现任何严重 Azure Monitor 警报时通知你。 为此，请按照 " [响应事件" Azure Monitor 警报](../azure-monitor/learn/tutorial-response.md)中的说明进行操作。

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据来改进服务的质量、安全性和完整性。

为了提供准确且高效的故障排除功能，收集的数据包括门户会话 ID、Azure Active Directory 用户 ID 以及发生事件的门户选项卡的名称。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://privacy.microsoft.com/privacystatement)。

>[!NOTE]
>若要了解如何查看或删除服务收集的个人数据，请参阅 [GDPR 的 Azure 数据主体请求](/microsoft-365/compliance/gdpr-dsr-azure)。 有关 GDPR 的详细信息，请参阅 [服务信任门户的 GDPR 部分](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)。

## <a name="next-steps"></a>后续步骤

现在，你已配置了 Windows 虚拟桌面 Azure 门户，下面提供了一些可以帮助你执行以下操作的资源：

- 查看 [术语表](azure-monitor-glossary.md) ，了解有关与 Windows 虚拟桌面 Azure Monitor 相关的术语和概念的详细信息。
- 如果遇到问题，请查看 [故障排除指南](troubleshoot-azure-monitor.md) 以获得帮助。