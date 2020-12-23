---
title: 快速入门 - 使用 GitHub Actions 工作流中的 Azure Key Vault 机密
description: 使用 GitHub Actions 中的 Key Vault 机密来自动执行软件开发工作流
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 9509f84b14a42180189a529282b5db348deab279
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920225"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>使用 GitHub Actions 工作流中的 Key Vault 机密

使用 [GitHub Actions](https://help.github.com/en/articles/about-github-actions) 中的 Key Vault 机密，并在 Azure 密钥保管库中安全地存储密码和其他机密。 深入了解 [Key Vault](../general/overview.md)。 

通过 Key Vault 和 GitHub Actions，你可以获得集中式机密管理工具的优势和 GitHub Actions 的所有优点。 GitHub Actions 是 GitHub 中用于自动执行软件开发工作流的一套功能。 可以将工作流部署在存储代码的同一位置，并就拉取请求和问题进行协作。 


## <a name="prerequisites"></a>先决条件 
- 一个 GitHub 帐户。 如果没有该帐户，请注册[免费版](https://github.com/join)。  
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 连接到 GitHub 存储库的 Azure 应用。 此示例使用[将容器部署到 Azure 应用服务](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)。 
- Azure 密钥保管库。  可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 来创建 Azure Key Vault。

## <a name="workflow-file-overview"></a>工作流文件概述

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

此文件具有用于使用 GitHub Actions 进行身份验证的两个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1.定义服务主体。 <br /> 2.创建 GitHub 机密。 <br /> 3.添加角色分配。 |
|**密钥保管库** | 1.添加密钥保管库操作。 <br /> 2.引用密钥保管库机密。 |

## <a name="authentication"></a>身份验证

可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 命令创建[服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 请使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

在上面的示例中，请将占位符替换为你的订阅 ID 和资源组名称。 请将 `myApp` 占位符替换为应用程序的名称。 输出是一个 JSON 对象，包含的角色分配凭据可提供对应用服务应用的访问权限，如下所示。 复制此 JSON 对象供以后使用。 只需具有 `clientId`、`clientSecret`、`subscriptionId` 和 `tenantId` 值的部分。 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>配置 GitHub 机密

为 Azure 凭据、资源组和订阅创建机密。 

1. 在 [GitHub](https://github.com/) 中，浏览存储库。

1. 选择“设置”>“机密”>“新的机密”。

1. 将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称 `AZURE_CREDENTIALS`。

1. 复制 `clientId` 的值，供稍后使用。 

### <a name="add-a-role-assignment"></a>添加角色分配 
 
需要授予对 Azure 服务主体的访问权限，以便可以访问密钥保管库进行 `get` 和 `list` 操作。 如果未执行此操作，则将无法使用服务主体。 

将 `keyVaultName` 替换为密钥保管库的名称，将 `clientIdGUID` 替换为 `clientId` 的值。 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>使用 Azure 密钥保管库操作

使用 [Azure 密钥保管库操作](https://github.com/marketplace/actions/azure-key-vault-get-secrets)，可以从 Azure 密钥保管库实例提取一个或多个机密，并在 GitHub 操作工作流中使用它。

提取的机密将被设置为输出和环境变量。 将变量打印到控制台或日志时，会自动屏蔽这些变量。

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

若要在工作流中使用密钥保管库，需要同时执行密钥保管库操作和引用该操作。 

在此示例中，密钥保管库的名称为 `containervault`。 将两个密钥保管库机密添加到环境，其中包含密钥保管库操作 - `containerPassword` 和 `containerUsername`。 

稍后会在 docker 登录任务中引用密钥保管库值，前缀为 `steps.myGetSecretAction.outputs`。 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Azure 应用、GitHub 存储库和密钥保管库，请删除应用、GitHub 存储库和密钥保管库的资源组，以清理部署的资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure 密钥保管库](../general/overview.md)
