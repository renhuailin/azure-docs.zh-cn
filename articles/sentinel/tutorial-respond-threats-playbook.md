---
title: 教程：在 Azure Sentinel 中结合自动化规则使用 playbook
description: 使用本教程可帮助你在 Azure Sentinel 中结合自动化规则使用 playbook，以自动执行事件响应并修正安全威胁。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: a9411d044a8c176387d8b9900ca56a7512c4a0de
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458319"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>教程：在 Azure Sentinel 中结合自动化规则使用 playbook

本教程演示如何结合使用 playbook 和自动化规则，自动执行事件响应并修正 Azure Sentinel 检测到的安全威胁。 完成本教程之后，能够：

> [!div class="checklist"]
>
> * 创建自动化规则
> * 创建 playbook
> * 向 playbook 添加操作
> * 将 playbook 附加到自动化规则或分析规则以自动执行威胁响应

## <a name="what-are-automation-rules-and-playbooks"></a>什么是自动化规则和 playbook？

自动化规则可帮助你在 Azure Sentinel 中会审事件。 你可以使用它们自动将事件分配给适当的人员、关闭有干扰的事件或已知[误报](false-positives.md)、更改其严重性并添加标记。 它们也是可用于为响应事件而运行 playbook 的机制。

playbook 是在 Azure Sentinel 中运行以响应警报或事件的一个流程集合。 playbook可帮助自动处理和编排响应，并可设置为在特定警报或事件生成时通过分别附加到分析规则或自动化规则来自动运行。 还可以按需手动运行。

Azure Sentinel 中的 playbook 基于 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)内置的工作流，这意味着你可获得逻辑应用的所有功能、自定义能力和内置模板。 每个 playbook 都是针对其所属的特定订阅创建的，但 **Playbook** 显示了所有选定订阅可以提供的所有 playbook。

> [!NOTE]
> 由于 playbook 使用 Azure 逻辑应用，因此可能要额外收费。 有关更多详细信息，请访问 [Azure 逻辑应用](https://azure.microsoft.com/pricing/details/logic-apps/)定价页。

例如，如果想要阻止可能被入侵的用户进入你的网络并偷窃信息，可以针对检测被入侵用户的规则所生成的事件创建自动、多面的响应。 首先，创建一个执行以下操作的 playbook：

1. 如果 playbook 由自动化规则向其传递事件而受到调用，playbook 将在 [ServiceNow](/connectors/service-now/) 或任何其他 IT 票证系统中打开一个票证。

1. 它向 [Microsoft Teams](/connectors/teams/) 或 [Slack](/connectors/slack/) 中的安全操作频道发送一条消息，确保安全分析师注意到此事件。

1. 它还将事件中的所有信息通过电子邮件发送给高级网络管理员和安全管理员。该电子邮件中会包含“阻止”和“忽略”用户选项按钮 。

1. playbook 将等待，直到从管理员收到响应，然后继续执行后续步骤。

1. 如果管理员选择“阻止”，它会将命令发送到 Azure AD 以禁用该用户，并将一个命令发送到防火墙以阻止 IP 地址。

1. 如果管理员选择“忽略”，playbook 会关闭 Azure Sentinel 中的事件，并关闭 ServiceNow 中的票证。

为了触发 playbook，你将创建一个自动化规则，在这些事件生成时运行。 该规则将执行以下步骤：

1. 规则将事件状态更改为“活动”。

1. 它将事件分配给负责管理此类事件的分析人员。

1. 它添加“被入侵用户”标记。

1. 最后，它调用刚创建的 playbook。 （[此步骤需要特殊权限](automate-responses-with-playbooks.md#incident-creation-automated-response)。）

可以通过创建调用 playbook 的自动化规则来响应事件自动运行 playbook，如上例所示。 还可以通过指示分析规则在警报生成时自动运行一个或多个 playbook 来响应警报自动运行。 

也可以选择按需手动运行 playbook，作为对所选警报的响应。

获取更完整和更详细的介绍，了解在 Azure Sentinel 中使用[自动化规则](automate-incident-handling-with-automation-rules.md)和 [playbook](automate-responses-with-playbooks.md) 自动执行威胁响应。

> [!IMPORTANT]
>
> - **自动化规则** 和 playbook 的 **事件触发器** 的使用目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="create-a-playbook"></a>创建 playbook

按照以下步骤在 Azure Sentinel 中创建新的 playbook：

### <a name="prepare-the-playbook-and-logic-app"></a>准备 playbook 和逻辑应用

1. 从 **Azure Sentinel** 导航菜单中，选择“自动化”。

1. 在顶部菜单中，选择“创建”和“添加新的 playbook”。 

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="添加新的 playbook":::

    新的浏览器选项卡会打开并转到“创建逻辑应用”向导。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="创建逻辑应用":::

1. 输入 **订阅** 和 **资源组**，并在 **逻辑应用名称** 下给 playbook 指定一个名称。

1. 对于 **区域**，选择要存储逻辑应用信息的 Azure 区域。

1. 如果想以诊断为目的监视此 playbook 的活动，标记 **启用 log analytics** 选框，然后输入 **Log Analytics 工作区** 名称。

1. 如果想将标记应用到 playbook，单击 **下一步：标记 >** （未连接到自动化规则应用的标记。 [了解有关标记的详细信息](../azure-resource-manager/management/tag-resources.md)）。 否则，单击“审阅 + 创建”。 确认所提供的详细信息，并单击“创建”。

1. 创建和部署 playbook 时（这需要几分钟时间），将转到一个名为 **Microsoft.EmptyWorkflow** 的屏幕。 出现“部署已完成”消息时，单击 **转到资源**。

1. 将转到新 playbook 的[逻辑应用设计器](../logic-apps/logic-apps-overview.md)，可在此处开始设计工作流。 屏幕上会显示一个简介视频和一些常用的逻辑应用触发器和模板。 [了解有关使用逻辑应用创建 playbook 的详细信息。](../logic-apps/logic-apps-create-logic-apps-from-templates.md)

1. 选择“空白逻辑应用”模板。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="逻辑应用设计器模板库":::

### <a name="choose-the-trigger"></a>选择触发器

每个 playbook 必须以触发器开头。 触发器定义启动 playbook 的操作以及 playbook 将接收的架构。

1. 在搜索栏中，查找 Azure Sentinel。 选择出现在结果中的 **Azure Sentinel**。

1. 在生成的 **触发器** 选项卡中，会看到 Azure Sentinel 提供的两个触发器：
    - 触发对 Azure Sentinel 警报的响应时
    - 触发 Azure Sentinel 事件创建规则时

   选择与要创建的 playbook 类型匹配的触发器。

    > [!NOTE]
    > 请记住，自动化规则只能调用基于事件触发器的 playbook。 基于警报触发器的 playbook 必须定义为直接在[分析规则](tutorial-detect-threats-custom.md#set-automated-responses-and-create-the-rule)中运行，也可以手动运行。
    > 
    > 有关要使用哪个触发器，请参阅[在 Azure Sentinel playbook 中使用触发器和操作](playbook-triggers-actions.md)

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="为 playbook 选择触发器":::

> [!NOTE]
> 选择触发器或任何后续操作时，系统将要求你对要交互的任何资源提供程序进行身份验证。 在本例中，提供程序为 Azure Sentinel。 可以采用几种不同方法进行身份验证。 有关详细信息和说明，请参阅[向 Azure Sentinel 验证 playbook](authenticate-playbooks-to-sentinel.md)。

### <a name="add-actions"></a>添加操作

现在可以定义调用 playbook 时的响应。 通过选择 **新建步骤**，可以添加操作、逻辑条件、循环或 switch case 条件。 此选择会在设计器中打开新框架，可在此处选择要交互的系统或应用程序或要设置的条件。 在框架顶部的搜索栏中输入系统或应用程序的名称，然后从可用结果中选择。

在上述每个步骤中，单击任意字段都将显示一个有两个菜单的面板：**动态内容** 和 **表达式**。 从 **动态内容** 菜单中，可以对传递给 playbook 的警报或事件的属性添加引用，包括涉及的所有实体的值和属性。 从 **表达式** 菜单中，可以在大型函数库中选择，将其他逻辑添加到步骤。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="逻辑应用设计器":::

此屏幕截图显示了在创建本文档开头的示例中描述的 playbook 时要添加的操作和条件。 唯一的区别是，在此处显示的 playbook 中，使用的是 **警报触发器**，而不是 **事件触发器**。 这意味着，将从分析规则（而不是从自动化规则）直接调用此 playbook。 下面将介绍两种调用 playbook 的方式。

## <a name="automate-threat-responses"></a>自动响应威胁

创建了 playbook 并定义了触发器，设置了条件，规定了要执行的操作以及将生成的输出。 现在，需要确定其运行的条件，并设置在满足这些条件时使其运行的自动化机制。

### <a name="respond-to-incidents"></a>响应事件

通过创建一个在事件生成时会运行并进而调用 playbook 的 [自动化规则](automate-incident-handling-with-automation-rules.md)，使用 playbook 响应 **事件**。

创建自动化规则：

1. 从 Azure Sentinel 导航菜单的 **自动化** 边栏选项卡中，从顶部菜单中选择 **创建**，然后选择 **添加新规则**。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="添加新规则":::

1. **创建新自动化规则** 面板开启。 输入规则的名称。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="创建自动化规则":::

1. 如果希望自动化规则仅对某些分析规则生效，可通过修改 **If 分析规则名称** 条件来指定。

1. 添加想要此自动化规则的激活依赖的任何其他条件。 单击 **添加条件**，然后从下拉列表中选择条件。 条件列表由警报详细信息和实体标识符字段填充。

1. 选择希望此自动化规则执行的操作。 可用操作包括 **分配所有者**、**更改状态**、**更改严重性**、**添加标记** 和 **运行 playbook**。 可添加任意数量的操作。

1. 如果添加 **运行 playbook** 操作，系统将提示从可用 playbook 下拉列表中选择。 只有以事件触发器开头的 playbook 才能由自动化规则运行，因此只有这类 playbook 会出现在列表中。<a name="permissions-to-run-playbooks"></a>

    > [!IMPORTANT]
    > 若要由自动化规则运行 playbook，必须向 Azure Sentinel 授予显式权限。 如果 playbook 在下拉列表中为"灰显"状态，则表示 Sentinel 无权访问该 playbook 的资源组。 单击 **管理 playbook 权限** 链接以分配权限。
    > 在打开的 **管理权限** 面板中，标记包含要运行的 playbook 的资源组的复选框，然后单击 **应用**。
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="管理权限":::
    > - 你本身必须对要授予 Azure Sentinel 权限的任何资源组拥有 **所有者** 权限，并且必须对包含要运行的 playbook 的任何资源组具有 **逻辑应用参与者** 角色。
    > - 在多租户部署中，如果要运行的 playbook 在不同的租户中，必须授予 Azure Sentinel 权限，才能在 playbook 的租户中运行 playbook。
    >    1. 从 playbook 的租户中的 Azure Sentinel 导航菜单中，选择 **设置**。
    >    1. 在 **设置** 边栏选项卡中，选择 **设置** 选项卡，然后选择 **playbook 权限** 扩展器。
    >    1. 单击 **配置权限** 按钮以打开上面提到的 **管理权限** 面板，并按上所述继续操作。
    > - 如果在 MSSP 方案中，你想要通过某个自动化规则[在客户租户中运行 playbook](automate-incident-handling-with-automation-rules.md#permissions-in-a-multi-tenant-architecture)，而该规则是在登录到服务提供商租户后创建的，则必须授予 Azure Sentinel 权限才能在这两个租户中运行 playbook。在客户租户中，请按照前面的要点中适用于多租户部署的说明进行操作 。 在服务提供商租户中，必须在 Azure Lighthouse 加入模板中添加 Azure Security Insights 应用：
    >    1. 在 Azure 门户中，转到“Azure Active Directory”。
    >    1. 单击“企业应用程序”。
    >    1. 选择“应用程序类型”，并根据“Microsoft 应用程序”进行筛选 。
    >    1. 在搜索框中，键入 Azure Security Insights。
    >    1. 复制“对象 ID”字段。 需要将此附加授权添加到现有的 Azure Lighthouse 委派。
    >
    >    “Azure Sentinel 自动化参与者”角色具有固定的 GUID，即 `f4c81013-99ee-4d62-a7ee-b3f1f648599a`。 参数模板中的示例 Azure Lighthouse 授权如下所示：
    >    
    >    ```json
    >    {
    >        "principalId": "<Enter the Azure Security Insights app Object ID>", 
    >        "roleDefinitionId": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
    >        "principalIdDisplayName": "Azure Sentinel Automation Contributors" 
    >    }
    >    ```

1. 如果想自动化规则有到期日期，可以设置一个。

1. 在 **顺序** 下输入一个数字，以确定此规则在自动化规则序列中运行的位置。

1. 单击“应用”。 大功告成！

[发现其他方法](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules)来创建自动化规则。

### <a name="respond-to-alerts"></a>响应警报

使用 playbook 通过以下方法来响应 **警报**：创建 **分析规则** 或编辑现有分析规则，警报生成时运行，并在 [分析规则向导](tutorial-detect-threats-custom.md)中选择 playbook 作为自动响应。

1. 从 Azure Sentinel 导航菜单中的 **分析** 边栏选项卡中，选择要自动响应的分析规则，并在细节窗格中单击 **编辑**。

1. 在 **分析规则向导-编辑现有规则** 页中，选择 **自动响应** 选项卡。

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="自动响应选项卡":::

1. 从下拉列表中选择 playbook。 可以选择多个 playbook，但只有使用 **警报触发器** 的 playbook 可用。

1. 在“查看和创建”选项卡中，选择“保存”。 

## <a name="run-a-playbook-on-demand"></a>按需运行 playbook

也可以按需运行 playbook。

 > [!NOTE]
 > 只有使用 **警报触发器** 的 playbook 可以按需运行。

要按需运行 playbook：

1. 在“事件”页中，选择一个事件并单击“查看完整详细信息” 。

2. 在“警报”选项卡中，单击要对其运行 playbook 的警报，然后一直滚动到右侧，单击“查看 playbook”，再选择要从订阅上可用 playbook 列表运行的 playbook  。

## <a name="next-steps"></a>后续步骤

在本教程中，你学习了如何在 Azure Sentinel 中使用 playbook 和自动化规则来应对威胁。 
- 了解如何使用 Azure Sentinel 来[主动搜寻威胁](hunting.md)。
