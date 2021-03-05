---
title: 设置实例和身份验证（门户）
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 门户设置 Azure 数字孪生服务的实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8263e0805f48976222e66922be8c04e0902101d6
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201832"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>设置 Azure 数字孪生实例和身份验证 (门户) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍 **设置新的 Azure 数字孪生实例** 的步骤，包括创建实例和设置身份验证。 完成本文后，你将拥有一个可开始对进行编程的 Azure 数字孪生实例。

本文的此版本通过使用 Azure 门户逐个手动完成这些步骤。 Azure 门户是基于 Web 的统一控制台，提供可替代命令行工具的方法。
* 若要使用 CLI 手动完成这些步骤，请参阅本文的 CLI 版本： how [*to： Set a instance and authentication (CLI)*](how-to-set-up-instance-cli.md)。
* 若要通过使用部署脚本示例的自动安装运行，请参阅本文的脚本编写版本： how [*to： Set a instance and authentication (script)*](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分中，将使用 [Azure 门户](https://ms.portal.azure.com/)**创建 Azure 数字孪生的新实例**。 导航到门户，然后用你的凭据登录。

进入门户后，首先在 Azure 服务的 "主页" 菜单中选择 " _创建资源_ "。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="从 &quot;主页&quot; 的 &quot;Azure 门户&quot; 页中选择 &quot;创建资源&quot;":::

在 "搜索" 框中搜索 *Azure 数字孪生* ，并从结果中选择 **azure 数字孪生** 服务。 选择 " _创建_ " 按钮以创建服务的新实例。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="从 Azure 数字孪生服务页中选择 &quot;创建&quot;":::

在下面的 " **创建资源** " 页上，填写以下给定值：
* **订阅**：要使用的 Azure 订阅
  - **资源组**：要在其中部署实例的资源组。 如果尚未记住现有的资源组，可通过选择 "新建" 链接并输入新资源组 *的名称* 来创建一个资源组。
* **位置**：用于部署的支持 Azure 数字孪生的区域。 有关区域支持的更多详细信息，请访问 azure [*孪生) 提供 (的 azure 产品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* **资源名称**： Azure 数字孪生实例的名称。 如果你的订阅在已使用指定名称的区域中有另一个 Azure 数字孪生实例，则系统将要求你选择其他名称。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="填写所述的值以创建 Azure 数字孪生资源":::

完成后，如果不想为实例配置更多设置，可以选择 " **查看 + 创建** "。 这会转到 "摘要" 页，您可以在其中查看已输入的实例详细信息并完成 **创建**。 

如果确实要为实例配置更多详细信息，下一节将介绍其余设置选项卡。

### <a name="additional-setup-options"></a>其他设置选项

下面是在安装过程中可以使用 " **创建资源** " 进程中的其他选项卡配置的其他选项。

* **网络**：在此选项卡中，你可以使用 [Azure private Link](../private-link/private-link-overview.md) 启用专用终结点，以消除你的实例的公共网络公开。 有关说明，请参阅 how [*to： Enable private access With Private Link (preview)*](./how-to-enable-private-link-portal.md#add-a-private-endpoint-during-instance-creation)。
* **高级**：在此选项卡中，你可以为可在将事件转发到 [终结点](concepts-route-events.md)时使用的实例启用 [系统管理的标识](../active-directory/managed-identities-azure-resources/overview.md)。 有关说明，请参阅 [*如何：为路由事件启用托管标识 (预览)*](./how-to-enable-managed-identities-portal.md#add-a-system-managed-identity-during-instance-creation)。
* **标记**：在此选项卡中，你可以将标记添加到你的实例，以帮助你在 Azure 资源之间对其进行组织。 有关 Azure 资源标记的详细信息，请参阅 [*为逻辑组织标记资源、资源组和订阅*](../azure-resource-manager/management/tag-resources.md)。

### <a name="verify-success-and-collect-important-values"></a>验证成功并收集重要值

通过选择 " **创建**" 完成实例设置后，可以通过门户图标栏在 Azure 通知中查看实例的部署状态。 通知将指示部署成功的时间，并且你将能够选择 " _中转到资源_ " 按钮来查看已创建的实例。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="显示成功部署并突出显示 &quot;中转到资源&quot; 按钮的 Azure 通知视图":::

或者，如果部署失败，通知将指出原因。 观察错误消息中的建议，然后重试创建实例。

>[!TIP]
>创建实例后，可以通过在 Azure 门户搜索栏中搜索实例的名称，随时返回到此页。

在实例的 " *概述* " 页上，记下其 *名称*、 *资源组* 和 *主机名*。 这些是你在继续使用 Azure 数字孪生实例时可能需要的所有重要值。 如果其他用户将对该实例进行编程，则应该与它们共享这些值。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="突出显示实例的 &quot;概述&quot; 页中的重要值":::

现已准备好使用 Azure 数字孪生实例。 接下来，你将为适当的 Azure 用户授予管理权限。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

首先，在 Azure 门户中打开 Azure 数字孪生实例的页面。 从实例的菜单中，选择 " *访问控制 (IAM)*。 选择 "  **+ 添加** " 按钮以添加新的角色分配。

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="选择从 &quot;访问控制 (IAM) &quot; 页添加角色分配":::

在 " *添加角色分配* " 页上，填写值 (必须由在 Azure 订阅中具有 [足够权限](#prerequisites-permission-requirements) 的用户完成) ：
* **角色**：从下拉菜单中选择 " *Azure 数字孪生数据所有者* "
* **分配对的访问权限**：使用 *用户、组或服务主体*
* **选择**：搜索要分配的用户的名称或电子邮件地址。 选择结果后，用户将显示在 " *所选成员* " 部分中。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="将列出的字段填充到 &quot;添加角色分配&quot; 对话框中":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

输入完详细信息后，单击 " *保存* " 按钮。

### <a name="verify-success"></a>验证是否成功

你可以在 " *访问控制" (IAM) > 角色分配*"下查看已设置的角色分配。 用户应显示在列表中，其中包含 *Azure 数字孪生数据所有者* 的角色。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 门户中查看 Azure 数字孪生实例的角色分配":::

现在，你已准备好使用 Azure 数字孪生实例，并已分配了管理它的权限。

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令测试实例上的单个 REST API 调用： 
* [az dt reference](/cli/azure/ext/azure-iot/dt)
* [*操作说明：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)

或者，请参阅如何使用身份验证代码将客户端应用程序连接到实例：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)