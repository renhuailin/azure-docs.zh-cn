---
title: 升级已启用 Azure Arc 的 Kubernetes 代理
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 控制已启用 Azure Arc 的 Kubernetes 的代理升级
keywords: Kubernetes, Arc, Azure, K8s, 容器, 代理, 升级
ms.openlocfilehash: 29df9973ead167e7774924cc3a0c685606092d28
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442518"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>升级已启用 Azure Arc 的 Kubernetes 代理

已启用 Azure Arc 的 Kubernetes 为其代理提供自动升级和手动升级功能。 如果禁用自动升级而依赖手动升级，则版本支持策略适用于 Arc 代理和基础 Kubernetes 群集。

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>将群集连接到 Azure Arc 时开启或关闭自动升级

已启用 Azure Arc 的 Kubernetes 为其代理提供现成的自动升级功能。

以下命令可将群集连接到已启用自动升级的 Azure Arc：

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

启用自动升级后，代理会每小时轮询 Azure，查询是否有较新版本的代理可用。 如果代理找到了可用的较新版本，则会触发对 Azure Arc 代理执行 Helm 图表升级。

若要选择退出自动升级，请在将群集连接到 Azure Arc 时指定 `--disable-auto-upgrade` 参数。以下命令将群集连接到 Azure Arc，其中将自动升级设置为“禁用”：

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> 如果计划禁用自动升级，请参阅已启用 Azure Arc 的 Kubernetes 的[版本支持策略](#version-support-policy)。

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>将群集连接到 Azure Arc 后开启/关闭自动升级

将群集连接到 Azure Arc 后，可以使用 `az connectedk8s update` 命令切换自动升级功能，如下所示：

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>手动升级代理

如果已对代理禁用自动升级，则可以使用 `az connectedk8s upgrade` 命令手动对这些代理启动升级，如下所示：

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.1.0
```

关于代理的版本控制，已启用 Azure Arc 的 Kubernetes 遵循 `MAJOR.MINOR.PATCH` 的标准[语义版本控制方案](https://semver.org/)。 

版本中的每个编写表示与前一版本的一般兼容性：

* 当存在不兼容的 API 更新或向后兼容性可能损坏时，表示重要版本变化。
* 当功能更改与其他次要版本向后兼容时，表示次要版本变化。
* 进行后向兼容的 Bug 修复时，表示修补程序版本变化。

## <a name="version-support-policy"></a>版本支持策略

创建支持问题时，已启用 Azure Arc 的 Kubernetes 将执行以下版本支持策略：

* 已启用 Azure Arc 的 Kubernetes 代理的支持窗口为“N-2”，其中“N”代表最新的代理次要版本。 
  * 例如，如果已启用 Azure Arc 的 Kubernetes 今天发布了 0.28.a，则，则 0.28.a、0.28.b、0.27.c、0.27.d、0.26.e 和 0.26.f 均受 Azure Arc 支持。

* 连接到 Azure Arc 的 Kubernetes 群集的支持窗口为“N-2”，其中“N”代表[上游 Kubernetes](https://github.com/kubernetes/kubernetes/releases) 的最新稳定次要版本。 
  * 例如，如果 Kubernetes 今天发布了 1.20.a，则 1.20.a、1.20.b、1.19.c、1.19.d、1.18.e 和 1.18.f 均受支持。

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>已启用 Azure Arc 的 Kubernetes 多久发布一次次要版本？

已启用 Azure Arc 的 Kubernetes 代理大约每月发布一次次要版本。

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>如果使用的是官方支持窗口以外的代理版本或 Kubernetes 版本，会发生什么情况？

“支持以外”意味着你运行的版本不在“N-2”支持的代理和上游 Kubernetes 群集版本之内。 要继续解决支持问题，系统将要求用户将群集和代理升级到受支持的版本。

## <a name="next-steps"></a>后续步骤

* 参考快速入门[将 Kubernetes 群集连接到 Azure Arc](./quickstart-connect-cluster.md)。
* 已将 Kubernetes 群集连接到 Azure Arc？ [在已启用 Arc 的 Kubernetes 群集上创建配置](./tutorial-use-gitops-connected-cluster.md)。
* 了解如何[使用 Azure Policy 大规模应用配置](./use-azure-policy.md)。