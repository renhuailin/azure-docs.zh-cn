---
title: 如何面向 Azure Functions 运行时版本
description: Azure Functions 支持多个版本的运行时。 了解如何在 Azure 中指定函数应用的运行时版本。
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6f608822a8c9b348c16021e64a9fe6a63170d6d6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646960"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何面向 Azure Functions 运行时版本

函数应用在特定版本的 Azure Functions 运行时上运行。 有三个主版本：[3.x、2.x 和 1.x](functions-versions.md)。 默认情况下，函数应用采用 3.x 版的运行时创建。 本文介绍如何在 Azure 中将函数应用配置为在所选的版本上运行。 有关如何为特定版本配置本地开发环境的信息，请参阅[在本地编码和测试 Azure Functions](functions-run-local.md)。

手动指定特定版本的方式取决于你运行的是 Windows 还是 Linux。

## <a name="automatic-and-manual-version-updates"></a>自动和手动版本更新

[在 Linux 上](#manual-version-updates-on-linux)运行函数应用时，本部分不适用。

Azure Functions 允许你通过使用函数应用中的 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置来针对 Windows 上特定版本的运行时。 函数应用将一直保留在指定的主版本上，直到显式选择迁移到新版本。 如果仅指定主版本，当运行时的新次要版本可用时，函数应用将自动更新到该版本。 新的次要版本不应引入中断性变更。 

如果指定次要版本（例如，“2.0.12345”），则函数应用将被固定到该特定版本，直到显式更改它。 会定期从生产环境中删除旧的次要版本。 如果次要版本被删除，则函数应用将恢复运行最新版本，而不是在 `FUNCTIONS_EXTENSION_VERSION` 中设置的版本。 因此，应快速解决需要特定次要版本来解决的任何函数应用问题。 然后，你可以返回到目标主版本。 [应用服务公告](https://github.com/Azure/app-service-announcements/issues)中会公布次要版本删除信息。

> [!NOTE]
> 如果你固定到 Azure Functions 的特定主要版本，然后尝试使用 Visual Studio 发布到 Azure，则会弹出一个对话框窗口，提示你更新到最新版本或取消发布。 若要避免出现此情况，请在 `.csproj` 文件中添加 `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` 属性。

有新版本公开可用时，门户中将显示提示，让你可以升级到该版本。 升级至新版本后，始终可使用 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置移回到之前的版本。

下表显示了启用自动更新所需的每个主版本的 `FUNCTIONS_EXTENSION_VERSION` 值：

| 主版本 | `FUNCTIONS_EXTENSION_VERSION` 值 | 其他配置  |
| ------------- | ----------------------------------- | ------------------------- |
| 4.x           | `~4`                                | [在 Windows 上启用 .NET 6](./functions-versions.md#migrating-from-3x-to-4x) |
| 3.x           | `~3`                                |                           |
| 2.x           | `~2`                                |                           |
| 1.x           | `~1`                                |                           |

更改运行时版本会导致函数应用重启。

>[!NOTE]
>固定到 `~2.0` 的 .NET 函数应用选择退出到 .NET Core 3.1 的自动升级。 有关详细信息，请参阅 [Functions v2.x 注意事项](functions-dotnet-class-library.md#functions-v2x-considerations)。  

## <a name="view-and-update-the-current-runtime-version"></a>查看和更新当前运行时版本

[在 Linux 上](#manual-version-updates-on-linux)运行函数应用时，本部分不适用。

可以更改函数应用使用的运行时版本。 由于可能会成为中断性变更，因此只可在函数应用中创建任何函数之前更改运行时版本。 

> [!IMPORTANT]
> 虽然运行时版本由 `FUNCTIONS_EXTENSION_VERSION` 设置决定，但你应只在 Azure 门户中进行此更改，而不是直接更改设置。 这是因为该门户会验证你的更改，并根据需要进行其他相关更改。

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> 使用 Azure 门户，无法更改已包含函数的函数应用的运行时版本。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

还可以通过 Azure CLI 查看和更新 `FUNCTIONS_EXTENSION_VERSION`。  

使用 Azure CLI 时，可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令查看当前的运行时版本。

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

在此代码中，用函数应用名称替代 `<function_app>`。 此外，还使用函数应用的资源组名称替代 `<my_resource_group>`。 

在以下输出中，你会看到 `FUNCTIONS_EXTENSION_VERSION`，为了清晰起见，该输出已被截断：

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令更新函数应用中的 `FUNCTIONS_EXTENSION_VERSION` 设置。

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

将 `<FUNCTION_APP>` 替换为你的函数应用的名称。 此外，还使用函数应用的资源组名称替代 `<RESOURCE_GROUP>`。 此外，将 `<VERSION>` 替换为特定版本，或者替换为 `~3`、`~2` 或 `~1`。

在上一个代码示例中选择“试用”，以在 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行命令。 也可以在[本地运行 Azure CLI](/cli/azure/install-azure-cli) 来执行此命令。 在本地运行时，必须首先运行 [az login](/cli/azure/reference-index#az_login) 进行登录。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要查看 Azure Functions 运行时，请使用以下 cmdlet： 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

将 `<FUNCTION_APP>` 替换为你的函数应用的名称和 `<RESOURCE_GROUP>`。 在哈希表中返回 `FUNCTIONS_EXTENSION_VERSION` 设置的当前值。

使用以下脚本更改 Functions 运行时：

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

与之前一样，请将 `<FUNCTION_APP>` 替换为函数应用的名称，将 `<RESOURCE_GROUP>` 替换为资源组的名称。 此外，将 `<VERSION>` 替换为特定版本或主要版本，如 `~2` 或 `~3`。 你可以在返回的哈希表中验证 `FUNCTIONS_EXTENSION_VERSION` 设置的更新值。 

---

在对应用程序设置进行更改后，函数应用将重启。

## <a name="manual-version-updates-on-linux"></a>Linux 上的手动版本更新

若要将 Linux 函数应用固定为特定的主机版本，请在站点配置的“LinuxFxVersion”字段中指定映像 URL。例如：如果我们想将节点 10 函数应用固定为主机版本 3.0.13142 -

对于 Linux 应用服务/弹性高级应用，将 `LinuxFxVersion` 设置为 `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice`。

对于 linux 消耗应用，将 `LinuxFxVersion` 设置为 `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10`。

# <a name="portal"></a>[Portal](#tab/portal)

Azure 门户不支持查看和修改函数应用的站点配置设置。 请改用 Azure CLI。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

可使用 Azure CLI 查看和设置 `LinuxFxVersion`。  

若要查看当前的运行时版本，请使用 [az functionapp config show](/cli/azure/functionapp/config) 命令。

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

在此代码中，用函数应用名称替代 `<function_app>`。 此外，还使用函数应用的资源组名称替代 `<my_resource_group>`。 返回当前值 `linuxFxVersion`。

若要更新函数应用中的 `linuxFxVersion` 设置，请使用 [az functionapp config set](/cli/azure/functionapp/config) 命令。

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

将 `<FUNCTION_APP>` 替换为你的函数应用的名称。 此外，还使用函数应用的资源组名称替代 `<RESOURCE_GROUP>`。 另外，如上所述，将 `<LINUX_FX_VERSION>` 替换为特定映像的值。

可以通过在前面代码示例中选择“试一试”运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](/cli/azure/reference-index#az_login) 登录后使用 [Azure CLI 在本地](/cli/azure/install-azure-cli)执行此命令。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

此时无法使用 Azure PowerShell 设置 `linuxFxVersion`。 请改用 Azure CLI。

---

在对站点配置进行更改后，函数应用会重启。

> [!NOTE]
> 对于在消耗计划中运行的应用，将 `LinuxFxVersion` 设置为特定映像可能会增加冷启动时间。 这是因为固定为特定映像会阻止 Functions 使用某些冷启动优化。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在本地开发环境中指定 2.0 运行时](functions-run-local.md)

> [!div class="nextstepaction"]
> [查看运行时版本的发行说明](https://github.com/Azure/azure-webjobs-sdk-script/releases)
