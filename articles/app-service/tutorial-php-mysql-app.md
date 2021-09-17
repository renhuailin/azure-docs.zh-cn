---
title: 教程：将 PHP 应用与 MySQL 配合使用
description: 了解如何创建一个可在 Azure 中运行的 PHP 应用，并将其连接到 MySQL 数据库。 本教程中使用 Laravel。
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/15/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 832f685bd53f19d4295863b922789b0c4ee85f62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746863"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure-app-service"></a>教程：在 Azure 应用服务中生成 PHP 和 MySQL 应用

::: zone pivot="platform-windows"  

[Azure 应用服务](overview.md)使用 Windows 操作系统，提供高度可缩放的自修补 Web 托管服务。 本教程介绍如何在 Azure 中创建 PHP 应用，并将其连接到 MySQL 数据库。 完成本教程后，Windows 的 Azure 应用服务中将会运行一个 [Laravel](https://laravel.com/) 应用。

::: zone-end

::: zone pivot="platform-linux"

[Azure 应用服务](overview.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本教程介绍如何在 Azure 中创建 PHP 应用，并将其连接到 MySQL 数据库。 完成本教程后，Linux 上的 Azure 应用服务中将会运行一个 [Laravel](https://laravel.com/) 应用。

::: zone-end

:::image type="content" source="./media/tutorial-php-mysql-app/complete-checkbox-published.png" alt-text="标题为“任务列表”的 PHP 应用示例的屏幕截图。":::

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 中创建 MySQL 数据库
> * 将 PHP 应用连接到 MySQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

为完成此教程：

- [安装 Git](https://git-scm.com/)
- [安装 PHP 5.6.4 或更高版本](https://php.net/downloads.php)
- [安装 Composer](https://getcomposer.org/doc/00-intro.md)
- 启用 Laravel 所需的以下 PHP 扩展：OpenSSL、PDO-MySQL、Mbstring、Tokenizer、XML
- [安装并启动 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

## <a name="prepare-local-mysql"></a>准备本地 MySQL

此步骤在本地 MySQL 服务器供中创建一个数据库，以便在本教程中使用。

### <a name="connect-to-local-mysql-server"></a>连接到本地 MySQL 服务器

在终端窗口中连接到本地 MySQL 服务器。 可使用此终端窗口运行本教程中的所有命令。

```bash
mysql -u root -p
```

当系统提示输入密码时，请输入 `root` 帐户的密码。 如果不记得自己的 Root 帐户密码，请参阅 [MySQL：如何重置 Root 密码](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。

如果命令成功运行，则表示 MySQL 服务器正在运行。 否则，请确保遵循 [MySQL 安装后步骤](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)启动本地 MySQL 服务器。

### <a name="create-a-database-locally"></a>在本地创建数据库

1. 在 `mysql` 提示中创建数据库。

    ```sql 
    CREATE DATABASE sampledb;
    ```

1. 键入 `quit` 退出服务器连接。

    ```sql
    quit
    ```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>在本地创建 PHP 应用
此步骤创建一个 Laravel 示例应用程序、配置其数据库连接，并在本地运行该应用程序。 

### <a name="clone-the-sample"></a>克隆示例

在终端窗口中，通过 `cd` 转到工作目录。

1. 克隆示例存储库并更改为存储库根路径。

    ```bash
    git clone https://github.com/Azure-Samples/laravel-tasks
    cd laravel-tasks
    ```

1. 确保默认分支为 `main`。

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > 应用服务不需要更改分支名称。 但是，由于许多存储库将其默认分支更改为 `main`，因此本教程还介绍如何从 `main` 部署存储库。 有关详细信息，请参阅[更改部署分支](deploy-local-git.md#change-deployment-branch)。

1. 安装所需程序包。

    ```bash
    composer install
    ```

### <a name="configure-mysql-connection"></a>配置 MySQL 连接

在存储库根路径中，创建名为 .env 的文件。 复制下列变量到 .env 文件。 请将 &lt;root_password> 占位符替换为 MySQL 根用户的密码。

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

有关 Laravel 如何使用 .env 文件的信息，请参阅 [Laravel 环境配置](https://laravel.com/docs/5.4/configuration#environment-configuration)。

### <a name="run-the-sample-locally"></a>在本地运行示例

1. 运行 [Laravel 数据库迁移](https://laravel.com/docs/5.4/migrations)，创建应用程序所需的表。 若要查看迁移中创建了哪些表，请查看 Git 存储库中的 database/migrations 目录。

    ```bash
    php artisan migrate
    ```

1. 生成新的 Laravel 应用程序密钥。

    ```bash
    php artisan key:generate
    ```

1. 运行应用程序。

    ```bash
    php artisan serve
    ```

1. 在浏览器中导航至 `http://localhost:8000` 。 在页面中添加一些任务。

    ![PHP 已成功连接到 MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

1. 在终端键入 `Ctrl + C` 可停止 PHP。

## <a name="create-mysql-in-azure"></a>在 Azure 中创建 MySQL

此步骤在 [Azure Database for MySQL](../mysql/index.yml) 中创建一个 MySQL 数据库。 稍后需要将 PHP 应用程序配置为连接到此数据库。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>创建 MySQL 服务器

在 Cloud Shell 中，使用 [`az mysql server create`](/cli/azure/mysql/server#az_mysql_server_create) 命令在 Azure Database for MySQL 中创建一个服务器。

在下列命令中，用唯一的服务器名称替换 \<mysql-server-name> 占位符，用用户名替换\<admin-user> 占位符，并用密码替换 \<admin-password> 占位符  。 此服务器名称用作 MySQL 终结点 (`https://<mysql-server-name>.mysql.database.azure.com`) 的一部分，因此需在 Azure 的所有服务器中保持唯一。 有关选择 MySQL DB SKU 的详细信息，请参阅[为 MySQL 服务器创建 Azure 数据库](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md#create-an-azure-database-for-mysql-server)。

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

创建 MySQL 服务器后，Azure CLI 会显示类似于以下示例的信息：

<pre>
{
  "administratorLogin": "&lt;admin-user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
  -  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>配置服务器防火墙

1. 在 Cloud Shell 中，使用 [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) 命令创建 MySQL 服务器的防火墙规则，以便建立客户端连接。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。 

    ```azurecli-interactive
    az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```

    > [!TIP] 
    > 你甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](overview-inbound-outbound-ips.md#find-outbound-ips)。
    >

1. 在 Cloud Shell 中再次运行该命令（将 \<your-ip-address> 替换为[本地 IPv4 IP 地址](https://www.whatsmyip.org/)），以便从本地计算机进行访问。

    ```azurecli-interactive
    az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
    ```

### <a name="create-a-production-database"></a>创建生产数据库

1. 在本地终端窗口中，连接到 Azure 中的 MySQL 服务器。 对于 &lt;admin-user> 和 &lt;mysql-server-name>，请使用前面指定的值 。 出现输入密码的提示时，请使用在 Azure 中创建数据库时指定的密码。

    ```bash
    mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
    ```

1. 在 `mysql` 提示中创建数据库。

    ```sql
    CREATE DATABASE sampledb;
    ```

1. 创建一个名为 phpappuser 的数据库用户并向其授予 `sampledb` 数据库中的所有特权。 为方便学习教程，请使用 MySQLAzure2017 作为密码。

    ```sql
    CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
    GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
    ```

1. 键入 `quit` 退出服务器连接。

    ```sql
    quit
    ```

## <a name="connect-app-to-azure-mysql"></a>将应用连接到 Azure MySQL

此步骤将 PHP 应用程序连接到在 Azure Database for MySQL 中创建的 MySQL 数据库。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>配置数据库连接

在存储库根路径中创建一个 .env.production 文件，并在其中复制以下变量。 替换 DB_HOST 和 DB_USERNAME 中的占位符 _&lt;mysql-server-name>_ 。

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

保存更改。

> [!TIP]
> 若要保护 MySQL 连接信息，此文件已从 Git 存储库（请参阅存储库根路径中的 .gitignore排除。 以后介绍如何将应用服务中的环境变量配置为连接到 Azure Database for MySQL 中的数据库。 有了环境变量，便不需要应用服务中的 .env 文件。
>

### <a name="configure-tlsssl-certificate"></a>配置 TLS/SSL 证书

默认情况下，Azure Database for MySQL 强制执行来自客户端的 TLS 连接。 若要连接到 Azure 中的 MySQL 数据库，必须使用 [Azure Database for MySQL 提供的 _.pem_ 证书](../mysql/howto-configure-ssl.md)。

打开 _config/database.php_，将 `sslmode` 和 `options` 参数添加到 `connections.mysql`，如以下代码所示。

::: zone pivot="platform-windows"  

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

::: zone-end

::: zone pivot="platform-linux"

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

::: zone-end

在本教程中，为方便起见，证书 `BaltimoreCyberTrustRoot.crt.pem` 在存储库中提供。 

### <a name="test-the-application-locally"></a>在本地测试应用程序

1. 使用 _.env.production_ 作为环境文件运行 Laravel 数据库迁移，在 Azure Database for MySQL 中的 MySQL 数据库内创建表。 请记住，在 Azure 中 .env.production 具有的 MySQL 数据库的连接信息。

    ```bash
    php artisan migrate --env=production --force
    ```

1. _.env.production_ 目前还不包含有效的应用程序密钥。 请在终端中为它生成一个新密钥。

    ```bash
    php artisan key:generate --env=production --force
    ```

1. 使用 _.env.production_ 作为环境文件运行示例应用程序。

    ```bash
    php artisan serve --env=production
    ```

1. 导航到 `http://localhost:8000`。 如果页面可加载且未出错，则表示 PHP 应用程序正在连接到 Azure 中的 MySQL 数据库。

1. 在页面中添加一些任务。

    ![PHP 已成功连接到 Azure Database for MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

1. 在终端键入 `Ctrl + C` 可停止 PHP。

### <a name="commit-your-changes"></a>提交更改

运行以下的 Git 命令，提交更改：

```bash
git add .
git commit -m "database.php updates"
```

应用已可用于部署。

## <a name="deploy-to-azure"></a>“部署到 Azure”

此步骤将已连接 MySQL 的 PHP 应用程序部署到 Azure 应用服务。

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>创建 Web 应用

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

::: zone-end

### <a name="configure-database-settings"></a>配置数据库设置

在应用服务中，使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 命令将环境变量设置为应用设置。

使用以下命令可以配置应用设置 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME` 和 `DB_PASSWORD`。 替换占位符 &lt;app-name> 和 &lt;mysql-server-name> 。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

可以使用 PHP [getenv](https://www.php.net/manual/en/function.getenv.php) 方法访问这些设置。 Laravel 代码使用 [env](https://laravel.com/docs/5.4/helpers#method-env) 包装器，而不是 PHP `getenv`。 例如，_config/database.php_ 中的 MySQL 配置如下代码所示：

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>配置 Laravel 环境变量

在应用服务中，Laravel 需要应用程序密钥。 可以使用应用设置来配置该密钥。

1. 在本地终端窗口中，使用 `php artisan` 生成新的应用程序密钥，但不要将它保存到 _.env_。

    ```bash
    php artisan key:generate --show
    ```

1. 在 Cloud Shell 中，使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 命令在应用服务应用中设置应用程序密钥。 替换占位符 &lt;app-name> 和 &lt;outputofphpartisankey:generate> 。

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
    ```

    当部署的应用遇到错误时，`APP_DEBUG="true"` 将告知 Laravel 返回调试信息。 在运行生产应用程序时，请将其设置为 `false`，这样会更安全。

### <a name="set-the-virtual-application-path"></a>设置虚拟应用程序路径

::: zone pivot="platform-windows"  

设置应用的虚拟应用程序路径。 需要执行此步骤的原因是 [Laravel 应用程序生命周期](https://laravel.com/docs/5.4/lifecycle)在 public 目录中开始，而不是在应用程序的根目录中开始。 无需手动配置虚拟应用程序路径，生命周期在根目录中开始的其他 PHP 框架也能正常工作。

在 Cloud Shell 中，使用 [`az resource update`](/cli/azure/resource#az_resource_update) 命令设置虚拟应用程序路径。 替换 &lt;app-name> 占位符。

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

默认情况下，Azure 应用服务将根虚拟应用程序路径 (/) 指向已部署的应用程序的文件的根目录 (sites\wwwroot)。

::: zone-end

::: zone pivot="platform-linux"

[Laravel 应用程序生命周期](https://laravel.com/docs/5.4/lifecycle)在 public 目录中开始，而不是在应用程序的根目录中开始。 适用于应用服务的默认 PHP Docker 映像使用 Apache，不允许为 Laravel 自定义 `DocumentRoot`。 但是，可以使用 `.htaccess` 来重写所有请求，使之指向 _/public_ 而不是根目录。 在存储库根目录中，已针对此目的添加了 `.htaccess`。 有了它即可部署 Laravel 应用程序。

有关详细信息，请参阅[更改站点根](configure-language-php.md#change-site-root)。

::: zone-end

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

::: zone pivot="platform-windows"  

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

   <pre>
   Counting objects: 3, done.
   Delta compression using up to 8 threads.
   Compressing objects: 100% (3/3), done.
   Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
   Total 3 (delta 2), reused 0 (delta 0)
   remote: Updating branch 'main'.
   remote: Updating submodules.
   remote: Preparing deployment for commit id 'a5e076db9c'.
   remote: Running custom deployment command...
   remote: Running deployment command...
   ...
   &lt; Output has been truncated for readability &gt;
   </pre>
    
   > [!NOTE]
   > 你可能会发现，部署过程在即将结束时会安装 [Composer](https://getcomposer.org/) 包。 应用服务在默认部署期间不会运行这些自动化任务，因此该示例存储库的根目录中提供了两个附加的文件用于运行这些任务：
   >
   > - `.deployment` - 此文件告知应用服务以自定义部署脚本运行 `bash deploy.sh`。
   > - `deploy.sh` - 自定义部署脚本。 查看该文件时可以看到，它会先运行 `npm install`，再运行 `php composer.phar install`。
   > - `composer.phar` - Composer 包管理器。
   >
   > 可使用此方法将任何步骤添加到应用服务中的基于 Git 的部署。 有关详细信息，请参阅[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。
   >

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

   <pre>
   Counting objects: 3, done.
   Delta compression using up to 8 threads.
   Compressing objects: 100% (3/3), done.
   Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
   Total 3 (delta 2), reused 0 (delta 0)
   remote: Updating branch 'main'.
   remote: Updating submodules.
   remote: Preparing deployment for commit id 'a5e076db9c'.
   remote: Running custom deployment command...
   remote: Running deployment command...
   ...
   &lt; Output has been truncated for readability &gt;
   </pre>
    
::: zone-end

### <a name="browse-to-the-azure-app"></a>转到 Azure 应用

浏览到 `http://<app-name>.azurewebsites.net` 并在列表中添加一些任务。

:::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="标题为“任务列表”的 Azure 应用示例的屏幕截图，显示了添加的新任务。":::

恭喜，你的数据驱动的 PHP 应用正在 Azure 应用服务中运行。

## <a name="update-model-locally-and-redeploy"></a>在本地更新模型和重新部署

在此步骤中，对 `task` 数据模型和 webapp 进行简单更改，然后将更新发布到 Azure。

对于任务方案，需要修改应用程序，以便能够将任务标记为已完成。

### <a name="add-a-column"></a>添加列

1. 在本地终端窗口中，导航到 Git 存储库中的根路径。

1. 为 `tasks` 表创建新数据库迁移：

    ```bash
    php artisan make:migration add_complete_column --table=tasks
    ```

1. 此命令显示已生成的迁移文件的名称。 在 database/migrations 中找到此文件，并打开它。

1. 将 `up` 方法替换为以下代码：

    ```php
    public function up()
    {
        Schema::table('tasks', function (Blueprint $table) {
            $table->boolean('complete')->default(False);
        });
    }
    ```

    上述代码在名为 `complete` 的 `tasks` 表中添加一个布尔值列。

1. 请将 `down` 方法替换为以下回滚操作代码：

    ```php
    public function down()
    {
        Schema::table('tasks', function (Blueprint $table) {
            $table->dropColumn('complete');
        });
    }
    ```

1. 在本地终端窗口中运行 Laravel 数据库迁移，以便在本地数据库中进行更改。

    ```bash
    php artisan migrate
    ```

    根据 [Laravel 命名约定](https://laravel.com/docs/5.4/eloquent#defining-models)，模型 `Task`（请参阅 app/Task.php）默认映射到 `tasks` 表。

### <a name="update-application-logic"></a>更新应用程序逻辑

1. 打开 routes/web.php 文件。 应用程序在此处定义其路由和业务逻辑。

1. 在文件末尾，添加包含以下代码的路由：

    ```php
    /**
     * Toggle Task completeness
     */
    Route::post('/task/{id}', function ($id) {
        error_log('INFO: post /task/'.$id);
        $task = Task::findOrFail($id);
    
        $task->complete = !$task->complete;
        $task->save();
    
        return redirect('/');
    });
    ```

    上述代码通过切换 `complete` 的值对数据模型进行简单的更新。

### <a name="update-the-view"></a>更新视图

1. 打开 resources/views/tasks.blade.php 文件。 找到 `<tr>` 开始标记并将其替换为：

    ```html
    <tr class="{{ $task->complete ? 'success' : 'active' }}" >
    ```

    上述代码会根据任务是否已完成来更改行的颜色。

1. 在下一行中包含以下代码：

    ```html
    <td class="table-text"><div>{{ $task->name }}</div></td>
    ```

    请将整行替换为以下代码：

    ```html
    <td>
        <form action="{{ url('task/'.$task->id) }}" method="POST">
            {{ csrf_field() }}
    
            <button type="submit" class="btn btn-xs">
                <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
            </button>
            {{ $task->name }}
        </form>
    </td>
    ```

    上述代码添加引用前面定义的路由的提交按钮。

### <a name="test-the-changes-locally"></a>在本地测试更改

1. 在本地终端窗口中，从 Git 存储库的根目录运行开发服务器。

    ```bash
    php artisan serve
    ```

1. 若要查看任务状态更改，请导航至 `http://localhost:8000` 并选择复选框。

    ![将复选框添加到任务](./media/tutorial-php-mysql-app/complete-checkbox.png)

1. 在终端键入 `Ctrl + C` 可停止 PHP。

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

1. 在本地终端窗口中，使用生产连接字符串运行 Laravel 数据库迁移，在 Azure 数据库中进行更改。

    ```bash
    php artisan migrate --env=production --force
    ```

1. 提交在 Git 中进行的所有更改，然后将代码更改推送到 Azure。

    ```bash
    git add .
    git commit -m "added complete checkbox"
    git push azure main
    ```

1. `git push` 完成后，请导航至 Azure 应用，测试新功能。

    ![发布到 Azure 的模型和数据库更改](media/tutorial-php-mysql-app/complete-checkbox-published.png)

如果添加任何任务，则它们保留在数据库中。 更新数据架构不会改变现有数据。

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志

::: zone pivot="platform-windows"  

当 PHP 应用程序在 Azure 应用服务中运行时，可以将控制台日志通过管道传输到终端。 如此，可以获得相同的诊断消息，以便调试应用程序错误。

若要启动日志流式处理，请在 Cloud Shell 中使用 [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) 命令。

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

启动日志流式处理后，请在浏览器中刷新 Azure 应用，以获取一些 Web 流量。 现在可以看到通过管道传送到终端的控制台日志。 如果没有立即看到控制台日志，请在 30 秒后重新查看。

若要随时停止日志流式处理，请键入 `Ctrl`+`C`。

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

> [!TIP]
> PHP 应用程序可以使用标准 [error_log()](https://php.net/manual/function.error-log.php) 输出到控制台。 示例应用程序在 _app/Http/routes.php_ 中使用此方法。
>
> 作为一种 Web 框架，[Laravel 使用 Monolog](https://laravel.com/docs/5.4/errors) 作为日志记录提供程序。 若要了解如何使用 Monolog 将消息输出到控制台，请参阅 [PHP：如何使用 monolog 记录到控制台 (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out)。
>
>

## <a name="manage-the-azure-app"></a>管理 Azure 应用

1. 转到 [Azure 门户](https://portal.azure.com)管理已创建的应用。

1. 在左侧菜单中单击 **应用程序服务**，然后单击 Azure 应用的名称。

    ![在门户中导航到 Azure 应用](./media/tutorial-php-mysql-app/access-portal.png)

    这里我们可以看到应用的“概述”页。 在此处可以执行基本的管理任务，例如停止、启动、重启、浏览和删除。

    左侧菜单提供用于配置应用的页面。

    ![Azure 门户中的“应用服务”页](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 中创建 MySQL 数据库
> * 将 PHP 应用连接到 MySQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

转到下一教程，了解如何向应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](app-service-web-tutorial-custom-domain.md)

或者，查看其他资源：

> [!div class="nextstepaction"]
> [配置 PHP 应用](configure-language-php.md)
