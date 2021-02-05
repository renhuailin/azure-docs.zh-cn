---
title: 通过 Azure 安全中心集成保护 Azure VMware 解决方案 Vm
description: 了解如何通过 azure 安全中心中的单个仪表板使用 Azure 的本机安全工具保护 Azure VMware 解决方案 Vm。
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 58cfa1e8e7faa56675d966f86d3b390e52acec27
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584951"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>通过 Azure 安全中心集成保护 Azure VMware 解决方案 Vm

Azure 本机安全工具为 Azure、Azure VMware 解决方案的混合环境和本地虚拟机 (Vm) 提供了一个安全的基础结构。 本文介绍如何设置 Azure tools for 混合环境安全性。 你将使用各种工具来识别和解决不同类型的威胁。

## <a name="azure-native-services"></a>Azure 本机服务

下面是每个 Azure 本机服务的快速摘要：

- **Log Analytics 工作区：** Log Analytics 工作区是存储日志数据的唯一环境。 每个工作区都有其自己的数据存储库和配置。 数据源和解决方案配置为将其数据存储在特定的工作区中。
- **Azure 安全中心：** Azure 安全中心是一个统一的基础结构安全管理系统。 它增强了数据中心的安全状况，并在云中或本地的混合工作负荷中提供高级威胁防护。
- **Azure Sentinel：** Azure Sentinel 是云本机、安全信息事件管理 (SIEM) 和安全业务流程自动响应 (之忠诚度) 解决方案。 它在整个环境中提供智能安全分析和威胁智能。 它是用于警报检测、威胁可见性、主动搜寻和威胁响应的单一解决方案。

## <a name="topology"></a>拓扑

![显示 Azure 集成安全性的体系结构的关系图。](media/azure-security-integration/azure-integrated-security-architecture.png)

Log Analytics 代理允许收集 Azure、Azure VMware 解决方案和本地 Vm 中的日志数据。 日志数据将发送到 Azure Monitor 日志，并存储在 Log Analytics 工作区中。 可以使用启用了 Arc 的服务器为新的和现有 [的 vm 部署](../azure-arc/servers/manage-vm-extensions.md) Log Analytics 代理。 

Log Analytics 工作区收集日志后，可以使用 Azure 安全中心配置 Log Analytics 工作区。 Azure 安全中心将评估 Azure VMware 解决方案 Vm 的漏洞状态，并为任何严重漏洞发出警报。 例如，它会评估缺少的操作系统修补程序、安全错误配置和 [endpoint protection](../security-center/security-center-services.md)。

可以通过 Azure Sentinel 配置 Log Analytics 工作区，以实现警报检测、威胁可见性、主动搜寻和威胁响应。 在上图中，Azure 安全中心使用 Azure 安全中心连接器连接到 Azure Sentinel。 Azure 安全中心会将环境漏洞转发到 Azure Sentinel 来创建事件，并与其他威胁进行映射。 你还可以创建 "计划的规则" 查询来检测不需要的活动，并将其转换为事件。

## <a name="benefits"></a>优点

- Azure 本机服务可用于 Azure、Azure VMware 解决方案和本地服务中的混合环境安全性。
- 使用 Log Analytics 工作区，你可以将数据或日志收集到一个点，并向不同的 Azure 本机服务提供相同的数据。
- Azure 安全中心提供了许多功能，包括：
    - 文件完整性监视
    - Fileless 攻击检测
    - 操作系统修补程序评估 
    - 安全配置错误评估
    - 终结点保护评估
- Azure Sentinel 使你能够：
    - 跨所有用户、设备、应用程序和基础结构，在本地和多个云中收集云规模的数据。
    - 检测以前未检测到的威胁。
    - 利用人工智能和探索大规模活动，调查威胁。
    - 通过内置的业务流程和常见任务自动化快速响应事件。

## <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

你将需要一个 Log Analytics 工作区，用于从各种源收集数据。 有关详细信息，请参阅 [从 Azure 门户创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>部署安全中心并配置 Azure VMware 解决方案 Vm

Azure 安全中心是预配置的工具，不需要进行部署。 在 Azure 门户中，搜索 " **安全中心** " 并选择它。

### <a name="enable-azure-defender"></a>启用 Azure Defender

Azure Defender 跨本地和云中的混合工作负荷扩展 Azure 安全中心的高级威胁防护。 因此，若要保护你的 Azure VMware 解决方案 Vm，你将需要启用 Azure Defender。 

1. 在安全中心中，选择 " **入门**"。

2. 选择 " **升级** " 选项卡，然后选择订阅或工作区。 

3. 选择“升级”以启用 Azure Defender。

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>将 Azure VMware 解决方案 Vm 添加到安全中心

1. 在 Azure 门户中，搜索 " **Azure Arc** " 并选择它。

2. 在 "资源" 下选择 " **服务器** "，然后单击 " **+ 添加**"。

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="显示用于将 Azure VMware 解决方案 VM 添加到 Azure 的 Azure Arc 服务器页的屏幕截图。":::

3. 选择 " **生成脚本**"。
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Azure Arc 页面的屏幕截图，其中显示了使用交互式脚本添加服务器的选项。"::: 
 
4. 在 " **先决条件** " 选项卡上，选择 " **下一步**"。

5. 在 " **资源详细信息** " 选项卡上，填写以下详细信息： 
    - 订阅
    - 资源组
    - 区域 
    - 操作系统
    - 代理服务器详细信息
    
    然后选择 " **下一步：标记**"。

6. 在 " **标记** " 选项卡上，选择 " **下一步**"。

7. 在 " **下载并运行脚本** " 选项卡上，选择 " **下载**"。

8. 指定操作系统，并在 Azure VMware 解决方案 VM 上运行该脚本。

## <a name="view-recommendations-and-passed-assessments"></a>查看建议并通过评估

1. 在 Azure 安全中心的左窗格中，选择 " **清单** "。

2. 对于 "资源类型"，选择 " **服务器-Azure Arc**"。
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="&quot;Azure 安全中心清单&quot; 页的屏幕截图，显示 &quot;服务器-在资源类型下选择的 Azure Arc&quot;。":::

3. 选择资源的名称。 此时会打开一个页面，其中显示了资源的安全运行状况详细信息。

4. 在 " **建议列表**" 下，选择 " **建议**"、" **通过评估**" 和 " **不可用评估** " 选项卡以查看这些详细信息

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="显示安全建议和评估的 Azure 安全中心的屏幕截图。":::

## <a name="deploy-an-azure-sentinel-workspace"></a>部署 Azure Sentinel 工作区

Azure Sentinel 构建在 Log Analytics 工作区之上。 加入 Azure Sentinel 的第一步是选择要用于此目的的 Log Analytics 工作区。

1. 在 Azure 门户中，搜索 " **Azure Sentinel**" 并将其选中。

2. 在 "Azure Sentinel 工作区" 页上，选择 " **+ 添加**"。

3. 选择 "Log Analytics" 工作区，然后选择 " **添加**"。

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>启用数据收集器，了解 Azure VMware 解决方案 Vm 上的安全事件

现在，你已准备好将 Azure Sentinel 连接到数据源（在本例中为安全事件）。

1. 在 "Azure Sentinel 工作区" 页上，选择配置的工作区。

2. 在 "配置" 下，选择 " **数据连接器**"。

3. 在 "连接器名称" 列下，从列表中选择 " **安全事件** "，然后选择 " **打开连接器" 页面**。

4. 在 "连接器" 页上，选择想要流式传输的事件，然后选择 " **应用更改**"。

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Azure Sentinel 中的 &quot;安全事件&quot; 页的屏幕截图，你可以在其中选择要流式传输的事件。":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>通过 Azure 安全中心连接 Azure Sentinel  

1. 在 "Azure Sentinel 工作区" 页上，选择配置的工作区。

2. 在 "配置" 下，选择 " **数据连接器**"。

3. 从列表中选择 " **Azure 安全中心** "，然后选择 " **打开连接器" 页面**。

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Azure Sentinel 中的 &quot;数据连接器&quot; 页面的屏幕截图，显示了通过 Azure Sentinel 连接 Azure 安全中心所做的选择。":::

4. 选择 " **连接** "，将 Azure 安全中心与 azure Sentinel 连接。

5. 启用 " **创建事件** " 以生成 Azure 安全中心的事件。

## <a name="create-rules-to-identify-security-threats"></a>创建用于识别安全威胁的规则

将数据源连接到 Azure Sentinel 后，可以创建规则，以根据检测到的威胁生成警报。 在下面的示例中，我们将创建一个规则，用于识别使用错误的密码登录到 Windows server 的尝试。

1. 在 Azure Sentinel 概述页上的 "配置" 下，选择 " **分析**"。

2. 在 "配置" 下，选择 " **分析**"。

3. 选择 " **+ 创建** "，并在下拉选择 " **计划查询规则**"。

4. 在 " **常规** " 选项卡上，输入所需信息。

    - 名称
    - 说明
    - 策略
    - 严重性
    - 状态

    选择 " **下一步：设置规则逻辑 >**"。

5. 在 " **设置规则逻辑** " 选项卡上，输入所需信息。

    - 此处 (规则查询显示示例查询) 
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - 映射实体
    - 查询计划
    - 警报阈值
    - 事件分组
    - 禁止

    选择“**下一页**”。

6. 在 " **事件设置** " 选项卡上，启用 "从此 **分析规则触发的警报创建事件** "，然后选择 " **下一步：自动响应 >**。
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="用于在 Azure Sentinel 中创建新规则的分析规则向导的屏幕截图。显示 &quot;根据启用的此规则触发的警报创建事件&quot;。":::

7. 选择 **下一步：查看 >**。

8. 在 " **检查和创建** " 选项卡上，查看信息并选择 " **创建**"。

在第三次尝试登录到 Windows server 后，创建的规则将触发每次尝试失败的事件。

## <a name="view-generated-alerts"></a>查看生成的警报

可以通过 Azure Sentinel 查看生成的事件。 你还可以分配事件，并在解决后关闭它们，所有这些事件都从 Azure Sentinel 内完成。

1. 请参阅 Azure Sentinel 概述页。

2. 在 "威胁管理" 下，选择 " **事件**"。

3. 选择事件。 然后，可以将事件分配给团队进行解决。

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="选择了事件的 &quot;Azure Sentinel 事件&quot; 页的屏幕截图，并提供分配事件以进行解析的选项。":::

    解决问题后，可以将其关闭。

## <a name="hunt-security-threats-with-queries"></a>利用查询查寻安全威胁

你可以创建查询或使用 Azure Sentinel 中的可用预定义查询来识别你的环境中的威胁。 以下步骤运行预定义的查询。

1. 请参阅 Azure Sentinel 概述页。

2. 在 "威胁管理" 下，选择 " **搜寻**"。 将显示预先定义的查询的列表。

3. 选择查询，然后选择 " **运行查询**"。

4. 选择 " **查看结果** " 以检查结果。

### <a name="create-a-new-query"></a>新建查询

1.  在 "威胁管理" 下，选择 " **搜寻** "，然后选择 " **+ 新建查询**"。

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="突出显示了 + 新查询的 Azure Sentinel 搜寻页的屏幕截图。":::

2. 填写以下信息以创建自定义查询。

    - 名称
    - 说明
    - 自定义查询
    - 输入映射
    - 策略
    
3. 选择“创建”。 然后，您可以选择创建的查询、 **运行查询** 并 **查看结果**。

## <a name="next-steps"></a>后续步骤

现在，你已介绍了如何保护 Azure VMware 解决方案 Vm，你可能想要了解：

- 使用 [Azure Defender 仪表板](../security-center/azure-defender-dashboard.md)。
- [Azure Sentinel 中的高级多阶段攻击检测](../azure-monitor/learn/quick-create-workspace.md)。
- [Azure VMware 解决方案 vm 的生命周期管理](lifecycle-management-of-azure-vmware-solution-vms.md)。
