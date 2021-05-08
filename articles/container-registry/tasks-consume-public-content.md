---
title: 用于管理公共注册表内容的任务工作流
description: 创建自动化 Azure 容器注册表任务工作流，用于在专用 Azure 容器注册表中跟踪、管理和消耗公共映像内容。
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349276"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>如何使用 Azure 容器注册表任务消耗并维护公共内容

本文提供了 Azure 容器注册表中的示例工作流，以帮助你管理公共内容的消耗和维护：

1. 导入所依赖的公共映像的本地副本。
1. 通过安全扫描和功能测试来验证公共映像。
1. 将映像提升到专用注册表供内部使用。
1. 为依赖于公共内容的应用程序触发基础映像更新。
1. 使用 [Azure 容器注册表任务](container-registry-tasks-overview.md)自动执行此工作流。

下图汇总了此工作流：

![消耗公共内容工作流](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

门控式导入工作流帮助你管理组织对外部托管项目（例如，源自公共注册表的映像，这些公共注册表包括 [Docker Hub][docker-hub]、[GCR][gcr]、[Quay][quay]、[GitHub 容器注册表][ghcr]、[Microsoft 容器注册表][mcr]甚至其他 [Azure 容器注册表][acr]）的依赖性。 

若要了解依赖于公共内容所带来的风险的背景信息以及如何使用 Azure 容器注册表来缓解这些风险，请参阅[消耗公共内容的 OCI 博客文章][oci-consuming-public-content]和[使用 Azure 容器注册表管理公共内容](buffer-gate-public-content.md)。

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装来完成本演练。 建议使用 Azure CLI 2.10 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-cli]。

## <a name="scenario-overview"></a>方案概述

![导入工作流组件](./media/tasks-consume-public-content/consuming-public-content-objects.png)

本演练设置：

1. 三个 **容器注册表**，表示：
   * 模拟的 [Docker Hub][docker-hub] (`publicregistry`)，支持更改基础映像
   * 用于共享专用映像的团队注册表 (`contoso`)
   * 导入的公共内容的公司/团队共享注册表 (`baseartifacts`)
1. 每个注册表中的 **ACR 任务**。 这些任务：  
   1. 构建模拟的公共 `node` 映像
   1. 将 `node` 映像导入到公司/团队共享注册表并对其进行验证
   1. 构建并部署 `hello-world` 映像
1. **ACR 任务定义**，其中包括以下项的配置：
1. **注册表凭据** 的集合，它们是指向密钥保管库的指针
1. `acr-task.yaml` 中提供的 **机密** 的集合，它们是指向密钥保管库的指针
1. `acr-task.yaml` 中使用的 **已配置值** 的集合
1. 一个用来保护所有机密的 **Azure 密钥保管库**
1. 一个承载 `hello-world` 生成应用程序的 **Azure 容器实例**

## <a name="prerequisites"></a>先决条件

以下步骤配置在演练中创建和使用的资源的值。

### <a name="set-environment-variables"></a>设置环境变量

配置你的环境特有的变量。 我们会遵循最佳做法，将具有持久性内容的资源置于其自己的资源组中，以最大程度地减少意外删除情况。 不过，你可以根据需要将其放在单个资源组中。

本文中的示例针对 Bash shell 设置了格式。

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git 存储库和令牌

若要模拟你的环境，请为以下每个 Git 存储库创建分支以获得你可以管理的存储库。 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

然后，为你的分支存储库更新下列变量：

追加到 git URL 末尾的 `:main` 表示默认存储库分支。

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

你需要一个适用于 ACR 任务的 [GitHub 访问令牌 (PAT)][git-token]，才能克隆和建立 Git Webhook。 如需通过相关步骤创建一个具有访问专用存储库所需权限的令牌，请参阅[创建 GitHub 访问令牌](container-registry-tutorial-build-task.md#create-a-github-personal-access-token)。 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Docker Hub 凭据  
若要避免在从 Docker Hub 拉取映像时出现限制和标识请求，请创建一个 [Docker Hub 令牌][docker-hub-tokens]。 然后设置以下环境变量：

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>创建注册表

使用 Azure CLI 命令创建三个高级层容器注册表，每个表都位于其自己的资源组中：

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>创建密钥保管库并设置机密

创建密钥保管库：

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

在密钥保管库中设置 Docker Hub 用户名和令牌：

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

在密钥保管库中设置并验证 Git PAT：

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>为 Azure 容器实例创建资源组

部署 `hello-world` 映像时，会在后续任务中使用此资源组。

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>创建公共 `node` 基础映像

若要在 Docker Hub 上模拟 `node` 映像，请创建一个 [ACR 任务][acr-task]来构建和维护公共映像。 此设置允许 `node` 映像维护人员模拟更改。

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

若要避免 Docker 限制，请将 [Docker Hub 凭据][docker-hub-tokens]添加到任务中。 可以使用 [acr task credentials][acr-task-credentials] 命令将 Docker 凭据传递给任何注册表，包括 Docker Hub。

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

授予任务从密钥保管库读取值所需的访问权限：

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

可以通过 Git 提交、基础映像更新、计时器或手动运行来[触发任务][acr-task-triggers]。 

手动运行任务以生成 `node` 映像：

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

列出模拟的公共注册表中的映像：

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>创建 `hello-world` 映像

根据模拟的公共 `node` 映像生成 `hello-world` 映像。

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>创建用于对模拟的公共注册表进行拉取访问的令牌

创建模拟的公共注册表的[访问令牌][acr-tokens]，其作用域为 `pull`。 然后，在密钥保管库中对其进行设置：

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>按 Azure 容器实例创建用于拉取访问的令牌

为承载 `hello-world` 映像的注册表创建[访问令牌][acr-tokens]，其作用域为拉取。 然后，在密钥保管库中对其进行设置：

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>创建用于生成和维护 `hello-world` 映像的任务

以下命令基于 `hello-world` 存储库的 `acr-tasks.yaml` 中的定义创建任务。 任务步骤生成 `hello-world` 映像，然后将其部署到 Azure 容器实例。 Azure 容器实例的资源组已在前面的一个部分创建。 通过在任务中调用 `az container create`（只是在 `image:tag` 中有差别），任务在整个演练中将部署到同一实例。

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

向任务添加用于模拟的公共注册表的凭据：

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

授予任务从密钥保管库读取值所需的访问权限：

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

通过向任务授予对资源组的访问权限，授予任务创建和管理 Azure 容器实例所需的访问权限：

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

创建并配置任务后，运行任务以生成并部署 `hello-world` 映像：

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

创建后，获取承载 `hello-world` 映像的容器的 IP 地址。

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

在浏览器中，转到该 IP 地址以查看正在运行的应用程序。

## <a name="update-the-base-image-with-a-questionable-change"></a>使用“可疑”更改更新基础映像

本部分模拟一项可能会导致环境中出现问题的基础映像更改。

1. 打开分支的 `base-image-node` 存储库中的 `Dockerfile`。
1. 将 `BACKGROUND_COLOR` 更改为 `Orange` 以模拟更改。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

提交更改并监视那些自动开始生成的 ACR 任务。

监视要开始执行的任务：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

最终，你应当会看到基于触发器 `Commit` 的状态“`Succeeded`”：

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

键入 **Ctrl + C** 退出监视命令，然后查看最近的运行的日志：

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

在 `node` 映像完成后，ACR 任务的 `watch` 会动开始构建 `hello-world` 映像：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

最终，你应当会看到基于触发器 `Image Update` 的状态“`Succeeded`”：

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

键入 **Ctrl + C** 退出监视命令，然后查看最近的运行的日志：

```azurecli-interactive
az acr task logs -r $REGISTRY
```

完成后，获取承载更新后的 `hello-world` 映像的站点的 IP 地址：

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

在浏览器中转到该站点，该站点应该具有橙色（可疑）背景。

### <a name="checking-in"></a>签入

此时，你已创建了一个 `hello-world` 映像，该映像是基于 Git 提交和对基础 `node` 映像的更改自动构建的。 在此示例中，任务基于 Azure 容器注册表中的基础映像进行构建，但任何受支持的注册表都可以使用。

更新 `node` 映像时，基础映像更新会自动触发任务运行。 在这里可以看到，并非所有更新都是需要的。

## <a name="gated-imports-of-public-content"></a>公共内容的门控式导入

若要防止上游更改损坏关键工作负荷，可以添加安全扫描和功能测试。

在本部分，我们创建一个 ACR 任务来执行以下操作：

* 构建测试映像
* 针对测试映像运行功能测试脚本 `./test.sh`
* 如果映像测试成功，请将公共映像导入到 **baseimages** 注册表

### <a name="add-automation-testing"></a>添加自动化测试

为了对任何上游内容进行控制，将实施自动化测试。 在此示例中，提供了一个用于检查 `$BACKGROUND_COLOR` 的 `test.sh`。 如果测试失败，将返回 `EXIT_CODE` `1`，这会导致 ACR 任务步骤失败，从而结束任务运行。 这些测试可以在任何形式的工具中进行扩展，其中包括日志记录结果。 此入口是通过脚本中的“通过/失败”响应进行管理的，此处重现了该响应：

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>任务 YAML 

查看 `import-baseimage-node` 存储库中的 `acr-task.yaml`，它执行以下步骤：

1. 使用以下 Dockerfile 构建测试基础映像：
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. 完成后，通过运行容器来验证映像，这将运行 `./test.sh`
1. 只有在成功完成后才运行导入步骤，这些步骤通过 `when: ['validate-base-image']` 进行控制

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>创建用于导入和测试基础映像的任务

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

向任务添加用于模拟的公共注册表的凭据：

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

授予任务从密钥保管库读取值所需的访问权限：

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

运行导入任务：

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> 如果任务由于 `./test.sh: Permission denied` 而失败，请确保该脚本具有执行权限，然后重新提交到 Git 存储库：
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>更新 `hello-world` 映像以基于门控式 `node` 映像进行构建

创建[访问令牌][acr-tokens]，以从 `node` 存储库访问作用域为 `read` 的基础项目注册表。 然后，在密钥保管库中进行设置：

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

向 **hello-world** 任务添加用于基础项目注册表的凭据：

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

通过更新任务来更改 `REGISTRY_FROM_URL`，以使用 `BASE_ARTIFACTS` 注册表

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

运行 **hello world** 任务来更改其基础映像依赖项：

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>使用“有效”更改更新基础映像

1. 打开 `base-image-node` 存储库中的 `Dockerfile`。
1. 将 `BACKGROUND_COLOR` 更改为 `Green` 以模拟有效的更改。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

提交更改并监视更新序列：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

运行后，键入 **Ctrl + C** 并监视日志：

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

完成后，监视 **base-image-import** 任务：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

运行后，键入 **Ctrl + C** 并监视日志：

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

完成后，监视 **hello-world** 任务：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

运行后，键入 **Ctrl + C** 并监视日志：

```azurecli-interactive
az acr task logs -r $REGISTRY
```

完成后，获取承载更新后的 `hello-world` 映像的站点的 IP 地址：

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

在浏览器中转到该站点，该站点应当具有绿色（有效）背景。

### <a name="view-the-gated-workflow"></a>查看门控式工作流

再次执行前一部分中的步骤，其背景色为红色。

1. 打开 `base-image-node` 存储库中的 `Dockerfile`
1. 将 `BACKGROUND_COLOR` 更改为 `Red` 以模拟无效的更改。

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

提交更改并监视更新序列：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

运行后，键入 **Ctrl + C** 并监视日志：

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

完成后，监视 **base-image-import** 任务：

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

运行后，键入 **Ctrl + C** 并监视日志：

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

此时，应会看到 **base-import-node** 任务未通过验证并停止发布 `hello-world` 更新所需的序列。 输出类似于：

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>发布对 `hello-world` 的更新

对 `hello-world` 映像的更改会继续使用上次验证的 `node` 映像。

通过了门控式验证的对基础 `node` 映像的任何其他更改都会触发对 `hello-world` 映像的基础映像更新。

## <a name="cleaning-up"></a>清理

如果不再需要本文中使用的资源，请将其删除：

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>后续步骤

在本文中， 你使用了 ACR 任务来创建自动化门控工作流，以将更新的基础映像引入你的环境。 请参阅相关信息，以管理 Azure 容器注册表中的映像。


* [有关对容器映像进行标记和版本控制的建议](container-registry-image-tag-version.md)
* [锁定 Azure 容器注册表中的容器映像](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io