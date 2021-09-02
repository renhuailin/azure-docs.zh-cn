---
title: Azure Service Fabric 版本
description: Azure Service Fabric 发行说明。 包含有关 Service Fabric 中的最新功能和改进功能的信息。
ms.date: 04/13/2021
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 11615d48bb1331e068c685a507670f0add883344
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721844"
---
# <a name="service-fabric-releases"></a>Service Fabric 版本

本文提供了有关 Service Fabric 运行时和 SDK 的最新版本和更新的详细信息。

还可以使用以下资源：
- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">故障排除指南</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">问题跟踪</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">支持选项</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">支持的版本</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">示例代码</a>


## <a name="service-fabric-81"></a>Service Fabric 8.1

我们很高兴地宣布，Service Fabric 运行时 8.1 版本已经开始与工具和 SDK 更新一起向各个 Azure 区域推出。 .NET SDK、Java SDK 和 Service Fabric 运行时的更新可通过 Web 平台安装程序、NuGet 包和 Maven 存储库获得。

### <a name="key-announcements"></a>重要公告
- 添加了对辅助副本的支持
- 预览版添加了对 .NET 6.0 Service Fabric 应用程序的支持
- 添加了对更新应用程序说明的 API 支持
- 添加了在重新配置代理 (RA) 和重新配置代理程序代理 (RAP) 之间定期 ping，以检测 IPC 故障和进程停滞
- 添加了对非容器化应用程序的运行状态和就绪情况探测的支持
- 使节点容量更新的群集升级无影响

### <a name="service-fabric-81-releases"></a>Service Fabric 8.1 版本
| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2021 年 7 月 28 日 | [Azure Service Fabric 8.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-release/ba-p/2594194)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81.md)|


## <a name="service-fabric-80"></a>Service Fabric 8.0

我们很高兴地宣布，Service Fabric 运行时的 8.0 版本已经开始在不同的 Azure 区域推出，同时还有工具和 SDK 更新。 .NET SDK、Java SDK 和 Service Fabric 运行时的更新可通过 Web 平台安装程序、NuGet 包和 Maven 存储库获得。

### <a name="key-announcements"></a>重要公告

- 对 Windows 的 .NET 5 支持正式发布
- [无状态 NodeTypes](./service-fabric-stateless-node-types.md)正式发布
- 能够移动无状态服务实例
- 能够在应用程序清单中添加参数化 DefaultLoad
- 对于单一副本升级 - 能够在应用程序级别定义某些群集级别设置
- 能够基于节点标记进行智能放置
- 能够定义影响群集运行状况的不正常节点的百分比阈值
- 能够查询加载排名靠前的服务
- 能够添加新错误代码的新间隔
- 将服务实例标记为已完成的功能
- 支持基于波的部署模型，用于自动升级
- 为容器化应用程序添加了就绪情况探测
- 默认情况下，将 UseSeparateSecondaryMoveCost 启用为 true
- 修复了 StateManager 在安全发布时立即发布引用的问题
- 在存储用户机密时阻止删除中央机密服务

### <a name="service-fabric-80-releases"></a>Service Fabric 8.0 版本
| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2021 年 4 月 8 日 | [Azure Service Fabric 8.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-release/ba-p/2260016)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80.md)|
| 2021 年 5 月 17 日 | [Azure Service Fabric 8.0 第一次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-first-refresh-release/ba-p/2362556) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU1.md) |
| 2021 年 6 月 17 日 | [Azure Service Fabric 8.0 第二次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-second-refresh-release/ba-p/2462979) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU2.md) |
| 2021 年 7 月 28 日 | [Azure Service Fabric 8.0 第三次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-third-refresh-release/ba-p/2594180) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU3.md) |


## <a name="previous-versions"></a>旧版

### <a name="service-fabric-72"></a>Service Fabric 7.2

#### <a name="key-announcements"></a>重要公告

- **预览**：[Service Fabric 托管群集](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572)目前为公共预览版。 Service Fabric 托管群集旨在通过将构成 Service Fabric 群集的基础资源封装到单个 ARM 资源中来简化群集的部署和管理。 有关更多详细信息，请参阅 [Service Fabric 托管群集概述](./overview-managed-cluster.md)。
- **预览**：[支持实例数大于节点数的无状态服务](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)功能现在为公共预览版。 使用放置策略可以在一个节点上创建分区的多个无状态实例。
- [FabricObserver (FO) 3.0](https://aka.ms/sf/fabricobserver) 现在可用。
    - 现在可以在 Linux 和 Windows 群集中运行 FabricObserver。
    - 现在可以生成自定义观察程序插件。 有关详细信息和代码，请参阅[插件自述文件](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md)和[示例插件项目](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin)。
    - 现在可以通过应用程序参数升级来更改任何观察程序设置。 这意味着不再需要重新部署 FO 来修改特定的观察程序设置。 请参阅[示例](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates)。
- [支持 Ubuntu 18.04 OneBox 容器映像](https://hub.docker.com/_/microsoft-service-fabric-onebox)。
- **预览**：[Service Fabric 应用程序的 KeyVault 引用仅支持 ****进行了版本控制的机密**。不支持没有版本的机密。**](./service-fabric-keyvault-references.md)
- SF SDK 需要最新的 VS 2019 Update 16.7.6 或 16.8 Preview 4 才能创建新的 .NET Framework 无状态/有状态/执行组件项目。 如果没有最新的 VS 更新，请在创建服务项目后使用包管理器从 nuget.org 为有状态/无状态项目安装 Microsoft.ServiceFabric.Services（4.2.x 版），为执行组件项目安装 Microsoft.ServiceFabric.Actors（4.2.x 版）。
- **RunToCompletion**：Service Fabric 支持来宾可执行文件的“运行至完成”概念。 使用此更新时，副本运行至完成后，系统就会释放分配给该副本的群集资源。
- [资源治理支持得到了增强](./service-fabric-resource-governance.md)：允许请求并限制 CPU 和内存资源的规格。

#### <a name="service-fabric-72-releases"></a>Service Fabric 7.2 版本
| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2020 年 10 月 21 日 | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 2020 年 11 月 9 日 | [Azure Service Fabric 7.2 第二次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 2020 年 11 月 10 日  | Azure Service Fabric 7.2 第三次刷新版本 | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2020 年 12 月 2 日 | [Azure Service Fabric 7.2 第四次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 2021 年 1 月 25 日 | [Azure Service Fabric 7.2 第五次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 2021 年 2 月 17 日 | [Azure Service Fabric 7.2 第六次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 2021 年 3 月 10 日 | [Azure Service Fabric 7.2 第七次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)


### <a name="service-fabric-71"></a>Service Fabric 7.1

由于当前的 COVID-19 危机，并且考虑到我们的客户所面临的挑战，我们正在提供 7.1，但不会自动升级设置为接收自动升级的群集。 在进一步通知之前，我们将暂停自动升级，以确保客户可以在最适合的情况下应用升级，避免意外中断。

你可以通过 [Azure 门户](./service-fabric-cluster-upgrade-version-azure.md#manual-upgrades-with-azure-portal)或 [Azure 资源管理器部署](./service-fabric-cluster-upgrade-version-azure.md#resource-manager-template)更新到 7.1。

当我们恢复标准推出过程后，启用了自动升级的 Service Fabric 群集将开始自动接收 7.1 更新。 在标准推出开始之前，我们会在 [Service Fabric 技术社区网站](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)上发布另一个公告。
对于从 6.5 到 7.1 的重要版本，我们还在[此处](./service-fabric-versions.md)发布了对终止支持日期的更新。 

#### <a name="key-announcements"></a>重要公告

- [**Service Fabric 应用程序的 Service Fabric 托管标识**](./concepts-managed-identity.md)**正式发布**
- [**支持 Ubuntu 18.04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**预览版：虚拟机规模集临时 OS 磁盘支持**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**：“临时 OS 磁盘”是在本地虚拟机上创建的存储，不保存到远程 Azure 存储。 建议将它们用于所有 Service Fabric 节点类型（主要和次要），因为与传统的持久 OS 磁盘相比，临时 OS 磁盘：
      -  降低了到 OS 磁盘的读/写延迟
      -  可实现更快的重置/重建映像节点管理操作
      -  降低了总体成本（磁盘免费，不会产生额外的存储成本）
- 支持 [**按使用者公用名声明 Service Fabric 应用程序的服务终结点证书**](./service-fabric-service-manifest-resources.md)。
- [**支持对容器化服务进行运行状况探测**](./probes-codepackage.md)：支持针对容器化应用程序的运行情况探测机制。 运行情况探测可以用来通知容器化应用程序的运行情况，在应用程序未及时响应时会导致重启。 
- 支持针对 [容器](./service-fabric-containers-overview.md)和 [来宾可执行](./service-fabric-guest-executables-introduction.md)应用程序的 [**初始化表达式代码包**](./initializer-codepackages.md)。 因此，可以按指定顺序执行代码包（例如容器），以便执行服务包初始化。
- **FabricObserver 和 ClusterObserver** 是无状态应用程序，用于捕获与 SF 群集的不同方面相关的 Service Fabric 遥测数据。 这两个应用程序都已做好部署到 Windows 生产群集的准备，可以通过所实施的对 ApplicationInsights、EventSource 和 LogAnalytics 的支持功能来捕获丰富的遥测数据。
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer) - 在所有节点上运行，生成运行状况事件，并在达到用户配置的资源使用率阈值时发出遥测数据。 此版本包含的几项增强功能涉及监视、数据管理、运行状况事件详细信息、结构化遥测。
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - 在一个节点上运行，捕获群集级运行状况遥测。 在此版本中，ClusterObserver 还监视节点状态，并在节点处于关闭/正在禁用/已禁用状态的时间超过用户指定的时长时发出遥测数据。

#### <a name="improve-application-life-cycle-experience"></a>改进应用程序生命周期体验

- **[预览版：请求清空](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** ：在计划内服务维护（例如服务升级或节点停用）期间，你希望允许服务正常清空连接。 此功能在服务配置中添加了实例关闭延迟持续时间。 在计划内操作期间，SF 会从发现结果中删除服务的地址，并在关闭服务前等待一段时间（即该持续时间）。
- **[自动化子聚类检测和平衡](./cluster-resource-manager-subclustering.md)** ：当具有不同放置约束的服务具有共同的 [负载指标](./service-fabric-cluster-resource-manager-metrics.md)时，就会发生子聚类化。 如果不同节点集上的负载差别很大，则 Service Fabric 群集资源管理器会认为群集不均衡，即使已达到了受制于放置约束的最佳平衡。 因此，它会尝试重新均衡群集，这可能会导致不必要的服务移动（因为无法显著改进“不均衡”）。 从此版本开始，群集资源管理器现在将尝试自动检测这些种类的配置，并了解何时可以通过移动来修复不均衡的情况，以及何时应该放任不管（因为无法做出实质性的改进）。  
- [**次要副本的不同移动成本**](./service-fabric-cluster-resource-manager-movement-cost.md)：我们引入了新的移动成本值 VeryHigh，在某些场景下提供了额外的灵活性，可定义次要副本是否应使用单独的移动成本。
- 启用了针对容器化应用程序的 [**运行情况探测**](./probes-codepackage.md)机制。 运行情况探测可以用来通知容器化应用程序的运行情况，在应用程序未及时响应时会导致重启。
- [**为服务运行至完成/运行一次**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>映像存储改进
 - Service Fabric 7.1 使用可默认保护节点之间的文件传输的自定义传输。 版本 7.1 中移除了对 SMB 文件共享的依赖。 受保护的 SMB 文件共享仍存在于包含映像存储服务副本的节点上，允许客户选择退出默认设置、进行升级以及降级到旧版本。
       
 #### <a name="reliable-collections-improvements"></a>可靠集合改进

- [**为使用 Reliable Collections 的有状态服务提供“纯内存中”存储支持**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)：Volatile Reliable Collections 允许将数据持久保存到磁盘中，以防止大规模中断，例如，可将其用于复制的缓存（允许偶尔丢失数据）之类的工作负荷。 根据 [Volatile Reliable Collections 的限制和局限](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)，对于不需要持久保存的工作负荷，以及用于处理罕见的仲裁丢失的服务，建议使用此功能。
- [**预览版：Service Fabric 备份资源管理器**](https://github.com/microsoft/service-fabric-backup-explorer)：为了便于管理 Service Fabric 有状态应用程序的 Reliable Collections 备份，Service Fabric 备份资源管理器允许用户执行以下操作：
    - 审核和查看 Reliable Collections 的内容
    - 将当前状态更新为一致视图
    - 创建 Reliable Collections 的当前快照的备份
    - 修复数据损坏
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 版本
| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2020 年 4 月 20 日 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [发行说明](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 2020 年 6 月 16 日 | [Microsoft Azure Service Fabric 7.1 第一次刷新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 2020 年 7 月 20 日 | [Microsoft Azure Service Fabric 7.1 第二次刷新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 2020 年 8 月 12 日 | [Microsoft Azure Service Fabric 7.1 第三次刷新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 2020 年 9 月 10 日 | [Microsoft Azure Service Fabric 7.1 第四次刷新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 2020 年 10 月 7 日 | Microsoft Azure Service Fabric 7.1 第六次刷新 | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 2020 年 11 月 23 日 | Microsoft Azure Service Fabric 7.1 第八次刷新 | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 现已推出！ 你可以通过 Azure 门户或 Azure 资源管理器部署更新到 7.0。 由于客户对假期发布的产品有反馈意见，因此我们要到 1 月才会开始自动更新设置为接收自动升级的群集。
在 1 月，我们将恢复标准推出过程，启用了自动升级的群集将开始自动接收 7.0 更新。 在推出开始之前，我们将发布另一个公告。
我们还会更新我们的计划发布日期，这样是为了表明我们考虑到了此策略。 在此处查看有关我们将来的[发行日程安排](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)的更新。

#### <a name="key-announcements"></a>重要公告
 - [**对应用程序机密的 KeyVaultReference 支持（预览版）**](./service-fabric-keyvault-references.md)：已启用 [托管标识](./concepts-managed-identity.md)的 Service Fabric 应用程序现在可以直接将 Key Vault 机密 URL 引用为环境变量、应用程序参数或容器存储库凭据。 Service Fabric 会使用应用程序的托管标识自动解析机密。 
     
- **改进了无状态服务的升级安全性**：为了保证应用程序升级期间的可用性，我们引入了新的配置来定义视为可用的 [无状态服务的最小实例数](/dotnet/api/system.fabric.description.statelessservicedescription)。 以前，对于所有服务，此值都是 1，且不可更改。 凭借此全新的每服务安全检查，你可以确保服务在应用程序升级、群集升级和其他维护（依赖于 Service Fabric 的运行状况和安全检查）期间保留最少的正常运行实例数。
  
- [**用户服务的资源限制**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services)：用户可以为节点上的用户服务设置资源限制，以防止诸如 Service Fabric 系统服务资源耗尽之类的情况。 
  
- 副本类型的 [**服务移动成本非常高**](./service-fabric-cluster-resource-manager-movement-cost.md)。 只有当群集中存在约束冲突且该冲突无法通过任何其他方式解决时，才会移动成本非常高的副本。 若要详细了解何时使用“非常高”的移动成本是合理的，并了解其他注意事项，请参考链接的文档。
  
-  **其他群集安全检查**：在此版本中，我们引入了一项可配置的种子节点仲裁安全检查。 这允许你自定义在群集生命周期和管理方案中必须有多少种子节点可用。 导致群集低于所配置值的操作会被阻止。 现在，默认值始终是种子节点的仲裁，例如，如果你有 7 个种子节点，则默认情况下会阻止导致群集低于 5 个种子节点的操作。 进行此更改后，你可以将最小安全值设置为 6，这样，一次只允许关闭一个种子节点。
   
- 添加了对 [**在 Service Fabric Explorer 中管理备份和还原服务**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)的支持。 这允许直接从 SFX 内执行以下活动：发现备份和还原服务、创建备份策略、启用自动备份、执行即席备份、触发还原操作和浏览现有备份。

- 宣布 [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool) 已推出：此工具可帮助验证在滚动应用程序升级期间在可靠集合中使用的类型是否前向和后向兼容。 这有助于防止因类型缺失或不兼容而导致升级失败或数据丢失和数据损坏。

- [**在次要副本上启用稳定读取**](./service-fabric-reliable-services-configuration.md#configuration-names-1)：稳定读取会将次要副本限制为返回仲裁确认的值。

此外，此版本还包含其他新功能、bug 修复、可支持性、可靠性和性能改进。 有关完整的变更列表，请参阅[发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)。

#### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 版本

| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020 年 1 月 30 日 | [Azure Service Fabric 7.0 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020 年 2 月 6 日 | [Azure Service Fabric 7.0 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020 年 3 月 2 日 | [Azure Service Fabric 7.0 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 2020 年 5 月 6 日 | [Azure Service Fabric 7.0 第六次刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 2020 年 10 月 9 日 | Azure Service Fabric 7.0 第九次刷新版本 | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

此版本包含可支持性、可靠性和性能改进，新功能、bug 修复和增强功能，可简化群集和应用程序生命周期管理。

> [!IMPORTANT]
> Service Fabric 6.5 是 Visual Studio 2015 中具有 Service Fabric 工具支持的最终版本。 今后，建议客户迁移到 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)。

Service Fabric 6.5 中的新增功能：

- Service Fabric Explorer 包括了一个[映像存储查看器](service-fabric-visualizing-your-cluster.md#image-store-viewer)，用于检查已上传到映像存储的应用程序。

- [修补业务流程应用程序 (POA)](service-fabric-patch-orchestration-application.md) 版本 [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) 包含许多自我诊断改进。 建议 POA 的客户改用此版本。

- 对于 Service Fabric 6.5 群集，[EventStore 服务默认启用](service-fabric-visualizing-your-cluster.md#event-store)，除非你已选择退出。

- 针对有状态服务添加了[副本生命周期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)。

- [种子节点状态的可见性更好](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)，包括种子节点不正常（关闭、已移除或未知  ）时的群集级警告。

- [Service Fabric 应用程序灾难恢复工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)使得 Service Fabric 有状态服务在主群集遇到灾难时能够迅速恢复。 使用定期备份和还原在辅助备用应用程序上持续同步主群集中的数据。

- Visual Studio 支持[将 .NET Core 应用发布到基于 Linux 的群集](service-fabric-how-to-publish-linux-app-vs.md)。

- 当你在 Azure 上升级或创建新的 Linux 群集时，系统将自动为 Service Fabric 6.5（及更高版本）安装 [Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md)。

- 在 MacOS/Linux OneBox 群集上，默认情况下会安装 [SFCTL](./service-fabric-cli.md)。

有关更多详细信息，请参阅 [Service Fabric 6.5 发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)。

#### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 版本

| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019 年 10 月 14 日 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 发布日期 | 发布 |
|---|---|
| 2018 年 11 月 30 日 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/) |
| 2018 年 12 月 12 日 | [适用于 Windows 群集的 Azure Service Fabric 6.4 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019 年 2 月 4 日 | [Azure Service Fabric 6.4 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019 年 3 月 4 日 | [Azure Service Fabric 6.4 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019 年 4 月 8 日 | [Azure Service Fabric 6.4 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019 年 5 月 2 日 | [Azure Service Fabric 6.4 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019 年 5 月 28 日 | [Azure Service Fabric 6.4 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
