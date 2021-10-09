---
title: 使用 GitHub Actions 将静态站点部署到 Azure 存储
description: 采用 GitHub Actions 的 Azure 存储静态网站托管
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/17/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3ec1eb55ae54a29d8bb5334993edeee7308dd2de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662526"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>在 Azure 存储中设置 GitHub Actions 工作流以部署静态网站

使用工作流将静态站点部署到 Azure 存储帐户以开始使用 [GitHub Actions](https://docs.github.com/en/actions)。 设置 GitHub Actions 工作流后，当更改站点代码时，站点会从 GitHub 自动部署到 Azure。

> [!NOTE]
> 如果你使用的是 [Azure Static Web Apps](../../static-web-apps/index.yml)，则无需手动设置 GitHub Actions 工作流。
> Azure Static Web Apps 会自动为你创建 GitHub Actions 工作流。

## <a name="prerequisites"></a>必备条件

Azure 订阅和 GitHub 帐户。

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 具有静态站点代码的 GitHub 存储库。 如果没有 GitHub 帐户，可以[免费注册](https://github.com/join)。
- 托管在 Azure 存储中的工作静态网站。 了解如何[在 Azure 存储中托管静态网站](storage-blob-static-website-how-to.md)。 若要遵循此示例，还应部署 [Azure CDN](static-website-content-delivery-network.md)。

> [!NOTE]
> 通常使用内容分发网络 (CDN) 来降低全球用户的延迟，并减少存储帐户的事务数。 将静态内容部署到基于云的存储服务可以降低对可能非常昂贵的计算实例的需求。 有关详细信息，请参阅“[静态内容托管模式](/azure/architecture/patterns/static-content-hosting)”。

## <a name="generate-deployment-credentials"></a>生成部署凭据

可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 命令创建[服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 请使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

将占位符 `myStaticSite` 替换为 Azure 存储中所托管站点的名称。

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

在上面的示例中，请将占位符替换为你的订阅 ID 和资源组名称。 输出是一个具有角色分配凭据的 JSON 对象，这些凭据提供对存储帐户的访问权限（类似于以下示例）。 复制此 JSON 对象供以后使用。

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
> 始终应授予最小访问权限。 上一个示例中的范围仅限于特定的应用服务应用而不是整个资源组。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

1. 在 [GitHub](https://github.com/) 中，浏览你的存储库。

1. 选择“设置”>“机密”>“新的机密”。

1. 将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称，如 `AZURE_CREDENTIALS`。

    以后配置工作流文件时，请使用该机密作为 Azure 登录操作的输入 `creds`。 例如：

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>添加工作流

1. 转到 GitHub 存储库的“操作”。

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="GitHub actions 菜单项":::

1. 选择“自己设置工作流”。

1. 删除工作流文件 `on:` 部分后面的所有内容。 例如，剩下的工作流可能如下所示。

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
    ```

1. 将工作流重命名为 `Blob storage website CI`，并添加签出和登录操作。 这些操作将签出你的站点代码，并使用之前创建的 `AZURE_CREDENTIALS` GitHub 机密向 Azure 进行身份验证。

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. 利用 Azure CLI 操作将代码上传到 blob 存储，并清除 CDN 终结点。 对于 `az storage blob upload-batch`，将占位符替换为存储帐户的名称。 脚本将上传到 `$web` 容器。 对于 `az cdn endpoint purge`，将占位符替换为 CDN 配置文件名称、CDN 终结点名称和资源组。 若要加快 CDN 清除的速度，可以将 `--no-wait` 选项添加到 `az cdn endpoint purge`。 若要增强安全性，还可以在[存储帐户密钥](../common/storage-account-keys-manage.md)中添加 `--account-key` 选项。

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME>  --auth-mode key -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ```

1. 通过添加注销 Azure 的操作来完成工作流。 下面是已完成的工作流。 文件将显示在存储库的 `.github/workflows` 文件夹中。

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> --auth-mode key -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"

      # Azure logout
        - name: logout
          run: |
                az logout
          if: always()
    ```

## <a name="review-your-deployment"></a>查看部署

1. 转到 GitHub 存储库的“操作”。

1. 打开第一个结果，查看工作流运行的详细日志。

    :::image type="content" source="../media/index/github-actions-run.png" alt-text="GitHub 操作运行的日志":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要静态网站和 GitHub 存储库，请删除资源组和 GitHub 存储库，以清理部署的资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Static Web Apps](../../static-web-apps/index.yml)
