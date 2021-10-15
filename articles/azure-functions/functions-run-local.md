---
title: 使用 Azure Functions Core Tools
description: 了解如何通过本地计算机上的命令提示符或终端编写和测试 Azure 函数，然后在 Azure Functions 中运行这些函数。
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 5f2ea49df446c26453bb8cf54af52ab54b2c24b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669698"
---
# <a name="work-with-azure-functions-core-tools"></a>使用 Azure Functions Core Tools

使用 Azure Functions Core Tools 可以在本地计算机上通过命令提示符或终端开发和测试函数。 本地函数可以连接到实时 Azure 服务，你可以在本地计算机上使用完整的 Functions 运行时调试函数。 甚至可以将函数应用部署到 Azure 订阅。

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

按照以下基本步骤在本地计算机上开发函数并使用 Core Tools 将其发布到 Azure：

> [!div class="checklist"]
> * [安装 Core Tools 和依赖项](#v2)。
> * [从特定于语言的模板创建函数应用项目](#create-a-local-functions-project)。
> * [注册触发器和绑定扩展](#register-extensions)。
> * [定义存储和其他连接](#local-settings)。
> * [从触发器和特定于语言的模板创建函数](#create-func)。
> * [在本地运行函数。](#start)
> * [将项目发布到 Azure。](#publish)

## <a name="prerequisites"></a>先决条件

Azure Functions Core Tools 当前依靠 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps) 通过 Azure 帐户进行身份验证。 这意味着必须安装以下工具之一才能通过 Azure Functions Core Tools [发布到 Azure](#publish)。 

## <a name="core-tools-versions"></a><a name="v2"></a>Core Tools 版本

Azure Functions Core Tools 有四个版本。 使用的版本取决于本地开发环境、[所选的语言](supported-languages.md)以及所需的支持级别。

选择以下版本选项卡来了解每个特定版本和详细的安装说明：

# <a name="version-3x"></a>[3\.x 版](#tab/v3)

支持 [3.x 版](functions-versions.md)的 Azure Functions 运行时。 此版本支持 Windows、macOS 和 Linux，并使用特定于平台的包管理器或 npm 进行安装。 这是建议的 Functions 运行时和 Core Tools 版本。

# <a name="version-4x-preview"></a>[版本 4.x（预览版）](#tab/v4)

支持 [4.x 版](functions-versions.md) Functions 运行时，以预览版提供。 此版本支持 Windows、macOS 和 Linux，并使用特定于平台的包管理器或 npm 进行安装。 除非需要在 .NET 6.0 上运行 C# 函数，否则请考虑使用 3.x 版 Functions 运行时和 Core Tools。

# <a name="version-2x"></a>[版本 2.x](#tab/v2)

支持 [2.x 版](functions-versions.md)的 Azure Functions 运行时。 此版本支持 Windows、macOS 和 Linux，并使用特定于平台的包管理器或 npm 进行安装。 

# <a name="version-1x"></a>[版本 1.x](#tab/v1) 

支持 1.x 版的 Azure Functions 运行时。 此 Tools 版本仅在 Windows 计算机上受支持，需从 [npm 包](https://www.npmjs.com/package/azure-functions-core-tools)安装。

---

你只能在给定计算机上安装一个版本的核心工具。  除非另有说明，否则本文中的示例适用于版本 3.x。

## <a name="install-the-azure-functions-core-tools"></a>安装 Azure Functions Core Tools

[Azure Functions Core Tools] 包含同一运行时的另一版本，该版本为本地开发计算机上可运行的 Azure Functions 运行时提供支持。 它还提供用于创建函数、连接到 Azure 和部署函数项目的命令。

从版本 2.x 开始，Core Tools 是在 .NET Core 基础上构建的。 这意味着，版本 2.x 及更高版本在 .NET Core 支持的所有平台（包括 [Windows](?tabs=windows#v2)、[macOS](?tabs=macos#v2) 和 [Linux](?tabs=linux#v2)）上运行。

> [!IMPORTANT]
> 对于非 .NET 语言，可以使用[扩展捆绑包]绕过安装 .NET Core SDK 的要求。

# <a name="windows"></a>[Windows](#tab/windows/v4)

安装 4.x 版 Core Tools 当前需要 npm。 如果使用不同的包管理器安装了以前版本的 Core Tools，请在安装版本 4.x 之前卸载它。

1. 如果尚未执行此操作，请[安装包含 npm 的 Node.js](https://nodejs.org/en/download/)。 

1. 运行以下 npm 命令以安装 Core Tools 包：

    ```
    npm i -g azure-functions-core-tools@4 --unsafe-perm true
    ```

1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 Windows 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。

# <a name="windows"></a>[Windows](#tab/windows/v3)

以下步骤使用 Windows 安装程序 (MSI) 安装 Core Tools v3.x。 若要详细了解其他基于包的安装程序，请参阅 [Core Tools 自述文件](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)。

1. 基于Windows 版本下载并运行 Core Tools 安装程序：

    - [v3.x - Windows 64 位](https://go.microsoft.com/fwlink/?linkid=2135274)（推荐。 [Visual Studio Code 调试](functions-develop-vs-code.md#debugging-functions-locally)需要 64 位。）
    - [v3.x - Windows 32 位](https://go.microsoft.com/fwlink/?linkid=2135275)

1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 Windows 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。

# <a name="windows"></a>[Windows](#tab/windows/v2)

安装 2.x 版 Core Tools 需要 npm。 还可以[使用 Chocolatey 安装包](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#azure-functions-core-tools)。

1. 如果尚未执行此操作，请[安装包含 npm 的 Node.js](https://nodejs.org/en/download/)。 

1. 运行以下 npm 命令以安装 Core Tools 包：

    ```
    npm install -g azure-functions-core-tools@2 --unsafe-perm true
    ```

1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 Windows 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。

# <a name="windows"></a>[Windows](#tab/windows/v1)

如果需要安装 1.x 版 Core Tools，请参阅 [GitHub 存储库](https://github.com/Azure/azure-functions-core-tools/blob/v1.x/README.md#installing)以了解详细信息。

# <a name="macos"></a>[macOS](#tab/macos/v4)

以下步骤使用 Homebrew 在 macOS 上安装 Core Tools。

1. 安装 [Homebrew](https://brew.sh/)（如果尚未安装）。

1. 安装 Core Tools 包：

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@4
    # if upgrading on a machine that has 2.x or 3.x installed:
    brew link --overwrite azure-functions-core-tools@4
    ```
    
1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 macOS 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。

# <a name="macos"></a>[macOS](#tab/macos/v3)

以下步骤使用 Homebrew 在 macOS 上安装 Core Tools。

1. 安装 [Homebrew](https://brew.sh/)（如果尚未安装）。

1. 安装 Core Tools 包：

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed:
    brew link --overwrite azure-functions-core-tools@3
    ```
    
1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 macOS 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。

# <a name="macos"></a>[macOS](#tab/macos/v2)

以下步骤使用 Homebrew 在 macOS 上安装 Core Tools。

1. 安装 [Homebrew](https://brew.sh/)（如果尚未安装）。

1. 安装 Core Tools 包：

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 macOS 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。

# <a name="macos"></a>[macOS](#tab/macos/v1)

macOS 不支持 1.x 版 Core Tools。 在 macOS 上使用版本 2.x 或更高版本。

# <a name="linux"></a>[Linux](#tab/linux/v4)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 安装 Core Tools 包：

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-4
    ```

1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 Linux 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。


# <a name="linux"></a>[Linux](#tab/linux/v3)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 安装 Core Tools 包：

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```

1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 Linux 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。

# <a name="linux"></a>[Linux](#tab/linux/v2)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 安装 Core Tools 包：

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. 如果不打算使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，请安装[用于 Linux 的 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)。


# <a name="linux"></a>[Linux](#tab/linux/v1)

Linux 不支持 1.x 版 Core Tools。 在 Linux 上使用版本 2.x 或更高版本。

---

## <a name="changing-core-tools-versions"></a>更改 Core Tools 版本

更改为不同版本的 Core Tools 时，应使用与原始安装相同的包管理器移动到不同的包版本。 例如，如果使用 npm 安装了 Core Tools 版本 2.x，则应使用以下命令升级到版本 3.x：

```bash
npm install -g azure-functions-core-tools@3 --unsafe-perm true
```

如果已使用 Windows 安装程序 (MSI) 在 Windows 上安装 Core Tools，则应在安装其他版本之前从“添加/删除程序”中卸载旧版本。

## <a name="create-a-local-functions-project"></a>创建本地 Functions 项目

不管语言如何，Functions 项目目录都包含以下文件和文件夹： 

| 文件名 | 说明 |
| --- | --- |
| host.json | 有关详细信息，请参阅 [host.json 参考](functions-host-json.md)。 |
| local.settings.json | Core Tools 在本地运行时使用的设置，包括应用设置。 有关详细信息，请参阅[本地设置](#local-settings)。 |
| .gitignore | 防止意外将 local.settings.json 文件发布到 Git 存储库。 有关详细信息，请参阅[本地设置](#local-settings)|
| .vscode\extensions.json | 在 Visual Studio Code 中打开项目文件夹时使用的设置文件。  |

若要详细了解 Functions 项目文件夹，请参阅 [Azure Functions 开发人员指南](functions-reference.md#folder-structure)。

在终端窗口中或者在命令提示符下，运行以下命令创建项目和本地 Git 存储库：

```
func init MyFunctionProj
```

此示例在新的 `MyFunctionProj` 文件夹中创建一个 Functions 项目。 系统会提示你为项目选择默认语言。 

以下注意事项适用于项目初始化：

+ 如果未在命令中提供 `--worker-runtime` 选项，则系统会提示你选择语言。 有关详细信息，请参阅 [func init 参考](functions-core-tools-reference.md#func-init)。

+ 如果未提供项目名称，则会初始化当前文件夹。 

+ 如果你打算将项目发布到自定义 Linux 容器，请使用 `--dockerfile` 选项来确保为项目生成 Dockerfile。 有关详细信息，请参阅[在 Linux 上使用自定义映像创建函数](functions-create-function-linux-custom-image.md)。 

对于某些语言，可能还要注意其他一些事项：

# <a name="c"></a>[C\#](#tab/csharp)

+ 默认情况下，Core Tools 2.x 及更高版本会为 .NET 运行时创建函数应用项目作为 [C# 类项目](functions-dotnet-class-library.md) (.csproj)。 版本 3.x 还支持创建可[在 .NET 5.0 上的隔离进程中运行](dotnet-isolated-process-guide.md)的函数。 在调试期间以及发布到 Azure 时，将会编译这些可在 Visual Studio 或 Visual Studio Code 中使用的 C# 项目。 

+ 如果要在本地处理 C# 脚本 (.csx) 文件，请使用 `--csx` 参数。 在 Azure 门户中创建函数时以及在使用 Core Tools 版本 1.x 时，也会获得这些文件。 有关详细信息，请参阅 [func init 参考](functions-core-tools-reference.md#func-init)。

# <a name="java"></a>[Java](#tab/java)

+ Java 使用 Maven archetype 来创建本地 Functions 项目，以及第一个由 HTTP 触发的函数。 不要使用 `func init` 和 `func new`，而应该遵循[命令行快速入门](./create-first-function-cli-java.md)中的步骤。  

# <a name="javascript"></a>[JavaScript](#tab/node)

+ 若要使用 `node` 的 `--worker-runtime` 值，请将 `--language` 指定为 `javascript`。 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

对于 PowerShell，没有其他注意事项。

# <a name="python"></a>[Python](#tab/python)

+ 应从虚拟环境内部运行所有命令，包括 `func init`。 有关详细信息，请参阅[创建并激活虚拟环境](create-first-function-cli-python.md#create-venv)。

# <a name="typescript"></a>[TypeScript](#tab/ts)

+ 若要使用 `node` 的 `--worker-runtime` 值，请将 `--language` 指定为 `javascript`。

+ 有关特定于 TypeScript 的 `func init` 行为，请参阅 [JavaScript 开发人员参考中的“TypeScript”部分](functions-reference-node.md#typescript)。 

--- 

## <a name="register-extensions"></a>注册扩展

从运行时版本 2.x 开始，Functions 绑定作为 .NET 扩展 (NuGet) 包实现。 对于已编译的 C# 项目，只需引用所用的特定触发器和绑定的 NuGet 扩展包即可。 HTTP 绑定和计时器触发器不需要扩展。 

为了改进非 C# 项目的开发体验，Functions 允许在 host.json 项目文件中引用版本受控的扩展捆绑包。 [扩展捆绑包](functions-bindings-register.md#extension-bundles)使得所有扩展可供应用使用，并消除了扩展之间出现包兼容性问题的可能性。 扩展捆绑包还消除了安装 .NET Core 2.x SDK 以及处理 extensions.csproj 文件的要求。

对于除 C# 已编译项目以外的其他 Functions 项目，建议使用扩展捆绑包。 对于这些项目，在初始化期间将在 host.json 文件中生成扩展捆绑包设置。 如果你接受这种行为，则可以完全跳过本部分。  

### <a name="use-extension-bundles"></a>使用扩展捆绑包

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

 如果受你的语言的支持，扩展捆绑包在你调用 `func init` 后应已启用。 在将绑定添加到 function.json 文件之前，应该先将扩展捆绑包添加到 host.json。 若要了解更多信息，请参阅[注册 Azure Functions 绑定扩展](functions-bindings-register.md#extension-bundles)。 

### <a name="explicitly-install-extensions"></a>显式安装扩展

有时，你可能无法在非 .NET 项目中使用扩展捆绑包，例如，需要将捆绑包中不包含的特定扩展版本作为目标时。 对于这种罕见情况，可以使用 Core Tools 在本地安装项目所需的特定扩展包。 有关详细信息，请参阅[显式安装扩展](functions-bindings-register.md#explicitly-install-extensions)。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

默认情况下，将项目发布到 Azure 时，这些设置不会自动迁移。 发布时使用[`--publish-local-settings` 选项][func azure functionapp publish]，以确保将这些设置添加到 Azure 中的函数应用。 永远不会发布 `ConnectionStrings` 节中的值。

还可以在代码中将函数应用设置值读取为环境变量。 有关详细信息，请参阅以下特定于语言的参考主题的“环境变量”部分：

* [预编译 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 脚本 (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

如果没有为 [`AzureWebJobsStorage`] 设置有效的存储连接字符串并且没有使用模拟器，则会显示以下错误消息：

> local.settings.json 中的 AzureWebJobsStorage 缺少值。 该值对除 HTTP 以外的所有触发器都是必需的。 可运行“func azure functionapp fetch-app-settings \<functionAppName\>”或在 local.settings.json 中指定连接字符串。

### <a name="get-your-storage-connection-strings"></a>获取存储连接字符串

即使在使用 Microsoft Azure 存储模拟器进行开发时，也可能需要在本地使用实际的存储连接来运行。 假设已[创建了存储帐户](../storage/common/storage-account-create.md)，则可以通过多种方式之一获取有效的存储连接字符串：

# <a name="portal"></a>[Portal](#tab/portal)

1. 在 [Azure 门户]中，搜索并选择“存储帐户”。 

    ![从 Azure 门户选择存储帐户](./media/functions-run-local/select-storage-accounts.png)
  
1.  选择你的存储帐户，在“设置”中选择“访问密钥”，然后复制其中一个 **连接字符串** 值。 

    ![从 Azure 门户复制连接字符串](./media/functions-run-local/copy-storage-connection-portal.png)

# <a name="core-tools"></a>[Core Tools](#tab/azurecli)

在项目根目录中，使用以下命令之一从 Azure 下载连接字符串：

  + 从现有函数应用下载所有设置：

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + 获取特定存储帐户的连接字符串。

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    如果你尚未登录到 Azure，系统会要求登录。 这些命令将覆盖 local.settings.json 文件中的任何现有设置。 有关详细信息，请参阅 [`func azure functionapp fetch-app-settings`](functions-core-tools-reference.md#func-azure-functionapp-fetch-app-settings) 和 [`func azure storage fetch-connection-string`](functions-core-tools-reference.md#func-azure-storage-fetch-connection-string) 命令。

# <a name="storage-explorer"></a>[存储资源管理器](#tab/storageexplorer)

1. 运行 [Azure 存储资源管理器](https://storageexplorer.com/)。 

1. 在“资源管理器”中展开你的订阅，然后展开“存储帐户” 。

1. 然后选择你的存储帐户并复制主要或辅助连接字符串。

    ![从存储资源管理器复制连接字符串](./media/functions-run-local/storage-explorer.png)

---

## <a name="create-a-function"></a><a name="create-func"></a>创建函数

若要在现有项目中创建函数，请运行以下命令：

```
func new
```

在版本 3.x/2.x 中运行 `func new` 时，系统会提示你选择一个模板，该模板采用你的函数应用的默认语言。 接下来，系统会提示你为你的函数选择一个名称。 在版本 1.x 中，系统还会要求你选择语言。 

还可以在 `func new` 命令中指定函数名称和模板。 以下示例使用 `--template` 选项创建名为 `MyHttpTrigger` 的 HTTP 触发器：

```
func new --template "Http Trigger" --name MyHttpTrigger
```

此示例创建名为 `MyQueueTrigger` 的队列存储触发器：

```
func new --template "Queue Trigger" --name MyQueueTrigger
```

有关详细信息，请参阅 [`func new` 命令](functions-core-tools-reference.md#func-new)。

## <a name="run-functions-locally"></a><a name="start"></a>在本地运行函数

若要运行 Functions 项目，请从项目的根目录运行 Functions 主机。 主机会为项目中的所有函数启用触发器。 [`start` 命令](functions-core-tools-reference.md#func-start)因项目语言而异。

# <a name="c"></a>[C\#](#tab/csharp)

```
func start
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
此命令必须[在虚拟环境中运行](./create-first-function-cli-python.md)。

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Functions 运行时的 1.x 版改为需要 `func host start`。 有关详细信息，请参阅 [Azure Functions Core Tools 参考](functions-core-tools-reference.md?tabs=v1#func-start)。 

Functions 主机启动时，会输出 HTTP 触发的函数的 URL，如以下示例所示：

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>在本地运行时，不会对 HTTP 终结点强制执行授权操作。 这意味着所有本地 HTTP 请求都将作为 `authLevel = "anonymous"` 处理。 有关详细信息，请参阅 [HTTP 绑定](functions-bindings-http-webhook-trigger.md#authorization-keys)一文。

### <a name="passing-test-data-to-a-function"></a>将测试数据传递给函数

若要在本地测试函数，请[启动 Functions 主机](#start)，并在本地服务器上使用 HTTP 请求调用终结点。 你调用的终结点要取决于函数的类型。

>[!NOTE]
> 本主题中的示例使用 cURL 工具从终端或命令提示符发送 HTTP 请求。 你可以使用所选的工具将 HTTP 请求发送到本地服务器。 默认情况下，cURL 工具在基于 Linux 的系统和 Windows 10 内部版本 17063 及更高版本上可用。 在较旧的 Windows 上，必须先下载并安装 [cURL 工具](https://curl.haxx.se/)。

有关测试函数的更多常规信息，请参阅[在 Azure Functions 中测试代码的策略](functions-test-a-function.md)。

#### <a name="http-and-webhook-triggered-functions"></a>HTTP 和 webhook 触发的函数

调用以下终结点，以在本地运行 HTTP 和 webhook 触发的函数：

```
http://localhost:{port}/api/{function_name}
```

请确保使用相同的服务器名称和 Functions 主机正在侦听的端口。 在启动 Function 主机时所生成的输出中可以看到该信息。 可以使用触发器所支持的任何 HTTP 方法来调用此 URL。

以下 cURL 命令使用查询字符串中传递的 name 参数从 GET 请求触发 `MyHttpTrigger` quickstart 函数。

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

下面的示例是在请求主体中传递 name 的 POST 请求中调用的相同函数：

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

可以从在查询字符串中传递数据的浏览器发出 GET 请求。 对于所有其他 HTTP 方法，必须使用 cURL、Fiddler、Postman 或类似的支持 POST 请求的 HTTP 测试工具。

#### <a name="non-http-triggered-functions"></a>非 HTTP 触发的函数

对于除 HTTP 和事件网格触发器以外的所有函数，可以使用 REST 通过调用一个称作“管理终结点”的特殊终结点，在本地测试函数。 在本地服务器上通过 HTTP POST 请求调用此终结点会触发该函数。 

若要在本地测试事件网格触发函数，请参阅[使用查看器 Web 应用进行本地测试](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)。

可以选择通过 POST 请求正文将测试数据传递给执行。 此功能类似于 Azure 门户中的“测试”选项卡。

可以调用以下管理员终结点以触发非 HTTP 函数：

```
http://localhost:{port}/admin/functions/{function_name}
```

若要将测试数据传递给函数的管理员终结点，必须在 POST 请求消息的正文中提供数据。 消息正文需要具有以下 JSON 格式：

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` 值包含函数所需格式的数据。 下面的 cURL 示例是指向 `QueueTriggerJS` 函数的 POST。 在这种情况下，输入是一个字符串，等同于期望在队列中找到的消息。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

在 Azure 中的函数应用上调用管理员终结点时，必须提供访问密钥。 有关详细信息，请参阅[函数访问密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。

## <a name="publish-to-azure"></a><a name="publish"></a>发布到 Azure

Azure Functions Core Tools 支持三种类型的部署：

| 部署类型 | Command | 说明 |
| ----- | ----- | ----- |
| 项目文件 | [`func azure functionapp publish`](functions-core-tools-reference.md#func-azure-functionapp-publish) | 使用 [zip 部署](functions-deployment-technologies.md#zip-deploy)将函数项目文件直接部署到函数应用。 |
| 自定义容器 | `func deploy` | 将项目作为自定义 Docker 容器部署到 Linux 函数应用。  |
| Kubernetes 群集 | `func kubernetes deploy` | 将 Linux 函数应用作为客户 Docker 容器部署到 Kubernetes 群集。 | 

### <a name="before-you-publish"></a>在发布之前 

>[!IMPORTANT]
>必须在本地安装 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps)，才能通过 Core Tools 发布到 Azure。  

项目文件夹可能包含不应该发布的特定于语言的文件和目录。 排除的项在根项目文件夹的 .funcignore 文件中列出。  

必须已[在 Azure 订阅中创建了一个函数应用](functions-cli-samples.md#create)，你将向其部署代码。 应该生成需要编译的项目，以便部署二进制文件。

若要了解如何使用 Azure CLI 或 Azure PowerShell 从命令提示符或终端窗口创建函数应用，请参阅[为无服务器执行创建函数应用](./scripts/functions-cli-create-serverless.md)。 

>[!IMPORTANT]
> 在 Azure 门户中创建函数应用时，该应用默认使用 3.x 版函数运行时。 若要让函数应用使用 1.x 版运行时，请遵照[在版本 1.x 上运行](functions-versions.md#creating-1x-apps)中的说明操作。
> 无法为包含现有函数的函数应用更改运行时版本。


### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>部署项目文件

若要将本地代码发布到 Azure 中的函数应用，请使用 `publish` 命令：

```
func azure functionapp publish <FunctionAppName>
```

以下注意事项适用于这种部署：

+ 发布过程会覆盖函数应用中的现有文件。

+ 使用 [`--publish-local-settings` 选项][func azure functionapp publish]可以根据 local.settings.json 文件中的值自动在函数应用中创建应用设置。  

+ 将对已编译的项目执行[远程生成](functions-deployment-technologies.md#remote-build)。 可以使用 [`--no-build` 选项][func azure functionapp publish]来控制此操作。  

+ 项目在部署后将[从部署包运行](run-functions-from-deployment-package.md)。 若要禁用这种建议的部署模式，请使用 [`--nozip` 选项][func azure functionapp publish]。

+ Java 使用 Maven 将本地项目发布到 Azure。 改用以下命令发布到 Azure：`mvn azure-functions:deploy`。 Azure 资源是在初始部署期间创建的。

+ 如果尝试发布到订阅中不存在的 `<FunctionAppName>`，则会收到错误。 

### <a name="kubernetes-cluster"></a>Kubernetes 群集

Functions 还允许将 Functions 项目定义为在 Docker 容器中运行。 使用 `func init` 的 [`--docker` 选项][func init]生成适用于特定语言的 Dockerfile。 然后，在创建要部署的容器时会使用此文件。 

可以使用 Core Tools 将项目作为自定义容器映像部署到 Kubernetes 群集。 使用的命令取决于群集中使用的缩放器类型。  

以下命令使用 Dockerfile 生成一个容器并将其部署到 Kubernetes 群集。 

# <a name="keda"></a>[KEDA](#tab/keda)

```command
func kubernetes deploy --name <DEPLOYMENT_NAME> --registry <REGISTRY_USERNAME> 
```

有关详细信息，请参阅[将函数应用部署到 Kubernetes](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes)。 

# <a name="defaultknative"></a>[默认/KNative](#tab/default)

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <REGISTRY_USERNAME> 
```

在以上示例中，请将 `<FUNCTION_APP>` 替换为 Azure 中函数应用的名称，并将 `<REGISTRY_USERNAME>` 替换为你的注册表帐户名称，例如你的 Docker 用户名。 容器将在本地生成，并使用一个基于 `<FUNCTION_APP>` 的映像名称推送到你的 Docker 注册表帐户。 Docker 命令行工具必须已安装。

有关详细信息，请参阅 [`func deploy` 命令](functions-core-tools-reference.md#func-deploy)。

---

若要了解如何在不使用 Kubernetes 的情况下将自定义容器发布到 Azure，请参阅[在 Linux 上使用自定义容器创建函数](functions-create-function-linux-custom-image.md)。

## <a name="monitoring-functions"></a>监视函数

监视函数执行的建议方法是与 Azure Application Insights 集成。 还可以将执行日志流式传输到本地计算机。 若要了解详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

### <a name="application-insights-integration"></a>Application Insights 集成

在 Azure 中创建函数应用时，应启用 Application Insights 集成。 如果由于某种原因，函数应用未连接到 Application Insights 实例，则在 Azure 门户中可以轻松地进行此集成。 若要了解详细信息，请参阅[启用 Application Insights 集成](configure-monitoring.md#enable-application-insights-integration)。

### <a name="enable-streaming-logs"></a>启用流式传输日志

可以在本地计算机的命令行会话中查看函数正在生成的日志文件流。 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

这种类型的流式传输日志需要为函数应用启用了 Application Insights 集成。   


## <a name="next-steps"></a>后续步骤

了解如何使用 Azure Functions Core Tools 开发、测试和发布 Azure Functions [Microsoft 学习模块](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools 是[开源工具，托管在 GitHub 上](https://github.com/azure/azure-functions-cli)。  
若要提交 bug 或功能请求，[请打开 GitHub 问题](https://github.com/azure/azure-functions-cli/issues)。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 门户]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[扩展捆绑包]: functions-bindings-register.md#extension-bundles
[func azure functionapp publish]: functions-core-tools-reference.md?tabs=v2#func-azure-functionapp-publish
[func init]: functions-core-tools-reference.md?tabs=v2#func-init
