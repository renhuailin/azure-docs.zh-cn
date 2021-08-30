---
title: 检查注册表运行状况
description: 了解如何运行快速诊断命令，以便确定使用 Azure 容器注册表时常见的问题，包括本地 Docker 配置和连接到注册表的问题
ms.topic: article
ms.date: 07/14/2021
ms.openlocfilehash: b0a95179e01de63185092c965b3290017dc96dfb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464296"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>检查 Azure 容器注册表的运行状况

使用 Azure 容器注册表时，可能会偶尔遇到问题。 例如，由于本地环境中的 Docker 出现问题，可能无法拉取容器映像。 或者，可能会因网络问题而无法连接到注册表。 

进行诊断时，首先请运行 [az acr check-health][az-acr-check-health] 命令，了解环境的运行状况，并可选择访问目标注册表。 此命令可在 Azure CLI 2.0.67 或更高版本中使用。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

有关其他注册表故障排除指南，请参阅：
* [注册表登录故障排除](container-registry-troubleshoot-login.md)
* [排查与注册表相关的网络问题](container-registry-troubleshoot-access.md)
* [注册表性能故障排除](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>运行 az acr check-health

下述示例演示了如何通过不同方式运行 `az acr check-health` 命令。

> [!NOTE]
> 如果在 Azure Cloud Shell 中运行此命令，则不会检查本地环境。 但是，你可以检查对目标注册表的访问权限。

### <a name="check-the-environment-only"></a>仅检查环境

若要检查本地 Docker 守护程序、CLI 版本和 Helm 客户端配置，请在没有其他参数的情况下运行此命令：

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>检查环境和目标注册表

若要检查对注册表的访问权限并执行本地环境检查，请传递目标注册表的名称。 例如：

```azurecli
az acr check-health --name myregistry
```

### <a name="check-registry-access-in-a-virtual-network"></a>检查虚拟网络中的注册表访问权限

若要验证要路由到专用终结点的 DNS 设置，请传递虚拟网络的名称或资源 ID。 当虚拟网络所在的订阅或资源组与注册表不同时，需要资源 ID。

```azurecli
az acr check-health --name myregistry --vnet myvnet
```

## <a name="error-reporting"></a>错误报告

此命令将信息记录到标准输出。 如果检测到问题，它会提供错误代码和说明。 若要详细了解代码和可能的解决方案，请查看[错误参考](container-registry-health-error-reference.md)。

默认情况下，该命令在发现错误时会停止。 也可运行此命令，这样它就会提供所有运行状况检查的输出，即使发现了错误。 按以下示例中所示添加参数 `--ignore-errors`：

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry; skip confirmation to pull image
az acr check-health --name myregistry --ignore-errors --yes
```      

示例输出：

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  

## <a name="next-steps"></a>后续步骤

若要详细了解 [az acr check-health][az-acr-check-health] 命令返回的错误代码，请参阅[运行状况检查错误参考](container-registry-health-error-reference.md)。

参阅[常见问题解答](container-registry-faq.yml)，其中解答了有关 Azure 容器注册表的常见问题和其他已知问题。





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
