---
title: 使用托管标识将 Azure SQL 连接到 Azure Spring Cloud 应用
description: 设置托管标识，将 Azure SQL 连接到 Azure Spring Cloud 应用。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: ed729dde51316b9a67f396e3f7de3d7d9f6d4568
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378782"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>使用托管标识将 Azure SQL 数据库连接到 Azure Spring Cloud 应用

本文适用于：✔️ Java

本文介绍如何为 Azure Spring Cloud 应用创建托管标识，并使用该标识来访问 Azure SQL 数据库。

[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)是面向云生成的一项可缩放的智能关系型数据库服务。 它始终处于最新状态，具有由 AI 提供支持的自动化功能，可用于优化性能和持续性。 无服务器计算和超大规模存储选项会自动按需缩放资源，使你可以专注于构建新的应用程序，而不必分心处理存储大小或资源管理。

## <a name="prerequisites"></a>先决条件
此示例使用以下资源。
* 按照 [Spring Data JPA 教程](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server)预配 Azure SQL 数据库，使其可在本地与 Java 应用一起使用
* 遵循 [Azure Spring Cloud 系统分配的托管标识教程](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity)来预配启用了 MI 的 Azure Spring Cloud 应用

## <a name="grant-permission-to-the-managed-identity"></a>向托管标识授予权限
连接到 SQL Server 并运行以下 SQL 查询：

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

此 <MIName> 遵循规则：`<service instance name>/apps/<app name>`，例如 myspringcloud/apps/sqldemo。 还可以通过 Azure CLI 查询 MIName：

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>将 Java 应用配置为使用托管标识
打开 `src/main/resources/application.properties` 文件，在以下行的末尾添加 `Authentication=ActiveDirectoryMSI;`。 请确保为 $AZ_DATABASE_NAME 变量使用正确的值。

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>生成应用并将其部署到 Azure Spring Cloud
重新生成应用，并将其部署到“先决条件”下第二个项目符号中预配的 Azure Spring Cloud 应用。 现在，你已有一个经过了托管标识身份验证的 Spring Boot 应用程序，它在 Azure Spring Cloud 中使用 JPA 存储并检索 Azure SQL 数据库中的数据。

## <a name="next-steps"></a>后续步骤

* [如何使用 Azure Spring Cloud 中的托管标识访问 Storage Blob](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [如何为 Azure Spring Cloud 应用程序启用系统分配的托管标识](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [了解有关 Azure 资源的托管标识的详细信息](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [在 GitHub Actions 中使用密钥保管库对 Azure Spring Cloud 进行身份验证](./spring-cloud-github-actions-key-vault.md)