---
title: 生成基于审批的自动化工作流
description: 教程 - 使用 Azure 逻辑应用来创建基于审批的自动化工作流，以便处理邮件列表订阅
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: c6186e6e9f60e852f77943834bcd1ae3d526491d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777268"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>教程：使用 Azure 逻辑应用创建自动化的基于审批的工作流

本教程介绍如何生成示例[逻辑应用](../logic-apps/logic-apps-overview.md)，用于自动完成基于审批的工作流。 具体说来，此示例逻辑应用处理通过 [MailChimp](https://mailchimp.com/) 服务托管的邮件列表的订阅请求。 此逻辑应用包括各种步骤，首先监视电子邮件帐户的请求，然后发送这些请求以获取批准，检查请求是否获得批准，向邮件列表添加已批准的成员以及确认是否向列表中添加了新成员。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 创建空白逻辑应用。
> * 添加一个触发器，用于监视电子邮件中的订阅请求。
> * 添加一项操作，用于发送批准或拒绝这些请求的电子邮件。
> * 添加一项操作，用于检查审批响应。
> * 添加一项操作，用于向邮件列表添加批准的成员。
> * 添加一个条件，用于检查这些成员是否已成功加入列表。
> * 添加一项操作，其发送的电子邮件用于确认这些成员是否已成功加入列表。

完成后，逻辑应用看起来大致与以下工作流类似：

![完成的高级逻辑应用概述](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个 MailChimp 帐户，你之前在其中创建了名为“test-members-ML”的列表，供逻辑应用在其中为批准的成员添加电子邮件地址。 如果没有帐户，请[注册免费帐户](https://login.mailchimp.com/signup/)，然后了解[如何创建 MailChimp 列表](https://us17.admin.mailchimp.com/lists/#)。

* 逻辑应用支持的电子邮件提供商（例如 Office 365 Outlook、Outlook.com 或 Gmail）提供的电子邮件帐户。 至于其他提供商，请[查看此处的连接器列表](/connectors/)。 本快速入门通过工作或学校帐户使用 Office 365 Outlook。 如果你使用其他电子邮件帐户，则常规步骤保持不变，但 UI 可能稍有不同。

* Office 365 Outlook 或 Outlook.com 中的电子邮件帐户，支持审批工作流。 本教程使用 Office 365 Outlook。 如果使用其他电子邮件帐户，则常规步骤保持不变，但 UI 显示可能稍有不同。

* 如果逻辑应用需要通过将流量限制为特定 IP 地址的防火墙进行通信，则该防火墙需要允许访问逻辑应用所在的 Azure 区域中的逻辑应用服务或运行时所使用的[入站](logic-apps-limits-and-config.md#inbound)和[出站](logic-apps-limits-and-config.md#outbound) IP 地址。 如果逻辑应用还使用[托管连接器](../connectors/managed.md)（如 Office 365 Outlook 连接器或 SQL 连接器），或使用[自定义连接器](/connectors/custom-connectors/)，则防火墙还需要允许访问逻辑应用的 Azure 区域中的所有[托管连接器出站 IP 地址](logic-apps-limits-and-config.md#outbound)。

## <a name="create-your-logic-app"></a>创建逻辑应用

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。 在 Azure 主页上，选择“创建资源”。

1. 在“Azure 市场”菜单上，选择“集成” > “逻辑应用”。

   ![屏幕截图显示了“Azure 市场”菜单，其中选定了“集成”和“逻辑应用”。](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. 在“逻辑应用”窗格上，提供此处所述的有关要创建的逻辑应用的信息。

   ![屏幕截图显示了逻辑应用创建窗格和要为新逻辑应用提供的信息。](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | properties | 值 | 说明 |
   |----------|-------|-------------|
   | **订阅** | <*Azure-subscription-name*> | Azure 订阅名称。 本示例使用 `Pay-As-You-Go`。 |
   | **资源组** | LA-MailingList-RG | 用于组织相关资源的 [Azure 资源组](../azure-resource-manager/management/overview.md)的名称。 此示例创建一个名为 `LA-MailingList-RG` 的新资源组。 |
   | **Name** | LA-MailingList | 逻辑应用的名称，只能包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号（`(`、`)`）和句点 (`.`)。 本示例使用 `LA-MailingList`。 |
   | **位置** | 美国西部 | 用于存储逻辑应用信息的区域。 本示例使用 `West US`。 |
   | **Log Analytics** | 关闭 | 对于诊断日志记录，请保留“关闭”设置。 |
   ||||

1. 完成操作后，选择“查看 + 创建”。 在 Azure 验证你的逻辑应用的相关信息后，选择“创建”。

1. 在 Azure 部署应用后，选择“转到资源”。

   Azure 会打开逻辑应用模板选择窗格，其中显示了简介视频、常用触发器和逻辑应用模板模式。

1. 向下滚动，越过视频和常用触发器部分，找到“模板”部分，然后选择“空白逻辑应用”。

   ![屏幕截图显示了逻辑应用模板选择窗格，其中选择了“空白逻辑应用”。](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

接下来，添加一个 Outlook [触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)，侦听包含订阅请求的传入电子邮件。 每个逻辑应用都必须从触发器开始，在发生特定事件或新数据符合特定条件的情况下触发。 有关详细信息，请参阅[创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-trigger-to-monitor-emails"></a>添加用于监视电子邮件的触发器

1. 在逻辑应用设计器搜索框中输入 `when email arrives`，然后选择名为“新电子邮件到达时”的触发器。

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。
   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。

   本示例接下来选择 Office 365 Outlook。

   ![显示逻辑应用设计器搜索框的屏幕截图，其中包含搜索词“新电子邮件到达时”，“新电子邮件到达时”触发器显示为选中状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. 如果还没有连接，请在系统提示时登录并验证对你的电子邮件帐户的访问权限。

   Azure 逻辑应用创建到电子邮件帐户的连接。

1. 在触发器中，提供查看新电子邮件的条件。

   1. 指定用于查看电子邮件的文件夹，并将其他属性保留为默认值。

      ![显示设计器的屏幕截图，其中“新电子邮件到达时”操作和“文件夹”设置为“收件箱”。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. 添加触发器的“主题筛选器”属性，以便可以根据主题行筛选电子邮件。 打开“添加新参数”列表中，然后选择“主题筛选器” 。

      ![屏幕截图显示打开的“添加新参数”列表，其中“主题筛选器”处于选中状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      有关此触发器的属性的详细信息，请参阅 [Office 365 Outlook 连接器参考](/connectors/office365/)或 [Outlook.com 连接器参考](/connectors/outlook/)。

   1. 当属性显示在触发器中以后，请输入此文本：`subscribe-test-members-ML`

      ![屏幕截图显示“主题筛选器”属性，其中输入了文本“subscribe-test-members-ML”。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. 若要立即隐藏触发器的详细信息，请在形状的标题栏内单击以折叠形状。

   ![屏幕截图显示了折叠的触发器形状。](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。

逻辑应用现已生成，但除了检查传入电子邮件，不能执行任何操作。 因此，请添加一项在触发器触发时进行响应的操作。

## <a name="send-approval-email"></a>发送批准电子邮件

有了触发器以后，即可添加一项[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)，以便发送批准或拒绝请求的电子邮件。

1. 在逻辑应用设计器中的“新电子邮件到达时”触发器下，选择“新建步骤” 。

1. 在“选择操作”下的搜索框中，输入 `send approval`，然后选择名为“发送批准电子邮件”的操作 。

   ![屏幕截图显示了按“批准”操作筛选的“选择操作”列表，“发送批准电子邮件”操作处于选定状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. 现在输入此处所示和所述的指定属性的值。 将其他所有值保留默认值。 有关这些属性的详细信息，请参阅 [Office 365 Outlook 连接器参考](/connectors/office365/)或 [Outlook.com 连接器参考](/connectors/outlook/)。

   ![显示“发送批准电子邮件”属性的屏幕截图](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **To** | <approval-email-address> | 审批者的电子邮件地址。 可以使用自己的地址进行测试。 此示例使用虚构的 `sophiaowen@fabrikam.com` 电子邮件地址。 |
   | **主题** | `Approve member request for test-members-ML` | 一个描述性的电子邮件主题 |
   | **用户选项** | `Approve, Reject` | 请确保此属性指定了审批者可以选择的响应选项，默认情况下为“批准”或“拒绝” 。 |
   ||||

   > [!NOTE]
   > 单击某些编辑框时，将显示动态内容列表，现在可以忽略它。 此列表显示以前的操作的输出，可以选择这些输出作为工作流中后续操作的输入。
 
1. 保存逻辑应用。

接下来请添加条件，用于检查审批者选定的响应。

## <a name="check-approval-response"></a>检查审批响应

1. 在“发送审批电子邮件”操作下，选择“新建步骤” 。

1. 在“选择操作”下，选择“内置”。  在搜索框中输入 `condition`，然后选择名为“条件”的操作。

   ![显示“选择操作”搜索框的屏幕截图，其中“内置”处于选中状态并将“条件”作为搜索词，而“条件”操作显示为选中状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. 在“条件”标题栏中，选择“省略号”(...) 按钮，然后选择“重命名”   。 重命名条件并提供以下说明：`If request approved`

   ![屏幕截图显示省略号按钮处于选定状态，其中“设置”列表处于打开状态，“重命名”命令处于选定状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. 生成一个条件，用于检查审批者是否选择了“批准”。

   1. 在条件的左侧，单击“选择值”框。

   1. 从显示的动态内容列表中的“发送审批电子邮件”下选择 **SelectedOption** 属性。

      ![显示动态内容列表的屏幕截图，其中在“发送审批电子邮件”部分，“SelectedOption”输出显示为选中状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. 在中间的比较框中，选择“等于”运算符。

   1. 在条件右侧的“选择值”框中，输入文本 `Approve`。

      完成后，条件如以下示例所示：

      ![屏幕截图显示批准的请求示例的已完成条件](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. 保存逻辑应用。

接下来，请指定一项操作，供逻辑应用在审批者批准请求的情况下执行。 

## <a name="add-member-to-mailchimp-list"></a>将成员添加到 MailChimp 列表

现在请添加一项操作，用于将批准的成员添加到邮件列表。

1. 在条件的 **True** 分支中，选择“添加操作”。

1. 在“选择操作”搜索框中，选择“所有” 。 在搜索框中输入 `mailchimp`，然后选择名为“将成员添加到列表”的操作。

   ![显示具有“mailchimp”搜索词的“选择操作”框的屏幕截图，其中“将成员添加到列表”操作处于选中状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. 如果尚未连接到 MailChimp 帐户，系统会提示你进行登录。

1. 在“将成员添加到列表”操作中，提供此处显示和描述的信息：

   ![显示“将成员添加到列表”操作信息的屏幕截图。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **列表 ID** | 是 | <mailing-list-name> | 选择 MailChimp 邮件列表的名称。 本示例使用 `test-members-ML`。 |
   | **电子邮件地址** | 是 | <*new-member-email-address*> | 在打开的动态内容列表中，从“新电子邮件到达时”部分中选择“发件人”，这是触发器的输出，并指定新成员的电子邮件地址 。 |
   | **Status** | 是 | <member-subscription-status> | 选择要为新成员设置的订阅状态。 此示例选择 `subscribed`。 <p>有关详细信息，请参阅 [Manage subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/)（使用 MailChimp API 管理订户）。 |
   |||||

   有关“将成员添加到列表”操作属性的详细信息，请参阅 [MailChimp 连接器参考](/connectors/mailchimp/)。

1. 保存逻辑应用。

接下来，请添加一个条件，以便检查新成员是否已成功加入邮件列表。 这样一来，逻辑应用便可通知你该操作是成功还是失败。

## <a name="check-for-success-or-failure"></a>检查操作是成功还是失败

1. 在“True”分支的“将成员添加到列表”操作下，选择“添加操作”  。

1. 在“选择操作”下，选择“内置”。  在搜索框中输入 `condition`，然后选择名为“条件”的操作。

1. 重命名条件并提供以下说明：`If add member succeeded`

1. 生成一个条件，用于检查批准的成员在加入邮件列表时是成功还是失败：

   1. 在条件的左侧，单击“选择值”框。 在显示的动态内容列表中，在“将成员添加到列表”部分中选择“状态”属性 。

      例如，你的条件如以下示例所示：

      ![显示条件左侧的“选择值”框的屏幕截图，其中输入了“状态”信息。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. 在中间的比较框中，选择“等于”运算符。

   1. 在条件右侧的“选择值”框中输入以下文本：`subscribed`

      完成后，条件如以下示例所示：

      ![屏幕截图显示用于检查成功或失败订阅的已完成条件。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

接下来，请设置电子邮件，以便在批准的成员成功加入或无法加入邮件列表时发送。

## <a name="send-email-if-member-added"></a>在添加了成员的情况下发送电子邮件

1. 在“如果添加成员成功”条件的“True”分支中，选择“添加操作”  。

   ![显示“如果添加成员成功”条件的“True”分支的屏幕截图，其中“添加操作”处于选中状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. 在“选择操作”搜索框中，输入 `outlook send email`，然后选择名为“发送电子邮件”的操作 。

   ![显示“选择操作”搜索框的屏幕截图，其中输入了“outlook 发送电子邮件”并为“通知”选择了“发送电子邮件”操作。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. 重命名操作并提供以下说明：`Send email on success`

1. 在“成功发送电子邮件”操作中，提供此处显示和描述的信息：

   ![屏幕截图显示“成功发送电子邮件”操作和为成功电子邮件提供的信息。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **正文** | 是 | <success-email-body> | 成功电子邮件的正文内容。 学习本教程时，请执行以下步骤： <p>1.输入带尾随空格的以下文本：`New member has joined "test-members-ML":` <p>2.在显示的动态内容列表中，选择“电子邮件地址”属性。 <p>**注意**：如果未显示此属性，请在“将成员添加到列表”部分的标头旁边选择“查看详细信息” 。 <p>3.在下一行中，输入带尾随空格的以下文本：`Member opt-in status: ` <p>4.在动态内容列表的“将成员添加到列表”下，选择“状态”属性。  |
   | **主题** | 是 | <success-email-subject> | 成功电子邮件的主题。 学习本教程时，请执行以下步骤： <p>1.输入带尾随空格的以下文本：`Success! Member added to "test-members-ML": ` <p>2.在动态内容列表的“将成员添加到列表”下，选择“电子邮件地址”属性。  |
   | **To** | 是 | <*your-email-address*> | 一个电子邮件地址，可向其发送成功电子邮件。 为进行测试，可以使用自己的电子邮件地址。 |
   |||||

1. 保存逻辑应用。

## <a name="send-email-if-member-not-added"></a>在添加不了成员的情况下发送电子邮件

1. 在“如果成功添加成员”条件的“False”分支中，选择“添加操作”  。

   ![显示“如果成功添加成员”条件的“False”分支的屏幕截图，其中“添加操作”处于选中状态。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. 在“选择操作”搜索框中，输入 `outlook send email`，然后选择名为“发送电子邮件”的操作 。

   ![显示“选择操作”搜索框的屏幕截图，其中输入了“outlook 发送电子邮件”并选择了“发送电子邮件”操作。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. 重命名操作并提供以下说明：`Send email on failure`

1. 提供有关此操作的信息，如下所示：

   ![屏幕截图显示“发送电子邮件失败”操作和为失败电子邮件提供的信息。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **正文** | 是 | <*body-for-failure-email*> | 失败电子邮件的正文内容。 对于本教程，请输入以下文本： <p>`Member might already exist. Check your MailChimp account.` |
   | **主题** | 是 | <*subject-for-failure-email*> | 失败电子邮件的主题。 学习本教程时，请执行以下步骤： <p>1.输入带尾随空格的以下文本：`Failed, member not added to "test-members-ML": ` <p>2.在动态内容列表的“将成员添加到列表”下，选择“电子邮件地址”属性。  |
   | **To** | 是 | <*your-email-address*> | 一个电子邮件地址，可向其发送失败电子邮件。 为进行测试，可以使用自己的电子邮件地址。 |
   |||||

1. 保存逻辑应用。 

接下来测试逻辑应用，该应用现在看起来类似于以下示例：

![屏幕截图显示该示例已完成逻辑应用工作流。](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>运行逻辑应用

1. 向自己发送一封加入邮件列表的电子邮件请求。 等待请求显示在收件箱中。

1. 若要手动启动逻辑应用，请在设计器工具栏中选择“运行”。 

   如果电子邮件的主题与触发器的主题筛选器匹配，逻辑应用会向你发送审批订阅请求的电子邮件。

1. 在收到的审批电子邮件中，选择“批准”。

1. 如果订户的电子邮件地址在邮件列表中不存在，逻辑应用会添加该人的电子邮件地址并向你发送一封电子邮件，如以下示例所示：

   ![显示有关成功订阅的示例电子邮件的屏幕截图。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   如果逻辑应用无法添加订户，你会收到一封电子邮件，如下示例所示：

   ![显示有关失败订阅的示例电子邮件的屏幕截图。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > 如果没有收到任何电子邮件，请检查电子邮件的垃圾邮件文件夹。 垃圾电子邮件筛选器可能会将这些类型的邮件重定向。 否则，如果不确定逻辑应用是否正常运行，请参阅[逻辑应用故障排除](../logic-apps/logic-apps-diagnosing-failures.md)。

祝贺！你现在已创建并运行可以跨 Azure、Microsoft 服务和其他 SaaS 应用集成信息的逻辑应用。

## <a name="clean-up-resources"></a>清理资源

在禁用或删除你的逻辑应用之前，该逻辑应用会一直运行。 不再需要示例逻辑应用时，请删除包含该逻辑应用和相关资源的资源组。

1. 在 Azure 门户的搜索框中，输入你创建的资源组的名称。 从结果中的“资源组”下，选择该资源组。

   此示例创建一个名为 `LA-MailingList-RG` 的资源组。

   ![显示 Azure 搜索框的屏幕截图，其中输入了“la-mailinglist-rg”并选择了“LA-MailingList-RG”。](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > 如果 Azure 主页在“最近的资源”下显示了该资源组，则可以从主页中选择该组。

1. 在资源组菜单上，检查是否已选中“概览”。 在“概览”窗格的工具栏上，选择“删除资源组”。

   ![屏幕截图显示了资源组的“概览”窗格，并在窗格的工具栏上选择了“删除资源组”。](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. 出现确认窗格时，输入资源组名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建逻辑应用，以便处理邮件列表请求的审批。 现在，请了解如何集成 Azure 存储和 Azure Functions 之类的 Azure 服务，以便生成处理和存储电子邮件附件的逻辑应用。

> [!div class="nextstepaction"]
> [处理电子邮件附件](../logic-apps/tutorial-process-email-attachments-workflow.md)
