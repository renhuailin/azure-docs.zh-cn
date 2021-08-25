---
title: 快速入门：使用 Go 创建管理组
description: 在本快速入门中，你将使用 Go 创建一个管理组，来将资源整理到资源层次结构中。
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 4949ef10d7bb38ada2faefcee1d6b104c4a08ad7
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322936"
---
# <a name="quickstart-create-a-management-group-with-go"></a>快速入门：使用 Go 创建管理组

管理组是一些容器，可以帮助跨多个订阅管理访问权限、策略和符合性。 可以创建这些容器来构建可以与 [Azure Policy](../policy/overview.md) 和 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)配合使用的有效且高效的层次结构。 若要详细了解管理组，请参阅[使用 Azure 管理组整理资源](overview.md)。

在目录中创建的第一个管理组可能需要最多 15 分钟才能完成。 一些进程会首次运行以在 Azure 中为目录设置管理组服务。 在进程完成后将显示通知。 有关详细信息，请参阅[管理组的初始设置](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- Azure 服务主体，包括 clientId 和 clientSecret 。 如果你没有与 Azure Policy 一起使用的服务主体，或想要创建一个新的服务主体，请参阅[用于 .NET 身份验证的 Azure 管理库](/dotnet/azure/sdk/authentication#mgmt-auth)。
  跳到安装 .NET Core 包的步骤，我们将在接下来的步骤中执行此安装。

- 如果未启用[层次结构保护](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，则租户中的任何 Azure AD 用户即使未分配有管理组写入权限，也可创建管理组。 这个新的管理组将成为根管理组的子级或[默认管理组](./how-to/protect-resource-hierarchy.md#setting---default-management-group)，并将为创建者分配“所有者”角色。 管理组服务允许此功能，因此不需要在根级别分配角色。 创建根管理组时，用户没有访问权限。 为避免在查找 Azure AD 全局管理员以开始使用管理组方面遇到阻碍，我们允许在根级别创建初始管理组。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>添加管理组包

若要使用 Go 来管理管理组，必须添加包。 此包适用于可使用 Go 的任何情况，包括 [Windows 10 上的 bash](/windows/wsl/install-win10) 或本地安装的 bash。

1. 检查是否已安装最新的 Go（至少为 1.15 版）。 如果尚未安装，请在 [Golang.org](https://golang.org/dl/) 下载。

1. 请确保安装最新的 Azure CLI（至少为 2.5.1）。 如果尚未安装，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

   > [!NOTE]
   > 在以下示例中，需要 Azure CLI 来启用 Go 以使用 `auth.NewAuthorizerFromCLI()` 方法。 有关其他选项的信息，请参阅 [Azure SDK for Go - 更多身份验证详细信息](https://github.com/Azure/azure-sdk-for-go#more-authentication-details)。

1. 通过 Azure CLI 进行身份验证。

   ```azurecli
   az login
   ```

1. 在你选择的 Go 环境中，请安装管理组所需的包：

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resources/mgmt/2020-05-01/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>应用程序设置

将 Go 包添加到你选择的环境中后，就可设置能创建管理组的 Go 应用程序了。

1. 创建 Go 应用程序并将以下源保存为 `mgCreate.go`：

   ```go
   package main

   import (
    "context"
    "fmt"
    "os"

    mg "github.com/Azure/azure-sdk-for-go/services/resources/mgmt/2020-05-01/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )

   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]

    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }

    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }

    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. 构建 Go 应用程序：

   ```bash
   go build mgCreate.go
   ```

1. 使用已编译的 Go 应用程序创建管理组。 将 `<Name>` 替换为新的管理组的名称：

   ```bash
   mgCreate "<Name>"
   ```

结果是根管理组中的一个新的管理组。

## <a name="clean-up-resources"></a>清理资源

如果希望从 Go 环境中删除已安装的包，可使用以下命令执行此操作：

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resources/mgmt/2020-05-01/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个管理组来整理资源层次结构。 管理组可以包含订阅或其他管理组。

要详细了解管理组以及如何管理资源层次结构，请继续执行以下操作：

> [!div class="nextstepaction"]
> [使用管理组管理资源](./manage.md)
