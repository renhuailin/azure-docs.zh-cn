---
title: 自托管开发人员门户
titleSuffix: Azure API Management
description: 了解如何自托管 API Management 开发人员门户。
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: f3e0004115b417c85914c7cc40fd98acf3c63d73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592634"
---
# <a name="self-host-the-api-management-developer-portal"></a>自托管 API Management 开发人员门户

本教程介绍如何自托管 [API Management 开发人员门户](api-management-howto-developer-portal.md)。 借助自托管，可以使用自定义逻辑和小组件在运行时上动态自定义页面来灵活扩展开发人员门户。 可为 API Management 实例自托管具有不同功能的多个门户。 自托管门户时，你将成为门户的维护人员并负责其升级。 

以下步骤演示如何设置本地开发环境，在开发人员门户中执行更改，并将其发布和部署到 Azure 存储帐户。

如果你已在托管门户中上传或修改了媒体文件，请参阅本文后面的[从托管迁移到自托管](#move-from-managed-to-self-hosted-developer-portal)部分。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>先决条件

若要设置本地开发环境，需要具有：

- API Management 服务实例。 如果没有，请参阅[快速入门 - 创建 Azure API Management 实例](get-started-create-service-instance.md)。
- 已启用[静态网站功能](../storage/blobs/storage-blob-static-website.md)的 Azure 存储帐户。 请参阅[创建存储帐户](../storage/common/storage-account-create.md)。
- 计算机上安装有 Git。 请按照[此 Git 教程](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)安装。
- 计算机上安装有 Node.js（LTS `v10.15.0` 或更高版本）和 npm。 请参阅[下载并安装 Node.js 和 npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)。
- Azure CLI。 请按照 [Azure CLI 安装步骤](/cli/azure/install-azure-cli-windows)操作。

## <a name="step-1-set-up-local-environment"></a>步骤 1：设置本地环境

若要设置本地环境，必须克隆存储库，切换到最新版本的开发人员门户，并安装 npm 包。

1. 从 GitHub 克隆 [api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) 存储库：

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. 转到存储库的本地副本：

    ```console
    cd api-management-developer-portal
    ```

1. 查看门户是否为最新版本。

    运行以下代码之前，在[存储库的版本部分](https://github.com/Azure/api-management-developer-portal/releases)中检查当前版本标记，并将 `<current-release-tag>` 值替换为最新版本标记。
    
    ```console
    git checkout <current-release-tag>
    ```

1. 安装任何可用的 npm 包：

    ```console
    npm install
    ```

> [!TIP]
> 始终使用[最新的门户版本](https://github.com/Azure/api-management-developer-portal/releases)，并使分支门户保持最新。 出于日常开发目的，软件工程师使用此存储库的 `master` 分支。 该分支具有不稳定的软件版本。

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>步骤 2：配置 JSON 文件、静态网站和 CORS 设置

开发人员门户需要使用 API Management 的 REST API 来管理内容。

### <a name="configdesignjson-file"></a>config.design.json 文件

转到 `src` 文件夹，然后打开 `config.design.json` 文件。

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

配置该文件：

1. 在 `managementApiUrl` 值中，将 `<service-name>` 替换为 API Management 实例的名称。 如果已配置[自定义域](configure-custom-domain.md)，请改用它（例如 `https://management.contoso.com`）。

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [手动创建一个 SAS 令牌](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken)，以启用对 API Management 实例的直接 REST API 访问。

1. 复制生成的令牌，并将其粘贴为 `managementApiAccessToken` 值。

1. 在 `backendUrl` 值中，将 `<service-name>` 替换为 API Management 实例的名称。 如果已配置[自定义域](configure-custom-domain.md)，请改用它（例如 `https://portal.contoso.com`）。

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. 若要在开发人员门户中启用 CAPTCHA，请参阅[启用 CAPTCHA](#enable-captcha)。

### <a name="configpublishjson-file"></a>config.publish.json 文件

转到 `src` 文件夹，然后打开 `config.publish.json` 文件。

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

配置该文件：

1. 复制并粘贴以前的配置文件中的 `managementApiUrl` 和 `managementApiAccessToken` 值。

1. 若要在开发人员门户中启用 CAPTCHA，请参阅[启用 CAPTCHA](#enable-captcha)。

### <a name="configruntimejson-file"></a>config.runtime.json 文件

转到 `src` 文件夹，然后打开 `config.runtime.json` 文件。

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

配置该文件：

1. 复制并粘贴以前的配置文件中的 `managementApiUrl` 值。

1. 在 `backendUrl` 值中，将 `<service-name>` 替换为 API Management 实例的名称。 如果已配置[自定义域](configure-custom-domain.md)，请改用它（例如 `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>配置静态网站

通过向索引和错误页提供路由，在存储帐户中配置“静态网站”功能：

1. 在 Azure 门户中转到存储帐户，然后从左侧菜单中选择“静态网站”。

1. 在“静态网站”页上，选择“启用” 。

1. 在“索引文档名称”字段中，输入 index.html。

1. 在“错误文档路径”字段中，输入 404/index.html。

1. 选择“保存”。

### <a name="configure-the-cors-settings"></a>配置 CORS 设置

配置跨源资源共享 (CORS) 设置：

1. 在 Azure 门户中转到存储帐户，然后从左侧菜单中选择“CORS”。

1. 在“BLOB 服务”选项卡中，配置以下规则：

    | 规则 | “值” |
    | ---- | ----- |
    | 允许的域 | * |
    | 允许的方法 | 选择所有 Http 谓词。 |
    | 允许的标题 | * |
    | 公开的标题 | * |
    | 最长时间 | 0 |

1. 选择“保存”。

## <a name="step-3-run-the-portal"></a>步骤 3：运行门户

现在，你可在开发模式下生成并运行本地门户实例。 在开发模式下，所有优化都处于禁用状态，源映射处于启用状态。

运行以下命令：

```console
npm start
```

一小段时间后，默认浏览器将自动打开你的本地开发人员门户实例。 默认地址为 `http://localhost:8080`，但如果 `8080` 已被占用，端口可能会更改。 对项目的代码库所做的任何更改都会触发重新生成和刷新浏览器窗口。

## <a name="step-4-edit-through-the-visual-editor"></a>步骤 4：通过可视化编辑器进行编辑

使用可视化编辑器执行以下任务：

- 自定义门户
- 创作内容
- 组织网站结构
- 设置外观样式

请参阅[教程：访问和自定义开发人员门户](api-management-howto-developer-portal-customize.md)。 该教程介绍管理用户界面的基本知识，并列出对默认内容的建议更改。 保存本地环境中的所有更改，然后按 Ctrl+C 将其关闭。

## <a name="step-5-publish-locally"></a>步骤 5：在本地发布

门户数据的最初形式为强类型化对象。 可使用以下命令将它们转换为静态文件，并将输出放入 `./dist/website` 目录中：

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>步骤 6：将静态文件上传到 Blob

使用 Azure CLI 将本地生成的静态文件上传到 Blob，确保访问者可以访问它们：

1. 打开 Windows 命令提示符、PowerShell 或其他命令行界面。

1. 运行以下 Azure CLI 命令。
   
    将 `<account-connection-string>` 替换为存储帐户的连接字符串。 可从存储帐户的“访问密钥”部分获取该字符串。

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>步骤 7：转到你的网站

你的网站现在处于 Azure 存储属性中指定的主机名下（“静态网站”中的“主终结点”） 。

## <a name="step-8-change-api-management-notification-templates"></a>步骤 8：更改 API Management 通知模板

替换 API Management 通知模板中的开发人员门户 URL，使其指向你的自托管门户。 请参阅[如何在 Azure API Management 中配置通知和电子邮件模板](api-management-howto-configure-notifications.md)。

具体而言，请对默认模板执行以下更改：

> [!NOTE]
> 下面的“已更新”部分中的值假定你要在 https:\//portal.contoso.com/ 托管门户 。 

### <a name="email-change-confirmation"></a>电子邮件更改确认

更新“电子邮件更改确认”通知模板中的开发人员门户 URL：

**原始内容**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**已更新**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>新的开发人员帐户确认

更新“新的开发人员帐户确认”通知模板中的开发人员门户 URL：

**原始内容**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**已更新**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>邀请用户

更新“邀请用户”通知模板中的开发人员门户 URL：

**原始内容**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**已更新**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>激活的新订阅

更新“激活的新订阅”通知模板中的开发人员门户 URL：

**原始内容**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**已更新**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**原始内容**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**已更新**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**原始内容**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**已更新**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**原始内容**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**已更新**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>密码更改确认

更新“密码更改确认”通知模板中的开发人员门户 URL：

**原始内容**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**已更新**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>所有模板

在页脚中包含链接的所有模板中更新开发人员门户 URL：

**原始内容**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**已更新**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>从托管迁移到自托管开发人员门户

随着时间的推移，业务需求可能会改变。 最终可能会发现 API Management 开发人员门户的托管版本不再满足你的需求。 例如，新的需求可能会迫使你生成与第三方数据提供程序集成的自定义小组件。 与托管版本不同，自托管版本的门户提供完全的灵活性和可扩展性。

### <a name="transition-process"></a>转换过程

你可以在同一 API Management 服务实例中从托管版本转换为自托管版本。 此过程将保留你在门户的托管版本中执行的修改。 请确保事先备份门户内容。 可在 API Management 开发人员门户 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal)的 `scripts` 文件夹中找到备份脚本。

转换过程与设置通用自托管门户几乎完全相同，如本文前面的步骤所示。 配置步骤中有一个例外。 `config.design.json` 文件中的存储帐户需要与门户的托管版本的存储帐户相同。 有关如何检索 SAS URL 的说明，请参阅[教程：使用 Linux VM 系统分配的标识通过 SAS 凭据访问 Azure 存储](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls)。

> [!TIP]
> 建议在 `config.publish.json` 文件中使用单独的存储帐户。 使用此方法，可以更好地控制和简化门户托管服务的管理。

## <a name="enable-captcha"></a>启用 CAPTCHA

设置自托管门户时，可能已通过 `useHipCaptcha` 设置禁用了 CAPTCHA。 与 CAPTCHA 的通信通过终结点进行，这使跨域资源共享 (CORS) 仅适用于托管开发人员门户主机名。 如果开发人员门户是自托管门户，则它使用不同的主机名，并且 CAPTCHA 不允许进行通信。

### <a name="update-the-json-config-files"></a>更新 JSON 配置文件

在自托管门户中启用 CAPTCHA：

1. 向 API Management 服务的“开发人员门户”终结点分配自定义域（例如 `api.contoso.com`）。

    此域适用于门户的托管版本和 CAPTCHA 终结点。 有关步骤，请参阅[为 Azure API Management 实例配置自定义域名](configure-custom-domain.md)。

1. 转到自托管门户的[本地环境](#step-1-set-up-local-environment)中的 `src` 文件夹。

1. 更新配置 `json` 文件：

    | 文件 | 新值 | 备注 |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | 将 `<custom-domain>` 替换为你在第一步中设置的自定义域。 |
    |  | `"useHipCaptcha": true` | 将值更改为 `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | 将 `<custom-domain>` 替换为你在第一步中设置的自定义域。 |
    |  | `"useHipCaptcha": true` | 将值更改为 `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | 将 `<custom-domain>` 替换为你在第一步中设置的自定义域。 |

1. [发布](#step-5-publish-locally)门户。

1. [上传](#step-6-upload-static-files-to-a-blob)并托管新发布的门户。

1. 通过自定义域公开自托管门户。

门户域的第一级和第二级需要与第一步中设置的域相匹配。 例如，`portal.contoso.com`。 具体步骤取决于你选择的托管平台。 如果你使用的是 Azure 存储帐户，请参阅[将自定义域映射到 Azure Blob 存储终结点](../storage/blobs/storage-custom-domain-name.md)，了解相关说明。

## <a name="next-steps"></a>后续步骤

- 了解[自托管的替代方法](developer-portal-alternative-processes-self-host.md)
