---
title: 使用 GitHub Actions 生成和测试容器并将其部署到 Azure Kubernetes 服务
description: 了解如何使用 GitHub Actions 将容器部署到 Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: d03acab340e593a925f042ca41f9e8967b468858
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605439"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>用于将容器部署到 Kubernetes 服务的 GitHub Actions

可以通过 [GitHub Actions](https://docs.github.com/en/free-pro-team@latest/actions) 灵活地生成自动化软件开发生命周期工作流。 可以使用多个 Kubernetes 操作将 Azure 容器注册表中的容器部署到具有 GitHub 操作的 Azure Kubernetes 服务。 

## <a name="prerequisites"></a>先决条件 

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个 GitHub 帐户。 如果没有该帐户，请注册[免费版](https://github.com/join)。  
- 工作 Kubernetes 群集
    - [教程：为 Azure Kubernetes 服务准备应用程序](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>工作流文件概述

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

对于以 AKS 为目标的工作流，该文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 登录到专用容器注册表 (ACR) |
|**生成** | 生成和推送容器映像  |
|**部署** | 1.设置目标 AKS 群集 |
| |2.在 Kubernetes 群集中创建通用/docker 注册表机密  |
||3.部署到 Kubernetes 群集|

## <a name="create-a-service-principal"></a>创建服务主体

可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 可以使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

在上述命令中，将占位符替换为你的订阅 ID 和资源组。 输出是用于访问资源的角色分配凭据。 此命令应输出下面这样的 JSON 对象。

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
请复制此 JSON 对象，它可以用来从 GitHub 进行身份验证。

## <a name="configure-the-github-secrets"></a>配置 GitHub 机密

按照以下步骤配置机密：

1. 在 [GitHub](https://github.com/) 中浏览到存储库，选择“设置”>“机密”>“添加新机密”。 

    ![屏幕截图显示了存储库的“添加新机密”链接。](media/kubernetes-action/secrets.png)

2. 将上述 `az cli` 命令的内容作为机密变量的值粘贴。 例如，`AZURE_CREDENTIALS`。

3. 同样，为容器注册表凭据定义以下附加机密，并在 Docker 登录操作中设置它们。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 在定义后，会看到如下所示的机密。

    ![屏幕截图显示了存储库的现有机密。](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>生成容器映像并将其部署到 Azure Kubernetes 服务群集

容器映像的生成和推送使用 `Azure/docker-login@v1` 操作完成。 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes 服务群集

若要将容器映像部署到 AKS，需使用 `Azure/k8s-deploy@v1` 操作。 该操作有五个参数：

| **参数**  | **解释**  |
|---------|---------|
| **namespace** | （可选）选择目标 Kubernetes 命名空间。 如果未提供命名空间，则命令会在默认命名空间中运行 | 
| **manifests** |  （必需）将要用于部署的清单文件的路径 |
| **images** | （可选）将要用于在清单文件上进行替换的映像的完全限定资源 URL |
| **imagepullsecrets** | （可选）已在群集中设置的 docker 注册表机密的名称。 这些机密名称的每一个都在输入清单文件中的工作负载的 imagePullSecrets 字段下添加 |
| **kubectl-version** | （可选）安装 kubectl 二进制文件的特定版本 |


在部署到 AKS 之前，需要设置目标 Kubernetes 命名空间，并创建映像请求机密。 若要详细了解如何使用拉取映像，请参阅 [从 Azure 容器注册表将映像提取到 Kubernetes 群集](../container-registry/container-registry-auth-kubernetes.md)。 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


完成包含操作的部署 `k8s-deploy` 。 将环境变量替换为应用程序的值。 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Kubernetes 群集、容器注册表和存储库，请通过删除资源组和 GitHub 存储库来清理部署的资源。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Kubernetes 服务](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>更多 Kubernetes GitHub 操作

* [Kubectl 工具安装程序](https://github.com/Azure/setup-kubectl) (`azure/setup-kubectl`) ：在运行程序上安装特定版本的 Kubectl。
* [Kubernetes 设置上下文](https://github.com/Azure/k8s-set-context) (`azure/k8s-set-context`) ：设置目标 Kubernetes 群集上下文，其他操作将使用此上下文，或者运行任何 kubectl 命令。
* [AKS 设置上下文](https://github.com/Azure/aks-set-context) (`azure/aks-set-context`) ：设置目标 Azure Kubernetes Service 群集上下文。
* [Kubernetes 创建机密](https://github.com/Azure/k8s-create-secret) (`azure/k8s-create-secret`) ：在 Kubernetes 群集中创建一般机密或 docker 注册表机密。
* [Kubernetes 部署](https://github.com/Azure/k8s-deploy) (`azure/k8s-deploy`) ：制作并将清单部署到 Kubernetes 群集。
* [设置 Helm](https://github.com/Azure/setup-helm) (`azure/setup-helm`) ：在运行程序上安装特定版本的 Helm 二进制文件。
* [Kubernetes 制作](https://github.com/Azure/k8s-bake) (`azure/k8s-bake`) ：制作用于使用 helm2、kustomize 或 kompose 的部署的清单文件。
* [Kubernetes](https://github.com/azure/k8s-lint) 不起毛 `azure/k8s-lint` 的 () ：验证/不的清单文件。
