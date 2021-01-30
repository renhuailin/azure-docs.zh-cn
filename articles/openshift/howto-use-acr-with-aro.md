---
title: 将 Azure 容器注册表与 Azure Red Hat OpenShift 配合使用
description: 了解如何在 Azure Red Hat OpenShift 群集中从 Azure 容器注册表拉取和运行容器。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 91b43aa939579f5df8115048cf52b9bcdd7ade3d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063740"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>将 Azure 容器注册表与 Azure Red Hat OpenShift (ARO) 

Azure 容器注册表 (ACR) 是一项托管容器注册表服务，可用于存储具有企业功能（如异地复制）的专用 Docker 容器映像。 若要从 ARO 群集访问 ACR，群集可以通过将 Docker 登录凭据存储在 Kubernetes 机密中，通过 ACR 进行身份验证。  同样，在拉取映像时，ARO 群集可以使用 pod 规范中的 imagePullSecret 对注册表进行身份验证。 在本文中，你将了解如何使用 Azure Red Hat OpenShift 群集设置 Azure 容器注册表，以存储和拉取专用 Docker 容器映像。

## <a name="prerequisites"></a>先决条件

本指南假定你已有 Azure 容器注册表。 否则，请使用 Azure 门户或 [Azure CLI 说明](../container-registry/container-registry-get-started-azure-cli.md) 创建容器注册表。

本文还假定你已有一个 Azure Red Hat OpenShift 群集并 `oc` 安装了 CLI。 如果没有，请按照 [创建 ARO 群集教程](tutorial-create-cluster.md)中的说明进行操作。

## <a name="get-a-pull-secret"></a>获取请求机密

你需要从 ACR 获取一个请求机密，才能从 ARO 群集访问注册表。

若要获取你的请求机密凭据，可以使用 Azure 门户或 Azure CLI。

如果使用 Azure 门户，请导航到 ACR 实例，然后选择 " **访问密钥**"。  你 `docker-username` 是容器注册表的名称，请使用 password 或 password2 作为 `docker-password` 。

![访问密钥](./media/acr-access-keys.png)

相反，你可以使用 Azure CLI 获取这些凭据：
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>创建 Kubernetes 机密

现在，我们将使用这些凭据创建 Kubernetes 机密。 用 ACR 凭据执行以下命令：

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>此密钥将存储在当前 OpenShift 项目 (Kubernetes 命名空间) 中，并将仅在该项目中创建的 pod 可引用。  请参阅此 [文档](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) ，以获取有关创建群集范围的请求机密的进一步说明。

## <a name="create-a-pod-using-a-private-registry-image"></a>使用专用注册表映像创建 pod

现在，我们已将你的 ARO 群集连接到 ACR，接下来让我们从 ACR 中提取一个图像来创建一个 pod。

从 podSpec 开始，并指定创建为 imagePullSecret 的机密：

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

若要测试 pod 是否已启动并正在运行，请执行此命令并等待状态为 " **正在运行**"：

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>后续步骤

* [Azure 容器注册表](../container-registry/container-registry-concepts.md)
* [快速入门：使用 Azure CLI 创建专用容器注册表](../container-registry/container-registry-get-started-azure-cli.md)
