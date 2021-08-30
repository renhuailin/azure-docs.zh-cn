---
title: 设置实例和身份验证 (CLI)
titleSuffix: Azure Digital Twins
description: 了解如何使用 CLI 设置 Azure 数字孪生服务实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6f64791df0c564183900fd75c718ac4e5f9d0976
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468837"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>设置 Azure 数字孪生实例和身份验证 (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍新 Azure 数字孪生实例的设置步骤，包括创建实例和设置身份验证。 完成本文操作后，即可对 Azure 数字孪生实例编程。

本文的此版本借助 CLI 逐个手动完成这些步骤。
* 若要使用 Azure 门户手动完成这些步骤，请参阅本文的门户版本：[设置实例和身份验证（门户）](how-to-set-up-instance-portal.md)。
* 若要使用部署脚本示例完成自动设置，请参阅本文的脚本编写版本：[设置实例和身份验证（已编写脚本）](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分，你将使用 Cloud Shell 命令 **创建新的 Azure 数字孪生实例**。 你需要提供：
* 将在其中部署实例的资源组。 如果你没有记住现有的资源组，可以使用以下命令创建一个资源组：
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 用于部署的区域。 若要查看哪些区域支持 Azure 数字孪生，请访问各区域的 Azure 产品可用性。
* 你的实例的名称。 如果订阅在该区域已存在使用指定名称的其他 Azure 数字孪生实例，系统会要求你选择其他的名称。

在以下 [az dt 命令](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)中使用这些值创建实例：

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> --resource-group <your-resource-group> --location <region>
```

### <a name="verify-success-and-collect-important-values"></a>验证是否成功并收集重要值

如果实例已成功创建，则 Cloud Shell 中的结果将如下所示，输出有关已创建资源的信息：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Cloud Shell 窗口屏幕截图，其中显示了在 Azure 门户中成功创建资源组和 Azure 数字孪生实例。":::

记下输出中 Azure 数字孪生实例的“hostName”、“name”和“resourceGroup”。 在继续使用 Azure 数字孪生实例时，可能需要使用所有这些重要的值，以便设置身份验证和相关的 Azure 资源。 如果其他用户将针对该实例进行编程，则应与他们共享这些值。

> [!TIP]
> 你可以随时通过运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 来查看这些属性和你的实例的所有属性。

你现在已准备好 Azure 数字孪生实例。 接下来，你将向适当的 Azure 用户授予权限来管理该实例。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>先决条件：权限要求

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>分配角色

若要授予用户管理 Azure 数字孪生实例的权限，必须在实例中为他们分配“Azure 数字孪生数据所有者”角色。

使用以下命令来分配角色（必须由在 Azure 订阅中具有[足够权限](#prerequisites-permission-requirements)的用户来运行）。 此命令要求你传入要为其分配角色的用户在 Azure AD 帐户中的用户主体名称。 在大多数情况下，这将与该用户在 Azure AD 帐户中的电子邮件匹配。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

此命令的结果是有关已创建的角色分配的输出信息。

> [!NOTE]
> 如果此命令返回一个错误，指出 CLI **在 graph 数据库中找不到用户或服务主体**：
>
> 请改为使用用户的对象 ID 来分配角色。 对于使用个人 [Microsoft 帐户 (MSA)](https://account.microsoft.com/account) 的用户，可能会发生这种情况。 
>
> 使用 [Azure Active Directory 用户的 Azure 门户页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)选择用户帐户并打开其详细信息。 复制用户的 ObjectID：
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Azure 门户中的用户页面屏幕截图，其中突出显示了“对象 ID”字段中的 GUID。" lightbox="media/includes/user-id.png":::
>
> 然后，重复执行角色分配列表命令，将用户的对象 ID 用于上面的 `assignee` 参数。

### <a name="verify-success"></a>验证是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

现在，你已准备好 Azure 数字孪生实例，并分配了管理权限。

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令在实例上测试各个 REST API 调用： 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Azure 数字孪生 CLI 命令集](concepts-cli.md)

或者，了解如何使用验证码将客户端应用程序连接到实例：
* [编写应用身份验证码](how-to-authenticate-client.md)