---
title: 使用托管标识将 Azure SQL 连接到 Azure Spring Cloud 应用
description: 设置托管标识将 Azure SQL 连接到 Azure Spring Cloud 应用。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 3350924bbf064009523c9b6892856a9c7d4ff818
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129074"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>使用托管标识将 Azure SQL 数据库连接到 Azure Spring Cloud 应用

本文适用于：✔️ Java

本文介绍如何为 Azure Spring Cloud 应用创建托管标识，并使用该标识来访问 Azure SQL 数据库。

[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)是面向云生成的一项可缩放的智能关系数据库服务。 它始终处于最新状态，具有 AI 支持的自动化功能，可优化性能和持续性。 无服务器计算和超大规模存储选项会自动按需缩放资源，因此你可以专注于构建新的应用程序，而无需担心存储大小或资源管理。

## <a name="prerequisites"></a>先决条件
此示例使用以下资源。
* 遵循 [Spring Data JPA 教程](/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server)预配 Azure SQL 数据库并将该数据库在本地与 Java 应用结合使用
* 遵循 [Azure Spring Cloud 系统分配的托管标识教程](./how-to-enable-system-assigned-managed-identity.md)预配已启用 MI 的 Azure Spring Cloud 应用

## <a name="grant-permission-to-the-managed-identity"></a>向托管标识授予权限
连接到 SQL Server 并运行以下 SQL 查询：

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

此 <MIName> 遵循 `<service instance name>/apps/<app name>` 规则，如 myspringcloud/apps/sqldemo。 还可以通过 Azure CLI 查询 MIName：

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>配置 Java 应用以使用托管标识
打开 `src/main/resources/application.properties` 文件，并在下面一行的末尾添加 `Authentication=ActiveDirectoryMSI;`。 请确保为 $AZ_DATABASE_NAME 变量使用正确的值。

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>生成应用并将其部署到 Azure Spring Cloud
重新生成应用，并将其部署到先决条件下第二个项目符号点中预配的 Azure Spring Cloud 应用。 现在，你有了一个通过托管标识进行身份验证的 Spring Boot 应用程序，该应用程序使用 JPA 在 Azure Spring Cloud 中的 Azure SQL 数据库中存储和检索数据。

## <a name="next-steps"></a>后续步骤

* [如何使用 Azure Spring Cloud 中的托管标识访问 Storage Blob](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [如何为 Azure Spring Cloud 应用程序启用系统分配的托管标识](./how-to-enable-system-assigned-managed-identity.md)
* [了解有关 Azure 资源的托管标识的详细信息](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [在 GitHub Actions 中使用密钥保管库对 Azure Spring Cloud 进行身份验证](./github-actions-key-vault.md)