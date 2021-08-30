---
title: Azure Kubernetes 服务中支持的 Kubernetes 版本
description: 了解 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 版本支持策略和群集生命周期
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.author: jpalma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8e883c92e2ce8d64c30aeb6c8a747c18935d36e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748909"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中支持的 Kubernetes 版本

Kubernetes 社区大约会每隔三个月发布次要版本。 最近，Kubernetes 社区[将每个版本的支持时长从 9 个月增加到了 12 个月](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)，从版本 1.19 开始。

次要版本包括新增功能和改进。 补丁发布更为频繁（有时每周都会发布），适用于次要版本中的关键 Bug 修复。 修补程序版本包括针对安全漏洞或主要 bug 的修复。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 对每个版本使用标准的[语义化版本控制](https://semver.org/)方案：

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

版本中的每个编写表示与前一版本的一般兼容性：

* 当存在不兼容的 API 更新或者后向兼容性可能损坏时，表示主要版本变化。
* 当功能更新与其他次要版本向后兼容时，表示次要版本变化。
* 当进行向后兼容的 bug 修复时，表示修补程序版本变化。

确保运行你正在运行的次要版本的最新修补程序版本。 例如，生产群集使用版本 `1.17.7`。 `1.17.8` 是 1.17 系列的最新可用修补程序版本。 应尽快升级到 `1.17.8` 以确保群集得到完全修补并受支持。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支持策略

AKS 定义了一个正式发布版，所有 SLO 或 SLA 衡量方案都采用此版本，并且在所有区域提供。 AKS 支持三个 Kubernetes GA 次要版本：

* AKS 中发布的最新 GA 次要版本（我们将其称为 N）。
* 以前的两个次要版本。
    * 每个受支持的次要版本还支持最多两 (2) 个稳定的修补程序。

AKS 可能还支持预览版本，这些版本带有明确的标签，使用时需遵守[预览版条款和条件][preview-terms]。

> [!NOTE]
> AKS 使用涉及逐步区域部署的安全部署做法。 这意味着，可能需要最长 10 个工作日才能使新发布或新版本在所有区域中均可用。

AKS 上的 Kubernetes 版本的受支持窗口称为“N-2”：(N (最新版本) -2 (次要版本))。

例如，如果 AKS 当前引入了 1.17.a，则提供以下版本的支持：

新的次要版本    |    支持的版本列表
-----------------    |    ----------------------
1.17.a               |    1.17.a、1.17.b、1.16.c、1.16.d、1.15.e、1.15.f

其中，“.字母”代表修补程序版本。

引入新的次要版本后，支持的最早次要版本和修补程序版本将被弃用并删除。 例如，当前支持的版本列表为：

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS 发布了 1.18.\*，将在 30 天内删除已终止支持的所有 1.15.\* 版本。

> [!NOTE]
> 如果客户运行不受支持的 Kubernetes 版本，则在请求群集支持时，系统将要求他们升级。 运行不受支持 Kubernetes 版本的群集未涵盖在 [AKS 支持策略](./support-policies.md)中。

除上述内容以外，AKS 还支持最多两个给定次要版本的修补程序版本。 因此，假设支持的版本如下：

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

如果 AKS 发布了 `1.17.9` 和 `1.16.11`，则最早的修补程序版本将被弃用并删除，支持的版本列表现为：

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>支持的 `kubectl` 版本

你可以使用一个相对于 kube-apiserver 版本较旧或较新的 `kubectl` 次要版本，这符合 [kubectl 的 Kubernetes 支持策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)。

例如，如果你的 kube-apiserver 处于 1.17，则可以将版本 1.16 到 1.18 的 `kubectl` 与该 kube-apiserver 结合使用    。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要安装或更新你版本的 `kubectl`，请运行 `az aks install-cli`。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

要安装或更新 `kubectl` 的版本，请运行 [Install-AzAksKubectl][install-azakskubectl]。

---

## <a name="release-and-deprecation-process"></a>发布和弃用流程

你可以在 [AKS Kubernetes 发布日历](#aks-kubernetes-release-calendar)上参考即将推出的版本发布和弃用。

对于 Kubernetes 的新次要版本：
  * AKS 至少在删除前 30 天在 [AKS 发行说明](https://aka.ms/aks/releasenotes)中发布预告，其中包含新版本发布的计划日期以及相应的旧版本弃用。
  * 如果新版本因 API 被弃用而导致群集出现问题，AKS 将使用 [Azure 顾问](../advisor/advisor-overview.md)向用户发出警报。 如果用户当前没有受到支持，也可以使用 Azure 顾问向用户发出警报。
  * AKS 会向具有 AKS 和门户访问权限的所有用户发布一个[服务运行状况通知](../service-health/service-health-overview.md)，并向订阅管理员发送一封电子邮件，其中包含计划的版本删除日期。

    > [!NOTE]
    > 若要确定订阅管理员或进行更改，请参阅[管理 Azure 订阅](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)。

  * 自版本删除起，用户有 30 天的时间升级到受支持的次要版本发布，以继续获得支持。

对于 Kubernetes 的新修补程序版本：
  * 由于修补程序版本的紧急性质，可以在修补程序变为可用时将其引入到服务中。
  * 通常情况下，对于新修补程序版本的发布，AKS 不会进行广泛地宣传。 但是，AKS 会持续监视和验证可用的 CVE 修补程序，以便及时在 AKS 中支持它们。 如果发现修补程序或需要用户进行操作，AKS 将通知用户升级到新发布的修补程序。
  * 自从 AKS 中删除修补程序版本之时起，用户有 30 天的时间升级到受支持的修补程序并继续获得支持。

### <a name="supported-versions-policy-exceptions"></a>支持的版本策略例外情况

AKS 保留无需提前通知即可添加或删除具有一个或多个影响生产的关键 bug 或安全问题的新/现有版本的权利。

特定的修补程序版本可能会跳过发布或者加速推出，具体取决于 bug 或安全问题的严重性。

## <a name="azure-portal-and-cli-versions"></a>Azure 门户和 CLI 版本

通过门户、Azure CLI 或 Azure PowerShell 部署 AKS 群集时，群集默认设置为 N-1 次要版本和最新补丁。 例如，如果 AKS 支持 1.17.a、1.17.b、1.16.c、1.16.d、1.15.e 和 1.15.f，则选择的默认版本为 1.16.c      。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要了解你的订阅和区域当前可用的版本，请使用 [az aks get-versions][az-aks-get-versions] 命令。 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```


### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要了解你的订阅和区域当前可用的版本，请使用 [Get-AzAksVersion][get-azaksversion] cmdlet。 以下示例列出了 *EastUS* 区域可用的 Kubernetes 版本：

```azurepowershell-interactive
Get-AzAksVersion -Location eastus
```

---

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes 发布日历

对于过去的发布历史记录，请参阅 [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History)。

|  K8s 版本 | 上游版本  | AKS 预览版  | AKS GA  | 生命周期结束 |
|--------------|-------------------|--------------|---------|-------------|
| 1.18  | 20-03-23  | 2020 年 5 月   | 2020 年 8 月  | *1.21 GA |
| 1.19  | 20-08-04  | 2020 年 9 月   | 2020 年 11 月  | 1.22 GA |
| 1.20  | 2020 年 12 月 8 日  | 2021 年 1 月   | 2021 年 3 月  | 1.23 GA |
| 1.21  | 2021 年 4 月 8 日 | 2021 年 5 月   | 2021 年 7 月  | 1.24 GA |
| 1.22  | Aug-04-21 | 2021 年 9 月   | 2021 年 10 月  | 1.25 GA |
| 1.23  | 2021 年 12 月 | 2022 年 1 月   | 2022 年 2 月  | 1.26 GA |

>[!NOTE]
>在 2021 年 7 月 31 日之前，AKS 版本 1.18 将继续可用。 此日期过后，AKS 将返回到其常规的三版本窗口支持。 请务必注意以下事项，因为从 2021 年 6 月 30 日到 7 月 31 日的支持将存在范围限制。 下面列出了会对用户进行限制的内容：
> - 在 1.18 上创建新的群集和节点池。
> - 1\.18 群集上的 CRUD 操作。
> - 非 Kubernetes 相关的平台问题的 Azure 支持。 平台问题包括在 Azure 上运行的网络、存储或计算方面的问题。 要求进行 K8s 修补和故障排除的任何支持请求都会被要求升级到受支持的版本。

## <a name="faq"></a>常见问题解答

**Microsoft 如何通知我关于新 Kubernetes 版本的发布？**

在我们的文档、[GitHub](https://github.com/Azure/AKS/releases) 以及向拥有即将不受支持的群集的订阅管理员发送的电子邮件中，AKS 团队发布了包含新 Kubernetes 版本的计划发布日期的预先公告。  除了公告外，AKS 还使用 [Azure 顾问](../advisor/advisor-overview.md) 在 Azure 门户中通知客户，在用户不受支持时向用户发出警报，并就会影响其应用程序或开发过程的已弃用的 API 提醒他们。

**我应该多久升级一次 Kubernetes 版本才能始终获得支持？**

从 Kubernetes 1.19 开始，[开源社区已将支持时间延长到 1 年](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)。 AKS 承诺启用修补程序并提供与上游承诺使用量匹配的支持。 对于版本 1.19 和更高版本的 AKS 群集，你将能够每年至少升级一次，这样就能始终使用受支持的版本。

对于 1.18 或更低版本，支持时间仍为 9 个月，这要求你每 9 个月升级一次才能始终使用受支持的版本。 定期测试新版本，并准备升级到更新的版本，以便在 Kubernetes 中获取最新的稳定增强功能。

**用户升级的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？**

如果你当前使用的是 n-3 或更低版本，则意味着你已不在支持范围，系统会要求你升级。 从版本 n-3 成功升级到 n-2 后，你将重新涵盖在我们的支持策略中。 例如：

- 如果支持的最低 AKS 版本为 1.15.a，而你使用的版本是 1.14.b 或更低，则你无法获得支持 。
- 从 1.14.b 成功升级到 1.15.a 或更高版本之后，你将重新受到我们支持策略的支持。 

不支持降级。

**“不在支持范围内”是什么意思**

“不受支持”意味着：
* 正在运行的版本不在受支持的版本列表中。
* 当你请求支持时，系统将要求你将群集升级到受支持的版本，除非你正处于版本弃用后的 30 天宽限期内。

此外，对于受支持版本列表以外的群集，AKS 不会在正常运行时间或其他方面做出任何保证。

**用户缩放的 Kubernetes 群集具有不受支持的次要版本时，会发生什么情况？**

对于 AKS 不支持的次要版本，缩减或扩展可继续进行。 由于没有服务质量保证，因此我们建议升级，使群集重新可接受支持。

**用户可以永久保留 Kubernetes 版本吗？**

如果群集有三 (3) 个以上的次要版本不受支持，并被发现存在安全风险，Azure 会主动联系你升级群集。 如果你不采取进一步的措施，Azure 将保留代表你自动升级群集的权利。

**如果节点池不在某个受支持的 AKS 版本中，控制平面支持哪个版本？**

控制平面必须位于所有节点池的版本窗口中。 有关升级控制平面或节点池的详细信息，请访问有关[升级节点池](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)的文档。

**在群集升级期间，是否可以跳过多个 AKS 版本？**

升级受支持的 AKS 群集时，不能跳过 Kubernetes 次要版本。 例如，对于以下升级过程：
  * 1\.12.x  ->  1.13.x：允许。
  * 1\.13.x  ->  1.14.x：允许。
  * 1\.12.x  ->  1.14.x：不允许。

若要实现 1.12.x  ->  1.14.x 升级，需要逐步执行以下升级：
1. 1\.12.x  ->  1.13.x。
1. 1\.13.x  ->  1.14.x。

仅当从不受支持的版本升级回受支持的版本时，才可以跳过多个版本。 例如，可以从不受支持的 1.10.x 升级到受支持的 1.15.x。

是否可以在 30 天的支持期内创建一个新的 1.xx.x 群集？

不是。 版本弃用/删除后，将无法使用该版本创建群集。 随着更改版的推出，你将开始看到旧版本从版本列表中删除。 从公告开始，此过程可能需要长达两周的时间，按区域逐步进行。

我使用的是刚弃用的版本，是否仍然可以添加新的节点池？或者必须升级？

不是。 系统不允许你将已弃用版本的节点池添加到群集中。 你可以添加新版本的节点池。 但是，这可能需要先更新控制平面。

## <a name="next-steps"></a>后续步骤

有关如何升级群集的信息，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az_aks_get_versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[install-azakskubectl]: /powershell/module/az.aks/install-azakskubectl
[get-azaksversion]: /powershell/module/az.aks/get-azaksversion
