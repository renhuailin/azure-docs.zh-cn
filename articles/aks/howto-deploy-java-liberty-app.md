---
title: 在 Azure Kubernetes Service (AKS) 群集上使用 Open Liberty 或 WebSphere Liberty 部署 Java 应用程序
description: 在 Azure Kubernetes Service (AKS) 群集上使用 Open Liberty 或 WebSphere Liberty 部署 Java 应用程序。
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aks, kubernetes
ms.custom:
- devx-track-java
- devx-track-javaee
ms.openlocfilehash: fd36c92764f8acd9526fdd04f7ef60e4219839f3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350771"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes Service (AKS) 群集上使用 Open Liberty 或 WebSphere Liberty 部署 Java 应用程序

本文演示了以下内容的操作方法：  
* 在 Open Liberty 或 WebSphere Liberty 运行时运行 Java、Java EE、Jakarta EE 或 MicroProfile 应用程序。
* 使用 Open Liberty 容器映像构建应用程序 Docker 映像。
* 使用 Open Liberty Operator 将容器化应用程序部署到 AKS 群集。   

Open Liberty Operator 简化了在 Kubernetes 群集上运行的应用程序的部署和管理。 利用 Open Liberty Operator，还可以执行更高级的操作，例如收集跟踪和转储。 

有关 Open Liberty 的详细信息，请参阅 [Open Liberty 项目页](https://openliberty.io/)。 有关 IBM WebSphere Liberty 的详细信息，请参阅 [WebSphere Liberty 产品页](https://www.ibm.com/cloud/websphere-liberty)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 本文需要 Azure CLI 的最新版本。 如果使用 Azure Cloud Shell，则最新版本已安装。
* 如果在本地（而不是在 Azure Cloud Shell 中）运行本指南中的命令：
  * 准备安装了 Unix 之类操作系统（如 Ubuntu、macOS、适用于 Linux 的 Windows 子系统）的本地计算机。
  * 安装 Java SE 实现（例如 [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)）。
  * 安装 [Maven](https://maven.apache.org/download.cgi) 3.5.0 或更高版本。
  * 为你的 OS 安装 [Docker](https://docs.docker.com/get-docker/)。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。  

使用 [az group create](/cli/azure/group#az_group_create) 命令在 eastus 位置创建名为“java-liberty-project”的资源组 。 稍后将使用此资源组创建 Azure 容器注册表 (ACR) 实例和 AKS 群集。 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>创建 ACR 实例

使用 [az acr create](/cli/azure/acr#az_acr_create) 命令创建 ACR 实例。 下面的示例创建名为“youruniqueacrname”的 ACR 实例。 请确保“youruniqueacrname”在 Azure 中是独一无二的。

```azurecli-interactive
export REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

很快，你应该会看到包含以下内容的 JSON 输出：

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>连接到该 ACR 实例

需要先登录到该 ACR 实例，然后才能向它推送映像。 运行以下命令，以验证连接：

```azurecli-interactive
export LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
export USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
export PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

如果已成功登录到该 ACR 实例，应该会在命令输出的末尾看到 `Login Succeeded`。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

使用 [az aks create](/cli/azure/aks#az_aks_create) 命令创建 AKS 群集。 以下示例创建一个具有一个节点的名为 myAKSCluster 的群集。 此操作将需要几分钟才能完成。

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

在几分钟之后，该命令会完成并返回有关群集的 JSON 格式的信息，包括以下内容：

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>连接到 AKS 群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli](/cli/azure/aks#az_aks_install_cli) 命令：

```azurecli-interactive
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
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

## <a name="install-open-liberty-operator"></a>安装 Open Liberty Operator

在创建并连接到群集后，请通过运行以下命令来安装 [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.1)。

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>生成应用程序映像

若要在 AKS 群集上部署并运行 Liberty 应用程序，请使用 [Open Liberty 容器映像](https://github.com/OpenLiberty/ci.docker)或 [WebSphere Liberty 容器映像](https://github.com/WASdev/ci.docker)将应用程序容器化为 Docker 映像。

1. 克隆本指南的示例代码。 该示例位于 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks) 上。
1. 将目录更改为本地克隆的 `javaee-app-simple-cluster`。
1. 运行 `mvn clean package` 来打包该应用程序。
1. 运行 `mvn liberty:dev` 来测试该应用程序。 如果成功，应该会在命令输出中看到 `The defaultServer server is ready to run a smarter planet.`。 使用 `CTRL-C` 停止应用程序。
1. 检索 `pom.xml` 中定义的 `artifactId` 和 `version` 属性的值。

   ```azurecli-interactive
   artifactId=$(mvn -q -Dexec.executable=echo -Dexec.args='${project.artifactId}' --non-recursive exec:exec)
   version=$(mvn -q -Dexec.executable=echo -Dexec.args='${project.version}' --non-recursive exec:exec)
   ```
1. 运行 `cd target` 以将目录更改为示例的构建。
1. 运行以下某个命令来生成应用程序映像并将其推送到 ACR 实例。
   * 如果希望使用 Open Liberty 作为轻量开源 Java™ 运行时，请使用 Open Liberty 基础映像来生成：

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t ${artifactId}:${version} -r $REGISTRY_NAME .
     ```

   * 如果希望使用 Open Liberty 的商业版，请使用 WebSphere Liberty 基础映像来生成：

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t ${artifactId}:${version} -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>在 AKS 群集上部署应用程序

按照以下步骤在 AKS 群集上部署 Liberty 应用程序。

1. 若要从 ACR 实例拉取映像，请创建拉取机密，以便对 AKS 群集进行身份验证。

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. 验证当前工作目录是否为本地克隆的 `javaee-app-simple-cluster/target`。
1. 运行以下命令，将包含 3 个副本的 Liberty 应用程序部署到 AKS 群集。 命令输出也会以内联方式显示。

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-cafe-cluster"
   kubectl apply -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication ${artifactId}-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-cafe-cluster         youruniqueacrname.azurecr.io/javaee-cafe:1.0.25         True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment ${artifactId}-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-cluster         0/3     3            0           20s
   ```

1. 请等候，直至看到 `READY` 列下出现 `3/3`，并且在 `AVAILABLE` 列下出现 `3`，然后使用 `CTRL-C` 来停止 `kubectl` 监视进程。

### <a name="test-the-application"></a>测试应用程序

在应用程序运行时，Kubernetes 负载均衡器服务会将向 Internet 公开应用程序前端。 此进程可能需要一段时间才能完成。

若要监视进度，请将 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令与 `--watch` 参数配合使用。

```azurecli-interactive
kubectl get service ${artifactId}-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-cafe-cluster         LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

在 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址后，请使用 `CTRL-C` 来停止 `kubectl` 监视进程。

打开 Web 浏览器访问服务的外部 IP 地址（上面示例中的 `52.152.189.57`），以查看应用程序主页。 你应该会看到应用程序副本的 Pod 名称显示在该页面的左上角。 请等待几分钟并刷新该页面，以查看由于 AKS 群集提供的负载均衡而显示的不同的 Pod 名称。

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="成功在 AKS 上部署了 Java liberty 应用程序":::

>[!NOTE]
> - 目前该应用程序未使用 HTTPS。 建议[使用你自己的证书启用 TLS](ingress-own-tls.md)。

## <a name="clean-up-the-resources"></a>清理资源

若要避免 Azure 费用，应清除不需要的资源。  如果不再需要群集，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令来删除资源组、容器服务、容器注册表和所有的相关资源。

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

可以从本指南中使用的参考资料中了解到更多信息：

* [Azure Kubernetes 服务](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Open Liberty](https://openliberty.io/)
* [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Open Liberty 服务器配置](https://openliberty.io/docs/ref/config/)
* [Liberty Maven 插件](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Open Liberty 容器映像](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty 容器映像](https://github.com/WASdev/ci.docker)
