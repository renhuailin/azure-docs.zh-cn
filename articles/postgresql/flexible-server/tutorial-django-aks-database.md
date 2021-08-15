---
title: 教程：使用 Azure CLI 通过 PostgreSQL 灵活服务器在 AKS 群集上部署 Django
description: 了解如何使用 Azure Database for PostgreSQL 灵活服务器在 AKS 上快速构建和部署 Django。
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4cea156f52538a58ba5eea86ace6272848a05ae9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467163"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>教程：使用 Azure Database for PostgreSQL 灵活服务器在 AKS 上部署 Django 应用

本快速入门介绍如何使用 Azure CLI 通过 Azure Database for PostgreSQL 灵活服务器（预览版）在 Azure Kubernetes 服务 (AKS) 群集上部署 Django 应用程序。

[AKS](../../aks/intro-kubernetes.md) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 [Azure Database for PostgreSQL 灵活服务器（预览版）](overview.md)是一种完全托管的数据库服务，旨在针对数据库管理功能和配置设置提供更精细的控制和更大的灵活性。

> [!NOTE]
> - Azure Database for PostgreSQL 灵活服务器当前以公共预览版提供
> - 本快速入门假设读者基本了解 Kubernetes 的概念以及 Django 和 PostgreSQL。

## <a name="pre-requisites"></a>先决条件
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- 在新的浏览器窗口中启动 [Azure Cloud Shell](https://shell.azure.com)。 也可以在本地计算机上[安装 Azure CLI](/cli/azure/install-azure-cli#install)。 如果使用本地安装，请通过 Azure CLI 使用 [az login](/cli/azure/reference-index#az_login) 命令登录。  若要完成身份验证过程，请遵循终端中显示的步骤。 
- 运行 [az version](/cli/azure/reference-index?#az_version) 以查找安装的版本和依赖库。 若要升级到最新版本，请运行 [az upgrade](/cli/azure/reference-index?#az_upgrade)。 本文需要 Azure CLI 的最新版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 我们来使用 [az-group-create](/cli/azure/group#az_group_create) 命令在“eastus”位置创建资源组 django-project 。

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> 此位置是资源组元数据的存储位置。 如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。

以下示例输出显示已成功创建资源组：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>创建 AKS 群集

使用 [az aks create](/cli/azure/aks#az_aks_create) 命令创建 AKS 群集。 以下示例创建一个具有一个节点的名为 myAKSCluster 的群集。 此操作将需要几分钟才能完成。

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。

> [!NOTE]
> 创建 AKS 群集时，会自动创建另一个资源组来存储 AKS 资源。 请参阅[为什么使用 AKS 创建两个资源组？](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 

> [!NOTE] 
> 如果在本地运行 Azure CLI，请运行 [az aks install-CLI](/cli/azure/aks#az_aks_install_cli) 命令来安装 `kubectl`。

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
```

若要验证到群集的连接，请使用 [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令返回群集节点列表。

```azurecli-interactive
kubectl get nodes
```

以下示例输出显示在上一步创建的单个节点。 请确保节点的状态为 *Ready*：

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>创建 Azure Database for PostgreSQL 灵活服务器
使用 [az postgreSQL flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) 命令来创建灵活服务器。 以下命令使用服务默认值和 Azure CLI 本地上下文中的值创建服务器：

```azurecli-interactive
az postgres flexible-server create --public-access all
```

创建的服务器具有以下属性：
- 首次预配服务器时，将创建一个新的空数据库 ```postgres```。 在本快速入门中，我们将使用此数据库。
- 自动生成的服务器名称、管理员用户名、管理员密码、资源组名称（如果尚未在本地上下文中指定），并且与资源组位于同一位置
- 使用 public-access 参数可以创建对任何具有正确用户名和密码的客户端具有公共访问权限的服务器。
- 由于该命令使用本地上下文，因此它会在资源组 ```django-project``` 和区域 ```eastus``` 中创建该服务器。


## <a name="build-your-django-docker-image"></a>生成 Django Docker 映像

新建 [Django 应用程序](https://docs.djangoproject.com/en/3.1/intro/)，或使用现有的 Django 项目。 确保代码位于该文件夹结构中。 

> [!NOTE] 
> 如果没有应用程序，可以直接转到[创建 Kubernetes 清单文件](./tutorial-django-aks-database.md#create-kubernetes-manifest-file)，以使用示例映像 [mksuni/django-aks-app:latest](https://hub.docker.com/r/mksuni/django-aks-app)。 

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
          . . . . . . .
    ├───static
         . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
更新 ```settings.py``` 中的 ```ALLOWED_HOSTS```确保 Django 应用程序使用分配给 kubernetes 应用的外部 IP。

```python
ALLOWED_HOSTS = ['*']
```

更新 ```settings.py``` 文件中的 ```DATABASES={ }``` 部分。 以下代码片段从 Kubernetes 清单文件读取数据库主机、用户名和密码。

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>生成 requirements.txt 文件
创建 ```requirements.txt``` 文件以列出 Django 应用程序的依赖项。 下面是 ```requirements.txt``` 示例文件。 你可以使用 [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) 来为现有应用程序生成 requirements.txt 文件。

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>创建 Dockerfile
新建名为 ```Dockerfile``` 的文件，然后复制以下代码片段。 此 Dockerfile 用于设置 Python 3.8 和安装 requirements.txt 文件中列出的所有要求。

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>生成映像
使用 ```cd``` 命令确保你位于终端的目录 ```my-django-app``` 中。 运行以下命令以生成公告板映像：

``` bash

docker build --tag myblog:latest .

```

将映像部署到 [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) 或 [Azure 容器注册表](../../container-registry/container-registry-get-started-azure-cli.md)。

> [!IMPORTANT]
>如果你使用 Azure 容器注册表 (ACR)，那么运行 ```az aks update``` 命令，将 ACR 帐户附加到 AKS 群集。
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>创建 Kubernetes 清单文件

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 我们来创建名为 ```djangoapp.yaml``` 的清单文件，并将其复制到以下 YAML 定义中。 

>[!IMPORTANT]
> - 将 ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` 替换为实际的 Django docker 映像名称和标记，例如 ```docker-hub-user/myblog:latest```。  可以使用清单文件中的演示示例应用 ```mksuni/django-aks-app:latest```。
> - 使用 postgres 灵活服务器的 ```SERVERNAME```、```YOUR-DATABASE-USERNAME``` 和 ```YOUR-DATABASE-PASSWORD``` 更新下面的 ```env``` 部分。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>将 Django 部署到 AKS 群集
使用 [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 命令部署应用程序，并指定 YAML 清单的名称：

```console
kubectl apply -f djangoapp.yaml
```

以下示例输出显示已成功创建了部署和服务：

```output
deployment "django-app" created
service "python-svc" created
```

使用部署 ```django-app``` 可说明有关部署的详细信息，例如应用要使用的映像、Pod 的数量和 Pod 配置。 创建服务 ```python-svc``` 通过外部 IP 公开应用程序。

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。

若要监视进度，请将 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令与 `--watch` 参数配合使用。

```azurecli-interactive
kubectl get service django-app --watch
```

最初，django-app 服务的 EXTERNAL-IP 显示为“挂起”  。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

现在，打开 Web 浏览器，转到服务的外部 IP 地址，查看 Django 应用程序。  

>[!NOTE]
> - 当前，Django 站点未使用 HTTPS。 建议[使用你自己的证书启用 TLS](../../aks/ingress-own-tls.md)。
> - 可以为群集启用 [HTTP 路由](../../aks/http-application-routing.md)。 启用 http 路由后，它可以在 AKS 群集中配置入口控制器。 在部署应用程序以后，此解决方案还可以为应用程序终结点创建可公开访问的 DNS 名称。

## <a name="run-database-migrations"></a>运行数据库迁移

对于任何 django 应用程序，需运行数据库迁移或收集静态文件。 可以使用 ```$ kubectl exec <pod-name> -- [COMMAND]``` 运行这些 django shell 命令。  在运行命令之前，需使用 ```kubectl get pods``` 查找 Pod 名称。 

```bash
$ kubectl get pods
```

应会看到如下所示的输出
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

找到 Pod 名称后，可以使用命令 ```$ kubectl exec <pod-name> -- [COMMAND]``` 运行 django 数据库迁移。 请注意，```/code/``` 是上面 ```Dockerfile``` 中所定义项目的工作目录。

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

输出可能如下所示 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

如果遇到问题，请运行 ```kubectl logs <pod-name>``` 以查看应用程序引发的异常。 如果应用程序成功运行，你将在运行 ```kubectl logs``` 时看到如下输出。

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>清理资源

若要避免 Azure 费用，应清除不需要的资源。  如果不再需要群集，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除](../../aks/kubernetes-service-principal.md#additional-considerations)。 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="next-steps"></a>后续步骤

- 了解如何[访问 AKS 群集的 Kubernetes Web 仪表板](../../aks/kubernetes-dashboard.md)
- 了解如何[启用连续部署](../../aks/deployment-center-launcher.md)
- 了解如何[缩放群集](../../aks/tutorial-kubernetes-scale.md)
- 了解如何管理 [postgres 灵活服务器](./quickstart-create-server-cli.md)
- 了解如何为数据库服务器[配置服务器参数](./howto-configure-server-parameters-using-cli.md)。
