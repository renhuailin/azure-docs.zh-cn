---
title: 与 Office 365 Outlook 集成
description: 使用 Azure 逻辑应用自动执行用于管理 Office 365 Outlook 中的电子邮件、联系人和日历的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 9caf69a7f78c7872f0a5f8a2ed07bdc749a29023
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682989"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Office 365 Outlook 中的电子邮件、联系人和日历

使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 [Office 365 Outlook 连接器](/connectors/office365connector/)，可以通过构建逻辑应用来创建用于管理工作或学校帐户的自动化任务和工作流。 例如，可以自动执行以下任务：

* 获取、发送和回复电子邮件。
* 在日历上安排会议。
* 添加和编辑联系人。

可以使用任何触发器来启动工作流，例如，当新电子邮件到达时、当日历项更新时或其他服务（例如 Salesforce）中发生某个事件时。 可以使用对触发器事件做出响应的操作，例如，发送电子邮件或创建新的日历事件。

## <a name="prerequisites"></a>先决条件

* 使用 [工作或学校帐户](https://www.office.com/)登录的 Outlook 帐户。 如果有 @outlook.com 或 @hotmail.com 帐户，请改为使用 [Outlook.com 连接器](../connectors/connectors-create-api-outlook.md) 。 若要使用不同的用户帐户（例如服务帐户）连接到 Outlook，请参阅 [使用其他帐户进行连接](#connect-using-other-accounts)。

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 要在其中访问 Outlook 帐户的逻辑应用。 若要通过 Office 365 Outlook 触发器启动工作流，需要有一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要向工作流中添加 Office 365 Outlook 操作，逻辑应用需要已有一个触发器。

## <a name="add-a-trigger"></a>添加触发器

[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)是一个事件，用于启动逻辑应用中的工作流。 此示例逻辑应用使用一个“轮询”触发器，该触发器根据指定的时间间隔和频率检查电子邮件帐户中是否有任何更新的日历事件。

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的空白逻辑应用。

1. 在搜索框中，输入 `office 365 outlook` 作为筛选器。 此示例选择“即将启动将要发生的事件时”  。
   
   ![选择用于启动逻辑应用的触发器](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. 如果你没有连接到 Outlook 帐户的活动连接，系统将提示你登录并创建该连接。 若要使用不同的用户帐户（例如服务帐户）连接到 Outlook，请参阅 [使用其他帐户进行连接](#connect-using-other-accounts)。 否则，请提供触发器属性的信息。

   > [!NOTE]
   > 即使更改了登录凭据，连接也不会在吊销之前过期。 有关详细信息，请参阅 [Azure Active Directory 中的可配置令牌生存期](../active-directory/develop/active-directory-configurable-token-lifetimes.md)。

   此示例选择供触发器检查的日历，例如：

   ![配置触发器的属性](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 在触发器中，设置“频率”  和“间隔”  值。 若要添加其他可用的触发器属性（例如“时区”  ），请从“添加新参数”  列表中选择那些属性。

   例如，如果希望触发器每 15 分钟检查一次日历，请将“频率”  设置为“分钟”  ，将“间隔”  设置为 `15`。 

   ![为触发器设置频率和间隔](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 在设计器工具栏上选择“保存”。 

现在，添加一个在触发器触发后运行的操作。 例如，可以添加 Twilio“发送消息”  操作，当日历事件将在 15 分钟内启动时，该操作会发送一个文本。

## <a name="add-an-action"></a>添加操作

[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指由逻辑应用中的工作流运行的操作。 此示例逻辑应用在 Office 365 Outlook 中新建一个联系人。 可以使用来自其他触发器或操作的输出创建联系人。 例如，假设你的逻辑应用使用 Dynamics 365 触发器“创建记录时”。  你可以添加 Office 365 Outlook 的“创建联系人”  操作，并使用来自 SalesForce 触发器的输出来新建联系人。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 若要将某个操作添加为工作流的最后一步，请选择“新建步骤”  。 

   若要在步骤之间添加操作，请将鼠标指针移到这些步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。 

1. 在搜索框中，输入 `office 365 outlook` 作为筛选器。 此示例选择“创建联系人”  。

   ![选择要在逻辑应用中运行的操作](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. 如果你没有连接到 Outlook 帐户的活动连接，系统将提示你登录并创建该连接。 若要使用不同的用户帐户（例如服务帐户）连接到 Outlook，请参阅 [使用其他帐户进行连接](#connect-using-other-accounts)。 否则，请提供操作属性的信息。

   > [!NOTE]
   > 即使更改了登录凭据，连接也不会在吊销之前过期。 有关详细信息，请参阅 [Azure Active Directory 中的可配置令牌生存期](../active-directory/develop/active-directory-configurable-token-lifetimes.md)。

   此示例选择可供操作在其中创建新联系人的联系人文件夹，例如：

   ![配置操作的属性](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   若要添加其他可用的操作属性，请从“添加新参数”列表中选择那些属性。 

1. 在设计器工具栏上选择“保存”。 

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>使用其他帐户进行连接

如果尝试使用与当前登录到 Azure 的帐户不同的帐户连接到 Outlook，则可能会收到 [单一登录 (SSO) ](../active-directory/manage-apps/what-is-single-sign-on.md) 错误。 当使用一个帐户登录到 Azure 门户，但使用不同的帐户来创建连接时，会发生此问题。 逻辑应用设计器需要使用登录到 Azure 的帐户。 若要解决此问题，可以使用以下选项：

* 将另一个帐户设置为逻辑应用的资源组的 **参与者** 。

  1. 在逻辑应用的资源组菜单上，选择 " **访问控制 (IAM)**"。 设置具有 **参与者** 角色的其他帐户。 有关详细信息，请参阅[使用 Azure 门户添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-portal.md)。

  1. 如果使用工作或学校帐户登录到 Azure 门户，请注销并使用其他帐户重新登录。 你现在可以使用其他帐户创建到 Outlook 的连接。

* 设置其他帐户，使工作或学校帐户具有 "发送为" 权限。

   如果你具有管理员权限，请在服务帐户的邮箱上设置你的工作或学校帐户，其中包含 " **发送为** " 或 " **代表发送"** 权限。 有关详细信息，请参阅向 [其他用户授予邮箱权限-管理帮助](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user)。 然后，可以使用工作或学校帐户创建连接。 现在，可以在可指定发送方的触发器或操作中使用服务帐户的电子邮件地址。

   例如，" **发送电子邮件** " 操作有一个可选参数， **(Send as)**，你可以将其添加到操作中，并使用你的服务帐户的电子邮件地址作为发件人。 若要添加此参数，请执行以下步骤：

   1. 在 " **发送电子邮件** " 操作中，打开 " **添加参数** " 列表，然后选择 " **(作为) 参数发送** "。

   1. 在操作中显示参数后，输入服务帐户的电子邮件地址。

## <a name="connector-reference"></a>连接器参考

有关此连接器的技术详细信息，例如触发器、操作和限制（如此连接器的 Swagger 文件所述），请参阅[连接器的参考页](/connectors/office365/)。 

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)