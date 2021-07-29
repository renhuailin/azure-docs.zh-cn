---
title: 容器和服务的资源调控
description: Azure Service Fabric 允许你为作为进程或容器运行的服务指定资源请求和限制。
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 2265640346525c6521d7f421c2e589979cceb4ca
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783400"
---
# <a name="resource-governance"></a>资源调控

在同一节点或群集上运行多个服务时，其中一个服务可能会占用较多资源，导致相应流程中的其他服务缺少资源。 这种问题称为“邻近干扰”问题。 借助 Azure Service Fabric，开发者可以为每个服务指定请求和限制来限制资源使用情况，从而控制此行为。

> 继续阅读本文之前，建议先熟悉 [Service Fabric 应用程序模型][application-model-link]和 [Service Fabric 托管模型][hosting-model-link]。
>

## <a name="resource-governance-metrics"></a>资源调控指标

根据[服务包][application-model-link]，Service Fabric 支持资源治理。 可以在代码包之间进一步划分分配到服务包的资源。 Service Fabric 通过两个内置[指标](service-fabric-cluster-resource-manager-metrics.md)为每个服务包的 CPU 和内存治理提供支持：

* *CPU*（指标名称 `servicefabric:/_CpuCores`）：主机上可用的逻辑核心。 所有节点上的全部内核都进行了相同的加权。

* *内存*（指标名称 `servicefabric:/_MemoryInMB`）：内存以 MB 表示，并映射到计算机上可用的物理内存。

对于这两个指标，[群集资源管理器 (CRM)][cluster-resource-manager-description-link] 会跟踪总群集容量、群集中每个节点上的负载以及群集中剩余的资源。 这两个指标等同于其他任何用户指标或自定义指标。 现有全部功能都可以与它们结合使用：

* 群集可根据这两个指标进行[均衡](service-fabric-cluster-resource-manager-balancing.md)（默认行为）。
* 群集可根据这两个指标进行[碎片整理](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* [描述群集][cluster-resource-manager-description-link]时，可为这两个指标设置缓冲容量。

> [!NOTE]
> 这些指标不支持[动态负载报告](service-fabric-cluster-resource-manager-metrics.md)；在创建时即定义了这些指标的负载。

## <a name="resource-governance-mechanism"></a>资源治理机制

从版本 7.2 开始，Service Fabric 运行时支持为 CPU 和内存资源指定请求和限制。

> [!NOTE]
> 低于 7.2 的 Service Fabric 运行时版本仅支持单个值同时充当特定资源（CPU 或内存）的 **请求** 和 **限制** 的模型。 本文档中将此描述为 **RequestsOnly** 规范。

* 请求：CPU 和内存请求值表示[群集资源管理器 (CRM)][cluster-resource-manager-description-link] 用于 `servicefabric:/_CpuCores` 和 `servicefabric:/_MemoryInMB` 指标的负载。 换句话说，CRM 会将服务的资源消耗视为与其请求值相等，并在做出放置决策时使用这些值。

* 限制：CPU 和内存限制值表示在节点上激活进程或容器时应用的实际资源限制。

Service Fabric 允许对 CPU 和内存使用 **RequestsOnly、LimitsOnly** 和 **RequestsAndLimits** 规范。
* 使用 RequestsOnly 规范时，Service Fabric 还会将请求值用作限制。
* 使用 LimitsOnly 规范时，Service Fabric 会将请求值视为 0。
* 使用 RequestsAndLimits 规范时，限制值必须大于或等于请求值。

为了更好地了解资源治理机制，让我们看一个示例放置方案，其中的 **RequestsOnly** 规范用于 CPU 资源（用于内存治理的机制是等效的）。 假设某个节点有两个 CPU 核心，将在其上放置两个服务包。 要放置的第一个服务包仅包含一个容器代码包，并且仅指定一个 CPU 核心的请求。 要放置的第二个服务包仅包含一个基于进程的代码包，并且也仅指定一个 CPU 核心的请求。 由于这两个服务包都具有 RequestsOnly 规范，因此其限制值将设置为其请求值。

1. 首先，请求一个 CPU 核心的基于容器的服务包被放置在节点上。 运行时会激活此容器，并将 CPU 限制设置为一个核心。 此容器无法使用多个内核。

2. 接下来，请求一个 CPU 核心的基于进程的服务包被放置在节点上。 运行时会激活服务进程，并将其 CPU 限制设置为一个核心。

此时，请求之和等于节点的容量。 CRM 不会在此节点上放置具有 CPU 请求的任何其他容器或服务进程。 在节点上，进程和容器各自使用一个核心运行，互不争用 CPU。

现在，让我们重新看一下具有 **RequestsAndLimits** 规范的示例。 这次，基于容器的服务包指定的请求为一个 CPU 核心，指定的限制为两个 CPU 核心。 基于进程的服务包同时将请求和限制指定为一个 CPU 核心。
  1. 首先，基于容器的服务包被放置在节点上。 运行时会激活此容器，并将 CPU 限制设置为两个核心。 此容器无法使用两个以上的核心。
  2. 接下来，基于进程的服务包被放置在节点上。 运行时会激活服务进程，并将其 CPU 限制设置为一个核心。

  此时，放置在节点上的服务包的 CPU 请求总数等于节点的 CPU 容量。 CRM 不会在此节点上放置具有 CPU 请求的任何其他容器或服务进程。 但是，在节点上，限制总和（容器的两个核心 + 进程的一个核心）超出了两个核心这一容量。 如果容器和进程同时突发，则可能会争用 CPU 资源。 此类争用将由平台的基础操作系统进行管理。 在此示例中，容器可能会突然增加至两个 CPU 核心，导致进程的一个 CPU 核心这一请求得不到保证。

> [!NOTE]
> 如前面的示例所示，对 CPU 和内存的请求值不会导致在节点上保留资源。 这些值表示群集资源管理器在做出放置决策时考虑的资源消耗。 限制值表示在节点上激活进程或容器时应用的实际资源限制。


在某些情况下，可能存在 CPU 争用现象。 在此类情况下，示例中的进程和容器可能会遇到邻近干扰问题：

* *混用调控和非调控服务与容器*：如果用户创建服务时没有指定任何资源治理，运行时将它视为不占用任何资源，能够将它放置在示例中的节点上。 在这种情况下，这一新进程实际上会占用部分 CPU，占用的是已在节点上运行的服务的份额。 此问题有两种解决方案。 在同一群集中不混用治理和非治理服务，或使用[放置约束](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)，阻止这两种类型的服务最终位于同一组节点上。

* *其他进程在 Service Fabric 外的节点上启动（例如 OS 服务）* ：在这种情况下，Service Fabric 外的进程也会与现有服务争用 CPU。 此问题的解决方案是，考虑 OS 开销以正确设置节点容量，如下一部分中所示。

* 当请求不等于限制时：如前面的 RequestsAndLimits 示例所述，请求不会导致在节点上预留资源。 当某个节点上放置了限制大于请求的服务时，它消耗的资源（如果有）可能会达到其限制。 在此类情况下，节点上的其他服务消耗的资源可能可能无法达到其请求值。

## <a name="cluster-setup-for-enabling-resource-governance"></a>启用资源治理所需的群集设置

当节点启动并加入群集时，Service Fabric 会先检测可用内存量和可用内核数，再设置这两个资源的节点容量。

为了给操作系统以及可能在节点上运行的其他进程留出缓冲空间，Service Fabric 只使用节点上 80% 的可用资源。 此百分比可进行配置，并且可在群集清单中进行更改。

以下示例介绍如何指示 Service Fabric 使用 50% 的可用 CPU 和 70% 的可用内存：

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

对于大多数客户和方案，建议的配置是自动检测 CPU 和内存的节点容量（默认情况下自动检测已启用）。 但是，如果需要完全手动设置节点容量，则可以使用用于描述群集中节点的机制按节点类型对其进行配置。 下面的示例展示了如何设置具有四个核心和 2GB 内存的节点类型：

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

如果已启用自动检测可用资源，并在群集清单中手动定义了节点容量，Service Fabric 会检查节点中的资源是否足以支持用户定义的容量：

* 如果清单中定义的节点容量小于或等于节点上的可用资源，Service Fabric 使用清单中指定的容量。

* 如果清单中定义的节点容量大于可用资源，Service Fabric 使用可用资源作为节点容量。

如果不需要，可以禁用自动检测可用资源。 若要禁用此功能，请更改以下设置：

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

为了获得最佳性能，还应在群集清单中打开以下设置：

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> 从 Service Fabric version 7.0 开始，我们更新了在用户手动提供节点资源容量值的情况下，节点资源容量的规则计算方法。 让我们考虑以下这种情况：
>
> * 节点上总共有 10 个 CPU 核心
> * SF 配置为使用用户服务总资源的 80%（默认设置），这将为节点上运行的其他服务（包括 Service Fabric 系统服务）保留 20% 的缓冲区
> * 用户决定手动覆盖 CPU 核心数指标的节点资源容量，并将其设置为 5 个核心
>
> 我们已更改了关于 Service Fabric 用户服务可用容量的规则计算方式，内容如下：
>
> * 在 Service Fabric 7.0 之前，用户服务的可用容量将计算为 5 个核心数（忽略 20% 的容量缓冲区）
> * 从 Service Fabric 7.0 开始，用户服务的可用容量将计算为 4 个核心数（不忽略 20% 的容量缓冲区）

## <a name="specify-resource-governance"></a>指定资源治理

资源治理请求和限制是在应用程序清单（ServiceManifestImport 部分）中指定的。 让我们看看几个示例：

**示例 1：RequestsOnly 规范**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

在此示例中，使用 `CpuCores` 属性指定的请求为 1 个 CPU 核心（适用于 **ServicePackageA**）。 由于未指定 CPU 限制（`CpuCoresLimit` 属性），Service Fabric 还使用指定的请求值（1 个核心）作为服务包的 CPU 限制。

**ServicePackageA** 将仅放置在符合以下要求的节点上：减去 **放置在该节点上的所有服务包的 CPU 请求之和** 后，剩余 CPU 容量大于或等于 1 个核心。 在该节点上，服务包的核心数限制为一个核心。 此服务包包含两个代码包（**CodeA1** 和 **CodeA2**），并且都指定了 `CpuShares` 属性。 CpuShares 512:256 这一比例用来计算各个代码包的 CPU 限制。 因此，CodeA1 的限制为一个核心的三分之二，CodeA2 的限制为一个核心的三分之一。 如果没有为所有代码包指定 CpuShares，则 Service Fabric 会在这两个代码包之间平分 CPU 限制。

尽管为代码包指定的 CpuShares 表示服务包总体 CPU 限制的相对比例，但代码包的内存值是以绝对值指定的。 在此示例中，`MemoryInMB` 特性用于为 CodeA1 和 CodeA2 指定 1024 MB 的内存请求。 由于未指定内存限制（`MemoryInMBLimit` 特性），因此 Service Fabric 还使用指定的请求值作为代码包的限制。 服务包的内存请求（和限制）计算为其成分代码包的内存请求（和限制）值之和。 因此，对于 **ServicePackageA**，内存请求和限制的计算结果为 2048 MB。

**ServicePackageA** 将仅放置在符合以下要求的节点上：减去 **放置在该节点上的所有服务包的内存请求之和** 后，剩余内存容量大于或等于 2048 MB。 在该节点上，两个代码包每个的内存限制为 1024 MB。 代码包（容器或进程）无法分配超出此限制的内存，尝试那样做会导致内存不足异常。

**示例 2：LimitsOnly 规范**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
此示例使用 `CpuCoresLimit` 和 `MemoryInMBLimit` 特性，这些特性仅在 SF 7.2 及更高版本中可用。 CpuCoresLimit 特性用来为 **ServicePackageA** 指定 1 个核心这一 CPU 限制。 由于未指定 CPU 请求（`CpuCores` 特性），因此它将被视为 0。 `MemoryInMBLimit` 特性用于为 CodeA1 和 CodeA2 指定 1024 MB 的内存限制，由于未指定请求（`MemoryInMB` 特性），它们会被视为 0。 因此，**ServicePackageA** 的内存请求和限制分别计算为 0 和 2048。 由于 **ServicePackageA** 的 CPU 和内存请求均为 0，因此对于 `servicefabric:/_CpuCores` 和 `servicefabric:/_MemoryInMB` 指标，它并没有为 CRM 提供需要考虑进行放置的负载。 因此，从资源治理的角度来看，**不管剩余容量为多少**，**ServicePackageA** 都可以放置在任何节点上。 类似于示例 1，在该节点上，CodeA1 的限制为一个核心的三分之二和 1024 MB 内存，CodeA2 的限制为一个核心的三分之一和 1024 MB 内存。

**示例 3：RequestsAndLimits 规范**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
基于前两个示例，此示例演示了如何指定针对 CPU 和内存的请求和限制。 **ServicePackageA** 的 CPU 和内存请求分别为 1 个核心和 3072 (1024 + 2048) MB。 它只能放置在从节点的总 CPU（和内存）容量中减去放置在节点上的所有服务包的所有 CPU（和内存）请求之和后至少剩余 1 个核心（和 3072 MB）容量的节点上。 在该节点上，CodeA1 的限制为 2 个核心的三分之二和 3072 MB 内存，CodeA2 的限制为 2 个核心的三分之一和 4096 MB 内存。

### <a name="using-application-parameters"></a>使用应用程序参数

指定资源调控设置时，可使用[应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)管理多个应用配置。 下例展示应用程序参数的用法：

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

在此示例中，会为生产环境设置默认参数，其中每个服务包具有 4 核和 2 GB 内存。 可使用应用程序参数文件更改默认值。 在此示例中，一个参数文件可以用来本地测试应用程序，其中它获得的资源将少于生产中所得：

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> 从 Service Fabric 6.1 版开始，可使用应用程序参数指定资源调控。<br>
>
> 使用应用程序参数指定资源调控时，Service Fabric 无法降级到 6.1 之前的版本。

## <a name="enforcing-the-resource-limits-for-user-services"></a>强制执行用户服务的资源限制

虽然将资源治理应用于 Service Fabric 服务可以确保这些受治理资源服务不超过其资源配额，但许多用户仍然需要以非治理模式运行某些 Service Fabric 服务。 使用非治理 Service Fabric 服务时，可能会遇到“失控”的非治理服务消耗 Service Fabric 节点上所有可用资源的情况，这可能会导致严重的问题，例如：

* 节点上运行的其他服务（包括 Service Fabric 系统服务）的资源不足
* 节点以不正常状态结束
* 群集管理 API Service Fabric 无响应

为了防止发生这些情况，Service Fabric 允许你对节点上运行的所有 Service Fabric 用户服务（受治理的和未受治理的）实施资源限制，以确保用户服务永远不会使用超过指定数量的资源。 此限制可通过将 ClusterManifest 的 PlacementAndLoadBalancing 部分中的 EnforceUserServiceMetricCapacities 配置的值设置为 true 来实现。 默认情况下，此设置处于关闭状态。

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

其他备注：

* 资源限制强制仅适用于 `servicefabric:/_CpuCores` 和 `servicefabric:/_MemoryInMB` 资源指标
* 仅当资源指标的节点容量可用于 Service Fabric 时，资源限制强制实施才会起作用，可以通过自动检测机制，也可以通过用户手动指定节点容量（如[启用资源治理的群集设置](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)部分所述）。 如果未配置节点容量，则无法使用资源限制强制实施功能，因为 Service Fabric 不知道要为用户服务保留多少资源。 如果“EnforceUserServiceMetricCapacities”为 true 但未配置节点容量，则 Service Fabric 将发出运行状况警告。

## <a name="other-resources-for-containers"></a>容器的其他资源

除了 CPU 和内存之外，还可以为容器指定[其他资源限制](service-fabric-service-model-schema-complex-types.md#resourcegovernancepolicytype-complextype)。 这些限制是在代码包一级指定，并在容器启动时应用。 这些资源与 CPU 和内存不同，群集资源管理器不会注意到它们，也不会针对它们进行任何容量检查或负载均衡。

* MemorySwapInMB：可以使用的交换内存的总容量，以 MB 为单位。 必须是正整数。
* MemoryReservationInMB：内存治理软限制（以 MB 为单位），仅当在节点上检测到内存争用时，才强制执行此限制。 必须是正整数。
* CpuPercent：可用 CPU 的可用百分比（仅适用于 Windows）。 必须是正整数。 不能与 CpuShares、CpuCores 或 CpuCoresLimit 一起使用。
* CpuShares：相对 CPU 权重。 必须是正整数。 不能与 CpuPercent、CpuCores 或 CpuCoresLimit 一起使用。
* MaximumIOps：以能够使用的 IOps 计的最大 IO 速率（读取和写入）。 必须是正整数。
* MaximumIOBandwidth：可以使用（读取和写入）的最大 IO（字节/秒）。 必须是正整数。
* BlockIOWeight：相对于其他代码包的块 IO 权重。 必须是介于 10 和 1000 之间的正整数。
* DiskQuotaInMB：容器的磁盘配额。 必须是正整数。
* KernelMemoryInMB：内核内存限制（以字节为单位）。  必须是正整数。  请注意，这是特定于 Linux 的，Windows 上的 Docker 在进行此设置的情况下会出错。
* ShmSizeInMB：/dev/shm 的大小（以字节为单位）。 如果省略此项，系统会使用 64MB。  必须是正整数。 请注意，这是特定于 Linux 的，但 Docker 仅在指定此项的情况下会忽略。

这些资源可与 CPU 和内存组合。 以下示例显示如何为容器指定其他资源：

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>后续步骤

* 若要详细了解群集资源管理器，请阅读 [Service Fabric 群集资源管理器简介](service-fabric-cluster-resource-manager-introduction.md)。
* 若要详细了解应用程序模型、服务包、代码包以及如何将副本映射到它们，请阅读 [Service Fabric 中的应用程序建模][application-model-link]。

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
