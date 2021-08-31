---
title: 设置实例和身份验证（门户）
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 门户设置 Azure 数字孪生服务实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2, subject-rbac-steps
ms.openlocfilehash: f520ed1054c5d0d9dd6c053e22a984a813840131
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468769"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>设置 Azure 数字孪生实例和身份验证（门户）。

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文将介绍新 Azure 数字孪生实例的设置步骤，包括创建实例和设置身份验证。 完成本文操作后，可准备对 Azure 数字孪生实例开始编程。

本文此版本借助 Azure 门户逐个手动完成这些步骤。 Azure 门户是基于 Web 的统一控制台，提供可替代命令行工具的方法。
* 若要使用 CLI 手动完成这些步骤，请参阅本文的 CLI 版本：[设置实例和身份验证 (CLI)](how-to-set-up-instance-cli.md)。
* 若要使用部署脚本示例完成自动设置，请参阅本文的脚本编写版本：[设置实例和身份验证（已编写脚本）](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. 在以下“创建资源”页中，填写以下给定值：
    * **订阅**：要使用的 Azure 订阅
      - **资源组**：要在其中部署实例的资源组。 如果尚未记住现有的资源组，可通过选择“新建”链接并输入新资源组的名称来创建资源组。
    * **位置**：用于部署且已启用 Azure 数字孪生的区域。 有关区域支持的更多详细信息，请访问各区域的 Azure 产品可用性（Azure 数字孪生）。
    * **资源名称**： Azure 数字孪生实例的名称。 如果订阅在区域中已存在使用指定名称的其他 Azure 数字孪生实例，则系统将要求选择其他名称。
    * **授予对资源的访问权限**：选中本部分中的框将授予 Azure 帐户访问和管理实例中的数据的权限。 如果你将管理实例，现在应选中此框。 如果它因你在订阅中无权限而灰显，你可以继续创建资源，然后让具有所需权限的人员稍后授予你该角色。 有关此角色以及向实例分配角色的详细信息，请参阅下一部分 - [设置用户访问权限](#set-up-user-access-permissions)。

    :::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Azure 门户中 Azure 数字孪生的“创建资源”过程的屏幕截图。已填写所描述的值。":::

4. 完成后，如果不想为实例配置更多设置，可以选择“查看 + 创建”。 将会转到“摘要”页，可以在其中查看已输入的实例详细信息并单击“创建”完成此步骤。 

    如果想要为实例配置更多详细信息，下一部分将介绍其余的设置选项卡。

### <a name="additional-setup-options"></a>其他设置选项

下面是设置期间可以配置的其他选项，在“创建资源”过程中使用其他选项卡可以完成这些配置。

* **网络**：在此选项卡中，可以使用“[Azure 专用链接](../private-link/private-link-overview.md)”启用专用终结点，以消除实例暴露于公共网络的风险。 有关说明，请参阅[使用专用链接启用专用访问（预览）](./how-to-enable-private-link.md?tabs=portal#add-a-private-endpoint-during-instance-creation)。
* **高级**：在此选项卡中，可以为实例启用“系统托管的标识”，以便用于将事件转发到“[终结点](concepts-route-events.md)”。 有关结合使用系统管理的标识与 Azure 数字孪生的更多信息，请参阅 [Azure 数字孪生安全性解决方案](concepts-security.md#managed-identity-for-accessing-other-resources-preview)。
* **标记**：在此选项卡中，可以将标记添加到实例，以便组织 Azure 资源。 有关 Azure 资源标记的详细信息，请参阅标记资源、资源组和订阅以合理进行组织。

### <a name="verify-success-and-collect-important-values"></a>验证是否成功并收集重要值

在选择“创建”完成实例设置后，可以在门户图标栏上的 Azure 通知中查看实例的部署状态。 通知将指示成功部署的时间，并且可以选择“转到资源”按钮来查看创建的实例。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="在 Azure 门户中显示成功部署并突出显示“转到资源”按钮的 Azure 通知的屏幕截图。":::

或者，如果部署失败，通知将指出失败原因。 观察错误消息中的建议，然后重新尝试创建实例。

>[!TIP]
>创建实例后，可以通过在 Azure 门户搜索栏中搜索实例的名称，随时返回到此页。

在实例的“概述”页中，记下其“名称”、“资源组”和“主机名”。 在继续使用 Azure 数字孪生实例时，需要使用所有这些重要的值。 如果其他用户将对该实例进行编程，则应与他们共享这些值。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Azure 门户的屏幕截图，突出显示了 Azure 数字孪生实例的“概述”页面中的重要值。":::

你现在已准备好 Azure 数字孪生实例。 接下来，你将向适当的 Azure 用户授予权限来管理该实例。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

可通过两种方法在 Azure 数字孪生中为用户创建角色分配：
* [在创建 Azure 数字孪生实例期间](#assign-the-role-during-instance-creation)
* [使用 Azure 标识管理 (IAM)](#assign-the-role-using-azure-identity-management-iam)

这两种方法需要相同的权限。

### <a name="prerequisites-permission-requirements"></a>先决条件：权限要求

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role-during-instance-creation"></a>在创建实例期间分配角色

在通过[本文前面部分](#create-the-azure-digital-twins-instance)所述的过程创建 Azure 数字孪生资源时，选择“授予对资源的访问权限”下的“分配 Azure 数字孪生数据所有者角色” 。 这将授予你对数据平面 API 的完全访问权限。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2-role.png" alt-text="Azure 门户中 Azure 数字孪生的“创建资源”过程的屏幕截图。突出显示了“授予对资源的访问权限”下的复选框。":::

如果你无权向标识分配角色，该框将灰显。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2-role-greyed.png" alt-text="Azure 门户中 Azure 数字孪生的“创建资源”过程的屏幕截图。“授予对资源的访问权限”下的复选框已禁用。":::

在此情况下，你仍可继续成功创建 Azure 数字孪生资源，但具有适当权限的人员将需要向你或将管理该实例数据的人员分配此角色。

### <a name="assign-the-role-using-azure-identity-management-iam"></a>使用 Azure 标识管理 (IAM) 分配角色

还可以使用 Azure 标识管理 (IAM) 中的访问控制选项分配“Azure 数字孪生数据所有者”角色。

1. 首先，在 Azure 门户中打开 Azure 数字孪生实例的页面。 

1. 选择“访问控制 (IAM)”。

1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

1. 分配“Azure 数字孪生数据所有者”角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | [Azure 数字孪生数据所有者](../role-based-access-control/built-in-roles.md#azure-digital-twins-data-owner) |
    | 将访问权限分配到 | 用户、组或服务主体 |
    | 成员 | 搜索要分配的用户的姓名或电子邮件地址。 |
    
    ![“添加角色分配”页](../../includes/role-based-access-control/media/add-role-assignment-page.png)

### <a name="verify-success"></a>验证是否成功

可以在“访问控制 (IAM) > 角色分配”下查看已设置的角色分配。 用户应显示在列表中，其角色显示为“Azure 数字孪生数据所有者”。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure 门户中 Azure 数字孪生实例的角色分配屏幕截图。":::

现在，你已准备好 Azure 数字孪生实例，并分配了管理权限。

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令在实例上测试各个 REST API 调用： 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Azure 数字孪生 CLI 命令集](concepts-cli.md)

或者，了解如何使用验证码将客户端应用程序连接到实例：
* [编写应用身份验证码](how-to-authenticate-client.md)