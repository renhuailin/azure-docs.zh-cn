---
title: 将 Azure 存储帐户诊断日志连接到 Azure Sentinel
description: 了解如何使用 Azure Policy 将 Azure 存储帐户诊断日志连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: af9f5218b7f7f94ddc73fb67fe82427d1cfbfff2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743071"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>连接 Azure 存储帐户诊断日志

> [!IMPORTANT]
> Azure 存储帐户连接器目前以预览版提供。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure 存储帐户适用于新式数据存储方案的云解决方案。 它包含所有数据对象：Blob、文件、队列、表和磁盘。

通过此连接器，你可以将 Azure 存储帐户的诊断日志流式传输到 Azure Sentinel，这样便可以持续监视活动并检测整个组织内所有 Azure 存储资源中的安全威胁。

详细了解[监视 Azure 存储帐户](../storage/common/storage-analytics-logging.md)。

## <a name="prerequisites"></a>先决条件

将 Azure 存储帐户诊断日志引入到 Azure Sentinel 中：

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 要使用 Azure Policy 将日志流式处理策略应用到 Azure 存储资源，必须具有策略分配范围的“所有者”角色。

## <a name="connect-to-azure-storage-account"></a>连接到 Azure 存储帐户

此连接器使用 Azure Policy 将单个日志流式处理配置应用于定义为范围的 Azure 存储帐户资源集合。 可以在连接器页面左侧的“数据类型”下看到可用的 Azure 存储日志和指标类型。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Azure 存储帐户”，然后在预览窗格中选择“打开连接器页面” 。

1. 在连接器页面的“配置”部分中，展开“从 Azure 存储帐户大规模流式传输诊断日志” 。

1. 选择“启动 Azure Policy 分配向导”按钮。

    策略分配向导随即打开，可在其中创建名为“为存储帐户配置诊断设置以传输到 Log Analytics 工作区”的新策略。

    1. 在“基本信息”选项卡中，单击“范围”旁的省略号按钮，选择你的订阅（或资源组） 。 还可以添加说明。

    1. 在“参数”选项卡中：
        - 从“Log Analytics 工作区”下拉列表中选择你的 Azure Sentinel 工作区。
        - 从“要部署的存储服务”下拉列表中，选择要向其部署诊断设置的存储资源类型（文件、表、队列等）。
        - 按原样保留“设置名称”和“效果”字段 。
        - 剩余的下拉字段表示可用的诊断日志和指标类型。 将要引入的所有类型保持标记为“True”。

    1. 以上步骤会将该策略应用于所有将来的存储资源。 若要对现有资源应用策略，请选择“修正”选项卡，然后勾选“创建修正任务”复选框。

    1. 在“查看 + 创建”选项卡中，单击“创建”   。 你的策略现已分配给所选的作用域。

> [!NOTE]
>
> 对于此特定数据连接器，仅当在过去 14 天内的某个时间点引入数据时，连接状态指示器（数据连接器库中的色带以及数据类型名称旁边的连接图标）才显示为“已连接”（绿色）。 一旦 14 天过去但未引入数据，连接器将显示为已断开连接。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何将 Azure 存储帐户连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
