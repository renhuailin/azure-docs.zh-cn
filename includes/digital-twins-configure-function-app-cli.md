---
author: baanders
description: include 文件介绍如何配置 Azure 函数以使用 Azure 数字孪生 - CLI 说明
ms.service: digital-twins
ms.topic: include
ms.date: 7/20/2021
ms.author: baanders
ms.openlocfilehash: fffa878ca69b0704417c7db7492eb5523217e4e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722338"
---
在 [Azure Cloud Shell](https://shell.azure.com) 或[本地 Azure CLI](/cli/azure/install-azure-cli) 中运行以下命令。

#### <a name="assign-an-access-role"></a>分配访问角色

Azure 函数需要将持有者令牌传递给它。 为确保传递了持有者令牌，请为函数应用授予 Azure 数字孪生实例的“Azure 数字孪生数据所有者”角色，这将授予函数应用在实例上执行数据平面活动的权限。

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

1. 使用以下命令查看函数的[系统托管标识](../articles/active-directory/managed-identities-azure-resources/overview.md)的详细信息。 记下输出中的 `principalId` 字段。 你将使用此 ID 来引用函数，以便可以在下一步中授予其权限。

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > 如果结果为空而不是显示标识详细信息，请使用以下命令为函数创建新的系统托管标识：
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > 输出会显示标识的详细信息，包括下一步所需的 `principalId` 值。 

1. 使用以下命令中的 `principalId` 值为函数赋予 Azure 数字孪生实例的“Azure 数字孪生数据所有者”角色。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

#### <a name="configure-application-settings"></a>配置应用程序设置

接下来，通过为函数设置[环境变量](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal#use-application-settings)使其可以访问 Azure 数字孪生实例的 URL。

> [!TIP]
> 通过将 https:// 添加到实例主机名的开头，创建 Azure 数字孪生实例的 URL。 若要查看主机名以及实例的所有属性，请运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>`。

以下命令为实例的 URL 设置环境变量，函数在需要访问实例时将使用该环境变量。

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```