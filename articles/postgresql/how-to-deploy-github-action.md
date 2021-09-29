---
title: 快速入门：通过 GitHub Actions 连接到 Azure PostgreSQL
description: 从 GitHub Actions 工作流使用 Azure PostgreSQL
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: acfed042be582475f3d186b5bcfbb6754d621b28
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766884"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>快速入门：使用 GitHub Actions 连接到 Azure PostgreSQL

适用于：:::image type="icon" source="./media/applies-to/yes.png" border="false":::Azure Database for PostgreSQL - 单一服务器 :::image type="icon" source="./media/applies-to/yes.png" border="false":::Azure Database for PostgreSQL - 灵活服务器  

使用工作流将数据库更新部署到 [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/) 以开始使用 [GitHub Actions](https://docs.github.com/en/actions)。

## <a name="prerequisites"></a>必备条件

将需要以下项：
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 包含示例数据的 GitHub 存储库 (`data.sql`)。 如果没有 GitHub 帐户，可以[免费注册](https://github.com/join)。
- Azure Database for PostgreSQL 服务器。
    - [快速入门：在 Azure 门户中创建用于 PostgreSQL 的 Azure 数据库服务器](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>工作流文件概述

GitHub Actions 工作流是由存储库中 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义的。 此定义包含组成工作流的各种步骤和参数。

此文件包含两个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1.定义服务主体。 <br /> 2.创建 GitHub 机密。 |
|**部署** | 1.部署数据库。 |

## <a name="generate-deployment-credentials"></a>生成部署凭据

可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md)。 请使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

将占位符 `server-name` 替换为在 Azure 上托管的 PostgreSQL 服务器的名称。 将 `subscription-id` 和 `resource-group` 替换为连接到你的 PostgreSQL 服务器的订阅 ID 和资源组。

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

输出是一个具有角色分配凭据的 JSON 对象，这些凭据提供对数据库的访问权限（类似于以下示例）。 复制此输出 JSON 对象以备后用。

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
> 始终应授予最小访问权限。 上一个示例中的范围仅限于特定的服务器而不是整个资源组。

## <a name="copy-the-postgresql-connection-string"></a>复制 PostgreSQL 连接字符串

在 Azure 门户中，转到 Azure Database for PostgreSQL 服务器并打开“设置” > “连接字符串” 。 复制 **ADO.NET** 连接字符串。 将占位符值替换为 `your_database` 和 `your_password`。 该连接字符串将如下所示。

> [!IMPORTANT]
> - 对于单一服务器，使用 ```user=adminusername@servername```。 注意，```@servername``` 是必需的。
> - 对于灵活服务器，使用不包含 ```@servername``` 的 ```user= adminusername```。

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

你将使用连接字符串作为 GitHub 机密。

## <a name="configure-the-github-secrets"></a>配置 GitHub 机密

1. 在 [GitHub](https://github.com/) 中，浏览存储库。

1. 选择“设置”>“机密”>“新的机密”。

1. 将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称 `AZURE_CREDENTIALS`。

    以后配置工作流文件时，请使用该机密作为 Azure 登录操作的输入 `creds`。 例如：

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. 再次选择“新的机密”。

1. 将连接字符串值粘贴到机密的值字段中。 为机密指定名称 `AZURE_POSTGRESQL_CONNECTION_STRING`。


## <a name="add-your-workflow"></a>添加工作流

1. 转到 GitHub 存储库的“操作”。

2. 选择“自己设置工作流”。

2. 删除工作流文件 `on:` 部分后面的所有内容。 例如，剩下的工作流可能如下所示。

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 将工作流重命名为 `PostgreSQL for GitHub Actions`，并添加签出和登录操作。 这些操作将签出你的站点代码，并使用之前创建的 `AZURE_CREDENTIALS` GitHub 机密向 Azure 进行身份验证。

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. 使用 Azure PostgreSQL 部署操作连接到 PostgreSQL 实例。 将 `POSTGRESQL_SERVER_NAME` 替换为服务器的名称。 在存储库的根级别应该有一个名为 `data.sql` 的 PostgreSQL 数据文件。

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. 通过添加注销 Azure 的操作来完成工作流。 下面是已完成的工作流。 文件将显示在存储库的 `.github/workflows` 文件夹中。

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>查看部署

1. 转到 GitHub 存储库的“操作”。

1. 打开第一个结果，查看工作流运行的详细日志。

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="GitHub 操作运行的日志":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Azure PostgreSQL 数据库和存储库，请删除资源组和 GitHub 存储库，以清理部署的资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure 和 GitHub 集成](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [了解如何连接到服务器](how-to-connect-query-guide.md)
