---
title: 使用 GitHub Actions 部署资源管理器模板
description: 介绍如何使用 GitHub Actions 部署资源管理器模板（ARM 模板）。
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 243316b32d5b0cf62f03ae77d8a9fb919743ace1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172824"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>使用 GitHub Actions 部署 ARM 模板

[Github 操作](https://docs.github.com/en/actions)是 GitHub 中的一个功能套件，可以在存储代码的同一位置自动执行软件开发工作流，并针对拉取请求和问题进行协作。

使用[“部署 Azure 资源管理器模板”操作](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)将 Azure 资源管理器模板（ARM 模板）自动部署到 Azure。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个 GitHub 帐户。 如果没有该帐户，请注册[免费版](https://github.com/join)。
    - GitHub 存储库，用于存储资源管理器模板和工作流文件。 若要创建一个存储库，请参阅[创建新存储库](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)。


## <a name="workflow-file-overview"></a>工作流文件概述

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

此文件包含两个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1.定义服务主体。 <br /> 2.创建 GitHub 机密。 |
|**部署** | 1.部署资源管理器模板。 |

## <a name="generate-deployment-credentials"></a>生成部署凭据


可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 命令创建[服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 请使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

如果没有资源组，请创建一个。

```azurecli-interactive
    az group create -n {MyResourceGroup}
```

请将 `myApp` 占位符替换为应用程序的名称。

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

在上面的示例中，请将占位符替换为你的订阅 ID 和资源组名称。 输出是一个 JSON 对象，包含的角色分配凭据可提供对应用服务应用的访问权限，如下所示。 复制此 JSON 对象供以后使用。 你只需要具有 `clientId`、`clientSecret`、`subscriptionId` 和 `tenantId` 值的部分。

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 始终应授予最小访问权限。 上一示例中的范围限制为资源组。



## <a name="configure-the-github-secrets"></a>配置 GitHub 机密

需要为 Azure 凭据、资源组和订阅创建机密。

1. 在 [GitHub](https://github.com/) 中，浏览你的存储库。

1. 选择“设置”>“机密”>“新的机密”。

1. 将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称 `AZURE_CREDENTIALS`。

1. 创建另一个名为 `AZURE_RG` 的机密。 将资源组的名称添加到该机密的“值”字段（例如：`myResourceGroup`）。

1. 再创建一个名为 `AZURE_SUBSCRIPTION` 的机密。 将订阅 ID 添加到该机密的“值”字段（例如：`90fd3f9d-4c61-432d-99ba-1273f236afa2`）。

## <a name="add-resource-manager-template"></a>添加资源管理器模板

将资源管理器模板添加到 GitHub 存储库。 此模板可用于创建存储帐户。

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

你可以将该文件放到存储库中的任何位置。 下一部分的工作流示例假定模板文件名为“azuredeploy.json”，它存储在存储库的根目录下。

## <a name="create-workflow"></a>创建工作流

工作流文件必须存储在存储库根目录的“.github/workflow”文件夹中。 工作流文件扩展名可以是“.yml”或“.yaml”。 

1. 在 GitHub 存储库的顶部菜单中，选择“操作”。
1. 选择“新建工作流”。
1. 选择“自己设置工作流”。
1. 如果希望使用“main.yml”以外的其他名称，请重命名工作流文件。 例如：“deployStorageAccount.yml”。
1. 将 yml 文件的内容替换为以下内容：

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > 可以改为在 ARM 部署操作中指定一个 JSON 格式的参数文件（例如：`.azuredeploy.parameters.json`）。

    工作流文件的第一部分包含：

    - **name**：工作流的名称。
    - 事件：触发工作流的 GitHub 事件的名称。 当主分支上有推送事件时，会触发工作流，修改所指定的两个文件中的至少一个。 这两个文件分别是工作流文件和模板文件。

1. 选择“开始提交”。
1. 选择“直接提交到主分支”。
1. 选择“提交新文件”（或“提交更改”）。 

由于工作流配置为由要更新的工作流文件或模板文件触发，因此在提交更改后，工作流将立即启动。

## <a name="check-workflow-status"></a>检查工作流状态

1. 选择“操作”选项卡。此时会看到列出的“创建 deployStorageAccount.yml”工作流。 运行此工作流需要 1-2 分钟。
1. 选择要打开的工作流。
1. 从菜单中选择“运行 ARM 部署”以验证此部署。

## <a name="clean-up-resources"></a>清理资源
不再需要资源组和存储库时，请通过删除资源组和 GitHub 存储库来清理部署的资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建第一个 ARM 模板](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [了解模块：使用 GitHub 操作自动部署 ARM 模板](/learn/modules/deploy-templates-command-line-github-actions/)
