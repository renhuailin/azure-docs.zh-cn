---
title: 排查和诊断工作流故障
description: 了解如何在 Azure 逻辑应用中对工作流中的问题、错误和故障进行排查和诊断
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995040"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>在 Azure 逻辑应用中排查和诊断工作流故障

逻辑应用生成的信息有助于诊断和调试应用中的问题。 可以在 Azure 门户中通过查看工作流中的每个步骤来诊断逻辑应用。 或者，可以在工作流中增加一些步骤，进行运行时调试。

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>查看触发器历史记录

每个逻辑应用的运行都以触发器尝试开始，因此，如果触发器没有触发，请遵循以下步骤：

1. 通过[查看触发器历史记录](../logic-apps/monitor-logic-apps.md#review-trigger-history)查看触发器状态。 若要查看有关触发器尝试的详细信息，请选择该触发器事件，例如：

   ![查看触发器状态和历史记录](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. 检查触发器的输入，确认它们按预期显示。 在“输入链接”下，选择显示“输入”窗格的链接 。

   触发器输入包含触发器预期的数据，并需要启动工作流。 查看这些输入可帮助你确定触发器输入是否正确，以及是否满足条件以使工作流继续进行。

   例如，下面的 `feedUrl` 属性包含错误的 RSS 源值：

   ![查看触发器输入中的错误](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. 检查触发器输出（如果有）以确认它们按预期显示。 在“输出链接”下，选择显示“输出”窗格的链接 。

   触发器输出包含触发器传递到工作流中的下一个步骤的数据。 查看这些输出可以帮助你确定是否将正确或预期的值传递到工作流中的下一个步骤，例如：

   ![查看触发器输出中的错误](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 如果找到任何不能识别的内容，请详细了解 Azure 逻辑应用中的[不同内容类型](../logic-apps/logic-apps-content-type.md)。

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>查看运行历史记录

每次针对某个项或事件激发触发器时，逻辑应用引擎将针对每个项或事件创建并运行一个独立的工作流实例。 如果运行失败，请按照以下步骤查看该次运行过程中发生的情况，包括工作流中的每个步骤的状态，以及每个步骤的输入和输出。

1. 通过查看[运行历史记录](../logic-apps/monitor-logic-apps.md#review-runs-history)检查工作流的运行状态。 若要查看有关失败的运行的详细信息（包括在其状态下运行的所有步骤），请选择失败的运行。

   ![查看运行历史记录并选择失败的运行](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 运行中的所有步骤都显示后，展开第一个失败的步骤。

   ![展开第一个失败的步骤](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 检查失败步骤的输入，确认它们是否按预期显示。

1. 查看某个特定运行中每个步骤的详细信息。 在“运行历史记录”下，选择要检查的运行。

   ![查看运行历史记录](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![查看逻辑应用运行的详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 若要检查输入、输出和特定步骤的任何错误消息，请选择该步骤，以便展开该形状并显示详细信息。 例如：

   ![查看步骤详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>执行运行时调试

若要帮助进行调试，可向逻辑应用工作流添加诊断步骤，同时查看触发器和运行历史记录。 例如，可以添加使用 [Webhook Tester](https://webhook.site/) 服务的步骤，以便可检查 HTTP 请求并确定其确切大小、形状和格式。

1. 转到 [Webhook Tester](https://webhook.site/) 站点并复制生成的唯一 URL。

1. 在逻辑应用中，添加 HTTP POST 操作以及想要测试的正文内容（例如，某个表达式或另一个步骤的输出）。

1. 将 Webhook Tester 中的 URL 粘贴到 HTTP POST 操作中。

1. 若要查看请求在从逻辑应用引擎生成时的格式，请运行逻辑应用并重新访问 Webhook Tester 站点了解更多详细信息。

## <a name="next-steps"></a>后续步骤

* [监视逻辑应用](../logic-apps/monitor-logic-apps.md)
