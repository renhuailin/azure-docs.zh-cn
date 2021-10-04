---
title: 为 Azure 静态 Web 应用配置应用程序设置
description: 了解如何为 Azure 静态 Web 应用配置应用程序设置
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/23/2021
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: a104860eb72a6376c2ab337f31bb06fd041f42a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597087"
---
# <a name="configure-application-settings-for-azure-static-web-apps"></a>为 Azure 静态 Web 应用配置应用程序设置

应用程序设置包含可能更改的值（例如数据库连接字符串）的配置设置。 通过添加应用程序设置，可以修改应用的配置输入，而无需更改应用程序代码。

应用程序设置：

- 可用作静态 Web 应用的后端 API 的环境变量
- 可用于存储[身份验证配置](key-vault-secrets.md)中使用的机密
- 静态加密
- 复制到[过渡环境](review-publish-pull-requests.md)和生产环境
- 只能是字母数字字符、`.` 和 `_`

> [!IMPORTANT]
> 本文所述的应用程序设置仅适用于 Azure 静态 Web 应用的后端 API。
>
> 若要配置生成前端 Web 应用程序所需的环境变量，请参阅[生成配置](build-configuration.md#environment-variables)。

## <a name="prerequisites"></a>先决条件

- Azure 静态 Web 应用应用程序
- [Azure CLI](/cli/azure/install-azure-cli) - 在要使用命令行的情况下为必需

## <a name="configure-api-application-settings-for-local-development"></a>为本地开发配置 API 应用程序设置

Azure Static Web Apps 中的 API 由 Azure Functions 提供支持，使你能够在 local.settings.json 文件中定义应用程序设置（当你在本地运行应用程序时）。 此文件定义配置的 `Values` 属性中的应用程序设置。

> [!NOTE]
> local.settings.json 文件仅用于本地开发。 使用 [Azure 门户](https://portal.azure.com)配置适用于生产环境的应用程序设置。

以下示例 local.settings.json 演示如何为 `DATABASE_CONNECTION_STRING` 添加值。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

`Values` 属性中定义的设置可以作为环境变量从代码中引用。 例如，在 Node.js 函数中，可以在 `process.env` 对象中获取它们。

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

GitHub 存储库不会跟踪 `local.settings.json` 文件，因为敏感信息（如数据库连接字符串）通常包含在文件中。 由于本地设置保留在计算机中，因此需要在 Azure 中手动配置你的设置。

通常不会频繁对设置进行配置，而且并非每次生成都需要进行配置。

## <a name="configure-application-settings"></a>配置应用程序设置

可以通过 Azure 门户或使用 Azure CLI 配置应用程序设置。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

Azure 门户提供一个用于创建、更新和删除应用程序设置的接口。

1. 导航到 [Azure 门户](https://portal.azure.com)。

1. 在搜索栏中，搜索并选择“静态 Web 应用”。

1. 单击边栏中的“配置”选项。

1. 选择要将应用程序设置应用到的环境。 过渡环境在拉取请求生成时自动创建，并在拉取请求合并后提升为生产环境。 可以设置每个环境的应用程序设置。

1. 单击“添加”按钮以添加新应用设置。

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Azure 静态 Web 应用配置视图":::

1. 输入“名称”和“值” 。

1. 单击 **“确定”** 。

1. 单击“保存” 。

### <a name="use-the-azure-cli"></a>使用 Azure CLI

可以使用 `az rest` 命令将设置批量上传至 Azure。 命令接收应用程序设置作为名为 `properties` 的父属性中的 JSON 对象。

创建具有适当值的 JSON 文件的最简单方法是创建 local.settings.json 文件的修改版本。

1. 若要确保不会公开公布包含敏感数据的新文件，请将以下条目添加到 .gitignore 文件中。

   ```bash
   local.settings*.json
   ```

1. 接下来，创建 local.settings.json 文件的副本，并将其命名为 local.settings.properties.json。

1. 在新文件中，从文件（应用程序设置除外）中删除所有其他数据，然后将 `Values` 重命名为 `properties`。

   文件现在应类似于以下示例：

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

1. 请执行以下命令以列出订阅中的静态 Web 应用并显示其详细信息。

    ```bash
    az staticwebapp list -o json
    ```

    找到要配置的静态 Web 应用，并记下其 ID。

1. 从终端或命令行执行以下命令，以上传设置。 将 `<YOUR_APP_ID>` 替换为在上一步检索到的应用 ID。

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "<YOUR_APP_ID>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

  > [!IMPORTANT]
  > “local.settings.properties.json”文件必须位于运行此命令的同一目录中。 你可以按需命名此文件。 此名称并不重要。

### <a name="view-application-settings-with-the-azure-cli"></a>通过 Azure CLI 查看应用程序设置

可通过 Azure CLI 查看应用程序设置。

- 从终端或命令行执行以下命令。 请务必将占位符 `<YOUR_APP_ID>` 替换为你的值。

   ```bash
   az rest --method post --uri "<YOUR_APP_ID>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置前端框架](front-end-frameworks.md)
