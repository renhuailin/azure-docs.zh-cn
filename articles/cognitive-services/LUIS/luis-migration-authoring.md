---
title: 迁移到 Azure 资源创作密钥
titleSuffix: Azure Cognitive Services
description: 本文介绍了如何将语言理解 (LUIS) 创作身份验证从电子邮件帐户迁移到 Azure 资源。
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787631"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>迁移到 Azure 资源创作密钥

> [!IMPORTANT]
>  从 12 月 3 日开始，现有的 LUIS 用户必须完成迁移过程才能继续创作 LUIS 应用程序。

语言理解 (LUIS) 创作身份验证已从电子邮件帐户更改为 Azure 资源。 如果你尚未进行迁移，请使用本文来了解如何迁移你的帐户。  


## <a name="what-is-migration"></a>什么是迁移？

迁移是指将创作身份验证从电子邮件帐户更改为 Azure 资源的过程。 迁移后，你的帐户会关联到 Azure 订阅和 Azure 创作资源。

必须从 [LUIS 门户](https://www.luis.ai)执行迁移。 例如，如果你使用 LUIS CLI 创建创作密钥，则需在 LUIS 门户中完成迁移过程。 迁移后，你的应用程序的共同创作者仍然能够存在，但会被添加到 Azure 资源级别，而不是应用程序级别。 帐户的迁移无法撤消。

> [!Note]
> * 如果你需要创建一个预测运行时资源，则可通过[单独的过程](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)来创建它。
> * 有关应用程序和参与者如何受影响的信息，请参阅下面的[迁移说明](#migration-notes)部分。 
> * LUIS 应用的创作是免费的，如 F0 层所指示的那样。 了解[有关定价层的更多信息](luis-limits.md#key-limits)。

## <a name="migration-prerequisites"></a>迁移先决条件

* 有效的 Azure 订阅。 请要求你的租户管理员将你添加到订阅，或者[注册一个免费订阅](https://azure.microsoft.com/free/cognitive-services)。
* 来自 LUIS 门户或 [Azure 门户](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)的 LUIS Azure 创作资源。 
    * 从 LUIS 门户创建创作资源是下一部分所述的迁移过程的一部分。
* 如果你是应用程序的协作者，则应用程序不会自动迁移。 在完成迁移流时，系统会提示你导出这些应用。 你还可以使用[导出 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)。 迁移后，你可以将应用导入回 LUIS。 导入过程会创建一个具有新应用 ID 的新应用，你是该应用的所有者。        
* 如果你是应用程序的所有者，则无需导出应用，因为它们会自动迁移。 提供了一个电子邮件模板，其中包含每个应用程序的所有协作者的列表，因此，这些协作者可以收到迁移过程通知。

## <a name="migration-steps"></a>迁移步骤

1. 登录到 [LUIS 门户](https://www.luis.ai)时，Azure 迁移窗口会打开，其中包含迁移步骤。 如果将其关闭，则无法继续创作 LUIS 应用程序，所显示的唯一操作将是继续进行迁移。

    > [!div class="mx-imgBorder"]
    > ![迁移时限简介](./media/migrate-authoring-key/notify-azure-migration.png)

2. 如果你的任何应用有协作者，你会看到所拥有的应用程序名称的列表，以及每个应用程序上的创作区域和协作者电子邮件。 建议向协作者发送一封电子邮件，通知其有关迁移的信息，方法是单击应用程序名称左侧的“发送”符号按钮。
如果协作者具有分配给应用程序的预测资源，则应用程序名称旁边会显示 `*` 符号。 迁移后，这些应用仍会向其分配这些预测资源，即使协作者没有创作应用程序所需的访问权限。 但是，如果预测资源的所有者从 Azure 门户[重新生成了密钥](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)，则此分配会中断。  

   > [!div class="mx-imgBorder"]
   > ![通知协作者](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   对于每个协作者和应用，会打开默认的电子邮件应用程序，显示一封格式简单的电子邮件。 可以在发送电子邮件之前对其进行编辑。 电子邮件模板包含确切的应用 ID 和应用名称。

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > 将帐户迁移到 Azure 后，你的应用将不再可供协作者使用。

3. 如果你是任何应用的协作者，则会显示与你共享的应用程序名称的列表以及每个应用程序的创作区域和所有者电子邮件。 建议通过单击应用程序名称左侧的导出按钮来导出应用的副本。 你可以在迁移后将这些应用重新导入回来，因为它们不会随你自动迁移。
如果你具有分配给应用程序的预测资源，则应用程序名称旁边会显示 `*` 符号。 迁移后，你的预测资源仍会分配给这些应用程序，即使你不再有权创作这些应用。 如果希望中断预测资源与应用程序之间的分配，则需转到 Azure 门户并[重新生成密钥](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)。

   > [!div class="mx-imgBorder"]
   > ![导出应用程序。](./media/migrate-authoring-key/migration-export-apps.png)


4. 在迁移区域的窗口中，系统会要求你将应用程序迁移到创作应用程序时所在的区域中的 Azure 资源。 LUIS 有三个创作区域[和门户](./luis-reference-regions.md#luis-authoring-regions)。 此窗口会显示在其中创作了你拥有的应用程序的区域。 根据你使用的区域门户和你创作的应用，显示的迁移区域可能会有所不同。 

   > [!div class="mx-imgBorder"]
   > ![多区域迁移。](./media/migrate-authoring-key/migration-regional-flow.png)

5. 对于每个区域，请使用按钮来选择是创建新的 LUIS 创作资源，还是迁移到现有资源。

   > [!div class="mx-imgBorder"]
   > ![选择创建新的创作资源还是使用现有的创作资源](./media/migrate-authoring-key/migration-multiregional-resource.png)

   提供以下信息：

   * 租户名称：与你的 Azure 订阅关联的租户。 默认情况下，这设置为你当前正在使用的租户。 可通过以下方式切换租户：关闭此窗口，并选择屏幕右上方包含你的姓名首字母的头像。 单击“迁移到 Azure”以重新打开该窗口。
   * Azure 订阅名称：将要与资源关联的订阅。 如果有多个订阅属于你的租户，请从下拉列表中选择所需的订阅。
   * 创作资源名称：你选择的自定义名称。 它用作创作和预测终结点查询的 URL 的一部分。 如果要创建新的创作资源，请注意，资源名称只能包含字母数字字符、`-`，并且不能以 `-` 开头或结尾。 如果名称中包含任何其他符号，则资源创建和迁移操作会失败。
   * Azure 资源组名称：你从下拉列表中选择的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。 如果订阅中目前没有资源组，则不允许在 LUIS 门户中创建资源组。 请转到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup)来创建一个资源组，然后转到 LUIS 以继续完成登录过程。

6. 在所有区域中成功迁移后，单击“完成”。 现在，你将能够访问你的应用程序。 你可以继续在门户中创作和维护所有区域中的所有应用程序。

## <a name="migration-notes"></a>迁移说明

* 迁移前，共同创作者在 LUIS 应用级别称为“协作者”。 迁移后，将在 Azure 资源级别使用 Azure 参与者角色来实现相同的功能。
* 如果你已登录到多个 [LUIS 区域门户](./luis-reference-regions.md#luis-authoring-regions)，则系统会要求你一次在多个区域中进行迁移。
* 如果你是应用程序的所有者，则应用程序会自动随你一起迁移。 如果你是应用程序的协作者，则应用程序不会随你一起迁移。 但是，系统会提示协作者导出所需的应用。
* 应用程序所有者无法选择要迁移的应用子集。所有者无法了解协作者是否已迁移。
* 迁移不会自动将协作者移动或添加到 Azure 创作资源。 应用所有者需要在迁移后完成此步骤。 此步骤需要[对 Azure 创作资源的权限](./luis-how-to-collaborate.md)。
* 将参与者分配给 Azure 资源后，需要先迁移参与者，然后参与者才能访问应用程序。 否则，参与者将没有创作应用程序所需的访问权限。


## <a name="using-apps-after-migration"></a>在迁移后使用应用

在迁移过程完成后，你拥有的所有 LUIS 应用现在会被分配到单个 LUIS 创作资源。
“我的应用”列表显示了已迁移到新的创作资源的应用。 在访问你的应用之前，请选择“选择其他创作资源”来选择订阅和创作资源，以查看可创作的应用。

> [!div class="mx-imgBorder"]
> ![选择订阅和创作资源](./media/migrate-authoring-key/select-sub-and-resource.png)


如果你计划以编程方式编辑应用，则你需要使用创作密钥值。 在 LUIS 门户中，单击屏幕顶部的“管理”，然后选择“Azure 资源”，这样就可以显示这些值。 在 Azure 门户中，资源的“密钥和终结点”页上也提供了它们。 你还可以从同一页面创建更多的创作资源并分配它们。

## <a name="adding-contributors-to-authoring-resources"></a>向创作资源添加参与者

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

了解在创作资源上[如何添加参与者](luis-how-to-collaborate.md)。 参与者将有权访问该资源下的所有应用程序。

可以通过 Azure 门户在该资源的“访问控制(IAM)”页上向创作资源添加参与者。 有关详细信息，请参阅[向应用添加参与者](luis-how-to-collaborate.md)。

> [!Note]
> 如果 LUIS 应用的所有者将协作者迁移到并添加为 Azure 资源上的参与者，协作者将仍无法访问该应用，除非他们自己也进行迁移。

## <a name="troubleshooting-the-migration-process"></a>排查迁移过程的问题

如果在下拉列表中找不到你的 Azure 订阅：
* 请确保你有一个有效的 Azure 订阅，且该订阅有权创建认知服务资源。 转到 [Azure 门户](https://ms.portal.azure.com)并检查订阅状态。 如果没有，请 [创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)。
* 确保你是在与有效订阅关联的正确租户中。 可通过以下方式切换租户：选择屏幕右上方包含你的姓名首字母的头像。

  > [!div class="mx-imgBorder"]
  > ![用于切换目录的页面](./media/migrate-authoring-key/switch-directories.png)

如果你有现有的创作资源，但在选择“使用现有的创作资源”选项时找不到它，则：
* 创建你的资源的区域可能不同于你尝试在其中进行迁移的区域。
* 请改为通过 LUIS 门户创建新资源。

如果你选择“创建新的创作资源”选项，但迁移失败并出现“检索用户的 Azure 信息失败，请稍后再重试”错误消息，则：
* 在单个区域中，你的订阅可能每个都有 10 个或更多个创作资源。 如果是这样，则无法创建新的创作资源。
* 请选择“使用现有的创作资源”选项，并选择你的订阅下的现有资源之一，通过这种方式进行迁移。

## <a name="create-new-support-request"></a>新建支持请求

如果你有故障排除部分未解决的任何迁移问题，请[创建支持主题](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)并在下面的字段中提供以下信息：

   * **问题类型**：技术方面
   * **订阅**：从下拉列表中选择一个订阅
   * **服务**：搜索并选择“认知服务”
   * **资源**：选择一个 LUIS 资源（如果有）。 如果没有，请选择“常规问题”。

## <a name="next-steps"></a>后续步骤

* 查看[有关创作和运行时密钥的概念](luis-how-to-azure-subscription.md)
* 查看如何[分配密钥](luis-how-to-azure-subscription.md)和[添加参与者](luis-how-to-collaborate.md)