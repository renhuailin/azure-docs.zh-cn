---
title: 教程：用于调用 Azure Functions 的托管标识
description: 使用托管标识从 Azure Spring Cloud 应用调用 Azure Functions
author: karlerickson
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 46efd49a809d8e13fe24045b7575b37f3aff3173
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015367"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>教程：使用托管标识从 Azure Spring Cloud 应用调用 Azure Functions

本文介绍如何为 Azure Spring Cloud 应用创建托管标识并用它来调用 HTTP 触发的 Functions。

Azure Functions 和应用服务都内置了对 Azure Active Directory (Azure AD) 身份验证的支持。 通过利用此内置身份验证功能以及 Azure Spring Cloud 的托管标识，我们可使用新式 OAuth 语义调用 RESTful 服务。 此方法不需要在代码中存储机密，并提供更精细的控制来控制对外部资源的访问。

## <a name="prerequisites"></a>先决条件

* [注册 Azure 订阅](https://azure.microsoft.com/free/)
* [安装 Azure CLI 2.0.67 或更高版本](/cli/azure/install-azure-cli)
* [安装 Maven 3.0 或更高版本](https://maven.apache.org/download.cgi)
* [安装 Azure Functions Core Tools 版本 3.0.2009 或更高版本](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [az group create](/cli/azure/group#az_group_create) 命令创建一个资源组，以同时包含函数应用和 Spring Cloud：

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-function-app"></a>创建函数应用

若要创建函数应用，必须先使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令创建一个后备存储帐户：

> [!Important]
> 每个函数应用和存储帐户都必须具有唯一名称。 在下面的示例中，将 \<your-functionapp-name> 替换为函数应用的名称，将 \<your-storageaccount-name> 替换为存储帐户的名称 。

```azurecli
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

创建存储帐户后，可创建函数应用。

```azurecli
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

记下返回的 hostNames（其格式将为“https://\<your-functionapp-name>.azurewebsites.net”）。 将在之后的步骤中用到它。

## <a name="enable-azure-active-directory-authentication"></a>启用 Azure Active Directory 身份验证

通过 [Azure 门户](https://portal.azure.com)访问新创建的函数应用，然后从设置菜单中选择“身份验证/授权”。 启用应用服务身份验证，并将“请求未经验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。 此设置将确保拒绝所有未经身份验证的请求（401 响应）。

![将 Azure Active Directory 显示为默认提供程序的身份验证设置](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

在“身份验证提供程序”下，选择 Azure Active Directory 来配置应用程序注册。 选择快速管理模式将自动在 Azure AD 租户中创建具有正确配置的应用程序注册。

![Azure Active Directory 提供程序设置为快速管理模式](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

保存设置后，函数应用将重启，并将提示所有后续请求通过 Azure AD 进行登录。 可导航到函数应用根 URL（在上述步骤中的“hostNames”输出中返回），测试现在是否已拒绝未经身份验证的请求。 你应会被重定向到组织 Azure AD 登录屏幕。

## <a name="create-an-http-triggered-function"></a>创建 Http 触发的函数

在空的本地目录中，创建新的函数应用并添加 Http 触发的函数。

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

默认情况下，Functions 使用基于密钥的身份验证来保护 Http 终结点。 我们将启用 Azure AD 身份验证来保护对 Functions 的访问，因此我们想要在 function.json 文件中[将函数身份验证级别设置为匿名](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

现可将应用发布到上一步中创建的[函数应用](#create-a-function-app)实例。

```console
func azure functionapp publish <your-functionapp-name>
```

publish 命令的输出应会列出新创建的函数的 URL。

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```

## <a name="create-azure-spring-cloud-service-and-app"></a>创建 Azure Spring Cloud 服务和应用

安装 spring-cloud 扩展后，使用 Azure CLI 命令 `az spring-cloud create` 创建一个 Azure Spring Cloud 实例。

```azurecli
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

以下示例按 `--assign-identity` 参数的请求，创建名为 `msiapp` 且已启用系统分配托管标识的应用。

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>构建示例 Spring Boot 应用以调用函数

此示例将首先从 [MSI 终结点](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http)请求访问令牌并使用该令牌对函数 http 请求进行身份验证，从而调用 Http 触发的函数。

1. 克隆示例项目。

    ```bash
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. 在应用属性中指定函数 URI 和触发器名称。

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    要对 Azure Spring Cloud 应用使用托管标识，请将具有以下内容的属性添加到“src/main/resources/application.properties”。

    ```properties
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. 打包示例应用。

    ```bash
    mvn clean package
    ```

4. 现在，使用 Azure CLI 命令 `az spring-cloud app deploy` 将应用部署到 Azure。

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. 访问公共终结点或测试终结点以测试应用。

    ```bash
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    你将在响应正文中看到以下消息。

    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```

    可更改路径参数，尝试将不同的值传递给函数。

## <a name="next-steps"></a>后续步骤

* [如何为 Azure Spring Cloud 应用程序启用系统分配的托管标识](./how-to-enable-system-assigned-managed-identity.md)
* [了解有关 Azure 资源的托管标识的详细信息](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [配置客户端应用以访问应用服务](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
