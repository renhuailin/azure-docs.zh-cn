---
title: 设置实例和身份验证 (CLI)
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 CLI 设置 Azure 数字孪生服务的实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8e82f8974b53224b3e471d1628a1ca5819ce2955
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044469"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>设置 Azure 数字孪生实例和 (CLI 的身份验证) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍 **设置新的 Azure 数字孪生实例** 的步骤，包括创建实例和设置身份验证。 完成本文后，你将拥有一个可开始对进行编程的 Azure 数字孪生实例。

本文的此版本通过使用 CLI 逐个手动完成这些步骤。
* 若要使用 Azure 门户手动完成这些步骤，请参阅本文的门户版本： [*如何：设置实例和身份验证 (门户)*](how-to-set-up-instance-portal.md)。
* 若要通过使用部署脚本示例的自动安装运行，请参阅本文的脚本编写版本： how [*to： Set a instance and authentication (script)*](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分中，将使用 Cloud Shell 命令 **创建 Azure 数字孪生的新实例** 。 需要提供：
* 将在其中部署实例的资源组。 如果尚未记住现有的资源组，可以使用以下命令创建一个资源组：
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 部署的区域。 若要查看哪些区域支持 Azure 数字孪生，请访问 [*按区域提供的 azure 产品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* 实例的名称。 如果你的订阅在已使用指定名称的区域中有另一个 Azure 数字孪生实例，则系统将要求你选择其他名称。

在以下命令中使用这些值来创建实例：

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>验证成功并收集重要值

如果已成功创建实例，Cloud Shell 中的结果如下所示，输出有关已创建资源的信息：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="成功创建资源组和 Azure 数字孪生实例命令窗口":::

请注意输出中的 Azure 数字孪生实例的 **主机名**、 **名称** 和 **资源** 组。 这些是你在继续使用 Azure 数字孪生实例时可能需要的所有重要值，用于设置身份验证和相关的 Azure 资源。 如果其他用户将对该实例进行编程，则应该与它们共享这些值。

> [!TIP]
> 你可以随时通过运行来查看这些属性和实例的所有属性 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

现已准备好使用 Azure 数字孪生实例。 接下来，你将为适当的 Azure 用户授予管理权限。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

使用以下命令分配角色 (必须由在 Azure 订阅) 中具有 [足够权限](#prerequisites-permission-requirements) 的用户运行。 命令要求用户在应分配角色的用户的 Azure AD 帐户上传递 *用户主体名称* 。 在大多数情况下，这将与 Azure AD 帐户上的用户电子邮件匹配。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

此命令的结果将输出有关已创建的角色分配的信息。

> [!NOTE]
> 如果此命令返回一个错误，指出 CLI **在 graph 数据库中找不到用户或服务主体**：
>
> 改为使用用户的 *对象 ID* 分配角色。 对于个人 Microsoft 帐户 (的用户，可能会发生这种情况， [msa) ](https://account.microsoft.com/account)。 
>
> 使用 [Azure Active Directory 用户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 的 "Azure 门户" 页选择用户帐户并打开其详细信息。 复制用户的 *ObjectID*：
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Azure 门户突出显示 &quot;对象 ID&quot; 字段中的 GUID 的用户页面视图" lightbox="media/includes/user-id.png":::
>
> 然后，使用上面参数的用户 *对象 ID* 重复角色分配列表命令 `assignee` 。

### <a name="verify-success"></a>验证是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

现在，你已准备好使用 Azure 数字孪生实例，并已分配了管理它的权限。

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令测试实例上的单个 REST API 调用： 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*操作说明：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)

或者，请参阅如何使用身份验证代码将客户端应用程序连接到实例：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)