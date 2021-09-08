---
title: 将 Azure Sentinel 连接到 Azure、Windows 和 Microsoft 服务
description: 了解如何将 Azure Sentinel 连接到 Azure 和 Microsoft 365 云服务以及 Windows Server 事件日志。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/18/2021
ms.author: yelevin
ms.openlocfilehash: c13e2e5ad14ada3c867682382b334242881ddb78
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260898"
---
# <a name="connect-azure-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>将 Azure Sentinel 连接到 Azure、Windows、Microsoft 和 Amazon 服务

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel 使用 Azure 基础为来自许多 Azure 和 Microsoft 365 服务、Amazon Web Services 和各种 Windows Server 服务的数据引入提供内置的服务到服务支持。 可以通过几种不同的方法建立这些连接，本文介绍如何建立这些连接。

本文讨论以下类型的连接器：

- 基于 API 的连接
- “诊断设置”连接，其中部分连接由 Azure Policy 进行管理
- 基于 Log Analytics 代理的连接

本文介绍连接器组的通用信息。 有关每个连接器独有的信息，例如数据存储的许可先决条件和 Log Analytics 表，请参阅随附的[数据连接器参考](data-connectors-reference.md)页面。

以下集成更独特且更常用，需区别对待，并附有自己的文章：

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Azure Defender](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Windows 安全事件](connect-windows-security-events.md)
- [Amazon Web Services (AWS) CloudTrail](connect-aws.md)


## <a name="api-based-connections"></a>基于 API 的连接

### <a name="prerequisites"></a>先决条件

- 必须对 Log Analytics 工作区拥有读取和写入权限。
- 必须对 Azure Sentinel 工作区租户拥有全局管理员或安全管理员角色。

### <a name="instructions"></a>Instructions

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择服务，然后在预览窗格中选择“打开连接器页面”。

1. 选择“连接”，开始将服务中的事件和/或警报流式传输到 Azure Sentinel。

1. 如果连接器页面上有一个标题为“创建事件 - 推荐!”的部分，并且你想要根据警报自动创建事件，请选择“启用” 。

可以使用[数据连接器参考](data-connectors-reference.md)页面中的服务连接器部分中出现的表名查找和查询每个服务的数据。

## <a name="diagnostic-settings-based-connections"></a>基于诊断设置的连接

此类型的某些连接器配置由 Azure Policy 进行管理。 选择下面的“Azure Policy”选项卡以查看说明。 对于此类型的其他连接器，请选择“独立”选项卡。

# <a name="standalone"></a>[独立](#tab/SA)

### <a name="prerequisites"></a>先决条件

要将数据引入 Azure Sentinel，请执行以下操作：

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

### <a name="instructions"></a>Instructions

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择资源类型，然后在预览窗格中选择“打开连接器页面”。

1. 在连接器页面的“配置”部分，选择打开资源配置页面的链接。

    如果显示所需类型的资源列表，请选择要引入其日志的资源的链接。

1. 从资源导航菜单中，选择“诊断设置”。

1. 选择列表底部的“+ 添加诊断设置”。

1. 在“诊断设置”屏幕的“诊断设置名称”字段中输入一个名称 。

    选中“发送到 Log Analytics”复选框。 其下将显示两个新字段。 选择相关“订阅”和“Log Analytics 工作区”（Azure Sentinel 驻留的位置）。

1. 勾选要收集的日志和指标类型的复选框。 请参阅[数据连接器参考](data-connectors-reference.md)页面中的资源连接器部分中针对每种资源类型的建议选择。

1. 选择屏幕顶部的“保存”。

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>先决条件

要将数据引入 Azure Sentinel，请执行以下操作：

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 要使用 Azure Policy 将日志流式处理策略应用到资源，必须拥有策略分配范围的“所有者”角色。

### <a name="instructions"></a>Instructions

此类型的连接器使用 Azure Policy 将单个诊断设置配置应用于定义为范围的一个类型的资源集合。 可以在该资源的连接器页面左侧的“数据类型”下查看从给定资源类型引入的日志类型。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择资源类型，然后在预览窗格中选择“打开连接器页面”。

1. 在连接器页面的“配置”部分，展开在此处看到的任何扩展器，然后选择“启动 Azure Policy 分配向导”按钮 。

    策略分配向导随即打开，准备创建新策略，并预先填充策略名称。

    1. 在“基本”选项卡中，选择“范围”下的省略号按钮，选择你的订阅（或资源组） 。 还可以添加说明。

    1. 在“参数”选项卡中：
       - 清除“仅显示需要输入的参数”复选框。
       - 如果看到“效果”和“设置名称”字段，请按原样保留 。
       - 从“Log Analytics 工作区”下拉列表中选择你的 Azure Sentinel 工作区。
       - 其余的下拉字段表示可用的诊断日志类型。 将要引入的所有日志类型标记为“True”。

    1. 此策略将应用于将来添加的资源。 若也要对现有资源应用该策略，请选择“修正”选项卡，然后勾选“创建修正任务”复选框 。

    1. 在“查看 + 创建”选项卡中，单击“创建”   。 你的策略现已分配给所选的作用域。

---

> [!NOTE]
>
> 对于此类型的数据连接器，仅当在过去 14 天内的某个时间点引入数据时，连接状态指示器（数据连接器库中的色带以及数据类型名称旁边的连接图标）才显示为“已连接”（绿色）。 一旦 14 天过去但未引入数据，连接器将显示为已断开连接。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

可以使用[数据连接器参考](data-connectors-reference.md)页面中的资源连接器部分中出现的表名查找和查询每种资源类型的数据。

## <a name="log-analytics-agent-based-connections"></a>基于 Log Analytics 代理的连接

### <a name="prerequisites"></a>先决条件

- 必须对 Log Analytics 工作区以及包含要从中收集日志的计算机的任何工作区拥有读取和写入权限。
- 除了任何 Azure Sentinel 角色之外，还必须在这些工作区的 SecurityInsights (Azure Sentinel) 解决方案上拥有“Log Analytics 参与者”角色。

### <a name="instructions"></a>Instructions

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 选择服务（DNS 或 Windows 防火墙），然后选择“打开连接器页面”  。

1. 在生成日志的设备上安装并加入代理。

    | 计算机类型  | Instructions  |
    | --------- | --------- |
    | 对于 Azure Windows VM | 1. 在“选择安装代理的位置”下，展开“在 Azure Windows 虚拟机上安装代理” 。 <br><br>2. 选择“下载并安装用于 Azure Windows 虚拟机的代理 >”链接。 <br><br>3. 在“虚拟机”边栏选项卡中，选择要在其上安装代理的虚拟机，然后选择“连接” 。 对于要连接的每个 VM，重复此步骤。 |
    | 对于任何其他 Windows 计算机 | 1. 在“选择安装代理的位置”下，展开“在非 Azure Windows 计算机上安装代理”  <br><br>2. 选择“下载并安装用于非 Azure Windows 计算机的代理 >”链接。  <br><br>3. 在“代理管理”边栏选项卡的“Windows 服务器”选项卡上，根据需要为 32 位或 64 位系统选择“下载 Windows 代理”链接  。      |

1. 选择 DNS 或 Windows 防火墙的“安装解决方案”按钮。

可以分别使用 DnsEvents、DnsInventory 和 WindowsFirewall 表名称查找和查询 DNS 和 Windows 防火墙的数据  。 可以在[数据连接器参考](data-connectors-reference.md)页面中的相应部分中查看有关这两个服务连接器的相关信息和其他信息。


## <a name="next-steps"></a>后续步骤

在本文档中，你了解了如何将 Azure、Microsoft 和 Windows 服务以及 Amazon Web Services 连接到 Azure Sentinel。 
- 大致了解 [Azure Sentinel 数据连接器](connect-data-sources.md)。
- [查找 Azure Sentinel 数据连接器](data-connectors-reference.md)。
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。