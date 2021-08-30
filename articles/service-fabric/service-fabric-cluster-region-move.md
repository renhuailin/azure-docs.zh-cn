---
title: 将 Azure Service Fabric 群集移到新的区域
description: 如何将 Azure Service Fabric 群集和应用程序迁移到另一个区域。
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: micraft
ms.openlocfilehash: ddda30f949ac4abfe7f19890ce9f7c6f8fedadbe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778718"
---
# <a name="move-an-azure-service-fabric-cluster-to-a-new-region"></a>将 Azure Service Fabric 群集移到新的区域

Service Fabric 群集资源的范围本质上局限于一个区域。 此范围意味着，当前通过首先在目标区域中创建一个新群集、迁移现有的工作负载，然后将流量定向到这个新的目标区域来完成“区域移动”。 本文档概述了完成此迁移所需的步骤。 有几个决策点可以决定迁移的复杂程度。 这些输入包括如何设置并配置群集和应用程序，通信在群集中如何工作，以及工作负载是无状态、有状态还是两者都有。  


## <a name="steps-to-follow-for-a-region-migration"></a>区域迁移要遵循的步骤

在参与任何区域迁移之前，建议建立一个试验台并执行这些步骤。 

## <a name="create-new-cluster"></a>创建新群集
1. 通过针对群集和基础结构拓扑调整现有 ARM 模板的用途，[在新区域中设置一个群集](./service-fabric-cluster-creation-via-arm.md#use-your-own-custom-template)。 如果当前没有描述群集的 ARM 模板，则建议从[Azure 资源浏览器](https://resources.azure.com/)检索当前 ARM 模板。 Azure 资源浏览器可帮助你发现当前已部署的资源及其配置信息，你可以使用这些资源和信息来创建一个或多个 ARM 模板，以便重复部署现有环境的克隆。 在继续操作之前，请测试并确认你具有可在此阶段部署现有环境克隆的可用 ARM 模板。 

## <a name="move-applications-and-traffic"></a>移动应用程序和流量
1. [通过 ARM 部署现有的应用程序和服务](service-fabric-application-arm-resource.md)。 请注意保留已执行的任何应用程序参数或配置自定义。 例如，如果应用程序有一个默认值为 5 的“count”参数，但你已在源环境中将该参数的值升级为 7，则需要确保该值在新区域的应用程序部署中也设置为 7。 如果你不使用 ARM 来管理应用程序和服务实例，则要负责识别当前区域中运行的当前应用程序和服务集及其配置，并在新的区域/群集中复制这些应用程序和服务。 

2. 迁移服务  
   -  对于有状态工作负载： 
      * <p>为了确保有状态服务已经达到稳定点，请首先确保已停止向这些服务传入流量。 执行此操作的方式取决于如何将流量传递给你的服务。 例如，你可能必须从事件中心切断该服务，或者通过删除相应的路由或转发规则来阻止 APIM 或 Azure 网络负载均衡器等服务将流量路由到该服务。 流量停止并且服务完成了与这些请求相关的任何后台工作后，可以继续操作。 </p>
      
      * 使用[备份还原服务](service-fabric-reliable-services-backup-restore.md)并执行[按需备份](service-fabric-backup-restore-service-ondemand-backup.md)，从任何有状态服务中进行备份。 请确保在流量停止并且任何后台处理工作完成之后完成此操作。 完成后，可将[数据还原](service-fabric-backup-restore-service-trigger-restore.md)到新区域和群集中的有状态服务中。 用于执行备份的 Azure 存储帐户必须可从新区域访问。

   -  对于无状态服务： 
      * <p>除了将服务部署到新群集（理想情况下，在步骤 2 中完成的 ARM 应用程序部署的过程中实现此操作），并确保它们的配置与源群集中的配置相同以外，不应该有额外的工作。</p>

   -  对于所有的服务：  
      * <p>确保客户端和服务之间任何通信阶段的配置与源群集类似。 例如，此验证可能包括确保将事件中心、网络负载均衡器、应用程序网关或 API 管理等中介设置了允许流量流向群集所需的规则。</p>  

3. 将流量从旧区域重定向到新区域。 建议使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) 进行迁移，因为它提供了一系列的[路由方法](../traffic-manager/traffic-manager-routing-methods.md)。 如何准确地更新流量传递规则取决于你要保留还是弃用现有区域，还取决于应用程序中流量的流动方式。 你可能需要调查是否可以在不同区域中的不同 Azure 资源之间移动专用/公共 IP 或 DNS 名称。 Service Fabric 不识别系统的这一部分，因此请进行调查，并在必要时让与你的流量流相关的 Azure 团队参与进来（特别是在该部分更复杂或你的工作负载对延迟非常关键的情况下）。 [配置自定义域](../api-management/configure-custom-domain.md)、[公共 IP 地址](../virtual-network/public-ip-addresses.md)和 [DNS 区域和记录](../dns/dns-zones-records.md)等文档也很有用，它们是你根据流量流和协议而需要的信息的示例。 下面是两个示例场景，演示了如何更新流量路由：  
   * 如果你不打算保留现有的源区域，并且有一个与网络负载均衡器（它将调用传递到原始源群集）的公共 IP 关联的 DNS/CNAME， 更新要与新区域中新的网络负载均衡器的新公共 IP 关联的 DNS/CNAME。 完成该传输将导致使用现有群集的客户端切换到使用新群集。 
  
   * 如果你打算保留现有的源区域，并且有一个与网络负载均衡器（它将调用传递到原始源群集）的公共 IP 关联的 DNS/CNAME， 设置 Azure 流量管理器的实例，然后将 DNS 名称与该 Azure 流量管理器实例相关联。 Azure 流量管理器可以配置为路由到每个区域中的单个网络负载均衡器。 

4. 如果你打算保留这两个区域，则通常会进行某种类型的“反向同步”，其中事实源保留在某个远程存储中，（例如 SQL、CosmosDB、Blob 或文件存储），然后在区域之间进行同步。 如果这适用于你的工作负载，则建议确认数据按预期在区域之间流动。  

## <a name="final-validation"></a>最终验证
1. 作为最终验证，请验证流量是否按预期流动，以及新区域（可能还有旧区域）中的服务是否按预期运行。 

2. 如果不打算保留原始源区域，则可以在此时删除该区域中的资源。 建议在删除资源之前等待一段时间，以防发现某个需要回滚到原始源区域的问题。  

## <a name="next-steps"></a>后续步骤
将群集和应用程序移动到新区域后，你应该验证是否设置了备份来保护任何所需的数据。

> [!div class="nextstepaction"]
> [迁移后设置备份](service-fabric-backuprestoreservice-quickstart-azurecluster.md)