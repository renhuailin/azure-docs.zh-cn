---
title: 升级启用了 Azure Arc 的 Kubernetes 代理
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 已启用 Azure Arc Kubernetes 的控件代理升级
keywords: Kubernetes，Arc，Azure，K8s，容器，代理，升级
ms.openlocfilehash: 172654f655e594c295a8807b417c32d7849bd4b1
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121909"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>升级启用了 Azure Arc 的 Kubernetes 代理

启用 Azure Arc 后，Kubernetes 为其代理提供自动升级和手动升级功能。 如果使用禁用自动升级，而是依赖手动升级，则版本支持策略适用于 Arc 代理和基础 Kubernetes 群集。

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>将群集连接到 Azure 时，开启或关闭自动升级

启用 Azure Arc 后，Kubernetes 为其代理提供现成的自动升级功能。

以下命令将群集连接到 **启用了** 自动升级的 Azure Arc：

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

启用自动升级后，代理会每小时轮询 Azure，以获取较新版本的代理。 如果代理找到可用的较新版本，则会触发 Azure Arc 代理的 Helm 图表升级。

若要选择退出自动升级，请在将 `--disable-auto-upgrade` 群集连接到 Azure Arc 时指定参数。以下命令将群集连接到 **已禁用** 自动升级的 Azure Arc：

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> 如果计划禁用自动升级，请参阅支持 Azure Arc 的 [版本支持策略](#version-support-policy) Kubernetes。

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>在将群集连接到 Azure Arc 后开启/关闭自动升级

将群集连接到 Azure Arc 后，可以使用命令切换自动升级功能 `az connectedk8s update` ，如下所示：

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>手动升级代理

如果已为代理禁用自动升级，则可以使用命令手动启动这些代理的升级，如下 `az connectedk8s upgrade` 所示：

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

启用 Azure Arc 后，Kubernetes 遵循的标准 [语义版本控制方案](https://semver.org/) `MAJOR.MINOR.PATCH` 来版本控制其代理。 

版本中的每个编写表示与前一版本的一般兼容性：

* 当存在不兼容的 API 更新或向后兼容性可能会损坏时，**主版本** 会发生更改。
* 当功能更改与其他次要版本向后兼容时，**次版本** 会发生变化。
* 当进行向后兼容的 bug 修复时，**修补程序版本** 会发生更改。

## <a name="version-support-policy"></a>版本支持策略

创建支持问题时，启用了 Azure Arc 的 Kubernetes 实践以下版本支持策略：

* 启用了 Azure Arc 的 Kubernetes 代理的支持窗口为 "N-2"，其中 "N" 是最新的代理次要版本。 
  * 例如，如果启用了 Azure Arc Kubernetes，则 Azure Arc 支持0.28、0.28、0.27、0.27、0.26、0.26、、、和。

* 连接到 Azure Arc 的 Kubernetes 群集具有支持窗口 "N-2"，其中 "N" 是 [上游 Kubernetes](https://github.com/kubernetes/kubernetes/releases)的最新稳定次版本。 
  * 例如，如果 Kubernetes 引入1.20，则支持1.20、1.20、1.19、1.19、、、、、和。

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>启用了 Azure Arc 的次要版本 Kubernetes 的频率如何？

每月发布一次 Azure Arc 已启用 Kubernetes 代理。

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>如果在官方支持窗口之外使用代理版本或 Kubernetes 版本，会发生什么情况？

"不受支持" 意味着你正在运行的版本不在支持的代理和上游 Kubernetes 群集版本的外部。 若要继续执行支持问题，将要求你将群集和代理升级到受支持的版本。

## <a name="next-steps"></a>后续步骤

* 逐步完成将 [Kubernetes 群集连接到 Azure Arc](./connect-cluster.md)的入门教程。
* Kubernetes 群集是否已连接 Azure Arc？ [在启用了 Arc 的 Kubernetes 群集上创建配置](./use-gitops-connected-cluster.md)。
* 了解如何 [使用 Azure 策略来大规模应用配置](./use-azure-policy.md)。