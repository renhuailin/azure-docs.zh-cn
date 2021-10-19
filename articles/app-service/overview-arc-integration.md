---
title: Azure Arc 上的应用服务
description: 为 Azure 操作员介绍如何将应用服务与 Azure Arc 集成。
ms.topic: article
ms.date: 08/17/2021
ms.openlocfilehash: cec1e7bb9dac43e33e85b6036910220a1fa287c2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711659"
---
# <a name="app-service-functions-and-logic-apps-on-azure-arc-preview"></a>Azure Arc 上的应用服务、Functions 和逻辑应用（预览）

可在已启用 Azure Arc 的 Kubernetes 群集上运行应用服务、Functions 和逻辑应用。 Kubernetes 群集可以位于本地，也可以托管在第三方云中。 此方法使应用开发人员能够利用应用服务的功能。 同时，它使 IT 管理员能够通过将应用服务应用托管在内部基础结构上来保持公司的合规性。 它还使其他 IT 操作员能够通过在现有 Kubernetes 群集上运行应用服务来保护其先前在其他云提供商中的投资。

> [!NOTE]
> 若要了解如何为应用服务、Functions 和逻辑应用设置 Kubernetes 群集，请参阅[创建应用服务 Kubernetes 环境（预览）](manage-create-arc-environment.md)。

在大多数情况下，应用开发人员只需了解如何部署到正确的 Azure 区域，这些区域代表了已部署的 Kubernetes 环境。 对于提供环境和维护基础 Kubernetes 基础结构的操作员，需要注意以下 Azure 资源：

- 连接的群集，该群集是 Kubernetes 基础结构的 Azure 投影。 有关详细信息，请参阅[什么是已启用 Azure Arc 的 Kubernetes？](../azure-arc/kubernetes/overview.md)。
- 群集扩展，该扩展是连接的群集资源的子资源。 应用服务扩展会[将所需的 Pod 安装到连接的群集中](#pods-created-by-the-app-service-extension)。 有关群集扩展的详细信息，请参阅[已启用 Azure Arc 的 Kubernetes 上的群集扩展](../azure-arc/kubernetes/conceptual-extensions.md)。
- 自定义位置，它将一组扩展捆绑在一起，并将器映射到已创建资源的命名空间。 有关详细信息，请参阅[已启用 Azure Arc 的 Kubernetes 上的自定义位置](../azure-arc/kubernetes/conceptual-custom-locations.md)。
- 应用服务 Kubernetes 环境，该环境使配置跨应用通用但与群集操作无关。 从概念上讲，该环境部署到自定义位置资源中，应用开发人员会在其中创建应用。 [应用服务 Kubernetes 环境](#app-service-kubernetes-environment)中对此进行了更详细的介绍。

## <a name="public-preview-limitations"></a>公共预览版限制

应用服务 Kubernetes 环境适用以下公共预览版限制。 当更改可用时，它们就会更新。

| 限制                                              | 详细信息                                                                               |
|---------------------------------------------------------|---------------------------------------------------------------------------------------|
| 支持的 Azure 区域                                 | 美国东部、西欧                                                                  |
| 群集网络要求                          | 必须支持 `LoadBalancer` 服务类型并提供可公开寻址的静态 IP |
| 功能：网络                                     | [不可用（取决于群集网络）](#are-networking-features-supported)      |
| 功能：托管标识                             | [不可用](#are-managed-identities-supported)                                    |
| 功能：密钥保管库引用                           | 不可用（取决于托管标识）                                         |
| 功能：使用托管标识从 ACR 拉取映像     | 不可用（取决于托管标识）                                         |
| 功能：Functions 和逻辑应用的门户内编辑 | 不可用                                                                         |
| 功能：FTP 发布                                 | 不可用                                                                         |
| 日志                                                    | Log Analytics 必须配置群集扩展；不是按站点                 |

## <a name="pods-created-by-the-app-service-extension"></a>应用服务扩展创建的 Pod

在已启用 Azure Arc 的 Kubernetes 群集上安装应用服务扩展后，可以看到在指定的发布命名空间中创建了几个 Pod。 这些 Pod 使 Kubernetes 群集能充当 Azure 中 `Microsoft.Web` 资源提供程序的扩展，并支持应用的管理和操作。 （可选）可以选择为扩展安装 [KEDA](https://keda.sh/) 以用于事件驱动的缩放。
 <!-- You can only have one installation of KEDA on the cluster. If you have one already, you must disable this behavior during installation of the cluster extension `TODO`. -->

下表描述了默认情况下创建的每个 Pod 的角色：

| Pod                                   | 说明                                                                                                                       |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| `<extensionName>-k8se-app-controller` | 在群集上创建资源并维护组件状态的核心运算符 Pod。                                |
| `<extensionName>-k8se-envoy`          | 用于所有数据平面请求的前端代理层。 它将入站流量路由到正确的应用。                           |
| `<extensionName>-k8se-activator`      | 备用路由目标，用于帮助在系统获得第一个可用实例时缩放至零的应用。 |
| `<extensionName>-k8se-build-service`  | 支持部署操作并提供[高级工具功能](resources-kudu.md)。                                        |
| `<extensionName>-k8se-http-scaler`    | 监视入站请求量以便向 [KEDA](https://keda.sh) 提供缩放信息。                               |
| `<extensionName>-k8se-img-cacher`     | 将占位符和应用图像提取到节点上的本地缓存中。                                                                  |
| `<extensionName>-k8se-log-processor`  | 从应用和其他组件收集日志并将其发送到 Log Analytics。                                                      |
| `placeholder-azure-functions-*`       | 用于加速 Azure Functions 的冷启动。                                                                                 |

## <a name="app-service-kubernetes-environment"></a>应用服务 Kubernetes 环境

创建应用需要应用服务 Kubernetes 环境资源。 它使配置能够通用于自定义位置中的应用，例如默认的 DNS 后缀。

一个自定义位置中只能创建一个 Kubernetes 环境资源。 在大多数情况下，创建和部署应用的开发人员不需要直接了解资源。 可以通过提供的自定义位置 ID 直接进行推断。 但是，在定义 Azure 资源管理器模板时，任何计划资源都需要直接引用环境的资源 ID。 计划的自定义位置值必须与指定的环境相匹配。

## <a name="faq-for-app-service-functions-and-logic-apps-on-azure-arc-preview"></a>有关 Azure Arc 上的应用服务、Functions 和逻辑应用（预览）的常见问题解答

- [它的费用是多少？](#how-much-does-it-cost)
- [是否同时支持 Windows 和 Linux 应用？](#are-both-windows-and-linux-apps-supported)
- [支持哪些内置应用程序堆栈？](#which-built-in-application-stacks-are-supported)
- [是否支持所有应用部署类型？](#are-all-app-deployment-types-supported)
- [支持哪些应用服务功能？](#which-app-service-features-are-supported)
- [是否支持网络功能？](#are-networking-features-supported)
- [是否支持托管标识？](#are-managed-identities-supported)
- [是否存在任何缩放限制？](#are-there-any-scaling-limits)
- [收集哪些日志？](#what-logs-are-collected)
- [如果看到提供程序注册错误，该怎么办？](#what-do-i-do-if-i-see-a-provider-registration-error)
- [能否在基于 ARM64 的群集上部署应用程序服务扩展？](#can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster)

### <a name="how-much-does-it-cost"></a>费用是多少？

在公共预览版期间，Azure Arc 上的应用服务免费。

### <a name="are-both-windows-and-linux-apps-supported"></a>是否同时支持 Windows 和 Linux 应用？

代码和自定义容器都仅支持基于 Linux 的应用。 不支持 Windows 应用。

### <a name="which-built-in-application-stacks-are-supported"></a>支持哪些内置应用程序堆栈？

支持所有内置 Linux 堆栈。

### <a name="are-all-app-deployment-types-supported"></a>是否支持所有应用部署类型？

不支持 FTP 部署。 当前也不支持 `az webapp up`。 支持其他部署方法，包括 Git、ZIP、CI/CD、Visual Studio 和 Visual Studio Code。

### <a name="which-app-service-features-are-supported"></a>支持哪些应用服务功能？

在预览版期间，将验证某些应用服务功能。 如果支持这些选项，将激活 Azure 门户中其左侧的导航选项。 尚不支持的功能保持灰显。

### <a name="are-networking-features-supported"></a>是否支持网络功能？

不是。 不支持混合连接、虚拟网络集成或 IP 限制等网络功能。 应在 Kubernetes 群集本身的网络规则中直接处理网络。

### <a name="are-managed-identities-supported"></a>是否支持托管标识？

不是。 应用在 Azure Arc 中运行时，无法为其分配托管标识。如果应用需要一个标识以便能使用另一个 Azure 资源，请考虑改用[应用程序服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。

### <a name="are-there-any-scaling-limits"></a>是否存在任何缩放限制？

使用 Azure 应用服务在具有 Azure Arc 的 Kubernetes 上部署的所有应用程序都可以在基础 Kubernetes 群集的限制范围内进行缩放。  如果基础 Kubernetes 群集用尽了可用计算资源（主要是 CPU 和内存），则应用程序将只能缩放到 Kubernetes 可使用可用资源进行计划的应用程序实例数。

### <a name="what-logs-are-collected"></a>收集哪些日志？

系统组件和应用程序的日志均写入标准输出。 可以使用标准 Kubernetes 工具收集这两类日志以进行分析。 还可以使用 [Log Analytics 工作区](../azure-monitor/logs/log-analytics-overview.md)配置应用服务群集扩展，它会将所有日志发送到该工作区。

默认情况下，来自系统组件的日志将发送到 Azure 团队。 不会发送应用程序日志。 可以通过将 `logProcessor.enabled=false` 设置为扩展配置设置以防止传输这些日志。 此操作还会禁止将应用程序转发到 Log Analytics 工作区。 禁用日志处理器可能会影响任何支持案例所需的时间，并且系统会要求你通过一些其他方式从标准输出中收集日志。

### <a name="what-do-i-do-if-i-see-a-provider-registration-error"></a>如果看到提供程序注册错误，该怎么办？

创建 Kubernetes 环境资源时，一些订阅可能会看到“找不到已注册的资源提供程序”错误。 错误详细信息可能包括一组被视为有效的位置和 API 版本。 如果发生这种情况，可能是需要向 Microsoft.Web 提供程序重新注册订阅，此操作对现有应用程序或 API 没有影响。 若要重新注册，请使用 Azure CLI 运行 `az provider register --namespace Microsoft.Web --wait`。 然后重新尝试运行 Kubernetes 环境命令。

### <a name="can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster"></a>能否在基于 ARM64 的群集上部署应用程序服务扩展？

目前不支持基于 ARM64 的群集。  

## <a name="next-steps"></a>后续步骤

[创建应用服务 Kubernetes 环境（预览）](manage-create-arc-environment.md)
