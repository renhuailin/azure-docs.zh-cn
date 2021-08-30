---
title: 管理 AuthN/AuthZ API 版本
description: 根据需要，将应用服务身份验证 API 升级到 V2 或将其固定到特定版本。
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e526f4a56806fa41d8955337d7e8463afac80b21
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094929"
---
# <a name="manage-the-api-and-runtime-versions-of-app-service-authentication"></a>管理应用服务身份验证的 API 和运行时版本

本文介绍如何自定义[应用服务中的内置身份验证和授权](overview-authentication-authorization.md)的 API 和运行时版本。

用于应用服务身份验证的管理 API 有两个版本。 V2 版本是 Azure 门户“身份验证”体验的必备项。 已在使用 V1 API 的应用可在进行一些更改后升级到 V2 版本。 具体而言，必须将机密配置移至槽粘滞应用程序设置。 这可以从应用门户的“身份验证”部分自动完成。

## <a name="update-the-configuration-version"></a>更新配置版本

> [!WARNING]
> 迁移到 V2 后，将禁止通过某些客户端（例如 Azure 门户、Azure CLI 和 Azure PowerShell 中的现有体验）管理应用程序的应用服务身份验证/授权功能。 此更改无法撤消。

V2 API 不支持像 V1 中那样将 Microsoft 帐户作为不同的提供程序进行创建和编辑。 而是利用聚合的 [Microsoft 标识平台](../active-directory/develop/v2-overview.md)，让用户可以同时通过 Azure AD 和个人 Microsoft 帐户登录。 切换到 V2 API 时，V1 Azure Active Directory 配置将用于配置 Microsoft 标识平台提供程序。 迁移过程中将传递 V1 Microsoft 帐户提供程序，且该程序依然能正常运行，但建议你迁移到更新的 Microsoft 标识平台模型。 有关详细信息，请参阅[对于 Microsoft 帐户提供程序注册的支持](#support-for-microsoft-account-provider-registrations)。

自动迁移过程会将提供程序机密移动到应用程序设置中，然后将其余配置转换为新格式。 若要使用自动迁移，请执行以下操作：

1. 在门户中导航到应用，然后选择“身份验证”菜单选项。
1. 如果应用是使用 V1 模型配置的，则会看到一个升级按钮。
1. 查看确认提示中的说明。 如果已准备好执行迁移，请点击提示中的“升级”。

### <a name="manually-managing-the-migration"></a>手动管理迁移

如果不希望使用上述自动版本，可通过以下步骤手动将应用程序迁移到 V2 API。

#### <a name="moving-secrets-to-application-settings"></a>将机密移动到应用程序设置

1. 使用 V1 API 获取现有配置：

   ```azurecli
   az webapp auth show -g <group_name> -n <site_name>
   ```

   在生成的 JSON 有效负载中，记下已配置的每个提供程序所用的机密值：

   * AAD：`clientSecret`
   * Google：`googleClientSecret`
   * Facebook：`facebookAppSecret`
   * Twitter：`twitterConsumerSecret`
   * Microsoft 帐户：`microsoftAccountClientSecret`

   > [!IMPORTANT]
   > 机密值是重要的安全凭据，应谨慎处理。 请勿共享这些值或将其保留在本地计算机。

1. 应为每个机密值创建槽粘滞应用程序设置。 可以选择每个应用程序设置的名称。 其值应与上一步中获取的值匹配，也可能[引用使用该值创建的密钥保管库机密](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json)。

   若要创建此设置，可使用 Azure 门户或对每个提供程序运行以下命令（应进行相应更改）：

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > 此配置的应用程序设置应标记为槽粘滞，这意味着它们不会在[槽交换操作](./deploy-staging-slots.md)期间在各环境之间移动。 这是因为身份验证配置本身与环境相关联。 

1. 创建名为 `authsettings.json` 的新 JSON 文件。采用之前接收的输出，并删除其中的每个机密值。 将其余的输出写入该文件，确保不包含任何机密。 在某些情况下，配置中可能有包含空字符串的数组。 请确保 `microsoftAccountOAuthScopes` 不包含这类数组，如有，请将值切换为 `null`。

1. 向 `authsettings.json` 添加一个属性，该属性指向之前为每个提供程序创建的应用程序设置名称：
 
   * AAD：`clientSecretSettingName`
   * Google：`googleClientSecretSettingName`
   * Facebook：`facebookAppSecretSettingName`
   * Twitter：`twitterConsumerSecretSettingName`
   * Microsoft 帐户：`microsoftAccountClientSecretSettingName`

   完成这一操作后的示例文件可能类似于以下内容，本例中仅对 AAD 进行了配置：

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. 提交此文件作为应用的新身份验证/授权配置：

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. 验证应用程序在此操作后是否仍按预期运行。

1. 删除前面的步骤中使用的文件。

现在，你已迁移应用，以将标识提供者机密存储为应用程序设置。

#### <a name="support-for-microsoft-account-provider-registrations"></a>对于 Microsoft 帐户提供程序注册的支持

如果现有配置包含 Microsoft 帐户提供程序而不包含 Azure Active Directory 提供程序，则可以将配置切换到 Azure Active Directory 提供程序，然后执行迁移。 要执行此操作：

1. 转到 Azure 门户中的[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)，并找到与你的 Microsoft 帐户提供程序关联的注册。 该注册可能位于“来自个人帐户的应用程序”标题下。
1. 导航到该注册的“身份验证”页。 在“重定向 URI”下，你应会看到以 `/.auth/login/microsoftaccount/callback` 结尾的条目。 复制此 URI。
1. 添加一个与刚刚复制的 URI 匹配的新 URI，但要将其结尾改为 `/.auth/login/aad/callback`。 这样，应用服务身份验证/授权配置便可以使用注册。
1. 导航到应用的应用服务身份验证/授权配置。
1. 收集 Microsoft 帐户提供程序的配置。
1. 使用“高级”管理模式配置 Azure Active Directory 提供程序，同时提供你在上一步中收集的客户端 ID 和客户端密码值。 对于证书颁发者 URL，请使用 `<authentication-endpoint>/<tenant-id>/v2.0`，然后将 \<authentication-endpoint> 替换为[云环境的身份验证终结点](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints)（例如全球 Azure 的“https://login.microsoftonline.com”），同时将 \<tenant-id> 替换为你的 Directory（租户）ID 。
1. 保存配置后，请在浏览器中导航到站点上的 `/.auth/login/aad` 终结点，以测试登录流，然后完成登录流。
1. 至此，你已成功复制配置，但现有 Microsoft 帐户提供程序配置仍然存在。 在将其删除之前，请确保应用程序的所有部分都通过登录链接等引用 Azure Active Directory 提供程序。验证应用的所有部分是否按预期工作。
1. 验证 AAD Azure Active Directory 提供程序能够正常运作后，即可删除 Microsoft 帐户提供程序配置。

> [!WARNING]
> 可通过修改 AAD 应用注册[支持的帐户类型](../active-directory/develop/supported-accounts-validation.md)来聚合两个注册。 但这样会强制向 Microsoft 帐户用户显示新的同意提示，并且这些用户的标识声明在结构上可能不同，同时由于正在使用新的应用 ID，`sub` 值会发生明显变化。 除非完全理解此方法，否则不建议使用该方法。 应等待 V2 API 表面提供对两个注册的支持。

#### <a name="switching-to-v2"></a>切换到 V2

执行上述步骤后，请在 Azure 门户中导航到应用。 选择“身份验证(预览)”部分。 

或者，也可以对站点资源下的 `config/authsettingsv2` 资源发出 PUT 请求。 有效负载的架构与[基于文件的配置](configure-authentication-file-based.md)部分所介绍的内容相同。

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>将应用固定到特定身份验证运行时版本

启用身份验证/授权时，会将平台中间件注入 HTTP 请求管道，如[功能概述](overview-authentication-authorization.md#how-it-works)中所述。 作为平台例常更新的一部分，此平台中间件定期更新新功能和改进。 默认情况下，Web 或函数应用在此平台中间件的最新版本上运行。 这些自动更新始终向后兼容。 但在极少情况下此自动更新引入 Web 或函数应用的运行时问题，此时可以暂时回滚到以前的中间件版本。 本文介绍如何将应用临时固定到特定版本的身份验证中间件。

### <a name="automatic-and-manual-version-updates"></a>自动和手动版本更新 

可以通过设置应用的 `runtimeVersion` 设置，将应用固定到平台中间件的特定版本。 应用始终在最新版本上运行，除非选择将其显式固定回特定版本。 一次支持几个版本。 如果固定到不再受支持的无效版本，应用将改用最新版本。 若要始终运行最新版本，将 `runtimeVersion` 设为 ~1。 

### <a name="view-and-update-the-current-runtime-version"></a>查看和更新当前运行时版本

可以更改应用使用的运行时版本。 新的运行时版本应在重启应用后生效。 

#### <a name="view-the-current-runtime-version"></a>查看当前运行时版本

可以使用 Azure CLI 或应用中的内置版本 HTTP 终结点之一来查看平台身份验证中间件的当前版本。

##### <a name="from-the-azure-cli"></a>通过 Azure CLI

使用 Azure CLI 通过 [az webapp auth show](/cli/azure/webapp/auth#az_webapp_auth_show) 命令查看当前中间件版本。

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

在此代码中，用应用名称替换 `<my_app_name>`。 还使用应用的资源组名称替换 `<my_resource_group>`。

将在 CLI 输出中看到 `runtimeVersion` 字段。 它类似于以下示例输出，为了清晰起见，该输出已被截断： 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>使用版本终结点

还可点击应用上的 /.auth/version 终结点来查看应用运行所在的当前中间件版本。 它类似于以下示例输出：
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>更新当前运行时版本

使用 Azure CLI，可以通过 [az webapp auth update](/cli/azure/webapp/auth#az_webapp_auth_update) 命令更新应用中的 `runtimeVersion` 设置。

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

将 `<my_app_name>` 替换为你的应用的名称。 还使用应用的资源组名称替换 `<my_resource_group>`。 另外，将 `<version>` 替换为 1.x 运行时的有效版本，或替换为 `~1` 获取最新版本。 请参阅[不同运行时版本的发行说明](https://github.com/Azure/app-service-announcements)，以便确定要固定到的版本。

可以通过在前面代码示例中选择“试一试”运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](/cli/azure/reference-index#az_login) 登录后使用 [Azure CLI 在本地](/cli/azure/install-azure-cli)执行此命令。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：对用户进行端到端身份验证和授权](tutorial-auth-aad.md)
