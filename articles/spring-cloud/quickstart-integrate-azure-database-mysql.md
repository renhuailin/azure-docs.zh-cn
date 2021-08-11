---
title: 快速入门 - 与 Azure Database for MySQL 集成
description: 介绍如何预配和准备 Azure Database for MySQL 实例，然后仅通过一条命令在 Azure Spring Cloud 上配置 Pet Clinic，以将其用作永久数据库。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 05/13/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 223a3ba0fdb11824cd7c45218dc72271054db428
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114470670"
---
# <a name="quickstart-integrate-azure-spring-cloud-with-azure-database-for-mysql"></a>快速入门：将 Azure Spring Cloud 与 Azure Database for MySQL 集成

根据[快速入门：生成应用并将其部署到 Azure Spring Cloud](quickstart-deploy-apps.md) 中所述部署在默认配置中的 Pet Clinic 使用一个在启动时会填充数据的内存中数据库 (HSQLDB)。 本快速入门介绍如何预配和准备 Azure Database for MySQL 实例，然后仅通过一条命令在 Azure Spring Cloud 上配置 Pet Clinic，以将其用作永久数据库。

## <a name="variables-preparation"></a>变量准备

我们将使用以下值。 请将这些值保存在文本文件或环境变量中，以避免出错。

```bash
export RESOURCE_GROUP=<resource-group-name> # customize this
export MYSQL_SERVER_NAME=<mysql-server-name> # customize this
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_NAME=<admin-name> # customize this
export MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_NAME}\@${MYSQL_SERVER_NAME}
export MYSQL_SERVER_ADMIN_PASSWORD=<password> # customize this
export MYSQL_DATABASE_NAME=petclinic
```

## <a name="prepare-an-azure-database-for-mysql-instance"></a>准备 Azure Database for MySQL 实例

1. 创建 Azure Database for MySQL 服务器。

    ```azcli
    az mysql server create --resource-group ${RESOURCE_GROUP} \
        --name ${MYSQL_SERVER_NAME} \
        --admin-user ${MYSQL_SERVER_ADMIN_NAME} \
        --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD} \
        --sku-name GP_Gen5_2 \
        --ssl-enforcement Disabled \
        --version 5.7
    ```

1. 允许从 Azure 资源访问。

    ```azcli
    az mysql server firewall-rule create --name allAzureIPs \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```

1. 允许从开发计算机访问以进行测试。

    ```azcli
    az mysql server firewall-rule create --name devMachine \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address <ip-address-of-your-dev-machine> \
     --end-ip-address <ip-address-of-your-dev-machine>
    ```

1. 增大连接超时。

    ```azcli
    az mysql server configuration set --name wait_timeout \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value 2147483
    ```

1. 在 MySQL 服务器中创建数据库并设置相应的设置。

    ```sql
    // SUBSTITUTE values
    mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
     -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p
    
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 64379
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> CREATE DATABASE petclinic;
    Query OK, 1 row affected (0.10 sec)
    
    mysql> CREATE USER 'root' IDENTIFIED BY 'petclinic';
    Query OK, 0 rows affected (0.11 sec)
    
    mysql> GRANT ALL PRIVILEGES ON petclinic.* TO 'root';
    Query OK, 0 rows affected (1.29 sec)
    
    mysql> CALL mysql.az_load_timezone();
    Query OK, 3179 rows affected, 1 warning (6.34 sec)
    
    mysql> SELECT name FROM mysql.time_zone_name;
    ...
    
    mysql> quit
    Bye
    ```

1. 设置时区。

    ```azcli
    az mysql server configuration set --name time_zone \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value "US/Pacific"
    ```

## <a name="update-apps-to-use-mysql-database"></a>更新应用以使用 MySQL 数据库

若要将 MySQL 启用为示例应用的数据库，只需使用有效配置文件 MySQL 和环境变量形式的数据库凭据来更新 customer-service 应用即可。

```azcli
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="update-extra-apps"></a>更新额外的应用

```azcli
az spring-cloud app update --name api-gateway \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name admin-server \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name vets-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name visits-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="next-steps"></a>后续步骤
* [将 Azure Database for MySQL 实例绑定到 Azure Spring Cloud 应用程序](how-to-bind-mysql.md)
* [使用托管标识将 Azure SQL 数据库连接到 Azure Spring Cloud 应用](./connect-managed-identity-to-azure-sql.md)