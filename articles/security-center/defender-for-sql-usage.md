---
title: 如何设置 Azure Defender for SQL
description: 了解如何启用 Azure 安全中心的可选 Azure Defender for SQL 计划
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: bbfd426c67d09ca8632c3ab665b1242d27c6ad58
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711689"
---
# <a name="enable-azure-defender-for-sql-servers-on-machines"></a>启用计算机上的 Azure Defender for SQL 服务器 

此 Azure Defender 计划会检测异常活动，这些活动表示异常和可能有害的数据库访问或攻击尝试。

出现可疑的数据库活动、潜在漏洞，或者 SQL 注入攻击以及异常的数据库访问和查询模式时，你会看到警报。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|计算机上的 Azure Defender for SQL 服务器按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中显示的定价计费|
|受保护的 SQL 版本：|Azure SQL Server（Microsoft 支持涵盖的所有版本）|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 政府<br>:::image type="icon" source="./media/icons/no-icon.png":::Azure 中国世纪互联|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>设置计算机上的 Azure Defender for SQL 服务器

若要启用此计划：

[步骤 1：安装代理扩展](#step-1-install-the-agent-extension)

[步骤 2：在 SQL 服务器的主机上预配 Log Analytics 代理：](#step-2-provision-the-log-analytics-agent-on-your-sql-servers-host)

[步骤 3：在安全中心的“定价和设置”页中启用可选计划：](#step-3-enable-the-optional-plan-in-security-centers-pricing-and-settings-page)


### <a name="step-1-install-the-agent-extension"></a>步骤 1。 安装代理扩展

- **Azure VM 上的 SQL Server** - 向 SQL IaaS 代理扩展注册你的 SQL Server VM，如 [向 SQL IaaS 代理扩展注册 SQL Server VM](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) 中所述。

- **Azure Arc 上的 SQL Server** - 按照 [Azure Arc 文档](../azure-arc/servers/manage-vm-extensions.md)中描述的安装方法安装 Azure Arc 代理。

### <a name="step-2-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>步骤 2。 在 SQL 服务器的主机上预配 Log Analytics 代理：

- **Azure VM 上的 SQL Server** - 如果 SQL 计算机托管在 Azure VM 上，则可以 [启用 Log Analytics 代理的自动预配 <a name="auto-provision-mma"></a>](security-center-enable-data-collection.md#auto-provision-mma)。 或者，可以按照手动过程来[加入 Azure Stack Hub VM](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms)。
- **Azure Arc 上的 SQL Server** - 如果 SQL Server 由启用了 [Azure Arc](../azure-arc/index.yml) 的服务器管理，则可以使用安全中心建议“Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上（预览版）”部署 Log Analytics 代理。

- **本地 SQL Server** - 如果 SQL Server 托管在不带 Azure Arc 的本地 Windows 计算机上，可使用两个选项将其连接到 Azure：
    
    - **部署 Azure Arc** - 可将任何 Windows 计算机连接到安全中心。 但是，Azure Arc 在所有 Azure 环境中提供更深入的集成。 如果设置了 Azure Arc，可在门户中看到“SQL Server - Azure Arc”页，并且安全警报将显示在该页面的专用“安全”选项卡上 。 因此，首个推荐的选项是[在主机上设置 Azure Arc](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) 并按照上面“Azure Arc 上的 SQL Server”的说明进行操作。
        
    - **连接不带 Azure Arc 的 Windows 计算机** - 如果选择在不使用 Azure Arc 的情况下连接在 Windows 计算机上运行的 SQL Server，请按照 [将 Windows 计算机连接到 Azure Monitor](../azure-monitor/agents/agent-windows.md) 中的说明进行操作。


### <a name="step-3-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>步骤 3. 在安全中心的“定价和设置”页中启用可选计划：

1. 从安全中心的菜单中，打开“定价和设置”页。

    - 如果使用的是“Azure 安全中心的默认工作区（名为“defaultworkspace-[your subscription ID]-[region]”），请选择相关订阅 。

    - 如果使用的是非默认工作区，请选择相关工作区（如有必要，在筛选器中输入工作区的名称） ：

        ![按标题查找非默认工作区。](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. 将“计算机上的 Azure Defender for SQL 服务器”计划的选项设置为“开” 。 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="具有可选计划的安全中心定价页。":::

    将在连接到所选工作区的所有 SQL 服务器上启用该计划。 首次重启 SQL Server 实例后，保护将处于完全激活状态。

    >[!TIP] 
    > 若要新建工作区，请按照[创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)中的说明进行操作。


1. （可选）配置安全警报的电子邮件通知。 
    可设置在生成安全中心警报时接收电子邮件通知的收件人列表。 电子邮件包含指向 Azure 安全中心警报的直接链接，以及所有的相关详细信息。 有关详细信息，请参阅[设置安全警报的电子邮件通知](security-center-provide-security-contact-details.md)。


## <a name="azure-defender-for-sql-alerts"></a>Azure Defender for SQL 警报
警报是因访问或攻击 SQL 计算机的异常且可能有害的尝试而生成的。 这些事件可触发[警报参考页面](alerts-reference.md#alerts-sql-db-and-warehouse)中显示的警报。

## <a name="explore-and-investigate-security-alerts"></a>浏览并调查安全警报

可以在安全中心的警报页、计算机的安全页、[Azure Defender 仪表板](azure-defender-dashboard.md)或通过警报电子邮件中的直接链接获取 Azure Defender for SQL 警报。

1. 若要查看警报，请从安全中心的菜单中选择“安全警报”，然后选择一个警报。

1. 警报各自独立，每个警报都包含详细的修正步骤和调查信息。 可以使用其他 Azure 安全中心和 Azure Sentinel 功能进一步调查，从而更深入地了解：

    * 启用 SQL Server 的审核功能以进一步调查。 如果你是 Azure Sentinel 用户，则可以将 SQL 审核日志从 Windows 安全日志事件上传到 Sentinel，并享受内容丰富的调查体验。 [了解有关 SQL Server 审核的详细信息](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15)。
    * 若要改善安全状况，请采纳每个警报中针对主机的安全中心建议。 这将降低未来攻击的风险。 

    [了解有关管理和响应警报的详细信息](security-center-managing-and-responding-alerts.md)。


## <a name="faq---azure-defender-for-sql-servers-on-machines"></a>常见问题解答 - 计算机上的 Azure Defender for SQL 服务器

### <a name="if-i-enable-this-azure-defender-plan-on-my-subscription-are-all-sql-servers-on-the-subscription-protected"></a>如果在我的订阅上启用此 Azure Defender 计划，则订阅上的所有 SQL 服务器是否均受保护？ 

否。 若要在 Azure 虚拟机上保护 SQL Server 部署，或者在已启用 Azure Arc 的计算机上运行 SQL Server，Azure Defender 需要以下两项内容：

- 计算机上的 Log Analytics 代理 
- 相关的 Log Analytics 工作区，用于启用 Azure Defender for SQL 解决方案 

订阅状态（显示在 Azure 门户的“SQL 服务器”页面）反映了默认工作区状态，并且适用于所有连接的计算机。 只有主机上 Log Analytics 代理报告到该工作区的 SQL 服务器会受到 Azure Defender 保护。 




## <a name="next-steps"></a>后续步骤

如需相关材料，请参阅以下文章：

- [针对 SQL 数据库和 Azure Synapse Analytics 的安全警报](alerts-reference.md#alerts-sql-db-and-warehouse)
- [设置安全警报的电子邮件通知](security-center-provide-security-contact-details.md)
- [了解有关 Azure Sentinel 的详细信息](../sentinel/index.yml)
