---
title: 将 Azure 安全中心与 Azure VMware 解决方案集成
description: 了解如何通过 Azure 安全中心仪表板中的 Azure 本机安全工具保护 Azure VMware 解决方案 VM。
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: 9c7326fca3aeebf277b5f54a65729e2594933984
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228665"
---
# <a name="integrate-azure-security-center-with-azure-vmware-solution"></a>将 Azure 安全中心与 Azure VMware 解决方案集成 

Azure 安全中心为 Azure VMware 解决方案和本地虚拟机 (VM) 提供高级威胁防护。 它会评估 Azure VMware 解决方案 VM 的漏洞，并根据需要引发警报。 这些安全警报可以转发到 Azure Monitor 以进行解决。 可以在 Azure 安全中心内定义安全策略。 有关详细信息，请参阅[使用安全策略](../security-center/tutorial-security-policy.md)。 

Azure 安全中心提供许多功能，包括：
- 文件完整性监视
- 无文件攻击检测
- 操作系统补丁评估 
- 安全配置错误评估
- 终结点保护评估

此示意图显示适用于 Azure VMware 解决方案 VM 集成安全性的集成监视体系结构。
 
:::image type="content" source="media/azure-security-integration/azure-integrated-security-architecture.png" alt-text="显示 Azure 集成安全性的体系结构的关系图。" border="false":::

Log Analytics 代理从 Azure、Azure VMware 解决方案和本地 VM 收集日志数据。 日志数据将发送到 Azure Monitor 日志，并存储在 Log Analytics 工作区中。 每个工作区都有其自己的数据存储库和用于存储数据的配置。  日志收集完成后，Azure 安全中心会评估 Azure VMware 解决方案 VM 的漏洞状态，并针对任何严重漏洞发出警报。 进行评估后，Azure 安全中心会将漏洞状态转发到 Azure Sentinel 来创建事件，并与其他威胁进行映射。  Azure 安全中心是使用 Azure 安全中心连接器连接到 Azure Sentinel 的。 

## <a name="prerequisites"></a>先决条件

- [规划安全中心的优化使用](../security-center/security-center-planning-and-operations-guide.md)。

- [查看安全中心的支持平台](../security-center/security-center-os-coverage.md)。

- [创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)以收集来自各种源的数据。

- [在订阅中启用 Azure 安全中心](../security-center/security-center-get-started.md)。 

   >[!NOTE]
   >Azure 安全中心是一个预配置的工具，你不需要部署它，但需要启用它。

- [启用 Azure Defender](../security-center/enable-azure-defender.md)。 


## <a name="add-azure-vmware-solution-vms-to-security-center"></a>将 Azure VMware 解决方案 VM 添加到安全中心

1. 在 Azure 门户中，搜索“Azure Arc”并选择它。

2. 在“资源”下选择“服务器”，然后单击“+ 添加”。

   :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Azure Arc“服务器”页的屏幕截图，其中显示将 Azure VMware 解决方案 VM 添加到 Azure。":::

3. 选择“生成脚本”。
 
   :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Azure Arc 页的屏幕截图，其中显示了使用交互式脚本添加服务器的选项。"::: 
 
4. 在“先决条件”选项卡上，选择“下一步”。

5. 在“资源详细信息”选项卡上，填写以下详细信息，然后选择“下一步: 标记”。 

    - 订阅

    - 资源组

    - 区域 

    - 操作系统

    - 代理服务器详细信息
    
6. 在“标签”选项卡上，选择“下一步”。

7. 在“下载并运行脚本”选项卡中，选择“下载” 。

8. 指定操作系统，并在 Azure VMware 解决方案 VM 上运行该脚本。

## <a name="view-recommendations-and-passed-assessments"></a>查看建议和已通过的评估

建议和评估向你提供了资源的安全运行状况详细信息。 

1. 在 Azure 安全中心的左窗格中，选择“清单”。

2. 对于资源类型，选择“服务器 - Azure Arc”。
 
   :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="显示 Azure 安全中心“清单”页的屏幕截图，其中选择了“资源”类型下的“服务器 - Azure Arc”。":::

3. 选择你资源的名称。 此时会打开一个页面，其中显示了资源的安全运行状况详细信息。

4. 在“建议列表”下，选择“建议”、“已通过的评估”和“不可用评估”选项卡，查看其详细信息  

   :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="显示 Azure 安全中心的安全建议和评估的屏幕截图。":::

## <a name="deploy-an-azure-sentinel-workspace"></a>部署 Azure Sentinel 工作区

Azure Sentinel 在整个环境中提供安全分析、警报检测和自动威胁响应。 它是一个云原生的安全信息和事件管理 (SIEM) 解决方案，构建在 Log Analytics 工作区基础之上。

由于 Azure Sentinel 构建在 Log Analytics 工作区基础之上，因此你只需选择要使用的工作区。

1. 在 Azure 门户中，搜索“Azure Sentinel”并选择它。

2. 在“Azure Sentinel 工作区”页上，选择“+ 添加”。

3. 选择 Log Analytics 工作区，然后选择“添加”。

## <a name="enable-data-collector-for-security-events"></a>为安全事件启用数据收集器

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

4. 在“常规”选项卡上，输入所需信息，然后选择“下一步: 设置规则逻辑”。

    - 名称

    - 说明

    - 策略

    - 严重性

    - 状态

5. 在“设置规则逻辑”选项卡上，输入所需信息，然后选择“下一步”。

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


6. 在“事件设置”选项卡上，启用“根据此分析规则触发的警报创建事件”，然后选择“下一步: 自动响应”。
 
    :::image type="content" source="../sentinel/media/tutorial-detect-threats-custom/general-tab.png" alt-text="显示用于在 Azure Sentinel 中创建新规则的分析规则向导的屏幕截图。":::

7. 在完成时选择“下一步:  查看”。

8. 在“查看并创建”选项卡中，查看信息，然后选择“创建”。

>[!TIP]
>第三次尝试登录 Windows Server 失败后，创建的规则将为每一次失败的尝试触发一个事件。

## <a name="view-alerts"></a>查看警报

你可以通过 Azure Sentinel 查看生成的事件。 还可以在 Azure Sentinel 中分配事件并在解决它们后将其关闭。

1. 转到 Azure Sentinel 概述页。

2. 在“威胁管理”下，选择“事件”。

3. 选择事件，然后将其分配给团队进行解决。

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="“Azure Sentinel 事件”页的屏幕截图，其中显示了已选择事件以及分配事件以求解决的选项。":::

>[!TIP]
>解决问题后，可以将其关闭。

## <a name="hunt-security-threats-with-queries"></a>利用查询搜寻安全威胁

你可以创建查询或使用 Azure Sentinel 中的可用预定义查询来识别环境中的威胁。 以下步骤运行预定义的查询。

1. 在 Azure Sentinel 概述页的“威胁管理”下，选择“搜寻”。 将显示预定义查询的列表。

   >[!TIP]
   >也可以通过选择“新建查询”来创建新查询。 
   >
   >:::image type="content" source="../sentinel/media/hunting/save-query.png" alt-text="“Azure Sentinel 搜寻”页的屏幕截图，其中突出显示了“+ 新建查询”。":::

3. 选择查询，然后选择“运行查询”。

4. 选择“查看结果”以检查结果。



## <a name="next-steps"></a>后续步骤

了解了如何保护 Azure VMware 解决方案 VM 后，接下来建议了解以下内容：

- [使用 Azure Defender 仪表板](../security-center/azure-defender-dashboard.md)
- [Azure Sentinel 中的高级多阶段攻击检测](../azure-monitor/logs/quick-create-workspace.md)
- [在 Azure VMware 解决方案中集成 Azure 原生服务](integrate-azure-native-services.md)
