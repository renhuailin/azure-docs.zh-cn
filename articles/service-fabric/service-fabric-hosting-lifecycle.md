---
title: Azure Service Fabric 宿主激活和停用生命周期
description: 了解节点上的应用程序和 ServicePackage 的生命周期。
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831816"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Azure Service Fabric 宿主生命周期

本文概述了在节点上激活应用程序时 Azure Service Fabric 中发生的事件， 并介绍了控制行为的各种群集配置。

在继续阅读之前，请确保了解[在 Service Fabric 中为应用程序建模][a1]中所述的概念及其相互关系。 

> [!NOTE]
> 本文使用了一些专用术语。 除非另有说明：
>
> - “副本”指有状态服务的副本或无状态服务的实例。
> - CodePackage 被视为与注册 ServiceType 的 ServiceHost 进程相同。 它承载该 ServiceType 的服务副本。
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>激活 ApplicationPackage 或 ServicePackage

激活 ApplicationPackage 或 ServicePackage：

1. 下载 ApplicationPackage（例如 ApplicationManifest.xml）。
2. 设置应用程序的环境。 例如，创建用户。
3. 开始跟踪应用程序的停用情况。
4. 下载 ServicePackage（例如 ServiceManifest.xml、代码、配置文件和数据包）。
5. 设置 ServicePackage 的环境。 例如，设置防火墙并为终结点分配端口。
6. 开始跟踪 ServicePackage 的停用情况。
7. 启动 CodePackage 的 SetupEntryPoint，并等待其完成。
8. 启动 CodePackage 的 MainEntryPoint。

### <a name="servicetype-blocklisting"></a>将 ServiceType 加入阻止列表
`ServiceTypeDisableFailureThreshold` 确定允许的激活、下载和 CodePackage 失败的次数。 达到阈值后，系统将安排 ServiceType 列入阻止列表。 第一次激活失败、下载失败或 CodePackage 崩溃会安排将 ServiceType 加入阻止列表。 

`ServiceTypeDisableGraceInterval` 配置确定在节点上将 ServiceType 列入阻止列表之前的宽限间隔。 在此过程中，将并行重试激活、下载和 CodePackage 重启。 例如，重试意味着会安排 CodePackage 在崩溃后再次启动，或者 Service Fabric 将再次尝试下载包。

在 ServiceType 列入阻止列表时，你会看到运行状况错误：`'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

如果发生以下任何事件，则会在节点上再次启用 ServiceType：
- 激活成功。 如果失败，会达到 `ActivationMaxFailureCount` 最大重试次数。
- 下载成功。 如果失败，会达到 `DeploymentMaxFailureCount` 最大重试次数。
- 已崩溃的 CodePackage 会启动并成功注册 ServiceType。

`ActivationMaxFailureCount` 和 `DeploymentMaxFailureCount` 是 Service Fabric 在节点上激活或下载应用程序的最大尝试次数。 达到最大值后，Service Fabric 会重新启用 ServiceType 以进行激活。 

这些阈值为服务提供了另一种激活机会。 如果激活成功，则问题会自动修复。 

新放置或激活的副本可能会触发新的激活或下载操作。 此操作不是成功就是再次使 ServiceType 列入阻止列表。

> [!NOTE]
> 发生崩溃的 CodePackage 只要不注册 ServiceType 就不会影响 ServiceType。 它只有在承载副本时才会影响 ServiceType。
>

### <a name="codepackage-crash"></a>CodePackage 崩溃
当 CodePackage 崩溃时，Service Fabric 会使用退避来再次启动它。 无论 CodePackage 是否已向 Service Fabric 运行时注册类型，都将应用退避。

退避值为 `Min(RetryTime, ActivationMaxRetryInterval)`。 该值根据 `ActivationRetryBackoffExponentiationBase` 配置设置在常数、线性或指数量中递增：

- **常数**：如果 `ActivationRetryBackoffExponentiationBase == 0`，则 `RetryTime = ActivationRetryBackoffInterval`。
- **线性**：如果 `ActivationRetryBackoffExponentiationBase == 0`，则 `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval`，其中 `ContinuousFailureCount` 是 CodePackage 崩溃或激活失败的次数。
- **指数**：`RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)`。
    
可通过更改值来控制行为。 例如，如果需要多次快速重启尝试，可通过将 `ActivationRetryBackoffExponentiationBase` 设置为 `0` 并将 `ActivationRetryBackoffInterval` 设置为 `10` 来使用线性值。 因此，如果 CodePackage 崩溃，启动间隔将在 10 秒后。 如果包继续崩溃，则退避将更改为 20 秒、30 秒、40 秒，依此类推，直到 CodePackage 成功激活或 CodePackage 被停用。 
    
出现故障后 Service Fabric 退避（即等待）的最长时间取决于 `ActivationMaxRetryInterval`。
    
如果 CodePackage 崩溃并重新启动，则需要在 `CodePackageContinuousExitFailureResetInterval` 指定的时间段内保持运行。 此间隔过后，Service Fabric 会将 CodePackage 视为正常。 然后 Service Fabric 将之前的错误运行状况报告覆盖为“正常”，并重置 `ContinuousFailureCount`。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage 未注册 ServiceType
如果 CodePackage 保持正常运行，并且预计会注册一个 ServiceType 但却未这样做，那么 Service Fabric 会在 `ServiceTypeRegistrationTimeout` 后生成一个警告运行状况报告。 报告指示 ServiceType 未在预期的时间内注册。

### <a name="activation-failure"></a>激活失败
如果 Service Fabric 在激活过程中发现错误，它始终会使用线性退避，此行为与处理 CodePackage 崩溃的行为相同。 按以下间隔重试后，将放弃激活操作：(0 + 10 + 20 + 30 + 40) = 100 秒。 （第一次重试是即时进行的。）在此序列后，不会重试激活。
    
最大激活退避可以为 `ActivationMaxRetryInterval`。 重试可以为 `ActivationMaxFailureCount`。

### <a name="download-failure"></a>下载失败
如果在下载过程中发现错误，Service Fabric 始终会使用线性退避。 按以下间隔重试后，将放弃激活操作：(0 + 10 + 20 + 30 + 40) = 100 秒。 （第一次重试是即时进行的。）在此序列后，不会重试下载。 

下载的线性退避等于 `ContinuousFailureCount` * `DeploymentRetryBackoffInterval`。 最大退避可以为 `DeploymentMaxRetryInterval`。 与激活一样，下载操作的重试限制可为 `ActivationMaxFailureCount`。

> [!NOTE]
> 更改这些设置之前，请记住以下示例：
>
>* 如果 CodePackage 不断地崩溃并退出，则会禁用 ServiceType。 但如果激活配置了快速重启，则 CodePackage 可在 ServiceType 实际列入阻止列表之前启动几次。 
>
>    例如，假定 CodePackage 启动后将 ServiceType 注册到 Service Fabric，然后崩溃。 在这种情况下，在宿主收到类型注册后，会取消 `ServiceTypeDisableGraceInterval` 时间段。 此过程可以重复执行，直到 CodePackage 退避到的值大于 `ServiceTypeDisableGraceInterval` 时间段。 然后，ServiceType 将在节点上被列入阻止列表。 将 ServiceType 列入阻止列表所需的时间可能比预期的要长。
>
>* 在激活的情况下，如果 Service Fabric 系统需要在节点上放置一个副本，重新配置代理会要求宿主子系统激活应用程序， 并每隔 15 秒重试激活请求。 （持续时间取决于 `RAPMessageRetryInterval` 配置设置。）Service Fabric 无法知道 ServiceType 是否已列入阻止列表，除非宿主中激活操作的持续时间长于重试间隔和 `ServiceTypeDisableGraceInterval`。 
>
>    例如，假设群集的 `ActivationMaxFailureCount` 设置为 5，`ActivationRetryBackoffInterval` 设置为 1 秒。 在这种情况下，激活操作将在 (0 + 1 + 2 + 3 + 4) = 10 秒的间隔后放弃。 （第一次重试是即时进行的。）在此序列之后，宿主会放弃重试。 激活操作完成，将不会在 15 秒后重试。 
>
>    Service Fabric 已在 15 秒内用尽了允许的所有重试。 因此，重新配置代理中的每次重试都会在宿主子系统中创建新的激活操作，并且此模式会不断重复。 这样一来，ServiceType 在节点上永远不会被列入阻止列表。 由于 ServiceType 在节点上不会被列入阻止列表，因此不会移动副本或在另一个节点上尝试。
> 

## <a name="deactivation"></a>停用

在节点上激活 ServicePackage 时，会跟踪其停用情况。 停用的工作方式有两种：

- **定期停用**：每隔 `DeactivationScanInterval` 秒，系统就会检查是否有从未承载过副本的服务包，并将其标记为要停用的候选项。
- **ReplicaClose 停用**：如果副本已关闭，Deactivator 会获取 `DecrementUsageCount`。 当 ServicePackage 不承载任何副本时，计数为 0；因此 ServicePackage 是停用的候选项。

激活模式确定安排何时停用候选项。 在共享模式下，安排在 `DeactivationGraceInterval` 后停用候选项。 在独占模式下，安排在 `ExclusiveModeDeactivationGraceInterval` 后停用它们。 如果在这段时间内进行了新的副本放置操作，则会取消停用。 

有关详细信息，请参阅[独占模式和共享模式][a2]。

### <a name="periodic-deactivation"></a>定期停用
下面是定期停用的一些示例：

* **示例 1**：假设 Deactivator 开始扫描的时间为 T (`DeactivationScanInterval`)。 其下次扫描的时间将是 2T。 假设 ServicePackage 的激活时间为 T + 1。 此 ServicePackage 未承载副本，因此需要将其停用。 

    ServicePackage 处于“无副本”状态的时间至少需要为 T 才能成为停用的候选项。 它会在 2T + 1 秒的时候符合停用条件。 因此，在 2T 的时候，扫描时看不到此 ServicePackage 作为停用候选项出现。 

    下一个停用周期 3T 会安排停用此 ServicePackage，因为此时该包处于“无副本”状态的时间已为 T。  

* **示例 2**：假设 ServicePackage 在 T - 1 的时候激活，Deactivator 在 T 的时候开始扫描。ServicePackage 未承载副本。 在 2T 的时候进行下一次扫描时，ServicePackage 会显示为停用候选项，因此系统会安排停用它。  

* **示例 3**：假设 ServicePackage 在 T - 1 的时候激活，Deactivator 在 T 的时候开始扫描。ServicePackage 尚未承载副本。 现在会在 T + 1 的时候放置副本。 也就是说，宿主会获得一个 `IncrementUsageCount`，这意味着已创建副本。 

    在 2T 的时候，系统不会安排停用此 ServicePackage。 由于该包包含副本，因此停用将遵循 ReplicaClose 逻辑，如本文的下一部分所述。

* **示例 4**：假设你的 ServicePackage 是 10 GB。 由于包很大，因此在节点上下载需要时间。 激活应用程序后，Deactivator 会跟踪其生命周期。 如果 `DeactivationScanInterval` 设置为较小的值，ServicePackage 可能没有时间在节点上激活，因为下载需要时间。 若要解决此问题，可[在节点上提前下载 ServicePackage][p1] 或增加 `DeactivationScanInterval`。 

> [!NOTE]
> ServicePackage 可以在（`DeactivationScanInterval` 到 2 * `DeactivationScanInterval`） + `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval` 之间的任何时候停用。 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose 停用

> [!NOTE]
> 此部分引用以下配置设置：
> - **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval**：为 ServicePackage 留出的时间，如果它已承载任意副本，则可在此时间内承载另一个副本。 
> - **DeactivationScanInterval**：为 ServicePackage 留出的最小时间，如果它从未承载任何副本（即从未使用过），则可在此时间内承载一个副本。
>

系统会保留 ServicePackage 保存的副本数。 如果 ServicePackage 保存了一个副本，而该副本处于关闭或故障状态，则宿主会获得一个 `DecrementUsageCount`。 打开副本时，宿主会获得一个 `IncrementUsageCount`。 

减量表示 ServicePackage 承载的副本数已减少一个副本。 当计数降到 0 时，ServicePackage 将被安排停用。 停用的时间将为 `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval`。 

例如，假设在 T 发生减量，并安排在 2T + X (`DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval`) 停用。 在这段时间内，如果宿主因为创建了一个副本而获得了一个 `IncrementUsage`，则会取消停用。

### <a name="ctrl--c"></a>Ctrl + C
如果 ServicePackage 传递 `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval` 并且仍未承载副本，则无法取消停用。 Codepackage 接收 Ctrl + C 处理程序。 因此现在必须完成停用管道才能使进程停止运行。 

在该时间内，如果尝试放置同一 ServicePackage 的新副本，则操作会失败，因为进程无法从停用转变为激活。

## <a name="cluster-configurations"></a>群集配置

本节列出了会影响激活和停用的配置默认值。

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**：默认值：1. 失败计数的阈值，到达此值后，就会通知 FailoverManager 禁用该节点上的服务类型并尝试在另一个节点上放置对象。
- **ServiceTypeDisableGraceInterval**：默认值：30 秒。 时间间隔，超过该时间间隔后禁用服务类型。
- **ServiceTypeRegistrationTimeout**：默认值：300 秒。 ServiceType 向 Service Fabric 注册的超时。

### <a name="activation"></a>激活
- **ActivationRetryBackoffInterval**：默认值：10 秒。 每次激活失败的退避间隔。
- **ActivationMaxFailureCount**：默认值：20。 系统在放弃之前重试失败激活的最大次数。 
- **ActivationRetryBackoffExponentiationBase**：默认值：1.5。
- **ActivationMaxRetryInterval**：默认值：3,600 秒。 失败后激活的最大退避重试间隔。
- **CodePackageContinuousExitFailureResetInterval**：默认值：300 秒。 为 CodePackage 重置连续退出失败计数的超时间隔。

### <a name="download"></a>下载
- **DeploymentRetryBackoffInterval**：默认值：10。 部署失败的退避间隔。
- **DeploymentMaxRetryInterval**：默认值：3,600 秒。 部署失败后的最大退避间隔。
- **DeploymentMaxFailureCount**：默认值：20。 重试 `DeploymentMaxFailureCount` 次应用程序部署后，节点上该应用程序的部署才会失败。

### <a name="deactivation"></a>停用
- **DeactivationScanInterval**：默认值：600 秒。 为 ServicePackage 留出的最小时间，如果它从未承载任何副本（即从未使用过），则可在此时间内承载一个副本。
- **DeactivationGraceInterval**：默认值：60 秒。 在共享进程模型中为 ServicePackage 留出的时间，在此时间内，允许其在承载任意副本后再重新承载另一个副本。
- **ExclusiveModeDeactivationGraceInterval**：默认值：1 秒。 在独占进程模型中为 ServicePackage 留出的时间，在此时间内，允许其在承载任意副本后再重新承载另一个副本。

## <a name="next-steps"></a>后续步骤

- [打包应用程序][a3]并准备好进行部署。
- 使用 PowerShell [部署和删除应用程序][a4]。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
