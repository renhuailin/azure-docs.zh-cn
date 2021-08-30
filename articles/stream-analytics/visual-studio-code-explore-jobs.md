---
title: 在 Visual Studio Code 中探索 Azure 流分析作业
description: 本文介绍如何将 Azure 流分析作业导出到本地项目、列出作业和查看作业实体。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 07/21/2021
ms.topic: how-to
ms.openlocfilehash: 85b6dece608f47818f2115d0462dd4a56752581f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750600"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>使用 Visual Studio Code（预览版）浏览 Azure 流分析

适用于 Visual Studio Code 的 Azure 流分析扩展为开发人员提供了管理其流分析作业的轻量级体验。 可以在 Windows、Mac 和 Linux 上使用该扩展。 使用 Azure 流分析扩展，可以：

- [创建](quick-create-visual-studio-code.md)、启动和停止作业
- 将现有作业导出到本地项目
- 列出作业和查看作业实体
- 在作业监视器中查看作业关系图和调试

## <a name="export-a-job-to-a-local-project"></a>将作业导出到本地项目

若要将作业导出到本地项目，请在 Visual Studio Code 的 **流分析资源管理器** 中找到要导出的作业。 然后为项目选择一个文件夹。 项目会导出到你选择的文件夹，你可以继续从 Visual Studio Code 管理该作业。 有关使用 Visual Studio Code 管理流分析作业的详细信息，请参阅 Visual Studio Code [快速入门](quick-create-visual-studio-code.md)。

![在 Visual Studio Code 中导出 ASA 作业](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>列出作业和查看作业实体

可使用作业视图从 Visual Studio 中与 Azure 流分析作业进行交互。


1. 单击 Visual Studio Code 活动栏上的 **Azure** 图标，然后展开“流分析”节点。 你的作业应显示在你的订阅下。

   ![打开流分析资源管理器](./media/vscode-explore-jobs/open-explorer.png)

2. 展开作业节点，可以打开并查看作业查询、配置、输入、输出和函数。 

3. 右键单击作业节点，然后选择“在门户中打开作业视图”节点以在 Azure 门户中打开作业视图。

   ![在门户中打开作业视图](./media/vscode-explore-jobs/open-job-view.png)

## <a name="view-job-diagram-and-debug-in-job-monitor"></a>在作业监视器中查看作业关系图和调试

可以使用 Visual Studio Code 中的作业监视器查看 Azure 流分析作业并对其进行故障排除。

### <a name="view-job-diagram-and-job-summary"></a>查看作业关系图和作业摘要
1. 选择“作业监视器”。 此时应显示作业监视器，并自动加载作业关系图。
   
   ![打开作业监视器](./media/vscode-explore-jobs/open-job-monitor.png)

2.  可以查看作业关系图，然后单击“作业摘要”以查看作业的属性和信息。 

      ![查看作业摘要](./media/vscode-explore-jobs/view-jobs-summary.png)

3.  单击“测试连接”按钮，可以测试输入和输出连接。

      ![测试连接](./media/vscode-explore-jobs/test-connection.png)

4.  还可以单击“查找脚本”按钮来查看查询。
   
      ![查看查询](./media/vscode-explore-jobs/view-query.png)

### <a name="monitor-and-debug-with-metrics"></a>使用“指标”面板进行监视和调试

1.  单击箭头按钮，可以打开“指标”面板。

      ![打开“指标”面板](./media/vscode-explore-jobs/open-metrics-panel.png)

2.  可以与该面板进行交互，并使用图表中显示的关键指标分析作业。 可以选择查看作业级指标或节点级指标。 还可以确定希望在图表中显示的指标。

      ![查看指标](./media/vscode-explore-jobs/view-metrics.png)

### <a name="debug-with-diagnostic-logs-and-activity-logs"></a>使用诊断日志和活动日志进行调试

可以查看作业的诊断日志和活动日志，以进行故障排除。

1. 选择“诊断日志”选项卡。

   ![查看诊断日志](./media/vscode-explore-jobs/view-diagnostic-log.png)

2. 选择“活动日志”选项卡 

   ![查看活动日志](./media/vscode-explore-jobs/view-activity-logs.png)

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio Code 中创建 Azure 流分析云作业（预览）](quick-create-visual-studio-code.md)
