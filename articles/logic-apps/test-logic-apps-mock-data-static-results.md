---
title: 模拟测试工作流
description: 设置模拟数据，以在不影响生产环境的情况下在 Azure 逻辑应用中测试工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/08/2021
ms.openlocfilehash: 4167dcffe0a1b4db50f6d1580ad6284f2cf9321d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712488"
---
# <a name="test-workflows-with-mock-data-in-azure-logic-apps-preview"></a>在 Azure 逻辑应用中使用模拟数据测试工作流（预览版）

> [!NOTE]
> 此功能为预览版，受 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)限制。

若要在不实际调用或访问实时应用、数据、服务或系统的情况下测试工作流，可以设置并返回操作中的模拟值。 例如，你可能想要根据各种条件测试不同的操作路径、强制错误、提供特定的消息响应正文，甚至尝试跳过一些步骤。 对操作设置模拟数据测试不会运行该操作，而是返回模拟数据。

例如，如果为 Outlook 365 发送邮件操作设置了模拟数据，Azure 逻辑应用只返回你提供的模拟数据，而不是调用 Outlook 并发送电子邮件。

本文展示了如何为[逻辑应用（消耗）和逻辑应用（标准）资源类型 ](logic-apps-overview.md#resource-environment-differences)的工作流中的操作设置模拟数据。 可以找到以前使用这些模拟数据的工作流运行，并将现有操作输出重用为模拟数据。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以<a href="https://azure.microsoft.com/free/?WT.mc_id=A261C142F" target="_blank">注册免费的 Azure 帐户</a>。

* 要在其中设置模拟数据的逻辑应用资源和工作流。 本文使用 Recurrence 触发器和 HTTP 操作作为示例工作流。 

  如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](logic-apps-overview.md)和[快速入门：创建第一个逻辑应用工作流](quickstart-create-first-logic-app-workflow.md)。

<a name="enable-mock-data"></a>

## <a name="enable-mock-data-output"></a>启用模拟数据输出

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)中，在设计器中打开逻辑应用。

1. 在要返回模拟数据的操作上，执行以下步骤：

   1. 在操作的右上角，选择省略号 (...) 按钮，然后选择“测试”，例如：

      ![显示 Azure 门户、工作流设计器、操作快捷菜单和选定的“测试”的屏幕截图。](./media/test-logic-apps-mock-data-static-results/select-testing.png)

   1. 在“测试”窗格上，选择“启用静态结果(预览版)”。  当操作的必需 (*) 属性出现时，指定要作为操作响应返回的模拟输出值。

      这些属性根据所选操作类型的不同而不同。 例如，HTTP 操作具有以下必需属性：

      | 属性 | 说明 |
      |----------|-------------|
      | **状态** | 要返回的操作状态 |
      | **状态代码** | 作为输出返回的特定状态代码 |
      | **标头** | 要返回的标头内容 |
      |||

      ![选择“启用静态结果”后显示“测试”窗格的屏幕截图。](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      > [!TIP]
      > 要以 JavaScript 对象表示法 (JSON) 格式输入值，请选择“切换到 JSON 模式”（![“切换到 JSON 模式”的图标](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)）。

   1. 对于可选属性，请打开“选择可选字段”列表，然后选择要模拟的属性。

      ![屏幕截图显示了“测试”窗格，其中“选择可选字段”列表已打开。](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 准备就绪后，选择“完成”。

   在操作的右上角，标题栏现在显示一个测试量杯图标（![静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)），指示已启用静态结果。

   ![显示带有静态结果图标的操作的屏幕截图。](./media/test-logic-apps-mock-data-static-results/static-result-enabled.png)

   要查找使用模拟数据的工作流运行，请查看本主题后文的[查找使用静态结果的运行](#find-runs-mock-data)。

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)中，在设计器中打开逻辑应用。

1. 在设计器上，选择要返回模拟数据的操作，以便显示操作详细信息窗格。

1. 在右侧打开操作详细信息窗格后，选择“测试”。

   ![显示 Azure 门户、工作流设计器、操作详细信息窗格并且选择了“测试”的屏幕截图。](./media/test-logic-apps-mock-data-static-results/select-testing-standard.png)

1. 在“测试”选项卡上，选择“启用静态结果(预览)”。  当操作的必需 (*) 属性出现时，指定要作为操作响应返回的模拟输出值。

   这些属性根据所选操作类型的不同而不同。 例如，HTTP 操作具有以下必需属性：

   | 属性 | 说明 |
   |----------|-------------|
   | **状态** | 要返回的操作状态 |
   | **状态代码** | 作为输出返回的特定状态代码 |
   | **标头** | 要返回的标头内容 |
   |||

   ![显示选择“启用静态结果”后的“测试”选项卡的屏幕截图。](./media/test-logic-apps-mock-data-static-results/enable-static-result-standard.png)

   > [!TIP]
   > 要以 JavaScript 对象表示法 (JSON) 格式输入值，请选择“切换到 JSON 模式”（![“切换到 JSON 模式”的图标](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)）。

1. 对于可选属性，请打开“选择可选字段”列表，然后选择要模拟的属性。

   ![屏幕截图显示了“测试”窗格，其中“选择可选字段”列表已打开。](./media/test-logic-apps-mock-data-static-results/optional-properties-standard.png)

1. 准备就绪后，选择“完成”。

   动作的右下角现在显示一个测试量杯图标（![静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)），指示已启用静态结果。

   ![在设计器上显示带有静态结果图标的操作的屏幕截图。](./media/test-logic-apps-mock-data-static-results/static-result-enabled-standard.png)

   要查找使用模拟数据的工作流运行，请查看本主题后文的[查找使用静态结果的运行](#find-runs-mock-data)。

---

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-mock-data"></a>查找使用模拟数据的运行

### <a name="consumption"></a>[消耗](#tab/consumption)

要查找操作使用模拟数据的早期工作流运行，请查看该工作流的运行历史记录。

1. 在 [Azure 门户](https://portal.azure.com)中，在设计器中打开逻辑应用。

1. 在逻辑应用资源菜单中，选择“概述”。

1. 在“Essentials”部分下，选择“运行历史记录”（如果尚未选择）。 

1. 在“运行历史记录”表中，查找“静态结果”列。 

   包含具有模拟数据输出的操作的任何运行都会将“静态结果”列设置为“已启用”，例如： 

   ![显示包含“静态结果”列的工作流运行历史记录的屏幕截图。](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 要查看使用模拟数据的运行中的操作，请选择所需的运行，其中“静态结果”列设置为“已启用”。 

   使用静态结果的操作显示量杯（![静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)）图标，例如：

   ![显示包含使用静态结果的操作的工作流运行历史记录的屏幕截图。](./media/test-logic-apps-mock-data-static-results/static-result-enabled-run-details.png)

### <a name="standard"></a>[标准](#tab/standard)

要查找操作使用模拟数据的其他工作流运行，必须检查每个运行。

1. 在 [Azure 门户](https://portal.azure.com)中，在设计器中打开逻辑应用。

1. 在工作流菜单上，选择“概述”。

1. 在“Essentials”部分下，选择“运行历史记录”（如果尚未选择）。 

1. 在“运行历史记录”表中，选择要查看的运行。

   ![显示工作流运行历史记录的屏幕截图。](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. 在“运行详细信息”窗格中，检查是否有任何操作显示测试量杯（![静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)）图标，例如：

   ![显示包含使用静态结果的操作的工作流运行历史记录的屏幕截图。](./media/test-logic-apps-mock-data-static-results/run-history-static-result-standard.png)

---

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs-as-mock-data"></a>重用以前的输出作为模拟数据

如果之前运行过含输出的工作流，则可以通过复制和粘贴来自该运行的输出，从而重复使用这些输出作为模拟数据。

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)中，在设计器中打开逻辑应用。

1. 在逻辑应用资源菜单中，选择“概述”。

1. 在“Essentials”部分下，选择“运行历史记录”（如果尚未选择）。  从出现的列表中，选择所需的工作流运行。

   ![显示工作流运行历史记录的屏幕截图。](./media/test-logic-apps-mock-data-static-results/select-run.png)

1. 在“运行详细信息”窗格打开后，展开具有所需输出的操作。

1. 在“输出”部分中，选择“显示原始输出”。 

1. 在“输出”窗格中，复制所需的完整 JavaScript 对象表示法 (JSON) 对象或特定的子节，例如 outputs 节，或者只是 headers 节。

1. 查看前面有关如何为操作[设置模拟数据](#enable-mock-data)的章节，并按照相关步骤打开操作的“测试”窗格。

1. “测试”窗格打开后，请选择以下任一步骤：

   * 若要粘贴完整的 JSON 对象，请在“测试”标签旁选择“切换到 JSON 模式”（![“切换到 json 模式”图标](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)）： 

     ![显示已选择“切换到 JSON 模式”图标以粘贴完整 JSON 对象的屏幕截图。](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 要仅粘贴某一 JSON 节，请在该节的标签（例如 Output 或 Headers ）旁边，选择“切换到 JSON 模式”，例如：  

     ![显示已选择“切换到 JSON 模式”图标以粘贴 JSON 对象中的节的屏幕截图。](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output.png)

1. 在 JSON 编辑器中，粘贴前面复制的 JSON。

   ![显示编辑器中的粘贴 JSON 的屏幕截图。](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完成后，选择“完成”。 或者，若要返回到设计器，请选择“切换编辑器模式”（![“切换编辑器模式”图标](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)）。

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)中，在设计器中打开逻辑应用。

1. 在工作流菜单上，选择“概述”。

1. 在“Essentials”部分下，选择“运行历史记录”（如果尚未选择）。 

1. 在“运行历史记录”表中，选择要查看的运行。

   ![显示工作流运行历史记录的屏幕截图。](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. 在“运行详细信息”窗格打开后，选择具有所需输出的操作。

1. 在“输出”部分中，选择“显示原始输出”。 

1. 在“输出”窗格中，复制所需的完整 JavaScript 对象表示法 (JSON) 对象或特定的子节，例如 outputs 节，或者只是 headers 节。

1. 查看前面有关如何为操作[设置模拟数据](#enable-mock-data)的章节，并按照相关步骤打开操作的“测试”选项卡。

1. “测试”选项卡打开后，请选择以下任一步骤：

   * 若要粘贴完整的 JSON 对象，请在“测试”标签旁选择“切换到 JSON 模式”（![“切换到 json 模式”图标](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)）： 

     ![显示已选择“切换到 JSON 模式”图标以粘贴完整 JSON 对象的屏幕截图。](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete-standard.png)

   * 要仅粘贴某一 JSON 节，请在该节的标签（例如 Output 或 Headers ）旁边，选择“切换到 JSON 模式”，例如：  

     ![显示已选择“切换到 JSON 模式”图标以粘贴 JSON 对象中的节的屏幕截图。](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output-standard.png)

1. 在 JSON 编辑器中，粘贴前面复制的 JSON。

   ![显示编辑器中的粘贴 JSON 的屏幕截图。](./media/test-logic-apps-mock-data-static-results/json-editing-mode-standard.png)

1. 完成后，选择“完成”。 或者，若要返回到设计器，请选择“切换编辑器模式”（![“切换编辑器模式”图标](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)）。

---

## <a name="disable-mock-data"></a>禁用模拟数据

关闭操作的静态结果不会从最后的设置中删除值。 因此，如果在同一操作上再次开启静态结果，则可以继续使用以前的值。

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)中，在设计器中打开逻辑应用。 查找要禁用模拟数据的操作。

1. 在操作的右上角，选择测试量杯图标（![静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)).

   ![显示选择了操作和测试量杯操作的屏幕截图。](./media/test-logic-apps-mock-data-static-results/disable-static-result.png)

1. 选择“禁用静态结果” > “完成”。 

   ![显示选定了“禁用静态结果”的屏幕截图。](./media/test-logic-apps-mock-data-static-results/disable-static-result-button.png)

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)中，在设计器中打开逻辑应用。 选择要禁用模拟数据的操作。

1. 在“操作详细信息”窗格中，选择“测试”选项卡。

1. 选择“禁用静态结果” > “完成”。 

   ![显示针对“标准”选择了“禁用静态结果”的屏幕截图。](./media/test-logic-apps-mock-data-static-results/disable-static-result-button-standard.png)

---

## <a name="reference"></a>参考

有关基础工作流定义中的此项设置的详细信息，请参阅[静态结果 - 工作流定义语言的架构参考](logic-apps-workflow-definition-language.md#static-results)和 [runtimeConfiguration.staticResult - 运行时配置设置](logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure 逻辑应用](logic-apps-overview.md)的详细信息