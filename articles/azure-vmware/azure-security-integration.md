---
title: 用 Azure 安全中心集成保护 Azure VMware 解决方案 VM
description: 通过 Azure 安全中心仪表板中的 Azure 本机安全工具保护 Azure VMware 解决方案 VM。
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 3012da2f9d5254b581a4ce53ef51503dfb048e98
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108769518"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>用 Azure 安全中心集成保护 Azure VMware 解决方案 VM

Azure 本机安全工具为 Azure、Azure VMware 解决方案和本地虚拟机 (VM) 的混合环境提供保护。 本文介绍如何为混合环境安全性设置 Azure 工具。 你将使用这些工具来识别并解决各种威胁。

## <a name="azure-native-services"></a>Azure 本机服务

下面是 Azure 本机服务的快速摘要：

- **Log Analytics 工作区：** Log Analytics 工作区是存储日志数据的唯一环境。 每个工作区都有其自己的数据存储库和配置。 数据源和解决方案均配置为将其数据存储在特定工作区中。
- **Azure 安全中心：** Azure 安全中心是一个统一的基础结构安全管理系统。 它增强了数据中心的安全性，并为云中或本地的混合工作负载提供高级威胁防护。
- **Azure Sentinel：** Azure Sentinel 是云原生的安全信息事件管理 (SIEM) 解决方案。 它在整个环境中提供安全分析、警报检测和自动威胁响应。

## <a name="topology"></a>拓扑

:::image type="content" source="media/azure-security-integration/azure-integrated-security-architecture.png" alt-text="显示 Azure 集成安全性的体系结构的关系图。" border="false":::

Log Analytics 代理支持收集 Azure、Azure VMware 解决方案和本地 VM 中的日志数据。 日志数据将发送到 Azure Monitor 日志，并存储在 Log Analytics 工作区中。 可以使用启用了 Arc 的服务器 [VM 扩展支持](../azure-arc/servers/manage-vm-extensions.md)为新的和现有的 VM 部署 Log Analytics 代理。 

Log Analytics 工作区收集日志后，可以通过 Azure 安全中心配置 Log Analytics 工作区。 Azure 安全中心将评估 Azure VMware 解决方案 VM 的漏洞状态，并针对任何严重漏洞发出警报。 例如，它会评估缺少的操作系统补丁、安全错误配置和[终结点保护](../security-center/security-center-services.md)。

可以通过 Azure Sentinel 配置 Log Analytics 工作区，以实现警报检测、威胁可见性、搜寻和威胁响应。 在上图中，Azure 安全中心使用 Azure 安全中心连接器连接到 Azure Sentinel。 Azure 安全中心会将环境漏洞转发到 Azure Sentinel 来创建事件，并与其他威胁进行映射。 你还可以创建“计划的规则”查询来检测不需要的活动，并将其转换为事件。

## <a name="benefits"></a>好处

- Azure 本机服务可用于 Azure、Azure VMware 解决方案和本地服务中的混合环境安全性。
- 使用 Log Analytics 工作区，可以将数据或日志收集到一个点，然后向不同的 Azure 本机服务提供相同的数据。
- Azure 安全中心提供许多功能，包括：
    - 文件完整性监视
    - 无文件攻击检测
    - 操作系统补丁评估 
    - 安全配置错误评估
    - 终结点保护评估
- 通过 Azure Sentinel，你能够：
    - 跨所有用户、设备、应用程序和基础结构（无论在本地还是多个云中）以云规模收集数据。
    - 检测以前未检测到的威胁。
    - 借助人工智能调查威胁，并大规模搜寻可疑活动。
    - 通过内置的业务流程和常见任务自动化快速响应事件。

## <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

要收集来自各种源的数据，需要 Log Analytics 工作区。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>部署安全中心并配置 Azure VMware 解决方案 VM

Azure 安全中心是一个预先配置的工具，不需要进行部署。 在 Azure 门户中，搜索“安全中心”并选择它。

### <a name="enable-azure-defender"></a>启用 Azure Defender

在本地和云中，Azure Defender 跨混合工作负载扩展 Azure 安全中心的高级威胁防护。 因此，若要保护 Azure VMware 解决方案 VM，需要启用 Azure Defender。 

1. 在安全中心中，选择“入门”。

2. 选择“升级”选项卡，然后选择订阅或工作区。 

3. 选择“升级”以启用 Azure Defender。

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>将 Azure VMware 解决方案 VM 添加到安全中心

1. 在 Azure 门户中，搜索“Azure Arc”并选择它。

2. 在“资源”下选择“服务器”，然后单击“+ 添加”。

   :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="显示用于将 Azure VMware 解决方案 VM 添加到 Azure 的 Azure Arc 服务器页的屏幕截图。":::

3. 选择“生成脚本”。
 
   :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Azure Arc 页面的屏幕截图，其中显示了使用交互式脚本添加服务器的选项。"::: 
 
4. 在“先决条件”选项卡上，选择“下一步”。

5. 在“资源详细信息”选项卡上，填写以下详细信息： 
    - 订阅
    - 资源组
    - 区域 
    - 操作系统
    - 代理服务器详细信息
    
    然后，选择“下一步: 标签”。

6. 在“标签”选项卡上，选择“下一步”。

7. 在“下载并运行脚本”选项卡中，选择“下载” 。

8. 指定操作系统，并在 Azure VMware 解决方案 VM 上运行该脚本。

## <a name="view-recommendations-and-passed-assessments"></a>查看建议和已通过的评估

1. 在 Azure 安全中心的左窗格中，选择“清单”。

2. 对于资源类型，选择“服务器 - Azure Arc”。
 
   :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Azure 安全中心“清单”页的屏幕截图，其中选择了“资源”类型下的“服务器 - Azure Arc”。":::

3. 选择你资源的名称。 此时会打开一个页面，其中显示了资源的安全运行状况详细信息。

4. 在“建议列表”下，选择“建议”、“已通过的评估”和“不可用评估”选项卡，查看其详细信息  

   :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Azure 安全中心的屏幕截图，其中显示了安全建议和评估。":::

## <a name="deploy-an-azure-sentinel-workspace"></a>部署 Azure Sentinel 工作区

Azure Sentinel 构建在 Log Analytics 工作区的基础之上。 你加入 Azure Sentinel 的第一步是选择要用于该目的的 Log Analytics 工作区。

1. 在 Azure 门户中，搜索“Azure Sentinel”并选择它。

2. 在“Azure Sentinel 工作区”页上，选择“+ 添加”。

3. 选择 Log Analytics 工作区，然后选择“添加”。

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>启用数据收集器，获取 Azure VMware 解决方案 VM 上的安全事件

现在，你已准备好将 Azure Sentinel 连接到数据源（在本例中为安全事件）。

1. 在“Azure Sentinel 工作区”页上，选择所配置的工作区。

2. 在“配置”下，选择“数据连接器”。

3. 在“连接器名称”列下，从列表中选择“安全事件”，然后选择“打开连接器页面”。

4. 在“连接器”页上，选择想要流式传输的事件，然后选择“应用更改”。

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Azure Sentinel 中“安全事件”页的屏幕截图，可在其中选择要流式传输的事件。":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>将 Azure Sentinel 与 Azure 安全中心连接  

1. 在“Azure Sentinel 工作区”页上，选择所配置的工作区。

2. 在“配置”下，选择“数据连接器”。

3. 从列表中选择“Azure 安全中心”，然后选择“打开连接器页面”。

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Azure Sentinel 中的“数据连接器”页的屏幕截图，显示了将 Azure 安全中心与 Azure Sentinel 连接的选择。":::

4. 选择“连接”，将 Azure 安全中心与 Azure Sentinel 连接。

5. 启用“创建事件”，为 Azure 安全中心生成事件。

## <a name="create-rules-to-identify-security-threats"></a>创建用于识别安全威胁的规则

将数据源连接到 Azure Sentinel 后，可以创建规则，为检测到的威胁生成警报。 在下面的示例中，我们将针对以下操作创建一个规则：尝试使用错误密码登录到 Windows Server。

1. 在 Azure Sentinel 概述页上的“配置”下，选择“分析”。

2. 在“配置”下，选择“分析”。

3. 选择“+ 创建”，并在下拉列表中选择“已计划的查询规则”。

4. 在“常规”选项卡上输入所需的信息。

    - 名称
    - 说明
    - 策略
    - 严重性
    - 状态

    选择“下一步: 设置规则逻辑 >”。

5. 在“设置规则逻辑”选项卡上，输入所需信息。

    - 规则查询（此处显示示例查询）
    
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

6. 在“事件设置”选项卡上，启用“根据此分析规则触发的警报创建事件”，然后选择“下一步: 自动响应 >”。
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="用于在 Azure Sentinel 中创建新规则的分析规则向导的屏幕截图。显示“根据启用的此规则触发的警报创建事件”。":::

7. 选择“下一步: 查看 >”。

8. 在“查看并创建”选项卡中，查看信息，然后选择“创建”。

第三次尝试登录 Windows Server 失败后，创建的规则将为每一次失败的尝试触发一个事件。

## <a name="view-alerts"></a>查看警报

你可以通过 Azure Sentinel 查看生成的事件。 还可以在 Azure Sentinel 中分配事件并在解决它们后将其关闭。

1. 转到 Azure Sentinel 概述页。

2. 在“威胁管理”下，选择“事件”。

3. 选择一个事件。 然后，可以将该事件分配给团队，让团队解决。

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="“Azure Sentinel 事件”页的屏幕截图，其中显示了已选择事件以及分配事件以求解决的选项。":::

    解决问题后，可以将其关闭。

## <a name="hunt-security-threats-with-queries"></a>利用查询搜寻安全威胁

你可以创建查询或使用 Azure Sentinel 中的可用预定义查询来识别环境中的威胁。 以下步骤运行预定义的查询。

1. 转到 Azure Sentinel 概述页。

2. 在“威胁管理”下，选择“搜寻”。 将显示预定义查询的列表。

3. 选择查询，然后选择“运行查询”。

4. 选择“查看结果”以检查结果。

### <a name="create-a-new-query"></a>新建查询

1.  在“威胁管理”下，选择“搜寻”，然后选择“+ 新建查询”。

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="“Azure Sentinel 搜寻”页的屏幕截图，其中突出显示了“+ 新建查询”。":::

2. 填写以下信息以创建自定义查询。

    - 名称
    - 说明
    - 自定义查询
    - 输入映射
    - 策略
    
3. 选择“创建”  。 然后，你可以选择创建的查询、“运行查询”并“查看结果” 。

## <a name="next-steps"></a>后续步骤

了解了如何保护 Azure VMware 解决方案 VM 后，接下来建议了解以下内容：

- 使用 [Azure Defender 仪表板](../security-center/azure-defender-dashboard.md)
- [Azure Sentinel 中的高级多阶段攻击检测](../azure-monitor/logs/quick-create-workspace.md)
- [监视和管理 Azure VMware 解决方案 VM](lifecycle-management-of-azure-vmware-solution-vms.md)
