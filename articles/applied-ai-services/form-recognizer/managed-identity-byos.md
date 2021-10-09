---
title: 通过自带存储方式 (BYOS) 创建和使用托管标识
titleSuffix: Azure Applied AI Services
description: 了解如何使用 BYOS 帐户创建和使用托管标识
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/08/2021
ms.author: lajanuar
ms.openlocfilehash: 43220ce85bf02919a0ccf069bc9646a16c3a0a26
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155346"
---
# <a name="create-and-use-managed-identity-for-your-form-recognizer-resource"></a>为表单识别器资源创建和使用托管标识

> [!IMPORTANT]
> Azure 基于角色的访问控制 (Azure RBAC) 分配目前处于预览状态，因此不建议用于生产工作负载。 某些功能可能不受支持或者受限。 Azure RBAC 分配用于向托管标识授予权限。

## <a name="what-is-managed-identity"></a>什么是托管标识？

Azure 托管标识是一项服务主体，用于创建 Azure 受管理资源的 Azure Active Directory (Azure AD) 标识和特定权限。 你可以使用托管标识授予对支持 Azure AD 身份验证的任何资源的访问权限。 若要授予权限，可以使用 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md) (Azure RBAC) 为托管标识分配角色。  在 Azure 中使用托管标识不会增加成本。

托管标识支持专用和可公开访问的 Azure blob 存储帐户。  对于具有公共访问权限的存储帐户，你可以选择使用共享访问签名 (SAS) 授予有限访问权限。   本文介绍如何为表单识别器实例启用系统分配的托管标识。

## <a name="private-storage-account-access"></a>专用存储帐户访问

 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)支持专用 Azure 存储帐户访问和身份验证。 如果 Azure 存储帐户受虚拟网络 (VNet) 或防火墙保护，或者已启用自带存储 (BYOS)，则表单识别器无法直接访问你的存储帐户数据；但是，启用托管标识后，表单识别器服务可以使用分配的托管标识凭据访问你的存储帐户。

> [!NOTE]
>
> * 如果要使用[表单识别器示例标记工具 (FOTT)](https://fott-2-1.azurewebsites.net/) 分析存储数据，则必须在启用 VNet 或防火墙的情况下部署该工具。
>
> * [Analyze Receipt API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)、[Business Card API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)、[Invoice API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)、[Identity Document API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707) 和 [Custom Form API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) 可以通过将请求发布为原始二进制内容，从单个文档中提取数据。     在这些情况下，不需要托管标识凭据。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个有效的 [Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)；如果没有帐户，可以[创建一个免费帐户](https://azure.microsoft.com/free/) 。

* Azure 门户中的[表单识别器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)或[认知服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)。  如需详细步骤，请参阅[使用 Azure 门户创建认知服务资源](../../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)。

* 与表单识别器资源位于同一区域的 [Azure blob 存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 你需创建一个容器，用于存储和整理存储帐户中的 blob 数据。 

  * 如果存储帐户位于防火墙后面，则必须启用以下配置： </br></br>

  * 从存储帐户页上的左侧菜单中选择“安全性 + 网络”→“网络” 。
    :::image type="content" source="media/managed-identities/security-and-networking-node.png" alt-text="屏幕截图：“安全性 + 网络”选项卡。":::

  * 在主窗口中，选择“允许从所选网络访问”。
  :::image type="content" source="media/managed-identities/firewalls-and-virtual-networks.png" alt-text="屏幕截图：选择了“所选网络”单选按钮。":::

  * 在所选网络页上，导航到“例外”类别，确保已启用[允许受信任的服务列表中的 Azure 服务访问此存储帐户](/azure/storage/common/storage-network-security?tabs=azure-portal#manage-exceptions)复选框。 

    :::image type="content" source="media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="屏幕截图：“允许受信任的服务”复选框，门户视图":::
* 简要了解使用 Azure 门户的 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)。

## <a name="managed-identity-assignments"></a>托管标识分配

有两种托管标识类型：系统分配的托管标识和用户分配的托管标识 。 目前表单识别器由系统分配的托管标识提供支持。 系统分配的托管标识直接在服务实例上启用。 系统默认禁用此标识，因此你必须转到资源并更新标识设置。 系统分配的托管标识在其整个生命周期内与资源绑定。 如果删除资源，则托管标识也将被删除。

在以下步骤中，我们将启用系统分配的托管标识，并授予表单识别器对 Azure blob 存储帐户的有限访问权限。

## <a name="enable-a-system-assigned-managed-identity"></a>启用系统分配的托管标识

>[!IMPORTANT]
>
> 若要启用系统分配的托管标识，你需要 Microsoft.Authorization/roleAssignments/write 权限，例如[所有者](../../role-based-access-control/built-in-roles.md#owner)或[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)皆拥有此权限  。 可在以下四个级别指定范围：管理组、订阅、资源组或资源。

1. 使用已与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。

1. 导航至 Azure 门户中的“表单识别器”资源页面。

1. 在左侧导航栏中的“资源管理”列表下，选择“标识”： 

    :::image type="content" source="media/managed-identities/resource-management-identity-tab.png" alt-text="屏幕截图：Azure 门户中“资源管理”下的“标识”选项卡。":::

1. 在主窗口中，将“系统分配的状态”选项卡切换为“开启” 。

1. 在“权限”下，选择“Azure 角色分配” ：

    :::image type="content" source="media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="屏幕截图：在 Azure 门户中启用系统分配的托管标识。":::

1. “Azure 角色分配”页将打开。 从下拉菜单中选择订阅，然后选择“&plus; 添加角色分配”。

    :::image type="content" source="media/managed-identities/azure-role-assignments-page-portal.png" alt-text="屏幕截图：Azure 门户中的“Azure 角色分配”页。":::

    > [!NOTE]
    >
    > 如果由于禁用了“添加 > 添加角色分配”选项而无法在 Azure 门户中分配角色，或者收到“你无权在此作用域添加角色分配”权限错误信息，请检查你目前登录的用户是否获分配在存储资源的存储作用域具有 Microsoft.Authorization/roleAssignments/write 权限的角色（例如所有者或用户访问管理员）。

 7. 接下来，将一个存储 blob 数据读者角色分配给你的表单识别器服务资源。 在“添加角色分配”弹出窗口中，按如下所示填写字段，然后选择“保存”： 

    | 字段 | 值|
    |------|--------|
    |**范围**| **_存储_**|
    |**订阅**| 与存储资源关联的订阅。|
    |**资源**| 存储资源的名称|
    |**角色** | 存储 blob 数据读者：允许对 Azure 存储 blob 容器和数据的读取访问。|

     :::image type="content" source="media/managed-identities/add-role-assignment-window.png" alt-text="屏幕截图：Azure 门户中的“添加角色分配”页。":::

1. 收到“已添加角色分配”确认消息后，可以刷新页面以查看添加的角色分配。

    :::image type="content" source="media/managed-identities/add-role-assignment-confirmation.png" alt-text="屏幕截图：已添加角色分配确认弹出消息。":::

1. 如果没有立即看到更改，请等待并尝试再次刷新页面。 分配角色或删除角色分配时，最长可能需要 30 分钟更改才能生效。

    :::image type="content" source="media/managed-identities/assigned-roles-window.png" alt-text="屏幕截图：“Azure 角色分配”窗口。":::

 就这么简单！ 你已完成启用系统分配的托管标识的相关步骤。 通过此标识凭据，你可以向表单识别器授予 BYOS 帐户中存储的文档和文件的特定访问权限。

## <a name="learn-more-about--managed-identity"></a>了解有关托管标识的详细信息

> [!div class="nextstepaction"]
> [有关 Azure 资源的托管标识：常见问题解答 - Azure AD](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)
