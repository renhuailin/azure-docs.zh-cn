---
title: 将 Azure 应用程序配置与 JavaScript 应用结合使用的快速入门 | Microsoft Docs
description: 在本快速入门中，使用 Azure 应用程序配置创建 Node.js 应用，集中存储和管理与代码分离的应用程序设置。
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: quickstart
ms.date: 07/12/2021
ms.author: drewbat
ms.openlocfilehash: 850771db454f854243ac9ba242d02ed1911f255c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783229"
---
# <a name="quickstart-create-a-javascript-app-with-azure-app-configuration"></a>快速入门：使用 Azure 应用程序配置创建 JavaScript 应用

在本快速入门中，你将使用[适用于 JavaScript 的应用程序配置客户端库](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/appconfiguration/app-configuration/README.md)通过 Azure 应用程序配置来集中存储和管理应用程序设置。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- [LTS 版本的 Node.js](https://nodejs.org/en/about/releases/)。 有关直接在 Windows 上或使用适用于 Linux 的 Windows 子系统 (WSL) 安装 Node.js 的信息，请参阅 [Node.js 入门](/windows/dev-environment/javascript/nodejs-overview)

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. 选择“配置资源管理器” > “创建” > “键-值”来添加以下键值对    ：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 |

    暂时将“标签”和“内容类型”保留为空   。

8. 选择“应用”。

## <a name="setting-up-the-nodejs-app"></a>设置 Node.js 应用

1. 在本教程中，你将为名为 app-configuration-quickstart 的项目创建一个新目录。

    ```console
    mkdir app-configuration-quickstart
    ```

1. 切换到新创建的 app-configuration-quickstart 目录。

    ```console
    cd app-configuration-quickstart
    ```

1. 使用 `npm install` 命令安装 Azure 应用程序配置客户端库。

    ```console
    npm install @azure/app-configuration
    ```

1. 在 app-configuration-quickstart 目录中，创建名为 app.js 的新文件，并添加以下代码 ：

   ```javascript
   const appConfig = require("@azure/app-configuration");
   ```

## <a name="configure-your-connection-string"></a>配置连接字符串

1. 设置名为“AZURE_APP_CONFIG_CONNECTION_STRING”的环境变量，并将其设置为应用程序配置存储区的访问密钥。 在命令行中运行以下命令：

    ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="command-line"></a>[命令行](#tab/command-line)

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="macos"></a>[macOS](#tab/macOS)
    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

    ---

2. 重启命令提示符以使更改生效。 输出环境变量的值以验证其设置是否正确。

## <a name="connect-to-an-app-configuration-store"></a>连接到应用程序配置存储区

以下代码片段使用环境变量中存储的连接字符串创建 AppConfigurationClient 实例。

```javascript
const connection_string = process.env.AZURE_APP_CONFIG_CONNECTION_STRING;
const client = new appConfig.AppConfigurationClient(connection_string);
```

## <a name="get-a-configuration-setting"></a>获取配置设置

以下代码片段按 `key` 名称检索配置设置。 此示例中显示的键值是在本文的前面步骤中创建的。

```javascript
async function run() {
  
  let retrievedSetting = await client.getConfigurationSetting({
    key: "TestApp:Settings:Message"
  });

  console.log("Retrieved value:", retrievedSetting.value);
}

run().catch((err) => console.log("ERROR:", err));
```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 运行以下命令以运行 Node.js 应用：

   ```powershell
   node app.js
   ```
1. 应在命令提示符处看到以下输出：

   ```powershell
   Retrieved value: Data from Azure App Configuration
   ```
## <a name="clean-up-resources"></a>清理资源


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了新的应用程序配置存储区，并了解到如何从 Node.js 应用访问键-值。

有关其他代码示例，请访问：

> [!div class="nextstepaction"]
> [Azure 应用程序配置客户端库示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
