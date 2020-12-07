---
title: 迁移到 Azure 资源创作密钥
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将语言理解 (LUIS) 创作身份验证从电子邮件帐户迁移到 Azure 资源。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/07/2020
ms.openlocfilehash: 243c9834aa256e26d620c00ac0fa7a262919aabd
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762621"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>迁移到 Azure 资源创作密钥

> [!IMPORTANT]
>  从12月3日开始，现有的 LUIS 用户必须完成迁移过程才能继续创作 LUIS 应用程序。

语言理解 (LUIS) 创作身份验证已从电子邮件帐户更改为 Azure 资源。 使用本文了解如何迁移你的帐户（如果尚未迁移）。  


## <a name="what-is-migration"></a>什么是迁移？

迁移是将创作身份验证从电子邮件帐户更改为 Azure 资源的过程。 迁移后，你的帐户将链接到 Azure 订阅和 Azure 创作资源。 *最终需要迁移 (应用所有者和合作者) 的所有 LUIS 用户。*

必须从 [LUIS 门户](https://www.luis.ai)完成迁移。 例如，如果使用 LUIS CLI 创建创作密钥，则需要在 LUIS 门户中完成迁移过程。 迁移后，你仍可以对应用程序进行共同创作者，但会将其添加到 Azure 资源级别，而不是应用程序级别。

> [!Note]
> 在迁移之前，coauthors 称为 "LUIS" 应用级别的 _协作_ 者。 迁移后， _参与者_ 的 azure 角色用于 azure 资源级别的相同功能。

## <a name="notes-before-you-migrate"></a>迁移之前的注意事项

* 迁移不可逆。
* 如果你已登录到多个 [LUIS 区域门户](./luis-reference-regions.md#luis-authoring-regions)，则系统将要求你一次迁移多个区域。
* 如果你是应用程序的所有者，应用程序将自动与你一起迁移。
* 所有者无法选择要迁移的应用子集，此过程不可逆。
* 所有者迁移后，应用程序将从协作者的帐户中消失。
* 系统会提示所有者向协作者发送电子邮件，告知他们迁移。
* 如果你是应用程序的协作者，应用程序将不会迁移。 但会提示协作者导出所需的应用。
* 所有者无法知道是否已迁移协作者。
* 迁移不会自动将协作者移动或添加到 Azure 创作资源。 应用所有者是在迁移后需要完成此步骤的人员。 此步骤需要 [Azure 创作资源的权限](./luis-how-to-collaborate.md)。
* 将协作者分配到 Azure 资源后，需要先迁移，然后才能访问应用程序。 否则，他们将无法访问创作应用程序。
* 无法将迁移的用户添加为应用程序的协作者。


> [!Note]
> 如果需要创建预测运行时资源，有 [一个单独的进程](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 来创建它。

## <a name="migration-prerequisites"></a>迁移先决条件

* 需要与有效的 Azure 订阅相关联。 要求租户管理员将你添加到订阅，或者注册一个 [免费](https://azure.microsoft.com/free/cognitive-services)订阅。
* 需要从 LUIS 门户或 [Azure 门户](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)创建 LUIS Azure 创作资源。 若要从 LUIS 门户创建创作资源，请参阅下一节中所述的迁移过程。
* 如果你是应用程序的协作者，应用程序将不会自动迁移。 在完成迁移流时，系统将提示你导出这些应用。 你还可以使用 [导出 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)。 迁移后，可以将应用导入回 LUIS。 导入过程使用新的应用 ID 创建新应用，你是其所有者。
* 如果你是应用程序的所有者，则无需导出应用，因为它们会自动迁移。 提供了一个电子邮件模板，其中包含每个应用程序的所有协作者列表，因此，他们可以在迁移过程中获得通知。

> [!Note]
> LUIS 应用免费创作，如 F0 层所示。 了解[有关定价层的更多信息](luis-limits.md#key-limits)。


## <a name="migration-steps"></a>迁移步骤

1. 登录到 [LUIS 门户](https://www.luis.ai)后，将打开 Azure 迁移窗口，其中包含迁移步骤。 如果你将其关闭，你将无法继续创作你的 LUIS 应用程序，并且所显示的唯一操作将继续进行迁移。

    > [!div class="mx-imgBorder"]
    > ![迁移窗口简介](./media/migrate-authoring-key/notify-azure-migration.png)

2. 如果你的应用程序是协作者，则会显示你所拥有的应用程序名称的列表，以及每个应用程序上的创作区域和协作者电子邮件。 建议向协作者发送一封电子邮件，通知他们有关迁移的信息，方法是单击应用程序名称左侧的 " **发送** 符号" 按钮。
`*`如果协作者具有分配给应用程序的预测资源，则应用程序名称旁边将显示一个符号。 迁移后，即使协作者无法访问创作应用程序，这些应用仍会分配这些预测资源。 但是，如果预测资源的所有者从 Azure 门户重新 [生成了密钥](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) ，则此分配将中断。  

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
   > 将你的帐户迁移到 Azure 后，你的应用程序将不能再用于协作者。

3. 如果你是任何应用的协作者，则与你共享的应用程序名称的列表将显示在每个应用程序上的创作区域和所有者电子邮件中。 建议通过单击应用程序名称左侧的 "导出" 按钮来导出应用的副本。 你可以在迁移后重新导入这些应用，因为它们不会随你自动迁移。
`*`如果向应用程序分配了预测资源，则应用程序名称旁边将显示一个符号。 迁移后，你的预测资源仍将分配给这些应用程序，即使你不再有权创建这些应用。 如果希望中断预测资源与应用程序之间的分配，则需要进行 Azure 门户并 [重新生成密钥](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)。

   > [!div class="mx-imgBorder"]
   > ![导出应用程序。](./media/migrate-authoring-key/migration-export-apps.png)


4. 在迁移区域的窗口中，系统将要求你将应用程序迁移到在其中创作的同一区域中的 Azure 资源。 LUIS 有三个创作区域 [和门户](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)。 此窗口将显示创作应用程序的区域。 根据你所使用的区域门户和你编写的应用，显示的迁移区域可能会有所不同。 

   > [!div class="mx-imgBorder"]
   > ![多区域迁移。](./media/migrate-authoring-key/migration-regional-flow.png)

5. 对于每个区域，请选择创建新的 LUIS 创作资源，或使用按钮迁移到现有资源。

   > [!div class="mx-imgBorder"]
   > ![选择创建或现有的创作资源](./media/migrate-authoring-key/migration-multiregional-resource.png)

   提供以下信息：

   * **租户名称**： Azure 订阅与之关联的租户。 默认情况下，此设置为你当前正在使用的租户。 可以通过以下方式切换租户：关闭此窗口，并选择屏幕右上方的头像，其中包含你的姓名缩写。 单击 " **迁移到 Azure** " 以重新打开该窗口。
   * **Azure 订阅名称**：将与资源关联的订阅。 如果有多个订阅属于租户，请从下拉列表中选择所需的订阅。
   * **创作资源名称**：所选的自定义名称。 它用作创作和预测终结点查询的 URL 的一部分。 如果要创建新的创作资源，请注意，资源名称只能包含字母数字字符， `-` 且不能以开头或结尾 `-` 。 如果名称中包含任何其他符号，则资源创建和迁移将会失败。
   * **Azure 资源组名称**：从下拉列表中选择的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。 如果订阅中目前没有资源组，则不允许在 LUIS 门户中创建资源组。 转到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) ，然后转到 LUIS 继续登录过程。

6. 在所有区域中成功迁移后，请单击 "完成"。 你现在可以访问应用程序。 你可以继续创作和维护门户内所有区域中的所有应用程序。


## <a name="using-apps-after-migration"></a>迁移后使用应用

迁移过程完成后，你作为其所有者的所有 LUIS 应用将立即分配给单个 LUIS 创作资源。
" **我的应用** " 列表显示迁移到新的创作资源的应用。 在访问你的应用程序之前，请选择 " **选择其他创作资源** " 以选择订阅和创作资源，以查看可以创作的应用。

> [!div class="mx-imgBorder"]
> ![选择订阅和创作资源](./media/migrate-authoring-key/select-sub-and-resource.png)


如果计划以编程方式编辑应用，则需要创作密钥值。 通过在 LUIS 门户中的屏幕顶部单击 " **管理** "，然后选择 " **Azure 资源**" 显示这些值。 资源的 " **密钥和终结点** " 页上的 "Azure 门户中也提供了它们。 你还可以创建更多的创作资源并从同一页分配这些资源。

## <a name="adding-contributors-to-authoring-resources"></a>添加参与者以创作资源

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

了解如何在创作资源上 [添加参与者](luis-how-to-collaborate.md) 。 参与者将有权访问该资源下的所有应用程序。

可以在该资源的 " **访问控制" (IAM ")** 页上，将参与者添加到 Azure 门户中的创作资源。 有关详细信息，请参阅 [将参与者添加到应用](luis-how-to-collaborate.md)。

> [!Note]
> 如果 LUIS 应用的所有者将协作者迁移到并添加为 Azure 资源上的参与者，协作者将仍无法访问该应用，除非他们自己也进行迁移。


## <a name="troubleshooting-the-migration-process"></a>迁移过程疑难解答

如果在下拉列表中找不到 Azure 订阅：
* 确保你有一个有效的 Azure 订阅，该订阅有权创建认知服务资源。 中转到 [Azure 门户](https://ms.portal.azure.com) 并检查订阅的状态。 如果没有，请 [创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)。
* 确保你处于与有效订阅关联的正确租户中。 可以在屏幕右上方切换租户，其中包含你的姓名缩写。

  > [!div class="mx-imgBorder"]
  > ![用于切换目录的页面](./media/migrate-authoring-key/switch-directories.png)

如果有现成的创作资源，但在选择 " **使用现有创作资源** " 选项时找不到该资源：
* 你的资源可能在不同于要迁移的区域中创建。
* 改为通过 LUIS 门户创建新资源。

如果选择 " **创建新的创作资源** " 选项，并且迁移失败，并出现错误消息 "检索用户的 Azure 信息失败，请稍后重试"：
* 对于每个区域，订阅可能有10个或更多的创作资源，每个订阅。 如果是这种情况，您将无法创建新的创作资源。
* 通过选择 " **使用现有创作资源** " 选项并选择订阅下的现有资源之一来进行迁移。

## <a name="create-new-support-request"></a>创建新的支持请求

如果在故障排除部分中未解决任何迁移问题，请 [创建支持主题](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) ，并使用以下字段提供以下信息：

   * **问题类型**：技术
   * **订阅**：从下拉列表中选择一个订阅
   * **服务**：搜索并选择 "认知服务"
   * **资源**：选择 LUIS 资源（如果有）。 否则，请选择 "常规问题"。

## <a name="next-steps"></a>后续步骤

* 查看 [有关创作和运行时密钥的概念](luis-how-to-azure-subscription.md)
* 查看如何 [分配密钥](luis-how-to-azure-subscription.md) 并 [添加参与者](luis-how-to-collaborate.md)
