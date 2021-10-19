---
title: 注册 Azure Functions 绑定扩展
description: 了解如何根据你的环境注册 Azure Functions 绑定扩展。
author: craigshoemaker
ms.topic: reference
ms.date: 09/14/2020
ms.author: cshoe
ms.openlocfilehash: 7267e733971b2abed258ca815a010b13938bc2a6
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613465"
---
# <a name="register-azure-functions-binding-extensions"></a>注册 Azure Functions 绑定扩展

从 Azure Functions 版本 2.x 开始，函数运行时默认情况下仅包括 HTTP 和计时器触发器。 其他[触发器和绑定](./functions-triggers-bindings.md)可作为单独的包提供。

.NET 类库函数应用使用以 NuGet 包的形式安装在项目中的绑定。 扩展捆绑允许非 .NET 函数应用使用相同的绑定，而不必处理 .NET 基础结构。

下表指明了何时以及如何注册绑定。

| 开发环境 |在<br/> Functions 1.x 中注册  |在<br/> Functions 2.x 或更高版本中注册  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 门户|自动|自动<sup>*</sup>|
|非 .NET 语言|自动|使用[扩展捆绑](#extension-bundles)（推荐）或[显式安装扩展](#explicitly-install-extensions)|
|使用 Visual Studio 的 C# 类库|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 类库|不适用|[使用 .NET Core CLI](#vs-code)|

<sup>*</sup> 门户使用扩展捆绑包。

## <a name="access-extensions-in-non-net-languages"></a>非 .NET 语言中的访问扩展

对于 Java、JavaScript、PowerShell、Python 和自定义处理程序函数应用，建议使用扩展捆绑来访问绑定。 如果无法使用扩展捆绑，则可以显式安装绑定扩展。

### <a name="extension-bundles"></a><a name="extension-bundles"></a>扩展捆绑包

使用扩展捆绑可将一组兼容的绑定扩展添加到函数应用。 可以在应用的 host.json 文件中启用扩展捆绑。

可以将扩展捆绑与 2.x 版和更高版本的 Functions 运行时一起使用。

扩展捆绑已进行版本控制。 每个版本都包含一组特定的绑定扩展，这些扩展经过验证，可协同工作。 根据应用中所需的扩展，选择捆绑版本。

若要将扩展捆绑添加到函数应用，请将 `extensionBundle` 部分添加到 host.json。 在许多情况下，Visual Studio Code 和 Azure Functions Core Tools 会为你自动添加它。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

下表列出了默认 Microsoft.Azure.Functions.ExtensionBundle 捆绑的当前可用版本以及指向它们所包括的扩展的链接。

| 捆绑版本 | host.json 中的版本 | 已包含的扩展 |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | 请参阅用于生成捆绑的 [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) |
| 2.x | `[2.*, 3.0.0)` | 请参阅用于生成捆绑的 [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) |

> [!NOTE]
> 虽然可以在 host.json 中指定自定义版本范围，但建议使用此表中的版本值。

### <a name="explicitly-install-extensions"></a>显式安装扩展

如果无法使用扩展捆绑包，则可在本地使用 Azure Functions Core Tools 来安装项目所需的特定扩展包。

> [!IMPORTANT]
> 不能在使用扩展捆绑包的函数应用中显式安装扩展。 在显式安装扩展之前，请删除 host.json 中的 `extensionBundle` 部分。

以下各项介绍了你可能需要手动安装扩展的一些原因：

* 需要访问捆绑包中不可用的扩展的特定版本。
* 需要访问捆绑包中不可用的自定义扩展。
* 需要访问单个捆绑包中不可用的特定扩展组合。

> [!NOTE]
> 若要使用 Core Tools 手动安装扩展，必须安装 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)。 Azure Functions Core Tools 使用 .NET Core SDK 从 NuGet 安装扩展。 不需要了解 .NET 即可使用 Azure Functions 扩展。

当你显式安装扩展时，系统会将名为 extensions.csproj 的 .NET 项目文件添加到项目的根目录。 此文件定义函数所需的 NuGet 包集。 尽管你可以使用此文件中的 [NuGet 包引用](/nuget/consume-packages/package-references-in-project-files)，但 Core Tools 允许你在不需手动编辑文件的情况下安装扩展。

可以通过多种方法使用 Core Tools 在本地项目中安装所需的扩展。 

#### <a name="install-all-extensions"></a>安装所有扩展 

使用以下命令自动添加本地项目中的绑定所使用的所有扩展包：

```command
func extensions install
```

该命令读取 function.json 文件以了解所需的程序包，安装这些包并重新生成扩展项目 (extensions.csproj)。 它在当前版本中添加任何新绑定，但不更新现有绑定。 使用 `--force` 选项可在安装新版本时将现有绑定更新为最新版本。 要了解详细信息，请参阅 [`func extensions install` 命令](functions-core-tools-reference.md#func-extensions-install)。

如果函数应用使用 Core Tools 无法识别的绑定，则必须手动安装特定扩展。

#### <a name="install-a-specific-extension"></a>安装特定扩展

使用以下命令安装特定版本的特定扩展包（在本例中为存储扩展）：

```command
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```

要了解详细信息，请参阅 [`func extensions install` 命令](functions-core-tools-reference.md#func-extensions-install)。

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>安装来自 NuGet 的 .NET 语言的扩展

对于基于 C# 类库的函数项目，应直接安装扩展。 扩展捆绑包专用于不基于 C# 类库的项目。

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>使用 Visual Studio 的 C\# 类库

在 **Visual Studio** 中，可以使用 [Install-Package](/nuget/tools/ps-ref-install-package) 命令从包管理器控制台安装包，如以下示例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用于给定绑定的包的名称在该绑定的参考文章中提供。 有关示例，请参阅[服务总线绑定参考文章的“包”部分](functions-bindings-service-bus.md#functions-1x)。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

如果使用 `Install-Package` 来引用绑定，则无需使用[扩展捆绑](#extension-bundles)。 此方法特定于在 Visual Studio 中生成的类库。

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> 使用 Visual Studio Code 的 C# 类库

在 **Visual Studio Code** 中，请使用 .NET Core CLI 中的 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令通过命令提示符安装 C# 类库项目的包。 以下示例演示如何添加绑定：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用于 Azure Functions 2.x 开发。

将 `<BINDING_TYPE_NAME>` 替换为包含所需绑定的包的名称。 可以在[支持的绑定列表](./functions-triggers-bindings.md#supported-bindings)中找到所需的绑定参考文章。

将示例中的 `<TARGET_VERSION>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。与 Functions 运行时 1.x 或 2.x 对应的主版本在绑定的参考文章中指定。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure Functions 触发器和绑定示例](./functions-bindings-example.md)
