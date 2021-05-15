---
title: 创建已启用 Azure Dev Spaces 的 Kubernetes 群集 - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 了解如何直接从浏览器快速创建为 Azure Dev Spaces 启用的 Kubernetes 群集，而无需安装任何内容。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: ce73f46a2451dfa87751d90f9cd6b31d5c80683f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91963544"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>通过 Azure Cloud Shell 创建已启用 Azure Dev Spaces 的 Kubernetes 群集

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

使用本页的“试用”按钮，可以通过 [Azure Cloud Shell](/azure/cloud-shell) 来创建 Azure Kubernetes 服务。 如果未登录，请按照提示使用 Azure 帐户登录，然后在出现 Azure Cloud Shell 提示符时在 Azure Cloud Shell 提示符下键入命令。

## <a name="create-the-cluster"></a>创建群集

首先，在[支持 Azure Dev Spaces 的区域][supported-regions]创建资源组。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用以下命令创建 Kubernetes 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

创建群集需要几分钟时间。  完成后，输出以 JSON 格式显示。 查找 `provisioningState` 并验证它是否为 `Succeeded`。

## <a name="next-steps"></a>后续步骤

如需完整教程的链接，请参阅 [Azure Dev Spaces](../index.yml)。

> [!IMPORTANT]
> 许多 Azure Dev Spaces 快速入门和教程都使用 Azure Dev Spaces CLI 来执行操作。 无法在 Azure Cloud Shell 中安装 Azure Dev Spaces CLI。


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
