---
title: Azure Service Fabric 托管激活和停用生命周期
description: 了解应用程序的生命周期和节点上的 ServicePackage。
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831816"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Azure Service Fabric 托管生命周期

本文概述了在节点上激活应用程序时 Azure Service Fabric 中发生的事件。 它介绍了控制行为的各种群集配置。

继续之前，请确保了解 Service Fabric 中的 [应用程序模型][a1]中所述的概念和关系。 

> [!NOTE]
> 本文以特殊方式使用一些术语。 除非另有说明：
>
> - “副本”指有状态服务的副本或无状态服务的实例。
> - *CodePackage* 被视为与注册 ServiceType 的 ServiceHost 进程等效。 它承载该 ServiceType 的服务副本。
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>激活 ApplicationPackage 或 ServicePackage

激活 ApplicationPackage 或 ServicePackage 的步骤：

1. 下载 ApplicationPackage (例如 *ApplicationManifest.xml*) 。
2. 为应用程序设置环境。 例如，你的步骤包括创建用户。
3. 开始跟踪应用程序以使其停用。
4. 下载 ServicePackage (例如，) *ServiceManifest.xml*、代码、配置文件和数据包。
5. 设置 ServicePackage 的环境。 例如，你的步骤包括设置防火墙并为终结点分配端口。
6. 开始跟踪 ServicePackage 以便停用。
7. 启动 Codepackage 的 SetupEntryPoint，并等待其完成。
8. 启动 Codepackage 的 MainEntryPoint。

### <a name="servicetype-blocklisting"></a>ServiceType 列入阻止列表
`ServiceTypeDisableFailureThreshold` 确定允许的激活、下载和 CodePackage 失败的次数。 达到阈值后，会将 ServiceType 计划用于列入阻止列表。 第一次激活失败、下载失败或 CodePackage 崩溃计划 ServiceType 列入阻止列表。 

此 `ServiceTypeDisableGraceInterval` 配置确定在节点上列入阻止列表时的宽限时间间隔。 在此过程开始时，将并行重试激活、下载和 CodePackage 重启。 例如，重试指的是，CodePackage 将在崩溃或 Service Fabric 以后再次尝试下载包时重新启动。

在列入阻止列表时，会看到运行状况错误： `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

如果发生以下任何事件，则会在节点上再次启用 ServiceType：
- 激活成功。 如果失败，则达到 `ActivationMaxFailureCount` 最大重试次数。
- 下载成功。 如果失败，则达到 `DeploymentMaxFailureCount` 最大重试次数。
- 已崩溃的 CodePackage 启动并成功注册 ServiceType。

`ActivationMaxFailureCount` 和 `DeploymentMaxFailureCount` 是 Service Fabric 将在节点上激活或下载应用程序的最大尝试次数。 达到最大值后，Service Fabric 会重新启用 ServiceType 以便再次激活。 

这些阈值为服务提供了另一种激活机会。 如果激活成功，则会自动修复该问题。 

新放置或激活的副本可能触发新的激活或下载操作。 此操作将成功或再次触发 ServiceType 列入阻止列表。

> [!NOTE]
> 不注册 ServiceType 的崩溃 CodePackage 不会影响 ServiceType。 承载副本的崩溃 CodePackage 将影响 ServiceType。
>

### <a name="codepackage-crash"></a>CodePackage 崩溃
当 CodePackage 崩溃时，Service Fabric 使用回退来重新启动它。 无论 CodePackage 是否已向 Service Fabric 运行时注册了类型，都将应用回退。

回退值为 `Min(RetryTime, ActivationMaxRetryInterval)` 。 根据配置设置，该值在常量、线性或指数量中递增 `ActivationRetryBackoffExponentiationBase` ：

- **常量**：如果 `ActivationRetryBackoffExponentiationBase == 0` ，则为 `RetryTime = ActivationRetryBackoffInterval` 。
- **线性**：如果  `ActivationRetryBackoffExponentiationBase == 0` 为，则 `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` 为，其中 `ContinuousFailureCount` 是 CodePackage 崩溃或无法激活的次数。
- **指数**： `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` 。
    
可以通过更改值来控制行为。 例如，如果需要多次快速重新启动尝试，可以通过将设置 `ActivationRetryBackoffExponentiationBase` 为 `0` 并将设置 `ActivationRetryBackoffInterval` 为来使用线性量 `10` 。 因此，如果 CodePackage 发生故障，开始间隔将在10秒后发生。 如果包继续崩溃，则回退将更改为20秒、30秒、40秒，依此类推，直到 CodePackage 激活成功，否则 CodePackage 将被停用。 
    
Service Fabric 关闭 (的最长时间，则在控制故障后将等待) `ActivationMaxRetryInterval` 。
    
如果 CodePackage 崩溃并重新启动，则需要保持指定的时间段 `CodePackageContinuousExitFailureResetInterval` 。 此时间间隔过后，Service Fabric 将 CodePackage 视为正常。 然后 Service Fabric 将之前的错误运行状况报告覆盖为 "正常"，并重置 `ContinuousFailureCount` 。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage 未注册 ServiceType
如果 CodePackage 保持正常运行，并且预计只注册一个 ServiceType，但却不是，Service Fabric 将在之后生成一个警告运行状况报告 `ServiceTypeRegistrationTimeout` 。 该报表指示 ServiceType 尚未在预期的时间内注册。

### <a name="activation-failure"></a>激活失败
当 Service Fabric 在激活过程中发现错误时，它始终使用线性回退，因为它会在 CodePackage 崩溃的情况下执行。 激活操作在重试后会按下列间隔重试： (0 + 10 + 20 + 30 + 40) = 100 秒。 第一次重试 (立即进行。在此序列后 ) ，不会重试激活。
    
最大激活回退可以是 `ActivationMaxRetryInterval` 。 重试可能是 `ActivationMaxFailureCount` 。

### <a name="download-failure"></a>下载失败
当在下载过程中发现错误时，Service Fabric 始终使用线性回退。 激活操作在重试后会按下列间隔重试： (0 + 10 + 20 + 30 + 40) = 100 秒。 第一次重试 (立即进行。在此序列后 ) ，不会重试下载。 

下载的线性回退与相同 `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` 。 最大回退可以是 `DeploymentMaxRetryInterval` 。 与激活一样，下载操作可以重试 `ActivationMaxFailureCount` 限制。

> [!NOTE]
> 更改这些设置之前，请记住以下示例：
>
>* 如果 CodePackage 保持崩溃并关闭，则将禁用 ServiceType。 但如果激活配置有快速重启，则 CodePackage 可能会在 ServiceType 实际列入阻止列表之前出现几次。 
>
>    例如，假设 CodePackage 出现，将 ServiceType 注册到 Service Fabric，然后崩溃。 在这种情况下，在宿主收到类型注册后，将 `ServiceTypeDisableGraceInterval` 取消该时间段。 此过程可以重复执行，直到 CodePackage 的值超过该 `ServiceTypeDisableGraceInterval` 时间段。 然后，将在节点上列入阻止列表 ServiceType。 ServiceType 列入阻止列表所需的时间可能比预期的要长。
>
>* 在激活的情况下，当 Service Fabric 系统需要在节点上放置一个副本时，重新配置代理会要求宿主子系统激活该应用程序。 它每15秒重试一次激活请求。  (持续时间由 `RAPMessageRetryInterval` 配置设置控制。 ) Service Fabric 无法知道是否已列入阻止列表 ServiceType，除非托管中的激活操作所用的时间比重试间隔和更长 `ServiceTypeDisableGraceInterval` 。 
>
>    例如，假设群集的 `ActivationMaxFailureCount` 设置为5，并且 `ActivationRetryBackoffInterval` 设置为1秒。 在这种情况下，激活操作将在 (0 + 1 + 2 + 3 + 4) 等于10秒的时间间隔后得出。 第一次重试 (立即进行。在此序列后 ) ，主机会放弃重试。 激活操作完成后将不会在15秒后重试。 
>
>    Service Fabric 已用尽了15秒内允许的所有重试。 因此，重新配置代理中的每次重试都会在宿主子系统中创建新的激活操作，并且模式将保持重复。 因此，ServiceType 永远不会列入阻止列表在节点上。 由于 ServiceType 不会在节点上列入阻止列表，因此不会在不同的节点上移动和尝试副本。
> 

## <a name="deactivation"></a>停用

在节点上激活 ServicePackage 时，会对其进行跟踪以使其停用。 停用有两种方式：

- **定期停用**：在每个 `DeactivationScanInterval` 系统中，系统都会检查 *从未* 托管过副本的服务包，并将其标记为候选项以便停用。
- **ReplicaClose 停用**：如果副本关闭，则 deactivator 将获取 `DecrementUsageCount` 。 当 ServicePackage 不托管任何副本时，计数为 0; 因此，ServicePackage 是取消激活的候选项。

激活模式确定何时计划取消激活。 在 "共享" 模式下，将在之后计划用于停用的候选项 `DeactivationGraceInterval` 。 在独占模式下，它们计划在之后 `ExclusiveModeDeactivationGraceInterval` 。 如果新的副本位置在这段时间内到达，则取消停用。 

有关详细信息，请参阅 [独占模式和共享模式][a2]。

### <a name="periodic-deactivation"></a>定期停用
下面是一些定期停用的示例：

* **示例 1**：假设 deactivator 开始扫描 (`DeactivationScanInterval`) 。 其下次扫描的时间将是 2T。 假设 ServicePackage 激活发生在 T + 1 中。 此 ServicePackage 不托管副本，因此需要将其停用。 

    若要成为停用的候选项，ServicePackage 至少需要为 T 次托管无副本。 它将有资格在 2T + 1 上停用。 因此，2T 上的扫描不会将此 ServicePackage 识别为取消激活的候选项。 

    下一次停用周期3T 会将此 ServicePackage 计划为停用，因为现在包已处于非副本状态的时间 T。  

* **示例 2**：假设 ServicePackage 在时间 t-1 激活，Deactivator 在 t 开始扫描。ServicePackage 不承载副本。 下一次扫描时，2T 会被标识为要停用的候选项，因此会将其计划为停用。  

* **示例 3**：假设 ServicePackage 在 t –1处激活，Deactivator 在 t 开始扫描。ServicePackage 尚未托管副本。 现在，在 T + 1 上放置副本。 也就是说，托管获取 `IncrementUsageCount` ，这意味着将创建副本。 

    在2T 上，不会将此 ServicePackage 计划为停用。 由于包包含副本，因此停用将遵循 ReplicaClose 逻辑，如本文的下一部分所述。

* **示例 4**：假设 ServicePackage 是 10 GB。 由于包很大，因此需要在节点上下载时间。 激活应用程序时，deactivator 会跟踪其生命周期。 如果 `DeactivationScanInterval` 设置为较小的值，则由于下载所需的时间，ServicePackage 可能没有时间在节点上激活。 若要解决此问题，可以 [提前在节点上下载 ServicePackage][p1] 或增加 `DeactivationScanInterval` 。 

> [!NOTE]
> ServicePackage 可以在 (`DeactivationScanInterval` 到 2 *) + 之间的任何位置停用 `DeactivationScanInterval` `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 。 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose 停用

> [!NOTE]
> 本部分引用以下配置设置：
> - **DeactivationGraceInterval** /**ExclusiveModeDeactivationGraceInterval**：指定给 ServicePackage 的时间，以承载另一个副本（如果它已承载任何副本）。 
> - **DeactivationScanInterval**：为 ServicePackage 托管副本的最短时间（如果它 *从未* 承载任何副本，即，如果尚未使用）。
>

系统会保留 ServicePackage 包含的副本计数。 如果 ServicePackage 持有副本并且副本关闭或关闭，则宿主会获得 `DecrementUsageCount` 。 打开副本时，承载将获取 `IncrementUsageCount` 。 

减量表示 ServicePackage 托管的副本的数量已减少一个副本。 当计数降到0时，ServicePackage 计划为停用。 停用之后的时间为 `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 。 

例如，假设某个减量发生在 T，并计划在 2T + X () 停用 `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 。 在此期间，如果 `IncrementUsage` 由于创建了副本而导致宿主获得，则取消停用。

### <a name="ctrl--c"></a>Ctrl + C
如果 ServicePackage 传递了 `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 并且仍未承载副本，则无法取消停用。 Codepackage 接收 Ctrl + C 处理程序。 现在，停用管道必须完成以使进程停止运行。 

在此期间，如果相同 ServicePackage 的新副本正在尝试执行，则该副本将失败，因为进程无法从停用切换到激活。

## <a name="cluster-configurations"></a>群集配置

本节列出了默认情况下会影响激活和停用的配置。

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**：默认值：1。 失败计数的阈值;达到此阈值后，将通知 FailoverManager 禁用节点上的服务类型，并尝试使用其他节点进行放置。
- **ServiceTypeDisableGraceInterval**：默认值：30秒。 时间间隔，在此时间间隔后可以禁用服务类型。
- **ServiceTypeRegistrationTimeout**：默认值：300秒。 ServiceType 注册 Service Fabric 所用的超时时间。

### <a name="activation"></a>激活
- **ActivationRetryBackoffInterval**：默认值：10秒。 每个激活失败的回退时间间隔。
- **Activationmaxfailurecount 次**：默认值：20。 系统将在放弃之前重试失败的激活的最大计数。 
- **ActivationRetryBackoffExponentiationBase**：默认值：1.5。
- **ActivationMaxRetryInterval**：默认值：3600秒。 故障后激活的最大回退重试间隔。
- **CodePackageContinuousExitFailureResetInterval**：默认值：300秒。 用于重置 CodePackage 的连续退出失败计数的超时间隔。

### <a name="download"></a>下载
- **DeploymentRetryBackoffInterval**：默认值：10。 部署失败的回退时间间隔。
- **DeploymentMaxRetryInterval**：默认值：3600秒。 部署失败后的最大回退时间间隔。
- **DeploymentMaxFailureCount**：默认值：20。 在 `DeploymentMaxFailureCount` 节点上部署应用程序之前，将重试应用程序部署。

### <a name="deactivation"></a>停用
- **DeactivationScanInterval**：默认值：600秒。 指定给 ServicePackage 宿主副本的最小时间（如果它从未承载过任何副本 (，如果未使用) ）。
- **DeactivationGraceInterval**：默认值：60秒。 在 *共享* 进程模型中，指定给 ServicePackage 的时间在已托管任何副本后再次托管另一个副本。
- **ExclusiveModeDeactivationGraceInterval**：默认值：1秒。 在 *独占* 进程模型中，指定给 ServicePackage 的时间会在已承载任何副本后再次托管另一个副本。

## <a name="next-steps"></a>后续步骤

- [打包应用程序][a3]并准备好进行部署。
- 在 PowerShell 中[部署和删除应用程序][a4]。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
