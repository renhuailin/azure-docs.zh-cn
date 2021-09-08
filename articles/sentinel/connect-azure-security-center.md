---
title: 将 Azure Defender 警报连接到 Azure Sentinel
description: 了解如何从 Azure 安全中心连接 Azure Defender 警报并将其流式传输到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 07/08/2021
ms.author: yelevin
ms.openlocfilehash: 9612b5aaaec10358609abba3878f6f09405ad076
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260115"
---
# <a name="connect-azure-defender-alerts-from-azure-security-center"></a>从 Azure 安全中心连接 Azure Defender 警报

## <a name="background"></a>背景

[Azure Defender](../security-center/azure-defender.md) 是 [Azure 安全中心](../security-center/security-center-introduction.md)的集成云工作负载保护平台 (CWPP)。它是一个安全管理工具，可用来检测并快速应对各种混合云工作负载中的威胁。 

此连接器允许你将 Azure Defender 安全警报从 Azure 安全中心流式传输到 Azure Sentinel，以便可以在更广泛的组织威胁上下文中查看、分析和应对 Defender 警报及其生成的事件。

由于 Azure Defender 自身是针对每个订阅启用的，因此，Azure Defender 连接器也是针对每个订阅分别启用或禁用的。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>警报同步

- 将 Azure Defender 连接到 Azure Sentinel 时，会在这两项服务之间同步已引入到 Azure Sentinel 中的 Azure Defender 警报的状态。 例如，当某个警报在 Azure Defender 中处于已关闭状态时，该警报在 Azure Sentinel 中也会显示为已关闭。

- 在 Azure Defender 中更改警报的状态不会影响包含 Azure Sentinel 警报的任何 Azure Sentinel 事件的状态，只会影响警报自身的状态。

### <a name="bi-directional-alert-synchronization"></a>双向警报同步

> [!IMPORTANT]
>
> - 双向警报同步功能当前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

- 启用双向同步会自动将原始 Azure Defender 警报的状态与包含这些警报的 Azure Sentinel 事件的状态进行同步。 例如，当包含 Azure Defender 警报的 Azure Sentinel 事件被关闭时，对应的原始警报会在 Azure Defender 中自动关闭。

## <a name="prerequisites"></a>先决条件

- 必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 你必须在流式传输的日志的订阅中具有“安全读取者”角色。

- 对于要启用连接器的每个订阅，你需要在 Azure 安全中心启用至少一个 Azure Defender 计划。 若要在订阅上启用 Azure Defender 计划，你必须具有该订阅的“安全管理员”角色。

-  若要启用双向同步，你必须在相关订阅上具有“参与者”或“安全管理员”角色。

## <a name="connect-to-azure-defender"></a>连接到 Azure Defender

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中，选择“Azure Defender”，并在详细信息窗格中选择“打开连接器页面”。

1. 在“配置”下，你会看到租户中的订阅的列表，以及这些订阅与 Azure Defender 之间的连接的状态。 选择要将其警报流式传输到 Azure Sentinel 中的每个订阅旁的“状态”开关。 如果要一次连接多个订阅，可选中相关订阅旁边的复选框，然后选择列表上方的栏中的“连接”按钮。

    > [!NOTE]
    > - 复选框和“连接”开关将仅在你拥有所需权限的订阅上处于活动状态。
    > - 只有选中了至少一个订阅的复选框时，“连接”按钮才会处于活动状态。

1. 若要在某个订阅上启用双向同步，请在列表中定位到该订阅，并从“双向同步(预览版)”列的下拉列表中选择“启用”。  若要一次在多个订阅上启用双向同步，请选中它们的复选框，然后在列表上方的栏中选择“启用双向同步”按钮。

    > [!NOTE]
    > - 复选框和下拉列表将仅在你拥有[所需权限](#prerequisites)的订阅上处于活动状态。
    > - 只有选中了至少一个订阅的复选框时，“启用双向同步”按钮才会处于活动状态。

1. 在列表的“Azure Defender 计划”列中，你可以看到订阅上是否启用了 Azure Defender 计划（这是启用连接器的先决条件）。 此列中每个订阅的值将为空白（表示未启用任何 Defender 计划）、“已全部启用”或“已部分启用”。 显示了“已部分启用”的订阅还会有一个可供选择的“全部启用”链接，单击该链接可前往该订阅的 Azure Defender 配置仪表板，在其中选择要启用的 Defender 计划。 使用列表上方的栏中的“为所有订阅启用 Azure Defender”链接按钮，可以前往 Azure Defender 入门页面，在其中选择要一起启用 Azure Defender 的订阅。

    :::image type="content" source="./media/connect-azure-security-center/azure-defender-config.png" alt-text="屏幕截图显示了 Azure Defender 连接器配置":::

1. 可以选择是否希望 Azure Defender 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下，选择“启用”以启用自动[根据警报创建事件](create-incidents-from-alerts.md)的默认分析规则。 然后，可以在“分析”下的“活动规则”选项卡中编辑此规则。 

    > [!TIP]
    > 为来自 Azure Defender 的警报配置[自定义分析规则](detect-threats-custom.md)时，请考虑警报严重性，避免为信息性警报创建事件。 
    >
    > Azure 安全中心的信息性警报本身不表示安全风险，只有在现有的未处理事件的上下文中进行考虑才有意义。 有关详细信息，请参阅 [Azure 安全中心内的安全警报和事件](../security-center/security-center-alerts-overview.md)。
    > 
    

## <a name="find-and-analyze-your-data"></a>查找并分析数据

> [!NOTE]
> 双向警报同步可能需要几分钟的时间。 警报状态的更改可能不会立即显示。

- Azure Defender 警报存储在 Log Analytics 工作区的 SecurityAlert 表中。

- 若要查询 Log Analytics 中的 Azure Defender 警报，请首先将以下内容复制到查询窗口中：

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- 如需其他有用的示例查询、分析规则模板和建议的工作簿，请参阅连接器页中的“后续步骤”选项卡。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何将 Azure Defender 连接到 Azure Sentinel，以及如何在它们之间同步警报。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- 编写你自己的规则来[检测威胁](detect-threats-custom.md)。