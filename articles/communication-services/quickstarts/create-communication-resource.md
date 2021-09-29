---
title: 快速入门 - 在 Azure 通信服务中创建和管理资源
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入门中，你将了解如何创建和管理你的第一个 Azure 通信服务资源。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: arm
zone_pivot_groups: acs-plat-azp-azcli-net-ps
ms.openlocfilehash: e3be24cda914edbf4f3ae3474ff43eef438fce20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635634"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>快速入门：创建和管理通信服务资源

通过预配第一个通信服务资源来开始使用 Azure 通信服务。 可以通过 [Azure 门户](https://portal.azure.com)或 .NET 管理 SDK 预配通信服务资源。 利用管理 SDK 和 Azure 门户，可以通过 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)（Azure 的部署和管理服务）创建、配置、更新和删除资源和接口。 该 SDK 中提供的所有功能都可在 Azure 门户中使用。 


> [!WARNING]
> 请注意，虽然在多个地理区域提供通信服务，但若要获得电话号码，资源必须有一个设置为“美国”的数据位置。 另请注意，不能同时创建一个资源组作为 Azure 通信服务的资源。 创建资源时，必须使用已创建的资源组。

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-azcli"
[!INCLUDE [Azure CLI](./includes/create-resource-azcli.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

::: zone pivot="platform-powershell"
[!INCLUDE [PowerShell](./includes/create-resource-powershell.md)]
::: zone-end


## <a name="access-your-connection-strings-and-service-endpoints"></a>访问连接字符串和服务终结点

通信服务 SDK 可以使用连接字符串连接到 Azure 并向其进行身份验证。 你可以从 Azure 门户或使用 Azure 资源管理器 API 以编程方式访问通信服务连接字符串和服务终结点。

导航到通信服务资源之后，请从导航菜单中选择“密钥”并复制“连接字符串”或“终结点”值供通信服务 SDK 使用  。 请注意，你可以访问主密钥和辅助密钥。 在你希望向第三方或过渡环境提供对通信服务资源的临时访问权限的方案中，这可能会十分有用。

:::image type="content" source="./media/key.png" alt-text="通信服务密钥页的屏幕截图。":::

还可以使用 Azure CLI 访问密钥信息（例如，资源组或特定资源的密钥）。 

安装 [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli) 并使用以下命令登录。 需要提供凭据才能连接到 Azure 帐户。
```azurecli
az login
```

现在，你可以访问有关资源的重要信息了。
```azurecli
az communication list --resource-group "<resourceGroup>"

az communication list-key --name "<communicationName>" --resource-group "<resourceGroup>"
```

如果要选择特定订阅，还可以指定 ```--subscription``` 标志并提供订阅 ID。
```
az communication list --resource-group  "resourceGroup>"  --subscription "<subscriptionID>"

az communication list-key --name "<communicationName>" --resource-group "resourceGroup>" --subscription "<subscriptionID>"
```

## <a name="store-your-connection-string"></a>存储连接字符串

通信服务 SDK 使用连接字符串对向通信服务发出的请求进行授权。 可使用多个选项来存储连接字符串：

* 在桌面或设备上运行的应用程序可在 **app.config** 或 **web.config** 文件中存储连接字符串。 将连接字符串添加到这些文件中的 **AppSettings** 节。
* 在 Azure 应用服务中运行的应用程序可以将连接字符串存储在[应用服务应用程序设置](../../app-service/configure-common.md)中。 在门户中将连接字符串添加到“应用程序设置”选项卡的“连接字符串”部分。
* 可以将连接字符串存储在 [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md) 中。
* 如果在本地运行应用程序，则可能要将连接字符串存储在环境变量中。

### <a name="store-your-connection-string-in-an-environment-variable"></a>将连接字符串存储在环境变量中

若要配置环境变量，请打开控制台窗口，然后从以下选项卡中选择操作系统。 将 `<yourconnectionstring>` 替换为实际的连接字符串。

#### <a name="windows"></a>[Windows](#tab/windows)

打开控制台窗口并输入以下命令：

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

添加环境变量后，可能需要重启任何正在运行的、需要读取环境变量的程序（包括控制台窗口）。 例如，如果使用 Visual Studio 作为编辑器，请在运行示例之前重启 Visual Studio。

#### <a name="macos"></a>[macOS](#tab/unix)

编辑 .zshrc，然后添加环境变量：

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

添加环境变量后，请从控制台窗口运行 `source ~/.zshrc`，使更改生效。 如果在 IDE 打开的情况下创建了环境变量，则可能需要关闭编辑器、IDE 或 shell，然后再重新打开才能访问该变量。

#### <a name="linux"></a>[Linux](#tab/linux)

编辑 .bash_profile，然后添加环境变量：

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

添加环境变量后，请从控制台窗口运行 `source ~/.bash_profile`，使更改生效。 如果在 IDE 打开的情况下创建了环境变量，则可能需要关闭编辑器、IDE 或 shell，然后再重新打开才能访问该变量。

---

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 

如果在删除资源时已为资源分配了任何电话号码，则会同时自动从资源释放电话号码。 

> [!Note]
> 资源删除是永久性的。如果删除资源，则无法恢复任何数据，包括事件网格筛选器、电话号码或与资源相关的其他数据。

## <a name="next-steps"></a>后续步骤

在此快速入门中，你学习了如何：

> [!div class="checklist"]
> * 创建通信服务资源
> * 配置资源地理位置和标记
> * 访问该资源的密钥
> * 删除资源

> [!div class="nextstepaction"]
> [创建第一个用户访问令牌](access-tokens.md)
