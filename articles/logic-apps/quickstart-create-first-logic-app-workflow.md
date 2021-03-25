---
title: 快速入门 - 创建第一个逻辑应用工作流 - Azure 门户
description: 使用本快速入门指南在 Azure 门户中构建你的第一个自动化逻辑应用工作流。 了解逻辑应用中系统集成和企业应用程序集成 (EAI) 解决方案的基本知识。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: d90b9e38158d951990fffc21a43317c688da12c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052036"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>快速入门：创建第一个逻辑应用工作流 - Azure 门户

本快速入门介绍了如何通过 [Azure 门户](https://portal.azure.com)，在 [Azure 逻辑应用](logic-apps-overview.md)中构建你的第一个工作流。 本介绍性指南还介绍了逻辑应用服务的基本概念，包括如何创建新的逻辑应用、如何向逻辑应用添加触发器和操作，以及如何测试逻辑应用。 按照此快速入门构建一个示例逻辑应用，该应用会定期检查 RSS 源并发送有关新项的电子邮件通知。 以下屏幕截图显示了此示例逻辑应用的概要工作流：

![逻辑应用设计器的屏幕截图，显示了示例逻辑应用，其中触发器是 RSS 源，操作是发送电子邮件。](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

如果想要了解如何通过其他接口和应用来创建和管理第一个逻辑应用，请参阅以下其他逻辑应用快速入门： 

* [使用 Azure 命令行接口 (Azure CLI) 创建和管理逻辑应用](quickstart-logic-apps-azure-cli.md)
* [在 Visual Studio Code 中创建和管理逻辑应用](quickstart-create-logic-apps-visual-studio-code.md)
* [在 Visual Studio 中创建和管理逻辑应用](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，可以[注册免费的 Account 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 逻辑应用支持的服务（例如 Office 365 Outlook 或 Outlook.com）提供的电子邮件帐户。 如需其他受支持的电子邮件提供程序，请[查看连接器列表](/connectors/)。

    > [!IMPORTANT]
    > 如果使用的是 [Gmail 连接器](/connectors/gmail/)，请注意，只有 G Suite 帐户才能在逻辑应用中不受限制地使用此连接器。 如果有 Gmail 用户帐户，则除非[创建用于通过 Gmail 连接器进行身份验证的 Google 客户端应用](/connectors/gmail/#authentication-and-bring-your-own-application)，否则只能将此连接器与 Google 批准的特定服务一起使用。 有关详细信息，请参阅 [Azure 逻辑应用中 Google 连接器的数据安全和隐私策略](../connectors/connectors-google-data-security-privacy-policy.md)。

* 如果逻辑应用需要通过仅限流量流经特定 IP 地址的防火墙进行通信，则该防火墙需要允许访问该逻辑应用所在的 Azure 区域中的逻辑应用服务或运行时使用的[入站](logic-apps-limits-and-config.md#inbound)和[出站](logic-apps-limits-and-config.md#outbound) IP 地址。 如果逻辑应用还使用[托管连接器](../connectors/apis-list.md#managed-api-connectors)（如 Office 365 Outlook 连接器或 SQL 连接器），或使用[自定义连接器](/connectors/custom-connectors/)，则防火墙还需要允许访问逻辑应用的 Azure 区域中的所有[托管连接器出站 IP 地址](logic-apps-limits-and-config.md#outbound)。

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>创建逻辑应用

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户搜索框中，输入 `logic apps`，然后选择“逻辑应用”。

   ![Azure 门户搜索框的屏幕截图，其中显示“逻辑应用”作为搜索词，“逻辑应用”作为所选搜索结果。](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. 在“逻辑应用”页上，选择“添加”。

   ![Azure 门户中“逻辑应用”服务页的屏幕截图，显示了逻辑应用列表和所选按钮“添加”。](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. 在“逻辑应用”窗格上，为逻辑应用提供基本的详细信息和设置。 为此，请为示例逻辑应用创建新的[资源组](../azure-resource-manager/management/overview.md#terminology)。

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **名称** | <*logic-app-name*> | 逻辑应用的名称，该名称必须在区域中是唯一的。 名称只能包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号（`(`、`)`）和句点 (`.`)。 此示例使用“My-First-Logic-App”。 |
   | **订阅** | <*Azure-subscription-name*> | Azure 订阅的名称。 |
   | **资源组** | <*Azure-resource-group-name*> | 要在其中创建逻辑应用的 [Azure 资源组](../azure-resource-manager/management/overview.md#terminology)的名称。 资源组的名称必须在区域中是唯一的。 此示例使用“My-First-LA-RG”。 |
   | **位置** | <*Azure-region*> | 用于存储逻辑应用信息的 Azure 区域。 此示例使用“美国西部”。 |
   | **Log Analytics** | 关 | 诊断日志记录的设置，默认情况下为“关闭”。 对于本示例，请保留“关闭”设置。 |
   ||||

   ![逻辑应用“创建”页的屏幕截图，显示了带有新逻辑应用详细信息的窗格。](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. 准备就绪后，选择“查看 + 创建”。 确认所提供的详细信息，并选择“创建”。

1. Azure 成功部署应用后，请选择“转到资源”。 或者，可以通过在搜索框中键入名称来查找和选择逻辑应用。

   ![“资源部署”页的屏幕截图，显示了所选按钮“转到资源”。](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   逻辑应用设计器打开并显示一个包含简介视频和常用触发器的页面。 在“模板”下选择“空白逻辑应用”。

   ![逻辑应用设计器的屏幕截图，显示了模板库和所选模板“空白逻辑应用”。](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

接下来，[将触发器添加到逻辑应用](#add-rss-trigger)。

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>添加 RSS 触发器

每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work)开始，该触发器在发生特定事件或满足特定条件的情况下触发。 触发器每次发现新项时都会触发，并且逻辑应用引擎会创建一个逻辑应用实例来启动并运工作流。 如果触发器未发现新项，则不会触发，并且不会在执行此检查时创建实例或运行工作流。

在此快速入门示例中，在[创建逻辑应用](#create-your-logic-app)后，可以添加一个触发器，用于检查 RSS 源中的新项，并在有新项时触发。 还可以创建具有不同类型触发器的逻辑应用，如[创建基于自动批准的工作流](tutorial-process-mailing-list-subscriptions-workflow.md)教程中所述。

1. 在“逻辑应用设计器”的搜索框下，选择“全部”。

1. 要查找 RSS 连接器，请在搜索框中输入 `rss`。 从“触发器”列表中，选择 RSS 触发器“发布源项时” 。

   ![显示逻辑应用设计器的屏幕截图，其中包含搜索框中的“rss”和所选 RSS 触发器“发布源项时”。](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. 为触发器提供 RSS 源 URL。 然后，通过设置时间间隔和频率来定义触发器的计划。

   | 属性 | 值 | 说明 |
   | -------- | ----- | ----------- |
   | **RSS 源 URL** | <*RSS-feed-URL*> | 要监视的 RSS 源的 URL。 本示例使用《华尔街日报》的 RSS 源 `https://feeds.a.dj.com/rss/RSSMarketsMain.xml`。 但对于本示例，可以使用不需要 HTTP 授权的任何 RSS 源。 选择经常发布的 RSS 源，以便以后可以轻松地测试逻辑应用。 |
   | 间隔 | 1 | 在 RSS 源检查之间需等待的时间间隔数。 本示例使用 1 分钟间隔。 |
   | **频率** | Minute | RSS 源检查的每个间隔的时间单位。 本示例使用 1 分钟间隔。 |
   ||||

   ![显示具有 RSS 触发器设置的逻辑应用设计器的屏幕截图，其中包括 RSS URL、频率和间隔。](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. 通过单击触发器的标题栏，暂时折叠触发器的详细信息。

   ![显示带有折叠逻辑应用形状的逻辑应用设计器的屏幕截图。](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. 通过选择设计器工具栏上的“保存”，保存逻辑应用。

逻辑应用现已生成，但除了检查 RSS 源，不能执行任何操作。 接下来，[添加操作](#add-email-action)以定义触发器触发时会发生的情况。

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>添加“发送电子邮件”操作

[为逻辑应用添加触发器](#add-rss-trigger)后，必须添加[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)以确定当逻辑应用检查 RSS 源并出现新项时的响应。 还可以创建具有更复杂的操作的逻辑应用，如[使用逻辑应用、Azure Functions 和 Azure 存储处理电子邮件](./tutorial-process-email-attachments-workflow.md)教程中所述。

> [!NOTE]
> 此示例使用 Office 365 Outlook 作为电子邮件服务。 如果在逻辑应用中使用的是其他受支持的电子邮件服务，则用户界面可能会有所不同。 但是，用于连接到其他电子邮件服务的基本概念仍保持不变。

1. 在“发布源项时”触发器下，选择“新建步骤”。

   ![逻辑应用设计器的屏幕截图，显示选中了按钮“新建步骤”的工作流。](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. 在“选择操作”和搜索框下，选择“全部”。

1. 在搜索框中，输入 `send an email` 以便查找提供此操作的连接器。 若要通过对操作列表进行筛选来查找特定的应用或服务，可以先选择应用或服务。

   例如，如果你使用 Microsoft 工作或学校帐户，并且想要使用 Office 365 Outlook，请选择“Office 365 Outlook”。 或者，如果你使用的是个人 Microsoft 帐户，则可以选择“Outlook.com”。 本示例将继续使用 Office 365 Outlook：

   ![逻辑应用设计器的屏幕截图，显示选中了电子邮件连接器“Office 365 Outlook”的操作步骤。](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   现在可以更轻松地找到并选择要使用的操作，例如 `send an email`：

   ![逻辑应用设计器的屏幕截图，显示了电子邮件连接器“Office 365 Outlook”的已筛选操作列表。](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. 如果选择的电子邮件连接器提示你对标识进行身份验证，请立即完成该步骤。 若要使此示例正常工作，必须在逻辑应用和电子邮件服务之间创建连接。 

    > [!NOTE]
    > 此示例演示如何对 Office 365 Outlook 连接器进行手动身份验证。 但是，其他连接器可能支持不同的身份验证类型。
    > 还可以根据用例，以不同的方式处理逻辑应用的身份验证。 例如，如果使用 Azure 资源管理器模板进行部署，则可以对经常更改的输入（如连接详细信息）进行参数化以提高安全性。 有关详细信息，请参阅以下主题：
   > * [部署的模板参数](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [授权 OAuth 连接](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [使用托管标识验证访问](../logic-apps/create-managed-service-identity.md)
   > * [为逻辑应用部署验证连接](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. 在“发送电子邮件”操作中，指定要包含在电子邮件通知中的信息。

   1. 在“收件人”框中，输入收件人的电子邮件地址。 对于本示例，请使用你的电子邮件地址。

        > [!NOTE]
        > 在逻辑应用设计器中的“收件人”框和某些其他输入框内单击时，会出现“添加动态内容”列表 。 此示例将在后续步骤中使用动态内容。 “添加动态内容”列表显示了上一步中的所有可用输出，你可以将其用作当前操作的输入。

   1. 在“主题”框中，输入电子邮件通知的主题。 对于此示例，请输入以下带有尾随空白的文本：`New RSS item: `

      ![逻辑应用设计器的屏幕截图，显示了“发送电子邮件”操作和“主题”属性框内的光标。](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. 从“添加动态内容”列表中，选择“源标题”，该标题为触发器“发布源项时”的输出  。 电子邮件通知使用此输出获取 RSS 项的标题。

      ![逻辑应用设计器的屏幕截图，显示了“发送电子邮件”操作和“主题”属性框内的光标，还有一个打开的动态内容列表和选中的输出“源标题”。](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > 在动态内容列表中，如果没有从“发布源项时”触发器中显示任何输出，则请在操作的标头旁边选择“查看更多” 。
      > 
      > ![逻辑应用设计器的屏幕截图，显示了打开的动态内容列表，以及为触发器选择的“查看更多”。](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      完成后，电子邮件主题如以下示例所示：

      ![逻辑应用设计器的屏幕截图，显示了“发送电子邮件”操作和具有“源标题”属性的示例电子邮件主题。](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      如果设计器上出现了“For each”循环，则表示你为数组选择了一个令牌。例如“categories-Item”令牌。 对于这些类型的令牌，设计器会自动围绕引用该令牌的操作添加此循环。 这样一来，逻辑应用会对每个数组项执行同一操作。 若要删除循环，请选择循环的标题栏上的省略号 (**...**)，然后选择“删除”。

   1. 在“正文”框中，输入电子邮件正文内容。 在此示例中，内容包括三个属性，其中每个属性都具有描述性文本：“源标题”属性 `Title:`；“源发布日期”属性 `Date published:`；“主源链接”属性 `Link:`  。 若要在编辑框中添加空行，请按 Shift + Enter。

      | 属性 | 说明 |
      |----------|-------------|
      | 源标题 | 项的标题 |
      | 源发布日期 | 项的发布日期和时间 |
      | 源主链接 | 项的 URL |
      |||

      ![逻辑应用设计器的屏幕截图，显示了“发送电子邮件”操作和“正文”框中选定的属性。](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. 保存逻辑应用。 在设计器菜单中选择“保存”。

接下来，[测试逻辑应用是否正常运行](#test-logic-app)。

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>运行逻辑应用

创建示例逻辑应用后，请确认已正确配置工作流。 可以等待逻辑应用根据指定的计划检查 RSS 源。 或者，可以通过选择逻辑应用设计器工具栏上的“运行”来手动运行逻辑应用，如以下屏幕截图中所示。 

如果 RSS 源有新项，逻辑应用会为每个新项发送一封电子邮件。 否则，逻辑应用会等到下一个间隔再次检查 RSS 源。 

![逻辑应用设计器的屏幕截图，显示在设计器工具栏上选择了“运行”按钮。](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

以下屏幕截图显示了此示例逻辑应用的示例电子邮件通知。 电子邮件包含设计器中选定的每个 RSS 源项的详细信息，以及为每项添加的描述性文本。

![Outlook 的屏幕截图，显示出现新的 RSS 源项时收到的示例电子邮件，其中包含项标题、发布日期和链接。](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

如果未按预期收到来自逻辑应用的通知电子邮件：

* 请检查电子邮件帐户的垃圾邮件文件夹，以防错误筛选了邮件。
* 请确保正在使用的 RSS 源自上次计划或手动检查后已发布项目。

## <a name="clean-up-resources"></a>清理资源

测试完此示例逻辑应用后，请通过删除为本示例创建的资源组来清理逻辑应用和所有相关资源。

> [!NOTE]
> [删除逻辑应用](manage-logic-apps-with-azure-portal.md#delete-logic-apps)后，任何新运行都不会实例化。 所有正在进行和挂起的运行都将取消。 如果有成千上万个运行，取消操作可能需要很长时间才能完成。

1. 在 Azure 搜索框中，输入 `resource groups`，然后选择“资源组”。

   ![显示带搜索词“资源组”的 Azure 门户搜索框的屏幕截图。](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. 查找并选择逻辑应用的资源组。 在“概述”窗格上，选择“删除资源组”。

   ![显示带选定资源组和“删除资源组”按钮的 Azure 门户的屏幕截图。](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. 出现确认窗格时，输入资源组名称，然后选择“删除”。

   ![显示带确认窗格和要删除的已输入资源组名称的 Azure 门户的屏幕截图。](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>后续步骤

在本快速入门中，你在 Azure 门户中创建了第一个逻辑应用程序，以检查 RSS 源是否按计划进行更新，并发送了有关每个新源项的电子邮件通知。 

若要了解如何在逻辑应用中创建更高级的基于计划的工作流，请参阅以下教程：

> [!div class="nextstepaction"]
> [使用基于计划的逻辑应用检查流量](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)