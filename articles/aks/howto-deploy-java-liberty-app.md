---
title: 在 Azure Kubernetes Service (AKS) 群集上，使用开放式自由或 WebSphere 自由部署 Java 应用程序
description: 在 Azure Kubernetes Service (AKS) 群集上，使用开放式自由或 WebSphere 自由部署 Java 应用程序。
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java、jakartaee、javaee、microprofile、开放式、websphere、、aks、kubernetes
ms.openlocfilehash: 93ffa3ded4d0771438c5d6a2dc23e6e184f04fe2
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227588"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes Service (AKS) 群集上，使用开放式自由或 WebSphere 自由部署 Java 应用程序

本指南演示如何在开放的自由或 WebSphere 自由运行时运行 Java、Java EE、 [雅加达 EE](https://jakarta.ee/)或 [MicroProfile](https://microprofile.io/) 应用程序，然后使用开放式自由运算符将容器化应用程序部署到 AKS 群集。 开放式自由运算符简化了在开放自由 Kubernetes 群集上运行的应用程序的部署和管理。 你还可以执行更高级的操作，如使用运算符收集跟踪和转储。 本文介绍如何准备可自由应用的应用程序，构建应用程序 Docker 映像并在 AKS 群集上运行容器化应用程序。  有关开放自由的详细信息，请参阅 [打开自由项目页](https://openliberty.io/)。 有关 IBM WebSphere 的更多详细信息，请参阅 [WebSphere 自由产品页](https://www.ibm.com/cloud/websphere-liberty)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 本文需要 Azure CLI 的最新版本。 如果使用 Azure Cloud Shell，则最新版本已安装。
* 如果本地运行本指南中的命令 (而不是 Azure Cloud Shell) ：
  * 使用安装了类似于 Unix 的操作系统 (例如，Ubuntu、macOS) 准备本地计算机。
  * 安装 Java SE 实现 (例如， [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)) 。
  * 安装 [Maven](https://maven.apache.org/download.cgi) 3.5.0 或更高版本。
  * 安装适用于你的操作系统的 [Docker](https://docs.docker.com/get-docker/) 。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 在 *eastus* 位置使用 [az group create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create)命令创建资源组、以 *java 为* 中心的项目。 稍后将用于创建 Azure 容器注册表 (ACR) 实例和 AKS 群集。 

```azurecli-interactive
az group create --name java-liberty-project --location eastus
```

## <a name="create-an-acr-instance"></a>创建 ACR 实例

使用 [az acr create](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az_acr_create) 命令创建 acr 实例。 下面的示例创建一个名为 *youruniqueacrname* 的 ACR 实例。 请确保 *youruniqueacrname* 在 Azure 中是唯一的。

```azurecli-interactive
az acr create --resource-group java-liberty-project --name youruniqueacrname --sku Basic --admin-enabled
```

短时间之后，应会看到包含以下内容的 JSON 输出：

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>连接到 ACR 实例

若要将图像推送到 ACR 实例，需要先登录到该实例。 运行以下命令以验证连接：

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

`Login Succeeded`如果已成功登录到 ACR 实例，则会在命令输出的末尾看到。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

使用 [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create) 命令创建 AKS 群集。 以下示例创建一个具有一个节点的名为 myAKSCluster 的群集。 此操作将需要几分钟才能完成。

```azurecli-interactive
az aks create --resource-group java-liberty-project --name myAKSCluster --node-count 1 --generate-ssh-keys --enable-managed-identity
```

几分钟后，该命令完成并返回有关群集的 JSON 格式信息，其中包括：

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>连接到 AKS 群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_install_cli) 命令：

```azurecli-interactive
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials) 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group java-liberty-project --name myAKSCluster --overwrite-existing
```

> [!NOTE]
> 以上命令使用 [Kubernetes 配置文件](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)的默认位置，即 `~/.kube/config`。 可以使用 --file 为 Kubernetes 配置文件指定其他位置。

若要验证到群集的连接，请使用 [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令返回群集节点列表。

```azurecli-interactive
kubectl get nodes
```

以下示例输出显示在上一步创建的单个节点。 请确保节点的状态为 *Ready*：

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>安装开放式自由运算符

创建并连接到群集后，请通过运行以下命令来安装 [开放式自由运算符](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) 。

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>构建应用程序映像

若要在 AKS 群集上部署并运行你的自由应用程序，请使用 [开放式自由容器映像](https://github.com/OpenLiberty/ci.docker) 或 [WebSphere 自由容器映像](https://github.com/WASdev/ci.docker)，将应用程序容器化为 Docker 映像。

1. 克隆本指南的示例代码。 该示例位于 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks)上。
1. 将目录更改为 `javaee-app-simple-cluster` 你的本地副本。
1. 运行 `mvn clean package` 将应用程序打包。
1. 运行以下命令之一来生成应用程序映像并将其推送到 ACR 实例。
   * 如果希望使用开放式自由作为轻量开源 Java™运行时，请使用开放式自由基映像生成：

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * 如果希望使用开放式自由商业版本，请使用 WebSphere 的 "自由" 基本映像生成：

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>在 AKS 群集上部署应用程序

按照以下步骤在 AKS 群集上部署可自由应用的应用程序。

1. 创建一个请求机密，以便对 AKS 群集进行身份验证，以便从 ACR 实例请求映像。

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. 验证当前工作目录是否为 `javaee-app-simple-cluster` 本地克隆。
1. 运行以下命令，将包含3个副本的自由应用程序部署到 AKS 群集。 命令输出也显示为内联。

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. 等待，直到在 `3/3` 列下显示 `READY` `3` `AVAILABLE` ，在列下，使用 `CTRL-C` 停止 `kubectl` 监视进程。

### <a name="test-the-application"></a>测试应用程序

当应用程序运行时，Kubernetes 负载均衡器服务会向 internet 公开应用程序前端。 此过程可能需要一段时间才能完成。

若要监视进度，请将 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令与 `--watch` 参数配合使用。

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   9080:31732/TCP   68s
```

等待 *外部 ip* 地址从 " *挂起* " 更改为实际的公共 IP 地址，使用 `CTRL-C` 停止 `kubectl` 监视进程。

在上面的示例中打开 web 浏览器，以访问服务 (的外部 IP 地址和端口 `52.152.189.57:9080`) 查看应用程序主页。 你应看到应用程序副本的 pod 名称显示在页面的左上角。 等待几分钟并刷新页面，你可能会看到因 AKS 群集提供的负载平衡而显示的不同 pod 名称。

:::image type="content" source="./media/howto-deploy-java-liberty-app/java-liberty-app-aks-deployed-success.png" alt-text="已成功在 AKS 上部署 Java 自由应用程序":::

>[!NOTE]
> - 应用程序当前未使用 HTTPS。 建议[使用你自己的证书启用 TLS](ingress-own-tls.md)。

## <a name="clean-up-the-resources"></a>清理资源

若要避免 Azure 费用，应清除不需要的资源。  如果不再需要群集，请使用 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 命令删除资源组、容器服务、容器注册表和所有相关资源。

```azurecli-interactive
az group delete --name java-liberty-project --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

可以通过本指南中使用的参考了解详细信息：

* [Azure Kubernetes 服务](https://azure.microsoft.com/free/services/kubernetes-service/)
* [自由打开](https://openliberty.io/)
* [开放式自由运算符](https://github.com/OpenLiberty/open-liberty-operator)
* [打开自由服务器配置](https://openliberty.io/docs/ref/config/)
* [自由 Maven 插件](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [打开自由容器映像](https://github.com/OpenLiberty/ci.docker)
* [WebSphere 自由容器映像](https://github.com/WASdev/ci.docker)
