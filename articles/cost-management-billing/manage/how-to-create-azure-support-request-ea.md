---
title: 如何为企业协议问题创建 Azure 支持请求
description: 需要协助的企业协议客户可在 Azure 门户中查找自助服务解决方案并创建和管理支持请求。
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: billing
ms.openlocfilehash: 7826b2c19a77ef3762f39ade9a80f26615f704bd
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660184"
---
# <a name="create-an-azure-support-request-for-an-enterprise-agreement-issue"></a>为企业协议问题创建 Azure 支持请求

通过 Azure，可以为企业协议创建和管理支持请求（也称为支持票证）。 可在 [Azure 门户](https://portal.azure.com)中创建和管理请求，本文将讲到这一点。 还可使用 [Azure 支持票证 REST API](/rest/api/support) 或通过使用 [Azure CLI](/cli/azure/azure-cli-support-request) 以编程方式创建和管理请求。

> [!NOTE]
> Azure 门户 URL 特定于 Azure 云，你的组织在云端部署。
>
>- 商业用途的 Azure 门户是：[https://portal.azure.com](https://portal.azure.com)
>- 德国的 Azure 门户是：[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>- 美国政府的 Azure 门户是：[https://portal.azure.us](https://portal.azure.us)

Azure 对订阅管理（包括计费、配额调整和帐户转移）提供无限支持。 你需要具备一个支持计划才能获得技术支持。 有关详细信息，请参阅[比较支持计划](https://azure.microsoft.com/support/plans)。

## <a name="getting-started"></a>开始使用

可转到 Azure 门户中的“帮助 + 支持”。 可通过 Azure 门户菜单、全局标头或服务的资源菜单访问该功能。 你必须先具有适当的权限，然后才可提出支持请求。

### <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

要为企业协议创建支持请求，你必须是与企业注册关联的企业管理员或合作伙伴管理员。 

### <a name="go-to-help--support-from-the-global-header"></a>通过全局标头转到“帮助 + 支持”

若要在 Azure 门户的任意位置启动支持请求：

1. 选择全局标题中的问号符号，然后选择“帮助 + 支持”。

   :::image type="content" source="media/how-to-create-azure-support-request-ea/help-support-new-lower.png" alt-text="Azure 门户中“帮助”菜单的屏幕截图。":::

1. 选择“创建支持请求”。 按照提示提供你所遇问题的相关信息。 我们将建议一些可能的解决方案，收集问题的相关详细信息，并帮助你提交和跟踪支持请求。

   :::image type="content" source="media/how-to-create-azure-support-request-ea/new-support-request-2-lower.png" alt-text="“帮助 + 支持”页面的屏幕截图，其中显示了“创建支持请求”链接。":::

### <a name="go-to-help--support-from-a-resource-menu"></a>通过资源菜单转到“帮助 + 支持”

启动支持请求：

1. 从资源菜单的“支持 + 疑难解答”部分，选择“新建支持请求” 。

   :::image type="content" source="media/how-to-create-azure-support-request-ea/in-context-2-lower.png" alt-text="资源窗格中“新建支持请求”选项的屏幕截图。":::

1. 按照提示提供你当前遇到的问题的相关信息。 从资源开始支持请求过程时，一些选项已经过预选择。

## <a name="create-a-support-request"></a>创建支持请求

我们将引导你完成一些步骤，以收集你所遇问题的相关信息并帮助你解决该问题。 以下部分将介绍各个步骤。

### <a name="problem-description"></a>问题描述

1. 键入问题摘要，然后选择“问题类型”。 
1. 在“问题类型”列表中，针对 EA 门户相关问题，选择“注册管理”。   
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" alt-text="显示选择“注册管理”的屏幕截图。" lightbox="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" :::
1. 对于“注册编号”，选择注册编号。 
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-enrollment.png" alt-text="显示选择“注册编号”的屏幕截图。" :::
1. 对于“问题类型”，选择最能描述你遇到的问题类型的问题类别。  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-problem-type.png" alt-text="示选择问题类型的屏幕截图。" :::
1. 对于“问题子类型”，选择一个问题子类别。 

提供所有这些详细信息后，选择“下一步:解决方案”。

### <a name="recommended-solution"></a>建议的解决方案

根据你提供的信息，我们将展示可用于尝试解决问题的建议解决方案。 在某些情况下，我们甚至可运行一次快速诊断。 这些解决方案由 Azure 工程师编写，可解决最常见的问题。

如果仍然无法解决问题，请选择“下一步:详细信息”，继续创建支持请求。

### <a name="additional-details"></a>其他详细信息

接下来，我们会收集有关问题的更多详细信息。 在此步骤中提供全面且详细的信息有助于我们将你的支持请求发送给合适的工程师。

1. 在“详细信息”选项卡上，完成“问题详细信息”部分，以便我们了解有关你的问题的更多信息。 可能的话，请提供问题开始时间及任何重现步骤。 可上传文件，例如日志文件或诊断输出内容。 有关文件上传的详细信息，请参阅[文件上传准则](../../azure-portal/supportability/how-to-manage-azure-support-request.md#file-upload-guidelines)。

1. 在“共享诊断信息”部分中，选择“是”或“否”。   选择“是”将允许 Azure 支持从你的 Azure 资源收集[诊断信息](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)。 如果不想共享此信息，请选择“否”。 在某些情况下，可以从更多选项中进行选择。

1. 在“支持方法”部分，选择问题的严重程度。 最大严重性级别取决于[支持计划](https://azure.microsoft.com/support/plans)。

1. 提供首选联系方法、空闲时间和首选支持语言。

1. 接下来，完成“联系人信息”部分，让我们知道如何与你联系。  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/details-tab.png" alt-text="显示“详细信息”选项卡的屏幕截图。" lightbox="./media/how-to-create-azure-support-request-ea/details-tab.png" :::

填写所有必要信息后，选择“下一步:查看 + 创建”。

### <a name="review--create"></a>查看 + 创建

创建请求之前，请查看要发送给支持人员的所有详细信息。 如果需要进行更改，可以选择“上一页”以返回到任何选项卡。 支持请求的完成情况让你满意时，请选择“创建”。

支持工程师将使用你指定的方法与你联系。 要了解初始响应时间，请参阅[支持范围和响应速度](https://azure.microsoft.com/support/plans/response/)。

## <a name="cant-create-request-with-microsoft-account"></a>无法使用 Microsoft 帐户创建请求

如果你有 Microsoft 帐户 (MSA)，则无法创建 Azure 支持票证。 Microsoft 帐户是为 Outlook、Windows Live 和 Hotmail 等服务创建的。

若要创建 Azure 支持票证，组织帐户必须具有 EA 管理员角色或合作伙伴管理员角色。

如果你有 MSA，请让管理员为你创建一个组织帐户。 然后，企业管理员或合作伙伴管理员必须将你的组织帐户添加为企业管理员或合作伙伴管理员。 然后，你可以使用你的组织帐户来提交支持请求。

- 要添加企业管理员，请参阅[创建另一个企业管理员](ea-portal-administration.md#create-another-enterprise-administrator)。
- 要添加合作伙伴管理员，请参阅[管理合作伙伴管理员](ea-partner-portal-administration.md#manage-partner-administrators)。

## <a name="next-steps"></a>后续步骤

打开以下链接以了解更多信息：

* [如何管理 Azure 支持请求]()../../azure-portal/supportability/how-to-manage-azure-support-request.md)
* [Azure 支持票证 REST API](/rest/api/support)
* 在 [Twitter](https://twitter.com/azuresupport) 上与我们联系
* 在 [Microsoft 问答页](/answers/products/azure)上获取同行的帮助
* 在 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq)中了解详细信息