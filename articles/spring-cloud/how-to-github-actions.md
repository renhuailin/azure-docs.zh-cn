---
title: Azure Spring Cloud CI/CD 与 GitHub Actions
description: 如何通过 GitHub Actions 来增进 Azure Spring Cloud 的 CI/CD 工作流
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2cdbec553909b354cc85e15d2b33c731c67e12b7
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015514"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD 与 GitHub Actions

GitHub Actions 支持自动化的软件开发生命周期工作流。 通过适用于 Azure Spring Cloud 的 GitHub Actions，可以在存储库中创建工作流来生成、测试、打包、发布并部署到 Azure。

## <a name="prerequisites"></a>先决条件

此示例需要 [Azure CLI](/cli/azure/install-azure-cli)。

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>设置 GitHub 存储库并进行身份验证

需要使用 Azure 服务主体凭据来为 Azure 登录操作授权。 若要获取 Azure 凭据，请在本地计算机上执行以下命令：

```azurecli
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

若要访问某个特定的资源组，可以缩小范围：

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

该命令应该会输出一个 JSON 对象：

```json
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

此示例使用 [GitHub 上的 steeltoe 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)。  创建存储库分支，打开分支的 GitHub 存储库页，然后选择“设置”选项卡。打开“机密”菜单，然后选择“新建机密”：

![添加新机密](./media/github-actions/actions1.png)

将机密名称设置为 `AZURE_CREDENTIALS`，并将其值设置为在标题“设置 GitHub 存储库并进行身份验证”下找到的 JSON 字符串。

![设置机密数据](./media/github-actions/actions2.png)

也可以在 GitHub Actions 中从 Key Vault 获取 Azure 登录凭据，如[在 GitHub Actions 中向 Key Vault 进行 Azure Spring 身份验证](./github-actions-key-vault.md)中所述。

## <a name="provision-service-instance"></a>预配服务实例

若要预配 Azure Spring Cloud 服务实例，请使用 Azure CLI 运行以下命令。

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>生成工作流

工作流是使用以下选项定义的。

### <a name="prepare-for-deployment-with-azure-cli"></a>使用 Azure CLI 准备部署

`az spring-cloud app create` 命令目前不是幂等的。 运行一次之后，如果再次运行相同的命令，则会看到错误。 建议将此工作流用于现有的 Azure Spring Cloud 应用和实例。

请使用以下 Azure CLI 命令来进行准备：

```azurecli
az config set defaults.group=<service group name>
az config set defaults.spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>直接使用 Azure CLI 进行部署

在存储库中使用以下内容创建 .github/workflows/main.yml 文件。 将 \<your resource group name> 和 \<your service name> 替换为正确的值 。

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>

    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}

      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes

      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```

::: zone-end

::: zone pivot="programming-language-java"

## <a name="set-up-github-repository-and-authenticate"></a>设置 GitHub 存储库并进行身份验证

需要使用 Azure 服务主体凭据来为 Azure 登录操作授权。 若要获取 Azure 凭据，请在本地计算机上执行以下命令：

```azurecli
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

若要访问某个特定的资源组，可以缩小范围：

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

该命令应该会输出一个 JSON 对象：

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

此示例使用 GitHub 上的 [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) 示例。  对于该示例，请打开 GitHub 存储库页，然后选择“设置”选项卡。打开“机密”菜单，然后选择“添加新机密”  ：

![添加新机密](./media/github-actions/actions1.png)

将机密名称设置为 `AZURE_CREDENTIALS`，并将其值设置为在标题“设置 GitHub 存储库并进行身份验证”下找到的 JSON 字符串。

![设置机密数据](./media/github-actions/actions2.png)

也可以在 GitHub Actions 中从 Key Vault 获取 Azure 登录凭据，如[在 GitHub Actions 中向 Key Vault 进行 Azure Spring 身份验证](./github-actions-key-vault.md)中所述。

## <a name="provision-service-instance"></a>预配服务实例

若要预配 Azure Spring Cloud 服务实例，请使用 Azure CLI 运行以下命令。

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```

## <a name="build-the-workflow"></a>生成工作流

工作流是使用以下选项定义的。

### <a name="prepare-for-deployment-with-azure-cli"></a>使用 Azure CLI 准备部署
`az spring-cloud app create` 命令目前不是幂等的。  建议将此工作流用于现有的 Azure Spring Cloud 应用和实例。

请使用以下 Azure CLI 命令来进行准备：

```azurecli
az config set defaults.group=<service group name>
az config set defaults.spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>直接使用 Azure CLI 进行部署

在存储库中创建 .github/workflow/main.yml 文件：

```yaml
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud

    - name: Deploy with AZ CLI commands
      run: |
        az config set defaults.group=$GROUP
        az config set defaults.spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```

### <a name="deploy-with-azure-cli-action"></a>使用 Azure CLI action 进行部署

Az `run` 命令将使用最新版本的 Azure CLI。 如果有重大更改，也可以将特定版本的 Azure CLI 与 azure/CLI `action` 配合使用。

> [!Note]
> 此命令将会在一个新容器中运行，所以 `env` 将不起作用，并且跨操作文件访问可能会有额外限制。

在存储库中创建 .github/workflow/main.yml 文件：

```yaml
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az config set defaults.group=<service group name>
          az config set defaults.spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>使用 Maven 插件进行部署

另一种选择是使用 [Maven 插件](./quickstart.md)来部署 Jar 并更新应用设置。 `mvn azure-spring-cloud:deploy` 命令是幂等的，将会在需要时自动创建应用。 你无需提前创建相应的应用。

```yaml
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@main

    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: maven build, clean
      run: |
        mvn clean package -DskipTests

    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>运行工作流

在将 .github/workflow/main.yml 推送到 GitHub 后，GitHub Actions 应该会自动启用。 在推送新提交时，将会触发该操作。 如果你在浏览器中创建此文件，你的操作应该已经运行了。

若要验证是否已启用操作，请选择 GitHub 存储库页上的“操作”选项卡：

![验证操作是否已启用](./media/github-actions/actions3.png)

如果在存在错误的情况下（例如，如果尚未设置 Azure 凭据）运行操作，则可以在修复该错误后重新运行检查。 在 GitHub 存储库页上，选择“操作”，选择特定的工作流任务，然后选择“重新运行检查”按钮来重新运行检查 ：

![重新运行检查](./media/github-actions/actions4.png)

## <a name="next-steps"></a>后续步骤

* [用于 Spring Cloud GitHub Actions 的 Key Vault](./github-actions-key-vault.md)
* [Azure Active Directory 服务主体](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [适用于 Azure 的 GitHub Actions](https://github.com/Azure/actions/)
