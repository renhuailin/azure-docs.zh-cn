---
title: 教程：使用 Azure CLI 通过 MySQL 灵活服务器在 AKS 群集上部署 WordPress
description: 了解如何使用 Azure Database for MySQL 灵活服务器在 AKS 上快速构建和部署 WordPress。
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: vc, devx-track-azurecli
ms.openlocfilehash: 749137187b7fffe111f854860517dc608ae44c8a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582407"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>教程：使用 Azure Database for MySQL 灵活服务器在 AKS 上部署 WordPress 应用

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

本快速入门介绍如何使用 Azure CLI 通过 Azure Database for MySQL 灵活服务器（预览版）在 Azure Kubernetes 服务 (AKS) 群集上部署 WordPress 应用程序。 
[AKS](../../aks/intro-kubernetes.md) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 [Azure Database for MySQL 灵活服务器（预览版）](overview.md)是一种完全托管的数据库服务，旨在针对数据库管理功能和配置设置提供更精细的控制和更大的灵活性。 当前，灵活服务器处于预览阶段。

> [!NOTE]
>
> - Azure Database for MySQL 灵活服务器当前以公共预览版提供
> - 本快速入门假设读者基本了解 Kubernetes 的概念以及 WordPress 和 MySQL。

[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 的最新版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

> [!NOTE]
> 如果在本地运行此快速入门中的命令（而不是 Azure Cloud Shell），请确保以管理员身份运行命令。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 我们来使用 [az group create][az-group-create] 命令在 eastus 位置中创建资源组 wordpress-project 。

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> 此位置是资源组元数据的存储位置。 如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。

以下示例输出显示已成功创建资源组：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>创建 AKS 群集

使用 [az aks create](/cli/azure/aks#az_aks_create) 命令创建 AKS 群集。 以下示例创建一个具有一个节点的名为 myAKSCluster 的群集。 此操作将需要几分钟才能完成。

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。

> [!NOTE]
> 创建 AKS 群集时，会自动创建另一个资源组来存储 AKS 资源。 请参阅[为什么使用 AKS 创建两个资源组？](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli](/cli/azure/aks#az_aks_install_cli) 命令：

```azurecli-interactive
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> 以上命令使用 [Kubernetes 配置文件](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)的默认位置，即 `~/.kube/config`。 可以使用 --file 为 Kubernetes 配置文件指定其他位置。

若要验证到群集的连接，请使用 [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令返回群集节点列表。

```azurecli-interactive
kubectl get nodes
```

以下示例输出显示在上一步创建的单个节点。 请确保节点的状态为 *Ready*：

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>创建 Azure Database for MySQL 灵活服务器

使用 [az mysql flexible-server create](/cli/azure/mysql/flexible-server) 命令创建灵活服务器。 以下命令使用服务默认值和 Azure CLI 本地上下文中的值创建服务器：

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

创建的服务器具有以下属性：

- 首次预配服务器时，将创建一个新的空数据库 ```flexibleserverdb```。 在本快速入门中，我们将使用此数据库。
- 自动生成的服务器名称、管理员用户名、管理员密码、资源组名称（如果尚未在本地上下文中指定），并且与资源组位于同一位置
- 其余服务器配置的服务默认值：计算层（可突增）、计算大小/SKU (B1MS)、备份保持期（7 天）和 MySQL 版本 (5.7)
- 使用公共访问参数，你可以创建具有受防火墙规则保护的公共访问权限的服务器。 通过提供 IP 地址来添加防火墙规则，以允许从客户端计算机进行访问。
- 由于该命令使用本地上下文，因此它会在资源组```wordpress-project``` 和区域 ```eastus``` 中创建服务器。

### <a name="build-your-wordpress-docker-image"></a>生成 WordPress docker 映像

下载[最新 WordPress](https://wordpress.org/download/) 版本。 为项目创建新的目录 ```my-wordpress-app```，并使用这个简单的文件夹结构

```wordpress
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```

将 ```wp-config-sample.php``` 重命名为 ```wp-config.php```，并用此代码片段替换第 21 到 32 行。 以下代码片段从 Kubernetes 清单文件读取数据库主机、用户名和密码。

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>创建 Dockerfile

创建新的 Dockerfile 并复制此代码片段。 此 Dockerfile 使用 PHP 设置 Apache Web 服务器并启用 mysqli 扩展。

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>生成 Docker 映像

使用 ```cd``` 命令确保你位于终端的目录 ```my-wordpress-app``` 中。 运行以下命令生成映像：

``` bash

docker build --tag myblog:latest .

```

将映像部署到 [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) 或 [Azure 容器注册表](../../container-registry/container-registry-get-started-azure-cli.md)。

> [!IMPORTANT]
> 如果你使用 Azure 容器注册表 (ACR)，那么运行 ```az aks update``` 命令，将 ACR 帐户附加到 AKS 群集。
>
> ```azurecli-interactive
> az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```

## <a name="create-kubernetes-manifest-file"></a>创建 Kubernetes 清单文件

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 我们来创建名为 `mywordpress.yaml` 的清单文件，并将其复制到以下 YAML 定义中。

> [!IMPORTANT]
>
> - 将 ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` 替换为实际的 WordPress docker 映像名称和标记，例如 ```docker-hub-user/myblog:latest```。
> - 使用 MySQL 灵活服务器的 ```SERVERNAME```、```YOUR-DATABASE-USERNAME``` 和 ```YOUR-DATABASE-PASSWORD```更新下面的 ```env``` 部分。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>将 WordPress 部署到 AKS 群集

使用 [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 命令部署应用程序，并指定 YAML 清单的名称：

```console
kubectl apply -f mywordpress.yaml
```

以下示例输出显示已成功创建了部署和服务：

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。

若要监视进度，请将 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令与 `--watch` 参数配合使用。

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

最初，wordpress-blog 的 EXTERNAL-IP 显示为“挂起”  。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>浏览 WordPress

打开 Web 浏览器，转到服务的外部 IP 地址，查看 WordPress 安装页。

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="Wordpress 在 AKS 和 MySQL 灵活服务器上安装成功":::

> [!NOTE]
>
> - 当前，WordPress 站点未使用 HTTPS。 建议[使用你自己的证书启用 TLS](../../aks/ingress-own-tls.md)。
> - 可以为群集启用 [HTTP 路由](../../aks/http-application-routing.md)。

## <a name="clean-up-the-resources"></a>清理资源

若要避免 Azure 费用，应清除不需要的资源。  如果不再需要群集，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除](../../aks/kubernetes-service-principal.md#additional-considerations)。 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="next-steps"></a>后续步骤

- 了解如何[访问 AKS 群集的 Kubernetes Web 仪表板](../../aks/kubernetes-dashboard.md)
- 了解如何[缩放群集](../../aks/tutorial-kubernetes-scale.md)
- 了解如何管理 [MySQL 灵活服务器](./quickstart-create-server-cli.md)
- 了解如何为数据库服务器[配置服务器参数](./how-to-configure-server-parameters-cli.md)。
