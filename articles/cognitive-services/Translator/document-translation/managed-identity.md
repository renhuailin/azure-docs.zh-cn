---
title: 创建并使用托管标识
titleSuffix: Azure Cognitive Services
description: 了解如何在 Azure 门户中创建并使用托管标识
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 07/08/2021
ms.author: lajanuar
ms.openlocfilehash: 340121b40845369fe05e36a302556543078629eb
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289129"
---
# <a name="create-and-use-managed-identity-for-document-translation"></a>创建并使用托管标识进行文档翻译

> [!IMPORTANT]
>
> 用于文档翻译的托管标识当前在全局区域不可用。 如果打算将托管标识用于文档翻译操作，请在非全局 Azure 区域中[创建翻译器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)。

## <a name="what-is-managed-identity"></a>什么是托管标识？

 Azure 托管标识是一项服务主体，用于创建 Azure 受管理资源的 Azure Active Directory (Azure AD) 标识和特定权限。 你可以使用托管标识授予对支持 Azure AD 身份验证的任何资源的访问权限。 若要授予权限，可以使用 [Azure 基于角色的访问控制](../../../role-based-access-control/overview.md) (Azure RBAC) 为托管标识分配角色。  在 Azure 中使用托管标识不会增加成本。

托管标识支持专用和可公开访问的 Azure blob 存储帐户。  对于具有公共访问权限的存储帐户，你可以选择使用共享访问签名 (SAS) 授予有限访问权限。  本文将探讨如何使用系统分配的托管标识管理对 Azure blob 存储帐户中翻译文档的访问权限。

> [!NOTE]
>
> 对于使用公共 Internet 上可用的 Azure blob 存储帐户的所有操作，可以在有限时段内提供具有受限权限的共享访问签名 (SAS) URL，并在 POST 请求中传递它：
>
> * 若要检索 SAS URL，请转到 Azure 门户的存储资源，并选择“存储资源管理器”选项卡。
> * 导航到你的容器，右键单击，并选择“获取共享访问签名”。 请务必获取容器的 SAS，而不是存储帐户本身的。
> * 确保选中“读取”、“写入”、“删除”和“列表”权限，然后单击“创建”    。
> * 然后将“URL”部分中的值复制到临时位置。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个有效的 [Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)；如果没有帐户，可以[创建一个免费帐户](https://azure.microsoft.com/free/) 。

* 已分配到非全局区域的[单一服务翻译器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)（而非多服务认知服务）资源 。 如需详细步骤，请参阅[使用 Azure 门户创建认知服务资源](../../cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)。

* 与翻译器资源位于同一区域的 [Azure blob 存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 你需创建一个容器，用于存储和整理存储帐户中的 blob 数据。 如果帐户具有防火墙，则必须启用“[受信任的 Azure 服务例外](../../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions)”复选框。

    :::image type="content" source="../media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="屏幕截图：“允许受信任的服务”复选框，门户视图":::

* 简要了解使用 Azure 门户的 [Azure 基于角色的访问控制 (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md)。

## <a name="managed-identity-assignments"></a>托管标识分配

有两种托管标识类型：系统分配的托管标识和用户分配的托管标识 。  目前，文档翻译由系统分配的托管标识提供支持。 系统分配的托管标识直接在服务实例上启用。 系统默认禁用此标识，因此你必须转到资源并更新标识设置。 系统分配的托管标识在其整个生命周期内与资源绑定。 如果删除资源，则托管标识也将被删除。

在以下步骤中，我们将启用系统分配的托管标识，并授予翻译器资源对 Azure blob 存储帐户的有限访问权限。

## <a name="enable-a-system-assigned-managed-identity-using-the-azure-portal"></a>使用 Azure 门户启用系统分配的托管标识

>[!IMPORTANT]
>
> 若要启用系统分配的托管标识，你需要 Microsoft.Authorization/roleAssignments/write 权限，例如[所有者](../../../role-based-access-control/built-in-roles.md#owner)或[用户访问管理员](../../../role-based-access-control/built-in-roles.md#user-access-administrator)皆拥有此权限  。 可在以下四个级别指定范围：管理组、订阅、资源组或资源。

1. 使用已与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中导航到“翻译器”资源页。

1. 在左侧导航栏中，从“资源管理”列表中选择“标识” ：

    :::image type="content" source="../media/managed-identities/resource-management-identity-tab.png" alt-text="屏幕截图：Azure 门户中“资源管理”下的“标识”选项卡。":::

1. 在主窗口中，将“系统分配的状态”选项卡切换为“开启” 。

1. 在“权限”下，选择“Azure 角色分配” ：

    :::image type="content" source="../media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="屏幕截图：在 Azure 门户中启用系统分配的托管标识。":::

1. “Azure 角色分配”页将打开。 从下拉菜单中选择订阅，然后选择“&plus; 添加角色分配”。

    :::image type="content" source="../media/managed-identities/azure-role-assignments-page-portal.png" alt-text="屏幕截图：Azure 门户中的“Azure 角色分配”页。":::

>[!NOTE]
>
> 如果由于禁用了“添加”>“添加角色分配”选项而无法在 Azure 门户中分配角色，或者收到“你无权在此范围内添加角色分配”权限错误信息，请检查你目前用于登录的用户是否分配有在存储资源的存储范围内具有 Microsoft.Authorization/roleAssignments/write 权限的角色（例如[所有者](../../../role-based-access-control/built-in-roles.md#owner)或[用户访问管理员](../../../role-based-access-control/built-in-roles.md#user-access-administrator)） 。

7. 接下来，你需要向翻译器服务资源分配一个存储 Blob 数据参与者角色。 在“添加角色分配”弹出窗口中，按如下所示填写字段，然后选择“保存” ：

    | 字段 | 值|
    |------|--------|
    |**范围**| 存储。|
    |**订阅**| 与存储资源关联的订阅。|
    |**资源**| 存储资源的名称。|
    |**角色** | 存储 Blob 数据参与者。|

     :::image type="content" source="../media/managed-identities/add-role-assignment-window.png" alt-text="屏幕截图：Azure 门户中的“添加角色分配”页。":::

1. 收到“已添加角色分配”确认消息后，可以刷新页面以查看添加的角色分配。 

    :::image type="content" source="../media/managed-identities/add-role-assignment-confirmation.png" alt-text="屏幕截图：已添加角色分配确认弹出消息。":::

1. 如果没有立即看到更改，请等待并尝试再次刷新页面。 分配角色或删除角色分配时，最长可能需要 30 分钟更改才能生效。

    :::image type="content" source="../media/managed-identities/assigned-roles-window.png" alt-text="屏幕截图：“Azure 角色分配”窗口。":::

 很好！ 你已完成启用系统分配的托管标识的相关步骤。 通过此标识凭据，可以授予翻译器对存储资源的特定访问权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [有关 Azure 资源的托管标识的常见问题解答](../../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)

> [!div class="nextstepaction"]
>[使用托管标识获取访问令牌](../../../app-service/overview-managed-identity.md?tabs=dotnet#obtain-tokens-for-azure-resources)