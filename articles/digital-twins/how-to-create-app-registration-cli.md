---
title: 创建应用注册 (CLI)
titleSuffix: Azure Digital Twins
description: 了解如何使用 CLI 创建 Azure AD 应用注册作为客户端应用的身份验证选项。
author: baanders
ms.author: baanders
ms.date: 8/27/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7c9f69d33c89fba209ecf7ad76bc1aa8e2b6b666
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224890"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins-cli"></a>创建与 Azure 数字孪生配合使用的应用注册 (CLI)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

使用 Azure 数字孪生实例时，我们往往会通过客户端应用程序（例如自定义客户端应用或 [Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md) 之类的示例）与该实例进行交互。 这些应用程序需要在 Azure 数字孪生中完成身份验证才能与该实例交互，而应用可以使用的某些[身份验证机制](how-to-authenticate-client.md)涉及到 [ Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 应用注册。

并非所有身份验证方案都需要应用注册。 但是，如果使用的身份验证策略或代码示例确实需要应用注册，本文将演示如何使用 [Azure CLI](/cli/azure/what-is-azure-cli) 设置应用注册。 本文还介绍了如何[收集重要值](#collect-important-values)，以便在使用应用注册进行身份验证时使用。

## <a name="azure-ad-app-registrations"></a>Azure AD 应用注册

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 是 Microsoft 推出的基于云的标识和访问管理服务。 在 Azure AD 中设置 **应用注册** 是向客户端应用授予对 Azure 数字孪生的访问权限的一种方式。

在此应用注册中，可以配置对 [Azure 数字孪生 API](concepts-apis-sdks.md) 的访问权限。 以后，客户端应用可以使用注册的 **客户端和租户 ID 值** 对应用注册进行身份验证，从而获得已配置的、对 API 的访问权限。

>[!TIP]
> 你可能希望在每次需要应用注册时才设置新的应用注册，或者，你可能希望仅设置应用注册一次，建立单个应用注册以在需要该应用注册的所有方案之间共享它。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-manifest"></a>创建清单

首先，创建一个文件，其中包含应用注册访问 Azure 数字孪生 API 所需的特定服务信息。 稍后，将在创建应用注册时传入此文件，用于设置 Azure 数字孪生权限。

在计算机上创建名为 manifest.json 的新 .json 文件。 将以下文本复制到该文件中：

```json
[
    {
        "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
        "resourceAccess": [
            {
                "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
                "type": "Scope"
            }
        ]
    }
]
```

静态值 `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` 是 Azure 数字孪生服务终结点的资源 ID，应用注册需要此 ID 才能访问 Azure 数字孪生 API。
 
保存完成的文件。

### <a name="upload-to-cloud-shell"></a>上传到 Cloud Shell

接下来，将创建的清单文件上传到 Cloud Shell，以便在配置应用注册时可在 Cloud Shell 命令中访问该文件。

要上传该文件，请在浏览器中转到 Cloud Shell 窗口。 选择“上传/下载文件”图标，然后选择“上传”。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Azure Cloud Shell 的屏幕截图。突出显示了“上传”图标。":::

在计算机上导航到 manifest.json 文件并选择“打开”。 这样做会将文件上传到 Cloud Shell 存储的根目录。

## <a name="create-the-registration"></a>创建注册

在本部分，你将运行 Cloud Shell 命令来创建具有以下设置的应用注册：
* 首选名称
* 仅适用于默认目录（单租户）中的帐户
* `http://localhost` 的 Web 回复 URL
* 对 Azure 数字孪生 API 的读/写权限

运行以下命令，创建注册：

```azurecli-interactive
az ad app create --display-name <app-registration-name> --available-to-other-tenants false --reply-urls http://localhost --native-app --required-resource-accesses "@manifest.json"
```

命令的输出是已创建的应用注册的相关信息。 

## <a name="verify-success"></a>验证是否成功

通过在 `az ad app create` 命令的输出中查找以下字段并确认其值与以下屏幕截图中显示的值匹配，可以确认是否授予了 Azure 数字孪生权限：

:::image type="content" source="media/how-to-create-app-registration/cli-required-resource-access.png" alt-text="应用注册创建命令的 Cloud Shell 输出的屏幕截图。突出显示了“requiredResourceAccess”下的项：“resourceAppId”值为 0b07f429-9f4b-4714-9392-cc5e8e80c8b0；“resourceAccess > id”值为 4589bd03-58cb-4e6c-b17f-b580e39652f8。":::

此外，还可使用 Azure 门户验证应用注册是否创建成功。 有关门户说明，请参阅[验证是否成功（门户）](how-to-create-app-registration-portal.md#verify-success)。

## <a name="collect-important-values"></a>收集重要值

接下来，收集有关应用注册的一些重要值，在使用应用注册对客户端应用程序进行身份验证时，你将需要使用这些值。 这些值包括：
* 资源名称
* 客户端 ID
* 租户 ID
* 客户端密码

若要使用 Azure 数字孪生，则资源名称为 `http://digitaltwins.azure.net`。

以下部分介绍如何查找其他值。

### <a name="collect-client-id-and-tenant-id"></a>收集客户端 ID 和租户 ID

若要使用应用注册进行身份验证，可能需要提供其应用程序（客户端）ID 和目录（租户）ID 。 在本部分中，你将收集这些值，以便可以将其保存并根据需要随时使用它们。

在 `az ad app create` 命令的输出中，可找到这两个值。

应用程序（客户端）ID：

:::image type="content" source="media/how-to-create-app-registration/cli-app-id.png" alt-text="应用注册创建命令的 Cloud Shell 输出的屏幕截图。突出显示了 appId 值。":::

目录（租户）ID：

:::image type="content" source="media/how-to-create-app-registration/cli-tenant-id.png" alt-text="应用注册创建命令的 Cloud Shell 输出的屏幕截图。突出显示了 odata.metadata 中的 GUID 值。":::

### <a name="collect-client-secret"></a>收集客户端密码

若要为应用注册创建客户端密码，需要使用上一部分中应用注册的客户端 ID 值。 使用以下 CLI 命令中的值创建新密码：

```azurecli-interactive
az ad app credential reset --id <client-ID> --append
```

还可以向此命令添加可选参数，以指定凭据说明、结束日期和其他详细信息。 有关命令及其参数的详细信息，请参阅 [az ad app credential reset 文档](/cli/azure/ad/app/credential?view=azure-cli-latest&preserve-view=true#az_ad_app_credential_reset)。

此命令的输出是已创建的客户端密码的相关信息。 复制 `password` 的值，以便在需要客户端密码进行身份验证时使用。

:::image type="content" source="media/how-to-create-app-registration/cli-client-secret.png" alt-text="应用注册创建命令的 Cloud Shell 输出的屏幕截图。突出显示了密码值。":::

>[!IMPORTANT]
>请确保立即复制该值并将其存储在安全的位置，因为无法再次检索该值。 如果以后找不到该值，则必须创建新的密码。

## <a name="other-possible-steps-for-your-organization"></a>适用于你的组织的其他可行步骤

你的组织可能需要订阅所有者/管理员执行的更多操作才能成功设置应用注册。 根据组织的特定设置，所需的步骤可能会有所不同。

下面是订阅所有者或管理员可能需要进行的一些常见潜在活动。
* 授予管理员同意进行应用注册。 你的组织可能已在 Azure AD 中对你的订阅中的所有应用注册全局启用了“需要管理员同意”。 如果已启用，所有者/管理员可能需要授予其他委托权限或应用程序权限。
* 通过将 `--set publicClient=true` 追加到 create 或 update 命令，为注册激活公共客户端访问。
* 使用 `--reply-urls` 参数设置特定的回复 URL 用于进行 Web 和桌面访问。 要详细了解如何将此参数与 `az ad` 命令结合使用，请参阅 [az ad app 文档](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true)。
* 使用 `--oauth2-allow-implicit-flow` 参数允许隐式 OAuth2 身份验证流。 要详细了解如何将此参数与 `az ad` 命令结合使用，请参阅 [az ad app 文档](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true)。

有关应用注册及其不同设置选项的详细信息，请参阅将应用程序注册到 Microsoft 标识平台。

## <a name="next-steps"></a>后续步骤

在本文中，你设置了一个可用于通过 Azure 数字孪生 API 对客户端应用程序进行身份验证的 Azure AD 应用注册。

接下来请了解身份验证机制，包括一个使用应用注册的机制，以及其他不使用应用注册的机制：
* [编写应用身份验证码](how-to-authenticate-client.md)