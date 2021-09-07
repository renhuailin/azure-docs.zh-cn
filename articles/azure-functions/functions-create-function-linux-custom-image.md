---
title: 在 Linux 上使用自定义映像创建 Azure Functions
description: 了解如何创建在自定义 Linux 映像中运行的 Azure Functions。
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: a6e3ad07f9ba46bd15fe662f370ae2ffc3deb4a8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830491"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>在 Linux 上使用自定义容器创建函数

在本教程中，你将创建代码，并使用 Linux 基础映像将其作为自定义 Docker 容器部署到 Azure Functions。 当函数需要特定的语言版本，或者需要内置映像不提供的特定依赖项或配置时，通常你会使用自定义映像。

::: zone pivot="programming-language-other"
Azure Functions 使用[自定义处理程序](functions-custom-handlers.md)支持任何语言或运行时。 对于某些语言（例如本教程中使用的 R 编程语言），需要安装运行时或其他库作为依赖项，这些依赖项需要使用自定义容器。
::: zone-end

在自定义 Linux 容器中部署函数代码需要[高级计划](functions-premium-plan.md)或[专用（应用服务）计划](dedicated-plan.md)托管。 完成本教程后，你的 Azure 帐户中会产生几美元的成本，你可以在完成后[清理资源](#clean-up-resources)来最大程度地降低该成本。

也可以根据[创建托管在 Linux 上的第一个函数](./create-first-function-cli-csharp.md?pivots=programming-language-python)中所述，使用默认的 Azure 应用服务容器。 Azure Functions 支持的基础映像可以在 [Azure Functions 基础映像存储库](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。

在本教程中，你将了解如何执行以下操作：

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * 使用 Azure Functions Core Tools 创建函数应用和 Dockerfile。
> * 使用 Docker 生成自定义映像。
> * 将自定义映像发布到容器注册表。
> * 在 Azure 中创建函数应用的支持性资源
> * 从 Docker 中心部署函数应用。
> * 将应用程序设置添加到函数应用。
> * 启用持续部署。
> * 与容器建立 SSH 连接。
> * 添加队列存储输出绑定。 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * 使用 Azure Functions Core Tools 创建函数应用和 Dockerfile。
> * 使用 Docker 生成自定义映像。
> * 将自定义映像发布到容器注册表。
> * 在 Azure 中创建函数应用的支持性资源
> * 从 Docker 中心部署函数应用。
> * 将应用程序设置添加到函数应用。
> * 启用持续部署。
> * 与容器建立 SSH 连接。
::: zone-end

可以在运行 Windows、macOS 或 Linux 的任何计算机上按照本教程所述进行操作。 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>创建并测试本地 Functions 项目

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
在终端或命令提示符中，根据所选的语言运行以下命令，在当前文件夹中创建一个函数应用项目。  
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[进程内](#tab/in-process)
```console
func init --worker-runtime dotnet --docker
```

# <a name="isolated-process"></a>[独立进程](#tab/isolated-process)
```console
func init --worker-runtime dotnet-isolated --docker
```
---
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
在空的文件夹中，运行以下命令以从 [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) 生成 Functions 项目。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

`-DjavaVersion` 参数告诉 Functions 运行时要使用哪个 Java 版本。 如果希望函数在 Java 11 上运行，请使用 `-DjavaVersion=11`。 如果不指定 `-DjavaVersion`，则 Maven 默认使用 Java 8。 有关详细信息，请参阅 [Java 版本](functions-reference-java.md#java-versions)。

> [!IMPORTANT]
> 要完成本文中的步骤，`JAVA_HOME` 环境变量必须设置为正确版本的 JDK 的安装位置。

Maven 会请求你提供所需的值，以在部署上完成项目的生成。   
系统提示时提供以下值：

| Prompt | 值 | 说明 |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | 一个值，用于按照 Java 的[包命名规则](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)在所有项目中标识你的项目。 |
| **artifactId** | `fabrikam-functions` | 一个值，该值是 jar 的名称，没有版本号。 |
| **version** | `1.0-SNAPSHOT` | 选择默认值。 |
| **package** | `com.fabrikam.functions` | 一个值，该值是所生成函数代码的 Java 包。 使用默认值。 |

键入 `Y` 或按 Enter 进行确认。

Maven 在名为 artifactId 的新文件夹（在此示例中为 `fabrikam-functions`）中创建项目文件。 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init --worker-runtime custom --docker
```
::: zone-end

`--docker` 选项生成该项目的 `Dockerfile`，其中定义了适合用于 Azure Functions 和所选运行时的自定义容器。

::: zone pivot="programming-language-java"  
导航到项目文件夹：

```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[进程内](#tab/in-process)
无需对 Dockerfile 进行更改。
# <a name="isolated-process"></a>[独立进程](#tab/isolated-process)
打开 Dockerfile，并在第一个 `FROM` 语句后面添加以下行（如果尚不存在）：

```docker
# Build requires 3.1 SDK
COPY --from=mcr.microsoft.com/dotnet/core/sdk:3.1 /usr/share/dotnet /usr/share/dotnet
```
---
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数的唯一名称，`--template` 参数指定该函数的触发器。 `func new` 将在项目中创建一个 C# 代码文件。

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end

::: zone pivot="programming-language-other,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数的唯一名称，`--template` 参数指定该函数的触发器。 `func new` 创建一个与函数名称匹配的子文件夹，该子文件夹包含一个名为 function.json 的配置文件。

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end  
::: zone pivot="programming-language-other" 
在文本编辑器中，在名为 handler.R 的项目文件夹中创建一个文件。 添加以下内容作为其内容。

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

在 host.json 中，修改 `customHandler` 部分以配置自定义处理程序的启动命令。

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

若要在本地测试函数，请启动项目文件夹的根目录中的本地 Azure Functions 运行时主机： 
::: zone pivot="programming-language-csharp"  
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

看到输出中显示了 `HttpExample` 终结点后，请导航到 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器会显示“hello”消息，该消息回显 `Functions`（提供给 `name` 查询参数的值）。

按 **Ctrl**-**C** 停止主机。

## <a name="build-the-container-image-and-test-locally"></a>生成容器映像并在本地测试

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
（可选）检查项目文件夹的根目录中的“Dockerfile”。 Dockerfile 描述了在 Linux 上运行函数应用所需的环境。  Azure Functions 支持的基础映像的完整列表可以在 [Azure Functions 基础映像页](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。
::: zone-end

::: zone pivot="programming-language-other"
检查项目文件夹的根目录中的“Dockerfile”。 Dockerfile 描述了在 Linux 上运行函数应用所需的环境。 自定义处理程序应用程序使用 `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` 映像作为其基础。

修改 Dockerfile 以安装 R。将 Dockerfile 的内容替换为以下内容。

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

在项目根文件夹中运行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令，并提供名称 `azurefunctionsimage` 和标记 `v1.0.0`。 将 `<DOCKER_ID>` 替换为 Docker 中心帐户 ID。 此命令为容器生成 Docker 映像。

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

该命令完成后，可在本地运行新容器。
    
若要测试生成，请使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行本地容器中的映像，并再次将 `<DOCKER_ID` 替换为 Docker ID，同时添加端口参数 `-p 8080:80`：

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp"
# <a name="in-process"></a>[进程内](#tab/in-process)
映像在本地容器中启动后，请浏览到 `http://localhost:8080/api/HttpExample?name=Functions`，其中应该会显示与先前相同的“hello”消息。 由于你创建的 HTTP 触发函数使用匿名授权，因此你无需获取访问密钥即可调用容器中运行的函数。 若要了解详细信息，请参阅[授权密钥]。
# <a name="isolated-process"></a>[独立进程](#tab/isolated-process)
映像在本地容器中启动后，请浏览到 `http://localhost:8080/api/HttpExample`，其中应该会显示与先前相同的问候消息。 由于你创建的 HTTP 触发函数使用匿名授权，因此你无需获取访问密钥即可调用容器中运行的函数。 若要了解详细信息，请参阅[授权密钥]。

---
::: zone-end
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
映像在本地容器中启动后，请浏览到 `http://localhost:8080/api/HttpExample?name=Functions`，其中应该会显示与先前相同的“hello”消息。 由于你创建的 HTTP 触发函数使用匿名授权，因此你无需获取访问密钥即可调用容器中运行的函数。 若要了解详细信息，请参阅[授权密钥]。
::: zone-end  

验证容器中的函数应用后，按 Ctrl+C 停止 Docker 。

## <a name="push-the-image-to-docker-hub"></a>将映像推送到 Docker Hub

Docker Hub 是托管映像并提供映像和容器服务的容器注册表。 若要共享映像（包括将映像部署到 Azure），必须将其推送到注册表。

1. 如果尚未登录到 Docker，请使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令登录（将 `<docker_id>` 替换为你的 Docker ID）。 此命令会提示输入用户名和密码。 如果出现“登录成功”消息，则表示已登录。

    ```console
    docker login
    ```
    
1. 登录后，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令将映像推送到 Docker Hub（同样，请将 `<docker_id>` 替换为你的 Docker ID）。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. 首次推送映像可能需要几分钟时间，具体取决于网络速度（推送后续的更改要快得多）。 在等待过程中，可以继续学习下一部分，并在另一个终端中创建 Azure 资源。

## <a name="create-supporting-azure-resources-for-your-function"></a>创建函数的支持性 Azure 资源

在将函数代码部署到 Azure 之前，需要创建三个资源：

- 一个[资源组](../azure-resource-manager/management/overview.md)：相关资源的逻辑容器。
- 一个[存储帐户](../storage/common/storage-account-create.md)：用于维护有关函数的状态和其他信息。
- 一个函数应用：提供用于执行函数代码的环境。 函数应用映射到本地函数项目，可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

使用以下命令创建这些项。 支持 Azure CLI 和 PowerShell。

1. 请登录到 Azure（如果尚未这样做）：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    使用 [az login](/cli/azure/reference-index#az_login) 命令登录到 Azure 帐户。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 登录到 Azure 帐户。

    ---

1. 在所选区域中创建名为 `AzureFunctionsContainers-rg` 的资源组：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location <REGION>
    ```
 
    [az group create](/cli/azure/group#az_group_create) 命令可创建资源组。 在上述命令中，使用从 [az account list-locations](/cli/azure/account#az_account_list_locations) 命令返回的可用区域代码，将 `<REGION>` 替换为附近的区域。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsContainers-rg -Location <REGION>
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令可创建资源组。 通常，你会在从 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 返回的、离你近的某个可用区域中创建资源组和资源。

    ---

1. 在资源组和区域中创建常规用途存储帐户：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令可创建存储帐户。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsContainers-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 可创建存储帐户。

    ---

    在上一个示例中，将 `<STORAGE_NAME>` 替换为适合你且在 Azure 存储中唯一的名称。 名称只能包含 3 到 24 个数字和小写字母字符。 `Standard_LRS` 指定 [Functions 支持](storage-considerations.md#storage-account-requirements)的常规用途帐户。
    
1. 使用以下命令在你的 `<REGION>` 和 Linux 容器 (`--is-linux`) 中的“弹性高级版 1”定价层 (`--sku EP1`) 中为 Azure Functions 创建名为 `myPremiumPlan` 的高级计划。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location <REGION> --number-of-workers 1 --sku EP1 --is-linux
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```powershell
    New-AzFunctionAppPlan -ResourceGroupName AzureFunctionsContainers-rg -Name MyPremiumPlan -Location <REGION> -Sku EP1 -WorkerType Linux
    ```
    ---
    此处我们使用了可按需缩放的高级计划。 若要了解有关托管的详细信息，请参阅 [Azure Functions 托管计划比较](functions-scale.md)。 若要计算费用，请参阅 [Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。

    该命令还会在同一资源组中预配关联的 Azure Application Insights 实例，可以使用它来监视函数应用和查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>使用映像在 Azure 上创建并配置函数应用

Azure 上的函数应用管理托管计划中函数的执行。 在本部分，你将使用在上一部分创建的 Azure 资源，基于 Docker Hub 中的某个映像创建一个函数应用，然后使用 Azure 存储的连接字符串对其进行配置。

1. 使用以下命令创建函数应用：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp create --name <APP_NAME> --storage-account <STORAGE_NAME> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```

    在 [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令中，deployment-container-image-name 参数指定用于函数应用的映像。 可以使用 [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) 命令查看用于部署的映像的相关信息。 还可以使用 [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) 命令从另一映像进行部署。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -PlanName myPremiumPlan -StorageAccount <STORAGE_NAME> -DockerImageName <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```
    ---
    
    在本示例中，请将 `<STORAGE_NAME>` 替换为在上一部分中用于存储帐户的名称。 另外，请将 `<APP_NAME>` 替换为适合自己的全局唯一名称，并将 `<DOCKER_ID>` 替换为你的 DockerHub ID。    
    
    > [!TIP]  
    > 可以使用 host.json 文件中的 [`DisableColor` 设置](functions-host-json.md#console)来防止将 ANSI 控制字符写入容器日志。 

1. 使用以下命令获取你创建的存储帐户的连接字符串：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv
    ```

    使用 [az storage account show-connection-string](/cli/azure/storage/account) 命令返回存储帐户的连接字符串。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    $storage_name = "glengagtestdockerstorage"
    $key = (Get-AzStorageAccountKey -ResourceGroupName AzureFunctionsContainers-rg -Name $storage_name)[0].Value
    $string = "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=" + $storage_name + ";AccountKey=" + $key
    Write-Output($string) 
    ```
    [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) cmdlet 返回的密钥用于构造存储帐户的连接字符串。

    ---    

    将 `<STORAGE_NAME>` 替换为之前创建的存储帐户的名称。

1. 使用以下命令将此设置添加到函数应用中： 
 
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp config appsettings set --name <APP_NAME> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<CONNECTION_STRING>
    ```
    [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) 命令将创建该设置。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"AzureWebJobsStorage"="<CONNECTION_STRING>"}
    ```
    [Update-AzFunctionAppSetting](/powershell/module/az.functions/update-azfunctionappsetting) cmdlet 将创建该设置。

    ---

    在此命令中，将 `<APP_NAME>` 替换为函数应用的名称，将 `<CONNECTION_STRING>` 替换为上一步中的连接字符串。 该连接应该是一个以 `DefaultEndpointProtocol=` 开头的长编码字符串。
 

1. 该函数现在可以使用此连接字符串来访问存储帐户。

> [!NOTE]    
> 如果将自定义映像发布到专用容器帐户，则应改为在连接字符串的 Dockerfile 中使用环境变量。 有关详细信息，请参阅 [ENV 指令](https://docs.docker.com/engine/reference/builder/#env)。 另外，应设置变量 `DOCKER_REGISTRY_SERVER_USERNAME` 和 `DOCKER_REGISTRY_SERVER_PASSWORD`。 若要使用这些值，必须重新生成映像，将映像推送到注册表，然后在 Azure 上重启函数应用。

## <a name="verify-your-functions-on-azure"></a>在 Azure 上验证函数

将映像部署到 Azure 中的函数应用后，可以像以前一样通过 HTTP 请求调用函数。
在浏览器中，导航到如下所示的 URL：

::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`  
::: zone-end  
::: zone pivot="programming-language-csharp"  
# <a name="in-process"></a>[进程内](#tab/in-process) 
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`
# <a name="isolated-process"></a>[独立进程](#tab/isolated-process)
`https://<APP_NAME>.azurewebsites.net/api/HttpExample`

---
:::zone-end  

将 `<APP_NAME>` 替换为你的函数应用的名称。 导航到此 URL 时，浏览器显示的输出应与在本地运行函数时显示的输出类似。

## <a name="enable-continuous-deployment-to-azure"></a>启用到 Azure 的持续部署

可以启用 Azure Functions，以便每次更新注册表中的映像时，都自动更新该映像的部署。

1. 使用以下命令启用持续部署并获取 Webhook URL：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <APP_NAME> --resource-group AzureFunctionsContainers-rg
    ```
    
    [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) 命令将启用持续部署并返回部署 Webhook URL。 以后随时可以使用 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url) 命令检索此 URL。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"DOCKER_ENABLE_CI" = "true"}
    Get-AzWebAppContainerContinuousDeploymentUrl -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg
    ```
    
    `DOCKER_ENABLE_CI` 应用程序设置控制是否从容器存储库启用持续部署。  [Get-AzWebAppContainerContinuousDeploymentUrl](/powershell/module/az.websites/get-azwebappcontainercontinuousdeploymenturl) cmdlet 将返回部署 Webhook 的 URL。

    ---    

    如前所述，将 `<APP_NAME>` 替换为函数应用名称。 

1. 将部署 Webhook URL 复制到剪贴板。

1. 打开 [Docker Hub](https://hub.docker.com/) 并登录，然后在导航栏上选择“存储库”。 找到并选择映像，选择“Webhook”选项卡，指定一个 **Webhook 名称**，将 URL 粘贴到“Webhook URL”中，然后选择“创建”：

    ![将 Webhook 添加到 DockerHub 存储库中](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. 设置 Webhook 后，每当在 Docker Hub 中更新映像时，Azure Functions 就会重新部署该映像。

## <a name="enable-ssh-connections"></a>启用 SSH 连接

SSH 实现容器和客户端之间的安全通信。 启用 SSH 后，可以使用应用服务高级工具 (Kudu) 连接到容器。 为了便于使用 SSH 连接到容器，Azure Functions 提供了已启用 SSH 的基础映像。 只需编辑 Dockerfile，然后重新生成并重新部署映像即可。 然后，可以通过高级工具 (Kudu) 连接到容器

1. 在 Dockerfile 中，将字符串 `-appservice` 追加到 `FROM` 指令中的基础映像：

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. 使用 `docker build` 命令重新生成映像（同样，请将 `<docker_id>` 替换为你的 Docker ID）：

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. 将更新的映像推送到 Docker Hub。此过程所需的时间远远少于首次推送，因为它只需上传更新的映像段。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions 会自动将映像重新部署到 Functions 应用；此过程在一分钟内即可完成。

1. 在浏览器中打开 `https://<app_name>.scm.azurewebsites.net/`（请将 `<app_name>` 替换为你的唯一名称）。 此 URL 是函数应用容器的高级工具 (Kudu) 终结点。

1. 登录到你的 Azure 帐户，然后选择“SSH”以便与容器建立连接。 如果 Azure 仍在更新容器映像，则连接可能需要一段时间。

1. 与容器建立连接后，运行 `top` 命令查看当前正在运行的进程。 

    ![在 SSH 会话中运行的 Linux top 命令](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-azure-queue-storage"></a>写入 Azure 队列存储

借助 Azure Functions，无需自行编写集成代码即可将函数连接到其他 Azure 服务和资源。 这些绑定表示输入和输出，在函数定义中声明。 绑定中的数据作为参数提供给函数。 触发器是一种特殊类型的输入绑定。 尽管一个函数只有一个触发器，但它可以有多个输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

本部分介绍如何将函数与 Azure 队列存储集成。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到队列中的消息。

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>注册绑定扩展
::: zone-end 

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>添加使用输出绑定的代码

定义队列绑定后，可以更新函数，以使用绑定参数将消息写入队列。
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>更新注册表中的映像

1. 在根文件夹中再次运行 `docker build`，但这次请将标记中的版本更新为 `v1.0.1`。 如前所述，将 `<docker_id>` 替换为你的 Docker Hub 帐户 ID：

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. 使用 `docker push` 将更新的映像推回到存储库：

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. 由于已配置持续交付，因此再次更新注册表中的映像会自动更新 Azure 中的函数应用。

## <a name="view-the-message-in-the-azure-storage-queue"></a>查看 Azure 存储队列中的消息

在浏览器中，使用前面所述的同一 URL 来调用函数。 浏览器应会显示与前面相同的响应，因为尚未修改函数代码的该部分。 但是，添加的代码已使用 `name` URL 参数将消息写入 `outqueue` 存储队列。

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>清理资源

若要继续使用在本教程中创建的资源来操作 Azure Functions，可以保留所有这些资源。 由于为 Azure Functions 创建了高级计划，因此每天会产生一到两美元的费用。

若要避免持续性的费用，请删除 `AzureFunctionsContainer-rg` 资源组以清理其中的所有资源： 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>后续步骤

+ [监视函数](functions-monitoring.md)
+ [缩放和托管选项](functions-scale.md)
+ [基于 Kubernetes 的无服务器托管](functions-kubernetes-keda.md)

[授权密钥]: functions-bindings-http-webhook-trigger.md#authorization-keys