---
title: 将 Azure 容器注册表与 Azure Red Hat OpenShift 配合使用
description: 了解如何在 Azure Red Hat OpenShift 群集中从 Azure 容器注册表拉取和运行容器。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634407"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>将 Azure 容器注册表与 Azure Red Hat OpenShift (ARO) 配合使用

Azure 容器注册表 (ACR) 是一项托管容器注册表服务，可用于存储具有企业功能（如异地复制）的专用 Docker 容器映像。 如果要从 ARO 群集访问 ACR，群集可以通过将 Docker 登录凭据存储在 Kubernetes 机密中来使用 ACR 进行身份验证。  同样，在拉取映像时，ARO 群集可以使用 pod 规范中的 imagePullSecret 对注册表进行身份验证。 在本文中，你将了解如何使用 Azure Red Hat OpenShift 群集设置 Azure 容器注册表，以存储和拉取专用 Docker 容器映像。

## <a name="prerequisites"></a>先决条件

本指南假定你已有 Azure 容器注册表。 若没有，请使用 Azure 门户或按照 [Azure CLI 说明](../container-registry/container-registry-get-started-azure-cli.md)创建容器注册表。

本文还假定你已有一个 Azure Red Hat OpenShift 群集并安装了 `oc` CLI。 如果没有，请按照[创建 ARO 群集教程](tutorial-create-cluster.md)中的说明进行操作。

## <a name="get-a-pull-secret"></a>获取拉取机密

要从 ARO 群集访问注册表，需要从 ACR 获取一个拉取机密。

可以使用 Azure 门户或 Azure CLI 来获取拉取机密凭据。

如果使用 Azure 门户，请导航到 ACR 实例，然后选择“访问密钥”。  `docker-username` 是容器注册表的名称，请为 `docker-password` 使用 password 或 password2。

![访问密钥](./media/acr-access-keys.png)

可以使用 Azure CLI 获取这些凭据：
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>创建 Kubernetes 机密

现在，我们将使用这些凭据创建 Kubernetes 机密。 使用 ACR 凭据执行以下命令：

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>此机密将存储在当前 OpenShift 项目（Kubernetes 命名空间）中，并且只能由在该项目中创建的 pod 引用。  请参阅此[文档](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html)，获取有关创建群集范围内的拉取机密的进一步说明。

## <a name="create-a-pod-using-a-private-registry-image"></a>使用专用注册表映像创建 pod

现在，我们已将你的 ARO 群集连接到 ACR，接下来让我们从 ACR 中拉取一个图像来创建一个 pod。

从 podSpec 开始，并指定作为 imagePullSecret 创建的机密：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

要测试 pod 是否已启动并正在运行，请执行此命令并等待，直到状态为“正在运行”：

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>后续步骤

* [Azure 容器注册表](../container-registry/container-registry-concepts.md)
* [快速入门：使用 Azure CLI 创建专用容器注册表](../container-registry/container-registry-get-started-azure-cli.md)
