---
title: 设置实例和身份验证（门户）
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 门户设置 Azure 数字孪生服务实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8263e0805f48976222e66922be8c04e0902101d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201832"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>设置 Azure 数字孪生实例和身份验证（门户）。

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文将介绍新 Azure 数字孪生实例的设置步骤，包括创建实例和设置身份验证。 完成本文操作后，可准备对 Azure 数字孪生实例开始编程。

本文此版本借助 Azure 门户逐个手动完成这些步骤。 Azure 门户是基于 Web 的统一控制台，提供可替代命令行工具的方法。
* 若要使用 CLI 手动完成这些步骤，请参阅本文的 CLI 版本：[操作说明：设置实例和身份验证 (CLI)](how-to-set-up-instance-cli.md)。
* 若要使用部署脚本示例完成自动安装，请参阅本文的脚本编写版本：[操作说明：设置实例和身份验证（已编写脚本）](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分中，将使用 [Azure 门户](https://ms.portal.azure.com/)创建新的 Azure 数字孪生实例。 导航到门户，并使用凭据登录。

登录门户后，首先在 Azure 服务的主页菜单中选择“创建资源”。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="在 Azure 门户的主页中，选择“创建资源”":::

在搜索框中搜索“Azure 数字孪生”，并从结果中选择“Azure 数字孪生”服务。 选择“创建”按钮，创建新的服务实例。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="在“Azure 数字孪生”服务页中选择“创建”":::

在以下“创建资源”页中，填写以下给定值：
* **订阅**：要使用的 Azure 订阅
  - **资源组**：要在其中部署实例的资源组。 如果尚未记住现有的资源组，可通过选择“新建”链接并输入新资源组的名称来创建资源组。
* **位置**：用于部署且已启用 Azure 数字孪生的区域。 有关区域支持的更多详细信息，请访问[各区域的 Azure 产品可用性（Azure 数字孪生）](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* **资源名称**： Azure 数字孪生实例的名称。 如果订阅在区域中已存在使用指定名称的其他 Azure 数字孪生实例，则系统将要求选择其他名称。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="填写所述值以创建 Azure 数字孪生资源":::

完成后，如果不想为实例配置更多设置，可以选择“查看 + 创建”。 将会转到“摘要”页，可以在其中查看已输入的实例详细信息并单击“创建”完成此步骤。 

如果想要为实例配置更多详细信息，下一部分将介绍其余的设置选项卡。

### <a name="additional-setup-options"></a>其他设置选项

下面是设置期间可以配置的其他选项，在“创建资源”过程中使用其他选项卡可以完成这些配置。

* **网络**：在此选项卡中，可以使用“[Azure 专用链接](../private-link/private-link-overview.md)”启用专用终结点，以消除实例暴露于公共网络的风险。 有关说明，请参阅[操作说明：使用专用链接启用专用访问（预览）](./how-to-enable-private-link-portal.md#add-a-private-endpoint-during-instance-creation)。
* **高级**：在此选项卡中，可以为实例启用“[系统托管的标识](../active-directory/managed-identities-azure-resources/overview.md)”，以便用于将事件转发到“[终结点](concepts-route-events.md)”。 有关说明，请参阅[操作说明：为路由事件启用托管标识（预览）](./how-to-enable-managed-identities-portal.md#add-a-system-managed-identity-during-instance-creation)。
* **标记**：在此选项卡中，可以将标记添加到实例，以便组织 Azure 资源。 有关 Azure 资源标记的详细信息，请参阅[标记资源、资源组和订阅以合理进行组织](../azure-resource-manager/management/tag-resources.md)。

### <a name="verify-success-and-collect-important-values"></a>验证是否成功并收集重要值

在选择“创建”完成实例设置后，可以在门户图标栏上的 Azure 通知中查看实例的部署状态。 通知将指示成功部署的时间，并且可以选择“转到资源”按钮来查看创建的实例。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Azure 通知视图，显示成功部署并突出显示“转到资源”按钮":::

或者，如果部署失败，通知将指出失败原因。 观察错误消息中的建议，然后重新尝试创建实例。

>[!TIP]
>创建实例后，可以通过在 Azure 门户搜索栏中搜索实例的名称，随时返回到此页。

在实例的“概述”页中，记下其“名称”、“资源组”和“主机名”。 在继续使用 Azure 数字孪生实例时，需要使用所有这些重要的值。 如果其他用户将对该实例进行编程，则应与他们共享这些值。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="实例“概述”页突出显示这些重要的值":::

现已准备好 Azure 数字孪生实例。 接下来，将授予相应的 Azure 用户权限对其进行管理。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

首先，在 Azure 门户中打开 Azure 数字孪生实例的页面。 从实例的菜单中，选择“访问控制 (IAM) ”。 选择“+ 添加”按钮以添加一个新的角色分配。

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="从“访问控制 (IAM)”页选择以添加角色分配":::

在以下“添加角色分配”页中，填写值（必须由在 Azure 订阅中具有[足够权限](#prerequisites-permission-requirements)的用户完成）：
* **角色**：从下拉列表中选择“Azure 数字孪生数据所有者”
* **分配访问权限至**：使用“用户、组或服务主体”
* **选择**：搜索要分配的用户的姓名或电子邮件地址。 选择结果后，用户将显示在“所选成员”部分中。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="将所列字段填充到“添加角色分配”对话框中":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

完成详细信息输入后，单击“保存”按钮。

### <a name="verify-success"></a>验证是否成功

可以在“访问控制 (IAM) > 角色分配”下查看已设置的角色分配。 用户应显示在列表中，其角色显示为“Azure 数字孪生数据所有者”。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure 门户中 Azure 数字孪生实例的角色分配视图":::

现在，已准备好 Azure 数字孪生实例，并分配了管理权限。

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令测试单个 REST API 对实例的调用： 
* [az dt reference](/cli/azure/ext/azure-iot/dt)
* [*操作说明：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)

或者，请参阅如何使用验证码将客户端应用程序连接到实例：
* [如何：编写应用验证码](how-to-authenticate-client.md)