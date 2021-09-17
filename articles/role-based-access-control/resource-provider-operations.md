---
title: Azure 资源提供程序操作
description: 列出 Azure 资源提供程序的操作。
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 08/13/2021
ms.custom: generated
ms.openlocfilehash: 868c7f47d151331fe9cc5b8352e2aa82797e1728
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634068"
---
# <a name="azure-resource-provider-operations"></a>Azure 资源提供程序操作

本部分列出了在内置角色中使用的 Azure 资源提供程序的操作。 可以在自己的 [Azure 自定义角色](custom-roles.md)中使用这些操作，以针对 Azure 中的资源提供精细的访问控制。 资源提供程序操作一直在不断发展。 若要获取最新操作，请使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 或 [az provider operation list](/cli/azure/provider/operation#az_provider_operation_list)。

单击下表中的资源提供程序名称可以查看操作的列表。

## <a name="all"></a>全部

| 常规 |
| --- |
| [Microsoft.Addons](#microsoftaddons) |
| [Microsoft.Marketplace](#microsoftmarketplace) |
| [Microsoft.MarketplaceApps](#microsoftmarketplaceapps) |
| [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering) |
| [Microsoft.ResourceHealth](#microsoftresourcehealth) |
| [Microsoft.Support](#microsoftsupport) |
| **计算** |
| [Microsoft.ClassicCompute](#microsoftclassiccompute) |
| [Microsoft.Compute](#microsoftcompute) |
| [Microsoft.ServiceFabric](#microsoftservicefabric) |
| **联网** |
| [Microsoft.Cdn](#microsoftcdn) |
| [Microsoft.ClassicNetwork](#microsoftclassicnetwork) |
| [Microsoft.Network](#microsoftnetwork) |
| **存储** |
| [Microsoft.ClassicStorage](#microsoftclassicstorage) |
| [Microsoft.DataBox](#microsoftdatabox) |
| [Microsoft.DataShare](#microsoftdatashare) |
| [Microsoft.ImportExport](#microsoftimportexport) |
| [Microsoft.NetApp](#microsoftnetapp) |
| [Microsoft.Storage](#microsoftstorage) |
| [microsoft.storagesync](#microsoftstoragesync) |
| [Microsoft.StorSimple](#microsoftstorsimple) |
| **Web** |
| [Microsoft.AppPlatform](#microsoftappplatform) |
| [Microsoft.CertificateRegistration](#microsoftcertificateregistration) |
| [Microsoft.DomainRegistration](#microsoftdomainregistration) |
| [Microsoft.Maps](#microsoftmaps) |
| [Microsoft.Media](#microsoftmedia) |
| [Microsoft.Search](#microsoftsearch) |
| [Microsoft.SignalRService](#microsoftsignalrservice) |
| [microsoft.web](#microsoftweb) |
| **容器** |
| [Microsoft.ContainerInstance](#microsoftcontainerinstance) |
| [Microsoft.ContainerRegistry](#microsoftcontainerregistry) |
| [Microsoft.ContainerService](#microsoftcontainerservice) |
| **数据库** |
| [Microsoft.Cache](#microsoftcache) |
| [Microsoft.DataFactory](#microsoftdatafactory) |
| [Microsoft.DataMigration](#microsoftdatamigration) |
| [Microsoft.DBforMariaDB](#microsoftdbformariadb) |
| [Microsoft.DBforMySQL](#microsoftdbformysql) |
| [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql) |
| [Microsoft.DocumentDB](#microsoftdocumentdb) |
| [Microsoft.Sql](#microsoftsql) |
| [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine) |
| **分析** |
| [Microsoft.AnalysisServices](#microsoftanalysisservices) |
| [Microsoft.Databricks](#microsoftdatabricks) |
| [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics) |
| [Microsoft.DataLakeStore](#microsoftdatalakestore) |
| [Microsoft.EventHub](#microsofteventhub) |
| [Microsoft.HDInsight](#microsofthdinsight) |
| [Microsoft.Kusto](#microsoftkusto) |
| [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated) |
| Microsoft.Purview |
| [Microsoft.StreamAnalytics](#microsoftstreamanalytics) |
| [Microsoft.Synapse](#microsoftsynapse) |
| 区块链 |
| [Microsoft.Blockchain](#microsoftblockchain) |
| AI + 机器学习 |
| [Microsoft.BotService](#microsoftbotservice) |
| [Microsoft.CognitiveServices](#microsoftcognitiveservices) |
| [Microsoft.MachineLearning](#microsoftmachinelearning) |
| [Microsoft.MachineLearningServices](#microsoftmachinelearningservices) |
| **物联网** |
| [Microsoft.Devices](#microsoftdevices) |
| [Microsoft.DeviceUpdate](#microsoftdeviceupdate) |
| [Microsoft.IoTCentral](#microsoftiotcentral) |
| [Microsoft.IoTSecurity](#microsoftiotsecurity) |
| [Microsoft.NotificationHubs](#microsoftnotificationhubs) |
| [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights) |
| 混合现实 |
| [Microsoft.MixedReality](#microsoftmixedreality) |
| **集成** |
| [Microsoft.ApiManagement](#microsoftapimanagement) |
| [Microsoft.AppConfiguration](#microsoftappconfiguration) |
| [Microsoft.AzureStack](#microsoftazurestack) |
| [Microsoft.DataBoxEdge](#microsoftdataboxedge) |
| [Microsoft.DataCatalog](#microsoftdatacatalog) |
| [Microsoft.EventGrid](#microsofteventgrid) |
| [Microsoft.Logic](#microsoftlogic) |
| [Microsoft.Relay](#microsoftrelay) |
| [Microsoft.ServiceBus](#microsoftservicebus) |
| **标识** |
| [Microsoft.AAD](#microsoftaad) |
| [microsoft.aadiam](#microsoftaadiam) |
| [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice) |
| [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory) |
| [Microsoft.ManagedIdentity](#microsoftmanagedidentity) |
| **安全性** |
| [Microsoft.KeyVault](#microsoftkeyvault) |
| [Microsoft.Security](#microsoftsecurity) |
| [Microsoft.SecurityGraph](#microsoftsecuritygraph) |
| [Microsoft.SecurityInsights](#microsoftsecurityinsights) |
| **DevOps** |
| [Microsoft.DevTestLab](#microsoftdevtestlab) |
| [Microsoft.LabServices](#microsoftlabservices) |
| [Microsoft.VisualStudio](#microsoftvisualstudio) |
| **迁移** |
| [Microsoft.Migrate](#microsoftmigrate) |
| [Microsoft.OffAzure](#microsoftoffazure) |
| **监视** |
| [Microsoft.AlertsManagement](#microsoftalertsmanagement) |
| [Microsoft.Insights](#microsoftinsights) |
| [Microsoft.OperationalInsights](#microsoftoperationalinsights) |
| [Microsoft.OperationsManagement](#microsoftoperationsmanagement) |
| [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor) |
| 管理 + 治理 |
| [Microsoft.Advisor](#microsoftadvisor) |
| [Microsoft.Authorization](#microsoftauthorization) |
| [Microsoft.Automation](#microsoftautomation) |
| [Microsoft.Batch](#microsoftbatch) |
| [Microsoft.Billing](#microsoftbilling) |
| [Microsoft.Blueprint](#microsoftblueprint) |
| [Microsoft.Capacity](#microsoftcapacity) |
| [Microsoft.Commerce](#microsoftcommerce) |
| [Microsoft.Consumption](#microsoftconsumption) |
| [Microsoft.CostManagement](#microsoftcostmanagement) |
| [Microsoft.DataProtection](#microsoftdataprotection) |
| [Microsoft.Features](#microsoftfeatures) |
| [Microsoft.GuestConfiguration](#microsoftguestconfiguration) |
| [Microsoft.HybridCompute](#microsofthybridcompute) |
| [Microsoft.Kubernetes](#microsoftkubernetes) |
| [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration) |
| [Microsoft.ManagedServices](#microsoftmanagedservices) |
| [Microsoft.Management](#microsoftmanagement) |
| [Microsoft.PolicyInsights](#microsoftpolicyinsights) |
| [Microsoft.Portal](#microsoftportal) |
| [Microsoft.RecoveryServices](#microsoftrecoveryservices) |
| [Microsoft.Resources](#microsoftresources) |
| [Microsoft.Scheduler](#microsoftscheduler) |
| [Microsoft.Solutions](#microsoftsolutions) |
| [Microsoft.Subscription](#microsoftsubscription) |
| **Intune** |
| [Microsoft.Intune](#microsoftintune) |
| **其他** |
| [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization) |
| [Microsoft.DigitalTwins](#microsoftdigitaltwins) |
| [Microsoft.ServicesHub](#microsoftserviceshub) |


## <a name="general"></a>常规

### <a name="microsoftaddons"></a>Microsoft.Addons

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Addons/register/action | 向 Microsoft.Addons 注册指定的订阅 |
> | Microsoft.Addons/operations/read | 获取受支持的 RP 操作。 |
> | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 列出指定的订阅的当前支持计划信息。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/read | 获取指定的 Canonical 支持计划状态。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/write | 添加指定的 Canonical 支持计划类型。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 删除指定的 Canonical 支持计划 |

### <a name="microsoftmarketplace"></a>Microsoft.Marketplace

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Marketplace/register/action | 注册订阅中的 Microsoft.Marketplace 资源提供程序。 |
> | Microsoft.Marketplace/privateStores/action | 更新 PrivateStore。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 返回一个协议。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 接受已签名的协议。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 返回配置。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 保存配置。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | 将映像导入到最终用户的 ACR。 |
> | Microsoft.Marketplace/privateStores/write | 创建 PrivateStore。 |
> | Microsoft.Marketplace/privateStores/delete | 删除 PrivateStore。 |
> | Microsoft.Marketplace/privateStores/offers/action | 更新 PrivateStore 中的套餐。 |
> | Microsoft.Marketplace/privateStores/read | 读取 PrivateStores。 |
> | Microsoft.Marketplace/privateStores/requestApprovals/action | 更新请求审批 |
> | Microsoft.Marketplace/privateStores/adminRequestApprovals/read | 读取所有请求审批详细信息，仅限管理员 |
> | Microsoft.Marketplace/privateStores/adminRequestApprovals/write | 管理员使用对请求做出的决定来更新请求 |
> | Microsoft.Marketplace/privateStores/offers/write | 在 PrivateStore 中创建套餐。 |
> | Microsoft.Marketplace/privateStores/offers/delete | 从 PrivateStore 中删除套餐。 |
> | Microsoft.Marketplace/privateStores/offers/read | 读取 PrivateStore 产品/服务。 |
> | Microsoft.Marketplace/privateStores/queryNotificationsState/read | 读取通知状态详细信息，仅限管理员 |
> | Microsoft.Marketplace/privateStores/requestApprovals/read | 读取请求审批 |
> | Microsoft.Marketplace/privateStores/requestApprovals/write | 创建请求审核 |
> | Microsoft.Marketplace/privateStores/RequestApprovals/offer/acknowledgeNotification/write | 确认通知，仅限管理员 |
> | Microsoft.Marketplace/privateStores/RequestApprovals/withdrawPlan/write | 从产品/服务的通知中撤回计划 |

### <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.MarketplaceApps/ClassicDevServices/read | 对经典开发人员服务执行 GET 操作。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/delete | 对经典开发人员服务资源执行 DELETE 操作。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | 获取经典开发人员服务的单一登录 URL。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | 获取经典开发人员服务资源管理密钥。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | 生成经典开发人员服务资源管理密钥。 |
> | Microsoft.MarketplaceApps/Operations/read | 读取所有资源类型的操作。 |

### <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.MarketplaceOrdering/agreements/read | 返回给定订阅下的所有协议 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 返回给定市场项的协议 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 为给定市场项的协议签名 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 取消给定市场项的协议 |
> | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 获取给定市场虚拟机项的协议 |
> | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 签订或取消给定市场虚拟机项的协议 |
> | Microsoft.MarketplaceOrdering/operations/read | 列出 API 中所有可能的操作 |

### <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

Azure 服务：[Azure 服务运行状况](../service-health/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ResourceHealth/register/action | 注册 Microsoft ResourceHealth 的订阅 |
> | Microsoft.ResourceHealth/unregister/action | 取消注册 Microsoft ResourceHealth 的订阅 |
> | Microsoft.Resourcehealth/healthevent/action | 表示指定资源的运行状况状态的更改 |
> | Microsoft.ResourceHealth/AvailabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 获取指定资源的可用性状态 |
> | Microsoft.ResourceHealth/emergingissues/read | 获取 Azure 服务新出现的问题 |
> | Microsoft.ResourceHealth/events/read | 获取给定订阅的服务运行状况事件 |
> | Microsoft.Resourcehealth/healthevent/Activated/action | 表示指定资源的运行状况状态的更改 |
> | Microsoft.Resourcehealth/healthevent/Updated/action | 表示指定资源的运行状况状态的更改 |
> | Microsoft.Resourcehealth/healthevent/Resolved/action | 表示指定资源的运行状况状态的更改 |
> | Microsoft.Resourcehealth/healthevent/InProgress/action | 表示指定资源的运行状况状态的更改 |
> | Microsoft.Resourcehealth/healthevent/Pending/action | 表示指定资源的运行状况状态的更改 |
> | Microsoft.ResourceHealth/impactedResources/read | 获取给定订阅中受影响的资源 |
> | Microsoft.ResourceHealth/metadata/read | 获取元数据 |
> | Microsoft.ResourceHealth/Notifications/read | 接收 Azure 资源管理器通知 |
> | Microsoft.ResourceHealth/Operations/read | 获取可用于 Microsoft ResourceHealth 的操作 |
> | Microsoft.ResourceHealth/potentialoutages/read |  |

### <a name="microsoftsupport"></a>Microsoft.Support

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Support/register/action | 注册 Support 资源提供程序 |
> | Microsoft.Support/lookUpResourceId/action | 查找资源类型的资源 ID |
> | Microsoft.Support/checkNameAvailability/action | 检查该名称是否有效且未用于资源类型 |
> | Microsoft.Support/operationresults/read | 获取异步操作的结果 |
> | Microsoft.Support/operations/read | 列出可对 Microsoft.Support 资源提供程序执行的所有操作 |
> | Microsoft.Support/operationsstatus/read | 获取异步操作的状态 |
> | Microsoft.Support/services/read | 列出可用于支持的一个或所有 Azure 服务 |
> | Microsoft.Support/services/problemClassifications/read | 列出 Azure 服务的一个或所有问题分类 |
> | Microsoft.Support/supportTickets/read | 列出一个或所有支持票证 |
> | Microsoft.Support/supportTickets/write | 允许创建和更新支持票证 |

## <a name="compute"></a>计算

### <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

Azure 服务：经典部署模型虚拟机

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ClassicCompute/register/action | 注册到经典计算 |
> | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 检查给定域名的可用性。 |
> | Microsoft.ClassicCompute/moveSubscriptionResources/action | 将所有经典资源移到不同的订阅。 |
> | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 验证订阅是否可用于经典移动操作。 |
> | Microsoft.ClassicCompute/capabilities/read | 显示功能 |
> | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 获取一个给定域名的可用性。 |
> | Microsoft.ClassicCompute/domainNames/read | 返回资源的域名。 |
> | Microsoft.ClassicCompute/domainNames/write | 添加或修改资源的域名。 |
> | Microsoft.ClassicCompute/domainNames/delete | 删除资源的域名。 |
> | Microsoft.ClassicCompute/domainNames/swap/action | 将过渡槽交换到生产槽。 |
> | Microsoft.ClassicCompute/domainNames/active/write | 设置活动域名。 |
> | Microsoft.ClassicCompute/domainNames/availabilitySets/read | 显示资源的可用性集。 |
> | Microsoft.ClassicCompute/domainNames/capabilities/read | 显示域名功能 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/read | 显示部署槽。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/write | 创建或更新部署。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | 获取域名的部署槽位上的角色 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | 获取域名的部署槽位上的角色的角色实例 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | 获取部署槽位状态。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | 添加部署槽位状态。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | 获取域名上的部署槽位的升级域 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | 更新域名上的部署槽位的升级域 |
> | Microsoft.ClassicCompute/domainNames/extensions/read | 返回域名扩展。 |
> | Microsoft.ClassicCompute/domainNames/extensions/write | 添加域名扩展。 |
> | Microsoft.ClassicCompute/domainNames/extensions/delete | 删除域名扩展。 |
> | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | 读取域名扩展的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 获取内部负载均衡器。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 创建新的内部负载均衡。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 删除新的内部负载均衡。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | 读取域名内部负载均衡器的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 获取负载均衡的终结点集。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 添加负载均衡的终结点集。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | 读取域名负载均衡的终结点集的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/operationstatuses/read | 获取域名的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/operationStatuses/read | 读取域名扩展的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 返回使用的服务证书。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 添加或修改使用的服务证书。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 删除使用的服务证书。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | 读取域名服务证书的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/slots/read | 显示部署槽。 |
> | Microsoft.ClassicCompute/domainNames/slots/write | 创建或更新部署。 |
> | Microsoft.ClassicCompute/domainNames/slots/delete | 删除给定的部署槽。 |
> | Microsoft.ClassicCompute/domainNames/slots/start/action | 启动部署槽。 |
> | Microsoft.ClassicCompute/domainNames/slots/stop/action | 暂停部署槽。 |
> | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | 验证部署槽位的迁移。 |
> | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | 准备部署槽位的迁移。 |
> | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | 提交部署槽位的迁移。 |
> | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | 中止部署槽位的迁移。 |
> | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | 读取域名槽的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/read | 获取部署槽的角色。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/write | 添加部署槽位的角色。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | 返回部署槽角色的扩展引用。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | 添加或修改部署槽角色的扩展引用。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | 删除部署槽角色的扩展引用。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | 读取域名槽角色扩展引用的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | 获取域名的角色指标定义。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | 获取域名的角色指标。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | 获取域名槽位角色的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | 下载域名槽位角色上角色实例的远程桌面连接文件。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | 获取角色实例。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | 重新启动角色实例。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | 重置角色实例的映像。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | 重新生成角色实例。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | 获取域名槽位角色上角色实例的操作状态。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | 获取部署槽位的角色 SKU。 |
> | Microsoft.ClassicCompute/domainNames/slots/state/start/write | 将部署槽状态更改为已停止。 |
> | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | 将部署槽状态更改为已启动。 |
> | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | 逐步升级域。 |
> | Microsoft.ClassicCompute/operatingSystemFamilies/read | 列出了 Microsoft Azure 中可用的来宾操作系统系列，还列出了每个系列可用的操作系统版本。 |
> | Microsoft.ClassicCompute/operatingSystems/read | 列出 Microsoft Azure 中当前可用的来宾操作系统版本。 |
> | Microsoft.ClassicCompute/operations/read | 获取操作列表。 |
> | Microsoft.ClassicCompute/operationStatuses/read | 读取资源的操作状态。 |
> | Microsoft.ClassicCompute/quotas/read | 获取订阅的配额。 |
> | Microsoft.ClassicCompute/resourceTypes/skus/read | 获取受支持资源类型的 SKU 列表。 |
> | Microsoft.ClassicCompute/virtualMachines/read | 检索虚拟机列表。 |
> | Microsoft.ClassicCompute/virtualMachines/write | 添加或修改虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/delete | 删除虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/capture/action | 捕获虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/start/action | 启动虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 重新部署虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 在虚拟机上执行维护。 |
> | Microsoft.ClassicCompute/virtualMachines/restart/action | 重新启动虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/stop/action | 停止虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 关闭虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 将数据磁盘附加到虚拟机。 |
> | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 从虚拟机中分离数据磁盘。 |
> | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 下载虚拟机的 RDP 文件。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 获取与虚拟机关联的网络安全组。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 添加与虚拟机关联的网络安全组。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 删除与虚拟机关联的网络安全组。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 读取与网络安全组关联的虚拟机的操作状态。 |
> | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 获取可能的异步操作 |
> | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 获取虚拟机诊断设置。 |
> | Microsoft.ClassicCompute/virtualMachines/disks/read | 检索数据磁盘的列表 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/read | 获取虚拟机扩展。 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/write | 放置虚拟机扩展。 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 读取虚拟机扩展的操作状态。 |
> | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 获取虚拟机指标定义。 |
> | Microsoft.ClassicCompute/virtualMachines/metrics/read | 获取指标。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | 获取与网络接口关联的网络安全组。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | 添加与网络接口关联的网络安全组。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | 删除与网络接口关联的网络安全组。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 读取与网络安全组关联的虚拟机的操作状态。 |
> | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 读取虚拟机的操作状态。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure 服务：[虚拟机](../virtual-machines/index.yml)、[虚拟机规模集](../virtual-machine-scale-sets/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Compute/register/action | 将订阅注册到 Microsoft.Compute 资源提供程序 |
> | Microsoft.Compute/unregister/action | 使用 Microsoft.Compute 资源提供程序取消注册订阅 |
> | Microsoft.Compute/availabilitySets/read | 获取可用性集的属性 |
> | Microsoft.Compute/availabilitySets/write | 创建新的可用性集，或更新现有的可用性集 |
> | Microsoft.Compute/availabilitySets/delete | 删除可用性集 |
> | Microsoft.Compute/availabilitySets/vmSizes/read | 列出可在可用性集中创建或更新的虚拟机大小 |
> | Microsoft.Compute/capacityReservationGroups/read | 获取产能预留组的属性 |
> | Microsoft.Compute/capacityReservationGroups/write | 创建新的产能预留组，或更新现有的产能预留组 |
> | Microsoft.Compute/capacityReservationGroups/delete | 删除产能预留组 |
> | Microsoft.Compute/capacityReservationGroups/capacityReservations/read | 获取产能预留的属性 |
> | Microsoft.Compute/capacityReservationGroups/capacityReservations/write | 创建新的产能预留，或更新现有的产能预留 |
> | Microsoft.Compute/capacityReservationGroups/capacityReservations/delete | 删除产能预留 |
> | Microsoft.Compute/cloudServices/read | 获取 CloudService 的属性。 |
> | Microsoft.Compute/cloudServices/write | 新建 CloudService 或更新现有的 CloudService。 |
> | Microsoft.Compute/cloudServices/delete | 删除 CloudService。 |
> | Microsoft.Compute/cloudServices/poweroff/action | 关闭 CloudService。 |
> | Microsoft.Compute/cloudServices/start/action | 启动 CloudService。 |
> | Microsoft.Compute/cloudServices/restart/action | 重启 CloudService 中的一个或多个角色实例。 |
> | Microsoft.Compute/cloudServices/reimage/action | 重新生成 CloudService 的角色实例中的所有磁盘。 |
> | Microsoft.Compute/cloudServices/rebuild/action | 为 CloudService 中的所有角色实例重置映像。 |
> | Microsoft.Compute/cloudServices/delete/action | 删除 CloudService 中的角色实例。 |
> | Microsoft.Compute/cloudServices/instanceView/read | 获取 CloudService 的状态。 |
> | Microsoft.Compute/cloudServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取 CloudService 的诊断设置。 |
> | Microsoft.Compute/cloudServices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 CloudService 的诊断设置。 |
> | Microsoft.Compute/cloudServices/providers/Microsoft.Insights/metricDefinitions/read | 获取 CloudService 指标定义 |
> | Microsoft.Compute/cloudServices/roleInstances/delete | 从 CloudService 中删除 RoleInstance。 |
> | Microsoft.Compute/cloudServices/roleInstances/read | 从 CloudService 中获取 RoleInstance。 |
> | Microsoft.Compute/cloudServices/roleInstances/restart/action | 重启 CloudService 的角色实例 |
> | Microsoft.Compute/cloudServices/roleInstances/reimage/action | 为 CloudService 的角色实例重置映像。 |
> | Microsoft.Compute/cloudServices/roleInstances/rebuild/action | 重新生成 CloudService 中的所有磁盘。 |
> | Microsoft.Compute/cloudServices/roleInstances/instanceView/read | 从 CloudService 中获取角色实例的状态。 |
> | Microsoft.Compute/cloudServices/roles/read | 从 CloudService 获取角色。 |
> | Microsoft.Compute/cloudServices/roles/write | 在角色中缩放实例 |
> | Microsoft.Compute/cloudServices/roles/providers/Microsoft.Insights/diagnosticSettings/read | 获取 CloudService 角色的诊断设置。 |
> | Microsoft.Compute/cloudServices/roles/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 CloudService 角色的诊断设置 |
> | Microsoft.Compute/cloudServices/roles/providers/Microsoft.Insights/metricDefinitions/read | 获取 CloudService 角色指标定义 |
> | Microsoft.Compute/cloudServices/updateDomains/read | 获取 CloudService 中所有更新域的列表。 |
> | Microsoft.Compute/diskAccesses/read | 获取 DiskAccess 资源的属性 |
> | Microsoft.Compute/diskAccesses/write | 创建新的 DiskAccess 资源或更新现有资源 |
> | Microsoft.Compute/diskAccesses/delete | 删除 DiskAccess 资源 |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionsApproval/action | 自动审批专用终结点连接 |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionProxies/read | 获取专用终结点连接代理的属性 |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionProxies/write | 创建新的专用终结点连接代理 |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理对象 |
> | Microsoft.Compute/diskAccesses/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Compute/diskAccesses/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.Compute/diskAccesses/privateEndpointConnections/write | 批准或拒绝专用终结点连接 |
> | Microsoft.Compute/diskEncryptionSets/read | 获取磁盘加密集的属性 |
> | Microsoft.Compute/diskEncryptionSets/write | 创建新的磁盘加密集，或更新现有的磁盘加密集 |
> | Microsoft.Compute/diskEncryptionSets/delete | 删除磁盘加密集 |
> | Microsoft.Compute/disks/read | 获取磁盘的属性 |
> | Microsoft.Compute/disks/write | 创建新的磁盘，或更新现有的磁盘 |
> | Microsoft.Compute/disks/delete | 删除磁盘 |
> | Microsoft.Compute/disks/beginGetAccess/action | 获取用于 Blob 访问的磁盘 SAS URI |
> | Microsoft.Compute/disks/endGetAccess/action | 吊销磁盘的 SAS URI |
> | Microsoft.Compute/galleries/read | 获取库的属性 |
> | Microsoft.Compute/galleries/write | 创建新的库或更新现有库 |
> | Microsoft.Compute/galleries/delete | 删除库 |
> | Microsoft.Compute/galleries/share/action | 将库共享到不同的范围 |
> | Microsoft.Compute/galleries/applications/read | 获取库应用程序的属性 |
> | Microsoft.Compute/galleries/applications/write | 创建新的库应用程序或更新现有库应用程序 |
> | Microsoft.Compute/galleries/applications/delete | 删除库应用程序 |
> | Microsoft.Compute/galleries/applications/versions/read | 获取库应用程序版本的属性 |
> | Microsoft.Compute/galleries/applications/versions/write | 创建新的库应用程序版本或更新现有版本 |
> | Microsoft.Compute/galleries/applications/versions/delete | 删除库应用程序版本 |
> | Microsoft.Compute/galleries/images/read | 获取库映像的属性 |
> | Microsoft.Compute/galleries/images/write | 创建新的库映像或更新现有库映像 |
> | Microsoft.Compute/galleries/images/delete | 删除库映像 |
> | Microsoft.Compute/galleries/images/versions/read | 获取库映像版本的属性 |
> | Microsoft.Compute/galleries/images/versions/write | 创建新的库映像版本或更新现有版本 |
> | Microsoft.Compute/galleries/images/versions/delete | 删除库映像版本 |
> | Microsoft.Compute/hostGroups/read | 获取主机组的属性 |
> | Microsoft.Compute/hostGroups/write | 创建新主机组或更新现有主机组 |
> | Microsoft.Compute/hostGroups/delete | 删除主机组 |
> | Microsoft.Compute/hostGroups/hosts/read | 获取主机的属性 |
> | Microsoft.Compute/hostGroups/hosts/write | 创建新的主机，或更新现有的主机 |
> | Microsoft.Compute/hostGroups/hosts/delete | 删除主机 |
> | Microsoft.Compute/images/read | 获取映像的属性 |
> | Microsoft.Compute/images/write | 创建新的映像，或更新现有的映像 |
> | Microsoft.Compute/images/delete | 删除映像 |
> | Microsoft.Compute/locations/capsOperations/read | 获取异步大写操作的状态 |
> | Microsoft.Compute/locations/cloudServiceOsFamilies/read | 读取可以在云服务的 XML 服务配置 (.cscfg) 中指定的任何来宾 OS 系列。 |
> | Microsoft.Compute/locations/cloudServiceOsVersions/read | 读取可以在云服务的 XML 服务配置 (.cscfg) 中指定的任何来宾 OS 版本。 |
> | Microsoft.Compute/locations/diagnosticOperations/read | 获取计算诊断操作的状态 |
> | Microsoft.Compute/locations/diagnostics/diskInspection/action | 创建用于执行 DiskInspection 诊断的请求 |
> | Microsoft.Compute/locations/diagnostics/read | 获取所有可用计算诊断的属性 |
> | Microsoft.Compute/locations/diagnostics/diskInspection/read | 获取 DiskInspection 诊断的属性 |
> | Microsoft.Compute/locations/diskOperations/read | 获取异步磁盘操作的状态 |
> | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 创建日志来按时间间隔显示请求总数，以帮助限制诊断。 |
> | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | 创建日志以显示按 ResourceName、OperationName 或应用的限制策略分组的限制请求数聚合。 |
> | Microsoft.Compute/locations/operations/read | 获取异步操作的状态 |
> | Microsoft.Compute/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | 获取专用终结点连接代理异步操作的状态 |
> | Microsoft.Compute/locations/privateEndpointConnectionProxyOperationResults/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.Compute/locations/publishers/read | 获取发布服务器的属性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | 获取平台映像产品/服务的属性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | 获取平台映像 SKU 的属性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | 获取平台映像版本的属性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/types/read | 获取 VMExtension 类型的属性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | 获取 VMExtension 版本的属性 |
> | Microsoft.Compute/locations/runCommands/read | 列出位置中的可用运行命令 |
> | Microsoft.Compute/locations/usages/read | 获取某个位置中订阅的计算资源的服务限制和当前用量 |
> | Microsoft.Compute/locations/vmSizes/read | 列出某个位置的可用虚拟机大小 |
> | Microsoft.Compute/locations/vsmOperations/read | 获取带有虚拟机运行时服务扩展的虚拟机规模集的异步操作的状态 |
> | Microsoft.Compute/operations/read | 列出可对 Microsoft.Compute 资源提供程序使用的操作 |
> | Microsoft.Compute/proximityPlacementGroups/read | 获取邻近放置组的属性 |
> | Microsoft.Compute/proximityPlacementGroups/write | 创建新的邻近放置组或更新现有的邻近放置组 |
> | Microsoft.Compute/proximityPlacementGroups/delete | 删除邻近放置组 |
> | Microsoft.Compute/restorePointCollections/read | 获取还原点集合的属性 |
> | Microsoft.Compute/restorePointCollections/write | 创建新的或更新现有的还原点集合 |
> | Microsoft.Compute/restorePointCollections/delete | 删除恢复点集合以及包含的还原点 |
> | Microsoft.Compute/restorePointCollections/restorePoints/read | 获取还原点的属性 |
> | Microsoft.Compute/restorePointCollections/restorePoints/write | 创建新的还原点 |
> | Microsoft.Compute/restorePointCollections/restorePoints/delete | 删除还原点 |
> | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | 获取还原点的属性以及 Blob SAS URI |
> | Microsoft.Compute/restorePointCollections/restorePoints/diskRestorePoints/read | 获取增量 DiskRestorePoint 的属性 |
> | Microsoft.Compute/restorePointCollections/restorePoints/diskRestorePoints/beginGetAccess/action | 获取增量 DiskRestorePoint 的 SAS URI |
> | Microsoft.Compute/restorePointCollections/restorePoints/diskRestorePoints/endGetAccess/action | 撤销增量 DiskRestorePoint 的 SAS URI |
> | Microsoft.Compute/sharedVMExtensions/read | 获取共享 VM 扩展的属性 |
> | Microsoft.Compute/sharedVMExtensions/write | 创建新的共享 VM 扩展或更新现有扩展 |
> | Microsoft.Compute/sharedVMExtensions/delete | 删除共享 VM 扩展 |
> | Microsoft.Compute/sharedVMExtensions/versions/read | 获取共享 VM 扩展版本的属性 |
> | Microsoft.Compute/sharedVMExtensions/versions/write | 创建新的共享 VM 扩展版本或更新现有版本 |
> | Microsoft.Compute/sharedVMExtensions/versions/delete | 删除共享 VM 扩展版本 |
> | Microsoft.Compute/sharedVMImages/read | 获取 SharedVMImage 的属性 |
> | Microsoft.Compute/sharedVMImages/write | 创建新的 SharedVMImage 或更新现有的 SharedVMImage |
> | Microsoft.Compute/sharedVMImages/delete | 删除 SharedVMImage |
> | Microsoft.Compute/sharedVMImages/versions/read | 获取 SharedVMImageVersion 的属性 |
> | Microsoft.Compute/sharedVMImages/versions/write | 创建新的 SharedVMImageVersion 或更新现有的 SharedVMImageVersion |
> | Microsoft.Compute/sharedVMImages/versions/delete | 删除 SharedVMImageVersion |
> | Microsoft.Compute/sharedVMImages/versions/replicate/action | 将 SharedVMImageVersion 复制到目标区域 |
> | Microsoft.Compute/skus/read | 获取订阅可用的 Microsoft.Compute SKU 列表 |
> | Microsoft.Compute/snapshots/read | 获取快照的属性 |
> | Microsoft.Compute/snapshots/write | 创建新的快照，或更新现有的快照 |
> | Microsoft.Compute/snapshots/delete | 删除快照 |
> | Microsoft.Compute/snapshots/beginGetAccess/action | 获取用于 blob 访问的快照 SAS URI |
> | Microsoft.Compute/snapshots/endGetAccess/action | 撤销快照的 SAS URI |
> | Microsoft.Compute/sshPublicKeys/read | 获取 SSH 公钥的属性 |
> | Microsoft.Compute/sshPublicKeys/write | 创建新的 SSH 公钥，或更新现有的 SSH 公钥 |
> | Microsoft.Compute/sshPublicKeys/delete | 删除 SSH 公钥 |
> | Microsoft.Compute/sshPublicKeys/generateKeyPair/action | 生成新的 SSH 公钥/私钥对 |
> | Microsoft.Compute/virtualMachines/read | 获取虚拟机的属性 |
> | Microsoft.Compute/virtualMachines/write | 创建新的虚拟机，或更新现有的虚拟机 |
> | Microsoft.Compute/virtualMachines/delete | 删除虚拟机 |
> | Microsoft.Compute/virtualMachines/start/action | 启动虚拟机 |
> | Microsoft.Compute/virtualMachines/powerOff/action | 关闭虚拟机。 请注意，该虚拟机会继续产生费用。 |
> | Microsoft.Compute/virtualMachines/reapply/action | 重新应用虚拟机的当前模型 |
> | Microsoft.Compute/virtualMachines/redeploy/action | 重新部署虚拟机 |
> | Microsoft.Compute/virtualMachines/restart/action | 重新启动虚拟机 |
> | Microsoft.Compute/virtualMachines/retrieveBootDiagnosticsData/action | 检索启动诊断日志 blob URI |
> | Microsoft.Compute/virtualMachines/deallocate/action | 关闭虚拟机并释放计算资源 |
> | Microsoft.Compute/virtualMachines/generalize/action | 将虚拟机状态设置为通用化，并准备要捕获的虚拟机 |
> | Microsoft.Compute/virtualMachines/capture/action | 通过复制虚拟硬盘捕获虚拟机，并生成可用于创建类似虚拟机的模板 |
> | Microsoft.Compute/virtualMachines/runCommand/action | 执行虚拟机上的预定义脚本 |
> | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 将虚拟机的基于 Blob 的磁盘转换为托管磁盘 |
> | Microsoft.Compute/virtualMachines/performMaintenance/action | 在 VM 上执行维护操作。 |
> | Microsoft.Compute/virtualMachines/reimage/action | 对使用差异磁盘的虚拟机重置映像。 |
> | Microsoft.Compute/virtualMachines/installPatches/action | 根据用户提供的参数在虚拟机上安装可用的 OS 更新修补程序。 在此过程中还会刷新包含可用修补程序列表的评估结果。 |
> | Microsoft.Compute/virtualMachines/assessPatches/action | 评估虚拟机并查找适用于它的 OS 更新修补程序列表。 |
> | Microsoft.Compute/virtualMachines/cancelPatchInstallation/action | 取消虚拟机上正在进行的安装 OS 更新修补程序的操作。 |
> | Microsoft.Compute/virtualMachines/simulateEviction/action | 模拟现成虚拟机的逐出 |
> | Microsoft.Compute/virtualMachines/extensions/read | 获取虚拟机扩展的属性 |
> | Microsoft.Compute/virtualMachines/extensions/write | 创建新的或更新现有的虚拟机扩展 |
> | Microsoft.Compute/virtualMachines/extensions/delete | 删除虚拟机扩展 |
> | Microsoft.Compute/virtualMachines/instanceView/read | 获取虚拟机的详细运行时状态及其资源 |
> | Microsoft.Compute/virtualMachines/patchAssessmentResults/latest/read | 检索最新补丁评估操作的摘要 |
> | Microsoft.Compute/virtualMachines/patchAssessmentResults/latest/softwarePatches/read | 检索上次补丁评估操作期间评估的补丁列表 |
> | Microsoft.Compute/virtualMachines/patchInstallationResults/read | 检索最新补丁安装操作的摘要 |
> | Microsoft.Compute/virtualMachines/patchInstallationResults/softwarePatches/read | 检索上次补丁安装操作期间尝试安装的补丁列表 |
> | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 获取虚拟机的诊断设置。 |
> | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新虚拟机的诊断设置。 |
> | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/logDefinitions/read | 获取虚拟机的可用日志。 |
> | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 读取虚拟机指标定义 |
> | Microsoft.Compute/virtualMachines/runCommands/read | 获取虚拟机运行命令的属性 |
> | Microsoft.Compute/virtualMachines/runCommands/write | 新建虚拟机运行命令或更新现有的命令 |
> | Microsoft.Compute/virtualMachines/runCommands/delete | 删除虚拟机运行命令 |
> | Microsoft.Compute/virtualMachines/vmSizes/read | 列出可将虚拟机更新到的大小 |
> | Microsoft.Compute/virtualMachineScaleSets/read | 获取虚拟机规模集的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/write | 创建新的或更新现有的虚拟机规模集 |
> | Microsoft.Compute/virtualMachineScaleSets/delete | 删除虚拟机规模集 |
> | Microsoft.Compute/virtualMachineScaleSets/delete/action | 删除虚拟机规模集的实例 |
> | Microsoft.Compute/virtualMachineScaleSets/start/action | 启动虚拟机规模集的实例 |
> | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 关闭虚拟机规模集的实例 |
> | Microsoft.Compute/virtualMachineScaleSets/restart/action | 重新启动虚拟机规模集的实例 |
> | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 关闭并释放虚拟机规模集实例的计算资源  |
> | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | 手动将实例更新到虚拟机规模集的最新模型 |
> | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 对虚拟机规模集的实例进行映像重置 |
> | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 重置虚拟机规模集实例的所有磁盘（OS 磁盘和数据磁盘）的映像  |
> | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 重新部署虚拟机规模集的实例 |
> | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 在虚拟机规模集的实例上执行计划内维护 |
> | Microsoft.Compute/virtualMachineScaleSets/scale/action | 验证现有虚拟机规模集是否可以横向缩减/扩展到指定的实例计数 |
> | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | 手动浏览服务结构虚拟机规模集的平台更新域，完成卡住的挂起更新 |
> | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | 启动滚动升级，将所有虚拟机规模集实例移动到最新可用的平台映像 OS 版本。 |
> | Microsoft.Compute/virtualMachineScaleSets/setOrchestrationServiceState/action | 根据操作输入中提供的操作来设置编排服务的状态。 |
> | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/action | 取消虚拟机规模集的滚动升级 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 获取虚拟机规模集扩展的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 创建新的虚拟机规模集扩展或更新现有虚拟机规模集扩展 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 删除虚拟机规模集扩展 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/roles/read | 获取具有虚拟机运行时服务扩展的虚拟机规模集中角色的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/roles/write | 更新具有虚拟机运行时服务扩展的虚拟机规模集中现有角色的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 获取虚拟机规模集的实例视图 |
> | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 获取虚拟机规模集的所有网络接口的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 获取虚拟机规模集的 OS 升级历史记录 |
> | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/diagnosticSettings/read | 获取虚拟机规模集的诊断设置。 |
> | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新虚拟机规模集的诊断设置。 |
> | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/logDefinitions/read | 获取虚拟机规模集的可用日志。 |
> | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | 读取虚拟机规模集指标定义 |
> | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 获取虚拟机规模集的所有公共 IP 地址的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 获取虚拟机规模集的最新滚动升级状态 |
> | Microsoft.Compute/virtualMachineScaleSets/skus/read | 列出现有虚拟机规模集的有效 SKU |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | 检索 VM 规模集中虚拟机的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | 更新 VM 规模集中虚拟机的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | 删除 VM 规模集中的特定虚拟机。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | 启动 VM 规模集中的虚拟机实例。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | 关闭 VM 规模集中的虚拟机实例。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | 重启 VM 规模集中的虚拟机实例。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | 关闭并释放 VM 规模集中虚拟机的计算资源。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 对虚拟机规模集中的虚拟机实例进行映像重置。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 重置虚拟机规模集中虚拟机实例的所有磁盘（OS 磁盘和数据磁盘）的映像。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 重新部署虚拟机规模集中的虚拟机实例 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/retrieveBootDiagnosticsData/action | 检索虚拟机规模集中虚拟机实例的启动诊断日志 blob URI |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 在虚拟机规模集的虚拟机实例上执行计划内维护 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 在虚拟机规模集的虚拟机实例上执行预定义的脚本。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/simulateEviction/action | 在虚拟机规模集中模拟现成虚拟机的逐出 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/extensions/read | 获取虚拟机规模集中的虚拟机扩展属性 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/extensions/write | 在虚拟机规模集中新建虚拟机扩展或更新现有扩展 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/extensions/delete | 删除虚拟机规模集中的虚拟机扩展 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | 检索 VM 规模集中虚拟机的实例视图。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 获取使用虚拟机规模集创建的虚拟机的一个或所有网络接口的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 获取使用虚拟机规模集创建的网络接口的一个或所有 IP 配置的属性。 IP 配置表示专用 IP |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 获取使用虚拟机规模集创建的公共 IP 地址的属性。 虚拟机规模集可为每个 ipconfiguration（专用 IP）最多创建一个公用 IP |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 读取虚拟机的规模集指标定义 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommands/read | 获取虚拟机规模集中虚拟机的运行命令的属性 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommands/write | 新建虚拟机规模集中虚拟机的运行命令或更新现有的命令 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommands/delete | 删除虚拟机规模集中虚拟机的运行命令 |
> | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | 列出可在虚拟机规模集中创建或更新的虚拟机大小 |
> | **DataAction** | **说明** |
> | Microsoft.Compute/virtualMachines/login/action | 以普通用户身份登录虚拟机 |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 以 Windows 管理员身份或 Linux 根用户权限登录虚拟机 |

### <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

Azure 服务：[Service Fabric](../service-fabric/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ServiceFabric/register/action | 注册任何操作 |
> | Microsoft.ServiceFabric/clusters/read | 读取任何群集 |
> | Microsoft.ServiceFabric/clusters/write | 创建或更新任何群集 |
> | Microsoft.ServiceFabric/clusters/delete | 删除任何群集 |
> | Microsoft.ServiceFabric/clusters/applications/read | 读取任何应用程序 |
> | Microsoft.ServiceFabric/clusters/applications/write | 创建或更新任何应用程序 |
> | Microsoft.ServiceFabric/clusters/applications/delete | 删除任何应用程序 |
> | Microsoft.ServiceFabric/clusters/applications/services/read | 读取任何服务 |
> | Microsoft.ServiceFabric/clusters/applications/services/write | 创建或更新任何服务 |
> | Microsoft.ServiceFabric/clusters/applications/services/delete | 删除任何服务 |
> | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | 读取任何分区 |
> | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | 读取任何副本 |
> | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | 读取任何服务状态 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/read | 读取任何应用程序类型 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/write | 创建或更新任何应用程序类型 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/delete | 删除任何应用程序类型 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | 读取任何应用程序类型版本 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | 创建或更新任何应用程序类型版本 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | 删除任何应用程序类型版本 |
> | Microsoft.ServiceFabric/clusters/nodes/read | 读取任何节点 |
> | Microsoft.ServiceFabric/clusters/statuses/read | 读取任何群集状态 |
> | Microsoft.ServiceFabric/locations/clusterVersions/read | 读取任何群集版本 |
> | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 读取特定环境的任何群集版本 |
> | Microsoft.ServiceFabric/locations/operationresults/read | 读取任何操作结果 |
> | Microsoft.ServiceFabric/locations/operations/read | 按位置读取任何操作 |
> | Microsoft.ServiceFabric/operations/read | 读取任何可用操作 |

## <a name="networking"></a>网络

### <a name="microsoftcdn"></a>Microsoft.Cdn

Azure 服务：[内容分发网络](../cdn/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Cdn/register/action | 注册 CDN 资源提供程序的订阅，并启用 CDN 配置文件的创建。 |
> | Microsoft.Cdn/unregister/action | 取消注册 CDN 资源提供程序的订阅。 |
> | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Microsoft.Cdn/ValidateProbe/action |  |
> | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/read |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/write |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/delete |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/read |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/write |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/delete |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/providers/Microsoft.Insights/logDefinitions/read | 获取 Microsoft.Cdn/cdnwebapplicationfirewallpolicies 的可用日志 |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft.Cdn 的可用指标 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/edgenodes/write |  |
> | Microsoft.Cdn/edgenodes/delete |  |
> | Microsoft.Cdn/operationresults/read |  |
> | Microsoft.Cdn/operationresults/write |  |
> | Microsoft.Cdn/operationresults/delete |  |
> | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/read |  |
> | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/write |  |
> | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Microsoft.Cdn/operationresults/profileresults/CheckHostNameAvailability/action |  |
> | Microsoft.Cdn/operationresults/profileresults/Usages/action |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/Purge/action |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/Usages/action |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/routeresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/routeresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/routeresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/customdomainresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/customdomainresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/customdomainresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/customdomainresults/RefreshValidationToken/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/origingroupresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/origingroupresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/origingroupresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/Usages/action |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/originresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/originresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/originresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/Usages/action |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/ruleresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/ruleresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/ruleresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/secretresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/secretresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/secretresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/secretresults/ValidateSecret/action |  |
> | Microsoft.Cdn/operationresults/profileresults/securitypolicyresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/securitypolicyresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/securitypolicyresults/delete |  |
> | Microsoft.Cdn/operations/read |  |
> | Microsoft.Cdn/profiles/read |  |
> | Microsoft.Cdn/profiles/write |  |
> | Microsoft.Cdn/profiles/delete |  |
> | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Microsoft.Cdn/profiles/CheckHostNameAvailability/action |  |
> | Microsoft.Cdn/profiles/Usages/action |  |
> | Microsoft.Cdn/profiles/afdendpoints/read |  |
> | Microsoft.Cdn/profiles/afdendpoints/write |  |
> | Microsoft.Cdn/profiles/afdendpoints/delete |  |
> | Microsoft.Cdn/profiles/afdendpoints/Purge/action |  |
> | Microsoft.Cdn/profiles/afdendpoints/Usages/action |  |
> | Microsoft.Cdn/profiles/afdendpoints/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/profiles/afdendpoints/routes/read |  |
> | Microsoft.Cdn/profiles/afdendpoints/routes/write |  |
> | Microsoft.Cdn/profiles/afdendpoints/routes/delete |  |
> | Microsoft.Cdn/profiles/customdomains/read |  |
> | Microsoft.Cdn/profiles/customdomains/write |  |
> | Microsoft.Cdn/profiles/customdomains/delete |  |
> | Microsoft.Cdn/profiles/customdomains/RefreshValidationToken/action |  |
> | Microsoft.Cdn/profiles/endpoints/read |  |
> | Microsoft.Cdn/profiles/endpoints/write |  |
> | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Microsoft.Cdn/profiles/endpoints/origingroups/read |  |
> | Microsoft.Cdn/profiles/endpoints/origingroups/write |  |
> | Microsoft.Cdn/profiles/endpoints/origingroups/delete |  |
> | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | 获取 Microsoft.Cdn/profiles/endpoints 的可用日志 |
> | Microsoft.Cdn/profiles/getloganalyticslocations/read |  |
> | Microsoft.Cdn/profiles/getloganalyticsmetrics/read |  |
> | Microsoft.Cdn/profiles/getloganalyticsrankings/read |  |
> | Microsoft.Cdn/profiles/getloganalyticsresources/read |  |
> | Microsoft.Cdn/profiles/getwafloganalyticsmetrics/read |  |
> | Microsoft.Cdn/profiles/getwafloganalyticsrankings/read |  |
> | Microsoft.Cdn/profiles/origingroups/read |  |
> | Microsoft.Cdn/profiles/origingroups/write |  |
> | Microsoft.Cdn/profiles/origingroups/delete |  |
> | Microsoft.Cdn/profiles/origingroups/Usages/action |  |
> | Microsoft.Cdn/profiles/origingroups/origins/read |  |
> | Microsoft.Cdn/profiles/origingroups/origins/write |  |
> | Microsoft.Cdn/profiles/origingroups/origins/delete |  |
> | Microsoft.Cdn/profiles/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Cdn/profiles/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Cdn/profiles/providers/Microsoft.Insights/logDefinitions/read | 获取 Microsoft.Cdn/profiles 的可用日志 |
> | Microsoft.Cdn/profiles/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft.Cdn 的可用指标 |
> | Microsoft.Cdn/profiles/rulesets/read |  |
> | Microsoft.Cdn/profiles/rulesets/write |  |
> | Microsoft.Cdn/profiles/rulesets/delete |  |
> | Microsoft.Cdn/profiles/rulesets/Usages/action |  |
> | Microsoft.Cdn/profiles/rulesets/rules/read |  |
> | Microsoft.Cdn/profiles/rulesets/rules/write |  |
> | Microsoft.Cdn/profiles/rulesets/rules/delete |  |
> | Microsoft.Cdn/profiles/secrets/read |  |
> | Microsoft.Cdn/profiles/secrets/write |  |
> | Microsoft.Cdn/profiles/secrets/delete |  |
> | Microsoft.Cdn/profiles/secrets/ValidateSecret/action |  |
> | Microsoft.Cdn/profiles/securitypolicies/read |  |
> | Microsoft.Cdn/profiles/securitypolicies/write |  |
> | Microsoft.Cdn/profiles/securitypolicies/delete |  |

### <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

Azure 服务：经典部署模型虚拟网络

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ClassicNetwork/register/action | 注册到经典网络 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/read | 获取快速路由交叉连接。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/write | 添加快速路由交叉连接。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | 获取快速路由交叉连接操作状态。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | 获取快速路由交叉连接对等互连。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | 添加快速路由交叉连接对等互连。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | 删除快速路由交叉连接对等互连。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | 获取快速路由交叉连接对等互连操作状态。 |
> | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | 检索受支持设备的列表。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/read | 获取网络安全组。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/write | 添加新的网络安全组。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/delete | 删除网络安全组。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | 读取网络安全组的操作状态。 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 获取网络安全组诊断设置 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新网络安全组诊断设置，此操作由见解资源提供程序进行补充。 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 获取网络安全组的事件 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | 获取安全规则。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | 添加或更新安全规则。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | 删除安全规则。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | 读取网络安全组安全规则的操作状态。 |
> | Microsoft.ClassicNetwork/operations/read | 获取经典网络操作。 |
> | Microsoft.ClassicNetwork/quotas/read | 获取订阅的配额。 |
> | Microsoft.ClassicNetwork/reservedIps/read | 获取保留 IP |
> | Microsoft.ClassicNetwork/reservedIps/write | 添加新的保留 IP |
> | Microsoft.ClassicNetwork/reservedIps/delete | 删除保留 IP。 |
> | Microsoft.ClassicNetwork/reservedIps/link/action | 链接保留 IP |
> | Microsoft.ClassicNetwork/reservedIps/join/action | 联接保留 IP |
> | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 读取保留 IP 的操作状态。 |
> | Microsoft.ClassicNetwork/virtualNetworks/read | 获取虚拟网络。 |
> | Microsoft.ClassicNetwork/virtualNetworks/write | 添加新的虚拟网络。 |
> | Microsoft.ClassicNetwork/virtualNetworks/delete | 删除虚拟网络。 |
> | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 在两个不同的虚拟网络之间建立对等互连。 |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虚拟网络。 |
> | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 检查虚拟网络中给定 IP 地址的可用性。 |
> | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | 验证虚拟网络的迁移 |
> | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | 准备虚拟网络的迁移 |
> | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | 提交虚拟网络的迁移 |
> | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | 中止虚拟网络的迁移 |
> | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 显示功能 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 获取虚拟网关。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 添加虚拟网关。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 删除虚拟网关。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 启动虚拟网关的诊断。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 停止虚拟网关的诊断。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | 下载网关诊断。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 检索线路服务密钥。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | 下载设备配置脚本。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 列出虚拟网关包。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 读取已吊销的客户端证书。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | 吊销客户端证书。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | 取消吊销客户端证书。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | 查找客户端根证书。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 上传新的客户端根证书。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 删除虚拟网关客户端证书。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 按指纹下载证书。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 列出虚拟网关证书包。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 检索连接列表。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | 建立站点到站点网关连接。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | 断开站点到站点网关连接。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | 测试站点到站点网关连接。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 读取虚拟网关的操作状态。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 获取虚拟网关包。 |
> | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 读取虚拟网络的操作状态。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 获取远程虚拟网络对等互连代理。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 添加或更新远程虚拟网络对等互连代理。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 删除远程虚拟网络对等互连代理。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | 获取与子网关联的网络安全组。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | 添加与子网关联的网络安全组。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | 删除与子网关联的网络安全组。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 读取与网络安全组关联的虚拟网络子网的操作状态。 |
> | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 获取虚拟网络对等互连。 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Azure 服务：[应用程序网关](../application-gateway/index.yml)、[Azure Bastion](../bastion/index.yml)、[Azure DDoS 防护](../ddos-protection/ddos-protection-overview.md)、[Azure DNS](../dns/index.yml)、[Azure ExpressRoute](../expressroute/index.yml)、[Azure 防火墙](../firewall/index.yml)、[Azure Front Door 服务](../frontdoor/index.yml)、[Azure 专用链接](../private-link/index.yml)、[负载均衡器](../load-balancer/index.yml)、[网络观察程序](../network-watcher/index.yml)、[流量管理器](../traffic-manager/index.yml)、[虚拟网络](../virtual-network/index.yml)、[虚拟 WAN](../virtual-wan/index.yml)、[VPN 网关](../vpn-gateway/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Network/register/action | 注册订阅 |
> | Microsoft.Network/unregister/action | 取消注册订阅 |
> | Microsoft.Network/checkTrafficManagerNameAvailability/action | 检查流量管理器相对 DNS 名称的可用性。 |
> | Microsoft.Network/internalNotify/action | DNS 别名资源通知 |
> | Microsoft.Network/getDnsResourceReference/action | DNS 别名资源依赖项请求 |
> | Microsoft.Network/checkFrontDoorNameAvailability/action | 检查 Front Door 名称是否可用 |
> | Microsoft.Network/privateDnsZonesInternal/action | 执行专用 DNS 区域内部 API |
> | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | 获取应用程序网关的可用请求标头 |
> | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | 获取应用程序网关的可用响应标头 |
> | Microsoft.Network/applicationGatewayAvailableServerVariables/read | 获取应用程序网关的可用服务器变量 |
> | Microsoft.Network/applicationGatewayAvailableSslOptions/read | 应用程序网关可用 SSL 选项 |
> | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | 应用程序网关 SSL 预定义策略 |
> | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | 获取应用程序网关可用的 WAF 规则集 |
> | Microsoft.Network/applicationGateways/read | 获取应用程序网关 |
> | Microsoft.Network/applicationGateways/write | 创建应用程序网关，或更新应用程序网关 |
> | Microsoft.Network/applicationGateways/delete | 删除应用程序网关 |
> | Microsoft.Network/applicationGateways/backendhealth/action | 获取应用程序网关后端运行状况 |
> | Microsoft.Network/applicationGateways/getBackendHealthOnDemand/action | 按需获取给定 http 设置和后端池的应用程序网关后端运行状况 |
> | Microsoft.Network/applicationGateways/resolvePrivateLinkServiceId/action | 解析应用程序网关专用链接资源的 privateLinkServiceId |
> | Microsoft.Network/applicationGateways/start/action | 启动应用程序网关 |
> | Microsoft.Network/applicationGateways/stop/action | 停止应用程序网关 |
> | Microsoft.Network/applicationGateways/restart/action | 重启应用程序网关 |
> | Microsoft.Network/applicationGateways/migrateV1ToV2/action | 将应用程序网关从 v1 sku 迁移到 v2 sku |
> | Microsoft.Network/applicationGateways/getMigrationStatus/action | 获取将应用程序网关从 V1 sku 迁移到 V2 sku 的状态 |
> | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | 设置应用程序网关安全中心配置 |
> | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | 获取应用程序网关上配置的路由表 |
> | Microsoft.Network/applicationGateways/effectiveRouteTable/action | 获取应用程序网关上配置的路由表 |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入应用程序网关后端地址池。 不可发出警报。 |
> | Microsoft.Network/applicationGateways/privateEndpointConnections/read | 获取应用程序网关专用终结点连接 |
> | Microsoft.Network/applicationGateways/privateEndpointConnections/write | 更新应用程序网关专用终结点连接 |
> | Microsoft.Network/applicationGateways/privateEndpointConnections/delete | 删除应用程序网关专用终结点连接 |
> | Microsoft.Network/applicationGateways/privateLinkConfigurations/read | 获取应用程序网关专用链接配置 |
> | Microsoft.Network/applicationGateways/privateLinkResources/read | 获取应用程序网关专用链接资源 |
> | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | 获取应用程序网关的事件 |
> | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | 获取应用程序网关的可用指标 |
> | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | 获取应用程序网关 WAF 策略 |
> | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | 创建应用程序网关 WAF 策略，或更新应用程序网关 WAF 策略 |
> | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | 删除应用程序网关 WAF 策略 |
> | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | 将 IP 配置加入到应用程序安全组中。 不可发出警报。 |
> | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 将安全规则加入到应用程序安全组中。 不可发出警报。 |
> | Microsoft.Network/applicationSecurityGroups/read | 获取应用程序安全组 ID。 |
> | Microsoft.Network/applicationSecurityGroups/write | 创建应用程序安全组，或更新现有应用程序安全组。 |
> | Microsoft.Network/applicationSecurityGroups/delete | 删除应用程序安全组 |
> | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | 列出 ApplicationSecurityGroup 中的 IP 配置 |
> | Microsoft.Network/azureFirewallFqdnTags/read | 获取 Azure 防火墙 FQDN 标记 |
> | Microsoft.Network/azurefirewalls/read | 获取 Azure 防火墙 |
> | Microsoft.Network/azurefirewalls/write | 创建或更新 Azure 防火墙 |
> | Microsoft.Network/azurefirewalls/delete | 删除 Azure 防火墙 |
> | Microsoft.Network/azureFirewalls/applicationRuleCollections/read | 获取 Azure 防火墙 ApplicationRuleCollection |
> | Microsoft.Network/azureFirewalls/applicationRuleCollections/write | 创建或更新 Azure 防火墙 ApplicationRuleCollection |
> | Microsoft.Network/azureFirewalls/applicationRuleCollections/delete | 删除 Azure 防火墙 ApplicationRuleCollection |
> | Microsoft.Network/azureFirewalls/natRuleCollections/read | 获取 Azure 防火墙 NatRuleCollection |
> | Microsoft.Network/azureFirewalls/natRuleCollections/write | 创建或更新 Azure 防火墙 NatRuleCollection |
> | Microsoft.Network/azureFirewalls/natRuleCollections/delete | 删除 Azure 防火墙 NatRuleCollection |
> | Microsoft.Network/azureFirewalls/networkRuleCollections/read | 获取 Azure 防火墙 NetworkRuleCollection |
> | Microsoft.Network/azureFirewalls/networkRuleCollections/write | 创建或更新 Azure 防火墙 NetworkRuleCollection |
> | Microsoft.Network/azureFirewalls/networkRuleCollections/delete | 删除 Azure 防火墙 NetworkRuleCollection |
> | Microsoft.Network/azurefirewalls/providers/Microsoft.Insights/logDefinitions/read | 获取 Azure 防火墙的事件 |
> | Microsoft.Network/azurefirewalls/providers/Microsoft.Insights/metricDefinitions/read | 获取 Azure 防火墙的可用指标 |
> | Microsoft.Network/azureWebCategories/read | 获取 Azure WebCategories |
> | Microsoft.Network/bastionHosts/read | 获取守护主机 |
> | Microsoft.Network/bastionHosts/write | 创建或更新守护主机 |
> | Microsoft.Network/bastionHosts/delete | 删除守护主机 |
> | Microsoft.Network/bastionHosts/getactivesessions/action | 获取堡垒主机中的活动会话 |
> | Microsoft.Network/bastionHosts/disconnectactivesessions/action | 断开连接堡垒主机中给定的活动会话 |
> | Microsoft.Network/bastionHosts/getShareableLinks/action | 返回堡垒子网中指定 VM 的可共享 URL（如果已创建其 URL） |
> | Microsoft.Network/bastionHosts/createShareableLinks/action | 为堡垒节点下的 VM 创建可共享的 URL 并返回 URL |
> | Microsoft.Network/bastionHosts/deleteShareableLinks/action | 删除堡垒节点下提供的 VM 的可共享 URL |
> | Microsoft.Network/bastionHosts/deleteShareableLinksByToken/action | 删除堡垒下提供的令牌的可共享 URL |
> | Microsoft.Network/bastionHosts/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Network/bastionHosts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Network/bastionHosts/providers/Microsoft.Insights/logDefinitions/read | 获取堡垒主机的可用审核日志 |
> | Microsoft.Network/bgpServiceCommunities/read | 获取 BGP 服务社区 |
> | Microsoft.Network/connections/read | 获取 VirtualNetworkGatewayConnection |
> | Microsoft.Network/connections/write | 创建新的或更新现有的 VirtualNetworkGatewayConnection |
> | Microsoft.Network/connections/delete | 删除 VirtualNetworkGatewayConnection |
> | Microsoft.Network/connections/sharedkey/action | 获取 VirtualNetworkGatewayConnection SharedKey |
> | Microsoft.Network/connections/vpndeviceconfigurationscript/action | 获取 VirtualNetworkGatewayConnection 的 VPN 设备配置 |
> | Microsoft.Network/connections/revoke/action | 将 Express Route 连接状态标记为“已撤销” |
> | Microsoft.Network/connections/startpacketcapture/action | 启动虚拟网络网关连接数据包捕获。 |
> | Microsoft.Network/connections/stoppacketcapture/action | 停止虚拟网络网关连接数据包捕获。 |
> | Microsoft.Network/connections/getikesas/action | 列出连接的 IKE 安全性关联 |
> | Microsoft.Network/connections/resetconnection/action | 重置 VNG 的连接 |
> | Microsoft.Network/connections/providers/Microsoft.Insights/diagnosticSettings/read | 获取连接的诊断设置 |
> | Microsoft.Network/connections/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新连接的诊断设置 |
> | Microsoft.Network/connections/providers/Microsoft.Insights/metricDefinitions/read | 获取连接的指标定义 |
> | Microsoft.Network/connections/sharedKey/read | 获取 VirtualNetworkGatewayConnection SharedKey |
> | Microsoft.Network/connections/sharedKey/write | 创建新的或更新现有的 VirtualNetworkGatewayConnection SharedKey |
> | Microsoft.Network/customIpPrefixes/read | 获取公共 IP 前缀定义 |
> | Microsoft.Network/customIpPrefixes/write | 创建自定义 IP 前缀或更新现有自定义 IP 前缀 |
> | Microsoft.Network/customIpPrefixes/delete | 删除自定义 PI 前缀 |
> | Microsoft.Network/ddosCustomPolicies/read | 获取 DDoS 自定义策略定义 |
> | Microsoft.Network/ddosCustomPolicies/write | 创建 DDoS 自定义策略，或更新现有的 DDoS 自定义策略 |
> | Microsoft.Network/ddosCustomPolicies/delete | 删除 DDoS 自定义策略 |
> | Microsoft.Network/ddosProtectionPlans/read | 获取 DDoS 保护计划 |
> | Microsoft.Network/ddosProtectionPlans/write | 创建 DDoS 保护计划或更新 DDoS 保护计划  |
> | Microsoft.Network/ddosProtectionPlans/delete | 删除 DDoS 保护计划 |
> | Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保护计划。 不可发出警报。 |
> | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | 获取 DDoS 保护计划代理定义 |
> | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | 创建 DDoS 保护计划代理或更新现有 DDoS 保护计划代理 |
> | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | 删除 DDoS 保护计划代理 |
> | Microsoft.Network/dnsoperationresults/read | 获取 DNS 操作的结果 |
> | Microsoft.Network/dnsoperationstatuses/read | 获取 DNS 操作的状态  |
> | Microsoft.Network/dnsResolvers/read | 获取 JSON 格式的 DNS 解析程序属性 |
> | Microsoft.Network/dnsResolvers/write | 创建或更新 JSON 格式的 DNS 解析程序 |
> | Microsoft.Network/dnsResolvers/delete | 删除 DNS 解析程序 |
> | Microsoft.Network/dnsResolvers/inboundEndpoints/read | 获取 JSON 格式的 DNS 解析程序入站终结点 |
> | Microsoft.Network/dnsResolvers/inboundEndpoints/write | 创建或更新 JSON 格式的 DNS 解析程序入站终结点 |
> | Microsoft.Network/dnsResolvers/inboundEndpoints/delete | 删除 JSON 格式的 DNS 解析程序入站终结点 |
> | Microsoft.Network/dnszones/read | 获取 JSON 格式的 DNS 区域。 区域属性包括 tags、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 请注意，此命令不会检索区域中包含的记录集。 |
> | Microsoft.Network/dnszones/write | 在资源组中创建或更新 DNS 区域。  用于更新 DNS 区域资源上的标记。 请注意，无法使用此命令在区域中创建或更新记录集。 |
> | Microsoft.Network/dnszones/delete | 删除 JSON 格式的 DNS 区域。 区域属性包括 tags、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 |
> | Microsoft.Network/dnszones/A/read | 获取 JSON 格式的“A”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/dnszones/A/write | 在 DNS 区域中创建或更新“A”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/dnszones/A/delete | 从 DNS 区域中删除具有给定名称的“A”类型的记录集。 |
> | Microsoft.Network/dnszones/AAAA/read | 获取 JSON 格式的“AAAA”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/dnszones/AAAA/write | 在 DNS 区域中创建或更新“AAAA”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/dnszones/AAAA/delete | 从 DNS 区域中删除具有给定名称的“AAAA”类型的记录集。 |
> | Microsoft.Network/dnszones/all/read | 获取各种类型的 DNS 记录集 |
> | Microsoft.Network/dnszones/CAA/read | 获取 JSON 格式的“CAA”类型的记录集。 记录集包含 TTL、标记和 etag。 |
> | Microsoft.Network/dnszones/CAA/write | 在 DNS 区域中创建或更新“CAA”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/dnszones/CAA/delete | 从 DNS 区域中删除具有给定名称的“CAA”类型的记录集。 |
> | Microsoft.Network/dnszones/CNAME/read | 获取 JSON 格式的“CNAME”类型的记录集。 记录集包含 TTL、标记和 etag。 |
> | Microsoft.Network/dnszones/CNAME/write | 在 DNS 区域中创建或更新“CNAME”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/dnszones/CNAME/delete | 从 DNS 区域中删除具有给定名称的“CNAME”类型的记录集。 |
> | Microsoft.Network/dnszones/MX/read | 获取 JSON 格式的“MX”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/dnszones/MX/write | 在 DNS 区域中创建或更新“MX”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/dnszones/MX/delete | 从 DNS 区域中删除具有给定名称的“MX”类型的记录集。 |
> | Microsoft.Network/dnszones/NS/read | 获取 NS 类型的 DNS 记录集 |
> | Microsoft.Network/dnszones/NS/write | 创建或更新 NS 类型的 DNS 记录集 |
> | Microsoft.Network/dnszones/NS/delete | 删除 NS 类型的 DNS 记录集 |
> | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | 获取 DNS 区域诊断设置 |
> | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 DNS 区域诊断设置 |
> | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | 获取 DNS 区域指标定义 |
> | Microsoft.Network/dnszones/PTR/read | 获取 JSON 格式的“PTR”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/dnszones/PTR/write | 在 DNS 区域中创建或更新“PTR”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/dnszones/PTR/delete | 从 DNS 区域中删除具有给定名称的“PTR”类型的记录集。 |
> | Microsoft.Network/dnszones/recordsets/read | 获取各种类型的 DNS 记录集 |
> | Microsoft.Network/dnszones/SOA/read | 获取 SOA 类型的 DNS 记录集 |
> | Microsoft.Network/dnszones/SOA/write | 创建或更新 SOA 类型的 DNS 记录集 |
> | Microsoft.Network/dnszones/SRV/read | 获取 JSON 格式的“SRV”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/dnszones/SRV/write | 创建或更新 SRV 类型的记录集 |
> | Microsoft.Network/dnszones/SRV/delete | 从 DNS 区域中删除具有给定名称的“SRV”类型的记录集。 |
> | Microsoft.Network/dnszones/TXT/read | 获取 JSON 格式的“TXT”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/dnszones/TXT/write | 在 DNS 区域中创建或更新“TXT”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/dnszones/TXT/delete | 从 DNS 区域中删除具有给定名称的“TXT”类型的记录集。 |
> | Microsoft.Network/dscpConfiguration/write | 用于放置 DSCP 配置的操作 |
> | Microsoft.Network/dscpConfiguration/read | 用于放置 DSCP 配置的操作 |
> | Microsoft.Network/dscpConfiguration/join/action | 加入 DSCP 配置 |
> | Microsoft.Network/expressRouteCircuits/read | 获取 ExpressRouteCircuit |
> | Microsoft.Network/expressRouteCircuits/write | 创建新的或更新现有的 ExpressRouteCircuit |
> | Microsoft.Network/expressRouteCircuits/join/action | 加入 Express Route 线路。 不可发出警报。 |
> | Microsoft.Network/expressRouteCircuits/delete | 删除 ExpressRouteCircuit |
> | Microsoft.Network/expressRouteCircuits/authorizations/read | 获取 ExpressRouteCircuit 授权 |
> | Microsoft.Network/expressRouteCircuits/authorizations/write | 创建新的或更新现有的 ExpressRouteCircuit 授权 |
> | Microsoft.Network/expressRouteCircuits/authorizations/delete | 删除 ExpressRouteCircuit 授权 |
> | Microsoft.Network/expressRouteCircuits/peerings/read | 获取 ExpressRouteCircuit 对等互连 |
> | Microsoft.Network/expressRouteCircuits/peerings/write | 创建新的或更新现有的 ExpressRouteCircuit 对等互连 |
> | Microsoft.Network/expressRouteCircuits/peerings/delete | 删除 ExpressRouteCircuit 对等互连 |
> | Microsoft.Network/expressRouteCircuits/peerings/arpTables/read | 获取 ExpressRouteCircuit 对等互连 ArpTable |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/read | 获取 ExpressRouteCircuit 连接 |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/write | 创建或更新现有 ExpressRouteCircuit 连接资源 |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | 删除 ExpressRouteCircuit 连接 |
> | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | 获取 Express Route 线路的对等连接 |
> | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/diagnosticSettings/read | 获取 ExpressRoute 线路对等互连的诊断设置 |
> | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 ExpressRoute 线路对等互连的诊断设置 |
> | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/metricDefinitions/read | 获取 ExpressRoute 线路对等互连的指标定义 |
> | Microsoft.Network/expressRouteCircuits/peerings/routeTables/read | 获取 ExpressRouteCircuit 对等互连 RouteTable |
> | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/read | 获取 ExpressRouteCircuit 对等互连 RouteTable 摘要 |
> | Microsoft.Network/expressRouteCircuits/peerings/stats/read | 获取 ExpressRouteCircuit 对等互连统计信息 |
> | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | 获取 ExpressRoute 线路的诊断设置 |
> | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 ExpressRoute 线路的诊断设置 |
> | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | 获取 ExpressRoute 线路的事件 |
> | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | 获取 ExpressRoute 线路的指标定义 |
> | Microsoft.Network/expressRouteCircuits/stats/read | 获取 ExpressRouteCircuit 统计信息 |
> | Microsoft.Network/expressRouteCrossConnections/read | 获取快速路由交叉连接 |
> | Microsoft.Network/expressRouteCrossConnections/write | 创建或更新快速路由交叉连接 |
> | Microsoft.Network/expressRouteCrossConnections/delete | 删除快速路由交叉连接 |
> | Microsoft.Network/expressRouteCrossConnections/join/action | 加入 Express Route 交叉连接。 不可发出警报。 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/read | 获取快速路由交叉对等互连 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/write | 创建快速路由交叉对等互连，或更新现有快速路由交叉对等互连 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/delete | 删除快速路由交叉对等互连 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/read | 获取快速路由交叉对等互连 ARP 表 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/read | 获取快速路由交叉对等互连路由表 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/read | 获取快速路由交叉对等互连路由表摘要 |
> | Microsoft.Network/expressRouteGateways/read | 获取快速路由网关 |
> | Microsoft.Network/expressRouteGateways/write | 创建或更新快速路由网关 |
> | Microsoft.Network/expressRouteGateways/delete | 删除快速路由网关 |
> | Microsoft.Network/expressRouteGateways/join/action | 加入 Express Route 网关。 不可发出警报。 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | 获取快速路由连接 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | 创建快速路由连接，或更新现有的快速路由连接 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | 删除快速路由连接 |
> | Microsoft.Network/expressRouteGateways/providers/Microsoft.Insights/metricDefinitions/read | 获取 ExpressRoute 网关的指标定义 |
> | Microsoft.Network/expressRoutePorts/read | 获取 ExpressRoutePorts |
> | Microsoft.Network/expressRoutePorts/write | 创建或更新 ExpressRoutePorts |
> | Microsoft.Network/expressRoutePorts/join/action | 加入 Express Route 端口。 不可发出警报。 |
> | Microsoft.Network/expressRoutePorts/delete | 删除 ExpressRoutePorts |
> | Microsoft.Network/expressRoutePorts/generateloa/action | 为 ExpressRoutePorts 生成 LOA |
> | Microsoft.Network/expressRoutePorts/authorizations/read | 获取 ExpressRoutePorts 授权 |
> | Microsoft.Network/expressRoutePorts/links/read | 获取 ExpressRouteLink |
> | Microsoft.Network/expressRoutePorts/providers/Microsoft.Insights/metricDefinitions/read | 获取 ExpressRoute 端口的指标定义 |
> | Microsoft.Network/expressRoutePortsLocations/read | 获取快速路由端口位置 |
> | Microsoft.Network/expressRouteServiceProviders/read | 获取 Express Route 服务提供商 |
> | Microsoft.Network/firewallPolicies/read | 获取防火墙策略 |
> | Microsoft.Network/firewallPolicies/write | 创建防火墙策略，或更新现有的防火墙策略 |
> | Microsoft.Network/firewallPolicies/join/action | 加入防火墙策略。 不可发出警报。 |
> | Microsoft.Network/firewallPolicies/certificates/action | 生成防火墙策略证书 |
> | Microsoft.Network/firewallPolicies/delete | 删除防火墙策略 |
> | Microsoft.Network/firewallPolicies/ruleCollectionGroups/read | 获取防火墙策略规则集合组 |
> | Microsoft.Network/firewallPolicies/ruleCollectionGroups/write | 创建防火墙策略规则集合组，或更新现有的防火墙策略规则集合组 |
> | Microsoft.Network/firewallPolicies/ruleCollectionGroups/delete | 删除防火墙策略规则集合组 |
> | Microsoft.Network/firewallPolicies/ruleGroups/read | 获取防火墙策略规则组 |
> | Microsoft.Network/firewallPolicies/ruleGroups/write | 创建防火墙策略规则组，或更新现有的防火墙策略规则组 |
> | Microsoft.Network/firewallPolicies/ruleGroups/delete | 删除防火墙策略规则组 |
> | Microsoft.Network/frontDoors/read | 获取 Front Door |
> | Microsoft.Network/frontDoors/write | 创建或更新 Front Door |
> | Microsoft.Network/frontDoors/delete | 删除 Front Door |
> | Microsoft.Network/frontDoors/purge/action | 从 Front Door 中清除缓存的内容 |
> | Microsoft.Network/frontDoors/validateCustomDomain/action | 验证 Front Door 的前端终结点 |
> | Microsoft.Network/frontDoors/backendPools/read | 获取后端池 |
> | Microsoft.Network/frontDoors/backendPools/write | 创建或更新后端池 |
> | Microsoft.Network/frontDoors/backendPools/delete | 删除后端池 |
> | Microsoft.Network/frontDoors/frontendEndpoints/read | 获取前端终结点 |
> | Microsoft.Network/frontDoors/frontendEndpoints/write | 创建或更新前端终结点 |
> | Microsoft.Network/frontDoors/frontendEndpoints/delete | 删除前端终结点 |
> | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | 在前端终结点上启用 HTTPS |
> | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | 在前端终结点上禁用 HTTPS |
> | Microsoft.Network/frontDoors/healthProbeSettings/read | 获取运行状况探测设置 |
> | Microsoft.Network/frontDoors/healthProbeSettings/write | 创建或更新运行状况探测设置 |
> | Microsoft.Network/frontDoors/healthProbeSettings/delete | 删除运行状况探测设置 |
> | Microsoft.Network/frontDoors/loadBalancingSettings/read | 获取负载均衡设置 |
> | Microsoft.Network/frontDoors/loadBalancingSettings/write | 创建或更新负载均衡设置 |
> | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 创建或更新负载均衡设置 |
> | Microsoft.Network/frontdoors/providers/Microsoft.Insights/diagnosticSettings/read | 获取 Frontdoor 资源的诊断设置 |
> | Microsoft.Network/frontdoors/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 Frontdoor 资源的诊断设置 |
> | Microsoft.Network/frontdoors/providers/Microsoft.Insights/logDefinitions/read | 获取 Frontdoor 资源的可用日志 |
> | Microsoft.Network/frontdoors/providers/Microsoft.Insights/metricDefinitions/read | 获取 Frontdoor 资源的可用指标 |
> | Microsoft.Network/frontDoors/routingRules/read | 获取路由规则 |
> | Microsoft.Network/frontDoors/routingRules/write | 创建或更新路由规则 |
> | Microsoft.Network/frontDoors/routingRules/delete | 删除路由规则 |
> | Microsoft.Network/frontDoors/rulesEngines/read | 获取规则引擎 |
> | Microsoft.Network/frontDoors/rulesEngines/write | 创建或更新规则引擎 |
> | Microsoft.Network/frontDoors/rulesEngines/delete | 删除规则引擎 |
> | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | 获取 Web 应用程序防火墙托管规则集 |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | 获取 Web 应用程序防火墙策略 |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | 创建或更新 Web 应用程序防火墙策略 |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | 删除 Web 应用程序防火墙策略 |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/join/action | 联接 Web 应用程序防火墙策略。 不可发出警报。 |
> | Microsoft.Network/ipAllocations/read | 获取 IpAllocation |
> | Microsoft.Network/ipAllocations/write | 创建 IpAllocation 或更新现有的 IpAllocation |
> | Microsoft.Network/ipAllocations/delete | 删除 IpAllocation |
> | Microsoft.Network/ipGroups/read | 获取 IpGroup |
> | Microsoft.Network/ipGroups/write | 创建 IpGroup 或更新现有 IpGroup |
> | Microsoft.Network/ipGroups/validate/action | 验证 IpGroup |
> | Microsoft.Network/ipGroups/updateReferences/action | 更新 IpGroup 中的引用 |
> | Microsoft.Network/ipGroups/join/action | 联接 IpGroup。 不可发出警报。 |
> | Microsoft.Network/ipGroups/delete | 删除 IpGroup |
> | Microsoft.Network/loadBalancers/read | 获取负载均衡器定义 |
> | Microsoft.Network/loadBalancers/write | 创建负载均衡器，或更新现有的负载均衡器 |
> | Microsoft.Network/loadBalancers/delete | 删除负载均衡器 |
> | Microsoft.Network/loadBalancers/backendAddressPools/queryInboundNatRulePortMapping/action | 查询入站 Nat 规则端口映射。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/read | 获取负载均衡器的后端地址池定义 |
> | Microsoft.Network/loadBalancers/backendAddressPools/write | 创建负载均衡器后端地址池，或更新现有的负载均衡器后端地址池 |
> | Microsoft.Network/loadBalancers/backendAddressPools/delete | 删除负载均衡器后端地址池 |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入负载均衡器后端地址池。 不可发出警报。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/backendPoolAddresses/read | 列出负载均衡器后端地址池的后端地址 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 获取负载均衡器的前端 IP 配置定义 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 加入负载均衡器前端 IP 配置。 不可发出警报。 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/loadBalancerPools/read | 获取负载均衡器前端 IP 地址后端池定义 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/loadBalancerPools/write | 创建负载均衡器前端 IP 地址后端池，或更新现有的公共 IP 地址负载均衡器后端池 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/loadBalancerPools/delete | 删除负载均衡器前端 IP 地址后端池 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/loadBalancerPools/join/action | 加入负载均衡器前端 IP 地址后端池。 不可发出警报。 |
> | Microsoft.Network/loadBalancers/inboundNatPools/read | 获取负载均衡器的入站 NAT 池定义 |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入负载均衡器入站 NAT 池。 不可发出警报。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/read | 获取负载均衡器的入站 NAT 规则定义 |
> | Microsoft.Network/loadBalancers/inboundNatRules/write | 创建负载均衡器入站 NAT 规则，或更新现有的负载均衡器入站 NAT 规则 |
> | Microsoft.Network/loadBalancers/inboundNatRules/delete | 删除负载均衡器入站 NAT 规则 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入负载均衡器入站 NAT 规则。 不可发出警报。 |
> | Microsoft.Network/loadBalancers/loadBalancingRules/read | 获取负载均衡器的负载均衡规则定义 |
> | Microsoft.Network/loadBalancers/networkInterfaces/read | 获取对负载均衡器下的所有网络接口的引用 |
> | Microsoft.Network/loadBalancers/outboundRules/read | 获取负载均衡器的出站规则定义 |
> | Microsoft.Network/loadBalancers/probes/read | 获取负载均衡器探测 |
> | Microsoft.Network/loadBalancers/probes/join/action | 允许使用负载均衡器的探测。 例如，使用此权限，VM 规模集的 healthProbe 属性可以引用探测。 不可发出警报。 |
> | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | 获取负载均衡器诊断设置 |
> | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新负载均衡器诊断设置 |
> | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | 获取负载均衡器的事件 |
> | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | 获取负载均衡器的可用指标 |
> | Microsoft.Network/loadBalancers/virtualMachines/read | 获取对负载均衡器下的所有虚拟机的引用 |
> | Microsoft.Network/localnetworkgateways/read | 获取 LocalNetworkGateway |
> | Microsoft.Network/localnetworkgateways/write | 创建新的或更新现有的 LocalNetworkGateway |
> | Microsoft.Network/localnetworkgateways/delete | 删除 LocalNetworkGateway |
> | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 检查加速网络支持 |
> | Microsoft.Network/locations/batchValidatePrivateEndpointsForResourceMove/action | 分批验证专用终结点以进行资源移动。 |
> | Microsoft.Network/locations/batchNotifyPrivateEndpointsForResourceMove/action | 分批通知专用终结点将发生资源移动。 |
> | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | 检查专用链接服务可见性 |
> | Microsoft.Network/locations/validateResourceOwnership/action | 验证资源所有权 |
> | Microsoft.Network/locations/setResourceOwnership/action | 设置资源所有权 |
> | Microsoft.Network/locations/effectiveResourceOwnership/action | 获取有效资源所有权 |
> | Microsoft.Network/locations/setAzureNetworkManagerConfiguration/action | 设置 Azure 网络管理器配置 |
> | Microsoft.Network/locations/getAzureNetworkManagerConfiguration/action | 获取 Azure 网络管理器配置 |
> | Microsoft.Network/locations/bareMetalTenants/action | 分配或验证裸机租户 |
> | Microsoft.Network/locations/commitInternalAzureNetworkManagerConfiguration/action | 提交 ANM 中的内部 AzureNetworkManager 配置 |
> | Microsoft.Network/locations/internalAzureVirtualNetworkManagerOperation/action | ANM 中的内部 AzureVirtualNetworkManager 操作 |
> | Microsoft.Network/locations/setLoadBalancerFrontendPublicIpAddresses/action | SetLoadBalancerFrontendPublicIpAddresses 针对 2 个负载均衡器的前端 IP 配置。 请求正文中提供 IP 配置的 Azure 资源管理器 ID。 |
> | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | 获取自动批准的专用链接服务 |
> | Microsoft.Network/locations/availableDelegations/read | 获取可用的委派 |
> | Microsoft.Network/locations/availablePrivateEndpointTypes/read | 获取可用的专用终结点资源 |
> | Microsoft.Network/locations/availableServiceAliases/read | 获取可用服务别名 |
> | Microsoft.Network/locations/checkDnsNameAvailability/read | 检查 DNS 标签是否可在指定的位置使用 |
> | Microsoft.Network/locations/dnsResolverOperationResults/read | 获取 JSON 格式的 DNS 解析程序操作结果 |
> | Microsoft.Network/locations/dnsResolverOperationStatuses/read | 获取 DNS 解析程序操作的状态 |
> | Microsoft.Network/locations/operationResults/read | 获取异步 POST 或 DELETE 操作的操作结果 |
> | Microsoft.Network/locations/operations/read | 获取表示异步操作状态的操作资源 |
> | Microsoft.Network/locations/privateLinkServices/privateEndpointConnectionProxies/read | 获取专用终结点连接代理资源。 |
> | Microsoft.Network/locations/privateLinkServices/privateEndpointConnectionProxies/write | 创建新的专用终结点连接代理，或更新现有的专用终结点连接代理。 |
> | Microsoft.Network/locations/privateLinkServices/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理资源。 |
> | Microsoft.Network/locations/serviceTagDetails/read | 获取服务标记详细信息 |
> | Microsoft.Network/locations/serviceTags/read | 获取服务标记 |
> | Microsoft.Network/locations/supportedVirtualMachineSizes/read | 获取支持的虚拟机大小 |
> | Microsoft.Network/locations/usages/read | 获取资源用量指标 |
> | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 获取可用虚拟网络终结点服务的列表 |
> | Microsoft.Network/masterCustomIpPrefixes/read | 获取主自定义 IP 前缀定义 |
> | Microsoft.Network/masterCustomIpPrefixes/write | 创建主自定义 IP 前缀或更新现有的主自定义 IP 前缀 |
> | Microsoft.Network/masterCustomIpPrefixes/delete | 删除主自定义 PI 前缀 |
> | Microsoft.Network/natGateways/join/action | 加入 NAT 网关 |
> | Microsoft.Network/natGateways/providers/Microsoft.Insights/metricDefinitions/read | 获取 NAT 网关的可用指标 |
> | Microsoft.Network/networkExperimentProfiles/read | 获取 Internet 分析器配置文件 |
> | Microsoft.Network/networkExperimentProfiles/write | 创建或更新 Internet 分析器配置文件 |
> | Microsoft.Network/networkExperimentProfiles/delete | 删除 Internet 分析器配置文件 |
> | Microsoft.Network/networkExperimentProfiles/experiments/read | 获取 Internet 分析器测试 |
> | Microsoft.Network/networkExperimentProfiles/experiments/write | 创建或更新 Internet 分析器测试 |
> | Microsoft.Network/networkExperimentProfiles/experiments/delete | 删除 Internet 分析器测试 |
> | Microsoft.Network/networkExperimentProfiles/experiments/timeseries/action | 获取 Internet 分析器测试的时序 |
> | Microsoft.Network/networkExperimentProfiles/experiments/latencyScorecard/action | 获取 Internet 分析器测试的延迟记分卡 |
> | Microsoft.Network/networkExperimentProfiles/preconfiguredEndpoints/read | 获取 Internet 分析器配置文件的预配置终结点 |
> | Microsoft.Network/networkIntentPolicies/read | 获取网络意向策略说明 |
> | Microsoft.Network/networkIntentPolicies/write | 创建网络意向策略或更新现有网络意向策略 |
> | Microsoft.Network/networkIntentPolicies/delete | 删除网络意向策略 |
> | Microsoft.Network/networkInterfaces/read | 获取网络接口定义。  |
> | Microsoft.Network/networkInterfaces/write | 创建网络接口，或更新现有的网络接口。  |
> | Microsoft.Network/networkInterfaces/join/action | 将虚拟机加入到网络接口。 不可发出警报。 |
> | Microsoft.Network/networkInterfaces/delete | 删除网络接口 |
> | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | 获取 VM 的网络接口上配置的路由表 |
> | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | 获取 VM 的网络接口上配置的网络安全组 |
> | Microsoft.Network/networkInterfaces/UpdateParentNicAttachmentOnElasticNic/action | 更新与弹性 NIC 关联的父 NIC |
> | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | 获取资源的诊断标识 |
> | Microsoft.Network/networkInterfaces/ipconfigurations/read | 获取网络接口 IP 配置定义。  |
> | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | 加入网络接口 IP 配置。 不可发出警报。 |
> | Microsoft.Network/networkInterfaces/loadBalancers/read | 获取网络接口所属的所有负载均衡器 |
> | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | 获取网络接口的可用指标 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/read | 获取网络接口分流配置。 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/write | 创建网络接口分流配置，或更新现有的网络接口分流配置。 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/delete | 删除网络接口分流配置。 |
> | Microsoft.Network/networkProfiles/read | 获取网络配置文件 |
> | Microsoft.Network/networkProfiles/write | 创建或更新网络配置文件 |
> | Microsoft.Network/networkProfiles/delete | 删除网络配置文件 |
> | Microsoft.Network/networkProfiles/setContainers/action | 设置容器 |
> | Microsoft.Network/networkProfiles/removeContainers/action | 删除容器 |
> | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | 设置容器网络接口 |
> | Microsoft.Network/networkSecurityGroups/read | 获取网络安全组定义 |
> | Microsoft.Network/networkSecurityGroups/write | 创建网络安全组，或更新现有的网络安全组 |
> | Microsoft.Network/networkSecurityGroups/delete | 删除网络安全组 |
> | Microsoft.Network/networkSecurityGroups/join/action | 加入网络安全组。 不可发出警报。 |
> | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 获取默认的安全规则定义 |
> | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 获取网络安全组诊断设置 |
> | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新网络安全组诊断设置，此操作由见解资源提供程序进行补充。 |
> | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 获取网络安全组的事件 |
> | Microsoft.Network/networkSecurityGroups/securityRules/read | 获取安全规则定义 |
> | Microsoft.Network/networkSecurityGroups/securityRules/write | 创建安全规则，或更新现有的安全规则 |
> | Microsoft.Network/networkSecurityGroups/securityRules/delete | 删除安全规则 |
> | Microsoft.Network/networkVirtualAppliances/delete | 删除网络虚拟设备 |
> | Microsoft.Network/networkVirtualAppliances/read | 获取网络虚拟设备 |
> | Microsoft.Network/networkVirtualAppliances/write | 创建或更新网络虚拟设备 |
> | Microsoft.Network/networkWatchers/read | 获取网络观察程序定义 |
> | Microsoft.Network/networkWatchers/write | 创建网络观察程序，或更新现有的网络观察程序 |
> | Microsoft.Network/networkWatchers/delete | 删除网络观察程序 |
> | Microsoft.Network/networkWatchers/configureFlowLog/action | 配置目标资源的流日志记录。 |
> | Microsoft.Network/networkWatchers/ipFlowVerify/action | 返回是允许还是拒绝与特定的目标相互发送数据包。 |
> | Microsoft.Network/networkWatchers/nextHop/action | 对于指定的目标和目标 IP 地址，返回下一跃点类型和下一跃点 IP 地址。 |
> | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | 获取资源上流日志记录的状态。 |
> | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 获取上次运行的或当前正在运行的故障排除操作的故障排除结果。 |
> | Microsoft.Network/networkWatchers/securityGroupView/action | 查看 VM 上应用的已配置和有效的网络安全组规则。 |
> | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | 诊断网络配置。 |
> | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | 批量查询指定终结点之间的监视连接 |
> | Microsoft.Network/networkWatchers/topology/action | 获取资源组中的资源及其关系的网络级视图。 |
> | Microsoft.Network/networkWatchers/troubleshoot/action | 开始针对 Azure 中的网络资源进行故障排除。 |
> | Microsoft.Network/networkWatchers/connectivityCheck/action | 验证从虚拟机到给定终结点（包括另一个 VM 或任意远程服务器）建立直接 TCP 连接的可能性。 |
> | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 返回 Internet 服务提供商从指定位置到 Azure 区域的相对延迟评分。 |
> | Microsoft.Network/networkWatchers/availableProvidersList/action | 返回指定 Azure 区域的所有可用 Internet 服务提供商。 |
> | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 开始监视指定终结点之间的连接 |
> | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 停止/暂停监视指定终结点之间的连接 |
> | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 监视指定终结点之间连接的查询 |
> | Microsoft.Network/networkWatchers/connectionMonitors/read | 获取连接监视器详细信息 |
> | Microsoft.Network/networkWatchers/connectionMonitors/write | 创建连接监视器 |
> | Microsoft.Network/networkWatchers/connectionMonitors/delete | 删除连接监视器 |
> | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | 获取连接监视器的可用指标 |
> | Microsoft.Network/networkWatchers/flowLogs/read | 获取流日志详细信息 |
> | Microsoft.Network/networkWatchers/flowLogs/write | 创建流日志 |
> | Microsoft.Network/networkWatchers/flowLogs/delete | 删除流日志 |
> | Microsoft.Network/networkWatchers/lenses/start/action | 开始监视指定终结点上的流量 |
> | Microsoft.Network/networkWatchers/lenses/stop/action | 停止/暂停监视指定终结点上的流量 |
> | Microsoft.Network/networkWatchers/lenses/query/action | 监视指定终结点上流量的查询 |
> | Microsoft.Network/networkWatchers/lenses/read | 获取可重用功能区详细信息 |
> | Microsoft.Network/networkWatchers/lenses/write | 创建可重用功能区 |
> | Microsoft.Network/networkWatchers/lenses/delete | 删除可重用功能区 |
> | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 获取有关数据包捕获资源的属性和状态的信息。 |
> | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 停止正在运行的数据包捕获会话。 |
> | Microsoft.Network/networkWatchers/packetCaptures/read | 获取数据包捕获定义 |
> | Microsoft.Network/networkWatchers/packetCaptures/write | 创建数据包捕获 |
> | Microsoft.Network/networkWatchers/packetCaptures/delete | 删除数据包捕获 |
> | Microsoft.Network/networkWatchers/pingMeshes/start/action | 启动指定 VM 之间的 PingMesh |
> | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 停止指定 VM 之间的 PingMesh |
> | Microsoft.Network/networkWatchers/pingMeshes/read | 获取 PingMesh 详细信息 |
> | Microsoft.Network/networkWatchers/pingMeshes/write | 创建 PingMesh |
> | Microsoft.Network/networkWatchers/pingMeshes/delete | 删除 PingMesh |
> | Microsoft.Network/networkWatchers/topology/read | 获取资源组中的资源及其关系的网络级视图。 |
> | Microsoft.Network/operations/read | 获取可用操作 |
> | Microsoft.Network/p2sVpnGateways/read | 获取 P2SVpnGateway。 |
> | Microsoft.Network/p2sVpnGateways/write | 放置 P2SVpnGateway。 |
> | Microsoft.Network/p2sVpnGateways/delete | 删除 P2SVpnGateway。 |
> | microsoft.network/p2sVpnGateways/reset/action | 重置 P2SVpnGateway |
> | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | 生成 P2SVpnGateway 的 Vpn 配置文件 |
> | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | 获取 P2SVpnGateway 的 P2S VPN 连接运行状况 |
> | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | 获取 P2SVpnGateway 的 P2S VPN 连接运行状况详情 |
> | Microsoft.Network/p2sVpnGateways/disconnectp2svpnconnections/action | 断开 P2S VPN 连接 |
> | Microsoft.Network/p2sVpnGateways/providers/Microsoft.Insights/diagnosticSettings/read | 获取 P2S VPN 网关诊断设置 |
> | Microsoft.Network/p2sVpnGateways/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 P2S VPN 网关诊断设置，此操作由 Insights 资源提供程序进行补充。 |
> | Microsoft.Network/p2sVpnGateways/providers/Microsoft.Insights/logDefinitions/read | 获取 P2S VPN 网关的事件 |
> | Microsoft.Network/p2sVpnGateways/providers/Microsoft.Insights/metricDefinitions/read | 获取 P2S VPN 网关的可用指标 |
> | Microsoft.Network/privateDnsOperationResults/read | 获取专用 DNS 操作的结果 |
> | Microsoft.Network/privateDnsOperationStatuses/read | 获取专用 DNS 操作的状态 |
> | Microsoft.Network/privateDnsZones/read | 获取 JSON 格式的专用 DNS 区域属性。 请注意，此命令不会检索专用 DNS 区域所链接到的虚拟网络，也不会检索区域中包含的记录集。 |
> | Microsoft.Network/privateDnsZones/write | 在资源组中创建或更新专用 DNS 区域。 请注意，无法使用此命令在区域中创建或者更新虚拟网络链接或记录集。 |
> | Microsoft.Network/privateDnsZones/delete | 删除专用 DNS 区域。 |
> | Microsoft.Network/privateDnsZones/join/action | 加入专用 DNS 区域 |
> | Microsoft.Network/privateDnsZones/A/read | 获取专用 DNS 区域中 JSON 格式的“A”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/privateDnsZones/A/write | 在专用 DNS 区域中创建或更新“A”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/privateDnsZones/A/delete | 从专用 DNS 区域中删除具有给定名称的“A”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/AAAA/read | 获取专用 DNS 区域中 JSON 格式的“AAAA”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/privateDnsZones/AAAA/write | 在专用 DNS 区域中创建或更新“AAAA”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/privateDnsZones/AAAA/delete | 从专用 DNS 区域中删除具有给定名称的“AAAA”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/ALL/read | 获取各种类型的专用 DNS 记录集 |
> | Microsoft.Network/privateDnsZones/CNAME/read | 获取专用 DNS 区域中 JSON 格式的“CNAME”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/CNAME/write | 在专用 DNS 区域中创建或更新“CNAME”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/CNAME/delete | 从专用 DNS 区域中删除具有给定名称的“CNAME”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/MX/read | 获取专用 DNS 区域中 JSON 格式的“MX”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/privateDnsZones/MX/write | 在专用 DNS 区域中创建或更新“MX”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/privateDnsZones/MX/delete | 从专用 DNS 区域中删除具有给定名称的“MX”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/providers/Microsoft.Insights/diagnosticSettings/read | 获取专用 DNS 区域诊断设置 |
> | Microsoft.Network/privateDnsZones/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新专用 DNS 区域诊断设置 |
> | Microsoft.Network/privateDnsZones/providers/Microsoft.Insights/metricDefinitions/read | 获取专用 DNS 区域指标设置 |
> | Microsoft.Network/privateDnsZones/PTR/read | 获取专用 DNS 区域中 JSON 格式的“PTR”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/privateDnsZones/PTR/write | 在专用 DNS 区域中创建或更新“PTR”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/privateDnsZones/PTR/delete | 从专用 DNS 区域中删除具有给定名称的“PTR”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/recordsets/read | 获取各种类型的专用 DNS 记录集 |
> | Microsoft.Network/privateDnsZones/SOA/read | 获取专用 DNS 区域中 JSON 格式的“SOA”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/SOA/write | 在专用 DNS 区域中更新“SOA”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/SRV/read | 获取专用 DNS 区域中 JSON 格式的“SRV”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/privateDnsZones/SRV/write | 在专用 DNS 区域中创建或更新“SRV”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/privateDnsZones/SRV/delete | 从专用 DNS 区域中删除具有给定名称的“SRV”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/TXT/read | 获取专用 DNS 区域中 JSON 格式的“TXT”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | Microsoft.Network/privateDnsZones/TXT/write | 在专用 DNS 区域中创建或更新“TXT”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | Microsoft.Network/privateDnsZones/TXT/delete | 从专用 DNS 区域中删除具有给定名称的“TXT”类型的记录集。 |
> | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | 获取专用 DNS 区域与虚拟网络的链接属性（JSON 格式）。 |
> | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | 创建或更新专用 DNS 区域与虚拟网络之间的链接。 |
> | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | 删除专用 DNS 区域与虚拟网络之间的链接。 |
> | Microsoft.Network/privateEndpointRedirectMaps/read | 获取专用终结点 RedirectMap |
> | Microsoft.Network/privateEndpointRedirectMaps/write | 创建专用终结点 RedirectMap，或更新现有的专用终结点 RedirectMap |
> | Microsoft.Network/privateEndpoints/pushPropertiesToResource/action | 从 NRP 客户端推送专用终结点属性更新的操作 |
> | Microsoft.Network/privateEndpoints/read | 获取专用终结点资源。 |
> | Microsoft.Network/privateEndpoints/write | 创建新的专用终结点，或更新现有的专用终结点。 |
> | Microsoft.Network/privateEndpoints/delete | 删除专用终结点资源。 |
> | Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read | 获取专用 DNS 区域组 |
> | Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write | 放置专用 DNS 区域组 |
> | Microsoft.Network/privateEndpoints/privateLinkServiceProxies/read | 获取专用链接服务代理资源。 |
> | Microsoft.Network/privateEndpoints/privateLinkServiceProxies/write | 创建新的专用链接服务代理，或更新现有的专用链接服务代理。 |
> | Microsoft.Network/privateEndpoints/privateLinkServiceProxies/delete | 删除专用链接服务代理资源。 |
> | Microsoft.Network/privateEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 获取专用终结点的可用指标 |
> | Microsoft.Network/privateLinkServices/read | 获取专用链接服务资源。 |
> | Microsoft.Network/privateLinkServices/write | 创建新的专用链接服务，或更新现有的专用链接服务。 |
> | Microsoft.Network/privateLinkServices/delete | 删除专用链接服务资源。 |
> | Microsoft.Network/privateLinkServices/notifyPrivateEndpointMove/action | 通知连接的专用链接服务将发生专用终结点移动 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnectionProxies/read | 获取专用终结点连接代理资源。 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnectionProxies/write | 创建新的专用终结点连接代理，或更新现有的专用终结点连接代理。 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理资源。 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | 获取专用终结点连接定义。 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | 创建新的专用终结点连接，或更新现有的专用终结点连接。 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | 删除专用终结点连接。 |
> | Microsoft.Network/privateLinkServices/providers/Microsoft.Insights/metricDefinitions/read | 获取专用链接服务的可用指标 |
> | Microsoft.Network/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | Microsoft.Network/publicIPAddresses/write | 创建公共 IP 地址，或更新现有的公共 IP 地址。  |
> | Microsoft.Network/publicIPAddresses/delete | 删除公共 IP 地址。 |
> | Microsoft.Network/publicIPAddresses/join/action | 加入公共 IP 地址。 不可发出警报。 |
> | Microsoft.Network/publicIPAddresses/dnsAliases/read | 获取公共 IP 地址 DNS 别名资源 |
> | Microsoft.Network/publicIPAddresses/dnsAliases/write | 创建公共 IP 地址 DNS 别名资源 |
> | Microsoft.Network/publicIPAddresses/dnsAliases/delete | 删除公共 IP 地址 DNS 别名资源 |
> | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | 获取公共 IP 地址的诊断设置 |
> | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新公共 IP 地址的诊断设置 |
> | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | 获取公共 IP 地址的日志定义 |
> | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | 获取公共 IP 地址的指标定义 |
> | Microsoft.Network/publicIPPrefixes/read | 获取公共 IP 前缀定义 |
> | Microsoft.Network/publicIPPrefixes/write | 创建公共 IP 前缀或更新现有的公共 IP 前缀 |
> | Microsoft.Network/publicIPPrefixes/delete | 删除公共 IP 前缀 |
> | Microsoft.Network/publicIPPrefixes/join/action | 加入 PublicIPPrefix。 不可发出警报。 |
> | Microsoft.Network/routeFilters/read | 获取路由筛选器定义 |
> | Microsoft.Network/routeFilters/join/action | 加入路由筛选器。 不可发出警报。 |
> | Microsoft.Network/routeFilters/delete | 删除路由筛选器定义 |
> | Microsoft.Network/routeFilters/write | 创建路由筛选器，或更新现有的路由筛选器 |
> | Microsoft.Network/routeFilters/routeFilterRules/read | 获取路由筛选器规则定义 |
> | Microsoft.Network/routeFilters/routeFilterRules/write | 创建路由筛选器规则，或更新现有的路由筛选器规则 |
> | Microsoft.Network/routeFilters/routeFilterRules/delete | 删除路由筛选器规则定义 |
> | Microsoft.Network/routeTables/read | 获取路由表定义 |
> | Microsoft.Network/routeTables/write | 创建路由表，或更新现有的路由表 |
> | Microsoft.Network/routeTables/delete | 删除路由表定义 |
> | Microsoft.Network/routeTables/join/action | 加入路由表。 不可发出警报。 |
> | Microsoft.Network/routeTables/routes/read | 获取路由定义 |
> | Microsoft.Network/routeTables/routes/write | 创建路由，或更新现有的路由 |
> | Microsoft.Network/routeTables/routes/delete | 删除路由定义 |
> | Microsoft.Network/securityPartnerProviders/read | 获取 SecurityPartnerProvider |
> | Microsoft.Network/securityPartnerProviders/write | 创建 SecurityPartnerProvider，或更新现有的 SecurityPartnerProvider |
> | Microsoft.Network/securityPartnerProviders/validate/action | 验证 SecurityPartnerProvider |
> | Microsoft.Network/securityPartnerProviders/updateReferences/action | 更新 SecurityPartnerProvider 中的引用 |
> | Microsoft.Network/securityPartnerProviders/join/action | 加入 SecurityPartnerProvider。 不可发出警报。 |
> | Microsoft.Network/securityPartnerProviders/delete | 删除 SecurityPartnerProvider |
> | Microsoft.Network/serviceEndpointPolicies/read | 获取服务终结点策略说明 |
> | Microsoft.Network/serviceEndpointPolicies/write | 创建服务终结点策略或更新现有服务终结点策略 |
> | Microsoft.Network/serviceEndpointPolicies/delete | 删除服务终结点策略 |
> | Microsoft.Network/serviceEndpointPolicies/join/action | 加入服务终结点策略。 不可发出警报。 |
> | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | 将子网加入到服务终结点策略。 不可发出警报。 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | 获取服务终结点策略定义说明 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | 创建服务终结点策略定义或更新现有服务终结点策略定义 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | 删除服务终结点策略定义 |
> | Microsoft.Network/trafficManagerGeographicHierarchies/read | 获取流量管理器地理层次结构，其中包含可以配合地理流量路由方法使用的区域 |
> | Microsoft.Network/trafficManagerProfiles/read | 获取流量管理器配置文件配置。 此配置包含 DNS 设置、流量路由设置、终结点监视设置，以及此流量管理器配置文件路由的终结点列表。 |
> | Microsoft.Network/trafficManagerProfiles/write | 创建流量管理器配置文件，或修改现有流量管理器配置文件的配置。<br>这包括启用或禁用配置文件，以及修改 DNS 设置、流量路由设置或终结点监视设置。<br>可以添加、删除、启用或禁用流量管理器配置文件路由的终结点。 |
> | Microsoft.Network/trafficManagerProfiles/delete | 删除流量管理器配置文件。 与流量管理器配置文件关联的所有设置都将丢失，该配置文件不再可用于路由流量。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | 获取属于流量管理器配置文件的 Azure 终结点，包括该 Azure 终结点的所有属性。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 在现有流量管理器配置文件中添加新的 Azure 终结点，或更新该流量管理器配置文件中的现有 Azure 终结点的属性。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 从现有流量管理器配置文件中删除 Azure 终结点。 流量管理器会停止将流量路由到已删除的 Azure 终结点。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | 获取属于流量管理器配置文件的外部终结点，包括该外部终结点的所有属性。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 在现有流量管理器配置文件中添加新的外部终结点，或更新该流量管理器配置文件中的现有外部终结点的属性。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 从现有流量管理器配置文件中删除外部终结点。 流量管理器会停止将流量路由到已删除的外部终结点。 |
> | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 获取给定流量管理器配置文件的流量管理器热度地图，其中包含按位置和源 IP 的查询计数和延迟数据。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | 获取属于流量管理器配置文件的嵌套终结点，包括该嵌套终结点的所有属性。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 在现有流量管理器配置文件中添加新的嵌套终结点，或更新该流量管理器配置文件中的现有嵌套终结点的属性。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 从现有流量管理器配置文件中删除嵌套终结点。 流量管理器会停止将流量路由到已删除的嵌套终结点。 |
> | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | 获取流量管理器诊断设置 |
> | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新流量管理器诊断设置，此操作由 Insights 资源提供程序进行补充。 |
> | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | 获取流量管理器的事件 |
> | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | 获取流量管理器的可用指标。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/read | 获取用于实时用户指标集合的订阅级别密钥。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/write | 创建将用于实时用户指标集合的新订阅级别密钥。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/delete | 删除用于实时用户指标集合的订阅级别密钥。 |
> | Microsoft.Network/virtualHubs/delete | 删除虚拟中心 |
> | Microsoft.Network/virtualHubs/read | 获取虚拟中心 |
> | Microsoft.Network/virtualHubs/write | 创建或更新虚拟中心 |
> | Microsoft.Network/virtualHubs/effectiveRoutes/action | 获取在虚拟中心配置的有效路由 |
> | Microsoft.Network/virtualHubs/bgpConnections/read | 获取虚拟中心的中心 Bgp 连接子资源 |
> | Microsoft.Network/virtualHubs/bgpConnections/write | 创建或更新虚拟中心的中心 Bgp 连接子资源 |
> | Microsoft.Network/virtualHubs/bgpConnections/delete | 删除虚拟中心的中心 Bgp 连接子资源 |
> | Microsoft.Network/virtualHubs/bgpConnections/advertisedRoutes/action | 获取 virtualrouter 播发路由 |
> | Microsoft.Network/virtualHubs/bgpConnections/learnedRoutes/action | 获取 virtualrouter 获知的路由 |
> | Microsoft.Network/virtualHubs/hubRouteTables/read | 获取虚拟中心的路由表子资源 |
> | Microsoft.Network/virtualHubs/hubRouteTables/write | 创建或更新虚拟中心的路由表子资源 |
> | Microsoft.Network/virtualHubs/hubRouteTables/delete | 删除虚拟中心的路由表子资源 |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | 获取 HubVirtualNetworkConnection |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | 创建或更新 HubVirtualNetworkConnection |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | 删除 HubVirtualNetworkConnection |
> | Microsoft.Network/virtualHubs/ipConfigurations/read | 获取虚拟中心的中心 IpConfiguration 子资源 |
> | Microsoft.Network/virtualHubs/ipConfigurations/write | 创建或更新虚拟中心的中心 IpConfiguration 子资源 |
> | Microsoft.Network/virtualHubs/ipConfigurations/delete | 删除虚拟中心的中心 IpConfiguration 子资源 |
> | Microsoft.Network/virtualHubs/routeTables/read | 获取 VirtualHubRouteTableV2 |
> | Microsoft.Network/virtualHubs/routeTables/write | 创建或更新 VirtualHubRouteTableV2 |
> | Microsoft.Network/virtualHubs/routeTables/delete | 删除 VirtualHubRouteTableV2 |
> | Microsoft.Network/virtualHubs/routingIntent/read | 获取虚拟中心的路由意图子资源 |
> | Microsoft.Network/virtualHubs/routingIntent/write | 创建或更新虚拟中心的路由意图子资源 |
> | Microsoft.Network/virtualHubs/routingIntent/delete | 删除虚拟中心的路由意图子资源 |
> | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | 列出支持的 VPN 设备 |
> | Microsoft.Network/virtualNetworkGateways/read | 获取 VirtualNetworkGateway |
> | Microsoft.Network/virtualNetworkGateways/write | 创建或更新 VirtualNetworkGateway |
> | Microsoft.Network/virtualNetworkGateways/delete | 删除 virtualNetworkGateway |
> | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | 生成 virtualNetworkGateway 的 VpnClient 包 |
> | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | 生成 VirtualNetworkGateway 的 VpnProfile 包 |
> | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | 获取 VirtualNetworkGateway 的每个 VPN 客户端连接运行状况 |
> | microsoft.network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | 断开虚拟网络网关 VPN 连接 |
> | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 获取预生成的 VPN 客户端配置文件包的 URL |
> | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | 设置 VirtualNetworkGateway P2S 客户端的 Vpnclient Ipsec 参数。 |
> | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | 获取 VirtualNetworkGateway P2S 客户端的 Vpnclient Ipsec 参数。 |
> | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | 重置 VirtualNetworkGateway P2S 客户端的 Vpnclient 共享密钥。 |
> | microsoft.network/virtualnetworkgateways/reset/action | 重置 virtualNetworkGateway |
> | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | 获取 virtualNetworkGateway 播发路由 |
> | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | 获取 virtualNetworkGateway BGP 对等状态 |
> | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | 获取 virtualnetworkgateway 获知的路由 |
> | microsoft.network/virtualnetworkgateways/startpacketcapture/action | 启动虚拟网络网关数据包捕获。 |
> | microsoft.network/virtualnetworkgateways/stoppacketcapture/action | 停止虚拟网络网关数据包捕获。 |
> | microsoft.network/virtualnetworkgateways/connections/read | 获取 VirtualNetworkGatewayConnection |
> | microsoft.network/virtualNetworkGateways/natRules/read | 获取 NAT 规则资源 |
> | microsoft.network/virtualNetworkGateways/natRules/write | 放置 NAT 规则资源 |
> | microsoft.network/virtualNetworkGateways/natRules/delete | 删除 NAT 规则资源 |
> | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | 获取虚拟网络网关诊断设置 |
> | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新虚拟网络网关诊断设置，此操作由 Insights 资源提供程序进行补充。 |
> | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | 获取虚拟网络网关的事件 |
> | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | 获取虚拟网络网关的可用指标 |
> | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | Microsoft.Network/virtualNetworks/write | 创建虚拟网络，或更新现有的虚拟网络 |
> | Microsoft.Network/virtualNetworks/delete | 删除虚拟网络 |
> | Microsoft.Network/virtualNetworks/joinLoadBalancer/action | 将负载均衡器加入虚拟网络 |
> | Microsoft.Network/virtualNetworks/peer/action | 在两个不同的虚拟网络之间建立对等互连 |
> | Microsoft.Network/virtualNetworks/join/action | 加入虚拟网络。 不可发出警报。 |
> | Microsoft.Network/virtualNetworks/BastionHosts/action | 获取虚拟网络中的守护主机引用。 |
> | Microsoft.Network/virtualNetworks/bastionHosts/default/action | 获取虚拟网络中的守护主机引用。 |
> | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | 检查 IP 地址是否在指定的虚拟网络中可用 |
> | Microsoft.Network/virtualNetworks/customViews/read | 获取虚拟网络的自定义视图的定义 |
> | Microsoft.Network/virtualNetworks/customViews/get/action | 获取虚拟网络自定义视图内容 |
> | Microsoft.Network/virtualNetworks/dnsForwardingRulesets/read | 获取 JSON 格式的虚拟网络 DNS 转发规则集 |
> | Microsoft.Network/virtualNetworks/dnsResolvers/read | 获取 JSON 格式的虚拟网络 DNS 解析程序 |
> | Microsoft.Network/virtualNetworks/privateDnsZoneLinks/read | 获取专用 DNS 区域与虚拟网络的链接属性（JSON 格式）。 |
> | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | 获取虚拟网络的诊断设置 |
> | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新虚拟网络的诊断设置 |
> | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | 获取虚拟网络的日志定义 |
> | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read | 获取 PingMesh 的可用指标 |
> | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 获取虚拟网络对等互连代理定义 |
> | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 创建虚拟网络对等互连代理，或更新现有虚拟网络对等互连代理 |
> | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 删除虚拟网络对等互连代理 |
> | Microsoft.Network/virtualNetworks/subnets/read | 获取虚拟网络子网定义 |
> | Microsoft.Network/virtualNetworks/subnets/write | 创建虚拟网络子网，或更新现有的虚拟网络子网 |
> | Microsoft.Network/virtualNetworks/subnets/delete | 删除虚拟网络子网 |
> | Microsoft.Network/virtualNetworks/subnets/joinLoadBalancer/action | 将负载均衡器加入虚拟网络子网 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 加入虚拟网络。 不可发出警报。 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 不可发出警报。 |
> | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 通过应用必要的网络策略来准备子网 |
> | Microsoft.Network/virtualNetworks/subnets/unprepareNetworkPolicies/action | 通过删除应用的网络策略，取消对子网的准备 |
> | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/read | 获取上下文服务终结点策略 |
> | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/write | 创建上下文服务终结点策略，或更新现有的上下文服务终结点策略 |
> | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/delete | 删除上下文服务终结点策略 |
> | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | 获取资源导航链接定义 |
> | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | 创建资源导航链接，或更新现有资源导航链接 |
> | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | 删除资源导航链接 |
> | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | 获取服务关联链接定义 |
> | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | 创建服务关联链接或更新现有的服务关联链接 |
> | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | 删除服务关联链接 |
> | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | 验证服务关联链接 |
> | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | 获取服务关联链接详细信息定义 |
> | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 获取对虚拟网络子网中的所有虚拟机的引用 |
> | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | 获取标记的流量使用者定义 |
> | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | 创建标记的流量使用者，或更新现有标记的流量使用者 |
> | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | 删除标记的流量使用者 |
> | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | 验证标记的流量使用者 |
> | Microsoft.Network/virtualNetworks/usages/read | 获取虚拟网络的每个子网的 IP 使用情况 |
> | Microsoft.Network/virtualNetworks/virtualMachines/read | 获取对虚拟网络中的所有虚拟机的引用 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 获取虚拟网络对等互连定义 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 创建虚拟网络对等互连，或更新现有的虚拟网络对等互连 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 删除虚拟网络对等互连 |
> | Microsoft.Network/virtualNetworkTaps/read | 获取虚拟网络点击 |
> | Microsoft.Network/virtualNetworkTaps/join/action | 加入虚拟网络分支。 不可发出警报。 |
> | Microsoft.Network/virtualNetworkTaps/delete | 删除虚拟网络点击 |
> | Microsoft.Network/virtualNetworkTaps/write | 创建或更新虚拟网络点击 |
> | Microsoft.Network/virtualNetworkTaps/networkInterfaceTapConfigurationProxies/read | 获取网络接口 Tap 配置代理。 |
> | Microsoft.Network/virtualNetworkTaps/networkInterfaceTapConfigurationProxies/write | 创建网络接口 Tap 配置代理，或更新现有的网络接口 Tap 配置代理。 |
> | Microsoft.Network/virtualNetworkTaps/networkInterfaceTapConfigurationProxies/delete | 删除网络接口 Tap 配置代理。 |
> | Microsoft.Network/virtualRouters/read | 获取 VirtualRouter |
> | Microsoft.Network/virtualRouters/write | 创建 VirtualRouter 或更新现有的 VirtualRouter |
> | Microsoft.Network/virtualRouters/delete | 删除 VirtualRouter |
> | Microsoft.Network/virtualRouters/join/action | 加入 VirtualRouter。 不可发出警报。 |
> | Microsoft.Network/virtualRouters/peerings/read | 获取 VirtualRouterPeering |
> | Microsoft.Network/virtualRouters/peerings/write | 创建 VirtualRouterPeering 或更新现有的 VirtualRouterPeering |
> | Microsoft.Network/virtualRouters/peerings/delete | 删除 VirtualRouterPeering |
> | Microsoft.Network/virtualRouters/providers/Microsoft.Insights/metricDefinitions/read | 获取 VirtualRouter 的指标定义 |
> | Microsoft.Network/virtualWans/delete | 删除虚拟 WAN |
> | Microsoft.Network/virtualWans/read | 获取虚拟 WAN |
> | Microsoft.Network/virtualWans/write | 创建或更新虚拟 WAN |
> | Microsoft.Network/virtualwans/vpnconfiguration/action | 获取 VPN 配置 |
> | Microsoft.Network/virtualwans/vpnServerConfigurations/action | 获取 VirtualWanVpnServerConfigurations |
> | Microsoft.Network/virtualwans/generateVpnProfile/action | 生成 VirtualWanVpnServerConfiguration VpnProfile |
> | Microsoft.Network/virtualWans/updateVpnReferences/action | 更新 VirtualWan 中的 VPN 参考 |
> | Microsoft.Network/virtualWans/updateVhubReferences/action | 更新 VirtualWan 中的 VirtualHub 参考 |
> | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | 获取虚拟 WAN P2SVpnServerConfiguration |
> | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | 创建虚拟 Wan P2SVpnServerConfiguration，或更新现有的虚拟 Wan P2SVpnServerConfiguration |
> | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | 删除虚拟 Wan P2SVpnServerConfiguration |
> | Microsoft.Network/virtualwans/supportedSecurityProviders/read | 获取受支持的 VirtualWan 安全提供程序。 |
> | Microsoft.Network/virtualWans/virtualHubProxies/read | 获取虚拟中心代理定义 |
> | Microsoft.Network/virtualWans/virtualHubProxies/write | 创建虚拟中心代理，或更新虚拟中心代理 |
> | Microsoft.Network/virtualWans/virtualHubProxies/delete | 删除虚拟中心代理 |
> | Microsoft.Network/virtualWans/virtualHubs/read | 获取引用了某个虚拟 WAN 的所有虚拟中心。 |
> | Microsoft.Network/virtualWans/vpnSiteProxies/read | 获取 VPN 站点代理定义 |
> | Microsoft.Network/virtualWans/vpnSiteProxies/write | 创建一个 VPN 站点代理，或更新 VPN 站点代理 |
> | Microsoft.Network/virtualWans/vpnSiteProxies/delete | 删除 VPN 站点代理 |
> | Microsoft.Network/virtualWans/vpnSites/read | 获取引用了某个虚拟 WAN 的所有 VPN 站点。 |
> | Microsoft.Network/vpnGateways/read | 获取 VpnGateway。 |
> | Microsoft.Network/vpnGateways/write | 放置 VpnGateway。 |
> | Microsoft.Network/vpnGateways/delete | 删除 VpnGateway。 |
> | microsoft.network/vpngateways/reset/action | 重置 VpnGateway |
> | microsoft.network/vpngateways/startpacketcapture/action | 使用相应资源启动 VPN 网关数据包捕获 |
> | microsoft.network/vpngateways/stoppacketcapture/action | 使用 sasURL 停止 VPN 网关数据包捕获 |
> | microsoft.network/vpngateways/listvpnconnectionshealth/action | 获取某个 VpnGateway 上的所有或一部分连接的连接运行状况 |
> | microsoft.network/vpnGateways/natRules/read | 获取 NAT 规则资源 |
> | microsoft.network/vpnGateways/natRules/write | 放置 NAT 规则资源 |
> | microsoft.network/vpnGateways/natRules/delete | 删除 NAT 规则资源 |
> | Microsoft.Network/vpnGateways/providers/Microsoft.Insights/diagnosticSettings/read | 获取 VPN 网关诊断设置 |
> | Microsoft.Network/vpnGateways/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 VPN 网关诊断设置，此操作由 Insights 资源提供程序进行补充。 |
> | Microsoft.Network/vpnGateways/providers/Microsoft.Insights/logDefinitions/read | 获取 VPN 网关的事件 |
> | Microsoft.Network/vpnGateways/providers/Microsoft.Insights/metricDefinitions/read | 获取 VPN 网关的可用指标 |
> | microsoft.network/vpnGateways/vpnConnections/read | 获取 VpnConnection。 |
> | microsoft.network/vpnGateways/vpnConnections/write | 放置 VpnConnection。 |
> | microsoft.network/vpnGateways/vpnConnections/delete | 删除 VpnConnection。 |
> | microsoft.network/vpnGateways/vpnConnections/startpacketcapture/action | 在 VPN 连接中启动所选链接对象的数据包捕获 |
> | microsoft.network/vpnGateways/vpnConnections/stoppacketcapture/action | 在 VPN 连接中停止所选链接对象的数据包捕获 |
> | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/getikesas/action | 列出 VPN 链接连接 IKE 安全性关联 |
> | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/resetconnection/action | 重置 vWAN 的连接 |
> | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/read | 获取 VPN 链接连接 |
> | Microsoft.Network/vpnServerConfigurations/read | 获取 VpnServerConfiguration |
> | Microsoft.Network/vpnServerConfigurations/write | 创建或更新 VpnServerConfiguration |
> | Microsoft.Network/vpnServerConfigurations/delete | 删除 VpnServerConfiguration |
> | Microsoft.Network/vpnServerConfigurations/p2sVpnGatewayProxies/read | 获取 P2SVpnGateway 代理定义 |
> | Microsoft.Network/vpnServerConfigurations/p2sVpnGatewayProxies/write | 创建 P2SVpnGateway 代理，或更新 P2SVpnGateway 代理 |
> | Microsoft.Network/vpnServerConfigurations/p2sVpnGatewayProxies/delete | 删除 P2SVpnGateway 代理 |
> | Microsoft.Network/vpnsites/read | 获取 VPN 站点资源。 |
> | Microsoft.Network/vpnsites/write | 创建或更新 VPN 站点资源。 |
> | Microsoft.Network/vpnsites/delete | 删除 VPN 站点资源。 |
> | microsoft.network/vpnSites/vpnSiteLinks/read | 获取 VPN 站点链接 |

## <a name="storage"></a>存储

### <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

Azure 服务：经典部署模型存储

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ClassicStorage/register/action | 注册到经典存储 |
> | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | 检查存储帐户的可用性。 |
> | Microsoft.ClassicStorage/capabilities/read | 显示功能 |
> | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | 获取存储帐户的可用性。 |
> | Microsoft.ClassicStorage/disks/read | 返回存储帐户磁盘。 |
> | Microsoft.ClassicStorage/images/read | 返回映像。 |
> | Microsoft.ClassicStorage/images/operationstatuses/read | 获取映像操作状态。 |
> | Microsoft.ClassicStorage/operations/read | 获取经典存储操作 |
> | Microsoft.ClassicStorage/osImages/read | 返回操作系统映像。 |
> | Microsoft.ClassicStorage/osPlatformImages/read | 获取操作系统平台映像。 |
> | Microsoft.ClassicStorage/publicImages/read | 获取公共虚拟机映像。 |
> | Microsoft.ClassicStorage/quotas/read | 获取订阅的配额。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 返回包含给定帐户的存储帐户。 |
> | Microsoft.ClassicStorage/storageAccounts/write | 添加新的存储帐户。 |
> | Microsoft.ClassicStorage/storageAccounts/delete | 删除存储帐户。 |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出存储帐户的访问密钥。 |
> | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | 再生成存储帐户的现有访问密钥。 |
> | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | 验证存储帐户的迁移。 |
> | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | 准备存储帐户的迁移。 |
> | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | 提交存储帐户的迁移。 |
> | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | 中止存储帐户的迁移。 |
> | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | 返回存储帐户磁盘。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/write | 添加存储帐户磁盘。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/delete | 删除给定的存储帐户磁盘。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | 读取资源的操作状态。 |
> | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |
> | Microsoft.ClassicStorage/storageAccounts/images/read | 返回存储帐户映像。 （已弃用。 请使用“Microsoft.ClassicStorage/storageAccounts/vmImages”） |
> | Microsoft.ClassicStorage/storageAccounts/images/delete | 删除给定的存储帐户映像。 （已弃用。 请使用“Microsoft.ClassicStorage/storageAccounts/vmImages”） |
> | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | 返回存储帐户映像操作状态。 |
> | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | 读取资源的操作状态。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/read | 返回存储帐户操作系统映像。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/write | 添加给定的存储帐户操作系统映像。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 删除给定的存储帐户操作系统映像。 |
> | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |
> | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |
> | Microsoft.ClassicStorage/storageAccounts/services/read | 获取可用服务。 |
> | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 获取诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | 获取指标定义。 |
> | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | 获取指标。 |
> | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 返回虚拟机映像。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 添加给定的虚拟机映像。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 删除给定的虚拟机映像。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 获取给定的虚拟机映像操作状态。 |
> | Microsoft.ClassicStorage/vmImages/read | 列出虚拟机映像。 |

### <a name="microsoftdatabox"></a>Microsoft.DataBox

Azure 服务：[Azure Data Box](../databox/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DataBox/register/action | 注册提供程序 Microsoft.Databox |
> | Microsoft.DataBox/unregister/action | 取消注册提供程序 Microsoft.Databox |
> | Microsoft.DataBox/jobs/cancel/action | 取消正在进行的订单。 |
> | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 允许为退件预订提货。 |
> | Microsoft.DataBox/jobs/mitigate/action | 此方法有助于对具有解决代码的作业执行缓解操作 |
> | Microsoft.DataBox/jobs/markDevicesShipped/action |  |
> | Microsoft.DataBox/jobs/read | 列出或获取订单 |
> | Microsoft.DataBox/jobs/delete | 删除订单 |
> | Microsoft.DataBox/jobs/write | 创建或更新订单 |
> | Microsoft.DataBox/jobs/listCredentials/action | 列出与订单相关的未加密凭据。 |
> | Microsoft.DataBox/locations/validateInputs/action | 此方法执行所有类型的验证。 |
> | Microsoft.DataBox/locations/validateAddress/action | 验证送货地址，并提供备用地址（如有）。 |
> | Microsoft.DataBox/locations/availableSkus/action | 此方法返回可用 SKU 列表。 |
> | Microsoft.DataBox/locations/regionConfiguration/action | 此方法返回区域的配置。 |
> | Microsoft.DataBox/locations/availableSkus/read | 列出或获取可用 SKU |
> | Microsoft.DataBox/locations/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBox/operations/read | 列出或获取操作 |
> | Microsoft.DataBox/subscriptions/resourceGroups/moveResources/action | 此方法执行资源移动。 |
> | Microsoft.DataBox/subscriptions/resourceGroups/validateMoveResources/action | 此方法验证是否允许资源移动。 |

### <a name="microsoftdatashare"></a>Microsoft.DataShare

Azure 服务：[Azure Data Share](../data-share/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DataShare/register/action | 注册 Data Share 资源提供程序的订阅。 |
> | Microsoft.DataShare/unregister/action | 注销 Data Share 资源提供程序的订阅。 |
> | Microsoft.DataShare/accounts/read | 读取 Data Share 帐户。 |
> | Microsoft.DataShare/accounts/write | 写入 Data Share 帐户。 |
> | Microsoft.DataShare/accounts/delete | 删除 Data Share 帐户。 |
> | Microsoft.DataShare/accounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.DataShare/accounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.DataShare/accounts/providers/Microsoft.Insights/logDefinitions/read | 获取帐户的可用日志。 |
> | Microsoft.DataShare/accounts/providers/Microsoft.Insights/metricDefinitions/read | 获取帐户的可用指标。 |
> | Microsoft.DataShare/accounts/shares/read | 读取 Data Share 共享。 |
> | Microsoft.DataShare/accounts/shares/write | 写入 Data Share 共享。 |
> | Microsoft.DataShare/accounts/shares/delete | 删除 Data Share 共享。 |
> | Microsoft.DataShare/accounts/shares/listSynchronizations/action | 针对 Data Share ListSynchronization 的操作。 |
> | Microsoft.DataShare/accounts/shares/listSynchronizationDetails/action | 针对 Data Share ListSynchronization 详细信息的操作。 |
> | Microsoft.DataShare/accounts/shares/dataSets/read | 读取数据集。 |
> | Microsoft.DataShare/accounts/shares/dataSets/write | 创建 Data Share 数据集。 |
> | Microsoft.DataShare/accounts/shares/dataSets/delete | 删除 Data Share 数据集。 |
> | Microsoft.DataShare/accounts/shares/invitations/read | 读取 Data Share 邀请。 |
> | Microsoft.DataShare/accounts/shares/invitations/write | 写入 Data Share 邀请。 |
> | Microsoft.DataShare/accounts/shares/invitations/delete | 删除 Data Share 邀请。 |
> | Microsoft.DataShare/accounts/shares/operationResults/read | 读取 Data Share 共享。 |
> | Microsoft.DataShare/accounts/shares/providerShareSubscriptions/read | 读取 Data Share 提供程序 ShareSubscription。 |
> | Microsoft.DataShare/accounts/shares/providerShareSubscriptions/revoke/action | 撤销 Data Share 订阅。 |
> | Microsoft.DataShare/accounts/shares/providerShareSubscriptions/reinstate/action | 恢复 Data Share 订阅。 |
> | Microsoft.DataShare/accounts/shares/synchronizationSettings/read | 读取 Data Share 同步设置。 |
> | Microsoft.DataShare/accounts/shares/synchronizationSettings/write | 写入 Data Share 同步设置。 |
> | Microsoft.DataShare/accounts/shares/synchronizationSettings/delete | 删除 Data Share 同步设置。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/cancelSynchronization/action | 取消 Data Share 同步。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/delete | 删除 Data Share 共享订阅。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/listSourceShareSynchronizationSettings/action | 列出 Data Share 源共享 SynchronizationSettings。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/listSynchronizationDetails/action | 列出 Data Share 同步详细信息。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/listSynchronizations/action | 列出 Data Share 同步。 |
> | .DataShare/accounts/shareSubscriptions/read | 读取 Data Share ShareSubscription。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/synchronize/action | 初始化 Data Share 同步操作。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/write | 写入 Data Share ShareSubscription。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/consumerSourceDataSets/read | 读取 Data Share 使用者源数据集。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/dataSetMappings/delete | 删除 Data Share DataSetMapping。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/dataSetMappings/write | 写入 Data Share DataSetMapping。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/dataSetMappings/read | 读取 Data Share DataSetMapping。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/operationResults/read | 读取 Data Share ShareSubscription 长时间运行的操作的状态。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/shareSubscriptionSynchronizations/read | 读取 Data Share 共享订阅同步。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/synchronizationOperationResults/read | 读取 Data Share 同步操作结果。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/triggers/read | 读取 Data Share 触发器。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/triggers/write | 写入 Data Share 触发器。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/triggers/delete | 删除 Data Share 触发器。 |
> | Microsoft.DataShare/listInvitations/read | 在租户级别读取邀请。 |
> | Microsoft.DataShare/locations/rejectInvitation/action | 拒绝 Data Share 邀请。 |
> | Microsoft.DataShare/locations/consumerInvitations/read | 获取 Data Share 使用者邀请。 |
> | Microsoft.DataShare/locations/operationResults/read | 读取支持 Data Share 的位置。 |
> | Microsoft.DataShare/operations/read | 读取 Data Share 资源提供程序中的所有可用操作。 |

### <a name="microsoftimportexport"></a>Microsoft.ImportExport

Azure 服务：[Azure 导入/导出](../import-export/storage-import-export-service.md)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ImportExport/register/action | 注册导入/导出资源提供程序的订阅，并启用导入/导出作业的创建。 |
> | Microsoft.ImportExport/jobs/write | 使用指定的参数创建作业，或更新指定作业的属性或标记。 |
> | Microsoft.ImportExport/jobs/read | 获取指定作业的属性，或返回作业列表。 |
> | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 获取指定作业的 BitLocker 密钥。 |
> | Microsoft.ImportExport/jobs/delete | 删除现有的作业。 |
> | Microsoft.ImportExport/locations/read | 获取指定位置的属性，或返回位置列表。 |
> | Microsoft.ImportExport/operations/read | 获取资源提供程序支持的操作。 |

### <a name="microsoftnetapp"></a>Microsoft.NetApp

Azure 服务：[Azure NetApp 文件](../azure-netapp-files/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.NetApp/register/action | 订阅注册操作 |
> | Microsoft.NetApp/unregister/action | 从 Microsoft.NetApp 资源提供程序中取消注册订阅 |
> | Microsoft.NetApp/locations/read | 读取可用性检查资源。 |
> | Microsoft.NetApp/locations/checknameavailability/action | 检查资源名称是否可用 |
> | Microsoft.NetApp/locations/checkfilepathavailability/action | 检查文件路径是否可用 |
> | Microsoft.NetApp/locations/checkinventory/action | 检查 ReservedCapacity 清单。 |
> | Microsoft.NetApp/locations/operationresults/read | 读取操作结果资源。 |
> | Microsoft.NetApp/netAppAccounts/read | 读取帐户资源。 |
> | Microsoft.NetApp/netAppAccounts/write | 写入帐户资源。 |
> | Microsoft.NetApp/netAppAccounts/delete | 删除帐户资源。 |
> | Microsoft.NetApp/netAppAccounts/RenewCredentials/action | 如果帐户具有已到期需要续订的 MSI 凭据，请续订帐户的 MSI 凭据。 |
> | Microsoft.NetApp/netAppAccounts/accountBackups/read | 读取帐户备份资源。 |
> | Microsoft.NetApp/netAppAccounts/accountBackups/write | 写入帐户备份资源。 |
> | Microsoft.NetApp/netAppAccounts/accountBackups/delete | 删除帐户备份资源。 |
> | Microsoft.NetApp/netAppAccounts/backupPolicies/read | 读取备份策略资源。 |
> | Microsoft.NetApp/netAppAccounts/backupPolicies/write | 写入备份策略资源。 |
> | Microsoft.NetApp/netAppAccounts/backupPolicies/delete | 删除备份策略资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/read | 读取池资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/write | 写入池资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/delete | 删除池资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/logDefinitions/read | 获取资源的日志定义。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/metricDefinitions/read | 获取卷资源的可用指标。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | 读取卷资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | 写入卷资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | 删除卷资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/Revert/action | 将卷还原到特定快照 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/BreakReplication/action | 中断卷复制关系 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/action | 读取卷复制的状态。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReInitializeReplication/action | 尝试重新初始化未初始化的复制 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/AuthorizeReplication/action | 授权源卷复制 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ResyncReplication/action | 重新同步目标卷的复制 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/DeleteReplication/action | 删除目标卷上的复制 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/PoolChange/action | 将卷移到另一个池。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/read | 读取备份资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/write | 写入备份资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/delete | 删除备份资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/BackupStatus/read |  |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/MountTargets/read | 读取装入目标资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/providers/Microsoft.Insights/metricDefinitions/read | 获取卷资源的可用指标。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/read | 读取卷复制的状态。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/RestoreStatus/read |  |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | 读取快照资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | 写入快照资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | 删除快照资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/RestoreFiles/action | 从快照资源还原文件 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/subvolumes/read |  |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/subvolumes/write | 写入子卷资源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/subvolumes/delete |  |
> | Microsoft.NetApp/netAppAccounts/ipsecPolicies/read | 读取 IPSec 策略资源。 |
> | Microsoft.NetApp/netAppAccounts/ipsecPolicies/write | 写入 IPSec 策略资源。 |
> | Microsoft.NetApp/netAppAccounts/ipsecPolicies/delete | 删除 IPSec 策略资源。 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/read | 读取快照策略资源。 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/write | 写入快照策略资源。 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/delete | 删除快照策略资源。 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/Volumes/action | 列出已连接到快照策略的卷 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/ListVolumes/action | 列出已连接到快照策略的卷 |
> | Microsoft.NetApp/netAppAccounts/vaults/read | 读取保管库资源。 |
> | Microsoft.NetApp/netAppAccounts/volumeGroups/read | 读取卷组资源。 |
> | Microsoft.NetApp/netAppAccounts/volumeGroups/write | 写入卷组资源。 |
> | Microsoft.NetApp/netAppAccounts/volumeGroups/delete | 删除卷组资源。 |
> | Microsoft.NetApp/Operations/read | 读取操作资源。 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Azure 服务：[存储](../storage/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Storage/register/action | 注册存储资源提供程序的订阅，并启用存储帐户的创建。 |
> | Microsoft.Storage/checknameavailability/read | 检查帐户名称是否有效且未被使用。 |
> | Microsoft.Storage/deletedAccounts/read |  |
> | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 向 Microsoft.Storage 通知正在删除虚拟网络或子网 |
> | Microsoft.Storage/locations/checknameavailability/read | 检查帐户名称是否有效且未被使用。 |
> | Microsoft.Storage/locations/usages/read | 返回指定订阅中的资源的限制和当前使用计数 |
> | Microsoft.Storage/operations/read | 轮询异步操作的状态。 |
> | Microsoft.Storage/resilienciesProgressions/read |  |
> | Microsoft.Storage/skus/read | 列出 Microsoft.Storage 支持的 SKU。 |
> | Microsoft.Storage/storageAccounts/updateInternalProperties/action |  |
> | Microsoft.Storage/storageAccounts/hnsonmigration/action | 客户可以中止存储帐户上正在进行的 Hns 迁移 |
> | Microsoft.Storage/storageAccounts/hnsonmigration/action | 客户可以迁移到 hns 帐户类型 |
> | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | 将 Blob 范围还原到指定时间的状态 |
> | Microsoft.Storage/storageAccounts/PrivateEndpointConnectionsApproval/action | 审批专用终结点连接 |
> | Microsoft.Storage/storageAccounts/failover/action | 发生可用性问题时，客户能够控制故障转移 |
> | Microsoft.Storage/storageAccounts/listkeys/action | 返回指定存储帐户的访问密钥。 |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | 再生成指定存储帐户的访问密钥。 |
> | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | 撤销指定的存储帐户的所有用户委托密钥。 |
> | Microsoft.Storage/storageAccounts/delete | 删除现有的存储帐户。 |
> | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | Microsoft.Storage/storageAccounts/listAccountSas/action | 返回指定存储帐户的帐户 SAS 令牌。 |
> | Microsoft.Storage/storageAccounts/listServiceSas/action | 返回指定存储帐户的服务 SAS 令牌。 |
> | Microsoft.Storage/storageAccounts/write | 使用指定的参数创建存储帐户、更新指定存储帐户的属性或标记，或者为其添加自定义域。 |
> | Microsoft.Storage/storageAccounts/accountLocks/read |  |
> | Microsoft.Storage/storageAccounts/accountLocks/write |  |
> | Microsoft.Storage/storageAccounts/accountLocks/delete |  |
> | Microsoft.Storage/storageAccounts/blobServices/read | 列出 blob 服务 |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | 返回 blob 服务的用户委托密钥 |
> | Microsoft.Storage/storageAccounts/blobServices/write | 返回放置 blob 服务属性的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/read | 返回 blob 服务属性或统计信息 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/migrate/action |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | 返回修补 blob 容器的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 返回删除容器的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 返回容器 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 返回容器列表 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | 返回租用 blob 容器的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | 返回放置 blob 容器的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | 清除 blob 容器法定保留 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | 设置 blob 容器法定保留 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | 扩展 blob 容器不可变性策略 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | 删除 blob 容器不可变性策略 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | 放置 blob 容器不可变性策略 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | 锁定 blob 容器不可变性策略 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | 获取 blob 容器不可变性策略 |
> | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/logDefinitions/read | 获取 Blob 的日志定义 |
> | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft 存储指标定义列表。 |
> | Microsoft.Storage/storageAccounts/consumerDataSharePolicies/read |  |
> | Microsoft.Storage/storageAccounts/consumerDataSharePolicies/write |  |
> | Microsoft.Storage/storageAccounts/dataSharePolicies/delete |  |
> | Microsoft.Storage/storageAccounts/dataSharePolicies/read |  |
> | Microsoft.Storage/storageAccounts/dataSharePolicies/read |  |
> | Microsoft.Storage/storageAccounts/dataSharePolicies/write |  |
> | Microsoft.Storage/storageAccounts/encryptionScopes/read |  |
> | Microsoft.Storage/storageAccounts/encryptionScopes/read |  |
> | Microsoft.Storage/storageAccounts/encryptionScopes/write |  |
> | Microsoft.Storage/storageAccounts/encryptionScopes/write |  |
> | Microsoft.Storage/storageAccounts/fileServices/shares/action | 还原文件共享 |
> | Microsoft.Storage/storageAccounts/fileServices/read | 列出文件服务 |
> | Microsoft.Storage/storageAccounts/fileServices/write | 放置文件服务属性 |
> | Microsoft.Storage/storageAccounts/fileServices/read | 获取文件服务属性 |
> | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/logDefinitions/read | 获取文件的日志定义 |
> | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft 存储指标定义列表。 |
> | Microsoft.Storage/storageAccounts/fileServices/shares/delete | 删除文件共享 |
> | Microsoft.Storage/storageAccounts/fileServices/shares/read | 获取文件共享 |
> | Microsoft.Storage/storageAccounts/fileServices/shares/lease/action |  |
> | Microsoft.Storage/storageAccounts/fileServices/shares/read | 列出文件共享 |
> | Microsoft.Storage/storageAccounts/fileServices/shares/write | 创建或更新文件共享 |
> | Microsoft.Storage/storageAccounts/inventoryPolicies/delete |  |
> | Microsoft.Storage/storageAccounts/inventoryPolicies/read |  |
> | Microsoft.Storage/storageAccounts/inventoryPolicies/read |  |
> | Microsoft.Storage/storageAccounts/inventoryPolicies/write |  |
> | Microsoft.Storage/storageAccounts/localUsers/delete | 删除本地用户 |
> | Microsoft.Storage/storageAccounts/localusers/listKeys/action | 列出本地用户密钥 |
> | Microsoft.Storage/storageAccounts/localusers/read | 列出本地用户 |
> | Microsoft.Storage/storageAccounts/localusers/read | 获取本地用户 |
> | Microsoft.Storage/storageAccounts/localusers/write | 创建或更新本地用户 |
> | Microsoft.Storage/storageAccounts/managementPolicies/delete | 删除存储帐户管理策略 |
> | Microsoft.Storage/storageAccounts/managementPolicies/read | 获取存储管理帐户策略 |
> | Microsoft.Storage/storageAccounts/managementPolicies/write | 放置存储帐户管理策略 |
> | Microsoft.Storage/storageAccounts/objectReplicationPolicies/delete | 删除对象复制策略 |
> | Microsoft.Storage/storageAccounts/objectReplicationPolicies/read | 获取对象复制策略 |
> | Microsoft.Storage/storageAccounts/objectReplicationPolicies/read | 列出对象复制策略 |
> | Microsoft.Storage/storageAccounts/objectReplicationPolicies/write | 创建或更新对象复制策略 |
> | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | 放置专用终结点连接代理 |
> | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | 列出专用终结点连接 |
> | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | 放置专用终结点连接 |
> | Microsoft.Storage/storageAccounts/privateLinkResources/read | 获取 StorageAccount groupids |
> | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft 存储指标定义列表。 |
> | Microsoft.Storage/storageAccounts/queueServices/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/write |  |
> | Microsoft.Storage/storageAccounts/queueServices/read | 获取队列服务属性 |
> | Microsoft.Storage/storageAccounts/queueServices/read | 返回队列服务属性或统计信息。 |
> | Microsoft.Storage/storageAccounts/queueServices/write | 返回设置队列服务属性的结果 |
> | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/logDefinitions/read | 获取队列的日志定义 |
> | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft 存储指标定义列表。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | 返回队列或队列列表。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | 返回写入队列的结果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 返回删除队列的结果 |
> | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | 创建/更新存储帐户诊断设置。 |
> | Microsoft.Storage/storageAccounts/tableServices/read |  |
> | Microsoft.Storage/storageAccounts/tableServices/read | 获取表服务属性 |
> | Microsoft.Storage/storageAccounts/tableServices/write |  |
> | Microsoft.Storage/storageAccounts/tableServices/read | 获取表服务属性或统计信息 |
> | Microsoft.Storage/storageAccounts/tableServices/write | 设置表服务属性 |
> | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/logDefinitions/read | 获取表的日志定义 |
> | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft 存储指标定义列表。 |
> | Microsoft.Storage/storageAccounts/tableServices/tables/delete |  |
> | Microsoft.Storage/storageAccounts/tableServices/tables/read |  |
> | Microsoft.Storage/storageAccounts/tableServices/tables/read |  |
> | Microsoft.Storage/storageAccounts/tableServices/tables/write |  |
> | Microsoft.Storage/storageAccounts/tableServices/tables/read | 查询表 |
> | Microsoft.Storage/storageAccounts/tableServices/tables/write | 创建表 |
> | Microsoft.Storage/storageAccounts/tableServices/tables/delete | 删除表 |
> | Microsoft.Storage/usages/read | 返回指定订阅中的资源的限制和当前使用计数 |
> | **DataAction** | **说明** |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | 返回 blob 或 blob 列表 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | 返回写入 blob 的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | 返回删除 blob 的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action | 返回删除 Blob 版本的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | 返回添加 blob 内容的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | 返回具有匹配标记筛选器的帐户下的 blob 列表 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action | 将 Blob 从一个路径移到另一个路径 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action | 更改 Blob 的所有权 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action | 修改 Blob 的权限 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | 返回 blob 命令的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/immutableStorage/runAsSuperUser/action |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | 返回读取 blob 标记的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | 返回写入 blob 标记的结果 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | 返回某个文件/文件夹，或文件/文件夹列表 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | 返回写入文件或创建文件夹的结果 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | 返回删除文件/文件夹的结果 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | 返回修改文件/文件夹权限的结果 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | 获取文件管理员特权 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 返回消息 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 返回编写消息的结果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 返回删除消息的结果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 返回添加消息的结果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 返回处理消息的结果 |
> | Microsoft.Storage/storageAccounts/tableServices/tables/entities/read | 查询表实体 |
> | Microsoft.Storage/storageAccounts/tableServices/tables/entities/write | 插入、合并或替换表实体 |
> | Microsoft.Storage/storageAccounts/tableServices/tables/entities/delete | 删除表实体 |
> | Microsoft.Storage/storageAccounts/tableServices/tables/entities/add/action | 插入表实体 |
> | Microsoft.Storage/storageAccounts/tableServices/tables/entities/update/action | 合并或更新表实体 |

### <a name="microsoftstoragesync"></a>microsoft.storagesync

Azure 服务：[存储](../storage/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.storagesync/register/action | 注册存储同步提供程序的订阅 |
> | microsoft.storagesync/unregister/action | 取消注册存储同步提供程序的订阅 |
> | microsoft.storagesync/locations/checkNameAvailability/action | 检查该存储同步服务名称是否有效且未被使用。 |
> | microsoft.storagesync/locations/operationresults/read | 获取异步操作的结果 |
> | microsoft.storagesync/locations/operations/read | 获取 Azure 异步操作的状态 |
> | microsoft.storagesync/locations/workflows/operations/read | 获取异步操作的状态 |
> | microsoft.storagesync/operations/read | 获取支持的操作列表 |
> | microsoft.storagesync/storageSyncServices/read | 读取任何存储同步服务 |
> | microsoft.storagesync/storageSyncServices/write | 创建或更新任何存储同步服务 |
> | microsoft.storagesync/storageSyncServices/delete | 删除任何存储同步服务 |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/validate/action | 验证任何专用终结点 ConnectionProxies |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/read | 读取任何专用终结点 ConnectionProxies |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/write | 创建或更新任何专用终结点 ConnectionProxies |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/delete | 删除任何专用终结点 ConnectionProxies |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/read | 读取任何专用终结点连接 |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/write | 创建或更新任何专用终结点连接 |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/delete | 删除任何专用终结点连接 |
> | microsoft.storagesync/storageSyncServices/privateLinkResources/read | 读取任何专用链接资源 |
> | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | 获取存储同步服务的可用指标 |
> | microsoft.storagesync/storageSyncServices/registeredServers/read | 读取任何已注册服务器 |
> | microsoft.storagesync/storageSyncServices/registeredServers/write | 创建或更新任何已注册服务器 |
> | microsoft.storagesync/storageSyncServices/registeredServers/delete | 删除任何已注册服务器 |
> | microsoft.storagesync/storageSyncServices/syncGroups/read | 读取任何同步组 |
> | microsoft.storagesync/storageSyncServices/syncGroups/write | 创建或更新任何同步组 |
> | microsoft.storagesync/storageSyncServices/syncGroups/delete | 删除任何同步组 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | 读取任何云终结点 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | 创建或更新任何云终结点 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | 删除任何云终结点 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | 备份前调用此操作 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | 备份后调用此操作 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 存储前调用此操作 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 存储后调用此操作 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | 还原检测信号 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/action | 调用此操作可以触发云终结点的文件共享的更改检测 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 获取异步备份/还原操作的状态 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | 读取任何服务器终结点 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | 创建或更新任何服务器终结点 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | 删除任何服务器终结点 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | 调用此操作，将文件撤回到服务器 |
> | microsoft.storagesync/storageSyncServices/workflows/read | 读取工作流 |
> | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 获取异步操作的状态 |
> | microsoft.storagesync/storageSyncServices/workflows/operations/read | 获取异步操作的状态 |

### <a name="microsoftstorsimple"></a>Microsoft.StorSimple

Azure 服务：[StorSimple](../storsimple/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.StorSimple/register/action | 注册提供程序 Microsoft.StorSimple |
> | Microsoft.StorSimple/managers/clearAlerts/action | 清除与设备管理器关联的所有警报。 |
> | Microsoft.StorSimple/managers/getEncryptionKey/action | 获取设备管理器的加密密钥。 |
> | Microsoft.StorSimple/managers/read | 列出或获取设备管理器 |
> | Microsoft.StorSimple/managers/delete | 删除设备管理器 |
> | Microsoft.StorSimple/managers/write | 创建或更新设备管理器 |
> | Microsoft.StorSimple/managers/configureDevice/action | 配置设备 |
> | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | 从经典模型迁移到 Resource Manager 模型 |
> | Microsoft.StorSimple/managers/listActivationKey/action | 获取 StorSimple Device Manager 的激活密钥。 |
> | Microsoft.StorSimple/managers/regenerateActivationKey/action | 重新生成现有 StorSimple 设备管理器的激活密钥。 |
> | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | 列出 StorSimple Device Manager 的加密公钥。 |
> | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 创建新的云设备。 |
> | Microsoft.StorSimple/Managers/write | “创建保管库”操作创建“vault”类型的 Azure 资源 |
> | Microsoft.StorSimple/Managers/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.StorSimple/Managers/delete | “删除保管库”操作删除“vault”类型的指定 Azure 资源 |
> | Microsoft.StorSimple/managers/accessControlRecords/read | 列出或获取访问控制记录 |
> | Microsoft.StorSimple/managers/accessControlRecords/write | 创建或更新访问控制记录 |
> | Microsoft.StorSimple/managers/accessControlRecords/delete | 删除访问控制记录 |
> | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/alerts/read | 列出或获取警报 |
> | Microsoft.StorSimple/managers/backups/read | 列出或获取备份集 |
> | Microsoft.StorSimple/managers/bandwidthSettings/read | 列出带宽设置（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/bandwidthSettings/write | 新建或更新带宽设置（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/bandwidthSettings/delete | 删除现有的带宽设置（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/certificates/write | 创建或更新证书 |
> | Microsoft.StorSimple/Managers/certificates/write | “更新资源证书”操作更新资源/保管库凭据证书。 |
> | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | 列出云设备支持的配置 |
> | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 将测试警报电子邮件发送到配置的电子邮件收件人。 |
> | Microsoft.StorSimple/managers/devices/scanForUpdates/action | 扫描设备中的更新。 |
> | Microsoft.StorSimple/managers/devices/download/action | 下载设备的更新。 |
> | Microsoft.StorSimple/managers/devices/install/action | 在设备上安装更新。 |
> | Microsoft.StorSimple/managers/devices/read | 列出或获取设备 |
> | Microsoft.StorSimple/managers/devices/write | 创建或更新设备 |
> | Microsoft.StorSimple/managers/devices/delete | 删除设备 |
> | Microsoft.StorSimple/managers/devices/deactivate/action | 停用设备。 |
> | Microsoft.StorSimple/managers/devices/failover/action | 设备故障转移。 |
> | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | 发布现有设备的支持包。 StorSimple 支持包是一种易于使用的机制，用于收集所有相关日志，协助 Microsoft 支持部门排除 StorSimple 设备问题。 |
> | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | 为设备的服务加密密钥滚动更新授权 |
> | Microsoft.StorSimple/managers/devices/installUpdates/action | 在设备（仅限 8000 系列）上安装更新。 |
> | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 列出现有设备（仅限 8000 系列）的故障转移集。 |
> | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | 列出设备（仅限 8000 系列）的故障转移目标。 |
> | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | 列出设备管理器的加密公钥 |
> | Microsoft.StorSimple/managers/devices/alertSettings/read | 列出或获取警报设置 |
> | Microsoft.StorSimple/managers/devices/alertSettings/write | 创建或更新警报设置 |
> | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/write | 新建或更新备份策略（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/devices/backupPolicies/read | 列出备份策略（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 删除现有的备份策略（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 执行手动备份以创建受策略保护的所有卷的按需备份。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 新建或更新计划 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | 列出计划 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 删除现有的计划 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/backups/read | 列出或获取备份集 |
> | Microsoft.StorSimple/managers/devices/backups/delete | 删除备份集 |
> | Microsoft.StorSimple/managers/devices/backups/restore/action | 从备份集还原所有卷。 |
> | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | 使用备份元素克隆共享或卷。 |
> | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | 列出或获取备份计划组 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | 创建或更新备份计划组 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | 删除备份计划组 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/chapSettings/write | 创建或更新 Chap 设置 |
> | Microsoft.StorSimple/managers/devices/chapSettings/read | 列出或获取 Chap 设置 |
> | Microsoft.StorSimple/managers/devices/chapSettings/delete | 删除 Chap 设置 |
> | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/disks/read | 列出或获取磁盘 |
> | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/failoverTargets/read | 列出或获取设备的故障转移目标 |
> | Microsoft.StorSimple/managers/devices/fileservers/read | 列出或获取文件服务器 |
> | Microsoft.StorSimple/managers/devices/fileservers/write | 创建或更新文件服务器 |
> | Microsoft.StorSimple/managers/devices/fileservers/delete | 删除文件服务器 |
> | Microsoft.StorSimple/managers/devices/fileservers/backup/action | 备份文件服务器。 |
> | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | 列出或获取指标 |
> | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | 列出或获取指标定义 |
> | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 创建或更新共享 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 列出或获取共享 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 删除共享 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | 列出或获取指标 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | 列出或获取指标定义 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | 列出硬件组件组 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | 更改硬件组件组的控制器电源状态 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/read | 列出或获取 iSCSI 服务器 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/write | 创建或更新 iSCSI 服务器 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/delete | 删除 iSCSI 服务器 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | 备份 iSCSI 服务器。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | 列出或获取磁盘 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | 创建或更新磁盘 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | 删除磁盘 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | 列出或获取指标 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | 列出或获取指标定义 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | 列出或获取指标 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | 列出或获取指标定义 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/jobs/read | 列出或获取作业 |
> | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 取消正在运行的作业 |
> | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/metrics/read | 列出或获取指标 |
> | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | 列出或获取指标定义 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 导入要迁移的源配置 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 启动作业来估计迁移过程的持续时间。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | 使用源配置开始迁移 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 确认成功迁移并提交结果。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 提取迁移估计作业的状态。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 提取迁移的状态。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 提取迁移的确认状态。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 列出确认迁移状态 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 列出迁移估算 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 列出迁移状态 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/networkSettings/read | 列出或获取网络设置 |
> | Microsoft.StorSimple/managers/devices/networkSettings/write | 新建或更新网络设置 |
> | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/securitySettings/update/action | 更新安全设置。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/read | 列出安全设置 |
> | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | 同步设备的远程管理证书。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/write | 新建或更新安全设置 |
> | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/shares/read | 列出或获取共享 |
> | Microsoft.StorSimple/managers/devices/timeSettings/read | 列出或获取时间设置 |
> | Microsoft.StorSimple/managers/devices/timeSettings/write | 新建或更新时间设置 |
> | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/devices/updateSummary/read | 列出或获取更新摘要 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/write | 新建或更新卷容器（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/devices/volumeContainers/read | 列出卷容器（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 删除现有的卷容器（仅适用于 8000 系列） |
> | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | 列出指标 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | 列出指标定义 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | 列出卷 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 新建或更新卷 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 删除现有的卷 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | 列出指标 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | 列出指标定义 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 列出操作结果 |
> | Microsoft.StorSimple/managers/devices/volumes/read | 列出卷 |
> | Microsoft.StorSimple/managers/encryptionSettings/read | 列出或获取加密设置 |
> | Microsoft.StorSimple/managers/extendedInformation/read | 列出或获取扩展保管库信息 |
> | Microsoft.StorSimple/managers/extendedInformation/write | 创建或更新扩展保管库信息 |
> | Microsoft.StorSimple/managers/extendedInformation/delete | 删除扩展保管库信息 |
> | Microsoft.StorSimple/Managers/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | Microsoft.StorSimple/Managers/extendedInformation/write | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | Microsoft.StorSimple/Managers/extendedInformation/delete | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | Microsoft.StorSimple/managers/features/read | 列出功能 |
> | Microsoft.StorSimple/managers/fileservers/read | 列出或获取文件服务器 |
> | Microsoft.StorSimple/managers/iscsiservers/read | 列出或获取 iSCSI 服务器 |
> | Microsoft.StorSimple/managers/jobs/read | 列出或获取作业 |
> | Microsoft.StorSimple/managers/metrics/read | 列出或获取指标 |
> | Microsoft.StorSimple/managers/metricsDefinitions/read | 列出或获取指标定义 |
> | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 列出迁移源配置（仅限 8000 系列） |
> | Microsoft.StorSimple/managers/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/write | 创建或更新存储帐户凭据 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/read | 列出或获取存储帐户凭据 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/delete | 删除存储帐户凭据 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/managers/storageDomains/read | 列出或获取存储域 |
> | Microsoft.StorSimple/managers/storageDomains/write | 创建或更新存储域 |
> | Microsoft.StorSimple/managers/storageDomains/delete | 删除存储域 |
> | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 列出或获取操作结果 |
> | Microsoft.StorSimple/operations/read | 列出或获取操作 |

## <a name="web"></a>Web

### <a name="microsoftappplatform"></a>Microsoft.AppPlatform

Azure 服务：[Azure Spring Cloud](../spring-cloud/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.AppPlatform/register/action | 将订阅注册到 Microsoft.AppPlatform 资源提供程序 |
> | Microsoft.AppPlatform/unregister/action | 从 Microsoft.AppPlatform 资源提供程序中注销订阅 |
> | Microsoft.AppPlatform/locations/checkNameAvailability/action | 检查资源名称可用性 |
> | Microsoft.AppPlatform/locations/operationResults/Spring/read | 读取资源操作结果 |
> | Microsoft.AppPlatform/locations/operationStatus/operationId/read | 读取资源操作状态 |
> | Microsoft.AppPlatform/operations/read | 列出 Microsoft Azure Spring Cloud 的可用操作 |
> | Microsoft.AppPlatform/skus/read | 列出 Microsoft Azure Spring Cloud 的可用 sku |
> | Microsoft.AppPlatform/Spring/write | 创建或更新特定的 Azure Spring Cloud 服务实例 |
> | Microsoft.AppPlatform/Spring/delete | 删除特定的 Azure Spring Cloud 服务实例 |
> | Microsoft.AppPlatform/Spring/read | 获取 Azure Spring Cloud 服务实例 |
> | Microsoft.AppPlatform/Spring/enableTestEndpoint/action | 为特定的 Azure Spring Cloud 服务实例启用测试终结点功能 |
> | Microsoft.AppPlatform/Spring/disableTestEndpoint/action | 为特定的 Azure Spring Cloud 服务实例禁用测试终结点功能 |
> | Microsoft.AppPlatform/Spring/listTestKeys/action | 列出特定的 Azure Spring Cloud 服务实例的测试密钥 |
> | Microsoft.AppPlatform/Spring/regenerateTestKey/action | 重新生成特定的 Azure Spring Cloud 服务实例的测试密钥 |
> | Microsoft.AppPlatform/Spring/apps/write | 创建或更新特定的 Azure Spring Cloud 服务实例的应用程序 |
> | Microsoft.AppPlatform/Spring/apps/delete | 删除特定的 Azure Spring Cloud 服务实例的应用程序 |
> | Microsoft.AppPlatform/Spring/apps/read | 获取特定的 Azure Spring Cloud 服务实例的应用程序 |
> | Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action | 获取特定 Microsoft Azure Spring Cloud 应用程序的资源上传 URL |
> | Microsoft.AppPlatform/Spring/apps/validateDomain/action | 验证特定应用程序的自定义域 |
> | Microsoft.AppPlatform/Spring/apps/bindings/write | 创建或更新特定应用程序的绑定 |
> | Microsoft.AppPlatform/Spring/apps/bindings/delete | 删除特定应用程序的绑定 |
> | Microsoft.AppPlatform/Spring/apps/bindings/read | 获取特定应用程序的绑定 |
> | Microsoft.AppPlatform/Spring/apps/deployments/write | 创建或更新特定应用程序的部署 |
> | Microsoft.AppPlatform/Spring/apps/deployments/delete | 删除特定应用程序的部署 |
> | Microsoft.AppPlatform/Spring/apps/deployments/read | 获取特定应用程序的部署 |
> | Microsoft.AppPlatform/Spring/apps/deployments/start/action | 开始特定应用程序的部署 |
> | Microsoft.AppPlatform/Spring/apps/deployments/stop/action | 停止特定应用程序的部署 |
> | Microsoft.AppPlatform/Spring/apps/deployments/restart/action | 重新开始特定应用程序的部署 |
> | Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action | 获取特定 Microsoft Azure Spring Cloud 应用程序部署的日志文件 URL |
> | Microsoft.AppPlatform/Spring/apps/deployments/skus/read | 列出应用程序部署的可用 sku |
> | Microsoft.AppPlatform/Spring/apps/domains/write | 创建或更新特定应用程序的自定义域 |
> | Microsoft.AppPlatform/Spring/apps/domains/delete | 删除特定应用程序的自定义域 |
> | Microsoft.AppPlatform/Spring/apps/domains/read | 获取特定应用程序的自定义域 |
> | Microsoft.AppPlatform/Spring/certificates/write | 创建或更新特定的 Azure Spring Cloud 服务实例的证书 |
> | Microsoft.AppPlatform/Spring/certificates/delete | 删除特定的 Azure Spring Cloud 服务实例的证书 |
> | Microsoft.AppPlatform/Spring/certificates/read | 获取特定的 Azure Spring Cloud 服务实例的证书 |
> | Microsoft.AppPlatform/Spring/configServers/read | 获取特定的 Azure Spring Cloud 服务实例的配置服务器 |
> | Microsoft.AppPlatform/Spring/configServers/write | 创建或更新特定的 Azure Spring Cloud 服务实例的配置服务器 |
> | Microsoft.AppPlatform/Spring/deployments/read | 获取特定的 Azure Spring Cloud 服务实例的部署 |
> | Microsoft.AppPlatform/Spring/detectors/read | 获取特定的 Azure Spring Cloud 服务实例的检测器 |
> | Microsoft.AppPlatform/Spring/monitoringSettings/read | 获取特定的 Azure Spring Cloud 服务实例的监视设置 |
> | Microsoft.AppPlatform/Spring/monitoringSettings/write | 创建或更新特定的 Azure Spring Cloud 服务实例的监视设置 |
> | Microsoft.AppPlatform/Spring/providers/Microsoft.Insights/diagnosticSettings/read | 获取特定 Azure Spring Cloud 服务实例的诊断设置 |
> | Microsoft.AppPlatform/Spring/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新特定 Azure Spring Cloud 服务实例的诊断设置 |
> | Microsoft.AppPlatform/Spring/providers/Microsoft.Insights/logDefinitions/read | 从 Azure Spring Cloud 服务实例获取日志定义 |
> | Microsoft.AppPlatform/Spring/providers/Microsoft.Insights/metricDefinitions/read | 从 Azure Spring Cloud 服务实例获取指标定义 |
> | **DataAction** | **说明** |
> | Microsoft.AppPlatform/Spring/configService/read | 读取特定的 Azure Spring Cloud 服务实例的配置内容（例如 application.yaml） |
> | Microsoft.AppPlatform/Spring/eurekaService/read | 读取特定的 Azure Spring Cloud 服务实例的用户应用注册信息 |
> | Microsoft.AppPlatform/Spring/eurekaService/write | 写入特定的 Azure Spring Cloud 服务实例的用户应用注册信息 |
> | Microsoft.AppPlatform/Spring/eurekaService/delete | 删除特定的 Azure Spring Cloud 服务实例的用户应用注册信息 |
> | Microsoft.AppPlatform/Spring/logstreamService/read | 读取特定的 Azure Spring Cloud 服务实例的用户应用的流式传输日志 |

### <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

Azure 服务：[应用服务证书](../app-service/configure-ssl-certificate.md#import-an-app-service-certificate)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | 为服务应用主体预配 AKSCluster 服务主体 |
> | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 验证证书购买对象但不提交该对象 |
> | Microsoft.CertificateRegistration/register/action | 注册订阅的 Microsoft 证书资源提供程序 |
> | Microsoft.CertificateRegistration/certificateOrders/Write | 添加新的或更新现有的 certificateOrder |
> | Microsoft.CertificateRegistration/certificateOrders/Delete | 删除现有的 AppServiceCertificate |
> | Microsoft.CertificateRegistration/certificateOrders/Read | 获取 CertificateOrders 列表 |
> | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 重新颁发现有的 certificateorder |
> | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 续订现有的 certificateorder |
> | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 检索证书操作的列表 |
> | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 检索证书电子邮件历史记录 |
> | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 重新发送证书电子邮件 |
> | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | 验证域所有权 |
> | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 将请求电子邮件重新发送到另一个电子邮件地址 |
> | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 检索颁发的应用服务证书的站点封印 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 添加新的证书，或更新现有的证书 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 删除现有证书 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 获取证书列表 |
> | Microsoft.CertificateRegistration/operations/Read | 列出应用服务证书注册的所有操作 |

### <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

Azure 服务：[应用服务](../app-service/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DomainRegistration/generateSsoRequest/Action | 生成登录域控制中心的请求。 |
> | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | 验证域购买对象但不提交该对象 |
> | Microsoft.DomainRegistration/checkDomainAvailability/Action | 检查是否可购买某个域 |
> | Microsoft.DomainRegistration/listDomainRecommendations/Action | 根据关键字检索列表域建议 |
> | Microsoft.DomainRegistration/register/action | 注册订阅的 Microsoft 域资源提供程序 |
> | Microsoft.DomainRegistration/domains/Read | 获取域的列表 |
> | Microsoft.DomainRegistration/domains/Read | 获取域 |
> | Microsoft.DomainRegistration/domains/Write | 添加新域，或更新现有域 |
> | Microsoft.DomainRegistration/domains/Delete | 删除现有域。 |
> | Microsoft.DomainRegistration/domains/renew/Action | 续订现有域。 |
> | Microsoft.DomainRegistration/domains/Read | 将域移出到另一个注册器。 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 列出所有权标识符 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 获取所有权标识符 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 创建或更新标识符 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 删除所有权标识符 |
> | Microsoft.DomainRegistration/domains/operationresults/Read | 获取域操作 |
> | Microsoft.DomainRegistration/operations/Read | 列出应用服务域注册的所有操作 |
> | Microsoft.DomainRegistration/topLevelDomains/Read | 获取顶级域 |
> | Microsoft.DomainRegistration/topLevelDomains/Read | 获取顶级域 |
> | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 列出协议操作 |

### <a name="microsoftmaps"></a>Microsoft.Maps

Azure 服务：[Azure Maps](../azure-maps/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Maps/unregister/action | 取消注册 Maps 提供程序 |
> | Microsoft.Maps/register/action | 注册提供程序 |
> | Microsoft.Maps/accounts/write | 创建或更新 Maps 帐户。 |
> | Microsoft.Maps/accounts/read | 获取 Maps 帐户。 |
> | Microsoft.Maps/accounts/delete | 删除 Maps 帐户。 |
> | Microsoft.Maps/accounts/listKeys/action | 列出 Maps 帐户密钥。 |
> | Microsoft.Maps/accounts/regenerateKey/action | 生成新的 Maps 帐户主要或辅助密钥。 |
> | Microsoft.Maps/accounts/creators/write | 创建或更新 Creator。 |
> | Microsoft.Maps/accounts/creators/read | 获取 Creator。 |
> | Microsoft.Maps/accounts/creators/delete | 删除 Creator。 |
> | Microsoft.Maps/accounts/eventGridFilters/delete | 删除事件网格筛选器。 |
> | Microsoft.Maps/accounts/eventGridFilters/read | 获取事件网格筛选器 |
> | Microsoft.Maps/accounts/eventGridFilters/write | 创建或更新事件网格筛选器。 |
> | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | 获取 Maps 帐户的可用指标 |
> | Microsoft.Maps/operations/read | 读取提供程序操作 |
> | Microsoft.Maps/resourceTypes/read | 读取提供程序 resourceTypes |
> | **DataAction** | **说明** |
> | Microsoft.Maps/accounts/services/analytics/read | 允许读取用于分析服务的数据。 |
> | Microsoft.Maps/accounts/services/analytics/delete | 允许删除用于分析服务的数据。 |
> | Microsoft.Maps/accounts/services/analytics/write | 允许写入用于分析服务的数据。 |
> | Microsoft.Maps/accounts/services/data/read | 允许读取数据上传服务和 Private Atlas 的数据。 |
> | Microsoft.Maps/accounts/services/data/delete | 允许删除数据上传服务和 Private Atlas 的数据 |
> | Microsoft.Maps/accounts/services/data/write | 允许为数据上传服务和 Private Atlas 写入或更新数据。 |
> | Microsoft.Maps/accounts/services/dataordering/read | 允许读取用于数据排序服务的数据。 |
> | Microsoft.Maps/accounts/services/dataordering/write | 允许写入用于数据排序服务的数据。 |
> | Microsoft.Maps/accounts/services/elevation/read | 允许读取高程服务的数据。 |
> | Microsoft.Maps/accounts/services/geolocation/read | 允许读取地理位置服务的数据。 |
> | Microsoft.Maps/accounts/services/mobility/read | 允许读取出行服务的数据。 |
> | Microsoft.Maps/accounts/services/render/read | 允许读取呈现服务的数据。 |
> | Microsoft.Maps/accounts/services/route/read | 允许读取路由服务的数据。 |
> | Microsoft.Maps/accounts/services/search/read | 允许读取搜索服务的数据。 |
> | Microsoft.Maps/accounts/services/spatial/read | 允许读取空间服务的数据。 |
> | Microsoft.Maps/accounts/services/spatial/write | 允许为空间服务（例如事件发布）写入数据。 |
> | Microsoft.Maps/accounts/services/timezone/read | 允许读取时区服务的数据。 |
> | Microsoft.Maps/accounts/services/traffic/read | 允许读取交通服务的数据。 |
> | Microsoft.Maps/accounts/services/turnbyturn/read | 允许读取 TurnByTurn 服务的数据。 |
> | Microsoft.Maps/accounts/services/weather/read | 允许读取天气服务的数据。 |

### <a name="microsoftmedia"></a>Microsoft.Media

Azure 服务：[媒体服务](../media-services/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Media/register/action | 注册媒体服务资源提供程序的订阅，并启用媒体服务帐户的创建 |
> | Microsoft.Media/unregister/action | 取消注册媒体服务资源提供程序的订阅 |
> | Microsoft.Media/checknameavailability/action | 检查媒体服务帐户名称是否可用 |
> | Microsoft.Media/locations/checkNameAvailability/action | 检查媒体服务帐户名称是否可用 |
> | Microsoft.Media/mediaservices/read | 读取任何媒体服务帐户 |
> | Microsoft.Media/mediaservices/write | 创建或更新任何媒体服务帐户 |
> | Microsoft.Media/mediaservices/delete | 删除任何媒体服务帐户 |
> | Microsoft.Media/mediaservices/regenerateKey/action | 生成媒体服务 ACS 密钥 |
> | Microsoft.Media/mediaservices/listKeys/action | 列出媒体服务帐户的 ACS 密钥 |
> | Microsoft.Media/mediaservices/syncStorageKeys/action | 同步附加的 Azure 存储帐户的存储密钥 |
> | Microsoft.Media/mediaservices/listEdgePolicies/action | 列出边缘设备的策略。 |
> | Microsoft.Media/mediaservices/PrivateEndpointConnectionsApproval/action | 审批专用终结点连接 |
> | Microsoft.Media/mediaservices/accountfilters/read | 读取任何帐户筛选器 |
> | Microsoft.Media/mediaservices/accountfilters/write | 创建或更新任何帐户筛选器 |
> | Microsoft.Media/mediaservices/accountfilters/delete | 删除任何帐户筛选器 |
> | Microsoft.Media/mediaservices/assets/read | 读取任何资产 |
> | Microsoft.Media/mediaservices/assets/write | 创建或更新任何资产 |
> | Microsoft.Media/mediaservices/assets/delete | 删除任何资产 |
> | Microsoft.Media/mediaservices/assets/listContainerSas/action | 列出资产容器 SAS URL |
> | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 获取资产加密密钥 |
> | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 列出资产的流式处理定位符 |
> | Microsoft.Media/mediaservices/assets/assetfilters/read | 读取任何资产筛选器 |
> | Microsoft.Media/mediaservices/assets/assetfilters/write | 创建或更新任何资产筛选器 |
> | Microsoft.Media/mediaservices/assets/assetfilters/delete | 删除任何资产筛选器 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/read | 读取任何内容密钥策略 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/write | 创建或更新任何内容密钥策略 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/delete | 删除任何内容密钥策略 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 获取包含机密的策略属性 |
> | Microsoft.Media/mediaservices/eventGridFilters/read | 读取任何事件网格筛选器 |
> | Microsoft.Media/mediaservices/eventGridFilters/write | 创建或更新任何事件网格筛选器 |
> | Microsoft.Media/mediaservices/eventGridFilters/delete | 删除任何事件网格筛选器 |
> | Microsoft.Media/mediaservices/liveEventOperations/read | 读取任何直播活动操作 |
> | Microsoft.Media/mediaservices/liveEvents/read | 读取任何直播活动 |
> | Microsoft.Media/mediaservices/liveEvents/write | 创建或更新任何直播活动 |
> | Microsoft.Media/mediaservices/liveEvents/delete | 删除任何直播活动 |
> | Microsoft.Media/mediaservices/liveEvents/start/action | 启动任何直播活动操作 |
> | Microsoft.Media/mediaservices/liveEvents/stop/action | 停止任何直播活动操作 |
> | Microsoft.Media/mediaservices/liveEvents/reset/action | 重置任何直播活动操作 |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | 读取任何直播输出 |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | 创建或更新任何直播输出 |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | 删除任何直播输出 |
> | Microsoft.Media/mediaservices/liveEvents/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Media/mediaservices/liveEvents/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Media/mediaservices/liveEvents/providers/Microsoft.Insights/metricDefinitions/read | 获取媒体服务实时事件指标定义列表。 |
> | Microsoft.Media/mediaservices/liveOutputOperations/read | 读取任何直播输出操作 |
> | Microsoft.Media/mediaservices/privateEndpointConnectionOperations/read | 读取任何专用终结点连接操作 |
> | Microsoft.Media/mediaservices/privateEndpointConnectionProxies/read | 读取任何专用终结点连接代理 |
> | Microsoft.Media/mediaservices/privateEndpointConnectionProxies/write | 创建专用终结点连接代理 |
> | Microsoft.Media/mediaservices/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Media/mediaservices/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Media/mediaservices/privateEndpointConnections/read | 读取任何专用终结点连接 |
> | Microsoft.Media/mediaservices/privateEndpointConnections/write | 创建专用终结点连接 |
> | Microsoft.Media/mediaservices/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Media/mediaservices/privateLinkResources/read | 读取任何专用链接资源 |
> | Microsoft.Media/mediaservices/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Media/mediaservices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Media/mediaservices/providers/Microsoft.Insights/logDefinitions/read | 获取媒体服务帐户的可用日志 |
> | Microsoft.Media/mediaservices/providers/Microsoft.Insights/metricDefinitions/read | 获取媒体服务指标定义列表。 |
> | Microsoft.Media/mediaservices/streamingEndpointOperations/read | 读取任何流式处理终结点操作 |
> | Microsoft.Media/mediaservices/streamingEndpoints/read | 读取任何流式处理终结点 |
> | Microsoft.Media/mediaservices/streamingEndpoints/write | 创建或更新任何流式处理终结点 |
> | Microsoft.Media/mediaservices/streamingEndpoints/delete | 删除任何流式处理终结点 |
> | Microsoft.Media/mediaservices/streamingEndpoints/start/action | 启动任何流式处理终结点操作 |
> | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | 停止任何流式处理终结点操作 |
> | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | 缩放任何流式处理终结点操作 |
> | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 获取媒体服务流式处理终结点指标定义的列表。 |
> | Microsoft.Media/mediaservices/streamingLocators/read | 读取任何流式处理定位符 |
> | Microsoft.Media/mediaservices/streamingLocators/write | 创建或更新任何流式处理定位符 |
> | Microsoft.Media/mediaservices/streamingLocators/delete | 删除任何流式处理定位符 |
> | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | 列出内容密钥 |
> | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | 列出路径 |
> | Microsoft.Media/mediaservices/streamingPolicies/read | 读取任何流式处理策略 |
> | Microsoft.Media/mediaservices/streamingPolicies/write | 创建或更新任何流式处理策略 |
> | Microsoft.Media/mediaservices/streamingPolicies/delete | 删除任何流式处理策略 |
> | Microsoft.Media/mediaservices/transforms/read | 读取任何转换 |
> | Microsoft.Media/mediaservices/transforms/write | 创建或更新任何转换 |
> | Microsoft.Media/mediaservices/transforms/delete | 删除任何转换 |
> | Microsoft.Media/mediaservices/transforms/jobs/read | 读取任何作业 |
> | Microsoft.Media/mediaservices/transforms/jobs/write | 创建或更新任何作业 |
> | Microsoft.Media/mediaservices/transforms/jobs/delete | 删除任何作业 |
> | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | 取消作业 |
> | Microsoft.Media/operations/read | 获取可用操作 |
> | Microsoft.Media/videoAnalyzers/read | 读取视频分析器帐户 |
> | Microsoft.Media/videoAnalyzers/write | 写入视频分析器帐户 |
> | Microsoft.Media/videoAnalyzers/delete | 删除视频分析器帐户 |
> | Microsoft.Media/videoAnalyzers/accessPolicies/read | 读取任何访问策略 |
> | Microsoft.Media/videoAnalyzers/accessPolicies/write | 创建或更新任何访问策略 |
> | Microsoft.Media/videoAnalyzers/accessPolicies/delete | 删除任何访问策略 |
> | Microsoft.Media/videoAnalyzers/edgeModules/read | 读取任何 Edge 模块 |
> | Microsoft.Media/videoAnalyzers/edgeModules/write | 创建或更新任何 Edge 模块 |
> | Microsoft.Media/videoAnalyzers/edgeModules/delete | 删除任何 Edge 模块 |
> | Microsoft.Media/videoAnalyzers/edgeModules/listProvisioningToken/action | 创建新的预配令牌。<br>利用预配令牌，可以初始化一个 Azure 视频分析器 IoT Edge 模块实例，并将它授权到云帐户。<br>预配令牌本身是短期的，并且仅用于 IoT Edge 模块与云之间的初始握手。<br>在初始握手之后，IoT Edge 模块将会接受一组身份验证密钥，只要模块能够定期连接到云，这些密钥就会自动轮换。<br>如果模块状态丢失或重置，可以为同一 IoT Edge 模块生成新的预配令牌 |
> | Microsoft.Media/videoAnalyzers/livePipelines/read | 读取任何实时管道 |
> | Microsoft.Media/videoAnalyzers/livePipelines/write | 创建或更新任何实时管道 |
> | Microsoft.Media/videoAnalyzers/livePipelines/delete | 删除任何实时管道 |
> | Microsoft.Media/videoAnalyzers/livePipelines/activate/action | 激活任何实时管道 |
> | Microsoft.Media/videoAnalyzers/livePipelines/deactivate/action | 停用任何实时管道 |
> | Microsoft.Media/videoAnalyzers/livePipelines/operationsStatus/read | 读取任何实时管道操作状态 |
> | Microsoft.Media/videoAnalyzers/pipelineTopologies/read | 读取任何管道拓扑 |
> | Microsoft.Media/videoAnalyzers/pipelineTopologies/write | 创建或更新任何管道拓扑 |
> | Microsoft.Media/videoAnalyzers/pipelineTopologies/delete | 删除任何管道拓扑 |
> | Microsoft.Media/videoAnalyzers/videos/read | 读取任何视频 |
> | Microsoft.Media/videoAnalyzers/videos/write | 创建或更新任何视频 |
> | Microsoft.Media/videoAnalyzers/videos/delete | 删除任何视频 |
> | Microsoft.Media/videoAnalyzers/videos/listStreamingToken/action | 生成可用于视频播放的流式处理令牌 |

### <a name="microsoftsearch"></a>Microsoft.Search

Azure 服务：[Azure 搜索](../search/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Search/register/action | 注册搜索资源提供程序的订阅，并启用搜索服务的创建。 |
> | Microsoft.Search/checkNameAvailability/action | 检查服务名称的可用性。 |
> | Microsoft.Search/operations/read | 列出 Microsoft.Search 提供程序的所有可用操作。 |
> | Microsoft.Search/searchServices/write | 创建或更新搜索服务。 |
> | Microsoft.Search/searchServices/read | 读取搜索服务。 |
> | Microsoft.Search/searchServices/delete | 删除搜索服务。 |
> | Microsoft.Search/searchServices/start/action | 启动搜索服务。 |
> | Microsoft.Search/searchServices/stop/action | 停止搜索服务。 |
> | Microsoft.Search/searchServices/listAdminKeys/action | 读取管理密钥。 |
> | Microsoft.Search/searchServices/regenerateAdminKey/action | 再生成管理密钥。 |
> | Microsoft.Search/searchServices/listQueryKeys/action | 返回给定的 Azure 搜索服务的查询 API 密钥的列表。 |
> | Microsoft.Search/searchServices/createQueryKey/action | 创建查询密钥。 |
> | Microsoft.Search/searchServices/dataSources/read | 返回一个数据源或一个数据源列表。 |
> | Microsoft.Search/searchServices/dataSources/write | 创建数据源或修改其属性。 |
> | Microsoft.Search/searchServices/dataSources/delete | 删除数据源。 |
> | Microsoft.Search/searchServices/debugSessions/read | 返回一个调试会话或一个调试会话列表。 |
> | Microsoft.Search/searchServices/debugSessions/write | 创建调试会话或修改其属性。 |
> | Microsoft.Search/searchServices/debugSessions/delete | 删除调试会话。 |
> | Microsoft.Search/searchServices/debugSessions/execute/action | 使用调试会话、获取执行数据或者针对它计算表达式结果。 |
> | Microsoft.Search/searchServices/deleteQueryKey/delete | 删除查询密钥。 |
> | Microsoft.Search/searchServices/diagnosticSettings/read | 获取用于读取资源的诊断设置 |
> | Microsoft.Search/searchServices/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Search/searchServices/indexers/read | 返回索引器或其状态，或者返回索引器或其状态的列表。 |
> | Microsoft.Search/searchServices/indexers/write | 创建索引器、修改其属性或管理其执行。 |
> | Microsoft.Search/searchServices/indexers/delete | 删除索引器。 |
> | Microsoft.Search/searchServices/indexes/read | 返回索引及其统计信息、返回索引及其统计信息的列表，或者测试索引的词法分析组件。 |
> | Microsoft.Search/searchServices/indexes/write | 创建索引或修改其属性。 |
> | Microsoft.Search/searchServices/indexes/delete | 删除索引。 |
> | Microsoft.Search/searchServices/logDefinitions/read | 获取搜索服务的可用日志 |
> | Microsoft.Search/searchServices/metricDefinitions/read | 获取搜索服务的可用指标 |
> | Microsoft.Search/searchServices/privateEndpointConnectionProxies/validate/action | 从 NRP 端验证专用终结点连接创建调用 |
> | Microsoft.Search/searchServices/privateEndpointConnectionProxies/write | 使用指定参数创建专用终结点连接代理，或更新指定专用终结点连接代理的属性或标记 |
> | Microsoft.Search/searchServices/privateEndpointConnectionProxies/read | 返回专用终结点连接代理列表，或获取指定专用终结点连接代理的属性 |
> | Microsoft.Search/searchServices/privateEndpointConnectionProxies/delete | 删除现有的专用终结点连接代理 |
> | Microsoft.Search/searchServices/privateEndpointConnections/write | 使用指定参数创建专用终结点连接，或更新指定专用终结点连接的属性或标记 |
> | Microsoft.Search/searchServices/privateEndpointConnections/read | 返回专用终结点连接列表，或获取指定专用终结点连接的属性 |
> | Microsoft.Search/searchServices/privateEndpointConnections/delete | 删除现有的专用终结点连接 |
> | Microsoft.Search/searchServices/sharedPrivateLinkResources/write | 使用指定的参数创建新的共享专用链接资源，或更新指定的共享专用链接资源的属性。 |
> | Microsoft.Search/searchServices/sharedPrivateLinkResources/read | 返回共享专用链接资源的列表，或获取指定的共享专用链接资源的属性。 |
> | Microsoft.Search/searchServices/sharedPrivateLinkResources/delete | 删除现有的共享专用链接资源 |
> | Microsoft.Search/searchServices/sharedPrivateLinkResources/operationStatuses/read | 获取长时间运行的共享专用链接资源操作的详细信息 |
> | Microsoft.Search/searchServices/skillsets/read | 返回一个技能组或一个技能组列表。 |
> | Microsoft.Search/searchServices/skillsets/write | 创建技能组或修改其属性。 |
> | Microsoft.Search/searchServices/skillsets/delete | 删除技能组。 |
> | Microsoft.Search/searchServices/synonymMaps/read | 返回一个同义词映射或一个同义词映射列表。 |
> | Microsoft.Search/searchServices/synonymMaps/write | 创建同义词映射或修改其属性。 |
> | Microsoft.Search/searchServices/synonymMaps/delete | 删除同义词映射。 |
> | **DataAction** | **说明** |
> | Microsoft.Search/searchServices/indexes/documents/read | 从索引中读取文档或建议的查询词。 |
> | Microsoft.Search/searchServices/indexes/documents/write | 将文档上传到索引或修改现有文档。 |
> | Microsoft.Search/searchServices/indexes/documents/delete | 从索引中删除文档。 |

### <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

Azure 服务：[Azure SignalR 服务](../azure-signalr/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.SignalRService/register/action | 将“Microsoft.SignalRService”资源提供程序注册到订阅 |
> | Microsoft.SignalRService/unregister/action | 将“Microsoft.SignalRService”资源提供程序从订阅中取消注册 |
> | Microsoft.SignalRService/locations/checknameavailability/action | 检查某个名称是否可用于新的 Microsoft.SignalRService 资源。 |
> | Microsoft.SignalRService/locations/operationresults/signalr/read | 查询基于位置的异步操作的结果 |
> | Microsoft.SignalRService/locations/operationresults/webpubsub/read | 查询基于位置的异步操作的结果 |
> | Microsoft.SignalRService/locations/operationStatuses/signalr/read | 查询基于位置的异步操作的状态 |
> | Microsoft.SignalRService/locations/operationStatuses/webpubsub/read | 查询基于位置的异步操作的状态 |
> | Microsoft.SignalRService/locations/usages/read | 获取 Microsoft.SignalRService 资源提供程序的配额使用情况。 |
> | Microsoft.SignalRService/operationresults/read | 查询提供程序级异步操作的结果 |
> | Microsoft.SignalRService/operations/read | 列出 Microsoft.SignalRService 资源提供程序的操作。 |
> | Microsoft.SignalRService/operationstatus/read | 查询提供程序级异步操作的状态 |
> | Microsoft.SignalRService/SignalR/read | 在管理门户中或通过 API 查看 SignalR 的设置和配置 |
> | Microsoft.SignalRService/SignalR/write | 在管理门户中或通过 API 修改 SignalR 的设置和配置 |
> | Microsoft.SignalRService/SignalR/delete | 删除 SignalR 资源。 |
> | Microsoft.SignalRService/SignalR/listkeys/action | 通过管理门户或 API 查看 SignalR 访问密钥的值 |
> | Microsoft.SignalRService/SignalR/regeneratekey/action | 通过管理门户或 API 更改 SignalR 访问密钥的值 |
> | Microsoft.SignalRService/SignalR/restart/action | 通过管理门户或 API 重启 SignalR 资源。 将会停机一段时间。 |
> | Microsoft.SignalRService/SignalR/detectors/read | 读取检测器 |
> | Microsoft.SignalRService/SignalR/eventGridFilters/read | 获取指定事件网格筛选器的属性，或列出指定 SignalR 资源的所有事件网格筛选器。 |
> | Microsoft.SignalRService/SignalR/eventGridFilters/write | 使用指定的参数创建或更新 SignalR 资源的事件网格筛选器。 |
> | Microsoft.SignalRService/SignalR/eventGridFilters/delete | 从 SignalR 资源中删除事件网格筛选器。 |
> | Microsoft.SignalRService/SignalR/operationResults/read |  |
> | Microsoft.SignalRService/SignalR/operationStatuses/read |  |
> | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/write | 写入专用终结点连接代理 |
> | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/read | 读取专用终结点连接代理 |
> | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.SignalRService/SignalR/privateEndpointConnections/write | 写入专用终结点连接 |
> | Microsoft.SignalRService/SignalR/privateEndpointConnections/read | 读取专用终结点连接 |
> | Microsoft.SignalRService/SignalR/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.SignalRService/SignalR/privateLinkResources/read | 列出专用链接资源 |
> | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/logDefinitions/read | 获取 SignalR 资源的可用日志。 |
> | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | 获取 SignalR 资源的可用指标。 |
> | Microsoft.SignalRService/SignalR/sharedPrivateLinkResources/write | 写入共享专用链接资源 |
> | Microsoft.SignalRService/SignalR/sharedPrivateLinkResources/read | 读取共享专用链接资源 |
> | Microsoft.SignalRService/SignalR/sharedPrivateLinkResources/delete | 删除共享专用链接资源 |
> | Microsoft.SignalRService/WebPubSub/read | 在管理门户中或通过 API 查看 WebPubSub 的设置和配置 |
> | Microsoft.SignalRService/WebPubSub/write | 在管理门户中或通过 API 修改 WebPubSub 的设置和配置 |
> | Microsoft.SignalRService/WebPubSub/delete | 删除 WebPubSub 资源。 |
> | Microsoft.SignalRService/WebPubSub/listkeys/action | 通过管理门户或 API 查看 WebPubSub 访问密钥的值 |
> | Microsoft.SignalRService/WebPubSub/regeneratekey/action | 通过管理门户或 API 更改 WebPubSub 访问密钥的值 |
> | Microsoft.SignalRService/WebPubSub/restart/action | 通过管理门户或 API 重启 WebPubSub 资源。 将会停机一段时间。 |
> | Microsoft.SignalRService/WebPubSub/detectors/read | 读取检测器 |
> | Microsoft.SignalRService/WebPubSub/operationResults/read |  |
> | Microsoft.SignalRService/WebPubSub/operationStatuses/read |  |
> | Microsoft.SignalRService/WebPubSub/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.SignalRService/WebPubSub/privateEndpointConnectionProxies/write | 写入专用终结点连接代理 |
> | Microsoft.SignalRService/WebPubSub/privateEndpointConnectionProxies/read | 读取专用终结点连接代理 |
> | Microsoft.SignalRService/WebPubSub/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.SignalRService/WebPubSub/privateEndpointConnections/write | 写入专用终结点连接 |
> | Microsoft.SignalRService/WebPubSub/privateEndpointConnections/read | 读取专用终结点连接 |
> | Microsoft.SignalRService/WebPubSub/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.SignalRService/WebPubSub/privateLinkResources/read | 列出专用链接资源 |
> | Microsoft.SignalRService/WebPubSub/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.SignalRService/WebPubSub/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.SignalRService/WebPubSub/providers/Microsoft.Insights/logDefinitions/read | 获取 WebPubSub 资源的可用日志。 |
> | Microsoft.SignalRService/WebPubSub/providers/Microsoft.Insights/metricDefinitions/read | 获取 WebPubSub 资源的可用指标。 |
> | Microsoft.SignalRService/WebPubSub/sharedPrivateLinkResources/write | 写入共享专用链接资源 |
> | Microsoft.SignalRService/WebPubSub/sharedPrivateLinkResources/read | 读取共享专用链接资源 |
> | Microsoft.SignalRService/WebPubSub/sharedPrivateLinkResources/delete | 删除共享专用链接资源 |
> | **DataAction** | **说明** |
> | Microsoft.SignalRService/SignalR/auth/clientToken/action | 生成供客户端连接 ASRS 的 AccessToken；默认情况下，该令牌将在 5 分钟后过期。 |
> | Microsoft.SignalRService/SignalR/auth/accessKey/action | 生成用于对 AccessTokens 进行签名的 AccessKey；默认情况下，此密钥将在 90 分钟后过期。 |
> | Microsoft.SignalRService/SignalR/auth/accessToken/action | 生成供客户端连接 ASRS 的 AccessToken；默认情况下，该令牌将在 5 分钟后过期。 |
> | Microsoft.SignalRService/SignalR/clientConnection/send/action | 将消息直接发送到客户端连接。 |
> | Microsoft.SignalRService/SignalR/clientConnection/read | 检查客户端连接是否存在。 |
> | Microsoft.SignalRService/SignalR/clientConnection/write | 关闭客户端连接。 |
> | Microsoft.SignalRService/SignalR/group/send/action | 将消息广播到组。 |
> | Microsoft.SignalRService/SignalR/group/read | 检查组是否存在或用户是否存在于组中。 |
> | Microsoft.SignalRService/SignalR/group/write | 加入/退出组。 |
> | Microsoft.SignalRService/SignalR/hub/send/action | 将消息广播到中心内的所有客户端连接。 |
> | Microsoft.SignalRService/SignalR/serverConnection/write | 启动服务器连接。 |
> | Microsoft.SignalRService/SignalR/user/send/action | 将消息发送给可能有多个客户端连接的用户。 |
> | Microsoft.SignalRService/SignalR/user/read | 检查用户是否存在。 |
> | Microsoft.SignalRService/SignalR/user/write | 修改用户。 |
> | Microsoft.SignalRService/WebPubSub/auth/accessKey/action | 生成用于对 AccessTokens 进行签名的 AccessKey；默认情况下，此密钥将在 90 分钟后过期。 |
> | Microsoft.SignalRService/WebPubSub/auth/accessToken/action | 生成供客户端用于连接到 AWPS 的 AccessToken；默认情况下，此令牌将在 5 分钟后过期。 |
> | Microsoft.SignalRService/WebPubSub/clientConnection/send/action | 将消息直接发送到客户端连接。 |
> | Microsoft.SignalRService/WebPubSub/clientConnection/read | 检查客户端连接是否存在。 |
> | Microsoft.SignalRService/WebPubSub/clientConnection/write | 关闭客户端连接。 |
> | Microsoft.SignalRService/WebPubSub/group/send/action | 将消息广播到组。 |
> | Microsoft.SignalRService/WebPubSub/group/read | 检查组是否存在或用户是否存在于组中。 |
> | Microsoft.SignalRService/WebPubSub/group/write | 加入/退出组。 |
> | Microsoft.SignalRService/WebPubSub/hub/send/action | 将消息广播到中心内的所有客户端连接。 |
> | Microsoft.SignalRService/WebPubSub/user/send/action | 将消息发送给可能有多个客户端连接的用户。 |
> | Microsoft.SignalRService/WebPubSub/user/read | 检查用户是否存在。 |

### <a name="microsoftweb"></a>microsoft.web

Azure 服务：[应用服务](../app-service/index.yml)、[Azure Functions](../azure-functions/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.web/unregister/action | 取消注册订阅的 Microsoft.Web 资源提供程序。 |
> | microsoft.web/validate/action | 验证。 |
> | microsoft.web/register/action | 注册订阅的 Microsoft.Web 资源提供程序。 |
> | microsoft.web/verifyhostingenvironmentvnet/action | 验证宿主环境 Vnet。 |
> | microsoft.web/apimanagementaccounts/apiacls/read | 获取 API 管理帐户 Apiacls。 |
> | microsoft.web/apimanagementaccounts/apis/read | 获取 API 管理帐户 API。 |
> | microsoft.web/apimanagementaccounts/apis/delete | 删除 API 管理帐户 API。 |
> | microsoft.web/apimanagementaccounts/apis/write | 更新 API 管理帐户 API。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/delete | 删除 API 管理帐户 API Apiacls。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/read | 获取 API 管理帐户 API Apiacls。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/write | 更新 API 管理帐户 API Apiacls。 |
> | microsoft.web/apimanagementaccounts/apis/connectionacls/read | 获取 API 管理帐户 API Connectionacls。 |
> | microsoft.web/apimanagementaccounts/apis/connections/read | 获取 API 管理帐户 API 连接。 |
> | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 确认同意代码 API 管理帐户 API 连接。 |
> | microsoft.web/apimanagementaccounts/apis/connections/delete | 删除 API 管理帐户 API 连接。 |
> | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | 获取 API 管理帐户 API 连接的同意链接。 |
> | microsoft.web/apimanagementaccounts/apis/connections/write | 更新 API 管理帐户 API 连接。 |
> | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 列出连接密钥 API 管理帐户 API 连接。 |
> | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | 列出机密 API 管理帐户 API 连接。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | 删除 API 管理帐户 API 连接 Connectionacls。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | 获取 API 管理帐户 API 连接 Connectionacls。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | 更新 API 管理帐户 API 连接 Connectionacls。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | 删除 API 管理帐户 API 本地化定义。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | 获取 API 管理帐户 API 本地化定义。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | 更新 API 管理帐户 API 本地化定义。 |
> | microsoft.web/apimanagementaccounts/connectionacls/read | 获取 API 管理帐户 Connectionacls。 |
> | microsoft.web/availablestacks/read | 获取可用堆栈。 |
> | microsoft.web/billingmeters/read | 获取账单计量的列表。 |
> | Microsoft.Web/certificates/Read | 获取证书列表。 |
> | Microsoft.Web/certificates/Write | 添加新的或更新现有的证书。 |
> | Microsoft.Web/certificates/Delete | 删除现有证书。 |
> | microsoft.web/checknameavailability/read | 检查资源名称是否可用。 |
> | microsoft.web/classicmobileservices/read | 获取经典移动服务。 |
> | Microsoft.Web/connectionGateways/Read | 获取连接网关列表。 |
> | Microsoft.Web/connectionGateways/Write | 创建或更新连接网关。 |
> | Microsoft.Web/connectionGateways/Delete | 删除连接网关。 |
> | Microsoft.Web/connectionGateways/Move/Action | 移动连接网关。 |
> | Microsoft.Web/connectionGateways/Join/Action | 加入连接网关。 |
> | Microsoft.Web/connectionGateways/Associate/Action | 与连接网关关联。 |
> | Microsoft.Web/connectionGateways/ListStatus/Action | 列出连接网关的状态。 |
> | Microsoft.Web/connections/Read | 获取连接列表。 |
> | Microsoft.Web/connections/Write | 创建或更新连接。 |
> | Microsoft.Web/connections/Delete | 删除连接。 |
> | Microsoft.Web/connections/Move/Action | 移动连接。 |
> | Microsoft.Web/connections/Join/Action | 加入连接。 |
> | microsoft.web/connections/confirmconsentcode/action | 确认连接许可代码。 |
> | microsoft.web/connections/listconsentlinks/action | 列出连接的许可链接。 |
> | microsoft.web/connections/listConnectionKeys/action | 列出 API 连接密钥。 |
> | microsoft.web/connections/revokeConnectionKeys/action | 撤销 API 连接密钥。 |
> | Microsoft.Web/connections/providers/Microsoft.Insights/metricDefinitions/Read | 获取 API 连接的可用指标 |
> | Microsoft.Web/customApis/Read | 获取自定义 API 的列表。 |
> | Microsoft.Web/customApis/Write | 创建或更新自定义 API。 |
> | Microsoft.Web/customApis/Delete | 删除自定义 API。 |
> | Microsoft.Web/customApis/Move/Action | 移动自定义 API。 |
> | Microsoft.Web/customApis/Join/Action | 加入自定义 API。 |
> | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | 从 WSDL 中提取 API 定义。 |
> | Microsoft.Web/customApis/listWsdlInterfaces/Action | 列出自定义 API 的 WSDL 接口。 |
> | Microsoft.Web/customhostnames/Read | 获取分配给订阅的自定义主机名。 |
> | Microsoft.Web/customhostnames/sites/Read | 获取分配给自定义主机名的站点名称。 |
> | Microsoft.Web/deletedSites/Read | 获取已删除的 Web 应用的属性 |
> | microsoft.web/deploymentlocations/read | 获取部署位置。 |
> | Microsoft.Web/geoRegions/Read | 获取地理区域的列表。 |
> | Microsoft.Web/hostingEnvironments/Read | 获取应用服务环境的属性 |
> | Microsoft.Web/hostingEnvironments/Write | 创建新的或更新现有的应用服务环境 |
> | Microsoft.Web/hostingEnvironments/Delete | 删除应用服务环境 |
> | Microsoft.Web/hostingEnvironments/Join/Action | 加入应用服务环境 |
> | Microsoft.Web/hostingEnvironments/reboot/Action | 重启应用服务环境中的所有计算机 |
> | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | 审批专用终结点连接 |
> | microsoft.web/hostingenvironments/resume/action | 恢复宿主环境。 |
> | microsoft.web/hostingenvironments/suspend/action | 暂停宿主环境。 |
> | microsoft.web/hostingenvironments/capacities/read | 获取宿主环境容量。 |
> | Microsoft.Web/hostingEnvironments/configurations/networking/Read | 获取应用服务环境的网络配置 |
> | Microsoft.Web/hostingEnvironments/configurations/networking/Write | 更新应用服务环境的网络配置。 |
> | microsoft.web/hostingenvironments/detectors/read | 获取宿主环境检测器。 |
> | microsoft.web/hostingenvironments/diagnostics/read | 获取宿主环境诊断。 |
> | Microsoft.Web/hostingEnvironments/eventGridFilters/delete | 删除宿主环境的事件网格筛选器。 |
> | Microsoft.Web/hostingEnvironments/eventGridFilters/read | 获取宿主环境的事件网格筛选器。 |
> | Microsoft.Web/hostingEnvironments/eventGridFilters/write | 放置宿主环境的事件网格筛选器。 |
> | microsoft.web/hostingenvironments/health/read | 获取应用服务环境的运行状况详细信息。 |
> | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | 获取所有入站依赖项的网络终结点。 |
> | microsoft.web/hostingenvironments/metricdefinitions/read | 获取宿主环境的指标定义。 |
> | Microsoft.Web/hostingEnvironments/multiRolePools/Read | 获取应用服务环境中前端池的属性 |
> | Microsoft.Web/hostingEnvironments/multiRolePools/Write | 在应用服务环境中创建新的或更新现有的前端池 |
> | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | 获取宿主环境的多角色池指标定义。 |
> | microsoft.web/hostingenvironments/multirolepools/metrics/read | 获取宿主环境的多角色池指标。 |
> | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | 获取应用服务环境多角色的可用指标 |
> | microsoft.web/hostingenvironments/multirolepools/skus/read | 获取宿主环境的多角色池 SKU。 |
> | microsoft.web/hostingenvironments/multirolepools/usages/read | 获取宿主环境的多角色池使用情况。 |
> | microsoft.web/hostingenvironments/operations/read | 获取宿主环境操作。 |
> | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | 获取所有出站依赖项的网络终结点。 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnectionProxies/Read | 读取专用终结点连接代理 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnectionProxies/Write | 创建或更新专用终结点连接代理 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnectionProxies/Delete | 删除专用终结点连接代理 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnectionProxies/operations/Read | 读取专用终结点连接代理操作 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnections/Write | 批准或拒绝专用终结点连接。 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnections/Read | 获取专用终结点连接或专用终结点连接的列表。 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnections/Delete | 删除专用终结点连接。 |
> | Microsoft.Web/hostingEnvironments/privateLinkResources/Read | 获取专用链接资源。 |
> | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | microsoft.web/hostingenvironments/providers/Microsoft.Insights/logDefinitions/read | 读取宿主环境日志定义 |
> | Microsoft.Web/hostingEnvironments/providers/Microsoft.Insights/metricDefinitions/Read | 获取应用服务环境的可用指标 |
> | microsoft.web/hostingenvironments/serverfarms/read | 获取宿主环境的应用服务计划。 |
> | microsoft.web/hostingenvironments/sites/read | 获取宿主环境的 Web 应用。 |
> | microsoft.web/hostingenvironments/usages/read | 获取宿主环境使用情况。 |
> | Microsoft.Web/hostingEnvironments/workerPools/Read | 获取应用服务环境中辅助角色池的属性 |
> | Microsoft.Web/hostingEnvironments/workerPools/Write | 在应用服务环境中创建新的或更新现有的辅助角色池 |
> | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | 获取宿主环境的辅助角色池指标定义。 |
> | microsoft.web/hostingenvironments/workerpools/metrics/read | 获取宿主环境的辅助角色池指标。 |
> | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | 获取应用服务环境角色池的可用指标 |
> | microsoft.web/hostingenvironments/workerpools/skus/read | 获取宿主环境的辅助角色池 SKU。 |
> | microsoft.web/hostingenvironments/workerpools/usages/read | 获取宿主环境的辅助角色池使用情况。 |
> | microsoft.web/ishostingenvironmentnameavailable/read | 检查宿主环境名称是否可用。 |
> | microsoft.web/ishostnameavailable/read | 检查主机名是否可用。 |
> | microsoft.web/isusernameavailable/read | 检查用户名是否可用。 |
> | Microsoft.Web/kubeEnvironments/read | 获取 Kubernetes 环境的属性 |
> | Microsoft.Web/kubeEnvironments/write | 创建 Kubernetes 环境或更新现有的 Kubernetes 环境 |
> | Microsoft.Web/kubeEnvironments/delete | 删除 Kubernetes 环境 |
> | Microsoft.Web/kubeEnvironments/join/action | 加入 Kubernetes 环境 |
> | Microsoft.Web/kubeEnvironments/operations/read | 获取 Kubernetes 环境的操作 |
> | Microsoft.Web/listSitesAssignedToHostName/Read | 获取分配给主机名的站点名称。 |
> | Microsoft.Web/locations/GetNetworkPolicies/action | 读取网络意向策略 |
> | microsoft.web/locations/extractapidefinitionfromwsdl/action | 从位置的 WSDL 中提取 API 定义。 |
> | microsoft.web/locations/listwsdlinterfaces/action | 列出位置的 WSDL 接口。 |
> | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 位置的 VNet 或子网删除通知。 |
> | Microsoft.Web/locations/previewstaticsiteworkflowfile/action | 预览静态站点工作流文件 |
> | microsoft.web/locations/apioperations/read | 获取位置 API 操作。 |
> | microsoft.web/locations/connectiongatewayinstallations/read | 获取位置连接网关安装。 |
> | microsoft.web/locations/managedapis/read | 获取位置托管 API。 |
> | Microsoft.Web/locations/managedapis/Join/Action | 联接托管的 API。 |
> | microsoft.web/locations/managedapis/apioperations/read | 获取位置托管 API 操作。 |
> | microsoft.web/locations/operationResults/read | 获取操作。 |
> | microsoft.web/locations/operations/read | 获取操作。 |
> | microsoft.web/operations/read | 获取操作。 |
> | microsoft.web/publishingusers/read | 获取发布用户。 |
> | microsoft.web/publishingusers/write | 更新发布用户。 |
> | Microsoft.Web/recommendations/Read | 获取订阅的建议列表。 |
> | microsoft.web/resourcehealthmetadata/read | 获取资源运行状况元数据。 |
> | Microsoft.Web/serverfarms/Read | 获取应用服务计划的属性 |
> | Microsoft.Web/serverfarms/Write | 创建新的或更新现有的应用服务计划。 |
> | Microsoft.Web/serverfarms/Delete | 删除现有的应用服务计划 |
> | Microsoft.Web/serverfarms/Join/Action | 加入应用服务计划 |
> | Microsoft.Web/serverfarms/restartSites/Action | 重启应用服务计划中的所有 Web 应用 |
> | microsoft.web/serverfarms/capabilities/read | 获取应用服务计划功能。 |
> | Microsoft.Web/serverfarms/eventGridFilters/delete | 删除服务器场中的事件网格筛选器。 |
> | Microsoft.Web/serverfarms/eventGridFilters/read | 获取服务器场中的事件网格筛选器。 |
> | Microsoft.Web/serverfarms/eventGridFilters/write | 在服务器场中放置事件网格筛选器。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/delete | 删除应用服务计划第一方应用设置。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/read | 获取应用服务计划第一方应用设置。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/write | 更新应用服务计划第一方应用设置。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | 获取应用服务计划混合连接命名空间中继。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | 删除应用服务计划混合连接命名空间中继。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | 获取应用服务计划混合连接命名空间中继 Web 应用。 |
> | microsoft.web/serverfarms/hybridconnectionplanlimits/read | 获取应用服务计划混合连接计划限制。 |
> | microsoft.web/serverfarms/hybridconnectionrelays/read | 获取应用服务计划混合连接中继。 |
> | microsoft.web/serverfarms/metricdefinitions/read | 获取应用服务计划指标定义。 |
> | microsoft.web/serverfarms/metrics/read | 获取应用服务计划指标。 |
> | microsoft.web/serverfarms/operationresults/read | 获取应用服务计划操作结果。 |
> | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | 获取应用服务计划的可用指标 |
> | microsoft.web/serverfarms/sites/read | 获取应用服务计划 Web 应用。 |
> | microsoft.web/serverfarms/skus/read | 获取应用服务计划 SKU。 |
> | microsoft.web/serverfarms/usages/read | 获取应用服务计划使用情况。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/read | 获取应用服务计划虚拟网络连接。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | 更新应用服务计划虚拟网络连接网关。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | 删除应用服务计划虚拟网络连接路由。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | 获取应用服务计划虚拟网络连接路由。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | 更新应用服务计划虚拟网络连接路由。 |
> | microsoft.web/serverfarms/workers/reboot/action | 重启应用服务计划辅助角色。 |
> | Microsoft.Web/sites/Read | 获取 Web 应用的属性 |
> | Microsoft.Web/sites/Write | 创建新的或更新现有的 Web 应用 |
> | Microsoft.Web/sites/Delete | 删除现有的 Web 应用 |
> | Microsoft.Web/sites/backup/Action | 创建新的 Web 应用备份 |
> | Microsoft.Web/sites/publishxml/Action | 获取 Web 应用的发布配置文件 xml |
> | Microsoft.Web/sites/publish/Action | 发布 Web 应用 |
> | Microsoft.Web/sites/restart/Action | 重启 Web 应用 |
> | Microsoft.Web/sites/start/Action | 启动 Web 应用 |
> | Microsoft.Web/sites/stop/Action | 停止 Web 应用 |
> | Microsoft.Web/sites/slotsswap/Action | 交换 Web 应用部署槽 |
> | Microsoft.Web/sites/slotsdiffs/Action | 获取 Web 应用与槽之间的配置差异 |
> | Microsoft.Web/sites/applySlotConfig/Action | 将目标槽中的 Web 应用槽配置应用到当前 Web 应用 |
> | Microsoft.Web/sites/resetSlotConfig/Action | 重置 Web 应用配置 |
> | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | 审批专用终结点连接 |
> | microsoft.web/sites/functions/action | 函数 Web 应用。 |
> | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 列出同步函数触发器状态。 |
> | microsoft.web/sites/networktrace/action | 网络跟踪 Web 应用。 |
> | microsoft.web/sites/newpassword/action | Newpassword Web 应用。 |
> | microsoft.web/sites/sync/action | 同步 Web 应用。 |
> | microsoft.web/sites/migratemysql/action | 迁移 MySql Web 应用。 |
> | microsoft.web/sites/recover/action | 恢复 Web 应用。 |
> | microsoft.web/sites/restoresnapshot/action | 还原 Web 应用快照。 |
> | microsoft.web/sites/restorefromdeletedapp/action | 从已删除的应用还原 Web 应用。 |
> | microsoft.web/sites/syncfunctiontriggers/action | 同步函数触发器。 |
> | microsoft.web/sites/backups/action | 发现可从 Azure 存储中的 blob 还原的现有应用备份。 |
> | microsoft.web/sites/containerlogs/action | 获取 Web 应用的压缩容器日志。 |
> | microsoft.web/sites/restorefrombackupblob/action | 基于备份 Blob 还原 Web 应用。 |
> | microsoft.web/sites/analyzecustomhostname/read | 分析自定义主机名。 |
> | microsoft.web/sites/backup/read | 获取 Web 应用备份。 |
> | microsoft.web/sites/backup/write | 更新 Web 应用备份。 |
> | Microsoft.Web/sites/backups/Read | 获取 Web 应用的备份属性 |
> | microsoft.web/sites/backups/list/action | 列出 Web 应用备份。 |
> | microsoft.web/sites/backups/restore/action | 还原 Web 应用备份。 |
> | microsoft.web/sites/backups/delete | 删除 Web 应用备份。 |
> | microsoft.web/sites/backups/write | 更新 Web 应用备份。 |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/Read | 列出可以用于 Web 应用的发布方法 |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/Write | 列出可以用于 Web 应用的发布方法 |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/ftp/Read | 获取 Web 应用是否允许 FTP 发布凭据 |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/ftp/Write | 更新 Web 应用是否允许 FTP 发布凭据 |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/scm/Read | 获取 Web 应用是否允许 SCM 发布凭据 |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/scm/Write | 更新 Web 应用是否允许 SCM 发布凭据 |
> | Microsoft.Web/sites/config/Read | 获取 Web 应用配置设置 |
> | Microsoft.Web/sites/config/list/Action | 列出 Web 应用的安全敏感设置，例如发布凭据、应用设置和连接字符串 |
> | Microsoft.Web/sites/config/Write | 更新 Web 应用的配置设置 |
> | microsoft.web/sites/config/delete | 删除 Web 应用配置。 |
> | microsoft.web/sites/config/snapshots/read | 获取 Web 应用配置快照。 |
> | microsoft.web/sites/config/snapshots/listsecrets/action | Web 应用列出快照中的机密。 |
> | microsoft.web/sites/containerlogs/download/action | 下载 Web 应用容器日志。 |
> | microsoft.web/sites/continuouswebjobs/delete | 删除 Web 应用连续 Web 作业。 |
> | microsoft.web/sites/continuouswebjobs/read | 获取 Web 应用连续 Web 作业。 |
> | microsoft.web/sites/continuouswebjobs/start/action | 启动 Web 应用连续 Web 作业。 |
> | microsoft.web/sites/continuouswebjobs/stop/action | 停止 Web 应用连续 Web 作业。 |
> | microsoft.web/sites/deployments/delete | 删除 Web 应用部署。 |
> | microsoft.web/sites/deployments/read | 获取 Web 应用部署。 |
> | microsoft.web/sites/deployments/write | 更新 Web 应用部署。 |
> | microsoft.web/sites/deployments/log/read | 获取 Web 应用部署日志。 |
> | microsoft.web/sites/detectors/read | 获取 Web 应用检测器。 |
> | microsoft.web/sites/diagnostics/read | 获取 Web 应用诊断类别。 |
> | microsoft.web/sites/diagnostics/analyses/read | 获取 Web 应用诊断分析。 |
> | microsoft.web/sites/diagnostics/analyses/execute/Action | 运行 Web 应用诊断分析。 |
> | microsoft.web/sites/diagnostics/aspnetcore/read | 获取 ASP.NET Core 应用的 Web 应用诊断。 |
> | microsoft.web/sites/diagnostics/autoheal/read | 获取 Web 应用诊断 Autoheal。 |
> | microsoft.web/sites/diagnostics/deployment/read | 获取 Web 应用诊断部署。 |
> | microsoft.web/sites/diagnostics/deployments/read | 获取 Web 应用诊断部署。 |
> | microsoft.web/sites/diagnostics/detectors/read | 获取 Web 应用诊断检测程序。 |
> | microsoft.web/sites/diagnostics/detectors/execute/Action | 运行 Web 应用诊断检测程序。 |
> | microsoft.web/sites/diagnostics/failedrequestsperuri/read | 获取每个 Uri 的 Web 应用诊断失败的请求。 |
> | microsoft.web/sites/diagnostics/frebanalysis/read | 获取 Web 应用诊断 FREB 分析。 |
> | microsoft.web/sites/diagnostics/loganalyzer/read | 获取 Web 应用诊断日志分析器。 |
> | microsoft.web/sites/diagnostics/runtimeavailability/read | 获取 Web 应用诊断运行时可用性。 |
> | microsoft.web/sites/diagnostics/servicehealth/read | 获取 Web 应用诊断服务运行状况。 |
> | microsoft.web/sites/diagnostics/sitecpuanalysis/read | 获取 Web 应用诊断站点 CPU 分析。 |
> | microsoft.web/sites/diagnostics/sitecrashes/read | 获取 Web 应用诊断站点故障。 |
> | microsoft.web/sites/diagnostics/sitelatency/read | 获取 Web 应用诊断站点延迟。 |
> | microsoft.web/sites/diagnostics/sitememoryanalysis/read | 获取 Web 应用诊断站点内存分析。 |
> | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | 获取 Web 应用诊断站点重启设置更新。 |
> | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | 获取 Web 应用诊断站点重启用户发起时间。 |
> | microsoft.web/sites/diagnostics/siteswap/read | 获取 Web 应用诊断站点交换。 |
> | microsoft.web/sites/diagnostics/threadcount/read | 获取 Web 应用诊断线程计数。 |
> | microsoft.web/sites/diagnostics/workeravailability/read | 获取 Web 应用诊断 Workeravailability。 |
> | microsoft.web/sites/diagnostics/workerprocessrecycle/read | 获取 Web 应用诊断工作进程回收。 |
> | microsoft.web/sites/domainownershipidentifiers/read | 获取 Web 应用域所有权标识符。 |
> | microsoft.web/sites/domainownershipidentifiers/write | 更新 Web 应用域所有权标识符。 |
> | Microsoft.Web/sites/eventGridFilters/delete | 删除 Web 应用中的事件网格筛选器。 |
> | Microsoft.Web/sites/eventGridFilters/read | 获取 Web 应用中的事件网格筛选器。 |
> | Microsoft.Web/sites/eventGridFilters/write | 在 Web 应用中放置事件网格筛选器。 |
> | microsoft.web/sites/extensions/delete | 删除 Web 应用站点扩展。 |
> | microsoft.web/sites/extensions/read | 获取 Web 应用站点扩展。 |
> | microsoft.web/sites/extensions/write | 更新 Web 应用站点扩展。 |
> | microsoft.web/sites/functions/delete | 删除 Web 应用函数。 |
> | microsoft.web/sites/functions/listsecrets/action | 列出函数机密。 |
> | microsoft.web/sites/functions/listkeys/action | 列出函数密钥。 |
> | microsoft.web/sites/functions/read | 获取 Web 应用函数。 |
> | microsoft.web/sites/functions/write | 更新 Web 应用函数。 |
> | microsoft.web/sites/functions/keys/write | 更新函数密钥。 |
> | microsoft.web/sites/functions/keys/delete | 删除函数密钥。 |
> | microsoft.web/sites/functions/masterkey/read | 获取 Web 应用函数主密钥。 |
> | microsoft.web/sites/functions/token/read | 获取 Web 应用函数令牌。 |
> | microsoft.web/sites/host/listkeys/action | 列出函数宿主密钥。 |
> | microsoft.web/sites/host/sync/action | 同步函数触发器。 |
> | microsoft.web/sites/host/listsyncstatus/action | 列出同步函数触发器状态。 |
> | microsoft.web/sites/host/functionkeys/write | 更新 Functions 宿主函数密钥。 |
> | microsoft.web/sites/host/functionkeys/delete | 删除 Functions 宿主函数密钥。 |
> | microsoft.web/sites/host/systemkeys/write | 更新 Functions 宿主系统密钥。 |
> | microsoft.web/sites/host/systemkeys/delete | 删除 Functions 宿主系统密钥。 |
> | microsoft.web/sites/hostnamebindings/delete | 删除 Web 应用主机名绑定。 |
> | microsoft.web/sites/hostnamebindings/read | 获取 Web 应用主机名绑定。 |
> | microsoft.web/sites/hostnamebindings/write | 更新 Web 应用主机名绑定。 |
> | Microsoft.Web/sites/hostruntime/host/action | 执行函数应用运行时操作，例如同步触发器、添加函数、调用函数、删除函数等。 |
> | microsoft.web/sites/hostruntime/functions/keys/read | 获取 Web 应用 Hostruntime 函数密钥。 |
> | microsoft.web/sites/hostruntime/host/read | 获取 Web 应用 Hostruntime 主机。 |
> | Microsoft.Web/sites/hostruntime/host/_master/read | 获取管理员操作的函数应用主密钥 |
> | microsoft.web/sites/hybridconnection/delete | 删除 Web 应用混合连接。 |
> | microsoft.web/sites/hybridconnection/read | 获取 Web 应用混合连接。 |
> | microsoft.web/sites/hybridconnection/write | 更新 Web 应用混合连接。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | 删除 Web 应用混合连接命名空间中继。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | 列出密钥 Web 应用混合连接命名空间中继。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/write | 更新 Web 应用混合连接命名空间中继。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/read | 获取 Web 应用混合连接命名空间中继。 |
> | microsoft.web/sites/hybridconnectionrelays/read | 获取 Web 应用混合连接中继。 |
> | microsoft.web/sites/instances/read | 获取 Web 应用实例。 |
> | microsoft.web/sites/instances/deployments/read | 获取 Web 应用实例部署。 |
> | microsoft.web/sites/instances/deployments/delete | 删除 Web 应用实例部署。 |
> | microsoft.web/sites/instances/extensions/read | 获取 Web 应用实例扩展。 |
> | microsoft.web/sites/instances/extensions/log/read | 获取 Web 应用实例扩展日志。 |
> | microsoft.web/sites/instances/extensions/processes/read | 获取 Web 应用实例扩展进程。 |
> | microsoft.web/sites/instances/processes/delete | 删除 Web 应用实例进程。 |
> | microsoft.web/sites/instances/processes/read | 获取 Web 应用实例进程。 |
> | microsoft.web/sites/instances/processes/modules/read | 获取 Web 应用实例进程模块。 |
> | microsoft.web/sites/instances/processes/threads/read | 获取 Web 应用实例进程线程。 |
> | microsoft.web/sites/metricdefinitions/read | 获取 Web 应用指标定义。 |
> | microsoft.web/sites/metrics/read | 获取 Web 应用指标。 |
> | microsoft.web/sites/metricsdefinitions/read | 获取 Web 应用指标定义。 |
> | microsoft.web/sites/migratemysql/read | 获取 Web 应用迁移 MySql。 |
> | microsoft.web/sites/networkConfig/read | 获取应用服务网络配置。 |
> | microsoft.web/sites/networkConfig/write | 更新应用服务网络配置。 |
> | microsoft.web/sites/networkConfig/delete | 删除应用服务网络配置。 |
> | microsoft.web/sites/networktraces/operationresults/read | 获取 Web 应用网络跟踪操作结果。 |
> | microsoft.web/sites/operationresults/read | 获取 Web 应用操作结果。 |
> | microsoft.web/sites/operations/read | 获取 Web 应用操作。 |
> | microsoft.web/sites/perfcounters/read | 获取 Web 应用性能计数器。 |
> | microsoft.web/sites/premieraddons/delete | 删除 Web 应用高级加载项。 |
> | microsoft.web/sites/premieraddons/read | 获取 Web 应用高级加载项。 |
> | microsoft.web/sites/premieraddons/write | 更新 Web 应用高级加载项。 |
> | microsoft.web/sites/privateaccess/read | 获取有关可访问站点的专用站点访问支持和授权虚拟网络的数据。 |
> | Microsoft.Web/sites/privateEndpointConnectionProxies/Read | 读取专用终结点连接代理 |
> | Microsoft.Web/sites/privateEndpointConnectionProxies/Write | 创建或更新专用终结点连接代理 |
> | Microsoft.Web/sites/privateEndpointConnectionProxies/Delete | 删除专用终结点连接代理 |
> | Microsoft.Web/sites/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Web/sites/privateEndpointConnectionProxies/operations/Read | 读取专用终结点连接代理操作 |
> | Microsoft.Web/sites/privateEndpointConnections/Write | 批准或拒绝专用终结点连接。 |
> | Microsoft.Web/sites/privateEndpointConnections/Read | 获取专用终结点连接或专用终结点连接的列表。 |
> | Microsoft.Web/sites/privateEndpointConnections/Delete | 删除专用终结点连接。 |
> | Microsoft.Web/sites/privateLinkResources/Read | 获取专用链接资源。 |
> | microsoft.web/sites/processes/read | 获取 Web 应用进程。 |
> | microsoft.web/sites/processes/modules/read | 获取 Web 应用进程模块。 |
> | microsoft.web/sites/processes/threads/read | 获取 Web 应用进程线程。 |
> | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | microsoft.web/sites/providers/Microsoft.Insights/logDefinitions/read | 获取 Web 应用的可用日志 |
> | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | 获取 Web 应用的可用指标 |
> | microsoft.web/sites/publiccertificates/delete | 删除 Web 应用公共证书。 |
> | microsoft.web/sites/publiccertificates/read | 获取 Web 应用公共证书。 |
> | microsoft.web/sites/publiccertificates/write | 更新 Web 应用公共证书。 |
> | microsoft.web/sites/publishxml/read | 获取 Web 应用发布 XML。 |
> | microsoft.web/sites/recommendationhistory/read | 获取 Web 应用建议历史记录。 |
> | Microsoft.Web/sites/recommendations/Read | 获取 Web 应用建议的列表。 |
> | microsoft.web/sites/recommendations/disable/action | 禁用 Web 应用建议。 |
> | microsoft.web/sites/resourcehealthmetadata/read | 获取 Web 应用资源运行状况元数据。 |
> | microsoft.web/sites/restore/read | 获取 Web 应用还原设置。 |
> | microsoft.web/sites/restore/write | 还原 Web 应用。 |
> | microsoft.web/sites/siteextensions/delete | 删除 Web 应用站点扩展。 |
> | microsoft.web/sites/siteextensions/read | 获取 Web 应用站点扩展。 |
> | microsoft.web/sites/siteextensions/write | 更新 Web 应用站点扩展。 |
> | Microsoft.Web/sites/slots/Write | 创建新的或更新现有的 Web 应用槽 |
> | Microsoft.Web/sites/slots/Delete | 删除现有的 Web 应用槽 |
> | Microsoft.Web/sites/slots/backup/Action | 创建新的 Web 应用槽备份。 |
> | Microsoft.Web/sites/slots/publishxml/Action | 获取 Web 应用槽的发布配置文件 xml |
> | Microsoft.Web/sites/slots/publish/Action | 发布 Web 应用槽 |
> | Microsoft.Web/sites/slots/restart/Action | 重启 Web 应用槽 |
> | Microsoft.Web/sites/slots/start/Action | 启动 Web 应用槽 |
> | Microsoft.Web/sites/slots/stop/Action | 停止 Web 应用槽 |
> | Microsoft.Web/sites/slots/slotsswap/Action | 交换 Web 应用部署槽 |
> | Microsoft.Web/sites/slots/slotsdiffs/Action | 获取 Web 应用与槽之间的配置差异 |
> | Microsoft.Web/sites/slots/applySlotConfig/Action | 将目标槽中的 Web 应用槽配置应用到当前槽 |
> | Microsoft.Web/sites/slots/resetSlotConfig/Action | 重置 Web 应用槽配置 |
> | Microsoft.Web/sites/slots/Read | 获取 Web 应用部署槽的属性 |
> | microsoft.web/sites/slots/newpassword/action | Newpassword Web 应用槽。 |
> | microsoft.web/sites/slots/sync/action | 同步 Web 应用槽。 |
> | microsoft.web/sites/slots/networktrace/action | 网络跟踪 Web 应用槽。 |
> | microsoft.web/sites/slots/recover/action | 恢复 Web 应用槽。 |
> | microsoft.web/sites/slots/restoresnapshot/action | 还原 Web 应用槽快照。 |
> | microsoft.web/sites/slots/restorefromdeletedapp/action | 从已删除的应用还原 Web 应用槽位。 |
> | microsoft.web/sites/slots/backups/action | 发现 Web 应用槽备份。 |
> | microsoft.web/sites/slots/containerlogs/action | 获取 Web 应用槽的压缩容器日志。 |
> | microsoft.web/sites/slots/restorefrombackupblob/action | 从备份 Blob 还原 Web 应用槽。 |
> | microsoft.web/sites/slots/analyzecustomhostname/read | 获取 Web 应用槽分析自定义主机名。 |
> | microsoft.web/sites/slots/backup/write | 更新 Web 应用槽备份。 |
> | microsoft.web/sites/slots/backup/read | 获取 Web 应用槽备份。 |
> | Microsoft.Web/sites/slots/backups/Read | 获取 Web 应用槽的备份属性 |
> | microsoft.web/sites/slots/backups/list/action | 列出 Web 应用槽备份。 |
> | microsoft.web/sites/slots/backups/restore/action | 还原 Web 应用槽备份。 |
> | microsoft.web/sites/slots/backups/delete | 删除 Web 应用槽备份。 |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/Read | 列出可以用于 Web 应用槽的发布凭据 |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/Write | 列出可以用于 Web 应用槽的发布凭据 |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/ftp/Read | 获取 Web 应用槽是否允许 FTP 发布凭据 |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/ftp/Write | 更新 Web 应用槽是否允许 FTP 发布凭据 |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/scm/Read | 获取 Web 应用槽是否允许 SCM 发布凭据 |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/scm/Write | 更新 Web 应用槽是否允许 SCM 发布凭据 |
> | Microsoft.Web/sites/slots/config/Read | 获取 Web 应用槽的配置设置 |
> | Microsoft.Web/sites/slots/config/list/Action | 列出 Web 应用槽的安全敏感设置，例如发布凭据、应用设置和连接字符串 |
> | Microsoft.Web/sites/slots/config/Write | 更新 Web 应用槽的配置设置 |
> | microsoft.web/sites/slots/config/delete | 删除 Web 应用槽配置。 |
> | microsoft.web/sites/slots/config/snapshots/listsecrets/action | Web 应用列出快照中的槽机密。 |
> | microsoft.web/sites/slots/containerlogs/download/action | 下载 Web 应用槽容器日志。 |
> | microsoft.web/sites/slots/continuouswebjobs/delete | 删除 Web 应用槽连续 Web 作业。 |
> | microsoft.web/sites/slots/continuouswebjobs/read | 获取 Web 应用槽连续 Web 作业。 |
> | microsoft.web/sites/slots/continuouswebjobs/start/action | 启动 Web 应用槽连续 Web 作业。 |
> | microsoft.web/sites/slots/continuouswebjobs/stop/action | 停止 Web 应用槽连续 Web 作业。 |
> | microsoft.web/sites/slots/deployments/delete | 删除 Web 应用槽部署。 |
> | microsoft.web/sites/slots/deployments/read | 获取 Web 应用槽部署。 |
> | microsoft.web/sites/slots/deployments/write | 更新 Web 应用槽部署。 |
> | microsoft.web/sites/slots/deployments/log/read | 获取 Web 应用槽部署日志。 |
> | microsoft.web/sites/slots/detectors/read | 获取 Web 应用槽检测程序。 |
> | microsoft.web/sites/slots/diagnostics/read | 获取 Web 应用槽诊断。 |
> | microsoft.web/sites/slots/diagnostics/analyses/read | 获取 Web 应用槽诊断分析。 |
> | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | 运行 Web 应用槽诊断分析。 |
> | microsoft.web/sites/slots/diagnostics/aspnetcore/read | 获取 ASP.NET Core 应用的 Web 应用槽诊断。 |
> | microsoft.web/sites/slots/diagnostics/autoheal/read | 获取 Web 应用槽诊断 Autoheal。 |
> | microsoft.web/sites/slots/diagnostics/deployment/read | 获取 Web 应用槽诊断部署。 |
> | microsoft.web/sites/slots/diagnostics/deployments/read | 获取 Web 应用槽诊断部署。 |
> | microsoft.web/sites/slots/diagnostics/detectors/read | 获取 Web 应用槽诊断检测程序。 |
> | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | 运行 Web 应用槽诊断检测程序。 |
> | microsoft.web/sites/slots/diagnostics/frebanalysis/read | 获取 Web 应用槽诊断 FREB 分析。 |
> | microsoft.web/sites/slots/diagnostics/loganalyzer/read | 获取 Web 应用槽诊断日志分析器。 |
> | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | 获取 Web 应用槽诊断运行时可用性。 |
> | microsoft.web/sites/slots/diagnostics/servicehealth/read | 获取 Web 应用槽诊断服务运行状况。 |
> | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | 获取 Web 应用槽诊断站点 CPU 分析。 |
> | microsoft.web/sites/slots/diagnostics/sitecrashes/read | 获取 Web 应用槽诊断站点故障。 |
> | microsoft.web/sites/slots/diagnostics/sitelatency/read | 获取 Web 应用槽诊断站点延迟。 |
> | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | 获取 Web 应用槽诊断站点内存分析。 |
> | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | 获取 Web 应用槽诊断站点重启设置更新。 |
> | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | 获取 Web 应用槽诊断站点重启用户发起时间。 |
> | microsoft.web/sites/slots/diagnostics/siteswap/read | 获取 Web 应用槽诊断站点交换。 |
> | microsoft.web/sites/slots/diagnostics/threadcount/read | 获取 Web 应用槽诊断线程计数。 |
> | microsoft.web/sites/slots/diagnostics/workeravailability/read | 获取 Web 应用槽诊断 Workeravailability。 |
> | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | 获取 Web 应用槽诊断工作进程回收。 |
> | microsoft.web/sites/slots/domainownershipidentifiers/read | 获取 Web 应用槽域所有权标识符。 |
> | microsoft.web/sites/slots/extensions/read | 获取 Web 应用槽扩展。 |
> | microsoft.web/sites/slots/extensions/write | 更新 Web 应用槽扩展。 |
> | microsoft.web/sites/slots/functions/listkeys/action | 列出函数密钥。 |
> | microsoft.web/sites/slots/functions/read | 获取 Web 应用槽函数。 |
> | microsoft.web/sites/slots/functions/listsecrets/action | 列出机密 Web 应用槽函数。 |
> | microsoft.web/sites/slots/functions/keys/write | 更新函数密钥。 |
> | microsoft.web/sites/slots/functions/keys/delete | 删除函数密钥。 |
> | microsoft.web/sites/slots/host/listkeys/action | 列出函数宿主密钥。 |
> | microsoft.web/sites/slots/host/sync/action | 同步函数触发器。 |
> | microsoft.web/sites/slots/host/functionkeys/write | 更新 Functions 宿主函数密钥。 |
> | microsoft.web/sites/slots/host/functionkeys/delete | 删除 Functions 宿主函数密钥。 |
> | microsoft.web/sites/slots/host/systemkeys/write | 更新 Functions 宿主系统密钥。 |
> | microsoft.web/sites/slots/host/systemkeys/delete | 删除 Functions 宿主系统密钥。 |
> | microsoft.web/sites/slots/hostnamebindings/delete | 删除 Web 应用槽主机名绑定。 |
> | microsoft.web/sites/slots/hostnamebindings/read | 获取 Web 应用槽主机名绑定。 |
> | microsoft.web/sites/slots/hostnamebindings/write | 更新 Web 应用槽主机名绑定。 |
> | microsoft.web/sites/slots/hybridconnection/delete | 删除 Web 应用槽混合连接。 |
> | microsoft.web/sites/slots/hybridconnection/read | 获取 Web 应用槽混合连接。 |
> | microsoft.web/sites/slots/hybridconnection/write | 更新 Web 应用槽混合连接。 |
> | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | 删除 Web 应用槽混合连接命名空间中继。 |
> | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | 更新 Web 应用槽混合连接命名空间中继。 |
> | microsoft.web/sites/slots/hybridconnectionrelays/read | 获取 Web 应用槽混合连接中继。 |
> | microsoft.web/sites/slots/instances/read | 获取 Web 应用槽实例。 |
> | microsoft.web/sites/slots/instances/deployments/read | 获取 Web 应用槽实例部署。 |
> | microsoft.web/sites/slots/instances/processes/read | 获取 Web 应用槽实例进程。 |
> | microsoft.web/sites/slots/instances/processes/delete | 删除 Web 应用槽实例进程。 |
> | microsoft.web/sites/slots/metricdefinitions/read | 获取 Web 应用槽指标定义。 |
> | microsoft.web/sites/slots/metrics/read | 获取 Web 应用槽指标。 |
> | microsoft.web/sites/slots/migratemysql/read | 获取 Web 应用槽迁移 MySql。 |
> | microsoft.web/sites/slots/networkConfig/read | 获取应用服务槽网络配置。 |
> | microsoft.web/sites/slots/networkConfig/write | 更新应用服务槽网络配置。 |
> | microsoft.web/sites/slots/networkConfig/delete | 删除应用服务槽网络配置。 |
> | microsoft.web/sites/slots/networktraces/operationresults/read | 获取 Web 应用槽位网络跟踪操作结果。 |
> | microsoft.web/sites/slots/operationresults/read | 获取 Web 应用槽操作结果。 |
> | microsoft.web/sites/slots/operations/read | 获取 Web 应用槽操作。 |
> | microsoft.web/sites/slots/perfcounters/read | 获取 Web 应用槽性能计数器。 |
> | microsoft.web/sites/slots/phplogging/read | 获取 Web 应用槽 Phplogging。 |
> | microsoft.web/sites/slots/premieraddons/delete | 删除 Web 应用槽高级加载项。 |
> | microsoft.web/sites/slots/premieraddons/read | 获取 Web 应用槽高级加载项。 |
> | microsoft.web/sites/slots/premieraddons/write | 更新 Web 应用槽高级加载项。 |
> | microsoft.web/sites/slots/processes/read | 获取 Web 应用槽进程。 |
> | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | microsoft.web/sites/slots/providers/Microsoft.Insights/logDefinitions/read | 获取 Web 应用槽的可用日志 |
> | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | 获取 Web 应用槽的可用指标 |
> | microsoft.web/sites/slots/publiccertificates/read | 获取 Web 应用槽公共证书。 |
> | microsoft.web/sites/slots/publiccertificates/write | 创建或更新 Web 应用槽公共证书。 |
> | microsoft.web/sites/slots/publiccertificates/delete | 删除 Web 应用槽位公共证书。 |
> | microsoft.web/sites/slots/resourcehealthmetadata/read | 获取 Web 应用槽资源运行状况元数据。 |
> | microsoft.web/sites/slots/restore/read | 获取 Web 应用槽还原设置。 |
> | microsoft.web/sites/slots/restore/write | 还原 Web 应用槽。 |
> | microsoft.web/sites/slots/siteextensions/delete | 删除 Web 应用槽站点扩展。 |
> | microsoft.web/sites/slots/siteextensions/read | 获取 Web 应用槽站点扩展。 |
> | microsoft.web/sites/slots/siteextensions/write | 更新 Web 应用槽站点扩展。 |
> | microsoft.web/sites/slots/snapshots/read | 获取 Web 应用槽快照。 |
> | Microsoft.Web/sites/slots/sourcecontrols/Read | 获取 Web 应用槽的源代码管理配置设置 |
> | Microsoft.Web/sites/slots/sourcecontrols/Write | 更新 Web 应用槽的源代码管理配置设置 |
> | Microsoft.Web/sites/slots/sourcecontrols/Delete | 删除 Web 应用槽的源代码管理配置设置 |
> | microsoft.web/sites/slots/triggeredwebjobs/delete | 删除 Web 应用槽触发的 Web 作业。 |
> | microsoft.web/sites/slots/triggeredwebjobs/read | 获取 Web 应用槽触发的 Web 作业。 |
> | microsoft.web/sites/slots/triggeredwebjobs/run/action | 运行 Web 应用槽触发的 Web 作业。 |
> | microsoft.web/sites/slots/usages/read | 获取 Web 应用槽使用情况。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/delete | 删除 Web 应用槽虚拟网络连接。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/read | 获取 Web 应用槽虚拟网络连接。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/write | 更新 Web 应用槽虚拟网络连接。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | 更新 Web 应用槽虚拟网络连接网关。 |
> | microsoft.web/sites/slots/webjobs/read | 获取 Web 应用槽 Web 作业。 |
> | microsoft.web/sites/snapshots/read | 获取 Web 应用快照。 |
> | Microsoft.Web/sites/sourcecontrols/Read | 获取 Web 应用的源代码管理配置设置 |
> | Microsoft.Web/sites/sourcecontrols/Write | 更新 Web 应用的源代码管理配置设置 |
> | Microsoft.Web/sites/sourcecontrols/Delete | 删除 Web 应用的源代码管理配置设置 |
> | microsoft.web/sites/triggeredwebjobs/delete | 删除 Web 应用触发的 Web 作业。 |
> | microsoft.web/sites/triggeredwebjobs/read | 获取 Web 应用触发的 Web 作业。 |
> | microsoft.web/sites/triggeredwebjobs/run/action | 运行 Web 应用触发的 Web 作业。 |
> | microsoft.web/sites/triggeredwebjobs/history/read | 获取 Web 应用触发的 Web 作业历史记录。 |
> | microsoft.web/sites/usages/read | 获取 Web 应用使用情况。 |
> | microsoft.web/sites/virtualnetworkconnections/delete | 删除 Web 应用虚拟网络连接。 |
> | microsoft.web/sites/virtualnetworkconnections/read | 获取 Web 应用虚拟网络连接。 |
> | microsoft.web/sites/virtualnetworkconnections/write | 更新 Web 应用虚拟网络连接。 |
> | microsoft.web/sites/virtualnetworkconnections/gateways/read | 获取 Web 应用虚拟网络连接网关。 |
> | microsoft.web/sites/virtualnetworkconnections/gateways/write | 更新 Web 应用虚拟网络连接网关。 |
> | microsoft.web/sites/webjobs/read | 获取 Web 应用 Web 作业。 |
> | microsoft.web/skus/read | 获取 SKU。 |
> | microsoft.web/sourcecontrols/read | 获取源代码管理。 |
> | microsoft.web/sourcecontrols/write | 更新源代码管理。 |
> | Microsoft.Web/staticSites/Read | 获取静态站点的属性 |
> | Microsoft.Web/staticSites/Write | 创建新的静态站点，或更新现有静态站点 |
> | Microsoft.Web/staticSites/Delete | 删除现有静态站点 |
> | Microsoft.Web/staticSites/validateCustomDomainOwnership/action | 验证静态站点的自定义域所有权 |
> | Microsoft.Web/staticSites/createinvitation/action | 为静态站点用户创建一组角色的邀请链接 |
> | Microsoft.Web/staticSites/listConfiguredRoles/action | 列出为静态站点配置的角色。 |
> | Microsoft.Web/staticSites/listfunctionappsettings/Action | 列出静态站点的函数应用设置 |
> | Microsoft.Web/staticSites/listappsettings/Action | 列出静态站点的应用设置 |
> | Microsoft.Web/staticSites/detach/Action | 从当前链接的存储库拆离静态站点 |
> | Microsoft.Web/staticSites/getuser/Action | 获取静态站点的用户信息 |
> | Microsoft.Web/staticSites/listsecrets/action | 列出静态站点的机密 |
> | Microsoft.Web/staticSites/resetapikey/Action | 重置静态站点的 API 密钥 |
> | Microsoft.Web/staticSites/zipdeploy/action | 基于压缩的内容部署静态站点 |
> | Microsoft.Web/staticSites/authproviders/listusers/Action | 列出静态站点的用户 |
> | Microsoft.Web/staticSites/authproviders/users/Delete | 删除静态站点的用户 |
> | Microsoft.Web/staticSites/authproviders/users/Write | 更新静态站点的用户 |
> | Microsoft.Web/staticSites/build/Read | 获取静态站点的版本 |
> | Microsoft.Web/staticSites/build/Delete | 删除静态站点的版本 |
> | Microsoft.Web/staticSites/builds/listfunctionappsettings/Action | 列出静态站点版本的函数应用设置 |
> | Microsoft.Web/staticSites/builds/listappsettings/Action | 列出静态站点版本的应用设置 |
> | Microsoft.Web/staticSites/builds/zipdeploy/action | 基于压缩的内容部署静态站点版本 |
> | Microsoft.Web/staticSites/builds/config/Write | 创建或更新静态站点版本的应用设置 |
> | Microsoft.Web/staticSites/builds/functions/Read | 列出静态站点版本的函数 |
> | Microsoft.Web/staticSites/builds/userProvidedFunctionApps/Delete | 从静态站点版本分离用户提供的函数应用 |
> | Microsoft.Web/staticSites/builds/userProvidedFunctionApps/Read | 获取静态站点版本用户提供的函数应用 |
> | Microsoft.Web/staticSites/builds/userProvidedFunctionApps/Write | 使用静态站点版本注册用户提供的函数应用 |
> | Microsoft.Web/staticSites/config/Write | 创建或更新静态站点的应用设置 |
> | Microsoft.Web/staticSites/customdomains/Write | 为静态站点创建自定义域 |
> | Microsoft.Web/staticSites/customdomains/Delete | 删除静态站点的自定义域 |
> | Microsoft.Web/staticSites/customdomains/Read | 列出静态站点的自定义域 |
> | Microsoft.Web/staticSites/customdomains/validate/Action | 验证能否可以将自定义域添加到静态站点 |
> | Microsoft.Web/staticSites/functions/Read | 列出静态站点的函数 |
> | Microsoft.Web/staticSites/privateEndpointConnectionProxies/validate/action | 验证静态站点的专用终结点连接代理 |
> | Microsoft.Web/staticSites/privateEndpointConnectionProxies/Write | 创建或更新静态站点的专用终结点连接代理 |
> | Microsoft.Web/staticSites/privateEndpointConnectionProxies/Delete | 删除静态站点的专用终结点连接代理 |
> | Microsoft.Web/staticSites/privateEndpointConnectionProxies/Read | 获取静态站点的专用终结点连接代理 |
> | Microsoft.Web/staticSites/privateEndpointConnectionProxies/operations/Read | 读取静态站点的专用终结点连接代理操作 |
> | Microsoft.Web/staticSites/privateEndpointConnections/Write | 批准或拒绝静态站点的专用终结点连接 |
> | Microsoft.Web/staticSites/privateEndpointConnections/Read | 获取静态站点的专用终结点连接或专用终结点连接的列表 |
> | Microsoft.Web/staticSites/privateEndpointConnections/Delete | 删除静态站点的专用终结点连接 |
> | Microsoft.Web/staticSites/privateLinkResources/Read | 获取专用链接资源 |
> | Microsoft.Web/staticSites/providers/Microsoft.Insights/metricDefinitions/Read | 获取静态站点的可用指标 |
> | Microsoft.Web/staticSites/userProvidedFunctionApps/Delete | 从静态站点分离用户提供的函数应用 |
> | Microsoft.Web/staticSites/userProvidedFunctionApps/Read | 获取静态站点用户提供的函数应用 |
> | Microsoft.Web/staticSites/userProvidedFunctionApps/Write | 使用静态站点注册用户提供的函数应用 |
> | Microsoft.Web/workerApps/read | 获取辅助角色应用的属性 |
> | Microsoft.Web/workerApps/write | 创建辅助角色应用或更新现有的辅助角色应用 |
> | Microsoft.Web/workerApps/delete | 删除辅助角色应用 |
> | Microsoft.Web/workerApps/operationResults/read | 获取辅助角色应用操作的结果 |

## <a name="containers"></a>容器

### <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

Azure 服务：[容器实例](../container-instances/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ContainerInstance/register/action | 为容器实例资源提供程序注册订阅并启用容器组的创建。 |
> | Microsoft.ContainerInstance/containerGroups/read | 获取所有容器组。 |
> | Microsoft.ContainerInstance/containerGroups/write | 创建或更新特定容器组。 |
> | Microsoft.ContainerInstance/containerGroups/delete | 删除特定容器组。 |
> | Microsoft.ContainerInstance/containerGroups/restart/action | 重启特定容器组。 |
> | Microsoft.ContainerInstance/containerGroups/stop/action | 停止特定容器组。 计算资源将解除分配且计费将停止。 |
> | Microsoft.ContainerInstance/containerGroups/start/action | 启动特定容器组。 |
> | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 执行到特定容器中。 |
> | Microsoft.ContainerInstance/containerGroups/containers/attach/action | 附加到容器的输出流。 |
> | Microsoft.ContainerInstance/containerGroups/containers/buildlogs/read | 获取特定容器的生成日志。 |
> | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 获取特定容器的日志。 |
> | Microsoft.ContainerInstance/containerGroups/detectors/read | 列出容器组检测程序 |
> | Microsoft.ContainerInstance/containerGroups/operationResults/read | 获取异步操作结果 |
> | Microsoft.ContainerInstance/containerGroups/outboundNetworkDependenciesEndpoints/read | 列出容器组检测程序 |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | 获取容器组的诊断设置。 |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新容器组的诊断设置。 |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | 获取容器组的可用指标。 |
> | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.ContainerInstance 正在删除虚拟网络或子网。 |
> | Microsoft.ContainerInstance/locations/cachedImages/read | 获取某个区域中订阅的缓存映像。 |
> | Microsoft.ContainerInstance/locations/capabilities/read | 获取区域的功能。 |
> | Microsoft.ContainerInstance/locations/operationResults/read | 获取异步操作结果 |
> | Microsoft.ContainerInstance/locations/operations/read | 列出 Azure 容器实例服务的操作。 |
> | Microsoft.ContainerInstance/locations/usages/read | 获取特定区域的使用情况。 |
> | Microsoft.ContainerInstance/operations/read | 列出 Azure 容器实例服务的操作。 |
> | Microsoft.ContainerInstance/serviceassociationlinks/delete | 删除 Azure 容器实例资源提供程序在子网中创建的服务关联链接。 |

### <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

Azure 服务：[容器注册表](../container-registry/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ContainerRegistry/register/action | 注册容器注册表资源提供程序的订阅，并启用容器注册表的创建 |
> | Microsoft.ContainerRegistry/checkNameAvailability/read | 检查容器注册表名称是否可用。 |
> | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 向 Microsoft.ContainerRegistry 通知正在删除虚拟网络或子网 |
> | Microsoft.ContainerRegistry/locations/operationResults/read | 获取异步操作结果 |
> | Microsoft.ContainerRegistry/operations/read | 列出所有可用的 Azure 容器注册表 REST API 操作 |
> | Microsoft.ContainerRegistry/registries/read | 获取指定容器注册表的属性，或列出指定资源组或订阅下的所有容器注册表。 |
> | Microsoft.ContainerRegistry/registries/write | 使用指定参数创建或更新容器注册表。 |
> | Microsoft.ContainerRegistry/registries/delete | 删除容器注册表。 |
> | Microsoft.ContainerRegistry/registries/listCredentials/action | 列出指定容器注册表的登录凭据。 |
> | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 为指定容器注册表重新生成一个登录凭据。 |
> | Microsoft.ContainerRegistry/registries/generateCredentials/action | 生成指定容器注册表的令牌的密钥。 |
> | Microsoft.ContainerRegistry/registries/importImage/action | 使用指定的参数将映像导入到容器注册表中。 |
> | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | 获取上传位置，以便用户能够上传源。 |
> | Microsoft.ContainerRegistry/registries/queueBuild/action | 根据请求参数创建新生成，并将其添加到生成队列。 |
> | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | 获取容器注册表的源上传 URL 位置。 |
> | Microsoft.ContainerRegistry/registries/scheduleRun/action | 计划针对容器注册表的运行。 |
> | Microsoft.ContainerRegistry/registries/agentpools/read | 获取容器注册表的代理池，或列出所有代理池。 |
> | Microsoft.ContainerRegistry/registries/agentpools/write | 创建或更新容器注册表的代理池。 |
> | Microsoft.ContainerRegistry/registries/agentpools/delete | 删除容器注册表的代理池。 |
> | Microsoft.ContainerRegistry/registries/agentpools/listQueueStatus/action | 列出容器注册表的代理池的所有队列状态。 |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | 删除容器注册表中的项目。 |
> | Microsoft.ContainerRegistry/registries/builds/read | 获取指定生成的属性，或列出指定容器注册表的所有生成。 |
> | Microsoft.ContainerRegistry/registries/builds/write | 使用指定参数更新容器注册表的生成。 |
> | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | 获取链接以下载生成日志。 |
> | Microsoft.ContainerRegistry/registries/builds/cancel/action | 取消现有生成。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/read | 获取指定生成任务的属性，或列出指定容器注册表的所有生成任务。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/write | 使用指定参数创建或更新容器注册表的生成任务。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/delete | 从容器注册表中删除生成任务。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | 列出生成任务的源代码管理属性。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 获取指定生成步骤的属性，或列出指定生成任务的所有生成步骤。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 使用指定的参数创建或更新生成任务的生成步骤。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | 从生成任务中删除生成步骤。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | 列出生成步骤的生成参数，包括机密参数。 |
> | Microsoft.ContainerRegistry/registries/connectedRegistries/read | 获取指定的已连接注册表的属性，或列出指定容器注册表的所有已连接注册表。 |
> | Microsoft.ContainerRegistry/registries/connectedRegistries/write | 使用指定参数创建或更新容器注册表的已连接注册表。 |
> | Microsoft.ContainerRegistry/registries/connectedRegistries/delete | 从容器注册表中删除已连接注册表。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 获取指定事件网格筛选器的属性，或列出指定容器注册表的所有事件网格筛选器。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 使用指定参数创建或更新容器注册表的事件网格筛选器。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | 从容器注册表中删除事件网格筛选器。 |
> | Microsoft.ContainerRegistry/registries/exportPipelines/read | 获取指定导出管道的属性，或列出指定容器注册表的所有导出管道。 |
> | Microsoft.ContainerRegistry/registries/exportPipelines/write | 使用指定参数创建或更新容器注册表的导出管道。 |
> | Microsoft.ContainerRegistry/registries/exportPipelines/delete | 从容器注册表中删除导出管道。 |
> | Microsoft.ContainerRegistry/registries/importPipelines/read | 获取指定导入管道的属性，或列出指定容器注册表的所有导入管道。 |
> | Microsoft.ContainerRegistry/registries/importPipelines/write | 使用指定参数创建或更新容器注册表的导入管道。 |
> | Microsoft.ContainerRegistry/registries/importPipelines/delete | 从容器注册表中删除导入管道。 |
> | Microsoft.ContainerRegistry/registries/listPolicies/read | 列出指定容器注册表的策略 |
> | Microsoft.ContainerRegistry/registries/listUsages/read | 列出指定容器注册表的配额使用情况。 |
> | Microsoft.ContainerRegistry/registries/metadata/read | 获取容器注册表的特定存储库的元数据 |
> | Microsoft.ContainerRegistry/registries/metadata/write | 更新容器注册表的存储库的元数据 |
> | Microsoft.ContainerRegistry/registries/operationStatuses/read | 获取注册表异步操作状态 |
> | Microsoft.ContainerRegistry/registries/pipelineRuns/read | 获取指定管道运行的属性，或列出指定容器注册表的所有管道运行。 |
> | Microsoft.ContainerRegistry/registries/pipelineRuns/write | 使用指定参数创建或更新容器注册表的管道运行。 |
> | Microsoft.ContainerRegistry/registries/pipelineRuns/delete | 从容器注册表中删除管道运行。 |
> | Microsoft.ContainerRegistry/registries/pipelineRuns/operationStatuses/read | 获取管道运行异步操作状态。 |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理（仅限 NRP） |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/read | 获取专用终结点连接代理（仅限 NRP） |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/write | 创建专用终结点连接代理（仅限 NRP） |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理（仅限 NRP） |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/operationStatuses/read | 获取专用终结点连接代理异步操作状态 |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnections/read | 获取专用终结点连接的属性，或列出指定容器注册表的所有专用终结点连接 |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnections/write | 批准/拒绝专用终结点连接 |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/logDefinitions/read | 获取 Microsoft ContainerRegistry 的可用日志 |
> | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft ContainerRegistry 的可用指标。 |
> | Microsoft.ContainerRegistry/registries/pull/read | 从容器注册表中拉取或获取映像。 |
> | Microsoft.ContainerRegistry/registries/push/write | 将映像推送或写入容器注册表。 |
> | Microsoft.ContainerRegistry/registries/quarantine/read | 从容器注册表中拉取或获取已隔离的映像 |
> | Microsoft.ContainerRegistry/registries/quarantine/write | 写入/修改已隔离映像的隔离状态 |
> | Microsoft.ContainerRegistry/registries/replications/read | 获取指定复制的属性，或列出指定容器注册表的所有复制。 |
> | Microsoft.ContainerRegistry/registries/replications/write | 使用指定参数创建或更新容器注册表的复制。 |
> | Microsoft.ContainerRegistry/registries/replications/delete | 从容器注册表中删除复制。 |
> | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | 获取复制步操作状态 |
> | Microsoft.ContainerRegistry/registries/runs/read | 获取针对容器注册表的运行的属性，或列出运行。 |
> | Microsoft.ContainerRegistry/registries/runs/write | 更新运行。 |
> | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 获取运行的日志 SAS URL。 |
> | Microsoft.ContainerRegistry/registries/runs/cancel/action | 取消现有的运行。 |
> | Microsoft.ContainerRegistry/registries/scopeMaps/read | 获取指定范围映射的属性，或列出指定容器注册表的所有范围映射。 |
> | Microsoft.ContainerRegistry/registries/scopeMaps/write | 使用指定参数创建或更新容器注册表的范围映射。 |
> | Microsoft.ContainerRegistry/registries/scopeMaps/delete | 从容器注册表中删除范围映射。 |
> | Microsoft.ContainerRegistry/registries/scopeMaps/operationStatuses/read | 获取范围映射异步操作状态。 |
> | Microsoft.ContainerRegistry/registries/sign/write | 推送/拉取容器注册表的内容信任元数据。 |
> | Microsoft.ContainerRegistry/registries/taskruns/read | 获取容器注册表的任务运行，或列出所有任务运行。 |
> | Microsoft.ContainerRegistry/registries/taskruns/write | 创建或更新容器注册表的任务运行。 |
> | Microsoft.ContainerRegistry/registries/taskruns/delete | 删除容器注册表的任务运行。 |
> | Microsoft.ContainerRegistry/registries/taskruns/listDetails/action | 列出容器注册表的任务运行的所有详细信息。 |
> | Microsoft.ContainerRegistry/registries/tasks/read | 获取容器注册表的任务，或列出所有任务。 |
> | Microsoft.ContainerRegistry/registries/tasks/write | 创建或更新容器注册表的任务。 |
> | Microsoft.ContainerRegistry/registries/tasks/delete | 删除容器注册表的任务。 |
> | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | 列出容器注册表的任务的所有详细信息。 |
> | Microsoft.ContainerRegistry/registries/tokens/read | 获取指定令牌的属性，或列出指定容器注册表的所有令牌。 |
> | Microsoft.ContainerRegistry/registries/tokens/write | 使用指定参数创建或更新容器注册表的令牌。 |
> | Microsoft.ContainerRegistry/registries/tokens/delete | 从容器注册表中删除令牌。 |
> | Microsoft.ContainerRegistry/registries/tokens/operationStatuses/read | 获取令牌异步操作状态。 |
> | Microsoft.ContainerRegistry/registries/updatePolicies/write | 更新指定容器注册表的策略 |
> | Microsoft.ContainerRegistry/registries/webhooks/read | 获取指定 Webhook 的属性，或列出指定容器注册表的所有 Webhook。 |
> | Microsoft.ContainerRegistry/registries/webhooks/write | 使用指定参数创建或更新容器注册表的 Webhook。 |
> | Microsoft.ContainerRegistry/registries/webhooks/delete | 从容器注册表中删除 Webhook。 |
> | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | 获取服务 URI 的配置和 Webhook 的自定义标头。 |
> | Microsoft.ContainerRegistry/registries/webhooks/ping/action | 触发一个将发送到 Webhook 的 ping 事件。 |
> | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 列出指定 Webhook 的最新事件。 |
> | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | 获取 Webhook 异步操作状态 |
> | **DataAction** | **说明** |
> | Microsoft.ContainerRegistry/registries/quarantinedArtifacts/read | 允许从容器注册表中拉取或获取隔离项目。 这类似于 Microsoft.ContainerRegistry/registries/quarantine/read，只不过这是一个数据操作 |
> | Microsoft.ContainerRegistry/registries/quarantinedArtifacts/write | 允许写入或更新隔离项目的隔离状态。 这类似于 Microsoft.ContainerRegistry/registries/quarantine/write 操作，只不过这是一个数据操作 |
> | Microsoft.ContainerRegistry/registries/trustedCollections/write | 允许推送或发布容器注册表内容的受信任集合。 这类似于 Microsoft.ContainerRegistry/registries/sign/write 操作，只不过这是一个数据操作 |

### <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

Azure 服务：[Azure Kubernetes 服务 (AKS)](../aks/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ContainerService/register/action | 将订阅注册到 Microsoft.ContainerService 资源提供程序 |
> | Microsoft.ContainerService/unregister/action | 取消注册到 Microsoft.ContainerService 资源提供程序的订阅 |
> | Microsoft.ContainerService/containerServices/read | 获取容器服务 |
> | Microsoft.ContainerService/containerServices/write | 创建新的或更新现有的容器服务 |
> | Microsoft.ContainerService/containerServices/delete | 删除容器服务 |
> | Microsoft.ContainerService/locations/operationresults/read | 获取异步操作结果的状态 |
> | Microsoft.ContainerService/locations/operations/read | 获取异步操作的状态 |
> | Microsoft.ContainerService/locations/orchestrators/read | 列出支持的业务流程协调程序 |
> | Microsoft.ContainerService/locations/osOptions/read | 获取 OS 选项 |
> | Microsoft.ContainerService/managedClusters/read | 获取托管的群集 |
> | Microsoft.ContainerService/managedClusters/write | 创建新的或更新现有的托管的群集 |
> | Microsoft.ContainerService/managedClusters/delete | 删除托管的群集 |
> | Microsoft.ContainerService/managedClusters/start/action | 启动托管群集 |
> | Microsoft.ContainerService/managedClusters/stop/action | 停止托管群集 |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 列出托管群集的 clusterAdmin 凭据 |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 列出托管群集的 clusterUser 凭据 |
> | Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | 列出托管群集的 clusterMonitoringUser 凭据 |
> | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | 重置托管群集的服务主体配置文件 |
> | Microsoft.ContainerService/managedClusters/resolvePrivateLinkServiceId/action | 解析托管群集的专用链接服务 ID |
> | Microsoft.ContainerService/managedClusters/resetAADProfile/action | 重置托管群集的 AAD 配置文件 |
> | Microsoft.ContainerService/managedClusters/rotateClusterCertificates/action | 轮换托管群集的证书 |
> | Microsoft.ContainerService/managedClusters/runCommand/action | 针对托管 kubernetes 服务器运行用户发出的命令。 |
> | Microsoft.ContainerService/managedClusters/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.ContainerService/managedClusters/accessProfiles/read | 按角色名称获取托管的群集访问配置文件 |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 使用列表凭据按角色名称获取托管的群集访问配置文件 |
> | Microsoft.ContainerService/managedClusters/agentPools/read | 获取代理池 |
> | Microsoft.ContainerService/managedClusters/agentPools/write | 创建新的代理池，或更新现有的代理池 |
> | Microsoft.ContainerService/managedClusters/agentPools/delete | 删除代理池 |
> | Microsoft.ContainerService/managedClusters/agentPools/upgradeNodeImageVersion/write | 升级代理池的节点映像版本 |
> | Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles/read | 获取代理池的升级配置文件 |
> | Microsoft.ContainerService/managedClusters/availableAgentPoolVersions/read | 获取群集的可用代理池版本 |
> | Microsoft.ContainerService/managedClusters/commandResults/read | 检索以前发出的命令的结果。 |
> | Microsoft.ContainerService/managedClusters/detectors/read | 获取托管群集检测器 |
> | Microsoft.ContainerService/managedClusters/diagnosticsState/read | 获取群集的诊断状态 |
> | Microsoft.ContainerService/managedClusters/eventGridFilters/read | 获取 eventgrid 筛选器 |
> | Microsoft.ContainerService/managedClusters/eventGridFilters/write | 创建或更新 eventgrid 筛选器 |
> | Microsoft.ContainerService/managedClusters/eventGridFilters/delete | 删除 eventgrid 筛选器 |
> | Microsoft.ContainerService/managedClusters/extensionaddons/read | 获取扩展加载项 |
> | Microsoft.ContainerService/managedClusters/extensionaddons/write | 创建新的或更新现有的扩展加载项 |
> | Microsoft.ContainerService/managedClusters/extensionaddons/delete | 删除扩展加载项 |
> | Microsoft.ContainerService/managedClusters/maintenanceConfigurations/read | 获取维护配置 |
> | Microsoft.ContainerService/managedClusters/maintenanceConfigurations/write | 创建新的或更新现有的 MaintenanceConfiguration |
> | Microsoft.ContainerService/managedClusters/maintenanceConfigurations/delete | 删除维护配置 |
> | Microsoft.ContainerService/managedClusters/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.ContainerService/managedClusters/privateEndpointConnections/write | 批准或拒绝专用终结点连接 |
> | Microsoft.ContainerService/managedClusters/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | 获取托管的群集资源的诊断设置 |
> | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新托管的群集资源的诊断设置 |
> | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/logDefinitions/read | 获取托管群集的可用日志 |
> | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | 获取托管的群集的可用指标 |
> | Microsoft.ContainerService/managedClusters/upgradeProfiles/read | 获取群集的升级配置文件 |
> | Microsoft.ContainerService/openShiftClusters/read | 获取 Open Shift 群集 |
> | Microsoft.ContainerService/openShiftClusters/write | 创建新的或更新现有的 Open Shift 群集 |
> | Microsoft.ContainerService/openShiftClusters/delete | 删除 Open Shift 群集 |
> | Microsoft.ContainerService/openShiftManagedClusters/read | 获取 Open Shift 托管群集 |
> | Microsoft.ContainerService/openShiftManagedClusters/write | 创建新的或更新现有的 Open Shift 托管群集 |
> | Microsoft.ContainerService/openShiftManagedClusters/delete | 删除 Open Shift 托管群集 |
> | Microsoft.ContainerService/operations/read | 列出可对 Microsoft.ContainerService 资源提供程序执行的操作 |
> | **DataAction** | **说明** |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/initializerconfigurations/read | 读取 initializerconfigurations |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/initializerconfigurations/write | 写入 initializerconfigurations |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/initializerconfigurations/delete | Deletes/DeletesCollection initializerconfigurations 资源 |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/read | 读取 mutatingwebhookconfigurations |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/write | 写入 mutatingwebhookconfigurations |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/delete | 删除 mutatingwebhookconfigurations |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/read | 读取 validatingwebhookconfigurations |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/write | 写入 validatingwebhookconfigurations |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/delete | 删除 validatingwebhookconfigurations |
> | Microsoft.ContainerService/managedClusters/api/read | 读取 API |
> | Microsoft.ContainerService/managedClusters/api/v1/read | 读取 API/v1 |
> | Microsoft.ContainerService/managedClusters/apiextensions.k8s.io/customresourcedefinitions/read | 读取 customresourcedefinitions |
> | Microsoft.ContainerService/managedClusters/apiextensions.k8s.io/customresourcedefinitions/write | 写入 customresourcedefinitions |
> | Microsoft.ContainerService/managedClusters/apiextensions.k8s.io/customresourcedefinitions/delete | 删除 customresourcedefinitions |
> | Microsoft.ContainerService/managedClusters/apiregistration.k8s.io/apiservices/read | 读取 apiservices |
> | Microsoft.ContainerService/managedClusters/apiregistration.k8s.io/apiservices/write | 写入 apiservices |
> | Microsoft.ContainerService/managedClusters/apiregistration.k8s.io/apiservices/delete | 删除 apiservices |
> | Microsoft.ContainerService/managedClusters/apis/read | 读取 API |
> | Microsoft.ContainerService/managedClusters/apis/admissionregistration.k8s.io/read | 读取 admissionregistration.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/admissionregistration.k8s.io/v1/read | 读取 admissionregistration.k8s.io/v1 |
> | Microsoft.ContainerService/managedClusters/apis/admissionregistration.k8s.io/v1beta1/read | 读取 admissionregistration.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/apiextensions.k8s.io/read | 读取 apiextensions.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/apiextensions.k8s.io/v1/read | 读取 apiextensions.k8s.io/v1 |
> | Microsoft.ContainerService/managedClusters/apis/apiextensions.k8s.io/v1beta1/read | 读取 apiextensions.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/apiregistration.k8s.io/read | 读取 apiregistration.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/apiregistration.k8s.io/v1/read | 读取 apiregistration.k8s.io/v1 |
> | Microsoft.ContainerService/managedClusters/apis/apiregistration.k8s.io/v1beta1/read | 读取 apiregistration.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/apps/read | 读取 apps |
> | Microsoft.ContainerService/managedClusters/apis/apps/v1/read | 读取 apps/v1 |
> | Microsoft.ContainerService/managedClusters/apis/apps/v1beta1/read | 读取 apps/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/apps/v1beta2/read | 读取 apps/v1beta2 |
> | Microsoft.ContainerService/managedClusters/apis/authentication.k8s.io/read | 读取 authentication.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/authentication.k8s.io/v1/read | 读取 authentication.k8s.io/v1 |
> | Microsoft.ContainerService/managedClusters/apis/authentication.k8s.io/v1beta1/read | 读取 authentication.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/authorization.k8s.io/read | 读取 authorization.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/authorization.k8s.io/v1/read | 读取 authorization.k8s.io/v1 |
> | Microsoft.ContainerService/managedClusters/apis/authorization.k8s.io/v1beta1/read | 读取 authorization.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/autoscaling/read | 读取 autoscaling |
> | Microsoft.ContainerService/managedClusters/apis/autoscaling/v1/read | 读取 autoscaling/v1 |
> | Microsoft.ContainerService/managedClusters/apis/autoscaling/v2beta1/read | 读取 autoscaling/v2beta1 |
> | Microsoft.ContainerService/managedClusters/apis/autoscaling/v2beta2/read | 读取 autoscaling/v2beta2 |
> | Microsoft.ContainerService/managedClusters/apis/batch/read | 读取 batch |
> | Microsoft.ContainerService/managedClusters/apis/batch/v1/read | 读取batch/v1 |
> | Microsoft.ContainerService/managedClusters/apis/batch/v1beta1/read | 读取 batch/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/certificates.k8s.io/read | 读取 certificates.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/certificates.k8s.io/v1beta1/read | 读取 certificates.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/coordination.k8s.io/read | 读取 coordination.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/coordination.k8s.io/v1/read | 读取 coordination/v1 |
> | Microsoft.ContainerService/managedClusters/apis/coordination.k8s.io/v1beta1/read | 读取 coordination.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/events.k8s.io/read | 读取 events.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/events.k8s.io/v1beta1/read | 读取 events.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/extensions/read | 读取 extensions |
> | Microsoft.ContainerService/managedClusters/apis/extensions/v1beta1/read | 读取 extensions/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/metrics.k8s.io/read | 读取 metrics.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/metrics.k8s.io/v1beta1/read | 读取 metrics.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/networking.k8s.io/read | 读取 networking.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/networking.k8s.io/v1/read | 读取 networking/v1 |
> | Microsoft.ContainerService/managedClusters/apis/networking.k8s.io/v1beta1/read | 读取 networking.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/node.k8s.io/read | 读取 node.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/node.k8s.io/v1beta1/read | 读取 node.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/policy/read | 读取 policy |
> | Microsoft.ContainerService/managedClusters/apis/policy/v1beta1/read | 读取 policy/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/rbac.authorization.k8s.io/read | 读取 rbac.authorization.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/rbac.authorization.k8s.io/v1/read | 读取 rbac.authorization/v1 |
> | Microsoft.ContainerService/managedClusters/apis/rbac.authorization.k8s.io/v1beta1/read | 读取 rbac.authorization.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/scheduling.k8s.io/read | 读取 scheduling.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/scheduling.k8s.io/v1/read | 读取 scheduling/v1 |
> | Microsoft.ContainerService/managedClusters/apis/scheduling.k8s.io/v1beta1/read | 读取 scheduling.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apis/storage.k8s.io/read | 读取 storage.k8s.io |
> | Microsoft.ContainerService/managedClusters/apis/storage.k8s.io/v1/read | 读取 storage/v1 |
> | Microsoft.ContainerService/managedClusters/apis/storage.k8s.io/v1beta1/read | 读取 storage.k8s.io/v1beta1 |
> | Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read | 读取 controllerrevisions |
> | Microsoft.ContainerService/managedClusters/apps/controllerrevisions/write | 写入 controllerrevisions |
> | Microsoft.ContainerService/managedClusters/apps/controllerrevisions/delete | 删除 controllerrevisions |
> | Microsoft.ContainerService/managedClusters/apps/daemonsets/read | 读取 daemonsets |
> | Microsoft.ContainerService/managedClusters/apps/daemonsets/write | 写入 daemonsets |
> | Microsoft.ContainerService/managedClusters/apps/daemonsets/delete | 删除 daemonsets |
> | Microsoft.ContainerService/managedClusters/apps/deployments/read | 读取 deployments |
> | Microsoft.ContainerService/managedClusters/apps/deployments/write | 写入 deployments |
> | Microsoft.ContainerService/managedClusters/apps/deployments/delete | 删除 deployments |
> | Microsoft.ContainerService/managedClusters/apps/replicasets/read | 读取 replicasets |
> | Microsoft.ContainerService/managedClusters/apps/replicasets/write | 写入 replicasets |
> | Microsoft.ContainerService/managedClusters/apps/replicasets/delete | 删除 replicasets |
> | Microsoft.ContainerService/managedClusters/apps/statefulsets/read | 读取 statefulsets |
> | Microsoft.ContainerService/managedClusters/apps/statefulsets/write | 写入 statefulsets |
> | Microsoft.ContainerService/managedClusters/apps/statefulsets/delete | 删除 statefulsets |
> | Microsoft.ContainerService/managedClusters/authentication.k8s.io/tokenreviews/write | 写入 tokenreviews |
> | Microsoft.ContainerService/managedClusters/authentication.k8s.io/userextras/impersonate/action | 模拟 userextras |
> | Microsoft.ContainerService/managedClusters/authorization.k8s.io/localsubjectaccessreviews/write | 写入 localsubjectaccessreviews |
> | Microsoft.ContainerService/managedClusters/authorization.k8s.io/selfsubjectaccessreviews/write | 写入 selfsubjectaccessreviews |
> | Microsoft.ContainerService/managedClusters/authorization.k8s.io/selfsubjectrulesreviews/write | 写入 selfsubjectrulesreviews |
> | Microsoft.ContainerService/managedClusters/authorization.k8s.io/subjectaccessreviews/write | 写入 subjectaccessreviews |
> | Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read | 读取 horizontalpodautoscalers |
> | Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/write | 写入 horizontalpodautoscalers |
> | Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/delete | 删除 horizontalpodautoscalers |
> | Microsoft.ContainerService/managedClusters/batch/cronjobs/read | 读取 cronjobs |
> | Microsoft.ContainerService/managedClusters/batch/cronjobs/write | 写入 cronjobs |
> | Microsoft.ContainerService/managedClusters/batch/cronjobs/delete | 删除 cronjobs |
> | Microsoft.ContainerService/managedClusters/batch/jobs/read | 读取作业 |
> | Microsoft.ContainerService/managedClusters/batch/jobs/write | 写入 jobs |
> | Microsoft.ContainerService/managedClusters/batch/jobs/delete | 删除 jobs |
> | Microsoft.ContainerService/managedClusters/bindings/write | 写入 bindings |
> | Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/read | 读取 certificatesigningrequests |
> | Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/write | 写入 certificatesigningrequests |
> | Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/delete | 删除 certificatesigningrequests |
> | Microsoft.ContainerService/managedClusters/componentstatuses/read | 读取 componentstatuses |
> | Microsoft.ContainerService/managedClusters/componentstatuses/write | 写入 componentstatuses |
> | Microsoft.ContainerService/managedClusters/componentstatuses/delete | 删除 componentstatuses |
> | Microsoft.ContainerService/managedClusters/configmaps/read | 读取 configmaps |
> | Microsoft.ContainerService/managedClusters/configmaps/write | 写入 configmaps |
> | Microsoft.ContainerService/managedClusters/configmaps/delete | 删除 configmaps |
> | Microsoft.ContainerService/managedClusters/coordination.k8s.io/leases/read | 读取 leases |
> | Microsoft.ContainerService/managedClusters/coordination.k8s.io/leases/write | 写入 leases |
> | Microsoft.ContainerService/managedClusters/coordination.k8s.io/leases/delete | 删除 leases |
> | Microsoft.ContainerService/managedClusters/endpoints/read | 读取 endpoints |
> | Microsoft.ContainerService/managedClusters/endpoints/write | 写入 endpoints |
> | Microsoft.ContainerService/managedClusters/endpoints/delete | 删除 endpoints |
> | Microsoft.ContainerService/managedClusters/events/read | 读取 events |
> | Microsoft.ContainerService/managedClusters/events/write | 写入 events |
> | Microsoft.ContainerService/managedClusters/events/delete | 删除 events |
> | Microsoft.ContainerService/managedClusters/events.k8s.io/events/read | 读取 events |
> | Microsoft.ContainerService/managedClusters/events.k8s.io/events/write | 写入 events |
> | Microsoft.ContainerService/managedClusters/events.k8s.io/events/delete | 删除 events |
> | Microsoft.ContainerService/managedClusters/extensions/daemonsets/read | 读取 daemonsets |
> | Microsoft.ContainerService/managedClusters/extensions/daemonsets/write | 写入 daemonsets |
> | Microsoft.ContainerService/managedClusters/extensions/daemonsets/delete | 删除 daemonsets |
> | Microsoft.ContainerService/managedClusters/extensions/deployments/read | 读取 deployments |
> | Microsoft.ContainerService/managedClusters/extensions/deployments/write | 写入 deployments |
> | Microsoft.ContainerService/managedClusters/extensions/deployments/delete | 删除 deployments |
> | Microsoft.ContainerService/managedClusters/extensions/ingresses/read | 读取 ingresses |
> | Microsoft.ContainerService/managedClusters/extensions/ingresses/write | 写入 ingresses |
> | Microsoft.ContainerService/managedClusters/extensions/ingresses/delete | 删除 ingresses |
> | Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read | 读取 networkpolicies |
> | Microsoft.ContainerService/managedClusters/extensions/networkpolicies/write | 写入 networkpolicies |
> | Microsoft.ContainerService/managedClusters/extensions/networkpolicies/delete | 删除 networkpolicies |
> | Microsoft.ContainerService/managedClusters/extensions/podsecuritypolicies/read | 读取 podsecuritypolicies |
> | Microsoft.ContainerService/managedClusters/extensions/podsecuritypolicies/write | 写入 podsecuritypolicies |
> | Microsoft.ContainerService/managedClusters/extensions/podsecuritypolicies/delete | 删除 podsecuritypolicies |
> | Microsoft.ContainerService/managedClusters/extensions/replicasets/read | 读取 replicasets |
> | Microsoft.ContainerService/managedClusters/extensions/replicasets/write | 写入 replicasets |
> | Microsoft.ContainerService/managedClusters/extensions/replicasets/delete | 删除 replicasets |
> | Microsoft.ContainerService/managedClusters/groups/impersonate/action | 模拟 groups |
> | Microsoft.ContainerService/managedClusters/healthz/read | 读取 healthz |
> | Microsoft.ContainerService/managedClusters/healthz/autoregister-completion/read | 读取 autoregister-completion |
> | Microsoft.ContainerService/managedClusters/healthz/etcd/read | 读取 etcd |
> | Microsoft.ContainerService/managedClusters/healthz/log/read | 读取 log |
> | Microsoft.ContainerService/managedClusters/healthz/ping/read | 读取 ping |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/apiservice-openapi-controller/read | 读取 apiservice-openapi-controller |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/apiservice-registration-controller/read | 读取 apiservice-registration-controller |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/apiservice-status-available-controller/read | 读取 apiservice-status-available-controller |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/bootstrap-controller/read | 读取 bootstrap-controller |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/ca-registration/read | 读取 ca-registration |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/crd-informer-synced/read | 读取 crd-informer-synced |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/generic-apiserver-start-informers/read | 读取 generic-apiserver-start-informers |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/kube-apiserver-autoregistration/read | 读取 kube-apiserver-autoregistration |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/rbac/bootstrap-roles/read | 读取 bootstrap-roles |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/scheduling/bootstrap-system-priority-classes/read | 读取 bootstrap-system-priority-classes |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/start-apiextensions-controllers/read | 读取 start-apiextensions-controllers |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/start-apiextensions-informers/read | 读取 start-apiextensions-informers |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/start-kube-aggregator-informers/read | 读取 start-kube-aggregator-informers |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/start-kube-apiserver-admission-initializer/read | 读取 start-kube-apiserver-admission-initializer |
> | Microsoft.ContainerService/managedClusters/limitranges/read | 读取 limitranges |
> | Microsoft.ContainerService/managedClusters/limitranges/write | 写入 limitranges |
> | Microsoft.ContainerService/managedClusters/limitranges/delete | 删除 limitranges |
> | Microsoft.ContainerService/managedClusters/livez/read | 读取 livez |
> | Microsoft.ContainerService/managedClusters/livez/autoregister-completion/read | 读取 autoregister-completion |
> | Microsoft.ContainerService/managedClusters/livez/etcd/read | 读取 etcd |
> | Microsoft.ContainerService/managedClusters/livez/log/read | 读取 log |
> | Microsoft.ContainerService/managedClusters/livez/ping/read | 读取 ping |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/apiservice-openapi-controller/read | 读取 apiservice-openapi-controller |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/apiservice-registration-controller/read | 读取 apiservice-registration-controller |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/apiservice-status-available-controller/read | 读取 apiservice-status-available-controller |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/bootstrap-controller/read | 读取 bootstrap-controller |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/ca-registration/read | 读取 ca-registration |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/crd-informer-synced/read | 读取 crd-informer-synced |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/generic-apiserver-start-informers/read | 读取 generic-apiserver-start-informers |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/kube-apiserver-autoregistration/read | 读取 kube-apiserver-autoregistration |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/rbac/bootstrap-roles/read | 读取 bootstrap-roles |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/scheduling/bootstrap-system-priority-classes/read | 读取 bootstrap-system-priority-classes |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/start-apiextensions-controllers/read | 读取 start-apiextensions-controllers |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/start-apiextensions-informers/read | 读取 start-apiextensions-informers |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/start-kube-aggregator-informers/read | 读取 start-kube-aggregator-informers |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/start-kube-apiserver-admission-initializer/read | 读取 start-kube-apiserver-admission-initializer |
> | Microsoft.ContainerService/managedClusters/logs/read | 读取 logs |
> | Microsoft.ContainerService/managedClusters/metrics/read | 读取 metrics |
> | Microsoft.ContainerService/managedClusters/metrics.k8s.io/nodes/read | 读取 nodes |
> | Microsoft.ContainerService/managedClusters/metrics.k8s.io/pods/read | 读取 Pod |
> | Microsoft.ContainerService/managedClusters/namespaces/read | 读取 namespaces |
> | Microsoft.ContainerService/managedClusters/namespaces/write | 写入 namespaces |
> | Microsoft.ContainerService/managedClusters/namespaces/delete | 删除 namespaces |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read | 读取 ingresses |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/write | 写入 ingresses |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/delete | 删除 ingresses |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read | 读取 networkpolicies |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/write | 写入 networkpolicies |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/delete | 删除 networkpolicies |
> | Microsoft.ContainerService/managedClusters/node.k8s.io/runtimeclasses/read | 读取 runtimeclasses |
> | Microsoft.ContainerService/managedClusters/node.k8s.io/runtimeclasses/write | 写入 runtimeclasses |
> | Microsoft.ContainerService/managedClusters/node.k8s.io/runtimeclasses/delete | 删除 runtimeclasses |
> | Microsoft.ContainerService/managedClusters/nodes/read | 读取 nodes |
> | Microsoft.ContainerService/managedClusters/nodes/write | 写入 nodes |
> | Microsoft.ContainerService/managedClusters/nodes/delete | 删除 nodes |
> | Microsoft.ContainerService/managedClusters/openapi/v2/read | 读取 v2 |
> | Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read | 读取 persistentvolumeclaims |
> | Microsoft.ContainerService/managedClusters/persistentvolumeclaims/write | 写入 persistentvolumeclaims |
> | Microsoft.ContainerService/managedClusters/persistentvolumeclaims/delete | 删除 persistentvolumeclaims |
> | Microsoft.ContainerService/managedClusters/persistentvolumes/read | 读取 persistentvolumes |
> | Microsoft.ContainerService/managedClusters/persistentvolumes/write | 写入 persistentvolumes |
> | Microsoft.ContainerService/managedClusters/persistentvolumes/delete | 删除 persistentvolumes |
> | Microsoft.ContainerService/managedClusters/pods/read | 读取 Pod |
> | Microsoft.ContainerService/managedClusters/pods/write | 写入 Pod |
> | Microsoft.ContainerService/managedClusters/pods/delete | 删除 Pod |
> | Microsoft.ContainerService/managedClusters/pods/exec/action | 执行到 Pod 资源 |
> | Microsoft.ContainerService/managedClusters/podtemplates/read | 读取 podtemplates |
> | Microsoft.ContainerService/managedClusters/podtemplates/write | 写入 podtemplates |
> | Microsoft.ContainerService/managedClusters/podtemplates/delete | 删除 podtemplates |
> | Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read | 读取 poddisruptionbudgets |
> | Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/write | 写入 poddisruptionbudgets |
> | Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/delete | 删除 poddisruptionbudgets |
> | Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/read | 读取 podsecuritypolicies |
> | Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/write | 写入 podsecuritypolicies |
> | Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/delete | 删除 podsecuritypolicies |
> | Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/use/action | 对 podsecuritypolicies 执行操作 |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterrolebindings/read | 读取 clusterrolebindings |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterrolebindings/write | 写入 clusterrolebindings |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterrolebindings/delete | 删除 clusterrolebindings |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/read | 读取 clusterroles |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/write | 写入 clusterroles |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/delete | 删除 clusterroles |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/bind/action | 绑定 clusterroles |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/escalate/action | 上报 |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/rolebindings/read | 读取 rolebindings |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/rolebindings/write | 写入 rolebindings |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/rolebindings/delete | 删除 rolebindings |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/read | 读取 roles |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/write | 写入 roles |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/delete | 删除 roles |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/bind/action | 绑定 roles |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/escalate/action | 上报 roles |
> | Microsoft.ContainerService/managedClusters/readyz/read | 读取 readyz |
> | Microsoft.ContainerService/managedClusters/readyz/autoregister-completion/read | 读取 autoregister-completion |
> | Microsoft.ContainerService/managedClusters/readyz/etcd/read | 读取 etcd |
> | Microsoft.ContainerService/managedClusters/readyz/log/read | 读取 log |
> | Microsoft.ContainerService/managedClusters/readyz/ping/read | 读取 ping |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/apiservice-openapi-controller/read | 读取 apiservice-openapi-controller |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/apiservice-registration-controller/read | 读取 apiservice-registration-controller |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/apiservice-status-available-controller/read | 读取 apiservice-status-available-controller |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/bootstrap-controller/read | 读取 bootstrap-controller |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/ca-registration/read | 读取 ca-registration |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/crd-informer-synced/read | 读取 crd-informer-synced |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/generic-apiserver-start-informers/read | 读取 generic-apiserver-start-informers |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/kube-apiserver-autoregistration/read | 读取 kube-apiserver-autoregistration |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/rbac/bootstrap-roles/read | 读取 bootstrap-roles |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/scheduling/bootstrap-system-priority-classes/read | 读取 bootstrap-system-priority-classes |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/start-apiextensions-controllers/read | 读取 start-apiextensions-controllers |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/start-apiextensions-informers/read | 读取 start-apiextensions-informers |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/start-kube-aggregator-informers/read | 读取 start-kube-aggregator-informers |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/start-kube-apiserver-admission-initializer/read | 读取 start-kube-apiserver-admission-initializer |
> | Microsoft.ContainerService/managedClusters/readyz/shutdown/read | 读取 shutdown |
> | Microsoft.ContainerService/managedClusters/replicationcontrollers/read | 读取 replicationcontrollers |
> | Microsoft.ContainerService/managedClusters/replicationcontrollers/write | 写入 replicationcontrollers |
> | Microsoft.ContainerService/managedClusters/replicationcontrollers/delete | 删除 replicationcontrollers |
> | Microsoft.ContainerService/managedClusters/resetMetrics/read | 读取 resetMetrics |
> | Microsoft.ContainerService/managedClusters/resourcequotas/read | 读取 resourcequotas |
> | Microsoft.ContainerService/managedClusters/resourcequotas/write | 写入 resourcequotas |
> | Microsoft.ContainerService/managedClusters/resourcequotas/delete | 删除 resourcequotas |
> | Microsoft.ContainerService/managedClusters/scheduling.k8s.io/priorityclasses/read | 读取 priorityclasses |
> | Microsoft.ContainerService/managedClusters/scheduling.k8s.io/priorityclasses/write | 写入 priorityclasses |
> | Microsoft.ContainerService/managedClusters/scheduling.k8s.io/priorityclasses/delete | 删除 priorityclasses |
> | Microsoft.ContainerService/managedClusters/secrets/read | 读取 secrets |
> | Microsoft.ContainerService/managedClusters/secrets/write | 写入 secrets |
> | Microsoft.ContainerService/managedClusters/secrets/delete | 删除 secrets |
> | Microsoft.ContainerService/managedClusters/serviceaccounts/read | 读取 serviceaccounts |
> | Microsoft.ContainerService/managedClusters/serviceaccounts/write | 写入 serviceaccounts |
> | Microsoft.ContainerService/managedClusters/serviceaccounts/delete | 删除 serviceaccounts |
> | Microsoft.ContainerService/managedClusters/serviceaccounts/impersonate/action | 模拟 serviceaccounts |
> | Microsoft.ContainerService/managedClusters/services/read | 读取 services |
> | Microsoft.ContainerService/managedClusters/services/write | 写入 services |
> | Microsoft.ContainerService/managedClusters/services/delete | 删除 services |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csidrivers/read | 读取 csidrivers |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csidrivers/write | 写入 csidrivers |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csidrivers/delete | 删除 csidrivers |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csinodes/read | 读取 csinodes |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csinodes/write | 写入 csinodes |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csinodes/delete | 删除 csinodes |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/storageclasses/read | 读取 storageclasses |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/storageclasses/write | 写入 storageclasses |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/storageclasses/delete | 删除 storageclasses |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/volumeattachments/read | 读取 volumeattachments |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/volumeattachments/write | 写入 volumeattachments |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/volumeattachments/delete | 删除 volumeattachments |
> | Microsoft.ContainerService/managedClusters/swagger-api/read | 读取 swagger-api |
> | Microsoft.ContainerService/managedClusters/swagger-ui/read | 读取 swagger-ui |
> | Microsoft.ContainerService/managedClusters/ui/read | 读取 ui |
> | Microsoft.ContainerService/managedClusters/users/impersonate/action | 模拟用户 |
> | Microsoft.ContainerService/managedClusters/version/read | 读取 version |

## <a name="databases"></a>数据库

### <a name="microsoftcache"></a>Microsoft.Cache

Azure 服务：[用于 Redis 的 Azure 缓存](../azure-cache-for-redis/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Cache/checknameavailability/action | 检查名称是否可用于新的 Redis 缓存 |
> | Microsoft.Cache/register/action | 将“Microsoft.Cache”资源提供程序注册到订阅 |
> | Microsoft.Cache/unregister/action | 从订阅中取消注册“Microsoft.Cache”资源提供程序 |
> | Microsoft.Cache/locations/checknameavailability/action | 检查名称是否可用于新的 Redis Enterprise 缓存 |
> | Microsoft.Cache/locations/asyncOperations/read | 读取异步操作的状态 |
> | Microsoft.Cache/locations/operationResults/read | 获取之前将“Location”标头返回给客户端的长时间运行的操作结果 |
> | Microsoft.Cache/locations/operationsStatus/read | 查看之前将“AzureAsync”标头返回到客户端的长时间运行的操作的状态 |
> | Microsoft.Cache/operations/read | 列出“Microsoft.Cache”提供程序支持的操作。 |
> | Microsoft.Cache/redis/write | 在管理门户中修改 Redis 缓存的设置和配置 |
> | Microsoft.Cache/redis/read | 在管理门户中查看 Redis 缓存的设置和配置 |
> | Microsoft.Cache/redis/delete | 删除整个 Redis 缓存 |
> | Microsoft.Cache/redis/listKeys/action | 在管理门户中查看 Redis 缓存访问密钥的值 |
> | Microsoft.Cache/redis/regenerateKey/action | 在管理门户中更改 Redis 缓存访问密钥的值 |
> | Microsoft.Cache/redis/import/action | 将多个 Blob 中指定格式的数据导入 Redis |
> | Microsoft.Cache/redis/export/action | 将 Redis 数据以指定的格式导出到带前缀的存储 Blob |
> | Microsoft.Cache/redis/forceReboot/action | 强制重新启动缓存实例（可能会发生数据丢失）。 |
> | Microsoft.Cache/redis/stop/action | 停止 Azure Cache for Redis，可能会丢失数据。 |
> | Microsoft.Cache/redis/start/action | 启动 Azure Cache for Redis |
> | Microsoft.Cache/redis/PrivateEndpointConnectionsApproval/action | 审批专用终结点连接 |
> | Microsoft.Cache/redis/eventGridFilters/read | 获取 Redis 缓存事件网格筛选器 |
> | Microsoft.Cache/redis/eventGridFilters/write | 更新 Redis 缓存事件网格筛选器 |
> | Microsoft.Cache/redis/eventGridFilters/delete | 删除 Redis 缓存事件网格筛选器 |
> | Microsoft.Cache/redis/firewallRules/read | 获取 Redis 缓存的 IP 防火墙规则 |
> | Microsoft.Cache/redis/firewallRules/write | 编辑 Redis 缓存的 IP 防火墙规则 |
> | Microsoft.Cache/redis/firewallRules/delete | 删除 Redis 缓存的 IP 防火墙规则 |
> | Microsoft.Cache/redis/linkedServers/read | 获取与 Redis 缓存关联的链接服务器。 |
> | Microsoft.Cache/redis/linkedServers/write | 将链接服务器添加到 Redis 缓存 |
> | Microsoft.Cache/redis/linkedServers/delete | 从 Redis 缓存中删除链接服务器 |
> | Microsoft.Cache/redis/metricDefinitions/read | 获取 Redis 缓存的可用指标 |
> | Microsoft.Cache/redis/patchSchedules/read | 获取 Redis 缓存的修补计划 |
> | Microsoft.Cache/redis/patchSchedules/write | 修改 Redis 缓存的修补计划 |
> | Microsoft.Cache/redis/patchSchedules/delete | 删除 Redis 缓存的修补计划 |
> | Microsoft.Cache/redis/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Cache/redis/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.Cache/redis/privateEndpointConnectionProxies/write | 创建专用终结点连接代理 |
> | Microsoft.Cache/redis/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Cache/redis/privateEndpointConnections/read | 读取专用终结点连接 |
> | Microsoft.Cache/redis/privateEndpointConnections/write | 写入专用终结点连接 |
> | Microsoft.Cache/redis/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Cache/redis/privateLinkResources/read | 读取专用链接可以连接到的 Redis 子资源的“groupId” |
> | Microsoft.Cache/redisEnterprise/delete | 删除整个 Redis Enterprise 缓存 |
> | Microsoft.Cache/redisEnterprise/read | 在管理门户中查看 Redis Enterprise 缓存的设置和配置 |
> | Microsoft.Cache/redisEnterprise/write | 在管理门户中修改 Redis Enterprise 缓存的设置和配置 |
> | Microsoft.Cache/redisEnterprise/PrivateEndpointConnectionsApproval/action | 审批专用终结点连接 |
> | Microsoft.Cache/redisEnterprise/databases/delete | 删除 Redis Enterprise 数据库及其内容 |
> | Microsoft.Cache/redisEnterprise/databases/read | 在管理门户中查看 Redis Enterprise 缓存数据库的设置和配置 |
> | Microsoft.Cache/redisEnterprise/databases/write | 在管理门户中修改 Redis Enterprise 缓存数据库的设置和配置 |
> | Microsoft.Cache/redisEnterprise/databases/export/action | 将 Redis Enterprise 数据库中的数据导出到存储 Blob  |
> | Microsoft.Cache/redisEnterprise/databases/forceUnlink/action | 强制将异地副本 Redis Enterprise 数据库与其对等方取消关联 |
> | Microsoft.Cache/redisEnterprise/databases/import/action | 将存储 Blob 中数据导入 Redis Enterprise 数据库 |
> | Microsoft.Cache/redisEnterprise/databases/listKeys/action | 在管理门户中查看 Redis Enterprise 数据库访问密钥的值 |
> | Microsoft.Cache/redisEnterprise/databases/regenerateKey/action | 在管理门户中更改 Redis Enterprise 数据库访问密钥的值 |
> | Microsoft.Cache/redisEnterprise/databases/operationResults/read | 在管理门户中查看 Redis Enterprise 数据库操作的结果 |
> | Microsoft.Cache/redisEnterprise/operationResults/read | 在管理门户中查看 Redis Enterprise 操作的结果 |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/write | 创建专用终结点连接代理 |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/operationResults/read | 在管理门户中查看专用终结点连接操作的结果 |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnections/read | 读取专用终结点连接 |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnections/write | 写入专用终结点连接 |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Cache/redisEnterprise/privateLinkResources/read | 读取专用链接可以连接到的 Redis 子资源的“groupId” |
> | Microsoft.Cache/redisEnterprise/providers/Microsoft.Insights/metricDefinitions/read | 获取 Redis Enterprise 缓存的可用指标 |

### <a name="microsoftdatafactory"></a>Microsoft.DataFactory

Azure 服务：[Data Factory](../data-factory/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DataFactory/register/action | 注册数据工厂资源提供程序的订阅。 |
> | Microsoft.DataFactory/unregister/action | 取消注册数据工厂资源提供程序的订阅。 |
> | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | 检查数据工厂名称是否可用。 |
> | Microsoft.DataFactory/datafactories/read | 读取数据工厂。 |
> | Microsoft.DataFactory/datafactories/write | 创建或更新数据工厂。 |
> | Microsoft.DataFactory/datafactories/delete | 删除数据工厂。 |
> | Microsoft.DataFactory/datafactories/activitywindows/read | 使用指定的参数读取数据工厂中的活动时段。 |
> | Microsoft.DataFactory/datafactories/datapipelines/read | 读取任何管道。 |
> | Microsoft.DataFactory/datafactories/datapipelines/delete | 删除任何管道。 |
> | Microsoft.DataFactory/datafactories/datapipelines/pause/action | 暂停任何管道。 |
> | Microsoft.DataFactory/datafactories/datapipelines/resume/action | 恢复任何管道。 |
> | Microsoft.DataFactory/datafactories/datapipelines/update/action | 更新任何管道。 |
> | Microsoft.DataFactory/datafactories/datapipelines/write | 创建或更新任何管道。 |
> | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 使用指定的参数读取管道活动中的活动时段。 |
> | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 使用指定的参数读取管道的活动时段。 |
> | Microsoft.DataFactory/datafactories/datasets/read | 读取任何数据集。 |
> | Microsoft.DataFactory/datafactories/datasets/delete | 删除任何数据集。 |
> | Microsoft.DataFactory/datafactories/datasets/write | 创建或更新任何数据集。 |
> | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 使用指定的参数读取数据集的活动时段。 |
> | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 从给定的开始时间读取针对给定数据集的数据切片运行。 |
> | Microsoft.DataFactory/datafactories/datasets/slices/read | 获取给定期间中的数据切片。 |
> | Microsoft.DataFactory/datafactories/datasets/slices/write | 更新数据切片的状态。 |
> | Microsoft.DataFactory/datafactories/gateways/read | 读取任何网关。 |
> | Microsoft.DataFactory/datafactories/gateways/write | 创建或更新任何网关。 |
> | Microsoft.DataFactory/datafactories/gateways/delete | 删除任何网关。 |
> | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | 读取任何网关的连接信息。 |
> | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | 列出任何网关的身份验证密钥。 |
> | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | 重新生成任何网关的身份验证密钥。 |
> | Microsoft.DataFactory/datafactories/linkedServices/read | 读取任何链接的服务。 |
> | Microsoft.DataFactory/datafactories/linkedServices/delete | 删除任何链接的服务。 |
> | Microsoft.DataFactory/datafactories/linkedServices/write | 创建或更新任何链接的服务。 |
> | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | 获取数据工厂的可用指标 |
> | Microsoft.DataFactory/datafactories/runs/loginfo/read | 将 SAS URI 读取到包含日志的 blob 容器。 |
> | Microsoft.DataFactory/datafactories/tables/read | 读取任何数据集。 |
> | Microsoft.DataFactory/datafactories/tables/delete | 删除任何数据集。 |
> | Microsoft.DataFactory/datafactories/tables/write | 创建或更新任何数据集。 |
> | Microsoft.DataFactory/factories/read | 读取数据工厂。 |
> | Microsoft.DataFactory/factories/write | 创建或更新数据工厂 |
> | Microsoft.DataFactory/factories/delete | 删除数据工厂。 |
> | Microsoft.DataFactory/factories/createdataflowdebugsession/action | 创建数据流调试会话。 |
> | Microsoft.DataFactory/factories/startdataflowdebugsession/action | 启动数据流调试会话。 |
> | Microsoft.DataFactory/factories/addDataFlowToDebugSession/action | 将数据流添加到调试会话以进行预览。 |
> | Microsoft.DataFactory/factories/executeDataFlowDebugCommand/action | 执行数据流调试命令。 |
> | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | 删除数据流调试会话。 |
> | Microsoft.DataFactory/factories/querydataflowdebugsessions/action | 查询数据流调试会话。 |
> | Microsoft.DataFactory/factories/cancelpipelinerun/action | 取消由运行 ID 指定的管道运行。 |
> | Microsoft.DataFactory/factories/cancelSandboxPipelineRun/action | 取消管道的调试运行。 |
> | Microsoft.DataFactory/factories/sandboxpipelineruns/action | 查询调试管道运行。 |
> | Microsoft.DataFactory/factories/querytriggers/action | 查询触发器。 |
> | Microsoft.DataFactory/factories/getFeatureValue/action | 获取特定位置的公开控件功能值。 |
> | Microsoft.DataFactory/factories/queryFeaturesValue/action | 获取功能列表的公开控制功能值 |
> | Microsoft.DataFactory/factories/getDataPlaneAccess/action | 获取对 ADF DataPlane 服务的访问权限。 |
> | Microsoft.DataFactory/factories/getGitHubAccessToken/action | 获取 GitHub 访问令牌。 |
> | Microsoft.DataFactory/factories/querytriggerruns/action | 查询触发器运行。 |
> | Microsoft.DataFactory/factories/querypipelineruns/action | 查询管道运行。 |
> | Microsoft.DataFactory/factories/querydebugpipelineruns/action | 查询调试管道运行。 |
> | Microsoft.DataFactory/factories/dataflows/read | 读取数据流。 |
> | Microsoft.DataFactory/factories/dataflows/delete | 删除数据流。 |
> | Microsoft.DataFactory/factories/dataflows/write | 创建或更新数据流 |
> | Microsoft.DataFactory/factories/datasets/read | 读取任何数据集。 |
> | Microsoft.DataFactory/factories/datasets/delete | 删除任何数据集。 |
> | Microsoft.DataFactory/factories/datasets/write | 创建或更新任何数据集。 |
> | Microsoft.DataFactory/factories/debugpipelineruns/cancel/action | 取消管道的调试运行。 |
> | Microsoft.DataFactory/factories/getDataPlaneAccess/read | 读取对 ADF DataPlane 服务的访问权限。 |
> | Microsoft.DataFactory/factories/getFeatureValue/read | 读取特定位置的公开控件功能值。 |
> | Microsoft.DataFactory/factories/integrationruntimes/read | 读取任何集成运行时。 |
> | Microsoft.DataFactory/factories/integrationruntimes/write | 创建或更新任何集成运行时。 |
> | Microsoft.DataFactory/factories/integrationruntimes/delete | 删除任何集成运行时。 |
> | Microsoft.DataFactory/factories/integrationruntimes/start/action | 启动任何集成运行时。 |
> | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 停止任何集成运行时。 |
> | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | 读取集成运行时连接信息。 |
> | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/action | 列出任何集成运行时的身份验证密钥。 |
> | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 同步指定的集成运行时的凭据。 |
> | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 升级指定的集成运行时。 |
> | Microsoft.DataFactory/factories/integrationruntimes/createexpressshirinstalllink/action | 为自托管 Integration Runtime 创建快速安装链接。 |
> | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 重新生成指定的集成运行时的身份验证密钥。 |
> | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | 从指定的集成运行时中删除链接的集成运行时引用。 |
> | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | 在指定的共享 Integration Runtime 上创建链接的 Integration Runtime 引用。 |
> | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | 获取指定的 Integration Runtime 的 SSIS Integration Runtime 元数据。 |
> | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | 刷新指定的 Integration Runtime 的 SSIS Integration Runtime 元数据。 |
> | Microsoft.DataFactory/factories/integrationruntimes/enableInteractiveQuery/action | 启用交互式创作会话。 |
> | Microsoft.DataFactory/factories/integrationruntimes/disableInteractiveQuery/action | 禁用交互式创作会话。 |
> | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | 读取集成运行时状态。 |
> | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 获取任何集成运行时的监视数据。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 读取指定的集成运行时的节点。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 删除指定的集成运行时的节点。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 更新自承载集成运行时节点。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | 返回集成运行时的指定节点的 IP 地址。 |
> | Microsoft.DataFactory/factories/integrationruntimes/outboundNetworkDependenciesEndpoints/read | 获取指定 Integration Runtime 的 Azure-SSIS Integration Runtime 出站网络依赖关系终结点。 |
> | Microsoft.DataFactory/factories/linkedServices/read | 读取链接的服务。 |
> | Microsoft.DataFactory/factories/linkedServices/delete | 删除链接的服务。 |
> | Microsoft.DataFactory/factories/linkedServices/write | 创建或更新链接的服务 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/read | 读取托管虚拟网络。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/write | 创建或更新托管虚拟网络。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints/read | 读取托管专用终结点。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints/write | 创建或更新托管专用终结点。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints/delete | 删除托管专用终结点。 |
> | Microsoft.DataFactory/factories/operationResults/read | 获取操作结果。 |
> | Microsoft.DataFactory/factories/pipelineruns/read | 读取管道运行。 |
> | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 取消由运行 ID 指定的管道运行。 |
> | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 查询指定的管道运行 ID 的活动运行。 |
> | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 读取指定的管道运行 ID 的活动运行。 |
> | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 读取指定管道运行 ID 的查询活动运行的结果。 |
> | Microsoft.DataFactory/factories/pipelines/read | 读取管道。 |
> | Microsoft.DataFactory/factories/pipelines/delete | 删除管道。 |
> | Microsoft.DataFactory/factories/pipelines/write | 创建或更新管道 |
> | Microsoft.DataFactory/factories/pipelines/createrun/action | 为管道创建运行。 |
> | Microsoft.DataFactory/factories/pipelines/sandbox/action | 为管道创建调试运行环境。 |
> | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | 读取管道运行。 |
> | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | 获取活动运行的进程。 |
> | Microsoft.DataFactory/factories/pipelines/sandbox/create/action | 为管道创建调试运行环境。 |
> | Microsoft.DataFactory/factories/pipelines/sandbox/run/action | 为管道创建调试运行。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/read | 读取专用终结点连接代理。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/operationresults/read | 读取创建专用终结点连接代理操作的结果。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/operationstatuses/read | 读取创建专用终结点连接代理操作的状态。 |
> | Microsoft.DataFactory/factories/privateEndpointConnections/read | 读取专用终结点连接。 |
> | Microsoft.DataFactory/factories/privateEndpointConnections/write | 创建或更新专用终结点连接。 |
> | Microsoft.DataFactory/factories/privateEndpointConnections/delete | 删除专用终结点连接。 |
> | Microsoft.DataFactory/factories/privateLinkResources/read | 读取专用链接资源。 |
> | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | 获取工厂的可用日志 |
> | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | 获取工厂的可用指标 |
> | Microsoft.DataFactory/factories/queryFeaturesValue/read | 读取功能列表的曝光控制功能值。 |
> | Microsoft.DataFactory/factories/querypipelineruns/read | 读取查询管道运行的结果。 |
> | Microsoft.DataFactory/factories/querytriggerruns/read | 读取触发器运行的结果。 |
> | Microsoft.DataFactory/factories/sandboxpipelineruns/read | 获取管道的调试运行信息。 |
> | Microsoft.DataFactory/factories/sandboxpipelineruns/sandboxActivityRuns/read | 获取活动的调试运行信息。 |
> | Microsoft.DataFactory/factories/triggerruns/read | 读取触发器运行。 |
> | Microsoft.DataFactory/factories/triggers/read | 读取任何触发器。 |
> | Microsoft.DataFactory/factories/triggers/write | 创建或更新任何触发器。 |
> | Microsoft.DataFactory/factories/triggers/delete | 删除任何触发器。 |
> | Microsoft.DataFactory/factories/triggers/subscribetoevents/action | 订阅事件。 |
> | Microsoft.DataFactory/factories/triggers/geteventsubscriptionstatus/action | 事件订阅状态。 |
> | Microsoft.DataFactory/factories/triggers/unsubscribefromevents/action | 取消订阅事件。 |
> | Microsoft.DataFactory/factories/triggers/querysubscriptionevents/action | 查询订阅事件。 |
> | Microsoft.DataFactory/factories/triggers/deletequeuedsubscriptionevents/action | 删除排队的订阅事件。 |
> | Microsoft.DataFactory/factories/triggers/start/action | 启动任何触发器。 |
> | Microsoft.DataFactory/factories/triggers/stop/action | 停止任何触发器。 |
> | Microsoft.DataFactory/factories/triggers/triggerruns/read | 读取触发器运行。 |
> | Microsoft.DataFactory/factories/triggers/triggerruns/cancel/action | 取消具有给定触发器运行 ID 的触发器运行。 |
> | Microsoft.DataFactory/factories/triggers/triggerruns/rerun/action | 重新运行具有给定触发器运行 ID 的触发器运行。 |
> | Microsoft.DataFactory/locations/configureFactoryRepo/action | 配置工厂的存储库。 |
> | Microsoft.DataFactory/locations/getFeatureValue/action | 获取特定位置的公开控件功能值。 |
> | Microsoft.DataFactory/locations/getFeatureValue/read | 读取特定位置的公开控件功能值。 |
> | Microsoft.DataFactory/operations/read | 读取 Microsoft 数据工厂提供程序中的所有操作。 |

### <a name="microsoftdatamigration"></a>Microsoft.DataMigration

Azure 服务：[Azure 数据库迁移服务](../dms/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DataMigration/locations/sqlMigrationServiceOperationResults/read | 检索服务操作结果 |
> | Microsoft.DataMigration/operations/read | 获取所有 REST 操作 |
> | Microsoft.DataMigration/sqlMigrationServices/write | 创建新服务或更改现有服务的属性 |
> | Microsoft.DataMigration/sqlMigrationServices/delete | 删除现有服务 |
> | Microsoft.DataMigration/sqlMigrationServices/write | 更新服务的标记 |
> | Microsoft.DataMigration/sqlMigrationServices/read | 检索迁移服务的详细信息 |
> | Microsoft.DataMigration/sqlMigrationServices/read | 在资源组中检索迁移服务的详细信息 |
> | Microsoft.DataMigration/sqlMigrationServices/listAuthKeys/action | 检索身份验证密钥列表 |
> | Microsoft.DataMigration/sqlMigrationServices/regenerateAuthKeys/action | 再生成身份验证密钥 |
> | Microsoft.DataMigration/sqlMigrationServices/deleteNode/action |  |
> | Microsoft.DataMigration/sqlMigrationServices/read | 检索订阅中的所有服务 |
> | Microsoft.DataMigration/sqlMigrationServices/getMonitoringData/read | 检索监视数据 |
> | Microsoft.DataMigration/sqlMigrationServices/listMigrations/read |  |
> | Microsoft.DataMigration/sqlMigrationServices/MonitoringData/read | 检索监视数据 |

### <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

Azure 服务：[Azure Database for MariaDB](../mariadb/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DBforMariaDB/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.DBforMariaDB/register/action | 注册 MariaDB 资源提供程序 |
> | Microsoft.DBforMariaDB/checkNameAvailability/action | 验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。 |
> | Microsoft.DBforMariaDB/locations/administratorAzureAsyncOperation/read | 获取正在对 MariaDB 服务器管理员进行的操作 |
> | Microsoft.DBforMariaDB/locations/administratorOperationResults/read | 返回 MariaDB 服务器管理员操作结果 |
> | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | 返回 MariaDB 服务器操作结果 |
> | Microsoft.DBforMariaDB/locations/operationResults/read | 返回基于 ResourceGroup 的 MariaDB 服务器操作结果 |
> | Microsoft.DBforMariaDB/locations/operationResults/read | 返回 MariaDB 服务器操作结果 |
> | Microsoft.DBforMariaDB/locations/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforMariaDB/locations/privateEndpointConnectionAzureAsyncOperation/read | 获取专用终结点连接操作的结果 |
> | Microsoft.DBforMariaDB/locations/privateEndpointConnectionOperationResults/read | 获取专用终结点连接操作的结果 |
> | Microsoft.DBforMariaDB/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.DBforMariaDB/locations/privateEndpointConnectionProxyOperationResults/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | 返回服务器威胁检测操作结果列表。 |
> | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | 返回服务器威胁检测操作结果列表。 |
> | Microsoft.DBforMariaDB/locations/serverKeyAzureAsyncOperation/read | 获取正在对数据加密服务器密钥进行的操作 |
> | Microsoft.DBforMariaDB/locations/serverKeyOperationResults/read | 获取正在对透明数据加密服务器密钥进行的操作 |
> | Microsoft.DBforMariaDB/operations/read | 返回 MariaDB 操作列表。 |
> | Microsoft.DBforMariaDB/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforMariaDB/servers/start/action | 启动特定服务器。 |
> | Microsoft.DBforMariaDB/servers/stop/action | 停止特定服务器。 |
> | Microsoft.DBforMariaDB/servers/resetQueryPerformanceInsightData/action | 重置 Query Performance Insight 数据 |
> | Microsoft.DBforMariaDB/servers/queryTexts/action | 返回查询列表的文本 |
> | Microsoft.DBforMariaDB/servers/queryTexts/action | 返回查询的文本 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.DBforMariaDB/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.DBforMariaDB/servers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | Microsoft.DBforMariaDB/servers/delete | 删除现有服务器。 |
> | Microsoft.DBforMariaDB/servers/restart/action | 重启特定的服务器。 |
> | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 更新指定服务器的配置 |
> | Microsoft.DBforMariaDB/servers/administrators/read | 获取 MariaDB 服务器管理员列表。 |
> | Microsoft.DBforMariaDB/servers/administrators/write | 使用指定参数创建或更新 MariaDB 服务器管理员。 |
> | Microsoft.DBforMariaDB/servers/administrators/delete | 删除 MariaDB 服务器的现有管理员。 |
> | Microsoft.DBforMariaDB/servers/advisors/read | 返回顾问列表 |
> | Microsoft.DBforMariaDB/servers/advisors/read | 返回顾问 |
> | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | 创建新的建议操作会话 |
> | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 返回建议的操作列表 |
> | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 返回建议的操作列表 |
> | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 返回建议的操作 |
> | Microsoft.DBforMariaDB/servers/configurations/read | 返回服务器的配置列表，或获取指定配置的属性。 |
> | Microsoft.DBforMariaDB/servers/configurations/write | 更新指定配置的值 |
> | Microsoft.DBforMariaDB/servers/databases/read | 返回 MariaDB 数据库列表，或获取指定数据库的属性。 |
> | Microsoft.DBforMariaDB/servers/databases/write | 使用指定的参数创建 MariaDB 数据库，或更新指定数据库的属性。 |
> | Microsoft.DBforMariaDB/servers/databases/delete | 删除现有的 MariaDB 数据库。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/read | 返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/write | 使用指定参数创建防火墙规则，或更新现有规则。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/delete | 删除现有防火墙规则。 |
> | Microsoft.DBforMariaDB/servers/keys/read | 返回服务器密钥的列表，或获取指定服务器密钥的属性。 |
> | Microsoft.DBforMariaDB/servers/keys/write | 使用指定参数创建密钥，或更新指定服务器密钥的属性或标记。 |
> | Microsoft.DBforMariaDB/servers/keys/delete | 删除现有服务器密钥。 |
> | Microsoft.DBforMariaDB/servers/logFiles/read | 返回 MariaDB LogFiles 列表。 |
> | Microsoft.DBforMariaDB/servers/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/validate/action | 从 NRP 端验证专用终结点连接创建调用 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/read | 返回专用终结点连接代理列表，或获取指定专用终结点连接代理的属性。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/write | 使用指定参数创建专用终结点连接代理，或更新指定专用终结点连接代理的属性或标记。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/delete | 删除现有的专用终结点连接代理 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnections/read | 返回专用终结点连接列表，或获取指定专用终结点连接的属性。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnections/delete | 删除现有的专用终结点连接 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnections/write | 批准或拒绝现有的专用终结点连接 |
> | Microsoft.DBforMariaDB/servers/privateLinkResources/read | 获取相应 MariaDB 服务器的专用链接资源 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | 获取 MariaDB 服务器的可用日志 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | Microsoft.DBforMariaDB/servers/recoverableServers/read | 返回可恢复的 MariaDB 服务器信息 |
> | Microsoft.DBforMariaDB/servers/replicas/read | 获取 MariaDB 服务器的只读副本 |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 检索在给定的服务器上配置的服务器威胁检测策略的详细信息 |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 更改给定服务器的服务器威胁检测策略 |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 检索为给定服务器配置的服务器威胁检测策略列表 |
> | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | 返回热门查询的查询统计信息列表。 |
> | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | 返回查询统计信息 |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。 |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。 |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 删除现有虚拟网络规则 |
> | Microsoft.DBforMariaDB/servers/waitStatistics/read | 返回实例的等待统计信息 |
> | Microsoft.DBforMariaDB/servers/waitStatistics/read | 返回等待统计信息 |

### <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

Azure 服务：[Azure Database for MySQL](../mysql/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DBforMySQL/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.DBforMySQL/register/action | 注册 MySQL 资源提供程序 |
> | Microsoft.DBforMySQL/checkNameAvailability/action | 验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。 |
> | Microsoft.DBforMySQL/flexibleServers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.DBforMySQL/flexibleServers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | Microsoft.DBforMySQL/flexibleServers/delete | 删除现有服务器。 |
> | Microsoft.DBforMySQL/flexibleServers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DBforMySQL/flexibleServers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DBforMySQL/flexibleServers/providers/Microsoft.Insights/logDefinitions/read | 获取 MySQL 服务器的可用日志 |
> | Microsoft.DBforMySQL/flexibleServers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | Microsoft.DBforMySQL/locations/administratorAzureAsyncOperation/read | 获取正在对 MySQL 服务器管理员进行的操作 |
> | Microsoft.DBforMySQL/locations/administratorOperationResults/read | 返回 MySQL 服务器管理员操作结果 |
> | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | 返回 MySQL 服务器操作结果 |
> | Microsoft.DBforMySQL/locations/operationResults/read | 返回基于 ResourceGroup 的 MySQL 服务器操作结果 |
> | Microsoft.DBforMySQL/locations/operationResults/read | 返回 MySQL 服务器操作结果 |
> | Microsoft.DBforMySQL/locations/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforMySQL/locations/privateEndpointConnectionAzureAsyncOperation/read | 获取专用终结点连接操作的结果 |
> | Microsoft.DBforMySQL/locations/privateEndpointConnectionOperationResults/read | 获取专用终结点连接操作的结果 |
> | Microsoft.DBforMySQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.DBforMySQL/locations/privateEndpointConnectionProxyOperationResults/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | 返回服务器威胁检测操作结果列表。 |
> | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | 返回服务器威胁检测操作结果列表。 |
> | Microsoft.DBforMySQL/locations/serverKeyAzureAsyncOperation/read | 获取正在对透明数据加密服务器密钥进行的操作 |
> | Microsoft.DBforMySQL/locations/serverKeyOperationResults/read | 获取正在对数据加密服务器密钥进行的操作 |
> | Microsoft.DBforMySQL/operations/read | 返回 MySQL 操作列表。 |
> | Microsoft.DBforMySQL/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforMySQL/servers/upgrade/action |  |
> | Microsoft.DBforMySQL/servers/start/action | 启动特定服务器。 |
> | Microsoft.DBforMySQL/servers/stop/action | 停止特定服务器。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.DBforMySQL/servers/resetQueryPerformanceInsightData/action | 重置 Query Performance Insight 数据 |
> | Microsoft.DBforMySQL/servers/queryTexts/action | 返回查询列表的文本 |
> | Microsoft.DBforMySQL/servers/queryTexts/action | 返回查询的文本 |
> | Microsoft.DBforMySQL/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.DBforMySQL/servers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | Microsoft.DBforMySQL/servers/delete | 删除现有服务器。 |
> | Microsoft.DBforMySQL/servers/restart/action | 重启特定的服务器。 |
> | Microsoft.DBforMySQL/servers/updateConfigurations/action | 更新指定服务器的配置 |
> | Microsoft.DBforMySQL/servers/administrators/read | 获取 MySQL 服务器管理员列表。 |
> | Microsoft.DBforMySQL/servers/administrators/write | 使用指定参数创建或更新 MySQL 服务器管理员。 |
> | Microsoft.DBforMySQL/servers/administrators/delete | 删除 MySQL 服务器的现有管理员。 |
> | Microsoft.DBforMySQL/servers/advisors/read | 返回顾问列表 |
> | Microsoft.DBforMySQL/servers/advisors/read | 返回顾问 |
> | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | 创建新的建议操作会话 |
> | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 返回建议的操作列表 |
> | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 返回建议的操作列表 |
> | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 返回建议的操作 |
> | Microsoft.DBforMySQL/servers/configurations/read | 返回服务器的配置列表，或获取指定配置的属性。 |
> | Microsoft.DBforMySQL/servers/configurations/write | 更新指定配置的值 |
> | Microsoft.DBforMySQL/servers/databases/read | 返回 MySQL 数据库列表，或获取指定数据库的属性。 |
> | Microsoft.DBforMySQL/servers/databases/write | 使用指定的参数创建 MySQL 数据库，或更新指定数据库的属性。 |
> | Microsoft.DBforMySQL/servers/databases/delete | 删除现有的 MySQL 数据库。 |
> | Microsoft.DBforMySQL/servers/exports/write |  |
> | Microsoft.DBforMySQL/servers/exports/read |  |
> | Microsoft.DBforMySQL/servers/exports/read |  |
> | Microsoft.DBforMySQL/servers/firewallRules/read | 返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。 |
> | Microsoft.DBforMySQL/servers/firewallRules/write | 使用指定参数创建防火墙规则，或更新现有规则。 |
> | Microsoft.DBforMySQL/servers/firewallRules/delete | 删除现有防火墙规则。 |
> | Microsoft.DBforMySQL/servers/keys/read | 返回服务器密钥的列表，或获取指定服务器密钥的属性。 |
> | Microsoft.DBforMySQL/servers/keys/write | 使用指定参数创建密钥，或更新指定服务器密钥的属性或标记。 |
> | Microsoft.DBforMySQL/servers/keys/delete | 删除现有服务器密钥。 |
> | Microsoft.DBforMySQL/servers/logFiles/read | 返回 MySQL LogFiles 列表。 |
> | Microsoft.DBforMySQL/servers/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/validate/action | 从 NRP 端验证专用终结点连接创建调用 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/read | 返回专用终结点连接代理列表，或获取指定专用终结点连接代理的属性。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/write | 使用指定参数创建专用终结点连接代理，或更新指定专用终结点连接代理的属性或标记。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/delete | 删除现有的专用终结点连接代理 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnections/read | 返回专用终结点连接列表，或获取指定专用终结点连接的属性。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnections/delete | 删除现有的专用终结点连接 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnections/write | 批准或拒绝现有的专用终结点连接 |
> | Microsoft.DBforMySQL/servers/privateLinkResources/read | 获取相应 MySQL 服务器的专用链接资源 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | 获取 MySQL 服务器的可用日志 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | Microsoft.DBforMySQL/servers/recoverableServers/read | 返回可恢复的 MySQL 服务器信息 |
> | Microsoft.DBforMySQL/servers/replicas/read | 获取 MySQL 服务器的只读副本 |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 检索在给定的服务器上配置的服务器威胁检测策略的详细信息 |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 更改给定服务器的服务器威胁检测策略 |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 检索为给定服务器配置的服务器威胁检测策略列表 |
> | Microsoft.DBforMySQL/servers/topQueryStatistics/read | 返回热门查询的查询统计信息列表。 |
> | Microsoft.DBforMySQL/servers/topQueryStatistics/read | 返回查询统计信息 |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。 |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。 |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 删除现有虚拟网络规则 |
> | Microsoft.DBforMySQL/servers/waitStatistics/read | 返回实例的等待统计信息 |
> | Microsoft.DBforMySQL/servers/waitStatistics/read | 返回等待统计信息 |

### <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

Azure 服务：[Azure Database for PostgreSQL](../postgresql/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DBforPostgreSQL/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.DBforPostgreSQL/register/action | 注册 PostgreSQL 资源提供程序 |
> | Microsoft.DBforPostgreSQL/checkNameAvailability/action | 验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/delete | 删除现有服务器。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DBforPostgreSQL/flexibleServers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DBforPostgreSQL/flexibleServers/providers/Microsoft.Insights/logDefinitions/read | 获取 PostgreSQL 服务器的可用日志 |
> | Microsoft.DBforPostgreSQL/flexibleServers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | Microsoft.DBforPostgreSQL/locations/administratorAzureAsyncOperation/read | 获取正在对 PostgreSQL 服务器管理员进行的操作 |
> | Microsoft.DBforPostgreSQL/locations/administratorOperationResults/read | 返回 PostgreSQL 服务器管理员操作结果 |
> | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | 返回 PostgreSQL 服务器操作结果 |
> | Microsoft.DBforPostgreSQL/locations/operationResults/read | 返回基于 ResourceGroup 的 PostgreSQL 服务器操作结果 |
> | Microsoft.DBforPostgreSQL/locations/operationResults/read | 返回 PostgreSQL 服务器操作结果 |
> | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionAzureAsyncOperation/read | 获取专用终结点连接操作的结果 |
> | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionOperationResults/read | 获取专用终结点连接操作的结果 |
> | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionProxyOperationResults/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | 返回服务器威胁检测操作结果列表。 |
> | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | 返回服务器威胁检测操作结果列表。 |
> | Microsoft.DBforPostgreSQL/locations/serverKeyAzureAsyncOperation/read | 获取正在对数据加密服务器密钥进行的操作 |
> | Microsoft.DBforPostgreSQL/locations/serverKeyOperationResults/read | 获取正在对数据加密服务器密钥进行的操作 |
> | Microsoft.DBforPostgreSQL/operations/read | 返回 PostgreSQL 操作列表。 |
> | Microsoft.DBforPostgreSQL/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateEndpointConnectionsApproval/action | 确定是否允许用户批准 PostgreSQL SGv2 的专用终结点连接 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateEndpointConnectionProxies/read | 返回专用终结点连接列表，或通过代理获取指定专用终结点连接的属性 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateEndpointConnectionProxies/write | 使用指定参数创建专用终结点连接，或通过代理更新指定专用终结点连接的属性或标记 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateEndpointConnectionProxies/delete | 通过代理删除现有的专用终结点连接 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateEndpointConnectionProxies/validate/action | 验证 NRP 创建的专用终结点连接 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateEndpointConnections/read | 返回专用终结点连接列表，或获取指定专用终结点连接的属性 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateEndpointConnections/write | 批准或拒绝现有的专用终结点连接 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateEndpointConnections/delete | 删除现有的专用终结点连接 |
> | Microsoft.DBforPostgreSQL/serverGroupsv2/privateLinkResources/read | 获取相应 PostgreSQL SGv2 的专用链接资源 |
> | Microsoft.DBforPostgreSQL/servers/queryTexts/action | 返回查询的文本 |
> | Microsoft.DBforPostgreSQL/servers/resetQueryPerformanceInsightData/action | 重置 Query Performance Insight 数据 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.DBforPostgreSQL/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.DBforPostgreSQL/servers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | Microsoft.DBforPostgreSQL/servers/delete | 删除现有服务器。 |
> | Microsoft.DBforPostgreSQL/servers/restart/action | 重启特定的服务器。 |
> | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 更新指定服务器的配置 |
> | Microsoft.DBforPostgreSQL/servers/administrators/read | 获取 PostgreSQL 服务器管理员列表。 |
> | Microsoft.DBforPostgreSQL/servers/administrators/write | 使用指定参数创建或更新 PostgreSQL 服务器管理员。 |
> | Microsoft.DBforPostgreSQL/servers/administrators/delete | 删除 PostgreSQL 服务器的现有管理员。 |
> | Microsoft.DBforPostgreSQL/servers/advisors/read | 返回顾问列表 |
> | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 做出建议 |
> | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 返回建议的操作列表 |
> | Microsoft.DBforPostgreSQL/servers/configurations/read | 返回服务器的配置列表，或获取指定配置的属性。 |
> | Microsoft.DBforPostgreSQL/servers/configurations/write | 更新指定配置的值 |
> | Microsoft.DBforPostgreSQL/servers/databases/read | 返回 PostgreSQL 数据库列表，或获取指定数据库的属性。 |
> | Microsoft.DBforPostgreSQL/servers/databases/write | 使用指定的参数创建 PostgreSQL 数据库，或更新指定数据库的属性。 |
> | Microsoft.DBforPostgreSQL/servers/databases/delete | 删除现有的 PostgreSQL 数据库。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/read | 返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 使用指定参数创建防火墙规则，或更新现有规则。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 删除现有防火墙规则。 |
> | Microsoft.DBforPostgreSQL/servers/keys/read | 返回服务器密钥的列表，或获取指定服务器密钥的属性。 |
> | Microsoft.DBforPostgreSQL/servers/keys/write | 使用指定参数创建密钥，或更新指定服务器密钥的属性或标记。 |
> | Microsoft.DBforPostgreSQL/servers/keys/delete | 删除现有服务器密钥。 |
> | Microsoft.DBforPostgreSQL/servers/logFiles/read | 返回 PostgreSQL LogFiles 列表。 |
> | Microsoft.DBforPostgreSQL/servers/performanceTiers/read | 返回可用的性能层列表。 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | 从 NRP 端验证专用终结点连接创建调用 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | 返回专用终结点连接代理列表，或获取指定专用终结点连接代理的属性。 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | 使用指定参数创建专用终结点连接代理，或更新指定专用终结点连接代理的属性或标记。 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | 删除现有的专用终结点连接代理 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/read | 返回专用终结点连接列表，或获取指定专用终结点连接的属性。 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/delete | 删除现有的专用终结点连接 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/write | 批准或拒绝现有的专用终结点连接 |
> | Microsoft.DBforPostgreSQL/servers/privateLinkResources/read | 获取相应 PostgreSQL 服务器的专用链接资源 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | 获取 PostgreSQL 服务器的可用日志 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | Microsoft.DBforPostgreSQL/servers/queryTexts/read | 返回查询列表的文本 |
> | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 返回可恢复的 PostgreSQL 服务器信息 |
> | Microsoft.DBforPostgreSQL/servers/replicas/read | 获取 PostgreSQL 服务器的只读副本 |
> | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 检索在给定的服务器上配置的服务器威胁检测策略的详细信息 |
> | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 更改给定服务器的服务器威胁检测策略 |
> | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 返回热门查询的查询统计信息列表。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 删除现有虚拟网络规则 |
> | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | 返回实例的等待统计信息 |
> | Microsoft.DBforPostgreSQL/serversv2/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.DBforPostgreSQL/serversv2/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | Microsoft.DBforPostgreSQL/serversv2/delete | 删除现有服务器。 |
> | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | 更新指定服务器的配置 |
> | Microsoft.DBforPostgreSQL/serversv2/configurations/read | 返回服务器的配置列表，或获取指定配置的属性。 |
> | Microsoft.DBforPostgreSQL/serversv2/configurations/write | 更新指定配置的值 |
> | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | 返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。 |
> | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | 使用指定参数创建防火墙规则，或更新现有规则。 |
> | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | 删除现有防火墙规则。 |
> | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | 获取 PostgreSQL 服务器的可用日志 |
> | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | Microsoft.DBforPostgreSQL/singleservers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.DBforPostgreSQL/singleservers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | Microsoft.DBforPostgreSQL/singleservers/delete | 删除现有服务器。 |
> | Microsoft.DBforPostgreSQL/singleservers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DBforPostgreSQL/singleservers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DBforPostgreSQL/singleservers/providers/Microsoft.Insights/logDefinitions/read | 获取 PostgreSQL 服务器的可用日志 |
> | Microsoft.DBforPostgreSQL/singleservers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |

### <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

Azure 服务：[Azure Cosmos DB](../cosmos-db/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DocumentDB/register/action |  注册订阅的 Microsoft DocumentDB 资源提供程序 |
> | Microsoft.DocumentDB/cassandraClusters/read | 读取某个托管 Cassandra 群集或列出所有托管 Cassandra 群集 |
> | Microsoft.DocumentDB/cassandraClusters/write | 创建或更新托管 Cassandra 群集 |
> | Microsoft.DocumentDB/cassandraClusters/delete | 删除托管 Cassandra 群集 |
> | Microsoft.DocumentDB/cassandraClusters/repair/action | 请求修复托管的 Cassandra 密钥空间 |
> | Microsoft.DocumentDB/cassandraClusters/fetchNodeStatus/action | 以异步方式获取托管 Cassandra 群集中所有节点的节点状态 |
> | Microsoft.DocumentDB/cassandraClusters/dataCenters/read | 读取托管 Cassandra 群集中的数据中心，或列出托管 Cassandra 群集中的所有数据中心 |
> | Microsoft.DocumentDB/cassandraClusters/dataCenters/write | 在托管 Cassandra 群集中创建或更新数据中心 |
> | Microsoft.DocumentDB/cassandraClusters/dataCenters/delete | 删除托管 Cassandra 群集中的数据中心 |
> | Microsoft.DocumentDB/databaseAccountNames/read | 检查名称可用性。 |
> | Microsoft.DocumentDB/databaseAccounts/read | 读取数据库帐户。 |
> | Microsoft.DocumentDB/databaseAccounts/write | 更新数据库帐户。 |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/action | 列出数据库帐户的密钥 |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 读取数据库帐户只读密钥。 |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | 轮换数据库帐户的密钥 |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | 获取数据库帐户的连接字符串 |
> | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | 更改数据库帐户的资源组 |
> | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | 更改数据库帐户的区域故障转移优先级。 用于执行手动故障转移操作 |
> | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | 使数据库帐户的区域脱机。  |
> | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | 使数据库帐户的区域联机。 |
> | Microsoft.DocumentDB/databaseAccounts/delete | 删除数据库帐户。 |
> | Microsoft.DocumentDB/databaseAccounts/getBackupPolicy/action | 获取数据库帐户的备份策略 |
> | Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action | 管理数据库帐户的专用终结点连接 |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | 提交还原请求 |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | 提交配置备份的请求 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | （已弃用。 请使用不带“/apis/”段的资源路径）创建数据库。 仅适用于 API 类型：“sql”、“mongodb”、“gremlin”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取数据库或列出所有数据库。 仅适用于 API 类型：“sql”、“mongodb”、“gremlin”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | （已弃用。 请使用不带“/apis/”段的资源路径）删除数据库。 仅适用于 API 类型：“sql”、“mongodb”、“gremlin”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | （已弃用。 请使用不带“/apis/”段的资源路径）创建或更新集合。 仅适用于 API 类型：“mongodb”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取集合或列出所有集合。 仅适用于 API 类型：“mongodb”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | （已弃用。 请使用不带“/apis/”段的资源路径）删除集合。 仅适用于 API 类型：“mongodb”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“mongodb”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | （已弃用。 请使用不带“/apis/”段的资源路径）更新集合吞吐量。 仅适用于 API 类型：“mongodb”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取集合吞吐量。 仅适用于 API 类型：“mongodb”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“mongodb”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | （已弃用。 请使用不带“/apis/”段的资源路径）创建或更新容器。 仅适用于 API 类型：“sql”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取容器或列出所有容器。 仅适用于 API 类型：“sql”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | （已弃用。 请使用不带“/apis/”段的资源路径）删除容器。 仅适用于 API 类型：“sql”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“sql”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | （已弃用。 请使用不带“/apis/”段的资源路径）更新容器吞吐量。 仅适用于 API 类型：“sql”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取容器吞吐量。 仅适用于 API 类型：“sql”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“sql”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | （已弃用。 请使用不带“/apis/”段的资源路径）创建或更新图形。 仅适用于 API 类型：“gremlin”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取图形或列出所有图形。 仅适用于 API 类型：“gremlin”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | （已弃用。 请使用不带“/apis/”段的资源路径）删除图形。 仅适用于 API 类型：“gremlin”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“gremlin”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | （已弃用。 请使用不带“/apis/”段的资源路径）更新图形吞吐量。 仅适用于 API 类型：“gremlin”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取图形吞吐量。 仅适用于 API 类型：“gremlin”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“gremlin”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“sql”、“mongodb”、“gremlin”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | （已弃用。 请使用不带“/apis/”段的资源路径）更新数据库吞吐量。 仅适用于 API 类型：“sql”、“mongodb”、“gremlin”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取数据库吞吐量。 仅适用于 API 类型：“sql”、“mongodb”、“gremlin”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“sql”、“mongodb”、“gremlin”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | （已弃用。 请使用不带“/apis/”段的资源路径）创建密钥空间。 仅适用于 API 类型：“cassandra”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取密钥空间或列出所有密钥空间。 仅适用于 API 类型：“cassandra”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | （已弃用。 请使用不带“/apis/”段的资源路径）删除密钥空间。 仅适用于 API 类型：“cassandra”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“cassandra”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | （已弃用。 请使用不带“/apis/”段的资源路径）更新密钥空间吞吐量。 仅适用于 API 类型：“cassandra”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取密钥空间吞吐量。 仅适用于 API 类型：“cassandra”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“cassandra”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | （已弃用。 请使用不带“/apis/”段的资源路径）创建或更新表。 仅适用于 API 类型：“cassandra”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取表或列出所有表。 仅适用于 API 类型：“cassandra”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | （已弃用。 请使用不带“/apis/”段的资源路径）删除表。 仅适用于 API 类型：“cassandra”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“cassandra”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | （已弃用。 请使用不带“/apis/”段的资源路径）更新表吞吐量。 仅适用于 API 类型：“cassandra”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取表吞吐量。 仅适用于 API 类型：“cassandra”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“cassandra”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | （已弃用。 请使用不带“/apis/”段的资源路径）创建或更新表。 仅适用于 API 类型：“table”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取表或列出所有表。 仅适用于 API 类型：“table”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | （已弃用。 请使用不带“/apis/”段的资源路径）删除表。 仅适用于 API 类型：“table”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“table”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | （已弃用。 请使用不带“/apis/”段的资源路径）更新表吞吐量。 仅适用于 API 类型：“table”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取表吞吐量。 仅适用于 API 类型：“table”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | （已弃用。 请使用不带“/apis/”段的资源路径）读取异步操作的状态。 仅适用于 API 类型：“table”。 仅适用于设置类型：“throughput”。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/write | 创建assandra 密钥空间。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/read | 读取 Cassandra 密钥空间或列出所有 Cassandra 密钥空间 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/delete | 删除 Cassandra 密钥空间。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/write | 创建或更新 Cassandra 表。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/read | 读取 Cassandra 表或列出所有 Cassandra 表。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/delete | 删除 Cassandra 表。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/write | 更新assandra 表吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/read | 读取 Cassandra 表吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToAutoscale/action | 将 Cassandra 表产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToManualThroughput/action | 将 Cassandra 表产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/write | 更新assandra 密钥空间吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/read | 读取 Cassandra 密钥空间吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToAutoscale/action | 将 Cassandra 密钥空间产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToManualThroughput/action | 将 Cassandra 密钥空间产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/write | 创建或更新 Cassandra 视图。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/read | 读取 Cassandra 表或列出所有 Cassandra 视图。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/delete | 删除 Cassandra 视图。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/throughputSettings/write | 更新 Cassandra 视图吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/throughputSettings/read | 读取 Cassandra 视图吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/throughputSettings/migrateToAutoscale/action | 将 Cassandra 视图产品/服务迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/throughputSettings/migrateToManualThroughput/action | 将 Cassandra 视图产品/服务迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/views/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | 读取集合指标定义。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | 读取集合指标。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | 读取数据库帐户分区密钥级别指标 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | 读取集合中的数据库帐户分区 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | 读取数据库帐户分区级别指标 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | 读取数据库帐户分区级别使用情况 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | 读取集合使用情况。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | 读取数据库指标定义 |
> | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | 读取数据库指标。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | 读取数据库使用情况。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/write | 创建 Gremlin 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/read | 读取 Gremlin 数据库或列出所有 Gremlin 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/delete | 删除 Gremlin 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/write | 创建或更新 Gremlin 图形 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/read | 读取 Gremlin 图形列出所有 Gremlin 图形。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/delete | 删除 Gremlin 图形。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/write | 更新 Gremlin 图形吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/read | 读取 Gremlin 图形吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/migrateToAutoscale/action | 将 Gremlin 图形产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/migrateToManualThroughput/action | 将 Gremlin 图形产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/write | 更新 Gremlin 数据库吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/read | 读取 Gremlin 数据库吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToAutoscale/action | 将 Gremlin 数据库产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToManualThroughput/action | 将 Gremlin 数据库产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | 读取数据库帐户指标定义。 |
> | Microsoft.DocumentDB/databaseAccounts/metrics/read | 读取数据库帐户指标。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/write | 创建 MongoDB 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/read | 读取 MongoDB 数据库或列出所有 MongoDB 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/delete | 删除 MongoDB 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/write | 创建或更新 MongoDB 集合。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/read | 读取 MongoDB 集合或列出所有 MongoDB 集合。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/delete | 删除 MongoDB 集合。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/write | 更新 MongoDB 集合吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/read | 读取 MongoDB 集合吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/migrateToAutoscale/action | 将 MongoDB 集合产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/migrateToManualThroughput/action | 将 MongoDB 集合产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/write | 更新 MongoDB 数据库吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/read | 读取 MongoDB 数据库吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToAutoscale/action | 将 MongoDB 数据库产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToManualThroughput/action | 将 MongoDB 数据库产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/write | 创建或更新笔记本工作区 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/read | 读取笔记本工作区 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/delete | 删除笔记本工作区 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/listConnectionInfo/action | 列出笔记本工作区的连接信息 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/operationResults/read | 读取笔记本工作区中的异步操作状态 |
> | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 读取异步操作的状态 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/read | 读取复制延迟的百分位数 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 读取延迟指标 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 读取特定源和目标区域的延迟指标 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 读取特定目标区域的延迟指标 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | 读取数据库帐户的专用终结点连接代理 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | 创建或更新数据库帐户的专用终结点连接代理 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | 删除数据库帐户的专用终结点连接代理 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | 验证数据库帐户的专用终结点连接代理 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | 读取专用终结点连接代理异步操作的状态 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/read | 读取专用终结点连接，或列出数据库帐户的所有专用终结点连接 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/write | 创建或更新数据库帐户的专用终结点连接 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/delete | 删除数据库帐户的专用终结点连接 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/read | 读取 privateEndpointConnenctions 异步操作的状态 |
> | Microsoft.DocumentDB/databaseAccounts/privateLinkResources/read | 读取专用链接资源，或列出数据库帐户的所有专用链接资源 |
> | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | 获取数据库帐户的可用日志类别 |
> | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | 获取数据库帐户的可用指标 |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | 读取数据库帐户只读密钥。 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | 读取区域集合指标。 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | 读取区域数据库帐户分区密钥级别指标 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | 读取集合中的区域性数据库帐户分区 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | 读取区域数据库帐户分区级别指标 |
> | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | 读取区域和数据库帐户指标。 |
> | Microsoft.DocumentDB/databaseAccounts/services/read | 读取 CosmosDB 服务资源 |
> | Microsoft.DocumentDB/databaseAccounts/services/write | 写入 CosmosDB 服务资源 |
> | Microsoft.DocumentDB/databaseAccounts/services/delete | 删除 CosmosDB 服务资源 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/write | 创建 SQL 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/read | 读取 SQL 数据库或列出所有 SQL 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/delete | 删除 SQL 数据库。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/write | 创建或更新 SQL 容器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/read | 读取 SQL 容器或列出所有 SQL 容器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/delete | 删除 SQL 容器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/write | 创建或更新 SQL 存储过程。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/read | 读取 SQL 存储过程或列出所有 SQL 存储过程。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/delete | 删除 SQL 存储过程。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/write | 更新 SQL 容器吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/read | 读取 SQL 容器吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/migrateToAutoscale/action | 将 SQL 容器产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/migrateToManualThroughput/action | 将 SQL 数据库吞吐量产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/write | 创建或更新 SQL 触发器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/read | 读取 SQL 触发器或列出所有 SQL 触发器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/delete | 删除 SQL 触发器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/write | 创建或更新 SQL 用户定义的函数。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/read | 读取 SQL 用户定义的函数或列出所有 SQL 用户定义的函数。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/delete | 删除 SQL 用户定义的函数。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/write | 更新 SQL 数据库吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/read | 读取 SQL 数据库吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToAutoscale/action | 将 SQL 数据库产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToManualThroughput/action | 将 SQL 数据库吞吐量产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/read | 读取 SQL 角色分配 |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write | 创建或更新 SQL 角色分配 |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete | 删除 SQL 角色分配 |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/read | 读取 SQL 角色定义 |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write | 创建或更新 SQL 角色定义 |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete | 删除 SQL 角色定义 |
> | Microsoft.DocumentDB/databaseAccounts/tables/write | 创建或更新表。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/read | 读取某个表或列出所有表。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/delete | 删除表。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/write | 更新表吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/read | 读取表吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToAutoscale/action | 将表产品迁移到自动缩放。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToManualThroughput/action | 将表产品迁移到手动吞吐量。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToAutoscale/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToManualThroughput/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/operationResults/read | 读取异步操作的状态。 |
> | Microsoft.DocumentDB/databaseAccounts/usages/read | 读取数据库帐户使用情况。 |
> | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | 向 Microsoft.DocumentDB 通知正在删除虚拟网络或子网 |
> | Microsoft.DocumentDB/locations/read | 读取位置元数据或列出所有位置元数据 |
> | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | 读取 deleteVirtualNetworkOrSubnets 异步操作的状态 |
> | Microsoft.DocumentDB/locations/operationsStatus/read | 读取异步操作的状态 |
> | Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read | 读取可还原数据库帐户或列出所有可还原数据库帐户 |
> | Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action | 提交还原请求 |
> | Microsoft.DocumentDB/operationResults/read | 读取异步操作的状态 |
> | Microsoft.DocumentDB/operations/read | 读取适用于 Microsoft DocumentDB 的操作  |

### <a name="microsoftsql"></a>Microsoft.Sql

Azure 服务：[Azure SQL 数据库](../azure-sql/database/index.yml)、[Azure SQL 托管实例](../azure-sql/managed-instance/index.yml)、[Azure Synapse Analytics](../synapse-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Sql/checkNameAvailability/action | 验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。 |
> | Microsoft.Sql/register/action | 注册 Microsoft SQL 数据库资源提供程序的订阅，并启用 Microsoft SQL 数据库的创建。 |
> | Microsoft.Sql/unregister/action | 取消注册 Microsoft SQL 数据库资源提供程序的订阅，并启用 Microsoft SQL 数据库的创建。 |
> | Microsoft.Sql/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.Sql/instancePools/read | 获取实例池 |
> | Microsoft.Sql/instancePools/write | 创建或更新实例池 |
> | Microsoft.Sql/instancePools/delete | 删除实例池 |
> | Microsoft.Sql/instancePools/usages/read | 获取实例池的使用情况信息 |
> | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | 删除与虚拟网络或子网关联的虚拟网络规则 |
> | Microsoft.Sql/locations/read | 获取给定订阅的可用位置 |
> | Microsoft.Sql/locations/administratorAzureAsyncOperation/read | 获取托管实例 Azure 异步管理员操作结果。 |
> | Microsoft.Sql/locations/administratorOperationResults/read | 获取托管实例管理员操作结果。 |
> | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 检索扩展服务器 blob 审核策略集操作的结果 |
> | Microsoft.Sql/locations/auditingSettingsOperationResults/read | 检索服务器 Blob 审核策略集操作的结果 |
> | Microsoft.Sql/locations/capabilities/read | 获取给定位置中的此订阅的功能 |
> | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | 获取数据库操作的状态。 |
> | Microsoft.Sql/locations/databaseOperationResults/read | 获取数据库操作的状态。 |
> | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 获取正在对已删除服务器进行的操作 |
> | Microsoft.Sql/locations/deletedServerOperationResults/read | 获取正在对已删除服务器进行的操作 |
> | Microsoft.Sql/locations/deletedServers/read | 返回已删除服务器的列表，或获取指定的已删除服务器的属性。 |
> | Microsoft.Sql/locations/deletedServers/recover/action | 恢复已删除的服务器 |
> | Microsoft.Sql/locations/devOpsAuditingSettingsAzureAsyncOperation/read | 检索服务器 DevOps 审核策略集操作的结果 |
> | Microsoft.Sql/locations/devOpsAuditingSettingsOperationResults/read | 检索服务器 DevOps 审核策略集操作的结果 |
> | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | 获取弹性池异步操作的 Azure 异步操作 |
> | Microsoft.Sql/locations/elasticPoolOperationResults/read | 获取弹性池操作的结果。 |
> | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | 获取正在对透明数据加密保护器进行的操作 |
> | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | 获取正在对透明数据加密保护器进行的操作 |
> | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 检索扩展服务器 blob 审核策略集操作的结果 |
> | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 检索扩展服务器 blob 审核策略集操作的结果 |
> | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | 获取防火墙规则操作的状态。 |
> | Microsoft.Sql/locations/firewallRulesOperationResults/read | 获取防火墙规则操作的状态。 |
> | Microsoft.Sql/locations/hybridCertificateAzureAsyncOperation/read | 获取 Azure SQL 托管实例上长期混合证书异步操作的状态。 |
> | Microsoft.Sql/locations/hybridCertificateOperationResults/read | 获取长期混合证书异步操作的状态。 |
> | Microsoft.Sql/locations/hybridLinkAzureAsyncOperation/read | 获取 Azure SQL 托管实例上长期混合链接异步操作的状态。 |
> | Microsoft.Sql/locations/hybridLinkOperationResults/read | 获取长期混合链接异步操作的状态。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/read | 返回实例故障转移组的列表，或获取指定实例故障转移组的属性。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/write | 使用指定参数创建实例故障转移组，或更新指定实例故障转移组的属性或标记。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/delete | 删除现有的实例故障转移组。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 在现有的实例故障转移组中执行计划的故障转移。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 在现有的实例故障转移组中执行强制故障转移。 |
> | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | 获取实例池操作的状态 |
> | Microsoft.Sql/locations/instancePoolOperationResults/read | 获取实例池操作的结果 |
> | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | 获取作业代理操作的状态。 |
> | Microsoft.Sql/locations/jobAgentOperationResults/read | 获取作业代理操作的结果。 |
> | Microsoft.Sql/locations/ledgerDigestUploadsAzureAsyncOperation/read | 获取账本摘要上传设置的正在进行的操作 |
> | Microsoft.Sql/locations/ledgerDigestUploadsOperationResults/read | 获取账本摘要上传设置的正在进行的操作 |
> | Microsoft.Sql/locations/longTermRetentionBackupAzureAsyncOperation/read | 获取长期保留备份操作的状态 |
> | Microsoft.Sql/locations/longTermRetentionBackupOperationResults/read | 获取长期保留备份操作的状态 |
> | Microsoft.Sql/locations/longTermRetentionBackups/read | 列出某个位置的每台服务器上的每个数据库的长期保留备份 |
> | Microsoft.Sql/locations/longTermRetentionManagedInstanceBackupAzureAsyncOperation/read | 获取托管实例长期保留备份操作的状态 |
> | Microsoft.Sql/locations/longTermRetentionManagedInstanceBackupOperationResults/read | 获取托管实例长期保留备份操作的状态 |
> | Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read | 返回特定位置的托管实例 LTR 备份列表  |
> | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read | 返回托管实例数据库的 LTR 备份列表 |
> | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete | 删除托管实例数据库的 LTR 备份 |
> | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read | 返回特定托管实例的托管实例 LTR 备份列表 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 列出服务器上每个数据库的长期保留备份 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/copy/action | 复制长期保留备份 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/update/action | 更新长期保留备份 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 列出数据库的长期保留备份 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 删除长期保留备份 |
> | Microsoft.Sql/locations/managedDatabaseMoveAzureAsyncOperation/read | 获取托管实例数据库移动 Azure 异步操作。 |
> | Microsoft.Sql/locations/managedDatabaseMoveOperationResults/read | 获取托管实例数据库移动操作结果。 |
> | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 完成托管数据库还原操作 |
> | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | 获取正在对透明数据加密托管实例加密保护器进行的操作 |
> | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | 获取正在对透明数据加密托管实例加密保护器进行的操作 |
> | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | 获取正在对透明数据加密托管实例密钥进行的操作 |
> | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | 获取正在对透明数据加密托管实例密钥进行的操作 |
> | Microsoft.Sql/locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/read | 获取托管数据库的长期保留策略操作状态 |
> | Microsoft.Sql/locations/managedInstanceLongTermRetentionPolicyOperationResults/read | 获取托管数据库的长期保留策略操作状态 |
> | Microsoft.Sql/locations/managedInstancePrivateEndpointConnectionAzureAsyncOperation/read | 获取专用终结点连接操作的结果 |
> | Microsoft.Sql/locations/managedInstancePrivateEndpointConnectionOperationResults/read | 获取专用终结点连接操作的结果 |
> | Microsoft.Sql/locations/managedInstancePrivateEndpointConnectionProxyAzureAsyncOperation/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.Sql/locations/managedInstancePrivateEndpointConnectionProxyOperationResults/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.Sql/locations/managedShortTermRetentionPolicyOperationResults/read | 获取短期保留策略操作状态 |
> | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 获取正在对托管数据库透明数据加密进行的操作 |
> | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 获取正在对托管数据库透明数据加密进行的操作 |
> | Microsoft.Sql/locations/networkSecurityPerimeterAssociationProxyAzureAsyncOperation/read | 获取网络安全外围代理 azure 异步操作 |
> | Microsoft.Sql/locations/networkSecurityPerimeterAssociationProxyOperationResults/read | 获取网络安全外围操作结果 |
> | Microsoft.Sql/locations/operationsHealth/read | 获取某个位置中服务操作的运行状况 |
> | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | 获取专用终结点连接操作的结果 |
> | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | 获取专用终结点连接操作的结果 |
> | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | 获取专用终结点连接代理操作的结果 |
> | Microsoft.Sql/locations/serverAdministratorAzureAsyncOperation/read | 服务器 Azure Active Directory 管理员异步操作结果 |
> | Microsoft.Sql/locations/serverAdministratorOperationResults/read | 服务器 Azure Active Directory 管理员操作结果 |
> | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | 获取正在对透明数据加密服务器密钥进行的操作 |
> | Microsoft.Sql/locations/serverKeyOperationResults/read | 获取正在对透明数据加密服务器密钥进行的操作 |
> | Microsoft.Sql/locations/serverTrustGroupAzureAsyncOperation/read | 获取服务器信任组异步操作的状态 |
> | Microsoft.Sql/locations/serverTrustGroupOperationResults/read | 获取服务器信任组操作的结果 |
> | Microsoft.Sql/locations/serverTrustGroups/write | 使用指定的参数创建服务器信任组 |
> | Microsoft.Sql/locations/serverTrustGroups/delete | 删除现有的 SQL Server 信任组 |
> | Microsoft.Sql/locations/serverTrustGroups/read | 返回现有的 SQL Server 信任组 |
> | Microsoft.Sql/locations/shortTermRetentionPolicyOperationResults/read | 获取短期保留策略操作状态 |
> | Microsoft.Sql/locations/syncAgentOperationResults/read | 检索同步代理资源操作的结果 |
> | Microsoft.Sql/locations/syncDatabaseIds/read | 检索特定区域和订阅的同步数据库 ID |
> | Microsoft.Sql/locations/syncGroupOperationResults/read | 检索同步组资源操作的结果 |
> | Microsoft.Sql/locations/syncMemberOperationResults/read | 检索同步成员资源操作的结果 |
> | Microsoft.Sql/locations/timeZones/read | 按位置返回托管实例时区的列表。 |
> | Microsoft.Sql/locations/transparentDataEncryptionAzureAsyncOperation/read | 获取正在对逻辑数据库透明数据加密进行的操作 |
> | Microsoft.Sql/locations/transparentDataEncryptionOperationResults/read | 获取正在对逻辑数据库透明数据加密进行的操作 |
> | Microsoft.Sql/locations/usages/read | 获取此位置中的此订阅的使用指标的集合 |
> | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 返回指定虚拟网络规则 Azure 异步操作的详细信息  |
> | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 返回指定虚拟网络规则操作的详细信息  |
> | Microsoft.Sql/managedInstances/tdeCertificates/action | 创建/更新 TDE 证书 |
> | Microsoft.Sql/managedInstances/joinServerTrustGroup/action | 确定是否允许用户将托管服务器加入服务器信任组 |
> | Microsoft.Sql/managedInstances/hybridCertificate/action | 使用指定参数创建或更新混合证书。 |
> | Microsoft.Sql/managedInstances/read | 返回托管实例的列表，或获取指定托管实例的属性。 |
> | Microsoft.Sql/managedInstances/write | 使用指定参数创建托管实例，或更新指定托管实例的属性或标记。 |
> | Microsoft.Sql/managedInstances/delete | 删除现有托管实例。 |
> | Microsoft.Sql/managedInstances/failover/action | 客户发起了托管实例故障转移。 |
> | Microsoft.Sql/managedInstances/administrators/read | 获取托管实例管理员的列表。 |
> | Microsoft.Sql/managedInstances/administrators/write | 使用指定参数创建或更新托管实例管理员。 |
> | Microsoft.Sql/managedInstances/administrators/delete | 删除托管实例的现有管理员。 |
> | Microsoft.Sql/managedInstances/azureADOnlyAuthentications/read | 读取特定的托管服务器仅限 Azure Active Directory 的身份验证对象 |
> | Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write | 添加或更新特定的托管服务器仅限 Azure Active Directory 的身份验证对象 |
> | Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete | 删除特定的托管服务器仅限 Azure Active Directory 的身份验证对象 |
> | Microsoft.Sql/managedInstances/databases/cancelMove/action | 取消托管实例数据库移动。 |
> | Microsoft.Sql/managedInstances/databases/completeMove/action | 完成托管实例数据库移动。 |
> | Microsoft.Sql/managedInstances/databases/startMove/action | 启动托管实例数据库移动。 |
> | Microsoft.Sql/managedInstances/databases/read | 获取现有托管数据库 |
> | Microsoft.Sql/managedInstances/databases/delete | 删除现有托管数据库 |
> | Microsoft.Sql/managedInstances/databases/write | 创建新数据库或更新现有数据库。 |
> | Microsoft.Sql/managedInstances/databases/completeRestore/action | 完成托管数据库还原操作 |
> | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/write | 更新托管数据库的长期保留策略 |
> | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/read | 获取托管数据库的长期保留策略 |
> | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | 获取托管数据库的短期保留策略 |
> | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | 更新托管数据库的短期保留策略 |
> | Microsoft.Sql/managedInstances/databases/columns/read | 返回托管数据库的列列表 |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | 批量更新敏感度标签 |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | 获取托管实例数据库的可用日志 |
> | Microsoft.Sql/managedInstances/databases/queries/read | 按查询 ID 获取查询文本 |
> | Microsoft.Sql/managedInstances/databases/queries/statistics/read | 按查询 ID 获取查询执行统计信息 |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/write | 批量更新建议的敏感度标签 |
> | Microsoft.Sql/managedInstances/databases/restoreDetails/read | 当还原正在进行时返回托管数据库还原详细信息。 |
> | Microsoft.Sql/managedInstances/databases/schemas/read | 获取托管数据库架构。 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/read | 获取托管数据库表 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | 获取托管数据库列 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 获取给定列的敏感度标签 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 创建或更新给定列的敏感度标签 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 删除给定列的敏感度标签 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | 禁用给定列上的敏感度建议 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | 启用给定列上的敏感度建议 |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 更改给定托管数据库的数据库威胁检测策略 |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 检索为给定服务器配置的托管数据库威胁检测策略列表 |
> | Microsoft.Sql/managedInstances/databases/securityEvents/read | 检索托管数据库安全事件 |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 检索给定托管数据库上的数据库透明数据加密的详细信息 |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 更改给定托管数据库的数据库透明数据加密 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 更改给定数据库的漏洞评估 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 删除给定数据库的漏洞评估 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | 检索给定数据库中的漏洞评估策略 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 删除给定数据库的漏洞评估规则基线 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 更改给定数据库的漏洞评估规则基线 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 获取给定数据库的漏洞评估规则基线 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | 执行漏洞评估数据库扫描。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 将现有扫描结果转换为可读取格式。 如果已存在，则没有任何反应 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | 返回数据库漏洞评估扫描记录的列表，或获取指定扫描 ID 的扫描记录。 |
> | Microsoft.Sql/managedInstances/encryptionProtector/revalidate/action | 更新指定服务器加密保护程序的属性。 |
> | Microsoft.Sql/managedInstances/encryptionProtector/read | 返回服务器加密保护程序的列表，或获取指定服务器加密保护程序的属性。 |
> | Microsoft.Sql/managedInstances/encryptionProtector/write | 更新指定服务器加密保护程序的属性。 |
> | Microsoft.Sql/managedInstances/hybridLink/read | 返回混合链接列表，或获取指定分布式可用性组的属性。 |
> | Microsoft.Sql/managedInstances/hybridLink/write | 使用指定参数创建或更新混合链接。 |
> | Microsoft.Sql/managedInstances/hybridLink/delete | 删除包含指定分布式可用性组的混合链接。 |
> | Microsoft.Sql/managedInstances/inaccessibleManagedDatabases/read | 获取托管实例中无法访问的托管数据库列表 |
> | Microsoft.Sql/managedInstances/keys/read | 返回托管实例密钥的列表，或获取指定托管实例密钥的属性。 |
> | Microsoft.Sql/managedInstances/keys/write | 使用指定参数创建密钥，或更新指定托管实例密钥的属性或标记。 |
> | Microsoft.Sql/managedInstances/keys/delete | 删除现有 Azure SQL 托管实例密钥。 |
> | Microsoft.Sql/managedInstances/metricDefinitions/read | 获取托管实例指标定义 |
> | Microsoft.Sql/managedInstances/metrics/read | 获取托管实例指标 |
> | Microsoft.Sql/managedInstances/operations/read | 获取托管实例操作 |
> | Microsoft.Sql/managedInstances/operations/cancel/action | 取消尚未完成的 Azure SQL 托管实例挂起异步操作。 |
> | Microsoft.Sql/managedInstances/privateEndpointConnectionProxies/read | 返回专用终结点连接代理列表，或获取指定专用终结点连接代理的属性。 |
> | Microsoft.Sql/managedInstances/privateEndpointConnectionProxies/write | 使用指定参数创建专用终结点连接代理，或更新指定专用终结点连接代理的属性或标记。 |
> | Microsoft.Sql/managedInstances/privateEndpointConnectionProxies/delete | 删除现有的专用终结点连接代理 |
> | Microsoft.Sql/managedInstances/privateEndpointConnectionProxies/validate/action | 从 NRP 端验证专用终结点连接创建调用 |
> | Microsoft.Sql/managedInstances/privateEndpointConnections/read | 返回专用终结点连接列表，或获取指定专用终结点连接的属性。 |
> | Microsoft.Sql/managedInstances/privateEndpointConnections/delete | 删除现有的专用终结点连接 |
> | Microsoft.Sql/managedInstances/privateEndpointConnections/write | 批准或拒绝现有的专用终结点连接 |
> | Microsoft.Sql/managedInstances/privateLinkResources/read | 获取相应 SQL Server 的专用链接资源 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | 获取托管实例的可用日志 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于托管实例的指标类型 |
> | Microsoft.Sql/managedInstances/recoverableDatabases/read | 返回可恢复托管数据库的列表 |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 返回可还原的已删除托管数据库的列表。 |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | 获取已删除的托管数据库的短期保留策略 |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | 更新已删除的托管数据库的短期保留策略 |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 更改给定托管服务器的托管服务器威胁检测策略 |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 检索为给定服务器配置的托管服务器威胁检测策略列表 |
> | Microsoft.Sql/managedInstances/serverTrustGroups/read | 按托管实例名称返回现有的 SQL Server 信任组 |
> | Microsoft.Sql/managedInstances/topqueries/read | 获取托管实例的资源消耗排名靠前的查询 |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 更改给定托管实例的漏洞评估 |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 删除给定托管实例的漏洞评估 |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 检索给定托管实例中的漏洞评估策略 |
> | Microsoft.Sql/operations/read | 获取可用的 REST 操作 |
> | Microsoft.Sql/servers/tdeCertificates/action | 创建/更新 TDE 证书 |
> | Microsoft.Sql/servers/import/action | 导入新的 Azure SQL 数据库 |
> | Microsoft.Sql/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.Sql/servers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | Microsoft.Sql/servers/delete | 删除现有服务器。 |
> | Microsoft.Sql/servers/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | Microsoft.Sql/servers/administratorOperationResults/read | 获取正在对服务器管理员进行的操作 |
> | Microsoft.Sql/servers/administrators/read | 获取特定的 Azure Active Directory 管理员对象 |
> | Microsoft.Sql/servers/administrators/write | 添加或更新特定的 Azure Active Directory 管理员对象 |
> | Microsoft.Sql/servers/administrators/delete | 删除特定的 Azure Active Directory 管理员对象 |
> | Microsoft.Sql/servers/advisors/read | 返回可用于服务器的顾问列表 |
> | Microsoft.Sql/servers/advisors/write | 在服务器级别更新顾问的自动执行状态。 |
> | Microsoft.Sql/servers/advisors/recommendedActions/read | 返回服务器的指定顾问的建议操作列表 |
> | Microsoft.Sql/servers/advisors/recommendedActions/write | 对服务器应用建议的操作 |
> | Microsoft.Sql/servers/auditingSettings/read | 检索在给定服务器上配置的服务器 Blob 审核策略的详细信息 |
> | Microsoft.Sql/servers/auditingSettings/write | 更改给定服务器的服务器 Blob 审核 |
> | Microsoft.Sql/servers/auditingSettings/operationResults/read | 检索服务器 Blob 审核策略集操作的结果 |
> | Microsoft.Sql/servers/automaticTuning/read | 返回服务器的自动微调设置 |
> | Microsoft.Sql/servers/automaticTuning/write | 更新服务器的自动微调设置并返回更新的设置 |
> | Microsoft.Sql/servers/azureADOnlyAuthentications/read | 读取特定服务器仅限 Azure Active Directory 的身份验证对象 |
> | Microsoft.Sql/servers/azureADOnlyAuthentications/write | 添加或更新特定服务器仅限 Azure Active Directory 的身份验证对象 |
> | Microsoft.Sql/servers/azureADOnlyAuthentications/delete | 删除特定服务器仅限 Azure Active Directory 的身份验证对象 |
> | Microsoft.Sql/servers/communicationLinks/read | 返回指定服务器的通信链接列表。 |
> | Microsoft.Sql/servers/communicationLinks/write | 创建或更新服务器通信链接。 |
> | Microsoft.Sql/servers/communicationLinks/delete | 删除现有服务器通信链接。 |
> | Microsoft.Sql/servers/connectionPolicies/read | 返回指定服务器的服务器连接策略列表。 |
> | Microsoft.Sql/servers/connectionPolicies/write | 创建或更新服务器连接策略。 |
> | Microsoft.Sql/servers/databases/read | 返回数据库的列表，或获取指定数据库的属性。 |
> | Microsoft.Sql/servers/databases/write | 使用指定的参数创建数据库，或更新指定数据库的属性或标记。 |
> | Microsoft.Sql/servers/databases/delete | 删除现有数据库。 |
> | Microsoft.Sql/servers/databases/pause/action | 暂停 Azure SQL 数据仓库数据库 |
> | Microsoft.Sql/servers/databases/resume/action | 恢复 Azure SQL 数据仓库数据库 |
> | Microsoft.Sql/servers/databases/export/action | 导出 Azure SQL 数据库 |
> | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | 升级 Azure SQL 数据仓库数据库 |
> | Microsoft.Sql/servers/databases/move/action | 更改现有数据源的名称。 |
> | Microsoft.Sql/servers/databases/restorePoints/action | 创建新的还原点 |
> | Microsoft.Sql/servers/databases/import/action | 导入 Azure SQL 数据库 |
> | Microsoft.Sql/servers/databases/failover/action | 客户发起了数据库故障转移。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | 执行漏洞评估数据库扫描。 |
> | Microsoft.Sql/servers/databases/advisors/read | 返回可用于数据库的顾问列表 |
> | Microsoft.Sql/servers/databases/advisors/write | 在数据库级别更新顾问的自动执行状态。 |
> | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | 返回数据库的指定顾问的建议操作列表 |
> | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | 对数据库应用建议的操作 |
> | Microsoft.Sql/servers/databases/auditingSettings/read | 检索在给定数据库上配置的 Blob 审核策略的详细信息 |
> | Microsoft.Sql/servers/databases/auditingSettings/write | 更改给定数据库的 Blob 审核策略 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 检索数据库 Blob 审核记录 |
> | Microsoft.Sql/servers/databases/automaticTuning/read | 返回数据库的自动微调设置 |
> | Microsoft.Sql/servers/databases/automaticTuning/write | 更新数据库的自动微调设置并返回更新的设置 |
> | Microsoft.Sql/servers/databases/azureAsyncOperation/read | 获取数据库操作的状态。 |
> | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | 创建或更新数据库备份存档策略。 |
> | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 返回指定数据库的备份存档策略列表。 |
> | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | 获取数据库的短期保留策略 |
> | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | 更新数据库的短期保留策略 |
> | Microsoft.Sql/servers/databases/columns/read | 返回数据库的列列表 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | 批量更新敏感度标签 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | 返回数据库数据掩码策略的列表。 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 更改给定数据库的数据掩码策略 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 检索在给定数据库上配置的数据掩码策略规则的详细信息 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 更改给定数据库的数据掩码策略规则 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 删除给定数据库的数据掩码策略规则 |
> | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 返回所选查询 ID 的数据仓库分布查询信息 |
> | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 返回所选步骤 ID 的数据仓库查询的分布式查询步骤信息 |
> | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 检索包含正在运行和已暂停查询的 SQL 数据仓库实例的用户活动 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 检索在给定的数据库上配置的扩展 blob 审核策略的详细信息 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 更改给定数据库的扩展 blob 审核策略 |
> | Microsoft.Sql/servers/databases/extensions/write | 执行数据库扩展操作。 |
> | Microsoft.Sql/servers/databases/extensions/read | 获取数据库扩展操作。 |
> | Microsoft.Sql/servers/databases/extensions/importExtensionOperationResults/read | 获取正在进行的导入操作 |
> | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 检索给定数据库的异地备份策略 |
> | Microsoft.Sql/servers/databases/geoBackupPolicies/write | 创建或更新数据库异地备份策略 |
> | Microsoft.Sql/servers/databases/importExportAzureAsyncOperation/read | 获取正在进行的导入/导出操作 |
> | Microsoft.Sql/servers/databases/importExportOperationResults/read | 获取正在进行的导入/导出操作 |
> | Microsoft.Sql/servers/databases/ledgerDigestUploads/read | 读取账本摘要上传设置 |
> | Microsoft.Sql/servers/databases/ledgerDigestUploads/write | 启用上传账本摘要  |
> | Microsoft.Sql/servers/databases/ledgerDigestUploads/disable/action | 禁用上传账本摘要 |
> | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 获取所选数据库的可用维护窗口列表。 |
> | Microsoft.Sql/servers/databases/maintenanceWindows/read | 获取所选数据库的维护窗口设置。 |
> | Microsoft.Sql/servers/databases/maintenanceWindows/write | 设置所选数据库的维护窗口设置。 |
> | Microsoft.Sql/servers/databases/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | Microsoft.Sql/servers/databases/metrics/read | 返回数据库的指标 |
> | Microsoft.Sql/servers/databases/operationResults/read | 获取数据库操作的状态。 |
> | Microsoft.Sql/servers/databases/operations/cancel/action | 取消尚未完成的 Azure SQL 数据库挂起异步操作。 |
> | Microsoft.Sql/servers/databases/operations/read | 返回对数据库执行的操作列表 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | 获取数据库的可用日志 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | Microsoft.Sql/servers/databases/queryStore/read | 返回数据库的查询存储设置的当前值。 |
> | Microsoft.Sql/servers/databases/queryStore/write | 更新数据库的 Query Store 设置 |
> | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 返回与指定参数对应的查询文本的集合。 |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/write | 批量更新建议的敏感度标签 |
> | Microsoft.Sql/servers/databases/replicationLinks/read | 返回复制链接列表，或获取指定复制链接的属性。 |
> | Microsoft.Sql/servers/databases/replicationLinks/delete | 强制终止复制关系，这可能会丢失数据 |
> | Microsoft.Sql/servers/databases/replicationLinks/failover/action | 从主节点同步所有更改后执行故障转移，使此数据库成为复制关系的主节点，并使远程主节点成为辅助节点 |
> | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 立即故障转移（可能会丢失数据），使此数据库成为复制关系的主节点，并使远程主节点成为辅助节点 |
> | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | 将链接的复制模式更新为同步或异步模式 |
> | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | 强制终止或者在与合作伙伴同步后终止复制关系 |
> | Microsoft.Sql/servers/databases/restorePoints/read | 返回数据库的还原点。 |
> | Microsoft.Sql/servers/databases/restorePoints/delete | 删除数据库的还原点。 |
> | Microsoft.Sql/servers/databases/schemas/read | 获取数据库架构。 |
> | Microsoft.Sql/servers/databases/schemas/tables/read | 获取数据库表。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 获取数据库列。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | 启用给定列上的敏感度建议 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | 禁用给定列上的敏感度建议 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 获取给定列的敏感度标签 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 创建或更新给定列的敏感度标签 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 删除给定列的敏感度标签 |
> | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | 检索数据库上的索引建议列表 |
> | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | 应用索引建议 |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 更改给定数据库的数据库威胁检测策略 |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 检索为给定服务器配置的数据库威胁检测策略列表 |
> | Microsoft.Sql/servers/databases/securityMetrics/read | 获取数据库安全指标的集合 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | 返回有关根据查询执行统计信息扩展或缩减数据库的建议，以提高性能或降低成本 |
> | Microsoft.Sql/servers/databases/skus/read | 获取数据库可用的 SKU 的集合 |
> | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 刷新同步中心数据库架构 |
> | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 取消同步组同步 |
> | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 触发同步组同步 |
> | Microsoft.Sql/servers/databases/syncGroups/read | 返回同步组的列表，或获取指定同步组的属性。 |
> | Microsoft.Sql/servers/databases/syncGroups/write | 使用指定参数创建同步组，或更新指定同步组的属性。 |
> | Microsoft.Sql/servers/databases/syncGroups/delete | 删除现有同步组。 |
> | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 返回同步中心数据库架构的列表 |
> | Microsoft.Sql/servers/databases/syncGroups/logs/read | 返回同步组日志的列表 |
> | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 检索同步中心架构刷新操作的结果 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 返回同步成员的列表，或获取指定同步成员的属性。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 使用指定参数创建同步成员，或更新指定同步成员的属性。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 删除现有同步成员。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 刷新同步成员架构 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 检索同步成员架构刷新操作的结果 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 返回同步成员数据库架构的列表 |
> | Microsoft.Sql/servers/databases/topQueries/queryText/action | 返回所选查询 ID 的 Transact-SQL 文本 |
> | Microsoft.Sql/servers/databases/topQueries/read | 返回所选查询在所选时间段内的聚合运行时统计信息 |
> | Microsoft.Sql/servers/databases/topQueries/statistics/read | 返回所选查询在所选时间段内的聚合运行时统计信息 |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 检索给定托管数据库中的逻辑数据库透明数据加密详细信息 |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 更改给定逻辑数据库的数据库透明数据加密 |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | 获取正在对透明数据加密进行的操作 |
> | Microsoft.Sql/servers/databases/usages/read | 获取 Azure SQL 数据库使用情况信息 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 更改给定数据库的漏洞评估 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 删除给定数据库的漏洞评估 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | 检索给定数据库中的漏洞评估策略 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 删除给定数据库的漏洞评估规则基线 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 更改给定数据库的漏洞评估规则基线 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 获取给定数据库的漏洞评估规则基线 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | 执行漏洞评估数据库扫描。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | 返回数据库漏洞评估扫描记录的列表，或获取指定扫描 ID 的扫描记录。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 将现有扫描结果转换为可读取格式。 如果已存在，则没有任何反应 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | 检索数据库漏洞评估扫描执行操作的结果 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 检索在给定数据库上配置的漏洞评估的详细信息 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 更改给定数据库的漏洞评估 |
> | Microsoft.Sql/servers/databases/workloadGroups/read | 列出所选数据库的工作负荷组。 |
> | Microsoft.Sql/servers/databases/workloadGroups/write | 设置特定工作负荷组的属性。 |
> | Microsoft.Sql/servers/databases/workloadGroups/delete | 删除特定的工作负荷组。 |
> | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/read | 列出所选数据库的工作负荷分类器。 |
> | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/write | 设置特定工作负荷分类器的属性。 |
> | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/delete | 删除特定的工作负荷分类器。 |
> | Microsoft.Sql/servers/devOpsAuditingSettings/read | 检索在给定服务器上配置的服务器 DevOps 审核策略的详细信息 |
> | Microsoft.Sql/servers/devOpsAuditingSettings/write | 更改给定服务器的服务器 DevOps 审核策略 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | 获取包含此服务器的灾难恢复配置的集合 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | 更改服务器灾难恢复配置 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 删除给定服务器的现有灾难恢复配置 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | 故障转移 DisasterRecoveryConfiguration |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | 强制故障转移 DisasterRecoveryConfiguration |
> | Microsoft.Sql/servers/elasticPoolEstimates/read | 返回已为此服务器创建的弹性池估计列表 |
> | Microsoft.Sql/servers/elasticPoolEstimates/write | 为提供的数据库列表创建新的弹性池估计 |
> | Microsoft.Sql/servers/elasticPools/read | 检索给定服务器上弹性池的详细信息 |
> | Microsoft.Sql/servers/elasticPools/write | 创建新弹性池或更改现有弹性池的属性 |
> | Microsoft.Sql/servers/elasticPools/delete | 删除现有弹性池 |
> | Microsoft.Sql/servers/elasticPools/failover/action | 客户发起了弹性池故障转移。 |
> | Microsoft.Sql/servers/elasticPools/advisors/read | 返回可用于弹性池的顾问列表 |
> | Microsoft.Sql/servers/elasticPools/advisors/write | 在弹性池级别更新顾问的自动执行状态。 |
> | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | 返回弹性池的指定顾问的建议操作列表 |
> | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | 对弹性池应用建议的操作 |
> | Microsoft.Sql/servers/elasticPools/databases/read | 获取弹性池的数据库列表 |
> | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 检索给定弹性数据库池的活动和详细信息 |
> | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | 检索属于弹性数据库池的给定数据库的活动和详细信息 |
> | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | 返回可用于弹性数据库池的指标类型 |
> | Microsoft.Sql/servers/elasticPools/metrics/read | 返回弹性数据库池的指标 |
> | Microsoft.Sql/servers/elasticPools/operations/cancel/action | 取消尚未完成的 Azure SQL 弹性池挂起异步操作。 |
> | Microsoft.Sql/servers/elasticPools/operations/read | 返回对弹性池执行的操作列表 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于弹性数据库池的指标类型 |
> | Microsoft.Sql/servers/elasticPools/skus/read | 获取弹性池可用的 SKU 的集合 |
> | Microsoft.Sql/servers/encryptionProtector/revalidate/action | 更新指定服务器加密保护程序的属性。 |
> | Microsoft.Sql/servers/encryptionProtector/read | 返回服务器加密保护程序的列表，或获取指定服务器加密保护程序的属性。 |
> | Microsoft.Sql/servers/encryptionProtector/write | 更新指定服务器加密保护程序的属性。 |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | 检索在给定服务器上配置的扩展服务器 blob 审核策略的详细信息 |
> | Microsoft.Sql/servers/extendedAuditingSettings/write | 更改给定服务器的扩展服务器 blob 审核 |
> | Microsoft.Sql/servers/failoverGroups/read | 返回故障转移组的列表，或获取指定故障转移组的属性。 |
> | Microsoft.Sql/servers/failoverGroups/write | 使用指定参数创建故障转移组，或更新指定故障转移组的属性或标记。 |
> | Microsoft.Sql/servers/failoverGroups/delete | 删除现有故障转移组。 |
> | Microsoft.Sql/servers/failoverGroups/failover/action | 在现有故障转移组中执行计划的故障转移。 |
> | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 在现有故障转移组中执行强制故障转移。 |
> | Microsoft.Sql/servers/firewallRules/write | 使用指定参数创建服务器防火墙规则、更新指定规则的属性、或使用新的服务器防火墙规则覆盖所有现有规则。 |
> | Microsoft.Sql/servers/firewallRules/read | 返回服务器防火墙规则的列表，或获取指定服务器防火墙规则的属性。 |
> | Microsoft.Sql/servers/firewallRules/delete | 删除现有服务器防火墙规则。 |
> | Microsoft.Sql/servers/importExportOperationResults/read | 获取正在进行的导入/导出操作 |
> | Microsoft.Sql/servers/inaccessibleDatabases/read | 返回逻辑服务器中无法访问的数据库列表。 |
> | Microsoft.Sql/servers/jobAgents/read | 获取 Azure SQL DB 作业代理 |
> | Microsoft.Sql/servers/jobAgents/write | 创建或更新 Azure SQL DB 作业代理 |
> | Microsoft.Sql/servers/jobAgents/delete | 删除 Azure SQL DB 作业代理 |
> | Microsoft.Sql/servers/keys/read | 返回服务器密钥的列表，或获取指定服务器密钥的属性。 |
> | Microsoft.Sql/servers/keys/write | 使用指定参数创建密钥，或更新指定服务器密钥的属性或标记。 |
> | Microsoft.Sql/servers/keys/delete | 删除现有服务器密钥。 |
> | Microsoft.Sql/servers/networkSecurityPerimeterAssociationProxies/read | 获取网络安全外围关联 |
> | Microsoft.Sql/servers/networkSecurityPerimeterAssociationProxies/write | 创建网络安全外围关联 |
> | Microsoft.Sql/servers/networkSecurityPerimeterAssociationProxies/delete | 删除网络安全外围关联 |
> | Microsoft.Sql/servers/operationResults/read | 获取正在进行的服务器操作 |
> | Microsoft.Sql/servers/operations/read | 返回对服务器执行的操作列表 |
> | Microsoft.Sql/servers/outboundFirewallRules/read | 读取出站防火墙规则 |
> | Microsoft.Sql/servers/outboundFirewallRules/delete | 删除出站防火墙规则 |
> | Microsoft.Sql/servers/outboundFirewallRules/write | 创建出站防火墙规则 |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/updatePrivateEndpointProperties/action | 由 NRP 用于将属性回填到专用终结点连接 |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | 从 NRP 端验证专用终结点连接创建调用 |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | 返回专用终结点连接代理列表，或获取指定专用终结点连接代理的属性。 |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | 使用指定参数创建专用终结点连接代理，或更新指定专用终结点连接代理的属性或标记。 |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | 删除现有的专用终结点连接代理 |
> | Microsoft.Sql/servers/privateEndpointConnections/read | 返回专用终结点连接列表，或获取指定专用终结点连接的属性。 |
> | Microsoft.Sql/servers/privateEndpointConnections/delete | 删除现有的专用终结点连接 |
> | Microsoft.Sql/servers/privateEndpointConnections/write | 批准或拒绝现有的专用终结点连接 |
> | Microsoft.Sql/servers/privateLinkResources/read | 获取相应 SQL Server 的专用链接资源 |
> | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | 返回服务器可用的指标类型 |
> | Microsoft.Sql/servers/recommendedElasticPools/read | 检索针对弹性数据库池的建议，以便根据历史资源利用率降低成本或提高性能 |
> | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 检索给定服务器的建议弹性数据库池的指标 |
> | Microsoft.Sql/servers/recoverableDatabases/read | 此操作用于实时数据库的灾难恢复，以便将数据库还原到最后一个已知正常的备份点。 它返回有关最后一个正常备份的信息，但实际上不会还原数据库。 |
> | Microsoft.Sql/servers/replicationLinks/read | 返回复制链接列表，或获取指定复制链接的属性。 |
> | Microsoft.Sql/servers/restorableDroppedDatabases/read | 获取已在给定服务器中删除但仍包含在保留策略中的数据库列表。 |
> | Microsoft.Sql/servers/securityAlertPolicies/write | 更改给定服务器的服务器威胁检测策略 |
> | Microsoft.Sql/servers/securityAlertPolicies/read | 检索为给定服务器配置的服务器威胁检测策略列表 |
> | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | 检索服务器威胁检测策略写入操作的结果 |
> | Microsoft.Sql/servers/serviceObjectives/read | 检索可在给定服务器上实现的服务级别目标（也称为性能层）的列表 |
> | Microsoft.Sql/servers/syncAgents/read | 返回同步代理的列表，或获取指定同步代理的属性。 |
> | Microsoft.Sql/servers/syncAgents/write | 使用指定参数创建同步代理，或更新指定同步代理的属性。 |
> | Microsoft.Sql/servers/syncAgents/delete | 删除现有同步代理。 |
> | Microsoft.Sql/servers/syncAgents/generateKey/action | 生成同步代理注册密钥 |
> | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 返回同步代理链接数据库的列表 |
> | Microsoft.Sql/servers/usages/read | 返回服务器 DTU 配额，以及服务器中所有数据库当前消耗的 DTU |
> | Microsoft.Sql/servers/virtualNetworkRules/read | 返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。 |
> | Microsoft.Sql/servers/virtualNetworkRules/write | 使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。 |
> | Microsoft.Sql/servers/virtualNetworkRules/delete | 删除现有虚拟网络规则 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/write | 更改给定服务器的漏洞评估 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 删除给定服务器的漏洞评估 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/read | 检索给定服务器中的漏洞评估策略 |
> | Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action | 将 Azure SQL 虚拟群集上的 DNS 服务器配置与虚拟群集所在的 Azure 虚拟网络的配置进行同步。 |
> | Microsoft.Sql/virtualClusters/read | 返回虚拟群集的列表，或获取指定虚拟群集的属性。 |
> | Microsoft.Sql/virtualClusters/write | 更新虚拟群集标记。 |
> | Microsoft.Sql/virtualClusters/delete | 删除现有虚拟群集。 |

### <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

Azure 服务：[Azure 虚拟机中的 SQL Server](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.SqlVirtualMachine/register/action | 将订阅注册到 Microsoft.SqlVirtualMachine 资源提供程序 |
> | Microsoft.SqlVirtualMachine/unregister/action | 从 Microsoft.SqlVirtualMachine 资源提供程序取消注册订阅 |
> | Microsoft.SqlVirtualMachine/locations/registerSqlVmCandidate/action | 注册 SQL VM 候选项 |
> | Microsoft.SqlVirtualMachine/locations/availabilityGroupListenerOperationResults/read | 获取可用性组侦听器操作的结果 |
> | Microsoft.SqlVirtualMachine/locations/sqlVirtualMachineGroupOperationResults/read | 获取 SQL 虚拟机组操作的结果 |
> | Microsoft.SqlVirtualMachine/locations/sqlVirtualMachineOperationResults/read | 获取 SQL 虚拟机操作的结果 |
> | Microsoft.SqlVirtualMachine/operations/read |  |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/read | 检索 SQL 虚拟机组的详细信息 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/write | 创建新的或更改现有的 SQL 虚拟机组的属性 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/delete | 删除现有的 SQL 虚拟机组 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/read | 检索给定 SQL 虚拟机组上的 SQL 可用性组侦听器的详细信息 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/write | 创建新的或更改现有的 SQL 可用性组侦听器的属性 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/delete | 删除现有的可用性组侦听器 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/read | 按特定的 SQL 虚拟机组列出 SQL 虚拟机 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachines/redeploy/action | 重新部署现有的 SQL 虚拟机 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachines/read | 检索 SQL 虚拟机的详细信息 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachines/write | 创建新的或更改现有的 SQL 虚拟机的属性 |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachines/delete | 删除现有的 SQL 虚拟机 |

## <a name="analytics"></a>分析

### <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

Azure 服务：[Azure Analysis Services](../analysis-services/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.AnalysisServices/register/action | 注册 Analysis Services 资源提供程序。 |
> | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 检查给定的 Analysis Server 名称是否有效且未被使用。 |
> | Microsoft.AnalysisServices/locations/operationresults/read | 检索指定操作结果的信息。 |
> | Microsoft.AnalysisServices/locations/operationstatuses/read | 检索指定操作状态的信息。 |
> | Microsoft.AnalysisServices/operations/read | 检索操作的信息 |
> | Microsoft.AnalysisServices/servers/read | 检索指定 Analysis Server 的信息。 |
> | Microsoft.AnalysisServices/servers/write | 创建或更新指定的 Analysis Server。 |
> | Microsoft.AnalysisServices/servers/delete | 删除 Analysis Server。 |
> | Microsoft.AnalysisServices/servers/suspend/action | 暂停 Analysis Server。 |
> | Microsoft.AnalysisServices/servers/resume/action | 恢复 Analysis Server。 |
> | Microsoft.AnalysisServices/servers/listGatewayStatus/action | 列出与服务器关联的网关的状态。 |
> | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | 获取 Analysis Server 的诊断设置 |
> | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 Analysis Server 的诊断设置 |
> | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | 获取服务器的可用日志 |
> | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | 获取 Analysis Server 的可用指标 |
> | Microsoft.AnalysisServices/servers/skus/read | 检索服务器的可用 SKU 信息 |
> | Microsoft.AnalysisServices/skus/read | 检索 SKU 的信息 |

### <a name="microsoftdatabricks"></a>Microsoft.Databricks

Azure 服务：[Azure Databricks](/azure/databricks/)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Databricks/register/action | 注册到 Databricks。 |
> | Microsoft.Databricks/locations/getNetworkPolicies/action | 根据 NRP 使用的位置获取子网的网络意向策略 |
> | Microsoft.Databricks/locations/operationstatuses/read | 读取资源的操作状态。 |
> | Microsoft.Databricks/operations/read | 获取操作列表。 |
> | Microsoft.Databricks/workspaces/read | 检索 Databricks 工作区列表。 |
> | Microsoft.Databricks/workspaces/write | 创建 Databricks 工作区。 |
> | Microsoft.Databricks/workspaces/delete | 删除 Databricks 工作区。 |
> | Microsoft.Databricks/workspaces/refreshPermissions/action | 刷新工作区的权限 |
> | Microsoft.Databricks/workspaces/migratePrivateLinkWorkspaces/action | 基于“requiredNsgRules”和“enablePublicAccess”应用新的 NIP 模板 |
> | Microsoft.Databricks/workspaces/updateDenyAssignment/action | 更新工作区的托管资源组的拒绝分配无操作 |
> | Microsoft.Databricks/workspaces/refreshWorkspaces/action | 使用 URL 等新的详细信息刷新工作区 |
> | Microsoft.Databricks/workspaces/dbWorkspaces/write | 初始化 Databricks 工作区（仅限内部） |
> | Microsoft.Databricks/workspaces/outboundNetworkDependenciesEndpoints/read | 获取 Azure Databricks 工作区的出口终结点（所有出站依赖项的网络终结点）列表。 该操作返回每个出口终结点的属性 |
> | Microsoft.Databricks/workspaces/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.Databricks/workspaces/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Databricks/workspaces/privateEndpointConnectionProxies/write | 放置专用终结点连接代理 |
> | Microsoft.Databricks/workspaces/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Databricks/workspaces/privateEndpointConnections/read | 列出专用终结点连接 |
> | Microsoft.Databricks/workspaces/privateEndpointConnections/write | 审批专用终结点连接 |
> | Microsoft.Databricks/workspaces/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Databricks/workspaces/privateLinkResources/read | 列出专用链接资源 |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 设置 Databricks 工作区的可用诊断设置 |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | 获取 Databricks 工作区的可用日志定义 |
> | Microsoft.Databricks/workspaces/virtualNetworkPeerings/read | 获取虚拟网络对等互连。 |
> | Microsoft.Databricks/workspaces/virtualNetworkPeerings/write | 添加或修改虚拟网络对等互连 |
> | Microsoft.Databricks/workspaces/virtualNetworkPeerings/delete | 删除虚拟网络对等互连 |

### <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

Azure 服务：[Data Lake Analytics](../data-lake-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DataLakeAnalytics/register/action | 将订阅注册到 DataLakeAnalytics。 |
> | Microsoft.DataLakeAnalytics/accounts/read | 获取有关现有 DataLakeAnalytics 帐户的信息。 |
> | Microsoft.DataLakeAnalytics/accounts/write | 创建或更新 DataLakeAnalytics 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/delete | 删除 DataLakeAnalytics 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | 在 DataLakeAnalytics 帐户中传输 SystemMaxAnalyticsUnits。 |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 授予取消由其他用户提交的作业的权限。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | 获取有关计算策略的信息。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | 创建或更新计算策略。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | 删除计算策略。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | 获取有关 DataLakeAnalytics 帐户的链接 DataLakeStore 帐户的信息。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | 获取或更新 DataLakeAnalytics 帐户的链接 DataLakeStore 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | 从 DataLakeAnalytics 帐户取消链接 DataLakeStore 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/read | 获取有关 DataLakeAnalytics 帐户的链接 DataLakeStoreGen2 帐户的信息。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/write | 获取或更新 DataLakeAnalytics 帐户的链接 DataLakeStoreGen2 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/delete | 从 DataLakeAnalytics 帐户取消链接 DataLakeStoreGen2 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | 获取有关防火墙规则的信息。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | 创建或更新防火墙规则。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | 删除防火墙规则。 |
> | Microsoft.DataLakeAnalytics/accounts/operationResults/read | 获取 DataLakeAnalytics 帐户操作的结果。 |
> | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取 DataLakeAnalytics 帐户的诊断设置。 |
> | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 DataLakeAnalytics 帐户的诊断设置。 |
> | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | 获取 DataLakeAnalytics 帐户的可用日志。 |
> | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | 获取 DataLakeAnalytics 帐户的可用指标。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | 获取有关 DataLakeAnalytics 帐户的链接存储帐户的信息。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | 创建或更新 DataLakeAnalytics 帐户的链接存储帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | 从 DataLakeAnalytics 帐户取消链接存储帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | 获取 DataLakeAnalytics 帐户的链接存储帐户的容器。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | 列出 DataLakeAnalytics 帐户的链接存储帐户的存储容器的 SAS 令牌。 |
> | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | 获取有关虚拟网络规则的信息。 |
> | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | 创建或更新虚拟网络规则。 |
> | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | 删除虚拟网络规则。 |
> | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | 检查 DataLakeAnalytics 帐户名称的可用性。 |
> | Microsoft.DataLakeAnalytics/locations/capability/read | 获取有关使用 DataLakeAnalytics 的订阅的功能信息。 |
> | Microsoft.DataLakeAnalytics/locations/operationResults/read | 获取 DataLakeAnalytics 帐户操作的结果。 |
> | Microsoft.DataLakeAnalytics/locations/usages/read | 获取有关 DataLakeAnalytics 使用情况的订阅的配额使用信息。 |
> | Microsoft.DataLakeAnalytics/operations/read | 获取 DataLakeAnalytics 的可用操作。 |

### <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

Azure 服务：[Azure Data Lake Store](../storage/blobs/data-lake-storage-introduction.md)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DataLakeStore/register/action | 将订阅注册到 DataLakeStore。 |
> | Microsoft.DataLakeStore/accounts/read | 获取有关现有 DataLakeStore 帐户的信息。 |
> | Microsoft.DataLakeStore/accounts/write | 创建或更新 DataLakeStore 帐户。 |
> | Microsoft.DataLakeStore/accounts/delete | 删除 DataLakeStore 帐户。 |
> | Microsoft.DataLakeStore/accounts/enableKeyVault/action | 为 DataLakeStore 帐户启用 KeyVault。 |
> | Microsoft.DataLakeStore/accounts/Superuser/action | 使用 Microsoft.Authorization/roleAssignments/write 授权后，对 Data Lake Store 上的超级用户授权。 |
> | Microsoft.DataLakeStore/accounts/cosmosCertMappings/read | 获取有关 Cosmos 证书映射的信息。 |
> | Microsoft.DataLakeStore/accounts/cosmosCertMappings/write | 创建或更新 Cosmos 证书映射。 |
> | Microsoft.DataLakeStore/accounts/cosmosCertMappings/delete | 删除 Cosmos 证书映射。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/read | 获取 EventGrid 筛选器。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/write | 创建或更新 EventGrid 筛选器。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | 删除 EventGrid 筛选器。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/read | 获取有关防火墙规则的信息。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/write | 创建或更新防火墙规则。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/delete | 删除防火墙规则。 |
> | Microsoft.DataLakeStore/accounts/mountpoints/read | 获取有关装入点的信息。 |
> | Microsoft.DataLakeStore/accounts/operationResults/read | 获取 DataLakeStore 帐户操作的结果。 |
> | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取 DataLakeStore 帐户的诊断设置。 |
> | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 DataLakeStore 帐户的诊断设置。 |
> | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | 获取 DataLakeStore 帐户的可用日志。 |
> | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | 获取 DataLakeStore 帐户的可用指标。 |
> | Microsoft.DataLakeStore/accounts/shares/read | 获取有关共享的信息。 |
> | Microsoft.DataLakeStore/accounts/shares/write | 创建或更新共享。 |
> | Microsoft.DataLakeStore/accounts/shares/delete | 删除共享。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 获取有关受信任标识提供者的信息。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 创建或更新受信任的标识提供者。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 删除受信任的标识提供者。 |
> | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | 获取有关虚拟网络规则的信息。 |
> | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | 创建或更新虚拟网络规则。 |
> | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | 删除虚拟网络规则。 |
> | Microsoft.DataLakeStore/locations/checkNameAvailability/action | 检查 DataLakeStore 帐户名称的可用性。 |
> | Microsoft.DataLakeStore/locations/deleteVirtualNetworkOrSubnets/action | 删除跨 DataLakeStore 帐户的虚拟网络或子网。 |
> | Microsoft.DataLakeStore/locations/capability/read | 获取有关使用 DataLakeStore 的订阅的功能信息。 |
> | Microsoft.DataLakeStore/locations/operationResults/read | 获取 DataLakeStore 帐户操作的结果。 |
> | Microsoft.DataLakeStore/locations/usages/read | 获取有关 DataLakeStore 使用情况的订阅的配额使用信息。 |
> | Microsoft.DataLakeStore/operations/read | 获取 DataLakeStore 的可用操作。 |

### <a name="microsofteventhub"></a>Microsoft.EventHub

Azure 服务：[事件中心](../event-hubs/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.EventHub/checkNamespaceAvailability/action | 检查给定订阅下的命名空间可用性。 已弃用此 API，请改用 CheckNameAvailability。 |
> | Microsoft.EventHub/checkNameAvailability/action | 检查给定订阅下的命名空间可用性。 |
> | Microsoft.EventHub/register/action | 注册 EventHub 资源提供程序的订阅，并启用 EventHub 资源的创建 |
> | Microsoft.EventHub/unregister/action | 注册 EventHub 资源提供程序 |
> | Microsoft.EventHub/availableClusterRegions/read | 读取按 Azure 区域列出可用预配群集的操作。 |
> | Microsoft.EventHub/clusters/read | 获取群集资源说明 |
> | Microsoft.EventHub/clusters/write | 创建或修改现有的群集资源。 |
> | Microsoft.EventHub/clusters/delete | 删除现有的群集资源。 |
> | Microsoft.EventHub/clusters/namespaces/read | 列出群集中命名空间的命名空间 Azure 资源管理器 ID。 |
> | Microsoft.EventHub/clusters/operationresults/read | 获取异步群集操作的状态。 |
> | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | 获取群集指标资源说明列表 |
> | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 在事件中心资源提供程序中删除指定 VNet 的 VNet 规则 |
> | Microsoft.EventHub/namespaces/write | 创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。 |
> | Microsoft.EventHub/namespaces/read | 获取命名空间资源说明列表 |
> | Microsoft.EventHub/namespaces/Delete | 删除命名空间资源 |
> | Microsoft.EventHub/namespaces/authorizationRules/action | 更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | 删除 ACS 命名空间 |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionsApproval/action | 批准专用终结点连接 |
> | Microsoft.EventHub/namespaces/authorizationRules/read | 获取命名空间授权规则说明列表。 |
> | Microsoft.EventHub/namespaces/authorizationRules/write | 创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | Microsoft.EventHub/namespaces/authorizationRules/delete | 删除命名空间授权规则。 无法删除默认的命名空间授权规则。  |
> | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 获取命名空间的连接字符串 |
> | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 检查给定订阅下命名空间别名的可用性。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 创建或更新与命名空间关联的灾难恢复配置。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 获取与命名空间关联的灾难恢复配置。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | 禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | 调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | 获取灾难恢复主命名空间的授权规则 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 获取灾难恢复主命名空间的授权规则密钥 |
> | Microsoft.EventHub/namespaces/eventhubs/write | 创建或更新 EventHub 属性。 |
> | Microsoft.EventHub/namespaces/eventhubs/read | 获取 EventHub 资源说明列表 |
> | Microsoft.EventHub/namespaces/eventhubs/Delete | 用于删除 EventHub 资源的操作 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | 更新 EventHub 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  获取 EventHub 授权规则列表 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | 创建 EventHub 授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | 用于删除 EventHub 授权规则的操作 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | 获取 EventHub 的连接字符串 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | 创建或更新 ConsumerGroup 属性。 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | 获取 ConsumerGroup 资源说明列表 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | 用于删除 ConsumerGroup 资源的操作 |
> | Microsoft.EventHub/namespaces/ipFilterRules/read | 获取 IP 筛选器资源 |
> | Microsoft.EventHub/namespaces/ipFilterRules/write | 创建 IP 筛选器资源 |
> | Microsoft.EventHub/namespaces/ipFilterRules/delete | 删除 IP 筛选器资源 |
> | Microsoft.EventHub/namespaces/messagingPlan/read | 获取命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.EventHub/namespaces/messagingPlan/write | 更新命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.EventHub/namespaces/networkruleset/read | 获取 NetworkRuleSet 资源 |
> | Microsoft.EventHub/namespaces/networkruleset/write | 创建 VNET 规则资源 |
> | Microsoft.EventHub/namespaces/networkruleset/delete | 删除 VNET 规则资源 |
> | Microsoft.EventHub/namespaces/networkrulesets/read | 获取 NetworkRuleSet 资源 |
> | Microsoft.EventHub/namespaces/networkrulesets/write | 创建 VNET 规则资源 |
> | Microsoft.EventHub/namespaces/networkrulesets/delete | 删除 VNET 规则资源 |
> | Microsoft.EventHub/namespaces/operationresults/read | 获取命名空间操作的状态 |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/write | 创建专用终结点连接代理 |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/operationstatus/read | 获取异步专用终结点操作的状态 |
> | Microsoft.EventHub/namespaces/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.EventHub/namespaces/privateEndpointConnections/write | 创建或更新专用终结点连接 |
> | Microsoft.EventHub/namespaces/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.EventHub/namespaces/privateEndpointConnections/operationstatus/read | 获取异步专用终结点操作的状态 |
> | Microsoft.EventHub/namespaces/privateLinkResources/read | 获取支持专用终结点连接的资源类型 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取命名空间诊断设置资源说明列表 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 获取命名空间诊断设置资源说明列表 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 获取命名空间日志资源说明列表 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 获取命名空间指标资源说明列表 |
> | Microsoft.EventHub/namespaces/schemagroups/write | 创建或更新 SchemaGroup 属性。 |
> | Microsoft.EventHub/namespaces/schemagroups/read | 获取 SchemaGroup 资源说明列表 |
> | Microsoft.EventHub/namespaces/schemagroups/delete | 用于删除 SchemaGroup 资源的操作 |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/read | 获取 VNET 规则资源 |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/write | 创建 VNET 规则资源 |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | 删除 VNET 规则资源 |
> | Microsoft.EventHub/operations/read | 获取操作 |
> | Microsoft.EventHub/sku/read | 获取 SKU 资源说明列表 |
> | Microsoft.EventHub/sku/regions/read | 获取 SKU 区域资源说明列表 |
> | **DataAction** | **说明** |
> | Microsoft.EventHub/namespaces/messages/send/action | 发送消息 |
> | Microsoft.EventHub/namespaces/messages/receive/action | 接收消息 |
> | Microsoft.EventHub/namespaces/schemas/read | 检索架构 |
> | Microsoft.EventHub/namespaces/schemas/write | 写入架构 |
> | Microsoft.EventHub/namespaces/schemas/delete | 删除架构 |

### <a name="microsofthdinsight"></a>Microsoft.HDInsight

Azure 服务：[HDInsight](../hdinsight/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.HDInsight/register/action | 注册订阅的 HDInsight 资源提供程序 |
> | Microsoft.HDInsight/unregister/action | 取消注册订阅的 HDInsight 资源提供程序 |
> | Microsoft.HDInsight/clusters/write | 创建或更新 HDInsight 群集 |
> | Microsoft.HDInsight/clusters/read | 获取有关 HDInsight 群集的详细信息 |
> | Microsoft.HDInsight/clusters/delete | 删除 HDInsight 群集 |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | 获取 HDInsight 群集的网关设置 |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | 更新 HDInsight 群集的网关设置 |
> | Microsoft.HDInsight/clusters/configurations/action | 获取 HDInsight 群集配置 |
> | Microsoft.HDInsight/clusters/executeScriptActions/action | 对 HDInsight 群集执行脚本操作 |
> | Microsoft.HDInsight/clusters/resolvePrivateLinkServiceId/action | 解析 HDInsight 群集的专用链接服务 ID |
> | Microsoft.HDInsight/clusters/privateEndpointConnectionsApproval/action | 自动批准 HDInsight 群集的专用终结点连接 |
> | Microsoft.HDInsight/clusters/applications/read | 获取 HDInsight 群集的应用程序 |
> | Microsoft.HDInsight/clusters/applications/write | 创建或更新 HDInsight 群集的应用程序 |
> | Microsoft.HDInsight/clusters/applications/delete | 删除 HDInsight 群集的应用程序 |
> | Microsoft.HDInsight/clusters/configurations/read | 获取 HDInsight 群集配置 |
> | Microsoft.HDInsight/clusters/executeScriptActions/azureasyncoperations/read | 获取 HDInsight 群集的脚本操作状态 |
> | Microsoft.HDInsight/clusters/executeScriptActions/operationresults/read | 获取 HDInsight 群集的脚本操作状态 |
> | Microsoft.HDInsight/clusters/extensions/write | 创建 HDInsight 群集的群集扩展 |
> | Microsoft.HDInsight/clusters/extensions/read | 获取 HDInsight 群集的群集扩展 |
> | Microsoft.HDInsight/clusters/extensions/delete | 删除 HDInsight 群集的群集扩展 |
> | Microsoft.HDInsight/clusters/privateEndpointConnections/read | 获取 HDInsight 群集的专用终结点连接 |
> | Microsoft.HDInsight/clusters/privateEndpointConnections/write | 更新 HDInsight 群集的专用终结点连接 |
> | Microsoft.HDInsight/clusters/privateEndpointConnections/delete | 删除 HDInsight 群集的专用终结点连接 |
> | Microsoft.HDInsight/clusters/privateLinkResources/read | 获取 HDInsight 群集的专用链接资源 |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源 HDInsight 群集的诊断设置 |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源 HDInsight 群集的诊断设置 |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | 获取 HDInsight 群集的可用指标 |
> | Microsoft.HDInsight/clusters/roles/resize/action | 调整 HDInsight 群集的大小 |
> | Microsoft.HDInsight/clusters/scriptActions/read | 获取 HDInsight 群集的持久性脚本操作 |
> | Microsoft.HDInsight/clusters/scriptActions/delete | 删除 HDInsight 群集的持久性脚本操作 |
> | Microsoft.HDInsight/clusters/scriptExecutionHistory/read | 获取 HDInsight 群集的脚本操作历史记录 |
> | Microsoft.HDInsight/clusters/scriptExecutionHistory/promote/action | 提升 HDInsight 群集的脚本操作 |
> | Microsoft.HDInsight/locations/capabilities/read | 获取订阅功能 |
> | Microsoft.HDInsight/locations/checkNameAvailability/read | 检查名称可用性 |

### <a name="microsoftkusto"></a>Microsoft.Kusto

Azure 服务：[Azure 数据资源管理器](/azure/data-explorer/)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Kusto/register/action | 订阅注册操作 |
> | Microsoft.Kusto/Register/action | 将订阅注册到 Kusto 资源提供程序。 |
> | Microsoft.Kusto/Unregister/action | 将订阅取消注册到 Kusto 资源提供程序。 |
> | Microsoft.Kusto/Clusters/read | 读取群集资源。 |
> | Microsoft.Kusto/Clusters/write | 写入群集资源。 |
> | Microsoft.Kusto/Clusters/delete | 删除群集资源。 |
> | Microsoft.Kusto/Clusters/Start/action | 启动群集。 |
> | Microsoft.Kusto/Clusters/Stop/action | 停止群集。 |
> | Microsoft.Kusto/Clusters/Activate/action | 启动群集。 |
> | Microsoft.Kusto/Clusters/Deactivate/action | 停止群集。 |
> | Microsoft.Kusto/Clusters/CheckNameAvailability/action | 检查群集名称可用性。 |
> | Microsoft.Kusto/Clusters/DetachFollowerDatabases/action | 分离关注者的数据库。 |
> | Microsoft.Kusto/Clusters/ListFollowerDatabases/action | 列出关注者的数据库。 |
> | Microsoft.Kusto/Clusters/DiagnoseVirtualNetwork/action | 诊断服务所依赖的外部资源的网络连接状态。 |
> | Microsoft.Kusto/Clusters/ListLanguageExtensions/action | 列出语言扩展。 |
> | Microsoft.Kusto/Clusters/AddLanguageExtensions/action | 添加语言扩展。 |
> | Microsoft.Kusto/Clusters/RemoveLanguageExtensions/action | 删除语言扩展。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | 读取附加的数据库配置资源。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | 写入附加的数据库配置资源。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | 删除附加的数据库配置资源。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | 写入脚本资源。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | 删除脚本资源。 |
> | Microsoft.Kusto/Clusters/Databases/read | 读取数据库资源。 |
> | Microsoft.Kusto/Clusters/Databases/write | 写入数据库资源。 |
> | Microsoft.Kusto/Clusters/Databases/delete | 删除数据库资源。 |
> | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | 列出数据库主体。 |
> | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | 添加数据库主体。 |
> | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | 删除数据库主体。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | 验证数据库数据连接。 |
> | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | 检查给定类型的名称可用性。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | 验证数据库事件中心连接。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/read | 读取数据连接资源。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/write | 写入数据连接资源。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | 删除数据连接资源。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | 读取事件中心连接资源。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | 写入事件中心连接资源。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | 删除事件中心连接资源。 |
> | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/read | 读取数据库主体分配资源。 |
> | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/write | 写入数据库主体分配资源。 |
> | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/delete | 删除数据库主体分配资源。 |
> | Microsoft.Kusto/Clusters/Databases/Scripts/read | 读取脚本资源。 |
> | Microsoft.Kusto/Clusters/DataConnections/read | 读取群集的数据连接资源。 |
> | Microsoft.Kusto/Clusters/DataConnections/write | 写入群集的数据连接资源。 |
> | Microsoft.Kusto/Clusters/DataConnections/delete | 删除群集的数据连接资源。 |
> | Microsoft.Kusto/Clusters/ManagedPrivateEndpoints/read | 读取附加的数据库配置资源。 |
> | Microsoft.Kusto/Clusters/ManagedPrivateEndpoints/write |  |
> | Microsoft.Kusto/Clusters/ManagedPrivateEndpoints/delete |  |
> | Microsoft.Kusto/Clusters/OutboundNetworkDependenciesEndpoints/read | 读取资源的出站网络依赖项终结点 |
> | Microsoft.Kusto/Clusters/PrincipalAssignments/read | 读取 Cluster 主体分配资源。 |
> | Microsoft.Kusto/Clusters/PrincipalAssignments/write | 写入 Cluster 主体分配资源。 |
> | Microsoft.Kusto/Clusters/PrincipalAssignments/delete | 删除 Cluster 主体分配资源。 |
> | Microsoft.Kusto/Clusters/PrivateEndpointConnectionProxies/read | 读取专用终结点连接代理 |
> | Microsoft.Kusto/Clusters/PrivateEndpointConnectionProxies/write | 写入专用终结点连接代理 |
> | Microsoft.Kusto/Clusters/PrivateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Kusto/Clusters/PrivateEndpointConnections/read | 读取专用终结点连接 |
> | Microsoft.Kusto/Clusters/PrivateEndpointConnections/write | 写入专用终结点连接 |
> | Microsoft.Kusto/Clusters/PrivateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Kusto/Clusters/PrivateLinkResources/read | 读取专用链接资源 |
> | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/logDefinitions/read | 获取资源的诊断日志设置 |
> | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/metricDefinitions/read | 获取资源的指标定义 |
> | Microsoft.Kusto/Clusters/SKUs/read | 读取群集 SKU 资源。 |
> | Microsoft.Kusto/Clusters/SKUs/PrivateEndpointConnectionProxyValidation/action | 验证专用终结点连接代理 |
> | Microsoft.Kusto/Deployments/Preflight/action | 运行预检操作 |
> | Microsoft.Kusto/Locations/CheckNameAvailability/action | 检查资源名称可用性。 |
> | Microsoft.Kusto/Locations/GetNetworkPolicies/action | 获取网络意向策略 |
> | Microsoft.Kusto/locations/operationresults/read | 读取操作资源 |
> | Microsoft.Kusto/Operations/read | 读取操作资源 |
> | Microsoft.Kusto/SKUs/read | 读取 SKU 资源。 |

### <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

Azure 服务：[Power BI Embedded](/azure/power-bi-embedded/)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.PowerBIDedicated/register/action | 注册 Power BI 专用资源提供程序。 |
> | Microsoft.PowerBIDedicated/register/action | 注册 Power BI 专用资源提供程序。 |
> | Microsoft.PowerBIDedicated/autoScaleVCores/read | 检索指定的 Power BI 自动缩放 V-Core 的信息。 |
> | Microsoft.PowerBIDedicated/autoScaleVCores/write | 创建或更新指定的 Power BI 自动缩放 V-Core。 |
> | Microsoft.PowerBIDedicated/autoScaleVCores/delete | 删除 Power BI 自动缩放 V-Core。 |
> | Microsoft.PowerBIDedicated/capacities/read | 检索指定的 Power BI 容量的信息。 |
> | Microsoft.PowerBIDedicated/capacities/write | 创建或更新指定的 Power BI 容量。 |
> | Microsoft.PowerBIDedicated/capacities/delete | 删除 Power BI 容量。 |
> | Microsoft.PowerBIDedicated/capacities/suspend/action | 暂停容量。 |
> | Microsoft.PowerBIDedicated/capacities/resume/action | 恢复容量。 |
> | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | 获取 Power BI 专用容量的可用日志。 |
> | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | 获取 Power BI 容量的可用指标。 |
> | Microsoft.PowerBIDedicated/capacities/skus/read | 检索容量的可用 SKU 信息 |
> | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | 检查给定的 Power BI 专用资源名称是否有效且未被使用。 |
> | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | 检查给定的 Power BI 专用资源名称是否有效且未被使用。 |
> | Microsoft.PowerBIDedicated/locations/operationresults/read | 检索指定操作结果的信息。 |
> | Microsoft.PowerBIDedicated/locations/operationresults/read | 检索指定操作结果的信息。 |
> | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 检索指定操作状态的信息。 |
> | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 检索指定操作状态的信息。 |
> | Microsoft.PowerBIDedicated/operations/read | 检索操作的信息 |
> | Microsoft.PowerBIDedicated/operations/read | 检索操作的信息 |
> | Microsoft.PowerBIDedicated/servers/read | 检索指定 Power BI 专用服务器的信息。 |
> | Microsoft.PowerBIDedicated/servers/write | 创建或更新指定的 Power BI 专用服务器 |
> | Microsoft.PowerBIDedicated/servers/delete | 删除 Power BI 专用服务器 |
> | Microsoft.PowerBIDedicated/servers/suspend/action | 暂停服务器。 |
> | Microsoft.PowerBIDedicated/servers/resume/action | 恢复服务器。 |
> | Microsoft.PowerBIDedicated/servers/skus/read | 检索服务器的可用 SKU 信息。 |
> | Microsoft.PowerBIDedicated/skus/read | 检索 SKU 的信息 |
> | Microsoft.PowerBIDedicated/skus/read | 检索 SKU 的信息 |

### <a name="microsoftpurview"></a>Microsoft.Purview

Azure 服务：[Azure Purview](../purview/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Purview/register/action | 为 Microsoft Purview 提供商注册订阅。 |
> | Microsoft.Purview/unregister/action | 为 Microsoft Purview 提供商注销订阅。 |
> | Microsoft.Purview/setDefaultAccount/action | 设置该范围的默认帐户。 |
> | Microsoft.Purview/accounts/read | 读取 Microsoft Purview 提供商的帐户资源。 |
> | Microsoft.Purview/accounts/write | 写入 Microsoft Purview 提供商的帐户资源。 |
> | Microsoft.Purview/accounts/delete | 删除 Microsoft Purview 提供商的帐户资源。 |
> | Microsoft.Purview/accounts/move/action | 移动 Microsoft Purview 提供商的帐户资源。 |
> | Microsoft.Purview/accounts/PrivateEndpointConnectionsApproval/action | 批准专用终结点连接。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/read | 读取帐户专用终结点连接代理。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/write | 写入帐户专用终结点连接代理。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/delete | 删除帐户专用终结点连接代理。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/validate/action | 验证帐户专用终结点连接代理。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/operationResults/read | 监视专用终结点连接代理异步操作。 |
> | Microsoft.Purview/accounts/privateEndpointConnections/read | 读取专用终结点连接。 |
> | Microsoft.Purview/accounts/privateEndpointConnections/write | 创建或更新专用终结点连接。 |
> | Microsoft.Purview/accounts/privateEndpointConnections/delete | 删除专用终结点连接。 |
> | Microsoft.Purview/accounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.Purview/accounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.Purview/accounts/providers/Microsoft.Insights/logDefinitions/read | 获取目录的可用日志。 |
> | Microsoft.Purview/accounts/providers/Microsoft.Insights/metricDefinitions/read | 获取目录的可用指标。 |
> | Microsoft.Purview/getDefaultAccount/read | 获取该范围的默认帐户。 |
> | Microsoft.Purview/locations/operationResults/read | 监视异步操作。 |
> | Microsoft.Purview/operations/read | 读取 Microsoft Purview 提供商的所有可用操作。 |
> | **DataAction** | **说明** |
> | Microsoft.Purview/accounts/data/read | 读取数据对象。 |
> | Microsoft.Purview/accounts/data/write | 创建、更新和删除数据对象。 |
> | Microsoft.Purview/accounts/scan/read | 读取数据源和扫描。 |
> | Microsoft.Purview/accounts/scan/write | 创建、更新和删除数据源，并且管理扫描。 |

### <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

Azure 服务：[流分析](../stream-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.StreamAnalytics/Register/action | 使用流分析资源提供程序注册订阅 |
> | Microsoft.StreamAnalytics/locations/TestQuery/action | 测试流分析资源提供程序的查询 |
> | Microsoft.StreamAnalytics/locations/operationresults/Read | 读取流分析操作结果 |
> | Microsoft.StreamAnalytics/locations/quotas/Read | 读取流分析订阅配额 |
> | Microsoft.StreamAnalytics/operations/Read | 读取流分析操作 |
> | Microsoft.StreamAnalytics/streamingjobs/Delete | 删除流分析作业 |
> | Microsoft.StreamAnalytics/streamingjobs/PublishEdgePackage/action | 为流分析作业发布边缘包 |
> | Microsoft.StreamAnalytics/streamingjobs/Read | 读取流分析作业 |
> | Microsoft.StreamAnalytics/streamingjobs/Scale/action | 缩放流分析作业 |
> | Microsoft.StreamAnalytics/streamingjobs/Start/action | 启动流分析作业 |
> | Microsoft.StreamAnalytics/streamingjobs/Stop/action | 停止流分析作业 |
> | Microsoft.StreamAnalytics/streamingjobs/Write | 写入流分析作业 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | 删除流分析作业函数 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Read | 读取流分析作业函数 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | 检索流分析作业函数的默认定义 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | 测试流分析作业函数 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Write | 写入流分析作业函数 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | 读取流分析作业函数的操作结果 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | 删除流分析作业输入 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | 读取流分析作业输入 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | 流分析作业输入采样 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | 测试流分析作业输入 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | 写入流分析作业输入 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | 读取流分析作业输入的操作结果 |
> | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | 读取指标定义 |
> | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | 读取流分析作业的操作结果 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | 删除流分析作业输出 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | 读取流分析作业输出 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | 测试流分析作业输出 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | 写入流分析作业输出 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | 读取流分析作业输出的操作结果 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 读取诊断设置。 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 写入诊断设置。 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | 获取 streamingjobs 的可用日志 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | 获取 streamingjobs 的可用指标 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | 删除流分析作业转换 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | 读取流分析作业转换 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | 写入流分析作业转换 |

### <a name="microsoftsynapse"></a>Microsoft.Synapse

Azure 服务：[Azure Synapse Analytics](../synapse-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Synapse/checkNameAvailability/action | 检查工作区名称可用性。 |
> | Microsoft.Synapse/register/action | 注册 Azure Synapse Analytics（工作区）资源提供程序，并启用工作区创建。 |
> | Microsoft.Synapse/unregister/action | 注销 Azure Synapse Analytics（工作区）资源提供程序，并禁用工作区创建。 |
> | Microsoft.Synapse/Deployments/Preflight/action | 运行预检操作 |
> | Microsoft.Synapse/Locations/KustoPoolCheckNameAvailability/action | 检查资源名称可用性。 |
> | Microsoft.Synapse/locations/kustoPoolOperationResults/read | 读取操作资源 |
> | Microsoft.Synapse/locations/operationResults/read | 读取任何异步操作结果。 |
> | Microsoft.Synapse/locations/operationStatuses/read | 读取任何异步操作状态。 |
> | Microsoft.Synapse/operations/read | 从 Azure Synapse Analytics 资源提供程序读取可用操作。 |
> | Microsoft.Synapse/privateEndpoints/notify/action | 通知将要移动专用终结点 |
> | Microsoft.Synapse/privateLinkHubs/write | 创建任何 PrivateLinkHub。 |
> | Microsoft.Synapse/privateLinkHubs/read | 读取任何 PrivateLinkHub。 |
> | Microsoft.Synapse/privateLinkHubs/delete | 删除 PrivateLinkHub。 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnectionsApproval/action | 确定是否允许用户自动批准与 privateLinkHub 的专用终结点连接 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnectionProxies/validate/action | 验证 PrivateLinkHub 代理的专用终结点连接 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnectionProxies/write | 创建或更新 PrivateLinkHub 代理的专用终结点连接 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnectionProxies/read | 读取任何专用终结点连接代理 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnectionProxies/delete | 删除 PrivateLinkHub 代理的专用终结点连接 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnectionProxies/updatePrivateEndpointProperties/action | 更新专用链接中心的专用终结点连接代理属性 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnections/write | 为 PrivateLinkHub 创建或更新专用终结点连接 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnections/read | 读取 PrivateLinkHub 的任何专用终结点连接 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnections/delete | 删除 PrivateLinkHub 的专用终结点连接 |
> | Microsoft.Synapse/privateLinkHubs/privateLinkResources/read | 获取专用链接资源的列表 |
> | Microsoft.Synapse/resourceGroups/operationStatuses/read | 读取任何异步操作状态。 |
> | Microsoft.Synapse/SKUs/read | 读取 SKU 资源。 |
> | Microsoft.Synapse/userAssignedIdentities/notify/action | 通知将要删除用户分配的标识 |
> | Microsoft.Synapse/workspaces/replaceAllIpFirewallRules/action | 替换工作区的所有 IP 防火墙规则。 |
> | Microsoft.Synapse/workspaces/write | 创建或更新任何工作区。 |
> | Microsoft.Synapse/workspaces/read | 读取任何工作区。 |
> | Microsoft.Synapse/workspaces/delete | 删除任何工作区。 |
> | Microsoft.Synapse/workspaces/checkDefaultStorageAccountStatus/action | 检查默认存储帐户状态。 |
> | Microsoft.Synapse/workspaces/privateEndpointConnectionsApproval/action | 确定是否允许用户自动批准与工作区的专用终结点连接 |
> | Microsoft.Synapse/workspaces/administrators/write | 在工作区上设置 Active Directory 管理员 |
> | Microsoft.Synapse/workspaces/administrators/read | 获取工作区 Active Directory 管理员 |
> | Microsoft.Synapse/workspaces/administrators/delete | 删除工作区 Active Directory 管理员 |
> | Microsoft.Synapse/workspaces/auditingSettings/write | 创建或更新 SQL 服务器审核设置。 |
> | Microsoft.Synapse/workspaces/auditingSettings/read | 读取默认的 SQL 服务器审核设置。 |
> | Microsoft.Synapse/workspaces/azureADOnlyAuthentications/write | 创建或更新针对工作区及其子资源的仅限 Azure AD 的身份验证。 |
> | Microsoft.Synapse/workspaces/azureADOnlyAuthentications/read | 针对工作区及其子资源的仅限 Azure AD 的身份验证的状态。 |
> | Microsoft.Synapse/workspaces/bigDataPools/write | 创建或更新任何 Spark 池。 |
> | Microsoft.Synapse/workspaces/bigDataPools/read | 读取任何 Spark 池。 |
> | Microsoft.Synapse/workspaces/bigDataPools/delete | 删除任何 Spark 池。 |
> | Microsoft.Synapse/workspaces/bigDataPools/providers/Microsoft.Insights/diagnosticSettings/read | 获取大数据池的诊断设置 |
> | Microsoft.Synapse/workspaces/bigDataPools/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新大数据池的诊断设置 |
> | Microsoft.Synapse/workspaces/bigDataPools/providers/Microsoft.Insights/logdefinitions/read | 获取大数据池的日志定义 |
> | Microsoft.Synapse/workspaces/bigDataPools/providers/Microsoft.Insights/metricDefinitions/read | 获取大数据池的可用指标 |
> | Microsoft.Synapse/workspaces/extendedAuditingSettings/write | 创建或更新 SQL 服务器扩展审核设置。 |
> | Microsoft.Synapse/workspaces/extendedAuditingSettings/read | 读取默认的 SQL 服务器扩展审核设置。 |
> | Microsoft.Synapse/workspaces/firewallRules/write | 创建或更新任何 IP 防火墙规则。 |
> | Microsoft.Synapse/workspaces/firewallRules/read | 读取 IP 防火墙规则 |
> | Microsoft.Synapse/workspaces/firewallRules/delete | 删除任何 IP 防火墙规则。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/read | 获取任何 Integration Runtime。 |
> | Microsoft.Synapse/workspaces/integrationruntimes/write | 创建或更新任何 Integration Runtime。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/delete | 删除任何 Integration Runtime |
> | Microsoft.Synapse/workspaces/integrationRuntimes/getStatus/action | 获取任何 Integration Runtime 的状态 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/createExpressSHIRInstallLink/action | 创建 Integration Runtime 安装链接 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/start/action | 启动任何 Integration Runtime |
> | Microsoft.Synapse/workspaces/integrationRuntimes/stop/action | 停止任何 Integration Runtime |
> | Microsoft.Synapse/workspaces/integrationRuntimes/getConnectionInfo/action | 获取任何 Integration Runtime 的连接信息 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/regenerateAuthKey/action | 重新生成任何 Integration Runtime 的身份验证密钥 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/listAuthKeys/action | 列出任何 Integration Runtime 的身份验证密钥 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/removeNode/action | 删除任何 Integration Runtime 节点 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/monitoringData/action | 获取任何 Integration Runtime 的监视数据 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/syncCredentials/action | 同步任何 Integration Runtime 上的凭据 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/upgrade/action | 升级任何 Integration Runtime |
> | Microsoft.Synapse/workspaces/integrationRuntimes/removeLinks/action | Remove any Integration Runtime link |
> | Microsoft.Synapse/workspaces/integrationRuntimes/enableInteractiveQuery/action | 在任何 Integration Runtime 上启用交互式查询 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/disableInteractiveQuery/action | 在任何 Integration Runtime 上禁用交互式查询 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/refreshObjectMetadata/action | 刷新任何 Intergration Runtime 上的对象元数据 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/getObjectMetadata/action | 获取任何 Intergration Runtime 上的对象元数据 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/nodes/read | 获取任何 Integration Runtime 节点。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/nodes/delete | 删除任何 Integration Runtime 节点。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/nodes/write | 修补任何 Integration Runtime 节点。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/nodes/ipAddress/action | 获取 Integration Runtime IP 地址 |
> | Microsoft.Synapse/workspaces/keys/write | 创建或更新工作区密钥 |
> | Microsoft.Synapse/workspaces/keys/read | 读取任何工作区密钥定义。 |
> | Microsoft.Synapse/workspaces/keys/delete | 删除任何工作区密钥。 |
> | Microsoft.Synapse/workspaces/kustoPools/read | 读取群集资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/write | 写入群集资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/delete | 删除群集资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Start/action | 启动群集。 |
> | Microsoft.Synapse/workspaces/kustoPools/Stop/action | 停止群集。 |
> | Microsoft.Synapse/workspaces/kustoPools/CheckNameAvailability/action | 检查群集名称可用性。 |
> | Microsoft.Synapse/workspaces/kustoPools/ListLanguageExtensions/action | 列出语言扩展。 |
> | Microsoft.Synapse/workspaces/kustoPools/AddLanguageExtensions/action | 添加语言扩展。 |
> | Microsoft.Synapse/workspaces/kustoPools/RemoveLanguageExtensions/action | 删除语言扩展。 |
> | Microsoft.Synapse/workspaces/kustoPools/DetachFollowerDatabases/action | 分离关注者的数据库。 |
> | Microsoft.Synapse/workspaces/kustoPools/ListFollowerDatabases/action | 列出关注者的数据库。 |
> | Microsoft.Synapse/workspaces/kustoPools/AttachedDatabaseConfigurations/read | 读取附加的数据库配置资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/AttachedDatabaseConfigurations/write | 写入附加的数据库配置资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/AttachedDatabaseConfigurations/delete | 删除附加的数据库配置资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/read | 读取数据库资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/write | 写入数据库资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/delete | 删除数据库资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/SynapseDataConnectionValidation/action | 验证数据库数据连接。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/DataConnections/read | 读取数据连接资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/DataConnections/write | 写入数据连接资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/DataConnections/delete | 删除数据连接资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/PrincipalAssignments/read | 读取数据库主体分配资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/PrincipalAssignments/write | 写入数据库主体分配资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/Databases/PrincipalAssignments/delete | 删除数据库主体分配资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/PrincipalAssignments/read | 读取 Cluster 主体分配资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/PrincipalAssignments/write | 写入 Cluster 主体分配资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/PrincipalAssignments/delete | 删除 Cluster 主体分配资源。 |
> | Microsoft.Synapse/workspaces/kustoPools/PrivateEndpointConnectionProxies/read | 读取专用终结点连接代理 |
> | Microsoft.Synapse/workspaces/kustoPools/PrivateEndpointConnectionProxies/write | 写入专用终结点连接代理 |
> | Microsoft.Synapse/workspaces/kustoPools/PrivateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Synapse/workspaces/kustoPools/PrivateEndpointConnectionProxies/Validate/action | 验证专用终结点连接代理 |
> | Microsoft.Synapse/workspaces/kustoPools/PrivateEndpointConnections/read | 读取专用终结点连接 |
> | Microsoft.Synapse/workspaces/kustoPools/PrivateEndpointConnections/write | 写入专用终结点连接 |
> | Microsoft.Synapse/workspaces/kustoPools/PrivateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Synapse/workspaces/kustoPools/PrivateLinkResources/read | 读取专用链接资源 |
> | Microsoft.Synapse/workspaces/kustoPools/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Synapse/workspaces/kustoPools/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Synapse/workspaces/kustoPools/providers/Microsoft.Insights/logDefinitions/read | 获取资源的诊断日志设置 |
> | Microsoft.Synapse/workspaces/kustoPools/providers/Microsoft.Insights/metricDefinitions/read | 获取资源的指标定义 |
> | Microsoft.Synapse/workspaces/kustoPools/SKUs/read | 读取群集 SKU 资源。 |
> | Microsoft.Synapse/workspaces/libraries/read | 读取库项目 |
> | Microsoft.Synapse/workspaces/managedIdentitySqlControlSettings/write | 更新工作区中的托管标识 SQL 控制设置 |
> | Microsoft.Synapse/workspaces/managedIdentitySqlControlSettings/read | 获取托管标识 SQL 控制设置 |
> | Microsoft.Synapse/workspaces/operationResults/read | 读取任何异步操作结果。 |
> | Microsoft.Synapse/workspaces/operationStatuses/read | 读取任何异步操作状态。 |
> | Microsoft.Synapse/workspaces/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Synapse/workspaces/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理 |
> | Microsoft.Synapse/workspaces/privateEndpointConnectionProxies/read | 读取任何专用终结点连接代理 |
> | Microsoft.Synapse/workspaces/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Synapse/workspaces/privateEndpointConnectionProxies/updatePrivateEndpointProperties/action | 更新专用终结点连接代理属性。 |
> | Microsoft.Synapse/workspaces/privateEndpointConnections/write | 创建或更新专用终结点连接 |
> | Microsoft.Synapse/workspaces/privateEndpointConnections/read | 读取任何专用终结点连接 |
> | Microsoft.Synapse/workspaces/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Synapse/workspaces/privateLinkResources/read | 获取专用链接资源的列表 |
> | Microsoft.Synapse/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取工作区的诊断设置 |
> | Microsoft.Synapse/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新工作区的诊断设置 |
> | Microsoft.Synapse/workspaces/providers/Microsoft.Insights/logDefinitions/read | 获取 Synapse 工作区的日志定义 |
> | Microsoft.Synapse/workspaces/providers/Microsoft.Insights/metricDefinitions/read | 获取工作区的指标定义 |
> | Microsoft.Synapse/workspaces/recoverableSqlpools/read | 获取可恢复的 SQL Analytics 池，这些池是表示 SQL Analytics 池的异地备份的资源 |
> | Microsoft.Synapse/workspaces/restorableDroppedSqlPools/read | 获取可还原的已删除 Sql 池 |
> | Microsoft.Synapse/workspaces/scopePools/write | 创建或更新任何 Scope 池。 |
> | Microsoft.Synapse/workspaces/scopePools/read | 读取任何 Scope 池。 |
> | Microsoft.Synapse/workspaces/scopePools/delete | 删除任何 Scope 池。 |
> | Microsoft.Synapse/workspaces/securityAlertPolicies/write | 创建或更新 SQL 服务器安全警报策略。 |
> | Microsoft.Synapse/workspaces/securityAlertPolicies/read | 读取默认的 SQL 服务器安全警报策略。 |
> | Microsoft.Synapse/workspaces/sparkConfigurations/read | 读取 SparkConfiguration 项目 |
> | Microsoft.Synapse/workspaces/sqlAdministrators/write | 在工作区上设置 Active Directory 管理员 |
> | Microsoft.Synapse/workspaces/sqlAdministrators/read | 获取工作区 Active Directory 管理员 |
> | Microsoft.Synapse/workspaces/sqlAdministrators/delete | 删除工作区 Active Directory 管理员 |
> | Microsoft.Synapse/workspaces/sqlDatabases/write | 创建或更新任何 SQL Analytics 数据库。 |
> | Microsoft.Synapse/workspaces/sqlDatabases/read | 读取任何 SQL Analytics 数据库。 |
> | Microsoft.Synapse/workspaces/sqlPools/write | 创建或更新任何 SQL Analytics 池。 |
> | Microsoft.Synapse/workspaces/sqlPools/read | 读取任何 SQL Analytics 池。 |
> | Microsoft.Synapse/workspaces/sqlPools/delete | 删除任何 SQL Analytics 池。 |
> | Microsoft.Synapse/workspaces/sqlPools/pause/action | 暂停任何 SQL Analytics 池。 |
> | Microsoft.Synapse/workspaces/sqlPools/resume/action | 恢复任何 SQL Analytics 池。 |
> | Microsoft.Synapse/workspaces/sqlPools/restorePoints/action | 创建 SQL Analytics 池还原点。 |
> | Microsoft.Synapse/workspaces/sqlPools/move/action | 重命名任何 SQL Analytics 池。 |
> | Microsoft.Synapse/workspaces/sqlPools/auditingSettings/read | 读取任何 SQL Analytics 池审核设置。 |
> | Microsoft.Synapse/workspaces/sqlPools/auditingSettings/write | 创建或更新任何 SQL Analytics 池审核设置。 |
> | Microsoft.Synapse/workspaces/sqlPools/auditRecords/read | 获取 Sql 池 blob 审核记录 |
> | Microsoft.Synapse/workspaces/sqlPools/columns/read | 返回 SQL Analytics 池的列的列表 |
> | Microsoft.Synapse/workspaces/sqlPools/connectionPolicies/read | 读取任何 SQL Analytics 池连接策略。 |
> | Microsoft.Synapse/workspaces/sqlPools/currentSensitivityLabels/read | 读取任何 SQL Analytics 池的当前敏感度标签。 |
> | Microsoft.Synapse/workspaces/sqlPools/currentSensitivityLabels/write | 批量更新当前的敏感度标签 |
> | Microsoft.Synapse/workspaces/sqlPools/dataMaskingPolicies/read | 返回 SQL Analytics 池数据掩码策略的列表。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataMaskingPolicies/write | 创建或更新 SQL Analytics 池数据掩码策略 |
> | Microsoft.Synapse/workspaces/sqlPools/dataMaskingPolicies/rules/read | 获取 SQL Analytics 池数据掩码规则的列表。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataMaskingPolicies/rules/write | 创建或更新 SQL Analytics 池数据掩码规则。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataWarehouseQueries/read | 读取任何 SQL Analytics 池查询。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataWarehouseQueries/dataWarehouseQuerySteps/read | 读取任何 SQL Analytics 池查询步骤。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataWarehouseQueries/Steps/read | 读取任何 SQL Analytics 池查询步骤。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataWarehouseUserActivities/read | 读取任何 SQL Analytics 池用户活动。 |
> | Microsoft.Synapse/workspaces/sqlPools/extendedAuditingSettings/read | 读取任何 SQL Analytics 池扩展审核设置。 |
> | Microsoft.Synapse/workspaces/sqlPools/extendedAuditingSettings/write | 创建或更新任何 SQL Analytics 池扩展审核设置。 |
> | Microsoft.Synapse/workspaces/sqlPools/extensions/read | 获取 SQL Analytics 池扩展 |
> | Microsoft.Synapse/workspaces/sqlPools/extensions/write | 更改给定 SQL Analytics 池的扩展 |
> | Microsoft.Synapse/workspaces/sqlPools/geoBackupPolicies/read | 读取任何 SQL Analytics 池异地备份策略。 |
> | Microsoft.Synapse/workspaces/sqlPools/maintenanceWindowOptions/read | 读取任何 SQL Analytics 池维护窗口选项。 |
> | Microsoft.Synapse/workspaces/sqlPools/maintenanceWindows/read | 读取任何 SQL Analytics 池维护窗口。 |
> | Microsoft.Synapse/workspaces/sqlPools/maintenanceWindows/write | 读取任何 SQL Analytics 池维护窗口。 |
> | Microsoft.Synapse/workspaces/sqlPools/metadataSync/write | 创建或更新 SQL Analytics 池元数据同步配置 |
> | Microsoft.Synapse/workspaces/sqlPools/metadataSync/read | 读取 SQL Analytics 池元数据同步配置 |
> | Microsoft.Synapse/workspaces/sqlPools/operationResults/read | 读取任何异步操作结果。 |
> | Microsoft.Synapse/workspaces/sqlPools/operations/read | 读取任何 SQL Analytics 池操作。 |
> | Microsoft.Synapse/workspaces/sqlPools/operationStatuses/read | 读取任何异步操作结果。 |
> | Microsoft.Synapse/workspaces/sqlPools/providers/Microsoft.Insights/diagnosticSettings/read | 获取 SQL 池的诊断设置 |
> | Microsoft.Synapse/workspaces/sqlPools/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 SQL 池的诊断设置 |
> | Microsoft.Synapse/workspaces/sqlPools/providers/Microsoft.Insights/logdefinitions/read | 获取 SQL 池的日志定义 |
> | Microsoft.Synapse/workspaces/sqlPools/providers/Microsoft.Insights/metricDefinitions/read | 获取 SQL 池的可用指标 |
> | Microsoft.Synapse/workspaces/sqlPools/recommendedSensitivityLabels/read | 读取任何 SQL Analytics 池建议的敏感度标签。 |
> | Microsoft.Synapse/workspaces/sqlPools/recommendedSensitivityLabels/write | 批量更新建议的敏感度标签 |
> | Microsoft.Synapse/workspaces/sqlPools/replicationLinks/read | 读取任何 SQL Analytics 池复制链接。 |
> | Microsoft.Synapse/workspaces/sqlPools/restorePoints/read | 读取任何 SQL Analytics 池还原点。 |
> | Microsoft.Synapse/workspaces/sqlPools/restorePoints/delete | 删除还原点。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/read | 读取任何 SQL Analytics 池架构。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/read | 读取任何 SQL Analytics 池架构表。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/read | 读取任何 SQL Analytics 池架构表列。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/read | 获取给定列的敏感度标签。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/enable/action | 启用任何 SQL Analytics 池敏感度标签。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/disable/action | 禁用任何 SQL Analytics 池敏感度标签。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/write | 创建或更新任何 SQL Analytics 池敏感度标签。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/delete | 删除任何 SQL Analytics 池敏感度标签。 |
> | Microsoft.Synapse/workspaces/sqlPools/securityAlertPolicies/read | 读取任何 SQL Analytics 池威胁检测策略。 |
> | Microsoft.Synapse/workspaces/sqlPools/securityAlertPolicies/write | 创建或更新任何 SQL Analytics 池威胁检测策略。 |
> | Microsoft.Synapse/workspaces/sqlPools/sensitivityLabels/read | 获取给定列的敏感度标签。 |
> | Microsoft.Synapse/workspaces/sqlPools/transparentDataEncryption/read | 读取任何 SQL Analytics 池透明数据加密配置。 |
> | Microsoft.Synapse/workspaces/sqlPools/transparentDataEncryption/write | 创建或更新任何 SQL Analytics 池透明数据加密配置。 |
> | Microsoft.Synapse/workspaces/sqlPools/transparentDataEncryption/operationResults/read | 读取任何 SQL Analytics 池透明数据加密配置操作结果。 |
> | Microsoft.Synapse/workspaces/sqlPools/usages/read | 读取任何 SQL Analytics 池使用情况。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/read | 读取任何 SQL Analytics 池漏洞评估。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/write | 创建或更新 SQL 池漏洞评估 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/delete | 删除任何 SQL Analytics 池漏洞评估。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/rules/baselines/read | 获取 SQL Analytics 池漏洞评估规则基线。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/rules/baselines/write | 创建或更新任何 SQL Analytics 池漏洞评估规则基线。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/rules/baselines/delete | 删除任何 SQL Analytics 池漏洞评估规则基线。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/scans/read | 读取任何 SQL Analytics 池漏洞评估扫描记录。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/scans/initiateScan/action | 启动任何 SQL Analytics 池漏洞评估扫描记录。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/scans/export/action | 导出任何 SQL Analytics 池漏洞评估扫描记录。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/read | 列出所选 SQL 池的工作负荷组。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/write | 设置特定工作负荷组的属性。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/delete | 删除特定的工作负荷组。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/operationStatuses/read | SQL Analytics 池工作负荷组操作状态 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/read | 列出所选 SQL Analytics 池的工作负荷分类器。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/write | 设置特定工作负荷分类器的属性。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/delete | 删除特定的工作负荷分类器。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/operationResults/read | SQL Analytics 池工作负荷分类器操作结果 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/operationStatuses/read | SQL Analytics 池工作负荷分类器操作状态 |
> | Microsoft.Synapse/workspaces/sqlUsages/read | 获取可供 SQL Analytics 池使用的使用量限制 |
> | Microsoft.Synapse/workspaces/trustedServiceBypassConfiguration/write | 更新工作区的受信任服务跳过配置。 |
> | Microsoft.Synapse/workspaces/vulnerabilityAssessments/write | 创建或更新 SQL 服务器漏洞评估报告。 |
> | Microsoft.Synapse/workspaces/vulnerabilityAssessments/read | 读取默认的 SQL 服务器漏洞评估报告。 |
> | Microsoft.Synapse/workspaces/vulnerabilityAssessments/delete | 删除 SQL 服务器漏洞评估报告。 |

## <a name="blockchain"></a>区块链

### <a name="microsoftblockchain"></a>Microsoft.Blockchain

Azure 服务：[Azure 区块链服务](../blockchain/workbench/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Blockchain/register/action | 注册区块链资源提供程序的订阅。 |
> | Microsoft.Blockchain/blockchainMembers/read | 获取或列出现有区块链成员。 |
> | Microsoft.Blockchain/blockchainMembers/write | 创建或更新区块链成员。 |
> | Microsoft.Blockchain/blockchainMembers/delete | 删除现有区块链成员。 |
> | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | 获取或列出现有区块链成员 API 密钥。 |
> | Microsoft.Blockchain/blockchainMembers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Blockchain/blockchainMembers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Blockchain/blockchainMembers/providers/Microsoft.Insights/logDefinitions/read | 获取 Microsoft 区块链的可用日志 |
> | Microsoft.Blockchain/blockchainMembers/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft 区块链的可用指标 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | 获取或列出现有的区块链成员事务节点。 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | 创建或更新区块链成员事务节点。 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | 删除现有的区块链成员事务节点。 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | 获取或列出现有的区块链成员事务节点 API 密钥。 |
> | Microsoft.Blockchain/cordaMembers/read | 获取或列出现有区块链 Corda 成员。 |
> | Microsoft.Blockchain/cordaMembers/write | 创建或更新区块链 Corda 成员。 |
> | Microsoft.Blockchain/cordaMembers/delete | 删除现有区块链 Corda 成员。 |
> | Microsoft.Blockchain/cordaMembers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Blockchain/cordaMembers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Blockchain/cordaMembers/providers/Microsoft.Insights/logDefinitions/read | 获取 Microsoft 区块链的可用日志 |
> | Microsoft.Blockchain/locations/checkNameAvailability/action | 检查资源名称是否有效且未被使用。 |
> | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | 获取区块链成员的操作结果。 |
> | Microsoft.Blockchain/operations/read | 列出 Microsoft 区块链资源提供程序中的所有操作。 |
> | **DataAction** | **说明** |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | 连接到区块链成员事务节点。 |

## <a name="ai--machine-learning"></a>AI + 机器学习

### <a name="microsoftbotservice"></a>Microsoft.BotService

Azure 服务：[Azure 机器人服务](/azure/bot-service/)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.BotService/checknameavailability/action | 检查机器人的名称可用性 |
> | Microsoft.BotService/listauthserviceproviders/action | 列出身份验证服务提供程序 |
> | Microsoft.BotService/botServices/read | 读取机器人服务 |
> | Microsoft.BotService/botServices/write | 写入机器人服务 |
> | Microsoft.BotService/botServices/delete | 删除机器人服务 |
> | Microsoft.BotService/botServices/channels/read | 读取机器人服务通道 |
> | Microsoft.BotService/botServices/channels/write | 写入机器人服务通道 |
> | Microsoft.BotService/botServices/channels/delete | 删除机器人服务通道 |
> | Microsoft.BotService/botServices/channels/listchannelwithkeys/action | 列出具有机密的机器人服务通道 |
> | Microsoft.BotService/botServices/channels/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.BotService/botServices/channels/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/botServices/channels/providers/Microsoft.Insights/logDefinitions/read | 获取&lt;资源名称&gt;的可用日志 |
> | Microsoft.BotService/botServices/channels/providers/Microsoft.Insights/metricDefinitions/read | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/botServices/connections/read | 读取机器人服务连接 |
> | Microsoft.BotService/botServices/connections/write | 写入机器人服务连接 |
> | Microsoft.BotService/botServices/connections/delete | 删除机器人服务连接 |
> | Microsoft.BotService/botServices/connections/listwithsecrets/write | 写入机器人服务连接列表  |
> | Microsoft.BotService/botServices/connections/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.BotService/botServices/connections/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/botServices/connections/providers/Microsoft.Insights/logDefinitions/read | 获取&lt;资源名称&gt;的可用日志 |
> | Microsoft.BotService/botServices/connections/providers/Microsoft.Insights/metricDefinitions/read | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/botServices/privateEndpointConnectionProxies/read | 读取连接代理资源 |
> | Microsoft.BotService/botServices/privateEndpointConnectionProxies/write | 写入连接代理资源 |
> | Microsoft.BotService/botServices/privateEndpointConnectionProxies/delete | 删除连接代理资源 |
> | Microsoft.BotService/botServices/privateEndpointConnectionProxies/validate/action | 验证连接代理资源 |
> | Microsoft.BotService/botServices/privateEndpointConnections/read | 读取专用终结点连接资源 |
> | Microsoft.BotService/botServices/privateEndpointConnections/write | 写入专用终结点连接资源 |
> | Microsoft.BotService/botServices/privateEndpointConnections/delete | 删除专用终结点连接资源 |
> | Microsoft.BotService/botServices/privateLinkResources/read | 读取专用链接资源 |
> | Microsoft.BotService/botServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.BotService/botServices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/botServices/providers/Microsoft.Insights/logDefinitions/read | 获取&lt;资源名称&gt;的可用日志 |
> | Microsoft.BotService/botServices/providers/Microsoft.Insights/metricDefinitions/read | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/checknameavailability/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.BotService/checknameavailability/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/checknameavailability/providers/Microsoft.Insights/logDefinitions/read | 获取&lt;资源名称&gt;的可用日志 |
> | Microsoft.BotService/checknameavailability/providers/Microsoft.Insights/metricDefinitions/read | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/hostsettings/read | 获取托管机器人服务所需的设置 |
> | Microsoft.BotService/hostsettings/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.BotService/hostsettings/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/hostsettings/providers/Microsoft.Insights/logDefinitions/read | 获取&lt;资源名称&gt;的可用日志 |
> | Microsoft.BotService/hostsettings/providers/Microsoft.Insights/metricDefinitions/read | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/listauthserviceproviders/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.BotService/listauthserviceproviders/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/listauthserviceproviders/providers/Microsoft.Insights/logDefinitions/read | 获取&lt;资源名称&gt;的可用日志 |
> | Microsoft.BotService/listauthserviceproviders/providers/Microsoft.Insights/metricDefinitions/read | 创建或更新资源的诊断设置 |
> | Microsoft.BotService/locations/operationresults/read | 读取异步操作的状态 |
> | Microsoft.BotService/operationresults/read | 读取异步操作的状态 |
> | Microsoft.BotService/Operations/read | 读取所有资源类型的操作 |

### <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

Azure 服务：[认知服务](../cognitive-services/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.CognitiveServices/register/action | 订阅注册操作 |
> | Microsoft.CognitiveServices/register/action | 注册认知服务的订阅 |
> | Microsoft.CognitiveServices/checkDomainAvailability/action | 读取订阅的可用 SKU。 |
> | Microsoft.CognitiveServices/checkDomainAvailability/action | 读取订阅的可用 SKU。 |
> | Microsoft.CognitiveServices/register/action | 注册认知服务的订阅 |
> | Microsoft.CognitiveServices/register/action | 注册认知服务的订阅 |
> | Microsoft.CognitiveServices/register/action | 注册认知服务的订阅 |
> | Microsoft.CognitiveServices/register/action | 注册认知服务的订阅 |
> | Microsoft.CognitiveServices/accounts/read | 读取 API 帐户。 |
> | Microsoft.CognitiveServices/accounts/write | 写入 API 帐户。 |
> | Microsoft.CognitiveServices/accounts/delete | 删除 API 帐户 |
> | Microsoft.CognitiveServices/accounts/listKeys/action | 列出密钥 |
> | Microsoft.CognitiveServices/accounts/regenerateKey/action | 再生成密钥 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnectionProxies/read | 读取专用终结点连接。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnectionProxies/write | 写入专用终结点连接。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnectionProxies/delete | 删除专用终结点连接。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnections/read | 读取专用终结点连接。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnections/write | 写入专用终结点连接。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnections/delete | 删除专用终结点连接。 |
> | Microsoft.CognitiveServices/accounts/privateLinkResources/read | 读取帐户的专用链接资源。 |
> | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置。 |
> | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置。 |
> | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/logDefinitions/read | 获取认知服务帐户的可用日志 |
> | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | 获取认知服务的可用指标。 |
> | Microsoft.CognitiveServices/accounts/skus/read | 读取现有资源的可用 SKU。 |
> | Microsoft.CognitiveServices/accounts/usages/read | 获取现有资源的配额用量。 |
> | Microsoft.CognitiveServices/deletedAccounts/read | 列出已删除的帐户。 |
> | Microsoft.CognitiveServices/deletedAccounts/read | 列出已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 读取订阅的可用 SKU。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 读取订阅的可用 SKU。 |
> | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.Network 提供的删除 VirtualNetworks 或子网的通知。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 读取订阅的可用 SKU。 |
> | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.Network 提供的删除 VirtualNetworks 或子网的通知。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 读取订阅的可用 SKU。 |
> | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.Network 提供的删除 VirtualNetworks 或子网的通知。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 读取订阅的可用 SKU。 |
> | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.Network 提供的删除 VirtualNetworks 或子网的通知。 |
> | Microsoft.CognitiveServices/locations/deletedAccounts/read | 获取已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/deletedAccounts/purge/action | 清除已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/deletedAccounts/read | 获取已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/deletedAccounts/purge/action | 清除已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/deletedAccounts/read | 获取已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/deletedAccounts/purge/action | 清除已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/deletedAccounts/read | 获取已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/deletedAccounts/purge/action | 清除已删除的帐户。 |
> | Microsoft.CognitiveServices/locations/operationresults/read | 读取异步操作的状态。 |
> | Microsoft.CognitiveServices/Operations/read | 读取所有可用操作 |
> | Microsoft.CognitiveServices/skus/read | 读取认知服务的可用 SKU。 |
> | Microsoft.CognitiveServices/skus/read | 读取认知服务的可用 SKU。 |
> | Microsoft.CognitiveServices/skus/read | 读取认知服务的可用 SKU。 |
> | Microsoft.CognitiveServices/skus/read | 读取认知服务的可用 SKU。 |
> | Microsoft.CognitiveServices/skus/read | 读取认知服务的可用 SKU。 |
> | **DataAction** | **说明** |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/multivariate/models/write | 创建和训练多变量异常情况检测模型。<br>请求必须包含用于指示外部可访问的 Azure 存储 URI（最好是共享访问签名 URI）的源参数。<br>用于生成模型的所有时序都必须压缩为单个 zip 文件。<br>每个时序都将位于单个 CSV 文件中，其中第一列是时间戳，第二列是值。 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/multivariate/models/delete | 根据 modelId 删除现有多变量模型 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/multivariate/models/detect/action | 使用带 modelId 经过训练的模型提交多变量异常情况检测任务，输入架构应与训练请求相同。<br>这样一来，请求将异步完成并返回用于查询检测结果的 resultId。请求应为指示外部可访问的 Azure 存储 URI（最好是共享访问签名 URI）的源链接。<br>用于生成模型的所有时序都必须压缩为单个 zip 文件。<br>每个时序将采用以下格式：第一列是时间戳，第二列是值。 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/multivariate/models/read | 获取多变量模型的详细信息，包括训练状态和模型中使用的变量。 列出订阅的模型 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/multivariate/models/export/action | 基于 modelId 导出多变量异常情况检测模型 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/multivariate/results/read | 根据 DetectAnomalyAsync API 返回的 resultId 获取多变量异常情况检测结果 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/timeseries/changepoint/detect/action | 此操作使用整个序列生成模型；每个点是使用同一模型检测的。<br>使用此方法时，特定点之前和之后的点将用于确定该特定点是否为趋势变化点。<br>整个检测可以检测时序的所有趋势变化点。 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/timeseries/entire/detect/action | 此操作使用整个序列生成模型；每个点是使用同一模型检测的。<br>使用此方法时，特定点之前和之后的点将用于确定该特定点是否异常。<br>整个检测可为用户提供时序的总体状态。 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/timeseries/last/detect/action | 此操作使用最新点之前的点生成模型。 使用此方法时，只会使用历史点来确定目标点是否异常。 最新点检测将匹配业务指标的实时监视方案。 |
> | Microsoft.CognitiveServices/accounts/Autosuggest/search/action | 此操作为给定查询或部分查询提供建议。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | 此操作根据图像内容提取一组丰富的可视特征。  |
> | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | 此操作使用完整的句子以人类可读语言生成图像的说明。<br> 该说明基于内容标记的集合，而这些标记也由该操作返回。<br>可为每个图像生成多段说明。<br> 说明按其置信度评分排序。<br>所有说明均为英文。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | 此操作使用用户指定的宽度和高度生成缩略图。<br> 默认情况下，服务将分析图像，标识兴趣区域 (ROI)，并根据 ROI 生成智能裁剪坐标。<br> 指定不同于输入图像的纵横比时，智能裁剪很有帮助 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | 光学字符识别 (OCR) 可以检测图像中的文本，并将识别到的字符提取到机器可用的字符流中。    |
> | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | 使用此接口可以获取“识别文本”操作的结果。 使用“识别文本”接口时，响应将包含名为“Operation-Location”的字段。 “Operation-Location”字段包含必须用于“获取识别文本操作结果”操作的 URL。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | 此操作生成与所提供图像内容相关的单词或标记列表。<br>计算机视觉 API 可以根据在图像中找到的对象、生物、风景或动作返回标记。<br>与类别不同，标记不是根据层次结构分类系统进行组织，而是对应于图像内容。<br>标记可以包含提示以避免多义性，或者可以提供上下文，例如，标记“大提琴”可以附带提示“乐器”。<br>所有标记均为英文。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | 此操作返回围绕图像最重要区域的边框。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | 此操作针对指定的图像执行对象检测。  |
> | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | 此操作返回计算机视觉 API 支持的域特定模型列表。  目前，该 API 支持以下域特定的模型：名人识别器、地标识别器。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | 此操作通过应用域特定的模型来识别图像中的内容。<br> 可以使用 /models GET 请求检索计算机视觉 API 支持的域特定模型列表。<br> 目前，该 API 提供以下域特定的模型：名人、地标。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/read/analyze/action | 使用此接口可以采用针对包含大量文本的文档优化的一流光学字符识别 (OCR) 算法执行读取操作。<br>它可以处理手写、打印或混合文档。<br>使用“读取”接口时，响应将包含名为“Operation-Location”的标头。<br>“Operation-Location”头包含必须在“获取读取结果”操作中用来访问 OCR 结果的 URL。** |
> | Microsoft.CognitiveServices/accounts/ComputerVision/read/analyzeresults/read | 使用此接口可以检索读取操作的状态和 OCR 结果。  将在读取操作的“Operation-Location”响应标头中返回包含“operationId”的 URL。* |
> | Microsoft.CognitiveServices/accounts/ComputerVision/read/core/asyncbatchanalyze/action | 使用此接口可以采用一流的光学字符算法获取“批量读取文件”操作的结果 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/read/operations/read | 此接口用于获取读取操作的 OCR 结果。 应该从“批量读取文件”接口返回的<b>“Operation-Location”</b>字段检索此接口的 URL。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | 此接口用于获取“识别文本”操作的结果。 应该通过“识别文本”接口返回的<b>“Operation-Location”</b>字段检索此接口的 URL。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/action | 创建图像列表。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/action | 创建团队列表。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/read | 图像列表 - 获取详细信息 - 图像列表 - 获取所有 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/delete | 图像列表 - 删除 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/refreshindex/action | 图像列表 - 刷新搜索索引 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/write | 图像列表 - 更新详细信息 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/write | 将图像添加到图像列表。 使用“图像/匹配”API 时，可以使用图像列表对其他图像进行模糊匹配。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/delete | 从图像列表中删除图像。 使用“图像/匹配”API 时，可以使用图像列表对其他图像进行模糊匹配。 从列表中删除所有图像。 使用“图像/匹配”API 时，可以使用图像列表对其他图像进行模糊匹配。* |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/read | 图像 - 获取所有图像 ID |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/evaluate/action | 返回图像包含猥亵内容或成人内容的概率。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/findfaces/action | 查找图像中的人脸。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/match/action | 根据某个自定义图像列表模糊匹配某个图像。 可以使用此 API 创建和管理自定义图像列表。  |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/ocr/action | 返回在图像中找到的采用指定语言的任何文本。 如果未在输入中指定语言，则检测结果默认为英语。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processtext/detectlanguage/action | 此操作检测给定输入内容的语言。 返回构成提交文本的首要语言的 ISO 639-3 代码。 支持 110 多种语言。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processtext/screen/action | 该操作检测 100 多种语言的猥亵内容，并将其与自定义和共享的阻止列表相匹配。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/jobs/action | 将返回在此终结点上发布的图像内容的作业 ID。  |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/action | 将为团队中的评审者显示创建的评审。 评审者完成评审后，将在指定的 CallBackEndpoint 上发布评审结果（即 HTTP POST）。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/jobs/read | 获取作业 ID 的作业详细信息。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/read | 返回传递的评审 ID 的评审详细信息。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/publish/action | 视频评论最初是以未发布状态创建的 - 这意味着，团队中的评审者还不能使用它来进行评审。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcript/action | 此 API 将脚本文件（视频中讲述的所有单词的文本版本）添加到视频评审。 文件应采用有效的 WebVTT 格式。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcriptmoderationresult/action | 此 API 为视频评审添加脚本筛选文本结果文件。 脚本筛选文本结果文件是“筛选文本”API 的结果。 若要生成脚本筛选文本结果文件，必须使用“筛选文本”API 来筛选脚本文件中的猥琐内容。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/accesskey/read | 获取团队的评审内容访问密钥。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/write | 使用此方法可为视频评审添加帧。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/write | 创建或更新指定的模板 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/delete | 删除团队中的模板 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/read | 返回在此团队中预配的评审模板数组。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/write | 创建新的或更新现有的工作流。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/read | 获取团队中特定工作流的详细信息。获取可用于团队的所有工作流* |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/bulkupdate/action | 字词列表 - 批量更新 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/delete | 字词列表 - 删除 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/read | 字词列表 - 获取所有 - 字词列表 - 获取详细信息 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/refreshindex/action | 字词列表 - 刷新搜索索引 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/write | 字词列表 - 更新详细信息 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/write | 字词 - 添加字词 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/delete | 字词 - 删除 - 字词 - 删除所有字词 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/read | 字词 - 获取所有字词 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/action | 创建项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/user/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/quota/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/action | 创建项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/action | 将图像分类并保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/action | 将图像 URL 分类并保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/nostore/action | 将图像分类但不保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/nostore/action | 将图像 URL 分类但不保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/action | 检测图像中的对象并保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/action | 检测图像 URL 中的对象并保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/nostore/action | 检测图像中的对象但不保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/nostore/action | 检测图像 URL 中的对象但不保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/domains/read | 获取有关特定域的信息。 获取可用域的列表。* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/action | 设置标签建议的池大小。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/read | 获取此项目的标签建议的池大小。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/project/migrate/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/action | 此 API 接受 multipart/form-data 和 application/octet-stream 形式的正文内容。 使用 multipart 时 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/action | 为项目创建标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/delete | 删除特定的项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/read | 获取特定的项目。 获取项目。* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/train/action | 将要训练的项目排队。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/write | 更新特定的项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/import/action | 导入项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/export/read | 导出项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regions/action | 此 API 接受一批图像区域和（可选）标记，以使用区域信息更新现有图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/files/action | 此 API 接受使用文件批（有时还接受使用标记）来创建图像。 限制为 64 个图像和 20 个标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/predictions/action | 此 API 基于指定的预测图像创建一批图像。 限制为 64 个图像和 20 个标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/urls/action | 此 API 接受使用 URL 批（有时还接受使用标记）来创建图像。 限制为 64 个图像和 20 个标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tags/action | 将一组图像与一组标记相关联。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/delete | 从训练图像集中删除图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regionproposals/action | 此 API 获取图像的区域建议以及区域的置信度。 如果未找到建议，则返回空数组。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/suggested/action | 此 API 提取按建议的标记 ID 筛选的未标记图像。 如果未找到图像，则返回空数组。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/id/read | 此 API 返回指定标记和（可选）迭代的一组图像。 如果未指定迭代 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regions/delete | 删除一组图像区域。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/suggested/count/action | 此 API 使用 tagId 获取按给定阈值的建议标记提供的未标记图像计数。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tagged/read | 此 API 支持批处理和范围选择。 默认情况下，它只返回与图像匹配的前 50 个图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tagged/count/read | 筛选依据为某个字词和/或关系。 例如，如果提供的标记 ID 适用于“Dog”， |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tags/delete | 从一组图像中删除一组标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/untagged/read | 此 API 支持批处理和范围选择。 默认情况下，它只返回与图像匹配的前 50 个图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/untagged/count/read | 此 API 返回没有给定项目的标记和（可选）迭代的图像。 如果未指定迭代 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/delete | 删除项目的特定迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/export/action | 导出训练的迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/read | 获取特定的迭代。 获取项目的迭代。* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/publish/action | 发布特定的迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/write | 更新特定的迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/export/read | 获取特定迭代的导出列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/read | 获取有关迭代的详细性能信息。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/images/read | 此 API 支持批处理和范围选择。 默认情况下，它只返回与图像匹配的前 50 个图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/images/count/read | 筛选依据为某个字词和/或关系。 例如，如果提供的标记 ID 适用于“Dog”， |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/publish/delete | 取消发布特定的迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/predictions/delete | 删除一组预测图像及其关联的预测结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/predictions/query/action | 获取已发送到预测终结点的图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/quicktest/image/action | 快速测试图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/quicktest/url/action | 快速测试图像 URL。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/delete | 删除项目的标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/read | 获取有关特定标记的信息。 获取给定项目和迭代的标记。* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/write | 更新标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tagsandregions/suggestions/action | 此 API 获取未标记图像数组/批的建议标记和区域，以及标记的置信度。 如果未找到标记，则返回空数组。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/train/advanced/action | 将要通过 PipelineConfiguration 和训练类型进行训练的项目排队。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/delete | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/refresh/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/prediction/user/read | 获取 Oxford 用户的预测资源用量 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/training/resource/tier/read | 获取 Azure 用户的训练资源用量 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/training/user/read | 获取 Oxford 用户的训练资源用量 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/delete | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/state/write | 更新用户状态 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/tier/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/users/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/delete | 删除具有特定功能的已加入允许列表的用户 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/read | 获取具有特定功能的已加入允许列表的用户列表 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/write | 在允许列表中更新或创建具有特定功能的用户 |
> | Microsoft.CognitiveServices/accounts/CustomVision/classify/iterations/image/action | 将图像分类并保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/classify/iterations/url/action | 将图像 URL 分类并保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/classify/iterations/image/nostore/action | 将图像分类但不保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/classify/iterations/url/nostore/action | 将图像 URL 分类但不保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/detect/iterations/image/action | 检测图像中的对象并保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/detect/iterations/url/action | 检测图像 URL 中的对象并保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/detect/iterations/image/nostore/action | 检测图像中的对象但不保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/detect/iterations/url/nostore/action | 检测图像 URL 中的对象但不保存结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/domains/read | 获取有关特定域的信息。 获取可用域的列表。* |
> | Microsoft.CognitiveServices/accounts/CustomVision/labelproposals/setting/action | 设置标签建议的池大小。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/labelproposals/setting/read | 获取此项目的标签建议的池大小。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/project/migrate/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/action | 此 API 接受 multipart/form-data 和 application/octet-stream 形式的正文内容。 使用 multipart 时 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/action | 为项目创建标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/delete | 删除特定的项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/read | 获取特定的项目。 获取项目。* |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/train/action | 将要训练的项目排队。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/write | 更新特定的项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action | 导入项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/artifacts/read | 基于 blob 中的项目相对路径，从 blob 存储获取项目内容。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read | 导出项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/regions/action | 此 API 接受一批图像区域和（可选）标记，以使用区域信息更新现有图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/files/action | 此 API 接受使用文件批（有时还接受使用标记）来创建图像。 限制为 64 个图像和 20 个标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/predictions/action | 此 API 基于指定的预测图像创建一批图像。 限制为 64 个图像和 20 个标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/urls/action | 此 API 接受使用 URL 批（有时还接受使用标记）来创建图像。 限制为 64 个图像和 20 个标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/tags/action | 将一组图像与一组标记相关联。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/delete | 从训练图像集中删除图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/regionproposals/action | 此 API 获取图像的区域建议以及区域的置信度。 如果未找到建议，则返回空数组。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/action | 此 API 提取按建议的标记 ID 筛选的未标记图像。 如果未找到图像，则返回空数组。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/read | 此 API 支持批处理和范围选择。 默认情况下，它只返回与图像匹配的前 50 个图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/metadata/action | 此 API 接受一批映像 ID 和元数据，以更新映像。 图像限制为 64 个。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/count/read | 筛选依据为某个字词和/或关系。 例如，如果提供的标记 ID 适用于“Dog”， |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/id/read | 此 API 返回指定标记和（可选）迭代的一组图像。 如果未指定迭代 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/regions/delete | 删除一组图像区域。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/count/action | 此 API 使用 tagId 获取按给定阈值的建议标记提供的未标记图像计数。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/tagged/read | 此 API 支持批处理和范围选择。 默认情况下，它只返回与图像匹配的前 50 个图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/tagged/count/read | 筛选依据为某个字词和/或关系。 例如，如果提供的标记 ID 适用于“Dog”， |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/tags/delete | 从一组图像中删除一组标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/untagged/read | 此 API 支持批处理和范围选择。 默认情况下，它只返回与图像匹配的前 50 个图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/untagged/count/read | 此 API 返回没有给定项目的标记和（可选）迭代的图像。 如果未指定迭代 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/delete | 删除项目的特定迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/action | 导出训练的迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/read | 获取特定的迭代。 获取项目的迭代。* |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/action | 发布特定的迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/write | 更新特定的迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/read | 获取特定迭代的导出列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/performance/read | 获取有关迭代的详细性能信息。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/performance/images/read | 此 API 支持批处理和范围选择。 默认情况下，它只返回与图像匹配的前 50 个图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/performance/images/count/read | 筛选依据为某个字词和/或关系。 例如，如果提供的标记 ID 适用于“Dog”， |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/delete | 取消发布特定的迭代。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/delete | 删除一组预测图像及其关联的预测结果。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action | 获取已发送到预测终结点的图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/image/action | 快速测试图像。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/url/action | 快速测试图像 URL。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/delete | 删除项目的标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/read | 获取有关特定标记的信息。 获取给定项目和迭代的标记。* |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/write | 更新标记。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action | 此 API 获取未标记图像数组/批的建议标记和区域，以及标记的置信度。 如果未找到标记，则返回空数组。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/train/advanced/action | 将要通过 PipelineConfiguration 和训练类型进行训练的项目排队。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/quota/delete | *NotDefined* |
> | .CognitiveServices/accounts/CustomVision/quota/refresh/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/usage/prediction/user/read | 获取 Oxford 用户的预测资源用量 |
> | Microsoft.CognitiveServices/accounts/CustomVision/usage/training/resource/tier/read | 获取 Azure 用户的训练资源用量 |
> | Microsoft.CognitiveServices/accounts/CustomVision/usage/training/user/read | 获取 Oxford 用户的训练资源用量 |
> | Microsoft.CognitiveServices/accounts/CustomVision/user/delete | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/user/state/write | 更新用户状态 |
> | Microsoft.CognitiveServices/accounts/CustomVision/user/tier/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/users/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/whitelist/delete | 删除具有特定功能的已加入允许列表的用户 |
> | Microsoft.CognitiveServices/accounts/CustomVision/whitelist/read | 获取具有特定功能的已加入允许列表的用户列表 |
> | Microsoft.CognitiveServices/accounts/CustomVision/whitelist/write | 在允许列表中更新或创建具有特定功能的用户 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/action | 创建新的语音终结点。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/action | LongAudio 和 AudioOnly 数据集将利用语音转文本的批量听录功能。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/action | 创建新的语音模型。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/evaluations/action | 创建新评估。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/projects/action | 新建一个项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/trainingsets/action | 创建新的训练集。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/api/common/v3.0-beta1/features/read | 获取允许的功能的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/delete | 删除具有给定 ID 的语音数据集。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/read | 获取一个或多个数据集。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/write | 更新由其 ID 标识的语音数据集的可变详细信息。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/upload/action | 上传数据并创建新的语音数据对象。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/audioonly/action | 上传只有音频的语音数据集。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/longaudio/action | 上传包含长音频和脚本的语音数据集。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/trainingset/action | 将数据导入分配到特定的训练集。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/files/read | 获取由指定的 ID 标识的数据集的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/locales/read | 获取支持自定义语音数据导入的区域设置的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/longaudiolocales/read | 获取支持导入长音频数据/仅音频数据的区域设置。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/personalvoicerecordingscript/read | 获取个人语音录制脚本。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/datasets/project/read | 获取指定项目的数据集的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/discount/read | 获取神经模型训练的折扣。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/delete | 删除指定的语音终结点。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/read | 获取一个或多个语音终结点 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/write | 更新由给定 ID 标识的终结点的名称和说明。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/partial/action | 在现有终结点上部署部分模型。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/operate/action | 禁用现有的语音终结点。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/locales/read | 获取支持自定义语音终结点的区域设置的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/manifest/read | 返回可在某个本地容器中使用的终结点清单。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/partial/read | 按模型 id 和部署 id 获取部分模型部署的详细信息。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/project/read | 获取指定项目的终结点的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/resume/write | 恢复现有的语音终结点。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/endpoints/suspend/write | 暂停现有的语音终结点。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/evaluations/delete | 删除指定的评估。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/evaluations/read | 获取一个或多个评估的详细信息 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/evaluations/audios/read | 获取指定评估的详细信息。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/evaluations/files/read | 获取由给定 ID 标识的评估的文件。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/evaluations/project/read | 获取指定项目的评估列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/evaluations/tests/delete | 删除指定的语音测试。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/evaluations/tests/batchdelete/action | 删除给定计算的指定语音测试。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/longaudiosynthesis/delete | 删除指定的长音频合成任务。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/longaudiosynthesis/read | 获取一个或多个长音频合成。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/longaudiosynthesis/write | 更新由其 ID 标识的长音频合成的可变详细信息。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/longaudiosynthesis/voices/read | 获取用于长音频合成的受支持语音列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/delete | 删除具有给定 id 的语音模型。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/read | 获取一个或多个语音模型。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/write | 更新由给定 ID 标识的语音模型的元数据。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/copy/action | 此方法可用于将模型从一个位置复制到另一个位置。 如果目标订阅 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/withtest/action | 创建新的语音模型对象。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/saveas/action | 此方法可用于复制具有新名称和说明的模型。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/evaluations/read | 获取指定模型的评估的详细信息。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/locales/read | 获取支持自定义语音模型的区域设置的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/locales/crosslingual/read | 获取支持自定义语音跨语言模型的区域设置的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/project/read | 获取指定项目的模型的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/models/recipes/read | 获取支持 customvoice 模型配方的区域设置的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/projects/delete | 删除由给定 ID 标识的项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/projects/read | 获取一个或多个项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/projects/write | 更新由给定 ID 标识的项目。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/projects/locales/read | 获取受支持的区域设置的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/speakerauthorizations/delete | 删除指定的扬声器授权。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/speakerauthorizations/read | 获取指定项目的扬声器授权的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/speakerauthorizations/write | 更新由其 ID 标识的语音扬声器授权的可变详细信息。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/speakerauthorizations/upload/action | 上传数据并创建新的语音扬声器授权对象。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/speakerauthorizations/project/read | 获取指定项目的扬声器授权的列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/speakerauthorizations/template/read | 获取给定区域设置的授权模板。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/trainingsets/delete | 删除指定的训练集。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/trainingsets/write | 更新现有的训练集。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/trainingsets/files/read | 获取由给定 ID 标识的训练集列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/trainingsets/project/read | 获取指定项目的训练集列表。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/trainingsets/utterance/delete | 删除指定的言语。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/trainingsets/utterances/batchdelete/action | 删除给定训练集的指定言语。 |
> | Microsoft.CognitiveServices/accounts/CustomVoice/trainingsets/utterances/read | 获取指定训练集的言语。 |
> | Microsoft.CognitiveServices/accounts/EntitySearch/search/action | 获取给定查询的实体和位置结果。 |
> | Microsoft.CognitiveServices/accounts/Face/detect/action | 检测图像中的人脸，返回人脸矩形以及可选的 faceId、地标和属性。 |
> | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | 给定查询人脸的 faceId，用于在 faceId 数组、人脸列表或大型人脸列表中搜索类似的人脸。 faceId |
> | Microsoft.CognitiveServices/accounts/Face/group/action | 根据人脸相似性将候选人脸划分为组。 |
> | Microsoft.CognitiveServices/accounts/Face/identify/action | 一对多的识别，用于在人员组或大型人员组中查找与特定查询人脸最接近的匹配项。 |
> | Microsoft.CognitiveServices/accounts/Face/verify/action | 验证两张人脸是否属于同一个人，或者一张人脸是否属于某一个人。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/write | 使用用户指定的 faceListId、名称和可选的 userData 创建空人脸列表。 最多允许 64 个人脸列表。更新人脸列表的信息，包括名称和 userData。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/delete | 删除指定的人脸列表。 也会删除该人脸列表中的相关人脸图像。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/read | 在人脸列表中检索人脸列表的 faceListId、名称、userData 和人脸。 列出人脸列表的 faceListId、名称和 userData。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | 将最多 1,000 张人脸添加到指定的人脸列表。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | 根据指定的 faceListId 和 persisitedFaceId 从人脸列表中删除人脸。 也会删除相关的人脸图像。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | 使用用户指定的 largeFaceListId、名称和可选的 userData 创建空的大型人脸列表。 更新大型人脸列表的信息，包括名称和 userData。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | 删除指定的大型人脸列表。 也会删除该大型人脸列表中的相关人脸图像。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | 检索大型人脸列表的 largeFaceListId、名称和 userData。 列出大型人脸列表的 largeFaceListId、名称和 userData 信息。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | 提交大型人脸列表训练任务。 训练是只有经过训练的大型人脸列表可以使用的关键步骤。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | 将最多 1,000,000 张人脸添加到指定的大型人脸列表。 根据指定人脸的 persistedFaceId 在大型人脸列表中更新该人脸的 userData 字段。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | 根据指定的 largeFaceListId 和 persisitedFaceId 从大型人脸列表中删除人脸。 也会删除相关的人脸图像。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | 根据 largeFaceListId 和 persistedFaceId 在大型人脸列表中检索持久性人脸。 列出指定大型人脸列表中人脸的 persistedFaceId 和 userData。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | 检查已完成或仍在进行的大型人脸列表训练状态。 LargeFaceList 训练是异步操作 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | 使用用户指定 largePersonGroupId、名称和可选的 userData 创建新的大型人员组。 更新现有大型人员组的名称和 userData。 如果属性不在请求正文中，则属性保持不变。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | 删除具有指定 personGroupId 的现有大型人员组。 将删除此大型人员组中的持久性数据。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | 检索大型人员组的信息，包括其名称和 userData。 此 API 返回大型用户组信息。列出所有现有大型人员组的 largePesonGroupId、名称和 userData。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | 提交大型人员组训练任务。 训练是只有经过训练的大型人员组可以使用的关键步骤。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | 在指定的大型人员组中创建新用户。 若要将人脸添加到此人员，请调用 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | 从大型人员组中删除现有人员。 将删除存储的所有人员数据，以及人员条目中的人脸图像。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | 检索某个人员的姓名和 userData，以及代表已注册人脸图像的持久性 faceId。 列出指定大型人员组中所有人员的信息，包括 personId、姓名、userData 和 persistedFaceIds。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | 更新某个人员的姓名或 userData。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | 将某个人员的人脸图像添加到大型人员组以进行人脸识别或验证。 用于处理“更新某个人员的持久性人脸的 userData 字段”的图像。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | 删除大型人员组中某个人员的人脸。 同时会删除与此人脸条目相关的人脸数据和图像。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | 检索人脸信息。 持久性人脸按其 largePersonGroupId、personId 和 persistedFaceId 指定。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | 检查已完成或仍在进行的大型人员组训练状态。 LargePersonGroup 训练是异步操作 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/write | 使用指定的 personGroupId、名称和用户提供的 userData 创建新的人员组。 更新现有人员组的名称和 userData。 如果属性不在请求正文中，则属性保持不变。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | 删除具有指定 personGroupId 的现有人员组。 将删除此人员组中的持久性数据。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/read | 检索人员组名称和 userData。 若要获取此 person Group 下的人员信息，请使用“列出人员组的 pesonGroupId、名称和 userData”。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | 提交人员组训练任务。 训练是只有经过训练的人员组可以使用的关键步骤。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | 在指定的人员组中创建新用户。 若要将人脸添加到此人员，请调用 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | 从人员组中删除现有人员。 将删除存储的所有人员数据，以及人员条目中的人脸图像。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | 检索某个人员的姓名和 userData，以及代表已注册人脸图像的持久性 faceId。 列出指定人员组中所有人员的信息，包括 personId、姓名、userData 和已注册人脸的 persistedFaceIds。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | 更新某个人员的姓名或 userData。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | 将某个人员的人脸图像添加到人员组以进行人脸识别或验证。 用于处理多个“更新某个人员的持久性人脸的 userData 字段”的图像。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | 删除人员组中某个人员的人脸。 同时会删除与此人脸条目相关的人脸数据和图像。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | 检索人脸信息。 持久性人脸按其 personGroupId、personId 和 persistedFaceId 指定。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | 检查已完成或仍在进行的人员组训练状态。 PersonGroup 训练是触发的异步操作 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/take/action | 为对象创建快照。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/read | 获取快照操作的状态。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/apply/action | 应用快照，并提供用户指定的对象 ID。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/delete | 删除快照。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/write | 更新快照的属性。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshots/read | 列出用户可访问的所有快照和信息。* |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/train/action | 创建和训练自定义模型。<br>训练请求必须包含源参数，该参数是外部可访问的 Azure 存储 Blob 容器 URI（最好是共享访问签名 URI），或者是本地装载的驱动器中某个数据文件夹的有效路径。<br>指定本地路径时，它们必须遵循 Linux/Unix 路径格式，并且是根为输入装入点配置的绝对路径。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/action | 创建和训练自定义模型。<br>请求必须包含源参数，该参数是外部可访问的 Azure 存储 Blob 容器 URI（最好是共享访问签名 URI），或者是本地装载的驱动器中某个数据文件夹的有效路径。<br>指定本地路径时，它们必须遵循 Linux/Unix 路径格式，并且是根植于输入装载配置设置值的绝对路径，例如，如果“{Mounts:Input}”配置设置值为“/input”，则有效的源路径为“/input/contosodataset”。<br>要训练的所有数据都应在源文件夹或其下的子文件夹下。<br>使用以下内容类型的文档训练模型 -“application/pdf”、“image/jpeg”、“image/png”、“image/tiff”。<br>忽略其他类型的内容。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyze/action | 从给定的文档中提取键值对。 输入文档必须是支持的内容类型之一：“application/pdf”、“image/jpeg”或“image/png”。 系统将返回 JSON 格式的成功响应， |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/delete | 删除模型项目。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/read | 获取有关模型的信息。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copyauthorization/action | 生成授权有效负载以在目标表单识别器资源上复制模型。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copy/action | 将自定义表单识别器模型从一个订阅复制到另一个订阅。<br>通过使用 `source=false` 查询的此 API 从目标终结点获取 `modelId` 令牌来启动该过程。<br>然后，在请求正文中传递 `modelId` 引用以及其他目标资源信息。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyze/action | 从给定文档中提取键值对、表和语义值。<br>输入文档必须是支持的内容类型之一：“application/pdf”、“image/jpeg”、“image/png”或“image/tiff”。<br>或者，使用“application/json”类型指定要分析的文档的 URL 位置。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copy/action | 将自定义表单识别器模型复制到目标表单识别器资源。 在调用此操作之前，必须首先获取授权以复制到  |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/delete | 标记要删除的模型。 模型项目将在 48 小时内永久删除。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copyauthorization/action | 为模型复制操作生成授权有效负载。 针对目标表单识别器资源终结点调用此操作  |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/read | 获取自定义模型的详细信息。 获取有关所有自定义模型的信息 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/compose/action | 组合请求将包括模型 ID 的列表。 它会验证通过标签模型或组合模型进行训练的所有模型。 它会验证放在一起的模型的限制。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyzeresults/read | 获取当前状态和分析表单操作的结果。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyzeresults/read | 获取当前状态和分析表单操作的结果。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copyresults/read | 获取当前状态和自定义表单模型复制操作的结果。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copyresults/read | 获取当前状态和自定义表单模型复制操作的结果。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/keys/read | 检索模型的密钥。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/layout/analyze/action | 从给定文档中提取文本和布局信息。<br>输入文档必须是支持的内容类型之一：“application/pdf”、“image/jpeg”、“image/png”或“image/tiff”。<br>或者，使用“application/json”类型指定要分析的文档的 URL 位置。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/layout/analyzeresults/read | 跟踪进度并获取分析布局操作的结果 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/businesscard/analyze/action | 从给定的名片文档中提取字段文本和语义值。  |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/businesscard/analyzeresults/read | 查询状态并检索“分析名片”操作的结果。 可从“分析名片”响应中的“Operation-Location”标头获取此接口的 URL。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/iddocument/analyze/action | 从给定的 ID 文档中提取字段文本和语义值。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/iddocument/analyzeresults/read | 查询“分析 ID”操作的状态并检索其结果。 可从“分析 ID”响应中的“Operation-Location”标头获取此接口的 URL。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/invoice/analyze/action | 从给定的发票单据中提取字段文本和语义值。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/invoice/analyzeresults/read | 查询“分析发票”操作的状态并检索其结果。 可从“分析发票”响应中的“Operation-Location”标头获取此接口的 URL。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/receipt/asyncbatchanalyze/action | 从给定的回执文档中提取字段文本和语义值。 输入文档必须是受支持的其中一项 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/receipt/analyzeresults/read | 查询状态并检索“分析回执”操作的结果。 可从“分析回执”响应中的“Operation-Location”标头获取此接口的 URL。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/receipt/operations/read | 查询状态并检索“分析回执”操作的结果。 可从“分析回执”响应中的“Operation-Location”标头获取此接口的 URL。 |
> | Microsoft.CognitiveServices/accounts/ImageSearch/details/action | 返回有关图像的见解，例如包含图像的网页。 |
> | Microsoft.CognitiveServices/accounts/ImageSearch/search/action | 获取给定查询的相关映像。 |
> | Microsoft.CognitiveServices/accounts/ImageSearch/trending/action | 获取当前热门的图像。 |
> | Microsoft.CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/action | 创建沉浸式阅读器会话 |
> | Microsoft.CognitiveServices/accounts/LUIS/predict/action | 获取给定查询的已发布终结点预测。 |
> | Microsoft.CognitiveServices/accounts/LUIS/unlabeled/action | 将未标记的数据追加到相应的应用程序 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/write | 创建新的 LUIS 应用。 更新应用程序的名称或说明。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/azureaccounts/action | 为应用程序分配一个 Azure 帐户。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/delete | 删除应用程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/read | 获取应用程序信息。 列出所有用户应用程序。 返回应用程序列表* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/import/action | 将应用程序导入 LUIS，该应用程序的 JSON 应包含在请求正文中。 返回新应用 ID。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/publish/action | 发布应用程序的特定版本。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/querylogsasync/action | 针对应用程序过去一个月的查询日志发起下载请求。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/move/action | 将应用移到其他 LUIS 创作 Azure 资源。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/subscriptions/action | 将订阅信息分配到指定的应用程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/unlabeled/action | 将未标记的数据从 csv 文件上传到应用程序 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/testdatasets/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/activeversion/write | 更新指定应用的当前活动版本 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/assistants/read | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/azureaccounts/read | 使用用户的 Azure 资源管理器令牌，获取已为该用户分配给应用程序的 LUIS Azure 帐户。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/azureaccounts/delete | 使用用户的 Azure 资源管理器令牌获取该用户的 LUIS Azure 帐户。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/cultures/read | 获取受支持的 LUIS 应用程序区域性。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/cultures/tokenizerversions/read | 获取 LUIS 应用程序区域性以及区域性支持的 tokenizer 版本。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/customprebuiltdomains/write | 添加预生成的域及其模型作为新的应用程序。 返回新应用 ID。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/customprebuiltdomains/read | 获取特定区域性的所有可用的自定义预生成域。获取所有区域性的所有可用的自定义预生成域 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/domains/read | 获取可用的应用程序域。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/endpoints/read | 返回可用的终结点部署区域和 URL |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/metadata/read | 获取应用程序元数据 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/metadata/write | 更新应用程序元数据 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/permissions/write | 将某个用户添加到有权访问此 LUIS 应用程序的用户的允许列表。 将当前用户访问列表替换为在正文中发送的访问列表。* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/permissions/read | 获取有权访问你的应用程序的用户电子邮件的列表。  |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/permissions/delete | 从有权访问此 LUIS 应用程序的用户的允许列表中删除某个用户。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/publishsettings/read | 获取应用程序的发布设置。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/publishsettings/write | 更新应用程序发布设置。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/querylogs/read | 获取应用程序过去一个月的查询日志。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/querylogsasync/read | 获取针对查询日志的下载请求的状态。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/runtimepermissions/bot/action | 向应用程序添加机器人运行时权限 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/runtimepermissions/bot/delete | 删除机器人运行时应用程序权限 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/runtimepermissions/bot/read | 获取应用程序的机器人运行时权限。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/settings/read | 获取应用程序设置 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/settings/write | 更新应用程序设置 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/slots/predict/action | 使用给定的查询获取指定槽的已发布预测。 当前最大查询大小为 500 个字符。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/slots/evaluations/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/slots/evaluations/result/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/slots/evaluations/status/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/slots/predict/read | 使用给定的查询获取指定槽的已发布预测。 当前最大查询大小为 500 个字符。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/stats/detailedendpointhitshistory/read | 获取给定时间范围内每日终结点命中数历史记录（包含槽和区域详细信息）。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/subscriptions/read | 返回为应用程序分配的订阅的信息 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/subscriptions/delete | 从为应用程序分配的订阅中删除具有指定 ID 的订阅 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/testdatasets/delete | 从给定的应用程序中删除给定的数据集。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/testdatasets/read | 获取给定的批处理测试元数据。 返回给定应用程序的所有批处理测试数据集的列表。* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/testdatasets/write | 更新给定应用程序的现有批处理测试数据集最近完成的测试结果。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/testdatasets/download/read | 下载具有给定 ID 的数据集。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/testdatasets/rename/write | 更新给定应用程序的现有批处理测试数据集的名称。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/usagescenarios/read | 获取应用程序的可用使用方案。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/train/action | 针对指定的 LUIS 应用程序的版本发送训练请求。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/clone/action | 创建与所选应用程序版本的当前快照等效的新应用程序版本。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/delete | 删除应用程序版本。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/read | 获取应用程序版本信息。 获取应用程序版本列表的信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/import/action | 将新版本导入 LUIS 应用程序，该版本的 JSON 应包含在请求正文中。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/write | 更新此应用程序版本的名称或说明。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/predict/action | 使用给定的查询获取指定应用程序版本的已发布预测。 当前最大查询大小为 500 个字符。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/evaluations/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/stats/action | 计算应用程序版本训练统计信息 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/releasedispatch/action | 发布要由调度应用程序使用的选定应用程序版本的新快照 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/assignedkey/write | **已弃用** |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/assignedkey/read | **已弃用** |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/write | 将列表实体添加到 LUIS 应用。 将一批子列表添加到现有的封闭式列表。*更新封闭式列表模型。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/delete | 从应用程序中删除封闭式列表实体。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/read | 获取封闭式列表模型的信息。 获取有关封闭式列表模型的信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/suggest/action | 建议对现有或新创建的封闭式列表使用新条目 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/presuggestion/read | 加载封闭式列表实体以前的建议结果。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/roles/write | 为封闭式列表实体模型添加角色。为封闭式列表实体模型更新角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/roles/delete | 删除封闭式列表实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/roles/read | 获取封闭式列表实体模型的角色。 获取封闭式列表实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/sublists/write | 向现有封闭式列表添加列表。更新封闭式列表的子列表之一 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/sublists/delete | 删除指定列表实体的子列表。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/write | 向应用程序添加复合实体提取程序。 更新复合实体提取程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/delete | 从此应用程序版本中删除复合实体提取程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/read | 获取有关复合实体模型的信息。 获取有关此应用程序版本的复合实体模型的信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/children/write | 在现有复合实体模型中添加单个子项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/children/delete | 从此应用程序版本中删除复合实体提取程序子项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/roles/write | 为复合实体模型添加角色。 为复合实体模型更新角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/roles/delete | 删除复合实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/roles/read | 获取复合实体模型的角色。 获取复合实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/connectedservices/write | 创建意向与服务之间的映射。更新意向与服务之间的映射* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/connectedservices/delete | 删除意向与服务之间的映射 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/connectedservices/read | 获取意向与服务之间的映射 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltdomains/write | 将可自定义的预生成域及其所有模型添加到此应用程序版本。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltdomains/delete | 从此应用程序版本中删除预生成域的模型。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/write | 向此应用程序版本添加自定义预生成域实体模型。 将[删除实体](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c1f)与实体 ID 配合使用以删除此实体。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/read | 获取此应用程序版本的所有自定义预生成域实体信息 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/roles/write | 为自定义预生成域实体模型添加角色。为自定义预生成域实体模型更新角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/roles/delete | 删除自定义预生成实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/roles/read | 获取自定义预生成域实体模型的角色。 获取自定义预生成域实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltintents/write | 向应用程序添加自定义预生成域意向模型。 将[删除意向](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c1c)与意向 ID 配合使用以删除此意向。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltintents/read | 获取此应用程序版本的自定义预生成意向信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltintentsbatch/write | 向应用程序批量添加自定义预生成域意向 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltmodels/read | 获取此应用程序版本的所有自定义预生成域模型信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/detailedmodels/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/write | 向此应用程序版本添加简单实体提取程序。 更新实体提取程序的名称。 更新实体提取程序。* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/delete | 从此应用程序版本中删除简单实体提取程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/read | 获取有关简单实体模型的信息。 获取此应用程序版本中的简单实体模型的相关信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/children/write | 在现有实体模型中创建单个子项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/features/write | 为实体模型添加特征关系。更新实体的特征关系列表* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/features/delete | 删除实体模型的特征关系。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/features/read | 获取实体模型的特征关系。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/roles/write | 为简单实体模型添加角色。更新简单实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/roles/delete | 删除简单实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/roles/read | 获取简单实体模型的角色。 获取简单实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/roles/suggest/read | 建议使用会提高实体模型准确性的示例。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/stats/endpointscores/read | 获取实体模型被评为首要意向的次数 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/suggest/read | 建议使用会提高实体模型准确性的示例。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/evaluations/result/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/evaluations/status/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/example/write | 向此应用程序版本添加标记的示例。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/examples/write | 将一批非重复的已标记示例添加到指定的应用程序。 批不能包含分层子实体。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/examples/delete | 删除具有指定 ID 的标签。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/examples/read | 返回要查看的终结点示例的子集。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/export/read | 将 LUIS 应用程序版本导出为 JSON 格式。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/externalkeys/delete | 此 API 已弃用。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/externalkeys/read | **已弃用** |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/externalkeys/write | **已弃用** |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/features/read | 获取所有应用程序版本功能。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/featuresuggestion/status/read | 获取应用程序版本功能建议状态 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/featuresuggestion/suggestions/read | 获取应用程序版本功能建议 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/write | 向此应用程序版本添加分层实体提取程序。 更新分层实体提取程序模型的名称和子项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/delete | 从此应用程序版本中删除分层实体提取程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/read | 获取有关分层实体模型的信息。 获取有关此应用程序版本中的分层实体模型的信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/children/write | 在现有分层实体模型中创建单个子项。 在现有分层实体模型中重命名单个子项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/children/delete | 从此应用程序版本中删除分层实体提取程序子项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/children/read | 获取有关分层实体子项模型的信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/roles/write | 为分层实体模型添加角色。为分层实体模型更新角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/roles/delete | 删除分层实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/roles/read | 获取分层实体模型的角色。 获取分层实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/write | 向此应用程序版本添加意向分类器。 更新意向分类器的名称。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/delete | 从此应用程序版本中删除意向分类器。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/read | 获取有关意向模型的信息。 获取此应用程序版本中的意向模型的相关信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/entitiescount/read | 获取给定应用中给定任务的给定意向的已标记言语的实体计数。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/features/write | 为意向模型添加特征关系。更新意向的特征关系列表* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/features/delete | 删除意向模型的特征关系。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/features/read | 获取意向模型的特征关系。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/patternrules/read | 获取特定意向的模式。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/stats/read | 获取每个意向的应用程序版本训练统计信息 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/stats/endpointscores/read | 获取意向模型被评为首要意向的次数 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/suggest/read | 建议使用会提高意向模型准确性的示例。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/labeleddata/read | 获取指定应用程序的标记数据 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/listprebuilts/read | 基于应用程序的区域性获取应用程序的所有可用的预生成实体。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/models/read | 获取有关应用程序版本模型的信息。 获取有关模型的信息。* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/models/endpointscoreshistory/read | 获取给定时间范围内意向模型被评为首要意向历史记录的次数 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/models/examples/read | 获取模型示例列表。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/models/review/read | 获取给定应用中给定任务的给定模型的已标记言语。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/models/reviewlabels/read | 获取给定应用中给定任务的给定模型的已标记言语。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/models/reviewpredictions/read | 获取给定应用中给定任务的给定模型的已标记言语。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/write | 向此应用程序版本添加 Pattern.any 实体提取程序。 更新 Pattern.any 实体提取程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/delete | 从此应用程序版本中删除 Pattern.any 实体提取程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/read | 获取有关 Pattern.any 实体模型的信息。 获取此应用程序版本中的 Pattern.any 实体模型的相关信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/explicitlist/write | 向 Pattern.any 显式列表添加项。 更新 Pattern.any 实体模型的显式列表项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/explicitlist/delete | 从 Pattern.any 显式列表中删除项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/explicitlist/read | 获取 Pattern.any 实体模型的显式列表。 获取 Pattern.any 实体模型的显式列表项。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/roles/write | 为 Pattern.any 实体模型添加角色。为 Pattern.any 实体模型更新角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/roles/delete | 删除 Pattern.any 实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/roles/read | 获取 Pattern.any 实体模型的角色。 获取 Pattern.any 实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternrule/write | 向指定的应用程序版本添加模式。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternrules/write | 向此应用程序版本添加模式列表。 更新此应用程序版本中的模式。 更新此应用程序版本中的模式列表。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternrules/delete | 从此应用程序版本中删除模式列表。 从此应用程序版本中删除模式。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternrules/read | 获取此应用程序版本中的模式。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patterns/write | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patterns/delete | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patterns/read | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/phraselists/write | 创建新的短语列表功能。 更新短语列表功能的短语、状态和名称。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/phraselists/delete | 从应用程序中删除短语列表功能。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/phraselists/read | 获取短语列表功能信息。 获取此应用程序版本的所有短语列表功能。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/phraselists/suggest/action | 建议对现有或新创建的短语列表使用新条目 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/phraselists/presuggestion/read | 加载短语列表功能以前的建议结果。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/write | 向此应用程序版本添加预生成实体提取程序的列表。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/delete | 从此应用程序版本中删除预生成实体提取程序。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/read | 获取有关预生成实体模型的信息。 获取此应用程序版本中的预生成实体模型的相关信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/roles/write | 为预生成实体模型添加角色。为预生成实体模型更新角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/roles/delete | 删除预生成实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/roles/read | 获取预生成实体模型的角色。 获取预生成实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/predict/read | 使用给定的查询获取指定应用程序版本的已发布预测。 当前最大查询大小为 500 个字符。 获取给定言语的预测（意向/实体）。* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/write | 向此应用程序版本添加正则表达式实体提取程序。 更新正则表达式实体模型。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/delete | 从此应用程序版本中删除正则表达式实体模型。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/read | 获取有关正则表达式实体模型的信息。 获取有关此应用程序版本中的正则表达式实体模型的信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/roles/write | 为正则表达式实体模型添加角色。为正则表达式实体模型更新角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/roles/delete | 删除正则表达式实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/roles/read | 获取正则表达式实体模型的角色。 获取正则表达式实体模型的角色。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/settings/read | 获取应用程序版本设置。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/settings/write | 更新应用程序版本设置。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/state/read | 获取一个标志，该标志指示应用版本以前是否受过训练 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/stats/read | 获取应用程序版本训练统计信息 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/stats/endpointhitshistory/read | 获取给定时间范围内每日终结点命中数历史记录。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/stats/examplesperentity/read | 获取给定应用程序的每个实体的示例数 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/stats/labelsperentity/read | 获取给定应用程序的每个实体的标签数 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/stats/labelsperintent/read | 获取给定应用程序的每个意向的标签数 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/stats/operations/read | 获取应用程序版本训练统计信息未过期的操作信息。获取应用程序版本训练统计信息未过期的操作* |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/statsmetadata/read | 获取应用程序版本训练统计信息元数据 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/suggest/delete | 删除终结点言语。 此言语出现在“评审终结点言语”列表中。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/testdatasets/run/read | 运行由给定的应用程序 ID 和数据集 ID 给出的批处理测试 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/train/read | 获取指定应用程序版本的所有模型（意向和实体）的训练状态。 在调用此 API 以获取训练状态之前，必须调用训练 API 来训练 LUIS 应用。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/trainingstatus/read | 获取一个标志，该标志指示应用版本以前是否受过训练 |
> | Microsoft.CognitiveServices/accounts/LUIS/azureaccounts/read | 使用用户的 Azure 资源管理器令牌获取该用户的 LUIS Azure 帐户。 |
> | Microsoft.CognitiveServices/accounts/LUIS/compositesmigration/apps/versions/migrate/action | 迁移应用程序版本的复合实体 |
> | Microsoft.CognitiveServices/accounts/LUIS/compositesmigration/apps/versions/operations/migrate/read | 获取复合迁移结果 |
> | Microsoft.CognitiveServices/accounts/LUIS/compositesmigration/apps/versions/operations/migrate/status/read | 获取复合迁移操作状态 |
> | Microsoft.CognitiveServices/accounts/LUIS/compositesmigration/needmigrationapps/read | 获取需要复合迁移的应用程序 |
> | Microsoft.CognitiveServices/accounts/LUIS/conversations/previewfeatures/read | 获取当前所有者的预览功能的资格状态。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/classifierslabelscount/read | 获取每个分类器的标签计数 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/documents/models/trellis/action | 发布网格提取操作 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/documents/models/operations/trellis/read | 获取网格提取操作响应 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/documents/models/operations/trellis/status/read | 获取网格提取操作状态 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/extractorslabelscount/read | 获取每个提取程序的标签计数 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/modeltracker/action | 发布模型跟踪器操作发起请求 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/predict/action | 触发器文档预测操作 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/predicttext/action | 触发器文档文本预测操作 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/clone/action | 在相同的应用版本中克隆模型。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/operations/modeltracker/read | 获取模型跟踪器操作响应 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/operations/modeltracker/metadata/read | 获取模型跟踪器操作元数据响应 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/operations/modeltracker/status/read | 获取模型跟踪器操作状态 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/operations/predict/read | 获取文档预测操作响应 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/operations/predict/status/read | 获取文档预测操作状态 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/operations/predicttext/read | 获取文档文本预测操作响应 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/models/operations/predicttext/status/read | 获取文档文本预测操作状态 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/apps/operations/read | 获取应用程序的操作 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/entity/exploratorysuggestions/suggestions/action | 获取特定模型的探索性实体建议。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/entity/exploratorysuggestions/results/read | 获取特定模型的前 N 个探索性实体建议。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/entity/exploratorysuggestions/status/read | 获取特定模型的探索性实体建议处理状态。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/entitysuggestions/suggestions/action | 获取特定模型的建议。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/entitysuggestions/results/read | 获取特定模型的建议。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/entitysuggestions/status/read | 获取特定模型的建议处理状态。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/featurerepositorysearch/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/featurerepositorystore/features/data/read | 访问特定流。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/featurerepositorystore/manifest/read | 获取全局存储的清单对象。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/read | 获取存储帐户容器的信息。 获取以前注册的属于存储帐户的容器。* |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/status/action | 获取指定存储帐户容器的进度。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/prepare/action | 对准备要在文档应用程序中使用的存储帐户容器的请求进行排队。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/sync/action | 对更新已准备好的存储帐户容器的请求进行排队。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/write | 更新存储帐户信息。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/operations/prepare/status/read | 获取存储帐户容器准备操作的状态。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/operations/sync/status/read | 获取存储帐户容器更新操作的状态。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/prepare/result/read | 获取最新准备操作的结果。 |
> | Microsoft.CognitiveServices/accounts/LUIS/documents/storageaccounts/containers/sync/result/read | 获取最新同步操作的结果。 |
> | Microsoft.CognitiveServices/accounts/LUIS/externalkeys/write | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/externalkeys/delete | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/externalkeys/read | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/package/slot/gzip/read | 将发布的 LUIS 应用程序打包为 GZip |
> | Microsoft.CognitiveServices/accounts/LUIS/package/versions/gzip/read | 将训练的 LUIS 应用程序打包为 GZip |
> | Microsoft.CognitiveServices/accounts/LUIS/ping/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/previewfeatures/read | 获取当前所有者的预览功能的资格状态。 |
> | Microsoft.CognitiveServices/accounts/LUIS/programmatickey/write | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/resources/apps/count/read | 获取用户拥有的应用程序的数目。 |
> | Microsoft.CognitiveServices/accounts/LUIS/resources/apps/versions/count/read | 获取给定应用程序的版本数。 |
> | Microsoft.CognitiveServices/accounts/LUIS/subscriptions/write | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/subscriptions/delete | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/subscriptions/read | 已弃用此 API。 |
> | Microsoft.CognitiveServices/accounts/LUIS/user/termsofuse/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/user/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/user/delete | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/user/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/user/authoringazureaccount/write | 将用户的 APIM 创作密钥迁移到 Azure 资源。 |
> | Microsoft.CognitiveServices/accounts/LUIS/user/collaborators/read | 获取用户具有协作关系的所有应用中每个应用的用户数。 |
> | Microsoft.CognitiveServices/accounts/LUIS/user/detailedinfo/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/user/programmatickey/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/user/programmatickeywithendpointurl/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/LUIS/user/unownedappsowners/read | 获取用户在其上进行协作的应用的所有者。 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/write | 创建或更新异常警报配置 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/delete | 删除异常警报配置 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/read | 查询单个异常警报配置 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/alerts/query/action | 查询异常警报配置下的警报 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/alerts/anomalies/read | 查询特定警报下的异常 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/alerts/incidents/read | 查询特定警报下的事件 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/credentials/write | 创建或更新新的数据源凭据 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/credentials/delete | 删除数据源凭据 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/credentials/read | 获取数据源凭据或列出所有凭据 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/write | 创建或更新数据馈送。 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/delete | 删除数据馈送 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/read | 按 ID 获取数据馈送或列出所有数据馈送 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionprogress/read | 按数据馈送获取数据上次成功引入作业的时间戳 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionprogress/reset/action | 按数据馈送重置数据引入状态以回填数据 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionstatus/query/action | 按数据馈送获取数据引入状态 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/write | 创建或更新异常情况检测配置 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/delete | 删除异常情况检测配置 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/read | 查询单个异常情况检测配置 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/alert/anomaly/configurations/read | 查询特定异常情况检测配置的所有异常情况警报配置 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/anomalies/query/action | 查询异常情况检测配置下的异常情况 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/anomalies/dimension/query/action | 查询异常情况的维度值 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/incidents/query/action | 查询异常情况检测配置下的事件 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/incidents/rootcause/read | 查询事件的根本原因 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/series/query/action | 查询由异常情况检测扩充的序列 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/feedback/metric/write | 创建新的指标反馈 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/feedback/metric/read | 按 ID 获取指标反馈 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/feedback/metric/query/action | 列出有关给定指标的反馈 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/hooks/write | 创建或更新挂钩 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/hooks/delete | 删除挂钩 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/hooks/read | 按 ID 获取挂钩或列出所有挂钩 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/data/query/action | 从指标获取时序数据 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/dimension/query/action | 列出特定指标中的维度 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/enrichment/anomalydetection/configurations/read | 查询特定指标的所有异常情况检测配置 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/series/query/action | 列出指标中的序列（维度组合） |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/status/enrichment/anomalydetection/query/action | 查询异常情况检测状态 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/stats/latest/read | 获取最新使用情况统计信息 |
> | Microsoft.CognitiveServices/accounts/NewsSearch/categorysearch/action | 返回所提供类别的新闻。 |
> | Microsoft.CognitiveServices/accounts/NewsSearch/search/action | 获取与给定查询相关的新闻文章。 |
> | Microsoft.CognitiveServices/accounts/NewsSearch/trendingtopics/action | 获取通过必应确定的热门主题。 这些主题与必应主页底部的横幅中显示的主题相同。 |
> | Microsoft.CognitiveServices/accounts/OpenAI/engines/read | 读取引擎信息。 |
> | Microsoft.CognitiveServices/accounts/OpenAI/engines/completions/action | 从所选模型创建完成 |
> | Microsoft.CognitiveServices/accounts/OpenAI/engines/search/action | 使用当前引擎搜索最相关的文档。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/rank/action | 个性化排名请求。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/evaluations/action | 提交新评估。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/client/action | 获取客户端配置。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/policy/delete | 删除当前策略。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/policy/read | 获取策略配置。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/policy/write | 更新策略配置。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/service/read | 获取服务配置。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/service/write | 更新服务配置。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/evaluations/delete | 删除与 ID 关联的评估。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/evaluations/read | 获取与 ID 关联的评估。 列出所有已提交的评估。* |
> | Microsoft.CognitiveServices/accounts/Personalizer/events/reward/action | 报告对于所指定事件的排名最高的操作，要为其分配的奖励。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/events/activate/action | 报告指定的事件已实际显示给用户，并且这应当获得奖励。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/logs/delete | 删除所有日志。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/logs/properties/read | 获取日志属性。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/model/read | 获取当前模型。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/model/delete | 重置模型。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/model/properties/read | 获取模型属性。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/status/read | 获取操作的状态。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/root/action | QnA Maker |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/root/action | QnA Maker |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read | 从运行时下载更改。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write | 替换更改数据。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read | 获取终结点的终结点密钥 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | 重新生成终结点密钥。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read | 获取终结点的终结点设置 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write | 更新终结点的终结点设置。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/publish/action | 将知识库测试索引中的所有更改发布到其生产索引。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/delete | 删除知识库及其所有数据。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read | 获取知识库列表或特定知识库的详细信息。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write | 用于修改知识库或替换知识库内容的异步操作。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | 用于查询知识库的 GenerateAnswer 调用。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action | 用于将建议添加到知识库的 Train 调用。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write | 用于创建新知识库的异步操作。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read | 下载知识库。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read | 获取特定的长时间运行的操作的详细信息。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/QnaMaker/generateanswer/action | 用于查询给定段落或文档的 GenerateAnswer 调用 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read | 从运行时下载更改。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write | 替换更改数据。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read | 获取终结点的终结点密钥 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action | 重新生成终结点密钥。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read | 获取终结点的终结点设置 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write | 更新终结点的终结点设置。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/publish/action | 将知识库测试索引中的所有更改发布到其生产索引。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/delete | 删除知识库及其所有数据。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read | 获取知识库列表或特定知识库的详细信息。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write | 用于修改知识库或替换知识库内容的异步操作。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action | 用于查询知识库的 GenerateAnswer 调用。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action | 用于将建议添加到知识库的 Train 调用。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write | 用于创建新知识库的异步操作。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read | 下载知识库。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/operations/read | 获取特定的长时间运行的操作的详细信息。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/models/action | 此方法可用于将模型从一个位置复制到另一个位置。 如果目标订阅 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/webhooks/action | Webhook 操作 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/datasets/write | 创建或更新数据集 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/datasets/delete | 删除数据集 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/datasets/read | 获取一个或多个数据集 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/datasets/files/read | 获取一个或多个数据集文件 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/endpoints/write | 创建或更新终结点 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/endpoints/delete | 删除终结点 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/endpoints/read | 获取一个或多个终结点 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/endpoints/files/logs/write | 创建终结点数据导出 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/endpoints/files/logs/delete | 删除部分或全部自定义模型终结点日志 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/endpoints/files/logs/read | 获取一个或多个自定义模型终结点日志 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/evaluations/write | 创建或更新评估 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/evaluations/delete | 删除评估 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/evaluations/read | 获取一个或多个评估 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/evaluations/files/read | 获取一个或多个评估文件 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/healthstatus/read | 获取运行状况 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/models/write | 创建或更新模型。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/models/delete | 删除模型 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/models/read | 获取一个或多个模型 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/models/base/manifest/read | 返回此基础模型的清单，该清单可用于本地容器。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/models/manifest/read | 返回此模型的清单，该清单可用于本地容器。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/projects/write | 创建或更新项目 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/projects/delete | 删除项目 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/projects/read | 获取一个或多个项目 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/transcriptions/write | 创建或更新听录 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/transcriptions/delete | 删除听录 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/transcriptions/read | 获取一个或多个听录 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/transcriptions/files/read | 获取一个或多个听录文件 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/webhooks/write | 创建或更新 Webhook |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/webhooks/delete | 删除 Webhook |
> | Microsoft.CognitiveServices/accounts/SpeechServices/speechrest/webhooks/read | 获取一个或多个 Webhook |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-dependent/phrases/read | 检索特定区域设置的受支持密码列表。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-dependent/profiles/write | 创建具有指定区域设置的新演讲者配置文件。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-dependent/profiles/delete | 删除现有配置文件。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-dependent/profiles/read | 检索一组配置文件或按 ID 检索单个配置文件。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-dependent/profiles/verify/action | 针对输入音频验证现有配置文件。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-dependent/profiles/enrollments/write | 向现有配置文件添加注册。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-dependent/profiles/reset/write | 将现有配置文件重置为其原始创建状态。 重置操作会执行以下操作： |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-independent/phrases/read | 检索特定区域设置的受支持密码列表。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-independent/profiles/write | 创建具有指定区域设置的新演讲者配置文件。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-independent/profiles/delete | 删除现有配置文件。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-independent/profiles/identifysinglespeaker/action | 在候选人配置文件列表中标识在输入音频中讲话的人。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-independent/profiles/read | 检索一组配置文件或按 ID 检索单个配置文件。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-independent/profiles/verify/action | 针对输入音频验证现有配置文件。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-independent/profiles/enrollments/write | 向现有配置文件添加注册。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/text-independent/profiles/reset/write | 将现有配置文件重置为其原始创建状态。 重置操作会执行以下操作： |
> | Microsoft.CognitiveServices/accounts/SpeechServices/unified-speech/frontend/action | 此终结点管理 Speech Frontend |
> | Microsoft.CognitiveServices/accounts/SpeechServices/unified-speech/management/action | 此终结点管理 Speech Frontend |
> | Microsoft.CognitiveServices/accounts/SpeechServices/unified-speech/probes/action | 此终结点监视 Speech Frontend 健康状况 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/unified-speech/languages/action | 此终结点提供 REST 语言 API。 |
> | Microsoft.CognitiveServices/accounts/SpeechServices/unified-speech/legacy/query/action | 语音服务旧版 REST API。 |
> | Microsoft.CognitiveServices/accounts/SpellCheck/spellcheck/action | 通过 GET 或 POST 获取拼写检查查询的结果。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | 该 API 返回检测到的语言以及介于 0 与 1 之间的数字评分。 接近 1 的分数表示 100% 确定所识别的语言是真实的。 共支持 120 种语言。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | 该 API 返回给定文档中已知实体和常规命名实体（\"Person\"、\"Location\"、\"Organization\" 等）的列表。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | API 返回表示输入文本中的关键话题的字符串列表。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | 该 API 返回介于 0 与 1 之间的数字评分。<br>接近 1 的分数表示积极情绪，而接近 0 的分数表示消极情绪。<br>评分 0.5 表示缺少情绪（例如<br>factoid 语句）。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/analyze/action | 提交用于分析的文本文档的集合。 指定一个或多个要执行的唯一任务。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/action | QnA Maker |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/generateanswer/action | 用于查询给定段落或文档的 GenerateAnswer 调用 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/alterations/read | 从运行时下载更改。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/alterations/write | 替换更改数据。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/endpointkeys/read | 获取终结点的终结点密钥 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/endpointkeys/refreshkeys/action | 重新生成终结点密钥。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/endpointsettings/read | 获取终结点的终结点设置 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/endpointsettings/write | 更新终结点的终结点设置。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/knowledgebases/publish/action | 将知识库测试索引中的所有更改发布到其生产索引。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/knowledgebases/delete | 删除知识库及其所有数据。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/knowledgebases/read | 获取知识库列表或特定知识库的详细信息。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/knowledgebases/write | 用于修改知识库或替换知识库内容的异步操作。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/knowledgebases/generateanswer/action | 用于查询知识库的 GenerateAnswer 调用。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/knowledgebases/train/action | 用于将建议添加到知识库的 Train 调用。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/knowledgebases/create/write | 用于创建新知识库的异步操作。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/knowledgebases/download/read | 下载知识库。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/QnaMaker/operations/read | 获取特定的长时间运行的操作的详细信息。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/detect/action | 标识一段文本的语言。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/breaksentence/action | 标识文本段中的句子边界的位置。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/translate/action | 翻译文本。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/transliterate/action | 将一种语言的文本从一个脚本转换为另一个脚本。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/batches/delete | 取消当前正在处理的或已排队的文档翻译请求。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/batches/read | 根据特定文档翻译请求的 ID 获取其状态，或获取所有已提交的文档翻译请求的状态 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/batches/write | 将批量（批处理）翻译请求提交到文档翻译服务。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/batches/documents/read | 根据请求 ID 和文档 ID 获取特定文档的翻译状态，或获取文档翻译请求中所有文档的状态。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/dictionary/examples/action | 提供示例，说明如何在上下文中使用字典中的术语。 此操作与字典查找一起使用。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/dictionary/lookup/action | 为某个单词和少量的惯用语提供替代翻译。<br>每条翻译都包含语性和回译列表。<br>回译可让用户在语境中理解该翻译。<br>使用字典示例操作可以进一步向下钻取，以查看每个翻译对的用法示例。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/documents/formats/read | 列出文档翻译服务支持的文档格式。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/glossaries/formats/read | 列出文档翻译服务支持的术语表格式。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/languages/read | 获取文本翻译 API 的其他操作当前支持的语言集。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/storagesources/read | 列出文档翻译服务支持的存储源/选项。 |
> | Microsoft.CognitiveServices/accounts/VideoSearch/trending/action | 获取当前热门的视频。 |
> | Microsoft.CognitiveServices/accounts/VideoSearch/details/action | 获取与视频有关的见解（如相关视频）。 |
> | Microsoft.CognitiveServices/accounts/VideoSearch/search/action | 获取与给定查询相关的视频。 |
> | Microsoft.CognitiveServices/accounts/VisualSearch/search/action | 返回一个列表，其中的标记与提供的图像相关 |
> | Microsoft.CognitiveServices/accounts/WebSearch/search/action | 获取给定查询的 Web、图像、新闻和视频结果。 |

### <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

Azure 服务：[机器学习工作室（经典）](../machine-learning/classic/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.MachineLearning/register/action | 注册机器学习 Web 服务资源提供程序的订阅，并启用 Web 服务的创建。 |
> | Microsoft.MachineLearning/webServices/action | 为受支持的区域创建区域 Web 服务属性 |
> | Microsoft.MachineLearning/commitmentPlans/read | 读取任何机器学习承诺计划 |
> | Microsoft.MachineLearning/commitmentPlans/write | 创建或更新任何机器学习承诺计划 |
> | Microsoft.MachineLearning/commitmentPlans/delete | 删除任何机器学习承诺计划 |
> | Microsoft.MachineLearning/commitmentPlans/join/action | 加入任何机器学习承诺计划 |
> | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | 读取任何机器学习承诺计划关联 |
> | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | 移动任何机器学习承诺计划关联 |
> | Microsoft.MachineLearning/locations/operationresults/read | 获取机器学习操作的结果 |
> | Microsoft.MachineLearning/locations/operationsstatus/read | 获取正在进行的机器学习操作的状态 |
> | Microsoft.MachineLearning/operations/read | 获取机器学习操作 |
> | Microsoft.MachineLearning/skus/read | 获取机器学习承诺计划 SKU |
> | Microsoft.MachineLearning/webServices/read | 读取任何机器学习 Web 服务 |
> | Microsoft.MachineLearning/webServices/write | 创建或更新任何机器学习 Web 服务 |
> | Microsoft.MachineLearning/webServices/delete | 删除任何机器学习 Web 服务 |
> | Microsoft.MachineLearning/webServices/listkeys/read | 获取机器学习 Web 服务的密钥 |
> | Microsoft.MachineLearning/Workspaces/read | 读取任何机器学习工作区 |
> | Microsoft.MachineLearning/Workspaces/write | 创建或更新任何机器学习工作区 |
> | Microsoft.MachineLearning/Workspaces/delete | 删除任何机器学习工作区 |
> | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | 列出机器学习工作区的密钥 |
> | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | 重新同步针对机器学习工作区配置的存储帐户密钥 |

### <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

Azure 服务：[机器学习服务](../machine-learning/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.MachineLearningServices/register/action | 注册机器学习服务资源提供程序的订阅。 |
> | Microsoft.MachineLearningServices/locations/deleteVirtualNetworkOrSubnets/action | 删除对与机器学习服务工作区关联的虚拟网络/子网的引用。 |
> | Microsoft.MachineLearningServices/locations/updateQuotas/action | 在订阅或工作区级别更新每个 VM 系列的配额。 |
> | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | 获取特定计算操作的状态 |
> | Microsoft.MachineLearningServices/locations/quotas/read | 基于 VMFamily 获取当前分配的工作区配额。 |
> | Microsoft.MachineLearningServices/locations/usages/read | 某个订阅中 aml 计算资源的使用情况报告 |
> | Microsoft.MachineLearningServices/locations/vmsizes/read | 获取支持的 VM 大小 |
> | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | 获取特定工作区操作的状态 |
> | Microsoft.MachineLearningServices/operations/read | 获取机器学习服务资源提供程序的所有操作 |
> | Microsoft.MachineLearningServices/virtualclusters/read | 获取机器学习服务虚拟群集 |
> | Microsoft.MachineLearningServices/virtualclusters/write | 创建或更新机器学习服务虚拟群集 |
> | Microsoft.MachineLearningServices/virtualclusters/delete | 删除机器学习服务虚拟群集 |
> | Microsoft.MachineLearningServices/virtualclusters/jobs/submit/action | 将作业提交到机器学习服务虚拟群集 |
> | Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action | 检查机器学习服务工作区的批处理终结点中的计算名称 |
> | Microsoft.MachineLearningServices/workspaces/read | 获取机器学习服务工作区 |
> | Microsoft.MachineLearningServices/workspaces/write | 创建或更新机器学习服务工作区 |
> | Microsoft.MachineLearningServices/workspaces/delete | 删除机器学习服务工作区 |
> | Microsoft.MachineLearningServices/workspaces/listKeys/action | 列出机器学习服务工作区的机密 |
> | Microsoft.MachineLearningServices/workspaces/resynckeys/action | 重新同步机器学习服务工作区的机密 |
> | Microsoft.MachineLearningServices/workspaces/listStorageAccountKeys/action | 列出机器学习服务工作区的存储帐户密钥 |
> | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionsApproval/action | 批准或拒绝到 Microsoft.Network 提供程序的专用终结点资源的连接 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/read | 获取机器学习服务工作区中的批推理终结点 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/write | 在机器学习服务工作区中创建或更新批推理终结点 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/delete | 删除机器学习服务工作区中的批推理终结点 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/listKeys/action | 列出机器学习服务工作区中的批推理终结点的密钥 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/checkNameAvailability/read | 检查机器学习服务工作区中的批推理终结点的名称 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/read | 获取机器学习服务工作区中的批推理终结点的部署 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/write | 在机器学习服务工作区的批推理终结点中创建或更新部署 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/delete | 删除机器学习服务工作区中的批推理终结点的部署 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/checkNameAvailability/read | 检查机器学习服务工作区的批推理终结点中的部署的名称 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/jobs/read | 读取机器学习服务工作区中的批处理推理部署中的作业 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/jobs/write | 创建或更新机器学习服务工作区中的批处理推理部署中的作业 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/jobs/delete | 删除机器学习服务工作区中的批处理推理部署中的作业 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/jobs/read | 读取机器学习服务工作区中的批处理推理终结点中的作业 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/jobs/write | 创建或更新机器学习服务工作区中的批处理推理终结点中的作业 |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/jobs/delete | 删除机器学习服务工作区中的批处理推理终结点中的作业 |
> | Microsoft.MachineLearningServices/workspaces/codes/read | 读取机器学习服务工作区中的代码 |
> | Microsoft.MachineLearningServices/workspaces/codes/write | 创建或更新机器学习服务工作区中的代码 |
> | Microsoft.MachineLearningServices/workspaces/codes/delete | 删除机器学习服务工作区中的代码 |
> | Microsoft.MachineLearningServices/workspaces/codes/versions/read | 读取机器学习服务工作区中的代码版本 |
> | Microsoft.MachineLearningServices/workspaces/codes/versions/write | 创建或更新机器学习服务工作区中的代码版本 |
> | Microsoft.MachineLearningServices/workspaces/codes/versions/delete | 删除机器学习服务工作区中的代码版本 |
> | Microsoft.MachineLearningServices/workspaces/computes/read | 获取机器学习服务工作区中的计算资源 |
> | Microsoft.MachineLearningServices/workspaces/computes/write | 在机器学习服务工作区中创建或更新计算资源 |
> | Microsoft.MachineLearningServices/workspaces/computes/delete | 删除机器学习服务工作区中的计算资源 |
> | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | 列出机器学习服务工作区中的计算资源的机密 |
> | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | 列出机器学习服务工作区中的计算资源的节点 |
> | Microsoft.MachineLearningServices/workspaces/computes/start/action | 启动机器学习服务工作区中的计算资源 |
> | Microsoft.MachineLearningServices/workspaces/computes/stop/action | 停止机器学习服务工作区中的计算资源 |
> | Microsoft.MachineLearningServices/workspaces/computes/restart/action | 重启机器学习服务工作区中的计算资源 |
> | Microsoft.MachineLearningServices/workspaces/computes/updateDataMounts/action | 更新机器学习服务工作区中的计算数据装载 |
> | Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action | 访问机器学习服务工作区中的计算资源 |
> | Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action | 编辑计算开始/停止计划 |
> | Microsoft.MachineLearningServices/workspaces/connections/read | 获取机器学习服务工作区连接 |
> | Microsoft.MachineLearningServices/workspaces/connections/write | 创建或更新机器学习服务工作区连接 |
> | Microsoft.MachineLearningServices/workspaces/connections/delete | 删除机器学习服务工作区连接 |
> | Microsoft.MachineLearningServices/workspaces/data/read | 读取机器学习服务工作区中的数据容器 |
> | Microsoft.MachineLearningServices/workspaces/data/delete | 删除机器学习服务工作区中的数据容器 |
> | Microsoft.MachineLearningServices/workspaces/data/versions/read | 读取机器学习服务工作区中的数据版本 |
> | Microsoft.MachineLearningServices/workspaces/data/versions/write | 创建或更新机器学习服务工作区中的数据版本 |
> | Microsoft.MachineLearningServices/workspaces/data/versions/delete | 删除机器学习服务工作区中的数据版本 |
> | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/read | 获取机器学习服务工作区中的数据偏移检测器 |
> | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/write | 创建或更新机器学习服务工作区中的数据偏移检测器 |
> | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/delete | 删除机器学习服务工作区中的数据偏移检测器 |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/read | 获取机器学习服务工作区中已注册的数据集 |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/write | 在机器学习服务工作区中创建或更新注册的数据集 |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/delete | 删除机器学习服务工作区中已注册的数据集 |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read | 获取机器学习服务工作区中已注册数据集的数据集预览 |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read | 获取机器学习服务工作区中已注册数据集的数据集配置文件 |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/write | 创建或更新机器学习服务工作区中已注册数据集的数据集配置文件 |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read | 获取机器学习服务工作区中已注册数据集的数据集架构 |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/read | 获取机器学习服务工作区中已取消注册的数据集 |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write | 在机器学习服务工作区中创建或更新取消注册的数据集 |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete | 删除机器学习服务工作区中已取消注册的数据集 |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read | 获取机器学习服务工作区中已取消注册数据集的数据集预览 |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read | 获取机器学习服务工作区中已取消注册数据集的数据集配置文件 |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/write | 创建或更新机器学习服务工作区中已取消注册数据集的数据集配置文件 |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read | 获取机器学习服务工作区中已取消注册数据集的数据集架构 |
> | Microsoft.MachineLearningServices/workspaces/datastores/read | 获取机器学习服务工作区中的数据存储 |
> | Microsoft.MachineLearningServices/workspaces/datastores/write | 在机器学习服务工作区中创建或更新数据存储 |
> | Microsoft.MachineLearningServices/workspaces/datastores/delete | 删除机器学习服务工作区中的数据存储 |
> | Microsoft.MachineLearningServices/workspaces/diagnose/read | 诊断机器学习服务工作区的设置问题 |
> | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read | 获取机器学习服务工作区中已发布的管道和管道终结点 |
> | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write | 在机器学习服务工作区中创建或更新发布的管道和管道终结点 |
> | Microsoft.MachineLearningServices/workspaces/environments/read | 获取机器学习服务工作区中的环境 |
> | Microsoft.MachineLearningServices/workspaces/environments/readSecrets/action | 获取机器学习服务工作区中的环境和机密 |
> | Microsoft.MachineLearningServices/workspaces/environments/write | 在机器学习服务工作区中创建或更新环境 |
> | Microsoft.MachineLearningServices/workspaces/environments/build/action | 在机器学习服务工作区中生成环境 |
> | Microsoft.MachineLearningServices/workspaces/environments/versions/read | 获取机器学习服务工作区中的环境版本 |
> | Microsoft.MachineLearningServices/workspaces/environments/versions/write | 生成或更新机器学习服务工作区中的环境版本 |
> | Microsoft.MachineLearningServices/workspaces/environments/versions/delete | 删除机器学习服务工作区中的环境版本 |
> | Microsoft.MachineLearningServices/workspaces/eventGridFilters/read | 获取特定工作区的事件网格筛选器 |
> | Microsoft.MachineLearningServices/workspaces/eventGridFilters/write | 创建或更新特定工作区的事件网格筛选器 |
> | Microsoft.MachineLearningServices/workspaces/eventGridFilters/delete | 删除特定工作区的事件网格筛选器 |
> | Microsoft.MachineLearningServices/workspaces/experiments/read | 获取机器学习服务工作区中的试验 |
> | Microsoft.MachineLearningServices/workspaces/experiments/write | 在机器学习服务工作区中创建或更新试验 |
> | Microsoft.MachineLearningServices/workspaces/experiments/delete | 删除机器学习服务工作区中的试验 |
> | Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action | 在机器学习服务工作区中创建或更新脚本运行 |
> | Microsoft.MachineLearningServices/workspaces/experiments/runs/read | 获取机器学习服务工作区中的运行 |
> | Microsoft.MachineLearningServices/workspaces/experiments/runs/write | 在机器学习服务工作区中创建或更新运行 |
> | Microsoft.MachineLearningServices/workspaces/experiments/runs/delete | 删除机器学习服务工作区中的运行 |
> | Microsoft.MachineLearningServices/workspaces/features/read | 获取机器学习服务工作区的所有已启用功能 |
> | Microsoft.MachineLearningServices/workspaces/jobs/read | 读取机器学习服务工作区中的作业 |
> | Microsoft.MachineLearningServices/workspaces/jobs/write | 创建或更新机器学习服务工作区中的作业 |
> | Microsoft.MachineLearningServices/workspaces/jobs/delete | 删除机器学习服务工作区中的作业 |
> | Microsoft.MachineLearningServices/workspaces/jobs/cancel/action | 取消机器学习服务工作区中的作业 |
> | Microsoft.MachineLearningServices/workspaces/jobs/operationresults/read | 读取机器学习服务工作区中的作业 |
> | Microsoft.MachineLearningServices/workspaces/labeling/export/action | 导出机器学习服务工作区中标记项目的标签 |
> | Microsoft.MachineLearningServices/workspaces/labeling/labels/read | 获取机器学习服务工作区中标记项目的标签 |
> | Microsoft.MachineLearningServices/workspaces/labeling/labels/write | 创建机器学习服务工作区中标记项目的标签 |
> | Microsoft.MachineLearningServices/workspaces/labeling/labels/reject/action | 拒绝机器学习服务工作区中标记项目的标签 |
> | Microsoft.MachineLearningServices/workspaces/labeling/projects/read | 获取机器学习服务工作区中的标记项目 |
> | Microsoft.MachineLearningServices/workspaces/labeling/projects/write | 创建或更新机器学习服务工作区中的标记项目 |
> | Microsoft.MachineLearningServices/workspaces/labeling/projects/delete | 删除机器学习服务工作区中的标记项目 |
> | Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read | 获取机器学习服务工作区中的标记项目摘要 |
> | Microsoft.MachineLearningServices/workspaces/linkedServices/read | 获取机器学习服务工作区的所有链接服务 |
> | Microsoft.MachineLearningServices/workspaces/linkedServices/write | 创建或更新机器学习服务工作区链接服务 |
> | Microsoft.MachineLearningServices/workspaces/linkedServices/delete | 删除机器学习服务工作区链接服务 |
> | Microsoft.MachineLearningServices/workspaces/listNotebookAccessToken/read | 列出机器学习服务工作区的 Azure Notebook 访问令牌 |
> | Microsoft.MachineLearningServices/workspaces/listNotebookKeys/read | 列出机器学习服务工作区的 Azure Notebook 密钥 |
> | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read | 获取机器学习服务工作区中的项目 |
> | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write | 在机器学习服务工作区中创建或更新项目 |
> | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/delete | 删除机器学习服务工作区中的项目 |
> | Microsoft.MachineLearningServices/workspaces/metadata/secrets/read | 获取机器学习服务工作区中的机密 |
> | Microsoft.MachineLearningServices/workspaces/metadata/secrets/write | 在机器学习服务工作区中创建或更新机密 |
> | Microsoft.MachineLearningServices/workspaces/metadata/secrets/delete | 删除机器学习服务工作区中的机密 |
> | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read | 获取机器学习服务工作区中的快照 |
> | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write | 在机器学习服务工作区中创建或更新快照 |
> | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/delete | 删除机器学习服务工作区中的快照 |
> | Microsoft.MachineLearningServices/workspaces/metrics/resource/write | 在机器学习服务工作区中创建资源指标 |
> | Microsoft.MachineLearningServices/workspaces/models/read | 获取机器学习服务工作区中的模型 |
> | Microsoft.MachineLearningServices/workspaces/models/write | 在机器学习服务工作区中创建或更新模型 |
> | Microsoft.MachineLearningServices/workspaces/models/delete | 删除机器学习服务工作区中的模型 |
> | Microsoft.MachineLearningServices/workspaces/models/package/action | 打包机器学习服务工作区中的模型 |
> | Microsoft.MachineLearningServices/workspaces/models/versions/read | 读取机器学习服务工作区中的模型版本 |
> | Microsoft.MachineLearningServices/workspaces/models/versions/write | 创建或更新机器学习服务工作区中的模型版本 |
> | Microsoft.MachineLearningServices/workspaces/models/versions/delete | 删除机器学习服务工作区中的模型版本 |
> | Microsoft.MachineLearningServices/workspaces/modules/read | 获取机器学习服务工作区中的模块 |
> | Microsoft.MachineLearningServices/workspaces/modules/write | 在机器学习服务工作区中创建或更新模块 |
> | Microsoft.MachineLearningServices/workspaces/notebooks/samples/read | 获取示例笔记本 |
> | Microsoft.MachineLearningServices/workspaces/notebooks/storage/read | 获取工作区的笔记本文件 |
> | Microsoft.MachineLearningServices/workspaces/notebooks/storage/write | 将文件写入工作区存储 |
> | Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete | 从工作区存储中删除文件 |
> | Microsoft.MachineLearningServices/workspaces/notebooks/storage/upload/action | 将文件上传到工作区存储 |
> | Microsoft.MachineLearningServices/workspaces/notebooks/storage/download/action | 从工作区存储中下载文件 |
> | Microsoft.MachineLearningServices/workspaces/notebooks/vm/read | 获取特定工作区的笔记本 VM |
> | Microsoft.MachineLearningServices/workspaces/notebooks/vm/write | 更改笔记本 VM 的状态 |
> | Microsoft.MachineLearningServices/workspaces/notebooks/vm/delete | 删除笔记本 VM |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/read | 获取机器学习服务工作区中的联机推理终结点 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/write | 在机器学习服务工作区中创建或更新联机推理终结点 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/delete | 删除机器学习服务工作区中的联机推理终结点 |
> | Microsoft.MachineLearningServices/workspaces/onlineendpoints/regeneratekeys/action | 重新生成机器学习服务工作区中联机终结点的密钥操作 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/score/action | 对机器学习服务工作区中的联机终结点进行评分 |
> | Microsoft.MachineLearningServices/workspaces/onlineendpoints/token/action | 检索用于对机器学习服务工作区中的联机终结点进行评分的身份验证令牌 |
> | Microsoft.MachineLearningServices/workspaces/onlineendpoints/listkeys/action | 检索用于对机器学习服务工作区中的联机终结点进行评分的身份验证密钥 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/checkNameAvailability/read | 检查机器学习服务工作区中的联机推理终结点的名称 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/read | 获取机器学习服务工作区的联机推理终结点中的部署 |
> | Microsoft.MachineLearningServices/workspaces/onlineendpoints/deployments/getlogs/action | 获取机器学习服务工作区的联机推理终结点中的部署日志 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/write | 在机器学习服务工作区的联机推理终结点中创建或更新部署 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/delete | 删除机器学习服务工作区的联机推理终结点中的部署 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/checkNameAvailability/read | 检查机器学习服务工作区的联机推理终结点中的部署的名称 |
> | Microsoft.MachineLearningServices/workspaces/onlineendpoints/deployments/operationresults/read | 获取机器学习服务工作区的联机推理终结点中的部署操作结果 |
> | Microsoft.MachineLearningServices/workspaces/onlineendpoints/deployments/operationsstatus/read | 获取机器学习服务工作区的联机推理终结点中的部署操作状态 |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/skus/read | 获取机器学习服务工作区的联机推理终结点中的部署的规模 SKU 设置 |
> | Microsoft.MachineLearningServices/workspaces/onlineendpoints/operationresults/read | 检查机器学习服务工作区的联机推理终结点中的联机终结点操作结果 |
> | Microsoft.MachineLearningServices/workspaces/onlineendpoints/operationsstatus/read | 检查机器学习服务工作区的联机推理终结点中的联机终结点操作状态 |
> | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/read | 获取机器学习服务工作区中的管道草稿 |
> | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write | 在机器学习服务工作区中创建或更新管道草稿 |
> | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/delete | 删除机器学习服务工作区中的管道草稿 |
> | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionProxies/read | 查看到 Microsoft.Network 提供程序的专用终结点资源的连接代理状态 |
> | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionProxies/write | 更改到 Microsoft.Network 提供程序的专用终结点资源的连接代理状态 |
> | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionProxies/delete | 删除到 Microsoft.Network 提供程序的专用终结点资源的连接代理 |
> | Microsoft.MachineLearningServices/workspaces/privateEndpointConnectionProxies/validate/action | 验证到 Microsoft.Network 提供程序的专用终结点资源的连接代理 |
> | Microsoft.MachineLearningServices/workspaces/privateEndpointConnections/read | 查看到 Microsoft.Network 提供程序的专用终结点资源的连接状态 |
> | Microsoft.MachineLearningServices/workspaces/privateEndpointConnections/write | 更改到 Microsoft.Network 提供程序的专用终结点资源的连接状态 |
> | Microsoft.MachineLearningServices/workspaces/privateEndpointConnections/delete | 删除到 Microsoft.Network 提供程序的专用终结点资源的连接 |
> | Microsoft.MachineLearningServices/workspaces/privateLinkResources/read | 获取机器学习服务工作区的指定实例的可用专用链接资源 |
> | Microsoft.MachineLearningServices/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.MachineLearningServices/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.MachineLearningServices/workspaces/providers/Microsoft.Insights/logDefinitions/read | 获取 Azure 机器学习工作区的可用日志 |
> | Microsoft.MachineLearningServices/workspaces/providers/Microsoft.Insights/metricDefinitions/read | 获取 Azure 机器学习工作区的可用指标 |
> | Microsoft.MachineLearningServices/workspaces/reports/read | 获取机器学习服务工作区中的自定义报表 |
> | Microsoft.MachineLearningServices/workspaces/reports/write | 在机器学习服务工作区中创建或更新自定义报表 |
> | Microsoft.MachineLearningServices/workspaces/reports/delete | 删除机器学习服务工作区中的自定义报表 |
> | Microsoft.MachineLearningServices/workspaces/services/read | 获取机器学习服务工作区中的服务 |
> | Microsoft.MachineLearningServices/workspaces/services/aci/write | 在机器学习服务工作区中创建或更新 ACI 服务 |
> | Microsoft.MachineLearningServices/workspaces/services/aci/listkeys/action | 列出机器学习服务工作区中 ACI 服务的密钥 |
> | Microsoft.MachineLearningServices/workspaces/services/aci/delete | 删除机器学习服务工作区中的 ACI 服务 |
> | Microsoft.MachineLearningServices/workspaces/services/aks/write | 在机器学习服务工作区中创建或更新 AKS 服务 |
> | Microsoft.MachineLearningServices/workspaces/services/aks/listkeys/action | 列出机器学习服务工作区中 AKS 服务的密钥 |
> | Microsoft.MachineLearningServices/workspaces/services/aks/delete | 删除机器学习服务工作区中的 AKS 服务 |
> | Microsoft.MachineLearningServices/workspaces/services/aks/score/action | 检索用于对机器学习服务工作区中的 AKS 服务进行评分的身份验证令牌或密钥 |

## <a name="internet-of-things"></a>物联网

### <a name="microsoftdevices"></a>Microsoft.Devices

Azure 服务：[IoT 中心](../iot-hub/index.yml)、[IoT 中心设备预配服务](../iot-dps/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Devices/register/action | 注册 IotHub 资源提供程序的订阅，并启用 IotHub 资源的创建 |
> | Microsoft.Devices/checkNameAvailability/Action | 检查 IotHub 名称是否可用 |
> | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 检查预配服务名称是否可用 |
> | Microsoft.Devices/register/action | 注册 IotHub 资源提供程序的订阅，并启用 IotHub 资源的创建 |
> | Microsoft.Devices/checkNameAvailability/Action | 检查 IotHub 名称是否可用 |
> | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 检查预配服务名称是否可用 |
> | Microsoft.Devices/register/action | 注册 IotHub 资源提供程序的订阅，并启用 IotHub 资源的创建 |
> | Microsoft.Devices/Account/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Devices/Account/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Devices/Account/logDefinitions/read | 获取 IotHub 服务的可用日志定义 |
> | Microsoft.Devices/Account/metricDefinitions/read | 获取 IotHub 服务的可用指标 |
> | Microsoft.Devices/digitalTwins/Read | 获取关联到某个订阅的数字孪生帐户的列表 |
> | Microsoft.Devices/digitalTwins/Write | 创建新的数字孪生帐户 |
> | Microsoft.Devices/digitalTwins/Delete | 删除现有的数字孪生帐户及其所有子项 |
> | Microsoft.Devices/digitalTwins/operationresults/Read | 获取针对数字孪生帐户执行的某项操作的状态 |
> | Microsoft.Devices/digitalTwins/skus/Read | 获取数字孪生帐户的有效 SKU 的列表 |
> | Microsoft.Devices/ElasticPools/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Devices/ElasticPools/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Devices/elasticPools/eventGridFilters/Write | 创建新的或更新现有的弹性池事件网格筛选器 |
> | Microsoft.Devices/elasticPools/eventGridFilters/Read | 获取弹性池事件网格筛选器 |
> | Microsoft.Devices/elasticPools/eventGridFilters/Delete | 删除弹性池事件网格筛选器 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Write | 创建或更新 IotHub 租户资源 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Read | 获取 IotHub 租户资源 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Delete | 删除 IotHub 租户资源 |
> | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | 获取 IotHub 租户密钥 |
> | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | 导出设备 |
> | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | 导入设备 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 获取证书 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 创建或更新证书 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 删除证书 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 生成验证码 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 验证证书资源 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | 创建 EventHub 使用者组 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | 获取 EventHub 使用者组 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | 删除 EventHub 使用者组 |
> | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | 获取 IotHub 租户统计资源 |
> | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | 获取 IotHub 租户密钥 |
> | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 获取在给定 IotHub 上提交的作业详细信息 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | 获取 IotHub 服务的可用日志定义 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | 获取 IotHub 服务的可用指标 |
> | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | 获取配额指标 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 针对所有现有路由测试消息 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 针对提供的测试路由测试消息 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | 获取 IotHub 的所有路由终结点的运行状况 |
> | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/Write | 更新 Iot 租户中心内的 Azure 安全中心设置 |
> | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/Read | 获取 Iot 租户中心内的 Azure 安全中心设置 |
> | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | 获取 Iot 租户中心 SecuritySettings 的“异步放置”操作的结果 |
> | Microsoft.Devices/ElasticPools/metricDefinitions/read | 获取 IotHub 服务的可用指标 |
> | Microsoft.Devices/iotHubs/Read | 获取 IotHub 资源 |
> | Microsoft.Devices/iotHubs/Write | 创建或更新 IotHub 资源 |
> | Microsoft.Devices/iotHubs/Delete | 删除 IotHub 资源 |
> | Microsoft.Devices/iotHubs/listkeys/Action | 获取所有 IotHub 密钥 |
> | Microsoft.Devices/iotHubs/exportDevices/Action | 导出设备 |
> | Microsoft.Devices/iotHubs/importDevices/Action | 导入设备 |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionsApproval/Action | 批准或拒绝专用终结点连接 |
> | Microsoft.Devices/iotHubs/certificates/Read | 获取证书 |
> | Microsoft.Devices/iotHubs/certificates/Write | 创建或更新证书 |
> | Microsoft.Devices/iotHubs/certificates/Delete | 删除证书 |
> | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 生成验证码 |
> | Microsoft.Devices/iotHubs/certificates/verify/Action | 验证证书资源 |
> | Microsoft.Devices/iotHubs/certificates/Read | 获取证书 |
> | Microsoft.Devices/iotHubs/certificates/Write | 创建或更新证书 |
> | Microsoft.Devices/iotHubs/certificates/Delete | 删除证书 |
> | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 生成验证码 |
> | Microsoft.Devices/iotHubs/certificates/verify/Action | 验证证书资源 |
> | Microsoft.Devices/IotHubs/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Devices/IotHubs/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Devices/iotHubs/digitalTwinsLinks/Write | 链接 IotHub 与数字孪生服务 |
> | Microsoft.Devices/iotHubs/digitalTwinsLinks/Read | 获取有关当前链接数字孪生服务的信息 |
> | Microsoft.Devices/iotHubs/digitalTwinsLinks/Delete | 删除指向数字孪生服务的链接 |
> | Microsoft.Devices/iotHubs/eventGridFilters/Write | 创建新的或更新现有的事件网格筛选器 |
> | Microsoft.Devices/iotHubs/eventGridFilters/Read | 获取事件网格筛选器 |
> | Microsoft.Devices/iotHubs/eventGridFilters/Delete | 删除事件网格筛选器 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | 创建 EventHub 使用者组 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | 获取 EventHub 使用者组 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | 删除 EventHub 使用者组 |
> | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 获取给定名称的 IotHub 密钥 |
> | Microsoft.Devices/iotHubs/iotHubStats/Read | 获取 IotHub 统计信息 |
> | Microsoft.Devices/iotHubs/jobs/Read | 获取在给定 IotHub 上提交的作业详细信息 |
> | Microsoft.Devices/IotHubs/logDefinitions/read | 获取 IotHub 服务的可用日志定义 |
> | Microsoft.Devices/IotHubs/metricDefinitions/read | 获取 IotHub 服务的可用指标 |
> | Microsoft.Devices/iotHubs/operationresults/Read | 获取操作结果（已过时的 API） |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/validate/Action | 在创建期间验证专用终结点连接代理输入 |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/Read | 获取指定专用终结点连接代理的属性 |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/Write | 创建或更新专用终结点连接代理 |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/Delete | 删除现有的专用终结点连接代理 |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/operationResults/Read | 获取对专用终结点连接代理执行的异步操作的结果 |
> | Microsoft.Devices/iotHubs/privateEndpointConnections/Read | 获取指定 IoT 中心的所有专用终结点连接 |
> | Microsoft.Devices/iotHubs/privateEndpointConnections/Delete | 删除现有的专用终结点连接 |
> | Microsoft.Devices/iotHubs/privateEndpointConnections/Write | 创建或更新专用终结点连接 |
> | Microsoft.Devices/iotHubs/privateEndpointConnections/operationResults/Read | 获取对专用终结点连接执行的异步操作的结果 |
> | Microsoft.Devices/iotHubs/privateLinkResources/Read | 获取 Iot 中心的专用链接资源 |
> | Microsoft.Devices/iotHubs/quotaMetrics/Read | 获取配额指标 |
> | Microsoft.Devices/iotHubs/routing/$testall/Action | 针对所有现有路由测试消息 |
> | Microsoft.Devices/iotHubs/routing/$testnew/Action | 针对提供的测试路由测试消息 |
> | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | 获取 IotHub 的所有路由终结点的运行状况 |
> | Microsoft.Devices/iotHubs/securitySettings/Write | 更新 Iot 中心内的 Azure 安全中心设置 |
> | Microsoft.Devices/iotHubs/securitySettings/Read | 获取 Iot 中心内的 Azure 安全中心设置 |
> | Microsoft.Devices/iotHubs/securitySettings/operationResults/Read | 获取 Iot 中心 SecuritySettings 的“异步放置”操作的结果 |
> | Microsoft.Devices/iotHubs/skus/Read | 获取有效的 IotHub SKU |
> | Microsoft.Devices/locations/operationresults/Read | 获取基于位置的操作结果 |
> | Microsoft.Devices/locations/operationresults/Read | 获取基于位置的操作结果 |
> | Microsoft.Devices/operationresults/Read | 获取操作结果 |
> | Microsoft.Devices/operationresults/Read | 获取操作结果 |
> | Microsoft.Devices/operations/Read | 获取所有 ResourceProvider 操作 |
> | Microsoft.Devices/operations/Read | 获取所有 ResourceProvider 操作 |
> | Microsoft.Devices/provisioningServices/Read | 获取 IotDps 资源 |
> | Microsoft.Devices/provisioningServices/Write | 创建 IotDps 资源 |
> | Microsoft.Devices/provisioningServices/Delete | 删除 IotDps 资源 |
> | Microsoft.Devices/provisioningServices/listkeys/Action | 获取所有 IotDps 密钥 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionsApproval/Action | 批准或拒绝专用终结点连接 |
> | Microsoft.Devices/provisioningServices/certificates/Read | 获取证书 |
> | Microsoft.Devices/provisioningServices/certificates/Write | 创建或更新证书 |
> | Microsoft.Devices/provisioningServices/certificates/Delete | 删除证书 |
> | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 生成验证码 |
> | Microsoft.Devices/provisioningServices/certificates/verify/Action | 验证证书资源 |
> | Microsoft.Devices/provisioningServices/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Devices/provisioningServices/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Devices/provisioningServices/keys/listkeys/Action | 获取 IotDps 密钥的密钥名称 |
> | Microsoft.Devices/provisioningServices/logDefinitions/read | 获取预配服务的可用日志定义 |
> | Microsoft.Devices/provisioningServices/metricDefinitions/read | 获取预配服务的可用指标 |
> | Microsoft.Devices/provisioningServices/operationresults/Read | 获取 DPS 操作结果 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/validate/Action | 在创建期间验证专用终结点连接代理输入 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/Read | 获取指定专用终结点连接代理的属性 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/Write | 创建或更新专用终结点连接代理 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/Delete | 删除现有的专用终结点连接代理 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/operationResults/Read | 获取对专用终结点连接代理执行的异步操作的结果 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnections/Read | 获取指定 IoT 中心的所有专用终结点连接 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnections/Delete | 删除现有的专用终结点连接 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnections/Write | 创建或更新专用终结点连接 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnections/operationResults/Read | 获取对专用终结点连接执行的异步操作的结果 |
> | Microsoft.Devices/provisioningServices/privateLinkResources/Read | 获取 Iot 中心的专用链接资源 |
> | Microsoft.Devices/provisioningServices/skus/Read | 获取有效的 IotDps SKU |
> | Microsoft.Devices/usages/Read | 获取此提供程序的订阅用量详细信息。 |
> | Microsoft.Devices/usages/Read | 获取此提供程序的订阅用量详细信息。 |
> | **DataAction** | **说明** |
> | Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action | 向任何设备发送云到设备的消息  |
> | Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action | 接收、完成或丢弃云到设备的消息反馈通知 |
> | Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action | 删除设备的所有挂起命令 |
> | Microsoft.Devices/IotHubs/configurations/read | 读取设备管理配置 |
> | Microsoft.Devices/IotHubs/configurations/write | 创建或更新设备管理配置 |
> | Microsoft.Devices/IotHubs/configurations/delete | 删除任何设备管理配置 |
> | Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action | 将配置内容应用于边缘设备 |
> | Microsoft.Devices/IotHubs/configurations/testQueries/action | 验证配置的目标条件和自定义指标查询 |
> | Microsoft.Devices/IotHubs/devices/read | 读取任何设备或模块标识 |
> | Microsoft.Devices/IotHubs/devices/write | 创建或更新任何设备或模块标识 |
> | Microsoft.Devices/IotHubs/devices/delete | 删除任何设备或模块标识 |
> | Microsoft.Devices/IotHubs/directMethods/invoke/action | 在设备上调用直接方法 |
> | Microsoft.Devices/IotHubs/fileUpload/notifications/action | 接收、完成或放弃文件上传通知 |
> | Microsoft.Devices/IotHubs/jobs/read | 返回作业列表 |
> | Microsoft.Devices/IotHubs/jobs/write | 创建或更新任何作业 |
> | Microsoft.Devices/IotHubs/jobs/delete | 删除任何作业 |
> | Microsoft.Devices/IotHubs/statistics/read | 读取设备和服务统计信息 |
> | Microsoft.Devices/IotHubs/twins/read | 读取任何设备或模块孪生 |
> | Microsoft.Devices/IotHubs/twins/write | 写入任何设备或模块孪生 |
> | Microsoft.Devices/provisioningServices/attestationmechanism/details/action | 提取证明机制详细信息 |
> | Microsoft.Devices/provisioningServices/enrollmentGroups/read | 读取注册组 |
> | Microsoft.Devices/provisioningServices/enrollmentGroups/write | 写入注册组 |
> | Microsoft.Devices/provisioningServices/enrollmentGroups/delete | 删除注册组 |
> | Microsoft.Devices/provisioningServices/enrollments/read | 读取注册 |
> | Microsoft.Devices/provisioningServices/enrollments/write | 写入注册 |
> | Microsoft.Devices/provisioningServices/enrollments/delete | 删除注册 |
> | Microsoft.Devices/provisioningServices/registrationStates/read | 读取注册状态 |
> | Microsoft.Devices/provisioningServices/registrationStates/delete | 删除注册状态 |

### <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

Azure 服务：[IoT 中心设备更新](../iot-hub-device-update/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DeviceUpdate/checkNameAvailability/action | 检查名称可用性 |
> | Microsoft.DeviceUpdate/register/action | 注册设备更新 |
> | Microsoft.DeviceUpdate/unregister/action | 注销设备更新 |
> | Microsoft.DeviceUpdate/accounts/read | 返回设备更新帐户列表 |
> | Microsoft.DeviceUpdate/accounts/write | 创建或更新设备更新帐户 |
> | Microsoft.DeviceUpdate/accounts/delete | 删除设备更新帐户 |
> | Microsoft.DeviceUpdate/accounts/instances/read | 返回设备更新实例列表 |
> | Microsoft.DeviceUpdate/accounts/instances/write | 创建或更新设备更新实例 |
> | Microsoft.DeviceUpdate/accounts/instances/delete | 删除设备更新实例 |
> | Microsoft.DeviceUpdate/locations/operationStatuses/read | 获取操作状态 |
> | Microsoft.DeviceUpdate/locations/operationStatuses/write | 更新操作状态 |
> | Microsoft.DeviceUpdate/operations/read | 列出设备更新操作 |
> | Microsoft.DeviceUpdate/registeredSubscriptions/read | 读取注册订阅 |
> | **DataAction** | **说明** |
> | Microsoft.DeviceUpdate/accounts/instances/management/read | 执行与管理相关的读取操作 |
> | Microsoft.DeviceUpdate/accounts/instances/management/write | 执行与管理相关的写入操作 |
> | Microsoft.DeviceUpdate/accounts/instances/management/delete | 执行与管理相关的删除操作 |
> | Microsoft.DeviceUpdate/accounts/instances/updates/read | 执行与更新相关的读取操作 |
> | Microsoft.DeviceUpdate/accounts/instances/updates/write | 执行与更新相关的写入操作 |
> | Microsoft.DeviceUpdate/accounts/instances/updates/delete | 执行与更新相关的删除操作 |

### <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

Azure 服务：[IoT Central](../iot-central/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.IoTCentral/checkNameAvailability/action | 检查 IoT Central 应用程序名称是否可用 |
> | Microsoft.IoTCentral/checkSubdomainAvailability/action | 检查 IoT Central 应用程序子域是否可用 |
> | Microsoft.IoTCentral/appTemplates/action | 获取 Azure IoT Central 上的所有可用应用程序模板 |
> | Microsoft.IoTCentral/register/action | 注册 Azure IoT Central 资源提供程序的订阅 |
> | Microsoft.IoTCentral/IoTApps/read | 获取单个 IoT Central 应用程序 |
> | Microsoft.IoTCentral/IoTApps/write | 创建或更新 IoT Central 应用程序 |
> | Microsoft.IoTCentral/IoTApps/delete | 删除 IoT Central 应用程序 |
> | Microsoft.IoTCentral/IoTApps/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.IoTCentral/IoTApps/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.IoTCentral/IoTApps/providers/Microsoft.Insights/metricDefinitions/read | 获取 Azure IoT Central 的所有可用指标定义 |
> | Microsoft.IoTCentral/operations/read | 获取可针对 IoT Central 应用程序执行的所有操作 |

### <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

Azure 服务：[IoT 安全性](../iot-fundamentals/iot-security-architecture.md)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.IoTSecurity/unregister/action | 取消注册 Azure Defender for IoT 的订阅 |
> | Microsoft.IoTSecurity/register/action | 注册 Azure Defender for IoT 的订阅 |
> | Microsoft.IoTSecurity/alerts/read | 获取 IoT 警报 |
> | Microsoft.IoTSecurity/defenderSettings/read | 获取 IoT Defender 设置 |
> | Microsoft.IoTSecurity/defenderSettings/write | 创建或更新 IoT Defender 设置 |
> | Microsoft.IoTSecurity/defenderSettings/delete | 删除 IoT Defender 设置 |
> | Microsoft.IoTSecurity/defenderSettings/packageDownloads/action | 获取可下载的 IoT Defender 包信息 |
> | Microsoft.IoTSecurity/defenderSettings/downloadManagerActivation/action | 下载管理器激活文件 |
> | Microsoft.IoTSecurity/deviceGroups/read | 获取设备组 |
> | Microsoft.IoTSecurity/devices/read | 获取设备 |
> | Microsoft.IoTSecurity/locations/read | 获取位置 |
> | Microsoft.IoTSecurity/onPremiseSensors/read | 获取本地 IoT 传感器 |
> | Microsoft.IoTSecurity/onPremiseSensors/write | 创建或更新本地 IoT 传感器 |
> | Microsoft.IoTSecurity/onPremiseSensors/delete | 删除本地 IoT 传感器 |
> | Microsoft.IoTSecurity/onPremiseSensors/downloadActivation/action | 获取本地 IoT 传感器激活文件 |
> | Microsoft.IoTSecurity/onPremiseSensors/downloadResetPassword/action | 下载本地 IoT 传感器的重置密码文件 |
> | Microsoft.IoTSecurity/recommendations/read | 获取 IoT 建议 |
> | Microsoft.IoTSecurity/sensors/read | 获取 IoT 传感器 |
> | Microsoft.IoTSecurity/sensors/write | 创建或更新 IoT 传感器 |
> | Microsoft.IoTSecurity/sensors/delete | 删除 IoT 传感器 |
> | Microsoft.IoTSecurity/sensors/downloadActivation/action | 下载 IoT 传感器的激活文件 |
> | Microsoft.IoTSecurity/sensors/triggerTiPackageUpdate/action | 触发威胁情报包更新 |
> | Microsoft.IoTSecurity/sensors/downloadResetPassword/action | 下载 IoT 传感器的重置密码文件 |
> | Microsoft.IoTSecurity/sites/read | 获取 IoT 站点 |
> | Microsoft.IoTSecurity/sites/write | 创建 IoT 站点 |
> | Microsoft.IoTSecurity/sites/delete | 删除 IoT 站点 |

### <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

Azure 服务：[通知中心](../notification-hubs/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.NotificationHubs/register/action | 注册 NotificationHubs 资源提供程序的订阅，并启用命名空间和 NotificationHubs 的创建 |
> | Microsoft.NotificationHubs/unregister/action | 取消注册 NotificationHubs 资源提供程序的订阅，并启用命名空间和 NotificationHubs 的创建 |
> | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 检查给定的命名空间资源名称是否在 NotificationHub 服务中可用。 |
> | Microsoft.NotificationHubs/Namespaces/write | 创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。 |
> | Microsoft.NotificationHubs/Namespaces/read | 获取命名空间资源说明列表 |
> | Microsoft.NotificationHubs/Namespaces/Delete | 删除命名空间资源 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 获取命名空间授权规则说明列表。 |
> | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 检查给定的 NotificationHub 名称是否在命名空间中可用。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 获取命名空间授权规则说明列表。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 删除命名空间授权规则。 无法删除默认的命名空间授权规则。  |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 获取命名空间的连接字符串 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 命名空间授权规则再生成主密钥/辅助密钥，指定需要再生成的密钥 |
> | Microsoft.NotificationHubs/namespaces/diagnosticSettings/read | 获取命名空间诊断设置资源说明列表 |
> | Microsoft.NotificationHubs/namespaces/diagnosticSettings/write | 获取命名空间诊断设置资源说明列表 |
> | Microsoft.NotificationHubs/namespaces/logDefinitions/read | 获取命名空间日志资源说明列表 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 创建通知中心并更新其属性。 其属性主要包括 PNS 凭据。 授权规则和 TTL |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 获取通知中心资源说明列表 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | 删除通知中心资源 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 获取通知中心授权规则列表 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 获取所有通知中心的 PNS 凭据。 这包括 WNS、MPNS、APNS、GCM 和百度凭据 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | 发送测试推送通知。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 创建通知中心授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 获取通知中心授权规则列表 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 删除通知中心授权规则 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 获取通知中心的连接字符串 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 通知中心授权规则再生成主密钥/辅助密钥，指定需要再生成的密钥 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 获取命名空间指标资源说明列表 |
> | Microsoft.NotificationHubs/operationResults/read | 返回通知中心提供程序的操作结果 |
> | Microsoft.NotificationHubs/operations/read | 返回通知中心提供程序支持的操作列表 |

### <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

Azure 服务：[时序见解](../time-series-insights/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.TimeSeriesInsights/register/action | 注册时序见解资源提供程序的订阅，并启用时序见解环境的创建。 |
> | Microsoft.TimeSeriesInsights/environments/read | 获取环境的属性。 |
> | Microsoft.TimeSeriesInsights/environments/write | 创建新环境，或更新现有环境。 |
> | Microsoft.TimeSeriesInsights/environments/delete | 删除环境。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | 获取访问策略的属性。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 为环境创建新的访问策略，或更新现有访问策略。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | 删除访问策略。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/read | 获取事件源的属性。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/write | 为环境创建新的事件源，或更新现有事件源。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/delete | 删除事件源。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/logDefinitions/read | 获取事件源的可用日志 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | 获取 eventsources 的可用指标 |
> | Microsoft.TimeSeriesInsights/environments/privateEndpointConnectionProxies/read | 获取专用终结点连接代理的属性。 |
> | Microsoft.TimeSeriesInsights/environments/privateEndpointConnectionProxies/write | 创建环境的新专用终结点连接代理，或更新现有的连接代理。 |
> | Microsoft.TimeSeriesInsights/environments/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理。 |
> | Microsoft.TimeSeriesInsights/environments/privateEndpointConnectionProxies/validate/action | 在创建之前验证专用终结点连接代理对象。 |
> | Microsoft.TimeSeriesInsights/environments/privateEndpointConnectionProxies/operationresults/read | 验证专用终结点连接代理操作状态。 |
> | Microsoft.TimeSeriesInsights/environments/privateendpointConnections/read | 获取专用终结点连接的属性。 |
> | Microsoft.TimeSeriesInsights/environments/privateendpointConnections/write | 创建环境的新专用终结点连接，或更新现有的连接。 |
> | Microsoft.TimeSeriesInsights/environments/privateendpointConnections/delete | 删除专用终结点连接。 |
> | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/logDefinitions/read | 获取环境的可用日志 |
> | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | 获取环境的可用指标 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 获取参考数据集的属性。 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 为环境创建新的参考数据集，或更新现有参考数据集。 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 删除参考数据集。 |
> | Microsoft.TimeSeriesInsights/environments/status/read | 获取环境的状态以及相关操作（如入口）的状态。 |

## <a name="mixed-reality"></a>混合现实

### <a name="microsoftmixedreality"></a>Microsoft.MixedReality

Azure 服务：[Azure 空间定位点](../spatial-anchors/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.MixedReality/register/action | 注册混合现实资源提供程序的订阅。 |
> | Microsoft.MixedReality/unregister/action | 注销混合现实资源提供程序的订阅。 |
> | Microsoft.MixedReality/locations/checknameavailability/read | 检查名称可用性 |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/delete | 删除 Object Anchors 帐户 |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/listkeys/action | 列出 Object Anchors 帐户的密钥 |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/read | 读取 Object Anchors 帐户的属性 |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/regeneratekeys/action | 重新生成 Object Anchors 帐户的密钥 |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/write | 更新 Object Anchors 帐户的属性 |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/delete | 删除物体理解帐户 |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/listkeys/action | 列出物体理解帐户的密钥 |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/read | 读取物体理解帐户的属性 |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/regeneratekeys/action | 重新生成物体理解帐户的密钥 |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/write | 更新物体理解帐户的属性 |
> | Microsoft.MixedReality/operations/read | 列出 Microsoft 混合现实的可用操作 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/delete | 删除远程渲染帐户 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/listkeys/action | 列出远程渲染帐户的密钥 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/read | 读取远程渲染帐户的属性 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/regeneratekeys/action | 重新生成远程渲染帐户的密钥 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/write | 更新远程渲染帐户的属性 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/keys/read | 读取远程渲染帐户的密钥 |
> | Microsoft.MixedReality/remoteRenderingAccounts/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft.MixedReality/remoteRenderingAccounts 的可用指标 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/listkeys/action | 列出空间定位点帐户的密钥 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/read | 读取空间定位点帐户的属性 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/regeneratekeys/action | 重新生成空间定位点帐户的密钥 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/keys/read | 读取空间定位点帐户的密钥 |
> | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取 Microsoft.MixedReality/spatialAnchorsAccounts 的诊断设置 |
> | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 Microsoft.MixedReality/spatialAnchorsAccounts 的诊断设置 |
> | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | 获取 Microsoft.MixedReality/spatialAnchorsAccounts 的可用指标 |
> | **DataAction** | **说明** |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/ingest/action | 创建模型引入作业 |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/ingest/read | 获取模型引入作业状态 |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/ingest/action | 创建模型引入作业 |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/ingest/read | 获取模型引入作业状态 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/convert/action | 启动资产转换 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action | 启动会话 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/convert/read | 获取资产转换属性 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete | 停止资产转换 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read | 连接到远程渲染检查器 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read | 获取会话属性 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete | 停止会话 |
> | Microsoft.MixedReality/RemoteRenderingAccounts/render/read | 连接到会话 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | 创建空间定位点 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | 删除空间定位点 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | 更新空间定位点属性 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 发现附近的空间定位点 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 获取空间定位点的属性 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 找到空间定位点 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | 提交诊断数据以帮助提高 Azure 空间定位点服务的质量 |

## <a name="integration"></a>集成

### <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

Azure 服务：[API 管理](../api-management/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ApiManagement/register/action | 注册 Microsoft.ApiManagement 资源提供程序的订阅 |
> | Microsoft.ApiManagement/unregister/action | 取消注册 Microsoft.ApiManagement 资源提供程序的订阅 |
> | Microsoft.ApiManagement/checkNameAvailability/read | 检查提供的服务名称是否可用 |
> | Microsoft.ApiManagement/deletedservices/read | 获取可以在软删除期间还原的已删除 API 管理服务 |
> | Microsoft.ApiManagement/locations/deletedservices/delete | 在不选择还原的情况下删除 API 管理服务 |
> | Microsoft.ApiManagement/operations/read | 读取适用于 Microsoft.ApiManagement 资源的所有 API 操作 |
> | Microsoft.ApiManagement/reports/read | 获取按时间段、地理区域、开发者、产品、API、操作、订阅和 byRequest 聚合的报表。 |
> | Microsoft.ApiManagement/service/write | 创建或更新 API 管理服务实例 |
> | Microsoft.ApiManagement/service/read | 读取 API 管理服务实例的元数据 |
> | Microsoft.ApiManagement/service/delete | 删除 API 管理服务实例 |
> | Microsoft.ApiManagement/service/updatehostname/action | 设置、更新或删除 API 管理服务的自定义域名 |
> | Microsoft.ApiManagement/service/updatecertificate/action | 上传 API 管理服务的 TLS/SSL 证书 |
> | Microsoft.ApiManagement/service/backup/action | 将 API 管理服务备份到用户提供的存储帐户中的指定容器 |
> | Microsoft.ApiManagement/service/restore/action | 从用户提供的存储帐户中的指定容器还原 API 管理服务 |
> | Microsoft.ApiManagement/service/managedeployments/action | 更改 API 管理服务的 SKU/单位，以及添加/删除其区域部署 |
> | Microsoft.ApiManagement/service/getssotoken/action | 获取可用于以管理员身份登录到 API 管理服务旧版门户的 SSO 令牌 |
> | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | 更新虚拟网络中运行的 Microsoft.ApiManagement 资源，以提取更新的网络设置。 |
> | Microsoft.ApiManagement/service/users/action | 注册新用户 |
> | Microsoft.ApiManagement/service/notifications/action | 向指定用户发送通知 |
> | Microsoft.ApiManagement/service/gateways/action | 检索网关配置。 或更新网关检测信号。 |
> | Microsoft.ApiManagement/service/apis/read | 列出 API 管理服务实例的所有 API。 或获取按标识符指定的 API 的详细信息。 |
> | Microsoft.ApiManagement/service/apis/write | 新建 API 管理服务实例的指定 API，或更新现有 API。 或更新 API 管理服务实例的指定 API。 |
> | Microsoft.ApiManagement/service/apis/delete | 删除 API 管理服务实例的指定 API。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/read | 列出 API 的所有诊断。 或获取按标识符指定的 API 的诊断的详细信息。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/write | 为 API 创建新诊断，或更新现有诊断。 或更新按标识符指定的 API 的诊断的详细信息。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/delete | 从 API 中删除指定的诊断。 |
> | Microsoft.ApiManagement/service/apis/issues/read | 列出与指定的 API 关联的所有问题。 或获取按标识符指定的 API 的问题的详细信息。 |
> | Microsoft.ApiManagement/service/apis/issues/write | 为 API 创建新问题，或更新现有问题。 或更新 API 的现有问题。 |
> | Microsoft.ApiManagement/service/apis/issues/delete | 从 API 中删除指定的问题。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/read | 列出与指定的 API 关联的问题的所有附件。 或获取按标识符指定的 API 的问题附件的详细信息。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/write | 为 API 中的问题创建新附件，或更新现有附件。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 从问题中删除指定的注释。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/read | 列出与指定的 API 关联的问题的所有注释。 或获取按标识符指定的 API 的问题注释的详细信息。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/write | 为 API 中的问题创建新注释，或更新现有注释。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/delete | 从问题中删除指定的注释。 |
> | Microsoft.ApiManagement/service/apis/operations/read | 列出指定 API 的操作集合。 或获取按标识符指定的 API 操作的详细信息。 |
> | Microsoft.ApiManagement/service/apis/operations/write | 在 API 中创建新操作，或更新现有操作。 或更新按标识符指定的 API 中的操作的详细信息。 |
> | Microsoft.ApiManagement/service/apis/operations/delete | 删除 API 中的指定操作。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/read | 获取 API 操作级别的策略配置列表。 或获取 API 操作级别的策略配置。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/write | 创建或更新 API 操作级别的策略配置。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/delete | 删除 API 操作中的策略配置。 |
> | Microsoft.ApiManagement/service/apis/operations/policy/read | 获取操作级别的策略配置 |
> | Microsoft.ApiManagement/service/apis/operations/policy/write | 创建操作级别的策略配置 |
> | Microsoft.ApiManagement/service/apis/operations/policy/delete | 删除操作级别的策略配置 |
> | Microsoft.ApiManagement/service/apis/operations/tags/read | 列出与操作关联的所有标记。 或获取与操作关联的标记。 |
> | Microsoft.ApiManagement/service/apis/operations/tags/write | 将标记分配给操作。 |
> | Microsoft.ApiManagement/service/apis/operations/tags/delete | 从操作分离标记。 |
> | Microsoft.ApiManagement/service/apis/operationsByTags/read | 列出与标记关联的操作集合。 |
> | Microsoft.ApiManagement/service/apis/policies/read | 获取 API 级别的策略配置。 或获取 API 级别的策略配置。 |
> | Microsoft.ApiManagement/service/apis/policies/write | 创建或更新 API 的策略配置。 |
> | Microsoft.ApiManagement/service/apis/policies/delete | 删除 API 中的策略配置。 |
> | Microsoft.ApiManagement/service/apis/policy/read | 获取 API 级别的策略配置 |
> | Microsoft.ApiManagement/service/apis/policy/write | 创建 API 级别的策略配置 |
> | Microsoft.ApiManagement/service/apis/policy/delete | 删除 API 级别的策略配置 |
> | Microsoft.ApiManagement/service/apis/products/read | 列出 API 所属的全部产品。 |
> | Microsoft.ApiManagement/service/apis/releases/read | 列出 API 的所有版本。<br>将某个 API 修订版设为当前版本时创建 API 版本。<br>版本还用于回滚到以前的修订版。<br>结果将会分页，并可由 $top 和 $skip 参数约束。<br>或返回版本 API 的详细信息。 |
> | Microsoft.ApiManagement/service/apis/releases/delete | 删除 API 的所有版本，或删除 API 中的指定版本。 |
> | Microsoft.ApiManagement/service/apis/releases/write | 为 API 创建新版本。 或更新按标识符指定的 API 的版本的详细信息。 |
> | Microsoft.ApiManagement/service/apis/revisions/read | 列出 API 的所有修订版。 |
> | Microsoft.ApiManagement/service/apis/revisions/delete | 删除 API 的所有修订 |
> | Microsoft.ApiManagement/service/apis/schemas/read | 获取 API 级别的架构配置。 或获取 API 级别的架构配置。 |
> | Microsoft.ApiManagement/service/apis/schemas/write | 创建或更新 API 的架构配置。 |
> | Microsoft.ApiManagement/service/apis/schemas/delete | 删除 API 中的架构配置。 |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/read | 列出 API 范围内的所有标记说明。 类似于 swagger - tagDescription 的模型在 API 级别定义，但标记可分配到 API 范围内的“操作”或“获取标记”说明 |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/write | 在 API 范围内创建/更新标记说明。 |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | 删除 API 的标记说明。 |
> | Microsoft.ApiManagement/service/apis/tags/read | 列出与 API 关联的所有标记。 或获取与 API 关联的标记。 |
> | Microsoft.ApiManagement/service/apis/tags/write | 将标记分配给 API。 |
> | Microsoft.ApiManagement/service/apis/tags/delete | 从 API 分离标记。 |
> | Microsoft.ApiManagement/service/apisByTags/read | 列出与标记关联的 API 集合。 |
> | Microsoft.ApiManagement/service/apiVersionSets/read | 列出指定服务实例中的 API 版本集集合。 或获取按标识符指定的 API 版本集的详细信息。 |
> | Microsoft.ApiManagement/service/apiVersionSets/write | 创建或更新 API 版本集。 或更新按标识符指定的 API 版本集的详细信息。 |
> | Microsoft.ApiManagement/service/apiVersionSets/delete | 删除特定的 API 版本集。 |
> | Microsoft.ApiManagement/service/apiVersionSets/versions/read | 获取版本实体的列表 |
> | Microsoft.ApiManagement/service/authorizationServers/read | 列出服务实例中定义的授权服务器集合。 或者获取授权服务器的详细信息而不获取机密。 |
> | Microsoft.ApiManagement/service/authorizationServers/write | 创建新的授权服务器，或更新现有的授权服务器。 或更新按标识符指定的授权服务器的详细信息。 |
> | Microsoft.ApiManagement/service/authorizationServers/delete | 删除特定的授权服务器实例。 |
> | Microsoft.ApiManagement/service/authorizationServers/listSecrets/action | 获取授权服务器的机密。 |
> | Microsoft.ApiManagement/service/backends/read | 列出指定服务实例中的后端集合。 或获取按标识符指定的后端的详细信息。 |
> | Microsoft.ApiManagement/service/backends/write | 创建或更新后端。 或更新现有后端。 |
> | Microsoft.ApiManagement/service/backends/delete | 删除指定的后端。 |
> | Microsoft.ApiManagement/service/backends/reconnect/action | 通知 APIM 代理在指定的超时后与后端建立新连接。 如果未指定超时，则使用 2 分钟超时。 |
> | Microsoft.ApiManagement/service/caches/read | 列出指定服务实例中所有外部缓存的集合。 或获取按标识符指定的缓存的详细信息。 |
> | Microsoft.ApiManagement/service/caches/write | 创建或更新要在 API 管理实例中使用的外部缓存。 或更新按标识符指定的缓存的详细信息。 |
> | Microsoft.ApiManagement/service/caches/delete | 删除特定的缓存。 |
> | Microsoft.ApiManagement/service/certificates/read | 列出指定服务实例中所有证书的集合。 或获取按标识符指定的证书的详细信息。 |
> | Microsoft.ApiManagement/service/certificates/write | 创建或更新用于在后端进行身份验证的证书。 |
> | Microsoft.ApiManagement/service/certificates/delete | 删除特定的证书。 |
> | Microsoft.ApiManagement/service/contentTypes/read | 返回内容类型的列表 |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/read | 返回内容项的列表或返回内容项详细信息 |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 创建新内容项，或更新指定的内容项 |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 删除指定的内容项。 |
> | Microsoft.ApiManagement/service/diagnostics/read | 列出 API 管理服务实例的所有诊断。 或获取按标识符指定的诊断的详细信息。 |
> | Microsoft.ApiManagement/service/diagnostics/write | 创建新的诊断，或更新现有的诊断。 或更新按标识符指定的诊断的详细信息。 |
> | Microsoft.ApiManagement/service/diagnostics/delete | 删除指定的诊断。 |
> | Microsoft.ApiManagement/service/eventGridFilters/write | 设置事件网格筛选器 |
> | Microsoft.ApiManagement/service/eventGridFilters/delete | 删除事件网格筛选器 |
> | Microsoft.ApiManagement/service/eventGridFilters/read | 获取事件网格筛选器 |
> | Microsoft.ApiManagement/service/gateways/read | 列出已注册到服务实例的网关集合。 或获取按标识符指定的网关的详细信息。 |
> | Microsoft.ApiManagement/service/gateways/write | 创建或更新要在 API 管理实例中使用的网关。 或更新按标识符指定的网关的详细信息。 |
> | Microsoft.ApiManagement/service/gateways/delete | 删除特定的网关。 |
> | Microsoft.ApiManagement/service/gateways/listKeys/action | 检索网关密钥。 |
> | Microsoft.ApiManagement/service/gateways/regenerateKey/action | 重新生成指定的网关密钥（该密钥使得它所创建的任何令牌失效）。 |
> | Microsoft.ApiManagement/service/gateways/generateToken/action | 获取网关的共享访问授权令牌。 |
> | Microsoft.ApiManagement/service/gateways/apis/read | 列出与网关关联的 API 集合。 |
> | Microsoft.ApiManagement/service/gateways/apis/write | 将 API 添加到指定的网关。 |
> | Microsoft.ApiManagement/service/gateways/apis/delete | 从指定的网关中删除指定的 API。 |
> | Microsoft.ApiManagement/service/gateways/certificateAuthorities/read | 获取网关 CA 列表。 或者获取分配的证书颁发机构详细信息。 |
> | Microsoft.ApiManagement/service/gateways/certificateAuthorities/write | 将 API 添加到指定的网关。 |
> | Microsoft.ApiManagement/service/gateways/certificateAuthorities/delete | 从网关中解除分配证书颁发机构。 |
> | Microsoft.ApiManagement/service/gateways/hostnameConfigurations/read | 列出指定网关的主机名配置集合。 |
> | Microsoft.ApiManagement/service/groups/read | 列出服务实例中定义的组的集合。 或获取按标识符指定的组的详细信息。 |
> | Microsoft.ApiManagement/service/groups/write | 创建或更新组。 或更新按标识符指定的组的详细信息。 |
> | Microsoft.ApiManagement/service/groups/delete | 删除 API 管理服务实例的特定组。 |
> | Microsoft.ApiManagement/service/groups/users/read | 列出与组关联的用户实体集合。 |
> | Microsoft.ApiManagement/service/groups/users/write | 将现有用户添加到现有组 |
> | Microsoft.ApiManagement/service/groups/users/delete | 从现有组中删除现有用户。 |
> | Microsoft.ApiManagement/service/identityProviders/read | 列出在指定服务实例中配置的标识提供者集合。 或者获取标识提供者的配置详细信息而不获取机密。 |
> | Microsoft.ApiManagement/service/identityProviders/write | 创建或更新 IdentityProvider 配置。 或更新现有的 IdentityProvider 配置。 |
> | Microsoft.ApiManagement/service/identityProviders/delete | 删除指定的标识提供者配置。 |
> | Microsoft.ApiManagement/service/identityProviders/listSecrets/action | 获取标识提供者机密。 |
> | Microsoft.ApiManagement/service/issues/read | 列出指定服务实例中的问题集合。 或获取 API 管理问题详细信息 |
> | Microsoft.ApiManagement/service/locations/networkstatus/read | 获取位置中服务所依赖的资源的网络访问状态。 |
> | Microsoft.ApiManagement/service/loggers/read | 列出指定服务实例中的记录器集合。 或获取按标识符指定的记录器的详细信息。 |
> | Microsoft.ApiManagement/service/loggers/write | 创建或更新记录器。 或更新现有的记录器。 |
> | Microsoft.ApiManagement/service/loggers/delete | 删除指定的记录器。 |
> | Microsoft.ApiManagement/service/namedValues/read | 列出服务实例中定义的命名值的集合。 或者获取按标识符指定的命名值的详细信息。 |
> | Microsoft.ApiManagement/service/namedValues/write | 创建或更新命名值。 或者更新特定的命名值。 |
> | Microsoft.ApiManagement/service/namedValues/delete | 从 API 管理服务实例中删除特定的命名值。 |
> | Microsoft.ApiManagement/service/namedValues/listValue/action | 获取按标识符指定的命名值的机密。 |
> | Microsoft.ApiManagement/service/networkstatus/read | 获取服务所依赖的资源的网络访问状态。 |
> | Microsoft.ApiManagement/service/notifications/read | 列出服务实例中定义的属性的集合。 或获取按标识符指定的通知的详细信息。 |
> | Microsoft.ApiManagement/service/notifications/write | 创建或更新 API 管理发布者通知。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 获取订阅通知的通知收件人电子邮件列表。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 将电子邮件地址添加到通知收件人列表。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 从通知列表中删除电子邮件。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 获取订阅通知的通知收件人用户列表。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 将 API 管理用户添加到通知收件人列表。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 从通知列表中删除 API 管理用户。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/read | 列出所有 OpenId Connect 提供程序。 或者获取特定的 OpenID Connect 提供程序而不获取机密。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/write | 创建或更新 OpenID Connect 提供程序。 或更新特定的 OpenID Connect 提供程序。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/delete | 删除 API 管理服务实例的特定 OpenID Connect 提供程序。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/listSecrets/action | 或者获取特定的 OpenID Connect 提供程序机密。 |
> | Microsoft.ApiManagement/service/operationresults/read | 获取长时间运行的操作的当前状态 |
> | Microsoft.ApiManagement/service/outboundNetworkDependenciesEndpoints/read | 获取服务所依赖的资源的出站网络依赖关系状态。 |
> | Microsoft.ApiManagement/service/policies/read | 列出 API 管理服务的所有全局策略定义。 或获取 API 管理服务的全局策略定义。 |
> | Microsoft.ApiManagement/service/policies/write | 创建或更新 API 管理服务的全局策略配置。 |
> | Microsoft.ApiManagement/service/policies/delete | 删除 API 管理服务的全局策略配置。 |
> | Microsoft.ApiManagement/service/policy/read | 获取租户级别的策略配置 |
> | Microsoft.ApiManagement/service/policy/write | 创建租户级别的策略配置 |
> | Microsoft.ApiManagement/service/policy/delete | 删除租户级别的策略配置 |
> | Microsoft.ApiManagement/service/policyDescriptions/read | 列出所有策略说明。 |
> | Microsoft.ApiManagement/service/policySnippets/read | 列出所有策略片段。 |
> | Microsoft.ApiManagement/service/portalRevisions/read | 列出开发人员门户修订版实体的集合。 或者获取由标识符指定的开发人员门户版本。 |
> | Microsoft.ApiManagement/service/portalRevisions/write | 创建新的开发人员门户修订版。 或者更新指定门户修订版的说明，或将其设为当前版本。 |
> | Microsoft.ApiManagement/service/portalSettings/read | 列出门户设置的集合。 或者获取门户的登录设置，或获取门户的注册设置，或获取门户的委托设置。 |
> | Microsoft.ApiManagement/service/portalSettings/write | 更新登录设置。 或者创建或更新登录设置。 或者更新注册设置、更新注册设置或更新委托设置。 或者创建或更新委托设置。 |
> | Microsoft.ApiManagement/service/portalSettings/listSecrets/action | 获取门户委托设置的验证密钥。 或者获取媒体内容 blob 容器 URI。 |
> | Microsoft.ApiManagement/service/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.ApiManagement/service/privateEndpointConnectionProxies/write | 创建专用终结点连接代理 |
> | Microsoft.ApiManagement/service/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.ApiManagement/service/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.ApiManagement/service/privateEndpointConnectionProxies/operationresults/read | 在管理门户中查看专用终结点连接操作的结果 |
> | Microsoft.ApiManagement/service/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.ApiManagement/service/privateEndpointConnections/write | 批准或拒绝专用终结点连接 |
> | Microsoft.ApiManagement/service/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.ApiManagement/service/privateLinkResources/read | 获取专用链接组资源 |
> | Microsoft.ApiManagement/service/products/read | 列出指定服务实例中的产品集合。 或获取按标识符指定的产品的详细信息。 |
> | Microsoft.ApiManagement/service/products/write | 创建或更新产品。 或更新现有产品详细信息。 |
> | Microsoft.ApiManagement/service/products/delete | 删除产品。 |
> | Microsoft.ApiManagement/service/products/apis/read | 列出与产品关联的 API 集合。 |
> | Microsoft.ApiManagement/service/products/apis/write | 将 API 添加到指定的产品。 |
> | Microsoft.ApiManagement/service/products/apis/delete | 从指定的产品中删除指定的 API。 |
> | Microsoft.ApiManagement/service/products/groups/read | 列出与指定的产品关联的开发人员组集合。 |
> | Microsoft.ApiManagement/service/products/groups/write | 在指定的开发人员组与指定的产品之间添加关联。 |
> | Microsoft.ApiManagement/service/products/groups/delete | 删除指定的组与产品之间的关联。 |
> | Microsoft.ApiManagement/service/products/policies/read | 获取产品级别的策略配置。 或获取产品级别的策略配置。 |
> | Microsoft.ApiManagement/service/products/policies/write | 创建或更新产品的策略配置。 |
> | Microsoft.ApiManagement/service/products/policies/delete | 删除产品中的策略配置。 |
> | Microsoft.ApiManagement/service/products/policy/read | 获取产品级别的策略配置 |
> | Microsoft.ApiManagement/service/products/policy/write | 创建产品级别的策略配置 |
> | Microsoft.ApiManagement/service/products/policy/delete | 删除产品级别的策略配置 |
> | Microsoft.ApiManagement/service/products/subscriptions/read | 列出指定产品的订阅集合。 |
> | Microsoft.ApiManagement/service/products/tags/read | 列出与产品关联的所有标记。 或获取与产品关联的标记。 |
> | Microsoft.ApiManagement/service/products/tags/write | 将标记分配给产品。 |
> | Microsoft.ApiManagement/service/products/tags/delete | 从产品分离标记。 |
> | Microsoft.ApiManagement/service/productsByTags/read | 列出与标记关联的产品集合。 |
> | Microsoft.ApiManagement/service/properties/read | 列出服务实例中定义的属性的集合。 或获取按标识符指定的属性的详细信息。 |
> | Microsoft.ApiManagement/service/properties/write | 创建或更新属性。 或更新特定的属性。 |
> | Microsoft.ApiManagement/service/properties/delete | 从 API 管理服务实例中删除特定的属性。 |
> | Microsoft.ApiManagement/service/properties/listSecrets/action | 获取按标识符指定的属性的机密。 |
> | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | 获取 ApiManagement 服务的诊断设置 |
> | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新 ApiManagement 服务的诊断设置 |
> | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | 获取 API 管理服务的可用日志 |
> | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | 获取 API 管理服务的可用指标 |
> | Microsoft.ApiManagement/service/quotas/read | 获取配额值 |
> | Microsoft.ApiManagement/service/quotas/write | 设置配额计数器当前值 |
> | Microsoft.ApiManagement/service/quotas/periods/read | 获取某时间段的配额计数器值 |
> | Microsoft.ApiManagement/service/quotas/periods/write | 设置配额计数器当前值 |
> | Microsoft.ApiManagement/service/regions/read | 列出服务所在的所有 Azure 区域。 |
> | Microsoft.ApiManagement/service/reports/read | 获取按时间段聚合的报表，或获取按地理区域聚合的报表，或获取按开发者聚合的报表。<br>或获取按产品聚合的报表。<br>或获取按 API 聚合的报表，或获取按操作聚合的报表，或获取按订阅聚合的报表。<br>或获取报告数据的请求 |
> | Microsoft.ApiManagement/service/settings/read | 列出租户设置的集合。 始终为空。 改用 /settings/public |
> | Microsoft.ApiManagement/service/subscriptions/read | 列出 API 管理服务实例的所有订阅。 或获取指定的订阅实体（不获取密钥）。 |
> | Microsoft.ApiManagement/service/subscriptions/write | 创建或更新指定用户对指定产品的订阅。 或更新按标识符指定的订阅的详细信息。 |
> | Microsoft.ApiManagement/service/subscriptions/delete | 删除指定订阅。 |
> | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | 重新生成 API 管理服务实例的现有订阅的主密钥。 |
> | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | 重新生成 API 管理服务实例的现有订阅的辅助密钥。 |
> | Microsoft.ApiManagement/service/subscriptions/listSecrets/action | 获取指定的订阅密钥。 |
> | Microsoft.ApiManagement/service/tagResources/read | 列出与标记关联的资源集合。 |
> | Microsoft.ApiManagement/service/tags/read | 列出服务实例中定义的标记的集合。 或获取按标识符指定的标记的详细信息。 |
> | Microsoft.ApiManagement/service/tags/write | 创建标记。 或更新按标识符指定的标记的详细信息。 |
> | Microsoft.ApiManagement/service/tags/delete | 删除 API 管理服务实例的特定标记。 |
> | Microsoft.ApiManagement/service/templates/read | 获取所有电子邮件模板，或获取 API 管理电子邮件模板详细信息 |
> | Microsoft.ApiManagement/service/templates/write | 创建或更新 API 管理电子邮件模板或更新 API 管理电子邮件模板 |
> | Microsoft.ApiManagement/service/templates/delete | 重置默认 API 管理电子邮件模板 |
> | Microsoft.ApiManagement/service/tenant/read | 列出租户访问设置的集合。 或获取 API 管理服务的全局策略定义。 或获取租户访问详细信息 |
> | Microsoft.ApiManagement/service/tenant/write | 设置租户的策略配置或更新租户访问详细信息 |
> | Microsoft.ApiManagement/service/tenant/delete | 删除租户的策略配置 |
> | Microsoft.ApiManagement/service/tenant/listSecrets/action | 获取租户访问详细信息 |
> | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | 再生成主访问密钥 |
> | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | 再生成辅助访问密钥 |
> | Microsoft.ApiManagement/service/tenant/deploy/action | 运行部署任务，以便将指定的 git 分支中的更改应用到数据库中的配置。 |
> | Microsoft.ApiManagement/service/tenant/save/action | 创建包含配置快照且目标为存储库中指定分支的提交内容 |
> | Microsoft.ApiManagement/service/tenant/validate/action | 验证指定的 git 分支中的更改 |
> | Microsoft.ApiManagement/service/tenant/operationResults/read | 获取操作结果的列表，或获取特定操作的结果 |
> | Microsoft.ApiManagement/service/tenant/syncState/read | 获取上次 git 同步的状态 |
> | Microsoft.ApiManagement/service/users/read | 列出指定服务实例中已注册用户的集合。 或获取按标识符指定的用户的详细信息。 |
> | Microsoft.ApiManagement/service/users/write | 创建或更新用户。 或更新按标识符指定的用户的详细信息。 |
> | Microsoft.ApiManagement/service/users/delete | 删除特定的用户。 |
> | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 检索包含身份验证令牌（用于将给定用户登录到开发人员门户）的重定向 URL。 |
> | Microsoft.ApiManagement/service/users/token/action | 获取用户的共享访问授权令牌。 |
> | Microsoft.ApiManagement/service/users/confirmations/send/action | 发送确认 |
> | Microsoft.ApiManagement/service/users/groups/read | 列出所有用户组。 |
> | Microsoft.ApiManagement/service/users/identities/read | 列出所有用户标识。 |
> | Microsoft.ApiManagement/service/users/keys/read | 获取与用户关联的密钥 |
> | Microsoft.ApiManagement/service/users/subscriptions/read | 列出指定用户的订阅集合。 |

### <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.AppConfiguration/register/action | 注册订阅以使用 Microsoft 应用程序配置。 |
> | Microsoft.AppConfiguration/unregister/action | 取消注册订阅，使其不再使用 Microsoft 应用程序配置。 |
> | Microsoft.AppConfiguration/checkNameAvailability/read | 检查资源名称是否可供使用。 |
> | Microsoft.AppConfiguration/configurationStores/read | 获取指定的配置存储区的属性，或列出指定资源组或订阅下的所有配置存储区。 |
> | Microsoft.AppConfiguration/configurationStores/write | 使用指定的参数创建或更新配置存储区。 |
> | Microsoft.AppConfiguration/configurationStores/delete | 删除配置存储区。 |
> | Microsoft.AppConfiguration/configurationStores/ListKeys/action | 列出指定的配置存储区的 API 密钥。 |
> | Microsoft.AppConfiguration/configurationStores/RegenerateKey/action | 为指定的配置存储区重新生成 API 密钥。 |
> | Microsoft.AppConfiguration/configurationStores/ListKeyValue/action | 列出指定的配置存储区的键-值。 |
> | Microsoft.AppConfiguration/configurationStores/PrivateEndpointConnectionsApproval/action | 自动批准指定配置存储区下的专用终结点连接。 |
> | Microsoft.AppConfiguration/configurationStores/eventGridFilters/read | 获取指定的配置存储区事件网格筛选器的属性，或列出指定配置存储区下的所有配置存储区事件网格筛选器。 |
> | Microsoft.AppConfiguration/configurationStores/eventGridFilters/write | 使用指定的参数创建或更新配置存储区事件网格筛选器。 |
> | Microsoft.AppConfiguration/configurationStores/eventGridFilters/delete | 删除配置存储区事件网格筛选器。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/validate/action | 验证指定配置存储区的专用终结点连接代理。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/read | 获取指定配置存储区的专用终结点连接代理。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/write | 创建或更新指定配置存储区的专用终结点连接代理。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/delete | 删除指定配置存储区的专用终结点连接代理。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/read | 在指定配置存储区下获取专用终结点连接或列出专用终结点连接。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/write | 批准或拒绝指定配置存储区下的专用终结点连接。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/delete | 删除指定配置存储区下的专用终结点连接。 |
> | Microsoft.AppConfiguration/configurationStores/privateLinkResources/read | 列出指定配置存储区下的所有专用链接资源。 |
> | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/diagnosticSettings/read | 读取配置存储区的所有诊断设置值。 |
> | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/diagnosticSettings/write | 写入/覆盖 Microsoft 应用配置的诊断设置。 |
> | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/logDefinitions/read | 检索 Microsoft 应用配置的所有日志定义。 |
> | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/metricDefinitions/read | 检索 Microsoft 应用配置的所有指标定义。 |
> | Microsoft.AppConfiguration/locations/operationsStatus/read | 获取操作的状态。 |
> | Microsoft.AppConfiguration/operations/read | 列出 Microsoft 应用配置支持的所有操作。 |
> | **DataAction** | **说明** |
> | Microsoft.AppConfiguration/configurationStores/keyValues/read | 从配置存储区中读取键-值。 |
> | Microsoft.AppConfiguration/configurationStores/keyValues/write | 在配置存储区中创建或更新键-值。 |
> | Microsoft.AppConfiguration/configurationStores/keyValues/delete | 从配置存储区中删除现有的键-值。 |

### <a name="microsoftazurestack"></a>Microsoft.AzureStack

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.AzureStack/register/action | 订阅注册操作 |
> | Microsoft.AzureStack/register/action | 使用 Microsoft.AzureStack 资源提供程序注册订阅 |
> | Microsoft.AzureStack/cloudManifestFiles/read | 获取云清单文件 |
> | Microsoft.AzureStack/linkedSubscriptions/read | 获取 Azure Stack 关联订阅的属性 |
> | Microsoft.AzureStack/linkedSubscriptions/write | 创建或更新关联的订阅 |
> | Microsoft.AzureStack/linkedSubscriptions/delete | 删除关联的订阅 |
> | Microsoft.AzureStack/linkedSubscriptions/linkedResourceGroups/action | 读取或写入到关联的边缘资源组下的计划的链接资源 |
> | Microsoft.AzureStack/linkedSubscriptions/linkedProviders/action | 读取或写入到给定的链接资源提供程序命名空间下的计划的链接资源 |
> | Microsoft.AzureStack/linkedSubscriptions/operations/action | 获取或列出对计划的链接资源进行的异步操作的状态 |
> | Microsoft.AzureStack/Operations/read | 获取资源提供程序操作的属性 |
> | Microsoft.AzureStack/registrations/read | 获取 Azure Stack 注册的属性 |
> | Microsoft.AzureStack/registrations/write | 创建或更新 Azure Stack 注册 |
> | Microsoft.AzureStack/registrations/delete | 删除 Azure Stack 注册 |
> | Microsoft.AzureStack/registrations/getActivationKey/action | 获取最新的 Azure Stack 激活密钥 |
> | Microsoft.AzureStack/registrations/enableRemoteManagement/action | 为 Azure Stack 注册启用 RemoteManagement |
> | Microsoft.AzureStack/registrations/customerSubscriptions/read | 获取 Azure Stack 客户订阅的属性 |
> | Microsoft.AzureStack/registrations/customerSubscriptions/write | 创建或更新 Azure Stack 客户订阅 |
> | Microsoft.AzureStack/registrations/customerSubscriptions/delete | 删除 Azure Stack 客户订阅 |
> | Microsoft.AzureStack/registrations/products/read | 获取 Azure Stack 市场产品的属性 |
> | Microsoft.AzureStack/registrations/products/listDetails/action | 检索 Azure Stack 市场产品的扩展详细信息 |
> | Microsoft.AzureStack/registrations/products/getProducts/action | 检索 Azure Stack 市场产品的列表 |
> | Microsoft.AzureStack/registrations/products/getProduct/action | 检索 Azure Stack 市场产品 |
> | Microsoft.AzureStack/registrations/products/uploadProductLog/action | 记录 Azure Stack 市场产品操作状态和时间戳 |

### <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

Azure 服务：[Azure Stack Edge](../databox-online/azure-stack-edge-overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | Microsoft.DataBoxEdge/availableSkus/read | 列出或获取可用的 SKU |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 创建或更新 Data Box Edge 设备 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或获取 Data Box Edge 设备 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | 删除 Data Box Edge 设备 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或获取 Data Box Edge 设备 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或获取 Data Box Edge 设备 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 创建或更新 Data Box Edge 设备 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | 检索资源扩展信息 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateExtendedInformation/action | ArmApiDesc_action_updateExtendedInformation_dataBoxEdgeDevices |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 扫描更新 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | 在设备中下载更新 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | 在设备上安装更新 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | 上传用于注册设备的证书 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/generateCertificate/action | ArmApiDesc_action_generateCertificate_dataBoxEdgeDevices |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggerSupportPackage/action | ArmApiDesc_action_triggerSupportPackage_dataBoxEdgeDevices |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或获取警报 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或获取警报 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或获取带宽计划 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或获取带宽计划 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 创建或更新带宽计划 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 删除带宽计划 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/diagnosticProactiveLogCollectionSettings/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/diagnosticRemoteSupportSettings/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | 列出或获取作业 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | 列出或获取设备网络设置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/nodes/read | 列出或获取节点 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | 列出或获取操作状态 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | 列出或获取订单 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | 列出或获取订单 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | 创建或更新订单 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | 删除订单 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/listDCAccessCode/action | ArmApiDesc_action_listDCAccessCode_orders |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/providers/Microsoft.Insights/metricDefinitions/read | 获取可用的 Data Box Edge 设备级指标 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或获取角色 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或获取角色 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | 创建或更新角色 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | 删除角色 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/read | 列出或获取 ArmApiRes_addons |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/read | 列出或获取 ArmApiRes_addons |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/write | 创建或更新 ArmApiRes_addons |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/delete | 删除 ArmApiRes_addons |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/write | 创建或更新监视配置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/delete | 删除监视配置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/read | 列出或获取监视配置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/read | 列出或获取监视配置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | 更新安全设置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或获取共享 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或获取共享 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 创建或更新共享 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | 使用云中的数据刷新共享元数据 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 删除共享 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | 创建或更新存储帐户凭据 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或获取存储帐户凭据 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或获取存储帐户凭据 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | 删除存储帐户凭据 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/read | 列出或获取存储帐户 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/read | 列出或获取存储帐户 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/write | 创建或更新存储帐户 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/delete | 删除存储帐户 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/read | 列出或获取容器 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/read | 列出或获取容器 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/write | 创建或更新容器 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/delete | 删除容器 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/refresh/action | 使用云中的数据刷新容器元数据 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | 列出或获取触发器 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | 列出或获取触发器 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | 创建或更新触发器 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | 删除触发器 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggerSupportPackage/operationResults/read | 列出或获取操作结果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 列出或获取更新摘要 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或获取共享用户 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或获取共享用户 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 创建或更新共享用户 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 删除共享用户 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | 列出或获取操作结果 |

### <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

Azure 服务：[数据目录](../data-catalog/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | Microsoft.DataCatalog/checkNameAvailability/action | 检查租户的目录名称可用性。 |
> | Microsoft.DataCatalog/register/action | 使用 Microsoft.DataCatalog 资源提供程序注册订阅。 |
> | Microsoft.DataCatalog/unregister/action | 从 Microsoft.DataCatalog 资源提供程序取消注册订阅。 |
> | Microsoft.DataCatalog/catalogs/read | 获取订阅或资源组下一个或多个目录的属性。 |
> | Microsoft.DataCatalog/catalogs/write | 创建目录，或更新目录的标记和属性。 |
> | Microsoft.DataCatalog/catalogs/delete | 删除目录。 |
> | Microsoft.DataCatalog/operations/read | 列出可对 Microsoft.DataCatalog 资源提供程序执行的操作。 |

### <a name="microsofteventgrid"></a>Microsoft.EventGrid

Azure 服务：[事件网格](../event-grid/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.EventGrid/register/action | 注册事件网格资源提供程序的订阅。 |
> | Microsoft.EventGrid/unregister/action | 取消注册事件网格资源提供程序的订阅。 |
> | Microsoft.EventGrid/domains/write | 创建或更新域 |
> | Microsoft.EventGrid/domains/read | 读取域 |
> | Microsoft.EventGrid/domains/delete | 删除域 |
> | Microsoft.EventGrid/domains/listKeys/action | 列出域的密钥 |
> | Microsoft.EventGrid/domains/regenerateKey/action | 重新生成域的密钥 |
> | Microsoft.EventGrid/domains/PrivateEndpointConnectionsApproval/action | 审批域的 PrivateEndpointConnection |
> | Microsoft.EventGrid/domains/privateEndpointConnectionProxies/validate/action | 验证域的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/domains/privateEndpointConnectionProxies/read | 读取域的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/domains/privateEndpointConnectionProxies/write | 写入域的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/domains/privateEndpointConnectionProxies/delete | 删除域的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/domains/privateEndpointConnections/read | 读取域的 PrivateEndpointConnections |
> | Microsoft.EventGrid/domains/privateEndpointConnections/write | 写入域的 PrivateEndpointConnections |
> | Microsoft.EventGrid/domains/privateEndpointConnections/delete | 删除域的 PrivateEndpointConnections |
> | Microsoft.EventGrid/domains/privateLinkResources/read | 获取或列出域的 PrivateLinkResources |
> | Microsoft.EventGrid/domains/providers/Microsoft.Insights/logDefinitions/read | 允许访问诊断日志 |
> | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | 获取域的可用指标 |
> | Microsoft.EventGrid/domains/topics/read | 读取域主题 |
> | Microsoft.EventGrid/domains/topics/write | 创建或更新域主题 |
> | Microsoft.EventGrid/domains/topics/delete | 删除域主题 |
> | Microsoft.EventGrid/eventSubscriptions/write | 创建或更新事件订阅 |
> | Microsoft.EventGrid/eventSubscriptions/read | 读取事件订阅 |
> | Microsoft.EventGrid/eventSubscriptions/delete | 删除事件订阅 |
> | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | 获取事件订阅的完整 URL |
> | Microsoft.EventGrid/eventSubscriptions/getDeliveryAttributes/action | 获取 EventSubscription 传递特性 |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | 获取事件订阅的诊断设置 |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新事件订阅的诊断设置 |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | 获取 eventSubscriptions 的可用指标 |
> | Microsoft.EventGrid/extensionTopics/read | 读取 extensionTopic。 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | 获取主题的诊断设置 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新主题的诊断设置 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | 获取主题的可用指标 |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | 列出区域事件订阅 |
> | Microsoft.EventGrid/locations/operationResults/read | 读取区域操作的结果 |
> | Microsoft.EventGrid/locations/operationsStatus/read | 读取区域操作的状态 |
> | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | 按主题类型列出区域事件订阅 |
> | Microsoft.EventGrid/operationResults/read | 读取操作的结果 |
> | Microsoft.EventGrid/operations/read | 列出 EventGrid 操作。 |
> | Microsoft.EventGrid/operationsStatus/read | 读取操作的状态 |
> | Microsoft.EventGrid/partnerNamespaces/write | 创建或更新合作伙伴命名空间 |
> | Microsoft.EventGrid/partnerNamespaces/read | 读取合作伙伴命名空间 |
> | Microsoft.EventGrid/partnerNamespaces/delete | 删除合作伙伴命名空间 |
> | Microsoft.EventGrid/partnerNamespaces/listKeys/action | 列出合作伙伴命名空间的密钥 |
> | Microsoft.EventGrid/partnerNamespaces/regenerateKey/action | 重新生成合作伙伴命名空间的密钥 |
> | Microsoft.EventGrid/partnerNamespaces/PrivateEndpointConnectionsApproval/action | 批准合作伙伴命名空间的 PrivateEndpointConnections |
> | Microsoft.EventGrid/partnerNamespaces/eventChannels/read | 读取事件通道 |
> | Microsoft.EventGrid/partnerNamespaces/eventChannels/write | 创建或更新事件通道 |
> | Microsoft.EventGrid/partnerNamespaces/eventChannels/delete | 删除事件通道 |
> | Microsoft.EventGrid/partnerNamespaces/eventChannels/channelReadinessStateChange/action | 更改合作伙伴主题就绪状态 |
> | Microsoft.EventGrid/partnerNamespaces/privateEndpointConnectionProxies/validate/action | 验证合作伙伴命名空间的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/partnerNamespaces/privateEndpointConnectionProxies/read | 读取合作伙伴命名空间的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/partnerNamespaces/privateEndpointConnectionProxies/write | 写入合作伙伴命名空间的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/partnerNamespaces/privateEndpointConnectionProxies/delete | 删除合作伙伴命名空间的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/partnerNamespaces/privateEndpointConnections/read | 读取合作伙伴命名空间的 PrivateEndpointConnections |
> | Microsoft.EventGrid/partnerNamespaces/privateEndpointConnections/write | 写入合作伙伴命名空间的 PrivateEndpointConnections |
> | Microsoft.EventGrid/partnerNamespaces/privateEndpointConnections/delete | 删除合作伙伴命名空间的 PrivateEndpointConnections |
> | Microsoft.EventGrid/partnerNamespaces/privateLinkResources/read | 读取合作伙伴命名空间的 PrivateLinkResources |
> | Microsoft.EventGrid/partnerNamespaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取合作伙伴命名空间的诊断设置 |
> | Microsoft.EventGrid/partnerNamespaces/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新合作伙伴命名空间的诊断设置 |
> | Microsoft.EventGrid/partnerNamespaces/providers/Microsoft.Insights/logDefinitions/read | 允许访问诊断日志 |
> | Microsoft.EventGrid/partnerNamespaces/providers/Microsoft.Insights/metricDefinitions/read | 获取合作伙伴命名空间的可用指标 |
> | Microsoft.EventGrid/partnerRegistrations/write | 创建或更新合作伙伴注册 |
> | Microsoft.EventGrid/partnerRegistrations/read | 读取合作伙伴注册 |
> | Microsoft.EventGrid/partnerRegistrations/delete | 删除合作伙伴注册 |
> | Microsoft.EventGrid/partnerTopics/read | 读取合作伙伴主题 |
> | Microsoft.EventGrid/partnerTopics/write | 创建或更新合作伙伴主题 |
> | Microsoft.EventGrid/partnerTopics/delete | 删除合作伙伴主题 |
> | Microsoft.EventGrid/partnerTopics/activate/action | 激活合作伙伴主题 |
> | Microsoft.EventGrid/partnerTopics/deactivate/action | 停用合作伙伴主题 |
> | Microsoft.EventGrid/partnerTopics/eventSubscriptions/write | 创建或更新 PartnerTopic eventSubscription |
> | Microsoft.EventGrid/partnerTopics/eventSubscriptions/read | 读取 PartnerTopic eventSubscription |
> | Microsoft.EventGrid/partnerTopics/eventSubscriptions/delete | 删除 PartnerTopic eventSubscription |
> | Microsoft.EventGrid/partnerTopics/eventSubscriptions/getFullUrl/action | 获取 PartnerTopic 事件订阅的完整 URL |
> | Microsoft.EventGrid/partnerTopics/eventSubscriptions/getDeliveryAttributes/action | 获取 PartnerTopic EventSubscription 传递特性 |
> | Microsoft.EventGrid/partnerTopics/providers/Microsoft.Insights/diagnosticSettings/read | 获取合作伙伴主题的诊断设置 |
> | Microsoft.EventGrid/partnerTopics/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新合作伙伴主题的诊断设置 |
> | Microsoft.EventGrid/partnerTopics/providers/Microsoft.Insights/logDefinitions/read | 允许访问诊断日志 |
> | Microsoft.EventGrid/partnerTopics/providers/Microsoft.Insights/metricDefinitions/read | 获取合作伙伴主题的可用指标 |
> | Microsoft.EventGrid/sku/read | 读取事件网格资源的可用 SKU 定义 |
> | Microsoft.EventGrid/systemTopics/read | 阅读系统主题 |
> | Microsoft.EventGrid/systemTopics/write | 创建或更新系统主题 |
> | Microsoft.EventGrid/systemTopics/delete | 删除系统主题 |
> | Microsoft.EventGrid/systemTopics/eventSubscriptions/write | 创建或更新 SystemTopic eventSubscription |
> | Microsoft.EventGrid/systemTopics/eventSubscriptions/read | 读取 SystemTopic eventSubscription |
> | Microsoft.EventGrid/systemTopics/eventSubscriptions/delete | 删除 SystemTopic eventSubscription |
> | Microsoft.EventGrid/systemTopics/eventSubscriptions/getFullUrl/action | 获取 SystemTopic 事件订阅的完整 URL |
> | Microsoft.EventGrid/systemTopics/eventSubscriptions/getDeliveryAttributes/action | 获取 SystemTopic EventSubscription 传递特性 |
> | Microsoft.EventGrid/systemTopics/providers/Microsoft.Insights/diagnosticSettings/read | 获取系统主题的诊断设置 |
> | Microsoft.EventGrid/systemTopics/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新系统主题的诊断设置 |
> | Microsoft.EventGrid/systemTopics/providers/Microsoft.Insights/logDefinitions/read | 允许访问诊断日志 |
> | Microsoft.EventGrid/systemTopics/providers/Microsoft.Insights/metricDefinitions/read | 获取系统主题的可用指标 |
> | Microsoft.EventGrid/topics/write | 创建或更新主题 |
> | Microsoft.EventGrid/topics/read | 读取主题 |
> | Microsoft.EventGrid/topics/delete | 删除主题 |
> | Microsoft.EventGrid/topics/listKeys/action | 列出主题的密钥 |
> | Microsoft.EventGrid/topics/regenerateKey/action | 重新生成主题的密钥 |
> | Microsoft.EventGrid/topics/PrivateEndpointConnectionsApproval/action | 审批主题的 PrivateEndpointConnection |
> | Microsoft.EventGrid/topics/privateEndpointConnectionProxies/validate/action | 验证主题的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/topics/privateEndpointConnectionProxies/read | 读取主题的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/topics/privateEndpointConnectionProxies/write | 写入主题的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/topics/privateEndpointConnectionProxies/delete | 删除主题的 PrivateEndpointConnectionProxies |
> | Microsoft.EventGrid/topics/privateEndpointConnections/read | 读取主题的 PrivateEndpointConnections |
> | Microsoft.EventGrid/topics/privateEndpointConnections/write | 写入主题的 PrivateEndpointConnections |
> | Microsoft.EventGrid/topics/privateEndpointConnections/delete | 删除主题的 PrivateEndpointConnections |
> | Microsoft.EventGrid/topics/privateLinkResources/read | 读取主题的 PrivateLinkResources |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | 获取主题的诊断设置 |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新主题的诊断设置 |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/logDefinitions/read | 允许访问诊断日志 |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | 获取主题的可用指标 |
> | Microsoft.EventGrid/topictypes/read | 读取主题类型 |
> | Microsoft.EventGrid/topictypes/eventSubscriptions/read | 按主题类型列出全局事件订阅 |
> | Microsoft.EventGrid/topictypes/eventtypes/read | 读取主题类型支持的事件类型 |
> | **DataAction** | **说明** |
> | Microsoft.EventGrid/events/send/action | 将事件发送到主题 |

### <a name="microsoftlogic"></a>Microsoft.Logic

Azure 服务：[逻辑应用](../logic-apps/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Logic/register/action | 为给定的订阅注册 Microsoft.Logic 资源提供程序。 |
> | Microsoft.Logic/integrationAccounts/read | 读取集成帐户。 |
> | Microsoft.Logic/integrationAccounts/write | 创建或更新集成帐户。 |
> | Microsoft.Logic/integrationAccounts/delete | 删除集成帐户。 |
> | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | 再生成访问密钥机密。 |
> | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 获取集成帐户的回调 URL。 |
> | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | 获取密钥保管库中的密钥。 |
> | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 记录集成帐户中的跟踪事件。 |
> | Microsoft.Logic/integrationAccounts/join/action | 加入集成帐户。 |
> | Microsoft.Logic/integrationAccounts/agreements/read | 读取集成帐户中的协议。 |
> | Microsoft.Logic/integrationAccounts/agreements/write | 创建或更新集成帐户中的协议。 |
> | Microsoft.Logic/integrationAccounts/agreements/delete | 删除集成帐户中的协议。 |
> | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 获取集成帐户中的协议内容的回调 URL。 |
> | Microsoft.Logic/integrationAccounts/assemblies/read | 读取集成帐户中的程序集。 |
> | Microsoft.Logic/integrationAccounts/assemblies/write | 创建或更新集成帐户中的程序集。 |
> | Microsoft.Logic/integrationAccounts/assemblies/delete | 删除集成帐户中的程序集。 |
> | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 获取集成帐户中的程序集内容的回调 URL。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 读取集成帐户中的批配置。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 创建或更新集成帐户中的批配置。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 删除集成帐户中的批配置。 |
> | Microsoft.Logic/integrationAccounts/certificates/read | 读取集成帐户中的证书。 |
> | Microsoft.Logic/integrationAccounts/certificates/write | 创建或更新集成帐户中的证书。 |
> | Microsoft.Logic/integrationAccounts/certificates/delete | 删除集成帐户中的证书。 |
> | Microsoft.Logic/integrationAccounts/groups/read | 读取集成帐户中的组。 |
> | Microsoft.Logic/integrationAccounts/groups/write | 创建或更新集成帐户中的组。 |
> | Microsoft.Logic/integrationAccounts/groups/delete | 删除集成帐户中的组。 |
> | Microsoft.Logic/integrationAccounts/maps/read | 读取集成帐户中的映射。 |
> | Microsoft.Logic/integrationAccounts/maps/write | 创建或更新集成帐户中的映射。 |
> | Microsoft.Logic/integrationAccounts/maps/delete | 删除集成帐户中的映射。 |
> | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 获取集成帐户中的映射内容的回调 URL。 |
> | Microsoft.Logic/integrationAccounts/partners/read | 读取集成帐户中的合作伙伴。 |
> | Microsoft.Logic/integrationAccounts/partners/write | 创建或更新集成帐户中的合作伙伴。 |
> | Microsoft.Logic/integrationAccounts/partners/delete | 删除集成帐户中的合作伙伴。 |
> | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 获取集成帐户中的合作伙伴内容的回调 URL。 |
> | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 读取集成帐户日志定义。 |
> | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/read | 读取集成帐户中的 RosettaNet 进程配置。 |
> | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/write | 创建或更新集成帐户中的 RosettaNet 进程配置。 |
> | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/delete | 删除集成帐户中的 RosettaNet 进程配置。 |
> | Microsoft.Logic/integrationAccounts/schedules/read | 读取集成帐户中的计划。 |
> | Microsoft.Logic/integrationAccounts/schedules/write | 创建或更新集成帐户中的计划。 |
> | Microsoft.Logic/integrationAccounts/schedules/delete | 删除集成帐户中的计划。 |
> | Microsoft.Logic/integrationAccounts/schemas/read | 读取集成帐户中的架构。 |
> | Microsoft.Logic/integrationAccounts/schemas/write | 创建或更新集成帐户中的架构。 |
> | Microsoft.Logic/integrationAccounts/schemas/delete | 删除集成帐户中的架构。 |
> | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 获取集成帐户中的架构内容的回调 URL。 |
> | Microsoft.Logic/integrationAccounts/sessions/read | 读取集成帐户中的会话。 |
> | Microsoft.Logic/integrationAccounts/sessions/write | 创建或更新集成帐户中的会话。 |
> | Microsoft.Logic/integrationAccounts/sessions/delete | 删除集成帐户中的会话。 |
> | Microsoft.Logic/integrationAccounts/usageConfigurations/read | 读取集成帐户中的用法配置。 |
> | Microsoft.Logic/integrationAccounts/usageConfigurations/write | 创建或更新集成帐户中的用法配置。 |
> | Microsoft.Logic/integrationAccounts/usageConfigurations/delete | 删除集成帐户中的用法配置。 |
> | Microsoft.Logic/integrationAccounts/usageConfigurations/listCallbackUrl/action | 获取集成帐户中用法配置的回叫 URL。 |
> | Microsoft.Logic/integrationServiceEnvironments/read | 读取集成服务环境。 |
> | Microsoft.Logic/integrationServiceEnvironments/write | 创建或更新集成服务环境。 |
> | Microsoft.Logic/integrationServiceEnvironments/delete | 删除集成服务环境。 |
> | Microsoft.Logic/integrationServiceEnvironments/join/action | 加入集成服务环境。 |
> | Microsoft.Logic/integrationServiceEnvironments/availableManagedApis/read | 读取集成服务环境可用的托管 API。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 读取集成服务环境托管 API。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/write | 创建或更新集成服务环境托管 API。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/join/action | 联接集成服务环境托管 API。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 读取集成服务环境托管 API 操作。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/operationStatuses/read | 读取集成服务环境托管 API 操作状态。 |
> | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 读取集成服务环境操作状态。 |
> | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 读取集成服务环境指标定义。 |
> | Microsoft.Logic/locations/workflows/validate/action | 验证工作流。 |
> | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | 获取工作流建议操作组。 |
> | Microsoft.Logic/operations/read | 获取操作。 |
> | Microsoft.Logic/workflows/read | 读取工作流。 |
> | Microsoft.Logic/workflows/write | 创建或更新工作流。 |
> | Microsoft.Logic/workflows/delete | 删除工作流。 |
> | Microsoft.Logic/workflows/run/action | 启动工作流的运行。 |
> | Microsoft.Logic/workflows/disable/action | 禁用工作流。 |
> | Microsoft.Logic/workflows/enable/action | 启用工作流。 |
> | Microsoft.Logic/workflows/suspend/action | 暂停工作流。 |
> | Microsoft.Logic/workflows/validate/action | 验证工作流。 |
> | Microsoft.Logic/workflows/move/action | 将工作流从其现有订阅 ID、资源组和/或名称移到其他订阅 ID、资源组和/或名称。 |
> | Microsoft.Logic/workflows/listSwagger/action | 获取工作流 Swagger 定义。 |
> | Microsoft.Logic/workflows/regenerateAccessKey/action | 再生成访问密钥机密。 |
> | Microsoft.Logic/workflows/listCallbackUrl/action | 获取工作流的回调 URL。 |
> | Microsoft.Logic/workflows/accessKeys/read | 读取访问密钥。 |
> | Microsoft.Logic/workflows/accessKeys/write | 创建或更新访问密钥。 |
> | Microsoft.Logic/workflows/accessKeys/delete | 删除访问密钥。 |
> | Microsoft.Logic/workflows/accessKeys/list/action | 列出访问密钥机密。 |
> | Microsoft.Logic/workflows/accessKeys/regenerate/action | 再生成访问密钥机密。 |
> | Microsoft.Logic/workflows/detectors/read | 读取工作流检测器。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | 读取工作流诊断设置。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新工作流诊断设置。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | 读取工作流日志定义。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | 读取工作流指标定义。 |
> | Microsoft.Logic/workflows/runs/read | 读取工作流运行。 |
> | Microsoft.Logic/workflows/runs/delete | 删除工作流的运行。 |
> | Microsoft.Logic/workflows/runs/cancel/action | 取消工作流的运行。 |
> | Microsoft.Logic/workflows/runs/actions/read | 读取工作流运行操作。 |
> | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | 获取工作流运行操作表达式跟踪。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/read | 读取工作流运行操作重复。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | 获取工作流运行操作重复表达式跟踪。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | 读取工作流运行重复操作请求历史记录。 |
> | Microsoft.Logic/workflows/runs/actions/requestHistories/read | 读取工作流运行操作请求历史记录。 |
> | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | 读取工作流运行操作范围重复。 |
> | Microsoft.Logic/workflows/runs/operations/read | 读取工作流运行操作状态。 |
> | Microsoft.Logic/workflows/triggers/read | 读取触发器。 |
> | Microsoft.Logic/workflows/triggers/run/action | 执行触发器。 |
> | Microsoft.Logic/workflows/triggers/reset/action | 重置触发器。 |
> | Microsoft.Logic/workflows/triggers/setState/action | 设置触发器状态。 |
> | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | 获取触发器的回调 URL。 |
> | Microsoft.Logic/workflows/triggers/histories/read | 读取触发器历史记录。 |
> | Microsoft.Logic/workflows/triggers/histories/resubmit/action | 重新提交工作流触发器。 |
> | Microsoft.Logic/workflows/versions/read | 读取工作流版本。 |
> | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | 获取触发器的回调 URL。 |

### <a name="microsoftrelay"></a>Microsoft.Relay

Azure 服务：[Azure 中继](../azure-relay/relay-what-is-it.md)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Relay/checkNamespaceAvailability/action | 检查给定订阅下的命名空间可用性。 已弃用此 API，请改用 CheckNameAvailability。 |
> | Microsoft.Relay/checkNameAvailability/action | 检查给定订阅下的命名空间可用性。 |
> | Microsoft.Relay/register/action | 注册中继资源提供程序的订阅，并启用中继资源的创建 |
> | Microsoft.Relay/unregister/action | 注册中继资源提供程序的订阅，并启用中继资源的创建 |
> | Microsoft.Relay/namespaces/write | 创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。 |
> | Microsoft.Relay/namespaces/read | 获取命名空间资源说明列表 |
> | Microsoft.Relay/namespaces/Delete | 删除命名空间资源 |
> | Microsoft.Relay/namespaces/authorizationRules/action | 更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.Relay/namespaces/removeAcsNamepsace/action | 删除 ACS 命名空间 |
> | Microsoft.Relay/namespaces/privateEndpointConnectionsApproval/action | 批准专用终结点连接 |
> | Microsoft.Relay/namespaces/authorizationRules/read | 获取命名空间授权规则说明列表。 |
> | Microsoft.Relay/namespaces/authorizationRules/write | 创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | Microsoft.Relay/namespaces/authorizationRules/delete | 删除命名空间授权规则。 无法删除默认的命名空间授权规则。  |
> | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 获取命名空间的连接字符串 |
> | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 检查给定订阅下命名空间别名的可用性。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 创建或更新与命名空间关联的灾难恢复配置。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 获取与命名空间关联的灾难恢复配置。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | 禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | 调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | 获取灾难恢复主命名空间的授权规则 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 获取灾难恢复主命名空间的授权规则密钥 |
> | Microsoft.Relay/namespaces/HybridConnections/write | 创建或更新 HybridConnection 属性。 |
> | Microsoft.Relay/namespaces/HybridConnections/read | 获取 HybridConnection 资源说明列表 |
> | Microsoft.Relay/namespaces/HybridConnections/Delete | 用于删除 HybridConnection 资源的操作 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | 更新 HybridConnection 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  获取 HybridConnection 授权规则的列表 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | 创建 HybridConnection 授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | 用于删除 HybridConnection 授权规则的操作 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | 获取 HybridConnection 的连接字符串 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | 再生成资源的主密钥或辅助密钥 |
> | Microsoft.Relay/namespaces/messagingPlan/read | 获取命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.Relay/namespaces/messagingPlan/write | 更新命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.Relay/namespaces/networkrulesets/read | 获取 NetworkRuleSet 资源 |
> | Microsoft.Relay/namespaces/networkrulesets/write | 创建 VNET 规则资源 |
> | Microsoft.Relay/namespaces/networkrulesets/delete | 删除 VNET 规则资源 |
> | Microsoft.Relay/namespaces/operationresults/read | 获取命名空间操作的状态 |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/write | 创建专用终结点连接代理 |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/operationstatus/read | 获取异步专用终结点操作的状态 |
> | Microsoft.Relay/namespaces/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.Relay/namespaces/privateEndpointConnections/write | 创建或更新专用终结点连接 |
> | Microsoft.Relay/namespaces/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Relay/namespaces/privateEndpointConnections/operationstatus/read | 获取异步专用终结点操作的状态 |
> | Microsoft.Relay/namespaces/privateLinkResources/read | 获取支持专用终结点连接的资源类型 |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取命名空间诊断设置资源说明列表 |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 获取命名空间诊断设置资源说明列表 |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/logDefinitions/read | 获取命名空间日志资源说明列表 |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 获取命名空间指标资源说明列表 |
> | Microsoft.Relay/namespaces/WcfRelays/write | 创建或更新 WcfRelay 属性。 |
> | Microsoft.Relay/namespaces/WcfRelays/read | 获取 WcfRelay 资源说明列表 |
> | Microsoft.Relay/namespaces/WcfRelays/Delete | 用于删除 WcfRelay 资源的操作 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | 更新 WcfRelay 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  获取 WcfRelay 授权规则的列表 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | 创建 WcfRelay 授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | 用于删除 WcfRelay 授权规则的操作 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | 获取 WcfRelay 的连接字符串 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | 再生成资源的主密钥或辅助密钥 |
> | Microsoft.Relay/operations/read | 获取操作 |
> | **DataAction** | **说明** |
> | Microsoft.Relay/namespaces/messages/send/action | 发送消息 |
> | Microsoft.Relay/namespaces/messages/listen/action | 接收消息 |

### <a name="microsoftservicebus"></a>Microsoft.ServiceBus

Azure 服务：[服务总线](../service-bus-messaging/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ServiceBus/checkNamespaceAvailability/action | 检查给定订阅下的命名空间可用性。 已弃用此 API，请改用 CheckNameAvailability。 |
> | Microsoft.ServiceBus/checkNameAvailability/action | 检查给定订阅下的命名空间可用性。 |
> | Microsoft.ServiceBus/register/action | 注册 ServiceBus 资源提供程序的订阅，并启用 ServiceBus 资源的创建 |
> | Microsoft.ServiceBus/unregister/action | 注册 ServiceBus 资源提供程序的订阅，并启用 ServiceBus 资源的创建 |
> | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 在服务总线资源提供程序中删除指定 VNet 的 VNet 规则 |
> | Microsoft.ServiceBus/namespaces/write | 创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。 |
> | Microsoft.ServiceBus/namespaces/read | 获取命名空间资源说明列表 |
> | Microsoft.ServiceBus/namespaces/Delete | 删除命名空间资源 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/action | 更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.ServiceBus/namespaces/migrate/action | 迁移命名空间操作 |
> | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | 删除 ACS 命名空间 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionsApproval/action | 批准专用终结点连接 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/write | 创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/read | 获取命名空间授权规则说明列表。 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 删除命名空间授权规则。 无法删除默认的命名空间授权规则。  |
> | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 获取命名空间的连接字符串 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 检查给定订阅下命名空间别名的可用性。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 创建或更新与命名空间关联的灾难恢复配置。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 获取与命名空间关联的灾难恢复配置。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | 禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | 调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | 获取灾难恢复主命名空间的授权规则 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 获取灾难恢复主命名空间的授权规则密钥 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 创建或更新与命名空间关联的事件网格筛选器。 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 获取与命名空间关联的事件网格筛选器。 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 删除与命名空间关联的事件网格筛选器。 |
> | Microsoft.ServiceBus/namespaces/eventhubs/read | 获取 EventHub 资源说明列表 |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/read | 获取 IP 筛选器资源 |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/write | 创建 IP 筛选器资源 |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | 删除 IP 筛选器资源 |
> | Microsoft.ServiceBus/namespaces/messagingPlan/read | 获取命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.ServiceBus/namespaces/messagingPlan/write | 更新命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 创建或更新迁移配置。 这会开始将资源从标准命名空间同步到高级命名空间 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 获取迁移配置以指示迁移状态并挂起复制操作 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 删除迁移配置。 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | 还原标准命名空间到高级命名空间的迁移 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | 向高级命名空间分配与标准命名空间相关的 DNS，以完成迁移并停止从标准命名空间到高级命名空间的资源同步 |
> | Microsoft.ServiceBus/namespaces/networkruleset/read | 获取 NetworkRuleSet 资源 |
> | Microsoft.ServiceBus/namespaces/networkruleset/write | 创建 VNET 规则资源 |
> | Microsoft.ServiceBus/namespaces/networkruleset/delete | 删除 VNET 规则资源 |
> | Microsoft.ServiceBus/namespaces/networkrulesets/read | 获取 NetworkRuleSet 资源 |
> | Microsoft.ServiceBus/namespaces/networkrulesets/write | 创建 VNET 规则资源 |
> | Microsoft.ServiceBus/namespaces/networkrulesets/delete | 删除 VNET 规则资源 |
> | Microsoft.ServiceBus/namespaces/operationresults/read | 获取命名空间操作的状态 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/write | 创建专用终结点连接代理 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/operationstatus/read | 获取异步专用终结点操作的状态 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnections/write | 创建或更新专用终结点连接 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnections/operationstatus/read | 获取异步专用终结点操作的状态 |
> | Microsoft.ServiceBus/namespaces/privateLinkResources/read | 获取支持专用终结点连接的资源类型 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取命名空间诊断设置资源说明列表 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 获取命名空间诊断设置资源说明列表 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 获取命名空间日志资源说明列表 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 获取命名空间指标资源说明列表 |
> | Microsoft.ServiceBus/namespaces/queues/write | 创建或更新队列属性。 |
> | Microsoft.ServiceBus/namespaces/queues/read | 获取队列资源说明列表 |
> | Microsoft.ServiceBus/namespaces/queues/Delete | 用于删除队列资源的操作 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | 更新队列的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | 创建队列授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  获取队列授权规则列表 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | 用于删除队列授权规则的操作 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | 获取队列的连接字符串 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | Microsoft.ServiceBus/namespaces/skus/read | 列出命名空间支持的 SKU |
> | Microsoft.ServiceBus/namespaces/topics/write | 创建或更新主题属性。 |
> | Microsoft.ServiceBus/namespaces/topics/read | 获取主题资源说明列表 |
> | Microsoft.ServiceBus/namespaces/topics/Delete | 用于删除主题资源的操作 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | 更新主题的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | 创建主题授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  获取主题授权规则列表 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | 用于删除主题授权规则的操作 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | 获取主题的连接字符串 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | 创建或更新 TopicSubscription 属性。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | 获取 TopicSubscription 资源说明列表 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | 用于删除 TopicSubscription 资源的操作 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 创建或更新规则属性。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 获取规则资源说明列表 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 用于删除规则资源的操作 |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | 获取 VNET 规则资源 |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | 创建 VNET 规则资源 |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | 删除 VNET 规则资源 |
> | Microsoft.ServiceBus/operations/read | 获取操作 |
> | Microsoft.ServiceBus/sku/read | 获取 SKU 资源说明列表 |
> | Microsoft.ServiceBus/sku/regions/read | 获取 SKU 区域资源说明列表 |
> | **DataAction** | **说明** |
> | Microsoft.ServiceBus/namespaces/messages/send/action | 发送消息 |
> | Microsoft.ServiceBus/namespaces/messages/receive/action | 接收消息 |

## <a name="identity"></a>标识

### <a name="microsoftaad"></a>Microsoft.AAD

Azure 服务：[Azure Active Directory 域服务](../active-directory-domain-services/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.AAD/unregister/action | 取消注册域服务 |
> | Microsoft.AAD/register/action | 注册域服务 |
> | Microsoft.AAD/domainServices/read | 读取域服务 |
> | Microsoft.AAD/domainServices/write | 写入域服务 |
> | Microsoft.AAD/domainServices/delete | 删除域服务 |
> | Microsoft.AAD/domainServices/oucontainer/read | 读取 OU 容器 |
> | Microsoft.AAD/domainServices/oucontainer/write | 写入 OU 容器 |
> | Microsoft.AAD/domainServices/oucontainer/delete | 删除 OU 容器 |
> | Microsoft.AAD/domainServices/OutboundNetworkDependenciesEndpoints/read | 获取所有出站依赖项的网络终结点 |
> | Microsoft.AAD/domainServices/providers/Microsoft.Insights/diagnosticSettings/read | 获取域服务的诊断设置 |
> | Microsoft.AAD/domainServices/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新域服务资源的诊断设置 |
> | Microsoft.AAD/domainServices/providers/Microsoft.Insights/logDefinitions/read | 获取域服务的可用日志 |
> | Microsoft.AAD/locations/operationresults/read |  |
> | Microsoft.AAD/Operations/read |  |

### <a name="microsoftaadiam"></a>microsoft.aadiam

Azure 服务：Azure Active Directory

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.aadiam/azureADMetrics/read | 读取 Azure AD 指标定义 |
> | microsoft.aadiam/azureADMetrics/write | 创建和更新 Azure AD 指标定义 |
> | microsoft.aadiam/azureADMetrics/delete | 删除 Azure AD 指标定义 |
> | microsoft.aadiam/azureADMetrics/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | microsoft.aadiam/azureADMetrics/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | microsoft.aadiam/azureADMetrics/providers/Microsoft.Insights/metricDefinitions/read | 获取 azureADMetrics 的可用指标 |
> | microsoft.aadiam/diagnosticsettings/write | 写入诊断设置 |
> | microsoft.aadiam/diagnosticsettings/read | 读取诊断设置 |
> | microsoft.aadiam/diagnosticsettings/delete | 删除诊断设置 |
> | microsoft.aadiam/diagnosticsettingscategories/read | 读取诊断设置类别 |
> | microsoft.aadiam/metricDefinitions/read | 读取租户级别的指标定义 |
> | microsoft.aadiam/metrics/read | 读取租户级别的指标 |
> | microsoft.aadiam/privateLinkForAzureAD/read | 读取专用链接策略定义 |
> | microsoft.aadiam/privateLinkForAzureAD/write | 创建和更新专用链接策略定义 |
> | microsoft.aadiam/privateLinkForAzureAD/delete | 删除专用链接策略定义 |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnectionsApproval/action | 审批 PrivateEndpointConnections |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/read | 读取专用链接代理 |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/delete | 删除专用链接代理 |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/validate/action | 验证专用链接代理 |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnections/read | 读取专用终结点连接 |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnections/write | 创建和更新专用终结点连接 |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnections/delete | 删除专用终结点连接 |
> | microsoft.aadiam/privateLinkForAzureAD/privateLinkResources/read | 读取专用链接资源 |
> | microsoft.aadiam/privateLinkForAzureAD/privateLinkResources/write | 创建和更新专用链接资源 |
> | microsoft.aadiam/privateLinkForAzureAD/privateLinkResources/delete | 删除专用链接资源 |
> | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | 获取租户的可用日志 |

### <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

Azure 服务：[Azure Active Directory](../active-directory/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ADHybridHealthService/configuration/action | 更新租户配置。 |
> | Microsoft.ADHybridHealthService/services/action | 更新租户中的服务实例。 |
> | Microsoft.ADHybridHealthService/addsservices/action | 为租户创建一个新林。 |
> | Microsoft.ADHybridHealthService/register/action | 注册 ADHybrid 运行状况服务资源提供程序并启用 ADHybrid 运行状况服务资源的创建。 |
> | Microsoft.ADHybridHealthService/unregister/action | 取消注册 ADHybrid 运行状况服务资源提供程序的订阅。 |
> | Microsoft.ADHybridHealthService/addsservices/write | 创建或更新租户的 ADDomainService 实例。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | 向服务添加服务器实例。 |
> | Microsoft.ADHybridHealthService/addsservices/read | 获取指定服务名称的服务详细信息。 |
> | Microsoft.ADHybridHealthService/addsservices/delete | 删除服务及其服务器和运行状况数据。 |
> | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 获取指定的服务名称的所有服务器。 |
> | Microsoft.ADHybridHealthService/addsservices/alerts/read | 获取有关林的警报详细信息，例如警报 ID、警报引发日期、上次检测到的警报、警报说明、上次更新时间、警报级别、警报状态、警报故障排除链接，等等。 |
> | Microsoft.ADHybridHealthService/addsservices/configuration/read | 获取林的服务配置。 示例 - 林名称、功能级别、域命名主机 FSMO 角色、架构主机 FSMO 角色，等等。 |
> | Microsoft.ADHybridHealthService/addsservices/dimensions/read | 获取林的域和站点详细信息。 示例 - 运行状况、活动警报、已解决的警报、属性（如域功能级别、林、基础结构主机、PDC、RID 主机，等等）。  |
> | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | 获取林的用户首选项设置。<br>示例 - MetricCounterName，例如 ldapsuccessfulbinds、ntlmauthentications、kerberosauthentications、addsinsightsagentprivatebytes、ldapsearches。<br>UI 图表的设置等等。 |
> | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | 获取给定林的林摘要，例如林名称、此林下的域数目、站点数目和站点详细信息，等等。 |
> | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 获取给定服务的受支持指标列表。<br>例如，ADFS 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomainService 的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>ADSync 服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | 对于给定的服务，此 API 获取指标信息。<br>例如，可以使用此 API 来获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | 此 API 获取高级租户的所有已载入 ADDomainServices 的列表。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 获取指定服务名称的所有服务器的复制详细信息。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | 获取域控制器的数目及其复制错误（如果有）。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 获取给定林的完整域控制器列表以及复制详细信息。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 删除给定服务和租户的服务器。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | 在 ADDomainService 的服务器注册期间，会调用此 API 来获取正在载入的新服务器的凭据。 |
> | Microsoft.ADHybridHealthService/configuration/write | 创建租户配置。 |
> | Microsoft.ADHybridHealthService/configuration/read | 读取租户配置。 |
> | Microsoft.ADHybridHealthService/logs/read | 获取租户的代理安装和注册日志。 |
> | Microsoft.ADHybridHealthService/logs/contents/read | 获取 blob 中存储的代理安装和注册日志的内容。 |
> | Microsoft.ADHybridHealthService/operations/read | 获取系统支持的操作的列表。 |
> | Microsoft.ADHybridHealthService/reports/availabledeployments/read | 获取 DevOps 为客户事件提供支持时使用的可用区域的列表。 |
> | Microsoft.ADHybridHealthService/reports/badpassword/read | 获取 Active Directory 联合身份验证服务中的所有用户的错误密码尝试列表。 |
> | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 获取包含新排队的用于以下用途的报告作业的状态和最终结果的 Blob SAS URI：显示给定租户中每 UserId 每 IPAddress 的错误用户名/密码尝试频率。 |
> | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | 获取 DevOps 已许可租户的列表。 通常用于客户支持。 |
> | Microsoft.ADHybridHealthService/reports/isdevops/read | 获取一个值，该值指示租户是否为 DevOps 许可的租户。 |
> | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 更新所选 DevOps 租户的 userid(objectid)。 |
> | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 获取给定租户的所选部署。 |
> | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | 对于给定的租户 id，获取租户存储位置。 |
> | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | 获取将从中访问数据的地理位置。 |
> | Microsoft.ADHybridHealthService/services/write | 在租户中创建服务实例。 |
> | Microsoft.ADHybridHealthService/services/read | 读取租户中的服务实例。 |
> | Microsoft.ADHybridHealthService/services/delete | 删除租户中的服务实例。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/action | 在服务中创建或更新服务器实例。 |
> | Microsoft.ADHybridHealthService/services/alerts/read | 读取服务的警报。 |
> | Microsoft.ADHybridHealthService/services/alerts/read | 读取服务的警报。 |
> | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 对于给定的功能名称，验证某项服务是否具有使用该功能所需的所有项。 |
> | Microsoft.ADHybridHealthService/services/exporterrors/read | 获取给定同步服务的导出错误。 |
> | Microsoft.ADHybridHealthService/services/exportstatus/read | 获取给定服务的导出状态。 |
> | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 获取给定服务和服务器的警报反馈。 |
> | Microsoft.ADHybridHealthService/services/ipAddressAggregates/read | 读取试图访问服务的错误 IP。 |
> | Microsoft.ADHybridHealthService/services/ipAddressAggregateSettings/read | 读取错误 IP 的警报阈值。 |
> | Microsoft.ADHybridHealthService/services/ipAddressAggregateSettings/write | 写入错误 IP 的警报阈值。 |
> | Microsoft.ADHybridHealthService/services/metricmetadata/read | 获取给定服务的受支持指标列表。<br>例如，ADFS 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomainService 的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>ADSync 服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/read | 对于给定的服务，此 API 获取指标信息。<br>例如，可以使用此 API 来获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | 对于给定的服务，此 API 获取给定服务的指标的平均值。<br>例如，可以使用此 API 来获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | 对于给定的服务，此 API 获取给定服务的指标的聚合视图。<br>例如，可以使用此 API 来获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | 添加或更新服务的监视配置。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 获取给定服务的监视配置。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | 添加或更新服务的监视配置。 |
> | Microsoft.ADHybridHealthService/services/premiumcheck/read | 此 API 获取高级租户的所有已载入服务的列表。 |
> | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | 生成风险 IP 报告并返回指向它的 URI。 |
> | Microsoft.ADHybridHealthService/services/reports/blobUris/read | 获取过去 7 天的所有风险 IP 报告 URI。 |
> | Microsoft.ADHybridHealthService/services/reports/details/read | 获取包含过去 7 天内发生的密码不正确错误最多的前 50 位用户的报告 |
> | Microsoft.ADHybridHealthService/services/servicemembers/read | 读取服务中的服务器实例。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/delete | 删除服务中的服务器实例。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | 读取服务器的警报。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | 在服务器注册期间，会调用此 API 来获取正在载入的新服务器的凭据。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | 对于给定的服务器，此 API 获取服务器正在上传的数据类型的列表，以及每次上传的最新时间。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 获取给定同步服务的同步导出错误详细信息。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | 获取给定服务和服务成员的连接器与运行配置文件名称的列表。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | 对于给定的服务，此 API 获取指标信息。<br>例如，可以使用此 API 来获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 获取给定租户的服务配置。 |
> | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 获取给定租户的功能允许列表状态。 |

### <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Azure 服务：[Azure Active Directory B2C](../active-directory-b2c/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.AzureActiveDirectory/register/action | 注册 Microsoft.AzureActiveDirectory 资源提供程序的订阅 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/write | 创建或更新 B2C 目录资源 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/read | 查看 B2C 目录资源 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/delete | 删除 B2C 目录资源 |
> | Microsoft.AzureActiveDirectory/b2ctenants/read | 列出用户所属的所有 B2C 租户 |
> | Microsoft.AzureActiveDirectory/guestUsages/write | 创建或更新来宾使用情况资源 |
> | Microsoft.AzureActiveDirectory/guestUsages/read | 查看来宾使用情况资源 |
> | Microsoft.AzureActiveDirectory/guestUsages/delete | 删除来宾使用情况资源 |
> | Microsoft.AzureActiveDirectory/operations/read | 读取适用于 Microsoft.AzureActiveDirectory 资源提供程序的所有 API 操作 |

### <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

Azure 服务：[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ManagedIdentity/register/action | 注册托管标识资源提供程序的订阅 |
> | Microsoft.ManagedIdentity/identities/read | 获取现有的系统分配标识 |
> | Microsoft.ManagedIdentity/operations/read | 列出适用于 Microsoft.ManagedIdentity 资源提供程序的操作 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | 获取现有用户分配标识 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | 创建新的用户分配标识或更新与现有用户分配标识关联的标记 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 删除现有用户分配标识 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC 操作用于将现有用户分配标识分配给资源 |

## <a name="security"></a>安全性

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Azure 服务：[密钥保管库](../key-vault/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.KeyVault/register/action | 注册订阅 |
> | Microsoft.KeyVault/unregister/action | 取消注册订阅 |
> | Microsoft.KeyVault/checkNameAvailability/read | 检查密钥保管库名称是否有效且未被使用 |
> | Microsoft.KeyVault/deletedVaults/read | 查看软删除的密钥保管库的属性 |
> | Microsoft.KeyVault/hsmPools/read | 查看 HSM 池的属性 |
> | Microsoft.KeyVault/hsmPools/write | 创建新 HSM 池或更新现有 HSM 池的属性 |
> | Microsoft.KeyVault/hsmPools/delete | 删除 HSM 池 |
> | Microsoft.KeyVault/hsmPools/joinVault/action | 将密钥保管库加入 HSM 池 |
> | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.KeyVault 正在删除虚拟网络或子网 |
> | Microsoft.KeyVault/locations/deletedVaults/read | 查看软删除的密钥保管库的属性 |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | 清除软删除的密钥保管库 |
> | Microsoft.KeyVault/locations/operationResults/read | 检查长时间运行的操作的结果 |
> | Microsoft.KeyVault/operations/read | 列出可对 Microsoft.KeyVault 资源提供程序执行的操作 |
> | Microsoft.KeyVault/vaults/read | 查看密钥保管库的属性 |
> | Microsoft.KeyVault/vaults/write | 创建新的密钥保管库，或更新现有密钥保管库的属性。 某些属性可能需要更多的权限。 |
> | Microsoft.KeyVault/vaults/delete | 删除密钥保管库 |
> | Microsoft.KeyVault/vaults/deploy/action | 部署 Azure 资源时启用对密钥保管库中机密的访问 |
> | Microsoft.KeyVault/vaults/PrivateEndpointConnectionsApproval/action | 批准或拒绝到 Microsoft.Network 提供程序的专用终结点资源的连接 |
> | Microsoft.KeyVault/vaults/accessPolicies/write | 通过合并或替换来更新现有访问策略，或向密钥保管库添加新的访问策略。 |
> | Microsoft.KeyVault/vaults/eventGridFilters/read | 通知 Microsoft.KeyVault 正在查看 Key Vault 的 EventGrid 订阅 |
> | Microsoft.KeyVault/vaults/eventGridFilters/write | 通知 Microsoft.KeyVault 正在创建 Key Vault 的新 EventGrid 订阅 |
> | Microsoft.KeyVault/vaults/eventGridFilters/delete | 通知 Microsoft.KeyVault 正在删除 Key Vault 的 EventGrid 订阅 |
> | Microsoft.KeyVault/vaults/keys/read | 列出指定保管库中的密钥，或读取指定密钥的当前版本。 |
> | Microsoft.KeyVault/vaults/keys/write | 创建新密钥的第一个版本（如果不存在）。 如果已存在，则返回现有密钥，而不进行任何修改。 此 API 不创建后续版本，也不更新现有的密钥。 |
> | Microsoft.KeyVault/vaults/keys/versions/read | 列出指定密钥的版本，或读取密钥的指定版本。 |
> | Microsoft.KeyVault/vaults/privateEndpointConnectionProxies/read | 查看到 Microsoft.Network 提供程序的专用终结点资源的连接代理状态 |
> | Microsoft.KeyVault/vaults/privateEndpointConnectionProxies/write | 更改到 Microsoft.Network 提供程序的专用终结点资源的连接代理状态 |
> | Microsoft.KeyVault/vaults/privateEndpointConnectionProxies/delete | 删除到 Microsoft.Network 提供程序的专用终结点资源的连接代理 |
> | Microsoft.KeyVault/vaults/privateEndpointConnectionProxies/validate/action | 验证到 Microsoft.Network 提供程序的专用终结点资源的连接代理 |
> | Microsoft.KeyVault/vaults/privateEndpointConnections/read | 查看到 Microsoft.Network 提供程序的专用终结点资源的连接状态 |
> | Microsoft.KeyVault/vaults/privateEndpointConnections/write | 更改到 Microsoft.Network 提供程序的专用终结点资源的连接状态 |
> | Microsoft.KeyVault/vaults/privateEndpointConnections/delete | 删除到 Microsoft.Network 提供程序的专用终结点资源的连接 |
> | Microsoft.KeyVault/vaults/privateLinkResources/read | 获取密钥保管库的指定实例的可用专用链接资源 |
> | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | 获取资源的诊断设置 |
> | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | 创建或更新资源的诊断设置 |
> | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | 获取密钥保管库的可用日志 |
> | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | 获取密钥保管库的可用指标 |
> | Microsoft.KeyVault/vaults/secrets/read | 查看机密的属性，但不查看其值。 |
> | Microsoft.KeyVault/vaults/secrets/write | 创建新机密或更新现有机密的值。 |
> | **DataAction** | **说明** |
> | Microsoft.KeyVault/vaults/certificatecas/delete | 删除证书颁发者 |
> | Microsoft.KeyVault/vaults/certificatecas/read | 读取证书颁发者 |
> | Microsoft.KeyVault/vaults/certificatecas/write | 写入证书颁发者 |
> | Microsoft.KeyVault/vaults/certificatecontacts/write | 管理证书联系人 |
> | Microsoft.KeyVault/vaults/certificates/delete | 删除证书。 所有版本都会被删除。 |
> | Microsoft.KeyVault/vaults/certificates/read | 列出指定的 Key Vault 中的证书，或获取有关证书的信息。 |
> | Microsoft.KeyVault/vaults/certificates/backup/action | 创建证书的备份文件。 该文件可用于还原同一订阅的 Key Vault 中的证书。 可能存在限制。 |
> | Microsoft.KeyVault/vaults/certificates/purge/action | 清除证书，使其不可恢复。 |
> | Microsoft.KeyVault/vaults/certificates/update/action | 更新与给定证书关联的指定属性。 |
> | Microsoft.KeyVault/vaults/certificates/create/action | 创建新证书。 如果证书不存在，则创建第一个版本， 否则会创建一个新版本。 |
> | Microsoft.KeyVault/vaults/certificates/import/action | 导入包含私钥的现有有效证书。<br>要导入的证书可以采用 PFX 或 PEM 格式。<br>如果 Key Vault 中不存在该证书，则会使用指定的内容创建第一个版本。<br>否则，将使用指定的内容创建一个新版本。 |
> | Microsoft.KeyVault/vaults/certificates/recover/action | 恢复已删除的证书。 该操作与 Delete 操作相反。 该操作在启用了软删除的保管库中适用，必须在保留间隔内发出。 |
> | Microsoft.KeyVault/vaults/certificates/restore/action | 从 Key Vault 所生成的备份文件还原证书及其所有版本。 |
> | Microsoft.KeyVault/vaults/keys/read | 列出指定保管库中的密钥，或读取密钥的属性和公共材料。<br>对于非对称密钥，此操作会公开公钥，并提供执行公钥算法（例如加密和验证签名）的功能。<br>永远不会公开私钥和对称密钥。 |
> | Microsoft.KeyVault/vaults/keys/update/action | 更新与给定密钥关联的指定属性。 |
> | Microsoft.KeyVault/vaults/keys/create/action | 创建新密钥。 如果密钥不存在，则创建第一个版本。 否则，将使用指定的值创建一个新版本。 |
> | Microsoft.KeyVault/vaults/keys/import/action | 导入在外部创建的密钥。 如果该密钥不存在，则使用导入的材料创建第一个版本。 否则，将使用导入的材料创建一个新版本。 |
> | Microsoft.KeyVault/vaults/keys/recover/action | 恢复已删除的密钥。 该操作与 Delete 操作相反。 该操作在启用了软删除的保管库中适用，必须在保留间隔内发出。 |
> | Microsoft.KeyVault/vaults/keys/restore/action | 从 Key Vault 所生成的备份文件还原密钥及其所有版本。 |
> | Microsoft.KeyVault/vaults/keys/delete | 删除密钥。 所有版本都会被删除。 |
> | Microsoft.KeyVault/vaults/keys/backup/action | 创建密钥的备份文件。 该文件可用于还原同一订阅的 Key Vault 中的密钥。 可能存在限制。 |
> | Microsoft.KeyVault/vaults/keys/purge/action | 清除密钥，使其不可恢复。 |
> | Microsoft.KeyVault/vaults/keys/encrypt/action | 使用密钥加密纯文本。 请注意，如果密钥为非对称密钥，此操作可以由拥有读取访问权限的主体执行。 |
> | Microsoft.KeyVault/vaults/keys/decrypt/action | 使用密钥解密已加密文本。 |
> | Microsoft.KeyVault/vaults/keys/wrap/action | 使用 Key Vault 密钥包装对称密钥。 请注意，如果 Key Vault 密钥为非对称密钥，则此操作可以由拥有读取访问权限的主体执行。 |
> | Microsoft.KeyVault/vaults/keys/unwrap/action | 使用 Key Vault 密钥解包对称密钥。 |
> | Microsoft.KeyVault/vaults/keys/sign/action | 使用密钥为消息摘要（哈希）签名。 |
> | Microsoft.KeyVault/vaults/keys/verify/action | 使用密钥验证消息摘要（哈希）的签名。 请注意，如果密钥为非对称密钥，此操作可以由拥有读取访问权限的主体执行。 |
> | Microsoft.KeyVault/vaults/keys/release/action | 使用证明令牌中 KEK 的公共部分来释放密钥。 |
> | Microsoft.KeyVault/vaults/secrets/delete | 删除机密。 所有版本都会被删除。 |
> | Microsoft.KeyVault/vaults/secrets/backup/action | 创建机密的备份文件。 该文件可用于还原同一订阅的 Key Vault 中的机密。 可能存在限制。 |
> | Microsoft.KeyVault/vaults/secrets/purge/action | 清除机密，使其不可恢复。 |
> | Microsoft.KeyVault/vaults/secrets/update/action | 更新与给定机密关联的指定属性。 |
> | Microsoft.KeyVault/vaults/secrets/recover/action | 恢复已删除的机密。 该操作与 Delete 操作相反。 该操作在启用了软删除的保管库中适用，必须在保留间隔内发出。 |
> | Microsoft.KeyVault/vaults/secrets/restore/action | 从 Key Vault 所生成的备份文件还原机密及其所有版本。 |
> | Microsoft.KeyVault/vaults/secrets/readMetadata/action | 列出或查看机密的属性，但不列出或查看机密的值。 |
> | Microsoft.KeyVault/vaults/secrets/getSecret/action | 获取机密的值。 |
> | Microsoft.KeyVault/vaults/secrets/setSecret/action | 设置机密的值。 如果机密不存在，则创建第一个版本。 否则，将使用指定的值创建一个新版本。 |
> | Microsoft.KeyVault/vaults/storageaccounts/read | 读取托管存储帐户和 SAS 的定义。 |
> | Microsoft.KeyVault/vaults/storageaccounts/set/action | 创建或更新托管存储帐户的定义。 |
> | Microsoft.KeyVault/vaults/storageaccounts/delete | 删除托管存储帐户的定义。 |
> | Microsoft.KeyVault/vaults/storageaccounts/backup/action | 创建托管存储帐户及其 SAS（共享访问签名）的定义的备份文件。 |
> | Microsoft.KeyVault/vaults/storageaccounts/purge/action | 清除托管存储帐户或 SAS（共享访问签名）的软删除定义。 |
> | Microsoft.KeyVault/vaults/storageaccounts/regeneratekey/action | 重新生成托管存储帐户的访问密钥。 |
> | Microsoft.KeyVault/vaults/storageaccounts/recover/action | 恢复托管存储帐户或 SAS（共享访问签名）的软删除定义。 |
> | Microsoft.KeyVault/vaults/storageaccounts/restore/action | 从 Key Vault 所生成的备份文件还原托管存储帐户及其 SAS（共享访问签名）的定义。 |
> | Microsoft.KeyVault/vaults/storageaccounts/sas/set/action | 创建或更新托管存储帐户的 SAS（共享访问签名）定义。 |
> | Microsoft.KeyVault/vaults/storageaccounts/sas/delete | 删除托管存储帐户的 SAS（共享访问签名）定义。 |

### <a name="microsoftsecurity"></a>Microsoft.Security

Azure 服务：[安全中心](../security-center/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Security/register/action | 注册 Azure 安全中心的订阅 |
> | Microsoft.Security/unregister/action | 从 Azure 安全中心取消注册订阅 |
> | Microsoft.Security/adaptiveNetworkHardenings/read | 获取受 Azure 保护的资源的自适应网络强化建议 |
> | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | 通过在给定网络安全组上创建匹配的安全规则，强制实施给定的流量强化规则 |
> | Microsoft.Security/advancedThreatProtectionSettings/read | 获取资源的高级威胁防护设置 |
> | Microsoft.Security/advancedThreatProtectionSettings/write | 更新资源的高级威胁防护设置 |
> | Microsoft.Security/alerts/read | 获取所有可用的安全警报 |
> | Microsoft.Security/alertsSuppressionRules/read | 获取所有可用的安全警报抑制规则 |
> | Microsoft.Security/alertsSuppressionRules/write | 创建新的安全警报抑制规则或更新现有规则 |
> | Microsoft.Security/alertsSuppressionRules/delete | 删除安全警报抑制规则 |
> | Microsoft.Security/applicationWhitelistings/read | 获取应用程序允许列表 |
> | Microsoft.Security/applicationWhitelistings/write | 创建新的或更新现有的应用程序允许列表 |
> | Microsoft.Security/assessmentMetadata/read | 获取订阅中的可用安全评估元数据 |
> | Microsoft.Security/assessmentMetadata/write | 创建或更新安全评估元数据 |
> | Microsoft.Security/assessments/read | 获取订阅的安全评估 |
> | Microsoft.Security/assessments/write | 创建或更新订阅的安全评估 |
> | Microsoft.Security/automations/read | 获取范围的自动化 |
> | Microsoft.Security/automations/write | 创建或更新范围的自动化 |
> | Microsoft.Security/automations/delete | 删除范围的自动化 |
> | Microsoft.Security/automations/validate/action | 验证范围的自动化模型 |
> | Microsoft.Security/autoProvisioningSettings/read | 获取订阅的安全自动预配设置 |
> | Microsoft.Security/autoProvisioningSettings/write | 创建或更新订阅的安全自动预配设置 |
> | Microsoft.Security/complianceResults/read | 获取资源的符合性结果 |
> | Microsoft.Security/deviceSecurityGroups/write | 创建或更新 IoT 设备安全组 |
> | Microsoft.Security/deviceSecurityGroups/delete | 删除 IoT 设备安全组 |
> | Microsoft.Security/deviceSecurityGroups/read | 获取 IoT 设备安全组 |
> | Microsoft.Security/informationProtectionPolicies/read | 获取资源的信息保护策略 |
> | Microsoft.Security/informationProtectionPolicies/write | 更新资源的信息保护策略 |
> | Microsoft.Security/iotDefenderSettings/read | 获取 IoT Defender 设置 |
> | Microsoft.Security/iotDefenderSettings/write | 创建或更新 IoT Defender 设置 |
> | Microsoft.Security/iotDefenderSettings/delete | 删除 IoT Defender 设置 |
> | Microsoft.Security/iotDefenderSettings/PackageDownloads/action | 获取可下载的 IoT Defender 包信息 |
> | Microsoft.Security/iotDefenderSettings/DownloadManagerActivation/action | 下载包含订阅配额数据的管理器激活文件 |
> | Microsoft.Security/iotSecuritySolutions/write | 创建或更新 IoT 安全解决方案 |
> | Microsoft.Security/iotSecuritySolutions/delete | 删除 IoT 安全解决方案 |
> | Microsoft.Security/iotSecuritySolutions/read | 获取 IoT 安全解决方案 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 安全分析模型 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 警报类型 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 警报类型 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 警报 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 警报 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 建议类型 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 建议类型 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 建议 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取 IoT 建议 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | 获取设备 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/aggregatedAlerts/read | 获取 IoT 聚合警报 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/aggregatedAlerts/dismiss/action | 关闭 IoT 聚合警报 |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/aggregatedRecommendations/read | 获取 IoT 聚合建议 |
> | Microsoft.Security/iotSensors/read | 获取 IoT 传感器 |
> | Microsoft.Security/iotSensors/write | 创建或更新 IoT 传感器 |
> | Microsoft.Security/iotSensors/delete | 删除 IoT 传感器 |
> | Microsoft.Security/iotSensors/DownloadActivation/action | 下载 IoT 传感器的激活文件 |
> | Microsoft.Security/iotSensors/TriggerTiPackageUpdate/action | 触发威胁情报包更新 |
> | Microsoft.Security/iotSensors/DownloadResetPassword/action | 下载 IoT 传感器的重置密码文件 |
> | Microsoft.Security/iotSite/read | 获取 IoT 站点 |
> | Microsoft.Security/iotSite/write | 创建或更新 IoT 站点 |
> | Microsoft.Security/iotSite/delete | 删除 IoT 站点 |
> | Microsoft.Security/locations/read | 获取安全数据位置 |
> | Microsoft.Security/locations/alerts/read | 获取所有可用的安全警报 |
> | Microsoft.Security/locations/alerts/dismiss/action | 消除安全警报 |
> | Microsoft.Security/locations/alerts/activate/action | 激活安全警报 |
> | Microsoft.Security/locations/alerts/resolve/action | 解决安全警报 |
> | Microsoft.Security/locations/alerts/simulate/action | 模拟安全警报 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/read | 获取实时网络访问策略 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 创建新的或更新现有的实时网络访问策略 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | 删除适时网络访问策略 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | 启动适时网络访问策略请求 |
> | Microsoft.Security/locations/tasks/read | 获取所有可用的安全建议 |
> | Microsoft.Security/locations/tasks/start/action | 启用安全建议 |
> | Microsoft.Security/locations/tasks/resolve/action | 解决安全建议 |
> | Microsoft.Security/locations/tasks/activate/action | 激活安全建议 |
> | Microsoft.Security/locations/tasks/dismiss/action | 关闭安全建议 |
> | Microsoft.Security/policies/read | 获取安全策略 |
> | Microsoft.Security/policies/write | 更新安全策略 |
> | Microsoft.Security/pricings/read | 获取某一范围的定价设置 |
> | Microsoft.Security/pricings/write | 更新某一范围的定价设置 |
> | Microsoft.Security/pricings/delete | 删除某一范围的定价设置 |
> | Microsoft.Security/secureScoreControlDefinitions/read | 获取安全评分控制定义 |
> | Microsoft.Security/secureScoreControls/read | 获取订阅的计算所得安全评分控制 |
> | Microsoft.Security/secureScores/read | 获取订阅的计算所得安全评分 |
> | Microsoft.Security/secureScores/secureScoreControls/read | 获取安全评分计算的计算所得安全评分控制 |
> | Microsoft.Security/securityContacts/read | 获取安全联系信息 |
> | Microsoft.Security/securityContacts/write | 更新安全联系信息 |
> | Microsoft.Security/securityContacts/delete | 删除安全联系信息 |
> | Microsoft.Security/securitySolutions/read | 获取安全解决方案 |
> | Microsoft.Security/securitySolutions/write | 创建新的或更新现有的安全解决方案 |
> | Microsoft.Security/securitySolutions/delete | 删除安全解决方案 |
> | Microsoft.Security/securitySolutionsReferenceData/read | 获取安全解决方案引用数据 |
> | Microsoft.Security/securityStatuses/read | 获取 Azure 资源的安全运行状况 |
> | Microsoft.Security/securityStatusesSummaries/read | 获取某一范围的安全状态摘要 |
> | Microsoft.Security/serverVulnerabilityAssessments/read | 获取给定资源的服务器漏洞评估加入状态 |
> | Microsoft.Security/serverVulnerabilityAssessments/write | 在资源上创建或更新服务器漏洞评估解决方案 |
> | Microsoft.Security/serverVulnerabilityAssessments/delete | 从资源中删除服务器漏洞评估解决方案 |
> | Microsoft.Security/settings/read | 获取范围的设置 |
> | Microsoft.Security/settings/write | 更新范围的设置 |
> | Microsoft.Security/sqlVulnerabilityAssessments/baselineRules/action | 将规则结果的列表添加到基线。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/baselineRules/read | 返回数据库的基线（已添加到基线的所有规则）或获取指定规则 ID 的规则基线结果。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/baselineRules/write | 更改规则基线结果。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/baselineRules/delete | 从基线中删除规则结果。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/scans/read | 返回漏洞评估扫描记录的列表，或获取指定扫描 ID 的扫描记录。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/scans/scanResults/read | 返回漏洞评估规则结果的列表，或获取指定规则 ID 的规则结果。 |
> | Microsoft.Security/tasks/read | 获取所有可用的安全建议 |
> | Microsoft.Security/webApplicationFirewalls/read | 获取 Web 应用程序防火墙 |
> | Microsoft.Security/webApplicationFirewalls/write | 创建新的或更新现有的 Web 应用程序防火墙 |
> | Microsoft.Security/webApplicationFirewalls/delete | 删除 Web 应用程序防火墙 |
> | Microsoft.Security/workspaceSettings/read | 获取工作区设置 |
> | Microsoft.Security/workspaceSettings/write | 更新工作区设置 |
> | Microsoft.Security/workspaceSettings/delete | 删除工作区设置 |
> | Microsoft.Security/workspaceSettings/connect/action | 更改工作区设置重新连接设置 |

### <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

Azure 服务：Microsoft Monitoring Insights

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.SecurityGraph/diagnosticsettings/write | 写入诊断设置 |
> | Microsoft.SecurityGraph/diagnosticsettings/read | 读取诊断设置 |
> | Microsoft.SecurityGraph/diagnosticsettings/delete | 删除诊断设置 |
> | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 读取诊断设置类别 |

### <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

Azure 服务：[Azure Sentinel](../sentinel/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.SecurityInsights/register/action | 将订阅注册到 Azure Sentinel |
> | Microsoft.SecurityInsights/unregister/action | 从 Azure Sentinel 取消注册订阅 |
> | Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action | 检查用户授权和许可证 |
> | Microsoft.SecurityInsights/Aggregations/read | 获取聚合信息 |
> | Microsoft.SecurityInsights/alertRules/read | 获取警报规则 |
> | Microsoft.SecurityInsights/alertRules/write | 更新警报规则 |
> | Microsoft.SecurityInsights/alertRules/delete | 删除警报规则 |
> | Microsoft.SecurityInsights/alertRules/actions/read | 获取警报规则的响应操作 |
> | Microsoft.SecurityInsights/alertRules/actions/write | 更新警报规则的响应操作 |
> | Microsoft.SecurityInsights/alertRules/actions/delete | 删除警报规则的响应操作 |
> | Microsoft.SecurityInsights/automationRules/read | 获取自动化规则 |
> | Microsoft.SecurityInsights/automationRules/write | 更新自动化规则 |
> | Microsoft.SecurityInsights/automationRules/delete | 删除自动化规则 |
> | Microsoft.SecurityInsights/Bookmarks/read | 获取书签 |
> | Microsoft.SecurityInsights/Bookmarks/write | 更新书签 |
> | Microsoft.SecurityInsights/Bookmarks/delete | 删除书签 |
> | Microsoft.SecurityInsights/Bookmarks/expand/action | 按特定的扩展获取实体的相关实体 |
> | Microsoft.SecurityInsights/bookmarks/relations/read | 获取书签关系 |
> | Microsoft.SecurityInsights/bookmarks/relations/write | 更新书签关系 |
> | Microsoft.SecurityInsights/bookmarks/relations/delete | 删除书签关系 |
> | Microsoft.SecurityInsights/cases/read | 获取案例 |
> | Microsoft.SecurityInsights/cases/write | 更新案例 |
> | Microsoft.SecurityInsights/cases/delete | 删除案例 |
> | Microsoft.SecurityInsights/cases/comments/read | 获取案例注释 |
> | Microsoft.SecurityInsights/cases/comments/write | 创建案例注释 |
> | Microsoft.SecurityInsights/cases/investigations/read | 获取案例调查 |
> | Microsoft.SecurityInsights/cases/investigations/write | 更新案例的元数据 |
> | Microsoft.SecurityInsights/dataConnectors/read | 获取数据连接器 |
> | Microsoft.SecurityInsights/dataConnectors/write | 更新数据连接器 |
> | Microsoft.SecurityInsights/dataConnectors/delete | 删除数据连接器 |
> | Microsoft.SecurityInsights/enrichment/domain/whois/read | 获取域的 whois 扩充 |
> | Microsoft.SecurityInsights/enrichment/ip/geodata/read | 获取 IP 的 geodata 扩充 |
> | Microsoft.SecurityInsights/entities/read | 获取 Sentinel 实体关系图 |
> | Microsoft.SecurityInsights/entities/gettimeline/action | 获取特定范围的实体时间线 |
> | Microsoft.SecurityInsights/entities/getInsights/action | 获取特定范围的实体见解 |
> | Microsoft.SecurityInsights/entities/relations/read | 获取实体与相关资源之间的关系 |
> | Microsoft.SecurityInsights/entities/relations/write | 更新实体与相关资源之间的关系 |
> | Microsoft.SecurityInsights/entities/relations/delete | 删除实体与相关资源之间的关系 |
> | Microsoft.SecurityInsights/entityQueries/read | 获取实体的调查扩展 |
> | Microsoft.SecurityInsights/incidents/read | 获取事件 |
> | Microsoft.SecurityInsights/incidents/write | 更新事件 |
> | Microsoft.SecurityInsights/incidents/delete | 删除事件 |
> | Microsoft.SecurityInsights/incidents/createTeam/action | 创建一个 Microsoft 团队，通过在参与者之间共享信息和见解来调查事件 |
> | Microsoft.SecurityInsights/incidents/comments/read | 获取事件注释 |
> | Microsoft.SecurityInsights/incidents/comments/write | 创建有关事件的注释 |
> | Microsoft.SecurityInsights/incidents/comments/delete | 删除有关事件的注释 |
> | Microsoft.SecurityInsights/incidents/relations/read | 获取事件与相关资源之间的关系 |
> | Microsoft.SecurityInsights/incidents/relations/write | 更新事件与相关资源之间的关系 |
> | Microsoft.SecurityInsights/incidents/relations/delete | 删除事件与相关资源之间的关系 |
> | Microsoft.SecurityInsights/Metadata/read | 读取 Sentinel 内容的元数据。 |
> | Microsoft.SecurityInsights/Metadata/write | 写入 Sentinel 内容的元数据。 |
> | Microsoft.SecurityInsights/Metadata/delete | 删除 Sentinel 内容的元数据。 |
> | Microsoft.SecurityInsights/officeConsents/read | 获取 Microsoft Office 的同意 |
> | Microsoft.SecurityInsights/officeConsents/delete | 删除 Microsoft Office 的同意 |
> | Microsoft.SecurityInsights/onboardingStates/read | 获取加入状态 |
> | Microsoft.SecurityInsights/onboardingStates/write | 更新加入状态 |
> | Microsoft.SecurityInsights/onboardingStates/delete | 删除加入状态 |
> | Microsoft.SecurityInsights/operations/read | Get 操作 |
> | Microsoft.SecurityInsights/settings/read | 获取设置 |
> | Microsoft.SecurityInsights/settings/write | 更新设置 |
> | Microsoft.SecurityInsights/settings/delete | 删除设置 |
> | Microsoft.SecurityInsights/SourceControls/read | 读取 SourceControls |
> | Microsoft.SecurityInsights/SourceControls/write | 写入 SourceControls |
> | Microsoft.SecurityInsights/SourceControls/delete | 删除 SourceControls |
> | Microsoft.SecurityInsights/threatintelligence/read | 获取威胁情报 |
> | Microsoft.SecurityInsights/threatintelligence/write | 更新威胁情报 |
> | Microsoft.SecurityInsights/threatintelligence/delete | 删除威胁情报 |
> | Microsoft.SecurityInsights/threatintelligence/query/action | 查询威胁情报 |
> | Microsoft.SecurityInsights/threatintelligence/metrics/action | 收集威胁情报指标 |
> | Microsoft.SecurityInsights/threatintelligence/bulkDelete/action | 批量删除威胁情报 |
> | Microsoft.SecurityInsights/threatintelligence/bulkTag/action | 批量标记威胁情报 |
> | Microsoft.SecurityInsights/threatintelligence/createIndicator/action | 创建威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/queryIndicators/action | 查询威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/write | 更新威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/delete | 删除威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/query/action | 查询威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/metrics/action | 获取威胁情报指示器指标 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/bulkDelete/action | 批量删除威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/bulkTag/action | 批量标记威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/read | 获取威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/appendTags/action | 将标记追加到威胁情报指示器 |
> | Microsoft.SecurityInsights/threatintelligence/indicators/replaceTags/action | 替换威胁情报指示器的标记 |
> | Microsoft.SecurityInsights/threatintelligence/metrics/read | 收集威胁情报指标 |
> | Microsoft.SecurityInsights/Watchlists/read | 获取播放列表 |
> | Microsoft.SecurityInsights/Watchlists/write | 创建播放列表 |
> | Microsoft.SecurityInsights/Watchlists/delete | 删除播放列表 |

## <a name="devops"></a>DevOps

### <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

Azure 服务：[Azure 实验室服务](../lab-services/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DevTestLab/register/action | 注册订阅 |
> | Microsoft.DevTestLab/labCenters/delete | 删除实验室中心。 |
> | Microsoft.DevTestLab/labCenters/read | 读取实验室中心。 |
> | Microsoft.DevTestLab/labCenters/write | 添加或修改实验室中心。 |
> | Microsoft.DevTestLab/labs/delete | 删除实验室。 |
> | Microsoft.DevTestLab/labs/read | 读取实验室。 |
> | Microsoft.DevTestLab/labs/write | 添加或修改实验室。 |
> | Microsoft.DevTestLab/labs/ListVhds/action | 列出可用于创建自定义映像的磁盘映像。 |
> | Microsoft.DevTestLab/labs/GenerateUploadUri/action | 生成用于将自定义磁盘映像上传到实验室的 URI。 |
> | Microsoft.DevTestLab/labs/CreateEnvironment/action | 在实验室中创建虚拟机。 |
> | Microsoft.DevTestLab/labs/ClaimAnyVm/action | 在实验室中声明随机可声明的虚拟机。 |
> | Microsoft.DevTestLab/labs/ExportResourceUsage/action | 将实验室资源使用情况导出到存储帐户 |
> | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 将虚拟机导入到不同实验室。 |
> | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | 确保当前用户在实验室中存在有效的配置文件。 |
> | Microsoft.DevTestLab/labs/artifactSources/delete | 删除项目源。 |
> | Microsoft.DevTestLab/labs/artifactSources/read | 读取项目源。 |
> | Microsoft.DevTestLab/labs/artifactSources/write | 添加或修改项目源。 |
> | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | 读取 Azure Resource Manager 模板。 |
> | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | 读取项目。 |
> | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 为给定的项目生成 Azure 资源管理器模板，将所需的文件上传到存储帐户，然后验证生成的项目。 |
> | Microsoft.DevTestLab/labs/costs/read | 读取成本。 |
> | Microsoft.DevTestLab/labs/costs/write | 添加或修改成本。 |
> | Microsoft.DevTestLab/labs/customImages/delete | 删除自定义映像。 |
> | Microsoft.DevTestLab/labs/customImages/read | 读取自定义映像。 |
> | Microsoft.DevTestLab/labs/customImages/write | 添加或修改自定义映像。 |
> | Microsoft.DevTestLab/labs/formulas/delete | 删除公式。 |
> | Microsoft.DevTestLab/labs/formulas/read | 读取公式。 |
> | Microsoft.DevTestLab/labs/formulas/write | 添加或修改公式。 |
> | Microsoft.DevTestLab/labs/galleryImages/read | 读取库映像。 |
> | Microsoft.DevTestLab/labs/notificationChannels/delete | 删除通知通道。 |
> | Microsoft.DevTestLab/labs/notificationChannels/read | 读取通知通道。 |
> | Microsoft.DevTestLab/labs/notificationChannels/write | 添加或修改通知通道。 |
> | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 将通知发送到提供的通道。 |
> | Microsoft.DevTestLab/labs/policySets/read | 读取策略集。 |
> | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | 评估实验室策略。 |
> | Microsoft.DevTestLab/labs/policySets/policies/delete | 删除策略。 |
> | Microsoft.DevTestLab/labs/policySets/policies/read | 读取策略。 |
> | Microsoft.DevTestLab/labs/policySets/policies/write | 添加或修改策略。 |
> | Microsoft.DevTestLab/labs/schedules/delete | 删除计划。 |
> | Microsoft.DevTestLab/labs/schedules/read | 读取计划。 |
> | Microsoft.DevTestLab/labs/schedules/write | 添加或修改计划。 |
> | Microsoft.DevTestLab/labs/schedules/Execute/action | 执行计划。 |
> | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 列出所有适用的计划 |
> | Microsoft.DevTestLab/labs/secrets/delete | 删除实验室机密。 |
> | Microsoft.DevTestLab/labs/secrets/read | 读取实验室机密。 |
> | Microsoft.DevTestLab/labs/secrets/write | 添加或修改实验室机密。 |
> | Microsoft.DevTestLab/labs/serviceRunners/delete | 删除服务运行程序。 |
> | Microsoft.DevTestLab/labs/serviceRunners/read | 读取服务运行程序。 |
> | Microsoft.DevTestLab/labs/serviceRunners/write | 添加或修改服务运行程序。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/delete | 删除共享库。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/read | 读取共享库。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/write | 添加或修改共享库。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | 删除共享映像。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | 读取共享映像。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | 添加或修改共享映像。 |
> | Microsoft.DevTestLab/labs/users/delete | 删除用户配置文件。 |
> | Microsoft.DevTestLab/labs/users/read | 读取用户配置文件。 |
> | Microsoft.DevTestLab/labs/users/write | 添加或修改用户配置文件。 |
> | Microsoft.DevTestLab/labs/users/disks/delete | 删除磁盘。 |
> | Microsoft.DevTestLab/labs/users/disks/read | 读取磁盘。 |
> | Microsoft.DevTestLab/labs/users/disks/write | 添加或修改磁盘。 |
> | Microsoft.DevTestLab/labs/users/disks/Attach/action | 在虚拟机上附加和创建磁盘的租约。 |
> | Microsoft.DevTestLab/labs/users/disks/Detach/action | 分离并破坏已附加到虚拟机的磁盘的租约。 |
> | Microsoft.DevTestLab/labs/users/environments/delete | 删除环境。 |
> | Microsoft.DevTestLab/labs/users/environments/read | 读取环境。 |
> | Microsoft.DevTestLab/labs/users/environments/write | 添加或修改环境。 |
> | Microsoft.DevTestLab/labs/users/secrets/delete | 删除机密。 |
> | Microsoft.DevTestLab/labs/users/secrets/read | 读取机密。 |
> | Microsoft.DevTestLab/labs/users/secrets/write | 添加或修改机密。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | 删除 Service Fabric。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/read | 读取 Service Fabric。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/write | 添加或修改 Service Fabric。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | 启动 Service Fabric。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | 停止 Service Fabric |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 列出适用的启动/停止计划（如果有）。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | 删除计划。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | 读取计划。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | 添加或修改计划。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | 执行计划。 |
> | Microsoft.DevTestLab/labs/virtualMachines/delete | 删除虚拟机。 |
> | Microsoft.DevTestLab/labs/virtualMachines/read | 读取虚拟机。 |
> | Microsoft.DevTestLab/labs/virtualMachines/write | 添加或修改虚拟机。 |
> | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 将新的或现有的数据磁盘附加到虚拟机。 |
> | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 将项目应用到虚拟机。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | 获得现有虚拟机的所有权 |
> | Microsoft.DevTestLab/labs/virtualMachines/ClearArtifactResults/action | 清除虚拟机的项目结果。 |
> | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 从虚拟机中分离指定的磁盘。 |
> | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 获取一个字符串，该字符串表示虚拟机的 RDP 文件内容 |
> | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 列出适用的启动/停止计划（如果有）。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 重新部署虚拟机 |
> | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 调整虚拟机大小。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 重新启动虚拟机。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 启动虚拟机。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 停止虚拟机 |
> | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 转移附加到虚拟机的所有数据磁盘，该虚拟机将规当前用户所有。 |
> | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 释放现有虚拟机的所有权 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | 删除计划。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | 读取计划。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | 添加或修改计划。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | 执行计划。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/delete | 创建虚拟网络。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/read | 读取虚拟网络。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/write | 添加或修改虚拟网络。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/delete | 删除守护主机。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/read | 读取守护主机。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/write | 添加或修改守护主机。 |
> | Microsoft.DevTestLab/labs/vmPools/delete | 删除虚拟机池。 |
> | Microsoft.DevTestLab/labs/vmPools/read | 读取虚拟机池。 |
> | Microsoft.DevTestLab/labs/vmPools/write | 添加或修改虚拟机池。 |
> | Microsoft.DevTestLab/locations/operations/read | 读取操作。 |
> | Microsoft.DevTestLab/schedules/delete | 删除计划。 |
> | Microsoft.DevTestLab/schedules/read | 读取计划。 |
> | Microsoft.DevTestLab/schedules/write | 添加或修改计划。 |
> | Microsoft.DevTestLab/schedules/Execute/action | 执行计划。 |
> | Microsoft.DevTestLab/schedules/Retarget/action | 更新计划的目标资源 ID。 |

### <a name="microsoftlabservices"></a>Microsoft.LabServices

Azure 服务：[Azure 实验室服务](../lab-services/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.LabServices/register/action | 向实验室服务提供商注册订阅并启用实验室创建。 |
> | Microsoft.LabServices/unregister/action | 向实验室服务提供商取消注册订阅。 |
> | Microsoft.LabServices/labAccounts/delete | 删除实验室帐户。 |
> | Microsoft.LabServices/labAccounts/read | 读取实验室帐户。 |
> | Microsoft.LabServices/labAccounts/write | 添加或修改实验室帐户。 |
> | Microsoft.LabServices/labAccounts/CreateLab/action | 在实验室帐户中创建实验室。 |
> | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | 获取实验室帐户下配置的每个大小类别的区域可用性信息 |
> | Microsoft.LabServices/labAccounts/GetPricingAndAvailability/action | 获取实验室帐户的大小、地理位置和操作系统组合的定价与可用性。 |
> | Microsoft.LabServices/labAccounts/GetRestrictionsAndUsage/action | 获取此订阅的核心限制和用量 |
> | Microsoft.LabServices/labAccounts/galleryImages/delete | 删除库映像。 |
> | Microsoft.LabServices/labAccounts/galleryImages/read | 读取库映像。 |
> | Microsoft.LabServices/labAccounts/galleryImages/write | 添加或修改库映像。 |
> | Microsoft.LabServices/labAccounts/labs/delete | 删除实验室。 |
> | Microsoft.LabServices/labAccounts/labs/read | 读取实验室。 |
> | Microsoft.LabServices/labAccounts/labs/write | 添加或修改实验室。 |
> | Microsoft.LabServices/labAccounts/labs/AddUsers/action | 将用户添加到实验室 |
> | Microsoft.LabServices/labAccounts/labs/SendEmail/action | 发送包含实验室注册链接的电子邮件 |
> | Microsoft.LabServices/labAccounts/labs/GetLabPricingAndAvailability/action | 获取此实验室的每个实验室单位的定价，以及指示此实验室是否可纵向扩展的可用性。 |
> | Microsoft.LabServices/labAccounts/labs/SyncUserList/action | 将来自 AAD 组的更改同步到用户列表 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 删除环境设置。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 读取环境设置。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 添加或修改环境设置。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | 基于实验室/环境设置的当前状态设置/取消设置环境设置的所需资源。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | 通过启动模板内的所有资源来启动模板。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | 通过停止模板内的所有资源来停止模板。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | 将当前模板映像保存到实验室帐户中的共享库 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | 重置模板虚拟机上的密码。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 删除环境。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 读取环境。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 通过启动环境内的所有资源来启动环境。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 通过停止环境内的所有资源来停止环境。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 在环境中重置用户密码 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | 删除计划。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | 读取计划。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | 添加或修改计划。 |
> | Microsoft.LabServices/labAccounts/labs/users/delete | 删除用户。 |
> | Microsoft.LabServices/labAccounts/labs/users/read | 读取用户。 |
> | Microsoft.LabServices/labAccounts/labs/users/write | 添加或修改用户。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/delete | 删除 sharedgalleries。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/read | 读取 sharedgalleries。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/write | 添加或修改 sharedgalleries。 |
> | Microsoft.LabServices/labAccounts/sharedImages/delete | 删除 sharedimages。 |
> | Microsoft.LabServices/labAccounts/sharedImages/read | 读取 sharedimages。 |
> | Microsoft.LabServices/labAccounts/sharedImages/write | 添加或修改 sharedimages。 |
> | Microsoft.LabServices/labPlans/read | 获取实验室计划的属性。 |
> | Microsoft.LabServices/labPlans/write | 新建实验室计划或更新现有计划。 |
> | Microsoft.LabServices/labPlans/delete | 删除实验室计划。 |
> | Microsoft.LabServices/labPlans/saveImage/action | 从附加到实验室计划的库中的虚拟机创建映像。 |
> | Microsoft.LabServices/labPlans/images/read | 获取映像的属性。 |
> | Microsoft.LabServices/labPlans/images/write | 启用或禁用市场或库映像。 |
> | Microsoft.LabServices/labs/read | 获取实验室的属性。 |
> | Microsoft.LabServices/labs/write | 新建实验室或更新现有的实验室。 |
> | Microsoft.LabServices/labs/delete | 删除实验室及其所有用户、计划和虚拟机。 |
> | Microsoft.LabServices/labs/publish/action | 通过将模板虚拟机的映像传播到实验室中的所有虚拟机来发布实验室。 |
> | Microsoft.LabServices/labs/syncGroup/action | 更新分配到实验室的 Active Directory 组中用户的列表。 |
> | Microsoft.LabServices/labs/schedules/read | 获取计划的属性。 |
> | Microsoft.LabServices/labs/schedules/write | 新建计划或更新现有的计划。 |
> | Microsoft.LabServices/labs/schedules/delete | 删除计划。 |
> | Microsoft.LabServices/labs/users/read | 获取用户的属性。 |
> | Microsoft.LabServices/labs/users/write | 新建用户或更新现有的用户。 |
> | Microsoft.LabServices/labs/users/delete | 删除用户。 |
> | Microsoft.LabServices/labs/users/invite/action | 向用户发送电子邮件以邀请其加入实验室。 |
> | Microsoft.LabServices/labs/virtualMachines/read | 获取虚拟机的属性。 |
> | Microsoft.LabServices/labs/virtualMachines/start/action | 启动虚拟机。 |
> | Microsoft.LabServices/labs/virtualMachines/stop/action | 停止和释放虚拟机。 |
> | Microsoft.LabServices/labs/virtualMachines/reimage/action | 将虚拟机重新映像到上次发布的映像。 |
> | Microsoft.LabServices/labs/virtualMachines/redeploy/action | 将虚拟机重新部署到其他计算节点。 |
> | Microsoft.LabServices/labs/virtualMachines/resetPassword/action | 重置虚拟机上本地用户的密码。 |
> | Microsoft.LabServices/locations/operationResults/read | 获取异步操作的属性和状态。 |
> | Microsoft.LabServices/locations/operations/read | 读取操作。 |
> | Microsoft.LabServices/skus/read | 获取实验室服务 SKU 的属性。 |
> | Microsoft.LabServices/users/Register/action | 将用户注册到托管的实验室 |
> | Microsoft.LabServices/users/ListAllEnvironments/action | 列出用户的所有环境 |
> | Microsoft.LabServices/users/StartEnvironment/action | 通过启动环境内的所有资源来启动环境。 |
> | Microsoft.LabServices/users/StopEnvironment/action | 通过停止环境内的所有资源来停止环境。 |
> | Microsoft.LabServices/users/ResetPassword/action | 在环境中重置用户密码 |
> | Microsoft.LabServices/users/UserSettings/action | 更新并返回个人用户设置。 |
> | **DataAction** | **说明** |
> | Microsoft.LabServices/labPlans/createLab/action | 从实验室计划创建新实验室。 |

### <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

Azure 服务：[Azure DevOps](/azure/devops/)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.VisualStudio/Register/Action | 使用 Microsoft.VisualStudio 提供程序注册 Azure 订阅 |
> | Microsoft.VisualStudio/Account/Write | 设置帐户 |
> | Microsoft.VisualStudio/Account/Delete | 删除帐户 |
> | Microsoft.VisualStudio/Account/Read | 读取帐户 |
> | Microsoft.VisualStudio/Account/Extension/Read | 读取帐户/扩展 |
> | Microsoft.VisualStudio/Account/Project/Read | 读取帐户/项目 |
> | Microsoft.VisualStudio/Account/Project/Write | 设置帐户/项目 |
> | Microsoft.VisualStudio/Extension/Write | 设置扩展 |
> | Microsoft.VisualStudio/Extension/Delete | 删除扩展 |
> | Microsoft.VisualStudio/Extension/Read | 读取扩展 |
> | Microsoft.VisualStudio/Project/Write | 设置项目 |
> | Microsoft.VisualStudio/Project/Delete | 删除项目 |
> | Microsoft.VisualStudio/Project/Read | 读取项目 |

## <a name="migrate"></a>迁移

### <a name="microsoftmigrate"></a>Microsoft.Migrate

Azure 服务：[Azure Migrate](../migrate/migrate-services-overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Migrate/register/action | 订阅注册操作 |
> | Microsoft.Migrate/register/action | 使用 Microsoft.Migrate 资源提供程序注册订阅 |
> | Microsoft.Migrate/unregister/action | 使用 Microsoft.Migrate 资源提供程序注销订阅 |
> | Microsoft.Migrate/assessmentprojects/read | 获取评估项目的属性 |
> | Microsoft.Migrate/assessmentprojects/write | 创建新的评估项目，或更新现有的评估项目 |
> | Microsoft.Migrate/assessmentprojects/delete | 删除评估项目 |
> | Microsoft.Migrate/assessmentprojects/assessmentOptions/read | 获取给定位置中提供的评估选项 |
> | Microsoft.Migrate/assessmentprojects/assessments/read | 列出项目内的评估 |
> | Microsoft.Migrate/assessmentprojects/assessmentsSummary/read | 获取在给定位置可用的评估摘要 |
> | Microsoft.Migrate/assessmentprojects/avsAssessmentOptions/read | 获取在给定位置可用的 AVS 评估选项 |
> | Microsoft.Migrate/assessmentprojects/groups/read | 获取组的属性 |
> | Microsoft.Migrate/assessmentprojects/groups/write | 创建新组或更新现有组 |
> | Microsoft.Migrate/assessmentprojects/groups/delete | 删除组 |
> | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | 通过添加或删除计算机来更新组 |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/read | 获取评估的属性 |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/write | 创建新评估或更新现有评估 |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | 删除评估 |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | 下载评估报告的 URL |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | 获取已评估计算机的属性 |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/read | 获取 AVS 评估的属性 |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/write | 创建新的 AVS 评估或更新现有的 AVS 评估 |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/delete | 删除 AVS 评估 |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/downloadurl/action | 下载 AVS 评估报告的 URL |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/avsassessedmachines/read | 获取经过 AVS 评估的计算机的属性 |
> | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | 获取 HyperV 收集器的属性 |
> | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | 创建新的 HyperV 收集器，或更新现有的 HyperV 收集器 |
> | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | 删除 HyperV 收集器 |
> | Microsoft.Migrate/assessmentprojects/importcollectors/read | 获取 Import 收集器的属性 |
> | Microsoft.Migrate/assessmentprojects/importcollectors/write | 创建新的或更新现有的 Import 收集器 |
> | Microsoft.Migrate/assessmentprojects/importcollectors/delete | 删除 Import 收集器 |
> | Microsoft.Migrate/assessmentprojects/machines/read | 获取计算机的属性 |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理 |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnections/write | 更新专用终结点连接 |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Migrate/assessmentprojects/privateLinkResources/read | 获取专用链接资源 |
> | Microsoft.Migrate/assessmentprojects/servercollectors/read | 获取 Server 收集器的属性 |
> | Microsoft.Migrate/assessmentprojects/servercollectors/write | 创建新的或更新现有的 Server 收集器 |
> | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | 获取 VMware 收集器的属性 |
> | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | 创建新的 VMware 收集器，或更新现有的 VMware 收集器 |
> | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | 删除 VMware 收集器 |
> | Microsoft.Migrate/locations/checknameavailability/action | 在给定位置检查给定订阅的资源名称的可用性 |
> | Microsoft.Migrate/locations/assessmentOptions/read | 获取给定位置中提供的评估选项 |
> | Microsoft.Migrate/locations/rmsOperationResults/read | 获取订阅范围内基于位置的操作的状态 |
> | Microsoft.Migrate/migrateprojects/read | 获取迁移项目的属性 |
> | Microsoft.Migrate/migrateprojects/write | 创建新的迁移项目，或更新现有的迁移项目 |
> | Microsoft.Migrate/migrateprojects/delete | 删除迁移项目 |
> | Microsoft.Migrate/migrateprojects/registerTool/action | 将工具注册到迁移项目 |
> | Microsoft.Migrate/migrateprojects/RefreshSummary/action | 刷新迁移项目摘要 |
> | Microsoft.Migrate/migrateprojects/registrationDetails/action | 提供工具注册详细信息 |
> | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | 获取数据库实例的属性 |
> | Microsoft.Migrate/migrateprojects/Databases/read | 获取数据库的属性 |
> | Microsoft.Migrate/migrateprojects/machines/read | 获取计算机的属性 |
> | Microsoft.Migrate/migrateprojects/MigrateEvents/read | 获取迁移事件的属性。 |
> | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | 删除迁移事件 |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理 |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnections/write | 更新专用终结点连接 |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.Migrate/migrateprojects/privateLinkResources/read | 获取专用链接资源 |
> | Microsoft.Migrate/migrateprojects/solutions/read | 获取迁移项目解决方案的属性 |
> | Microsoft.Migrate/migrateprojects/solutions/write | 创建新的迁移项目解决方案，或更新现有的迁移项目解决方案 |
> | Microsoft.Migrate/migrateprojects/solutions/Delete | 删除迁移项目解决方案 |
> | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 获取迁移项目解决方案配置 |
> | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | 清理迁移项目解决方案数据 |
> | Microsoft.Migrate/migrateprojects/VirtualDesktopUsers/read | 获取虚拟桌面用户的属性 |
> | Microsoft.Migrate/migrateprojects/WebServers/read | 获取 Web 服务器的属性 |
> | Microsoft.Migrate/migrateprojects/WebSites/read | 获取网站的属性 |
> | Microsoft.Migrate/moveCollections/read | 获取移动集合 |
> | Microsoft.Migrate/moveCollections/write | 创建或更新移动集合 |
> | Microsoft.Migrate/moveCollections/delete | 删除移动集合 |
> | Microsoft.Migrate/moveCollections/resolveDependencies/action | 计算、解析并验证移动集合中移动资源的依赖项 |
> | Microsoft.Migrate/moveCollections/prepare/action | 对请求正文中包含的资源集启动准备操作 |
> | Microsoft.Migrate/moveCollections/initiateMove/action | 移动请求正文中包含的资源集 |
> | Microsoft.Migrate/moveCollections/discard/action | 放弃请求正文中包含的资源集 |
> | Microsoft.Migrate/moveCollections/commit/action | 提交请求正文中包含的资源集 |
> | Microsoft.Migrate/moveCollections/bulkRemove/action | 从移动集合中删除请求正文中包含的移动资源集 |
> | Microsoft.Migrate/moveCollections/moveResources/read | 获取移动集合中的所有移动资源或某个移动资源 |
> | Microsoft.Migrate/moveCollections/moveResources/write | 创建或更新某个移动资源 |
> | Microsoft.Migrate/moveCollections/moveResources/delete | 从移动集合中删除某个移动资源 |
> | Microsoft.Migrate/moveCollections/operations/read | 获取操作的状态 |
> | Microsoft.Migrate/moveCollections/requiredFor/read | 获取将使用查询参数中所传递资源的资源 |
> | Microsoft.Migrate/moveCollections/unresolvedDependencies/read | 获取移动集合中未解析的依赖项的列表 |
> | Microsoft.Migrate/Operations/read | 列出适用于 Microsoft.Migrate 资源提供程序的操作 |
> | Microsoft.Migrate/projects/read | 获取项目的属性 |
> | Microsoft.Migrate/projects/write | 创建新项目或更新现有项目 |
> | Microsoft.Migrate/projects/delete | 删除项目 |
> | Microsoft.Migrate/projects/keys/action | 获取项目的共享密钥 |
> | Microsoft.Migrate/projects/assessments/read | 列出项目内的评估 |
> | Microsoft.Migrate/projects/groups/read | 获取组的属性 |
> | Microsoft.Migrate/projects/groups/write | 创建新组或更新现有组 |
> | Microsoft.Migrate/projects/groups/delete | 删除组 |
> | Microsoft.Migrate/projects/groups/assessments/read | 获取评估的属性 |
> | Microsoft.Migrate/projects/groups/assessments/write | 创建新评估或更新现有评估 |
> | Microsoft.Migrate/projects/groups/assessments/delete | 删除评估 |
> | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 下载评估报告的 URL |
> | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 获取已评估计算机的属性 |
> | Microsoft.Migrate/projects/machines/read | 获取计算机的属性 |

### <a name="microsoftoffazure"></a>Microsoft.OffAzure

Azure 服务：[Azure Migrate](../migrate/migrate-services-overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.OffAzure/register/action | 订阅注册操作 |
> | Microsoft.OffAzure/unregister/action | 使用 Microsoft.Migrate 资源提供程序注销订阅 |
> | Microsoft.OffAzure/register/action | 使用 Microsoft.OffAzure 资源提供程序注册订阅 |
> | Microsoft.OffAzure/unregister/action | 使用 Microsoft.Migrate 资源提供程序注销订阅 |
> | Microsoft.OffAzure/register/action | 使用 Microsoft.OffAzure 资源提供程序注册订阅 |
> | Microsoft.OffAzure/unregister/action | 使用 Microsoft.Migrate 资源提供程序注销订阅 |
> | Microsoft.OffAzure/register/action | 使用 Microsoft.OffAzure 资源提供程序注册订阅 |
> | Microsoft.OffAzure/unregister/action | 使用 Microsoft.Migrate 资源提供程序注销订阅 |
> | Microsoft.OffAzure/register/action | 使用 Microsoft.OffAzure 资源提供程序注册订阅 |
> | Microsoft.OffAzure/unregister/action | 使用 Microsoft.Migrate 资源提供程序注销订阅 |
> | Microsoft.OffAzure/register/action | 使用 Microsoft.OffAzure 资源提供程序注册订阅 |
> | Microsoft.OffAzure/HyperVSites/read | 获取 Hyper-V 站点的属性 |
> | Microsoft.OffAzure/HyperVSites/write | 创建或更新 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/delete | 删除 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 刷新 Hyper-V 站点中的对象 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/read | 获取 Hyper-V 站点的属性 |
> | Microsoft.OffAzure/HyperVSites/write | 创建或更新 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/delete | 删除 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 刷新 Hyper-V 站点中的对象 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/read | 获取 Hyper-V 站点的属性 |
> | Microsoft.OffAzure/HyperVSites/write | 创建或更新 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/delete | 删除 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 刷新 Hyper-V 站点中的对象 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/read | 获取 Hyper-V 站点的属性 |
> | Microsoft.OffAzure/HyperVSites/write | 创建或更新 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/delete | 删除 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 刷新 Hyper-V 站点中的对象 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/read | 获取 Hyper-V 站点的属性 |
> | Microsoft.OffAzure/HyperVSites/write | 创建或更新 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/delete | 删除 Hyper-V 站点 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 刷新 Hyper-V 站点中的对象 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 获取 Hyper-V 群集的属性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 创建或更新 Hyper-V 群集 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 获取 Hyper-V 群集的属性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 创建或更新 Hyper-V 群集 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 获取 Hyper-V 群集的属性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 创建或更新 Hyper-V 群集 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 获取 Hyper-V 群集的属性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 创建或更新 Hyper-V 群集 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 获取 Hyper-V 群集的属性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 创建或更新 Hyper-V 群集 |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | 获取 Hyper-V 资源的运行状况摘要 |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | 获取 Hyper-V 资源的运行状况摘要 |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | 获取 Hyper-V 资源的运行状况摘要 |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | 获取 Hyper-V 资源的运行状况摘要 |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | 获取 Hyper-V 资源的运行状况摘要 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 获取 Hyper-V 主机的属性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 创建或更新 Hyper-V 主机 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 获取 Hyper-V 主机的属性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 创建或更新 Hyper-V 主机 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 获取 Hyper-V 主机的属性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 创建或更新 Hyper-V 主机 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 获取 Hyper-V 主机的属性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 创建或更新 Hyper-V 主机 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 获取 Hyper-V 主机的属性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 创建或更新 Hyper-V 主机 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 获取 Hyper-V 作业的属性 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 获取 Hyper-V 作业的属性 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 获取 Hyper-V 作业的属性 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 获取 Hyper-V 作业的属性 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 获取 Hyper-V 作业的属性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 获取 Hyper-V 计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 获取 Hyper-V 计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 获取 Hyper-V 计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 获取 Hyper-V 计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 获取 Hyper-V 计算机的属性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 获取 Hyper-V 操作状态的属性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 获取 Hyper-V 操作状态的属性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 获取 Hyper-V 操作状态的属性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 获取 Hyper-V 操作状态的属性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 获取 Hyper-V 操作状态的属性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 获取作为帐户运行的 Hyper-V 的属性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 获取作为帐户运行的 Hyper-V 的属性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 获取作为帐户运行的 Hyper-V 的属性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 获取作为帐户运行的 Hyper-V 的属性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 获取作为帐户运行的 Hyper-V 的属性 |
> | Microsoft.OffAzure/HyperVSites/summary/read | 获取 Hyper-V 站点的摘要 |
> | Microsoft.OffAzure/HyperVSites/summary/read | 获取 Hyper-V 站点的摘要 |
> | Microsoft.OffAzure/HyperVSites/summary/read | 获取 Hyper-V 站点的摘要 |
> | Microsoft.OffAzure/HyperVSites/summary/read | 获取 Hyper-V 站点的摘要 |
> | Microsoft.OffAzure/HyperVSites/summary/read | 获取 Hyper-V 站点的摘要 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 获取 Hyper-V 的使用情况 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 获取 Hyper-V 的使用情况 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 获取 Hyper-V 的使用情况 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 获取 Hyper-V 的使用情况 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 获取 Hyper-V 的使用情况 |
> | Microsoft.OffAzure/ImportSites/read | 获取导入站点的属性 |
> | Microsoft.OffAzure/ImportSites/write | 创建或更新导入站点 |
> | Microsoft.OffAzure/ImportSites/delete | 删除导入站点 |
> | Microsoft.OffAzure/ImportSites/importuri/action | 获取用于导入计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | 获取用于导出计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/read | 获取导入站点的属性 |
> | Microsoft.OffAzure/ImportSites/write | 创建或更新导入站点 |
> | Microsoft.OffAzure/ImportSites/delete | 删除导入站点 |
> | Microsoft.OffAzure/ImportSites/importuri/action | 获取用于导入计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | 获取用于导出计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/read | 获取导入站点的属性 |
> | Microsoft.OffAzure/ImportSites/write | 创建或更新导入站点 |
> | Microsoft.OffAzure/ImportSites/delete | 删除导入站点 |
> | Microsoft.OffAzure/ImportSites/importuri/action | 获取用于导入计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | 获取用于导出计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/read | 获取导入站点的属性 |
> | Microsoft.OffAzure/ImportSites/write | 创建或更新导入站点 |
> | Microsoft.OffAzure/ImportSites/delete | 删除导入站点 |
> | Microsoft.OffAzure/ImportSites/importuri/action | 获取用于导入计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | 获取用于导出计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/read | 获取导入站点的属性 |
> | Microsoft.OffAzure/ImportSites/write | 创建或更新导入站点 |
> | Microsoft.OffAzure/ImportSites/delete | 删除导入站点 |
> | Microsoft.OffAzure/ImportSites/importuri/action | 获取用于导入计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | 获取用于导出计算机 CSV 文件的 SAS URI。 |
> | Microsoft.OffAzure/ImportSites/jobs/read | 获取导入作业的属性 |
> | Microsoft.OffAzure/ImportSites/jobs/read | 获取导入作业的属性 |
> | Microsoft.OffAzure/ImportSites/jobs/read | 获取导入作业的属性 |
> | Microsoft.OffAzure/ImportSites/jobs/read | 获取导入作业的属性 |
> | Microsoft.OffAzure/ImportSites/jobs/read | 获取导入作业的属性 |
> | Microsoft.OffAzure/ImportSites/machines/read | 获取导入计算机的属性 |
> | Microsoft.OffAzure/ImportSites/machines/delete | 删除导入计算机 |
> | Microsoft.OffAzure/ImportSites/machines/read | 获取导入计算机的属性 |
> | Microsoft.OffAzure/ImportSites/machines/delete | 删除导入计算机 |
> | Microsoft.OffAzure/ImportSites/machines/read | 获取导入计算机的属性 |
> | Microsoft.OffAzure/ImportSites/machines/delete | 删除导入计算机 |
> | Microsoft.OffAzure/ImportSites/machines/read | 获取导入计算机的属性 |
> | Microsoft.OffAzure/ImportSites/machines/delete | 删除导入计算机 |
> | Microsoft.OffAzure/ImportSites/machines/read | 获取导入计算机的属性 |
> | Microsoft.OffAzure/ImportSites/machines/delete | 删除导入计算机 |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | 获取主站点操作状态的属性 |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | 获取主站点操作状态的属性 |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | 获取主站点操作状态的属性 |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | 获取主站点操作状态的属性 |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | 获取主站点操作状态的属性 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/operationsstatus/read | 获取针对专用终结点连接代理执行的长时间运行操作的状态 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/operationsstatus/read | 获取针对专用终结点连接代理执行的长时间运行操作的状态 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/operationsstatus/read | 获取针对专用终结点连接代理执行的长时间运行操作的状态 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/operationsstatus/read | 获取针对专用终结点连接代理执行的长时间运行操作的状态 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/operationsstatus/read | 获取针对专用终结点连接代理执行的长时间运行操作的状态 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | 更新专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | 更新专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | 更新专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | 更新专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | 获取专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | 更新专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | 删除专用终结点连接 |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | 获取专用链接资源 |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | 获取专用链接资源 |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | 获取专用链接资源 |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | 获取专用链接资源 |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | 获取专用链接资源 |
> | Microsoft.OffAzure/MasterSites/webAppSites/read | 获取 WebApp 网站的属性 |
> | Microsoft.OffAzure/MasterSites/webAppSites/write | 创建或更新 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/delete | 删除 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/read | 获取 WebApp 网站的属性 |
> | Microsoft.OffAzure/MasterSites/webAppSites/write | 创建或更新 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/delete | 删除 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/read | 获取 WebApp 网站的属性 |
> | Microsoft.OffAzure/MasterSites/webAppSites/write | 创建或更新 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/delete | 删除 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/read | 获取 WebApp 网站的属性 |
> | Microsoft.OffAzure/MasterSites/webAppSites/write | 创建或更新 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/delete | 删除 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/read | 获取 WebApp 网站的属性 |
> | Microsoft.OffAzure/MasterSites/webAppSites/write | 创建或更新 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/delete | 删除 WebApp 网站 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebApplications/read | 获取 IIS Web 应用程序的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebApplications/read | 获取 IIS Web 应用程序的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebApplications/read | 获取 IIS Web 应用程序的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebApplications/read | 获取 IIS Web 应用程序的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebApplications/read | 获取 IIS Web 应用程序的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebServers/read | 获取 IIS Web 服务器的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebServers/read | 获取 IIS Web 服务器的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebServers/read | 获取 IIS Web 服务器的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebServers/read | 获取 IIS Web 服务器的属性。 |
> | Microsoft.OffAzure/MasterSites/webAppSites/IISWebServers/read | 获取 IIS Web 服务器的属性。 |
> | Microsoft.OffAzure/Operations/read | 读取公开的操作 |
> | Microsoft.OffAzure/ServerSites/read | 获取服务器站点的属性 |
> | Microsoft.OffAzure/ServerSites/write | 创建或更新服务器站点 |
> | Microsoft.OffAzure/ServerSites/delete | 删除服务器站点 |
> | Microsoft.OffAzure/ServerSites/refresh/action | 刷新服务器站点中的对象 |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/ServerSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/ServerSites/read | 获取服务器站点的属性 |
> | Microsoft.OffAzure/ServerSites/write | 创建或更新服务器站点 |
> | Microsoft.OffAzure/ServerSites/delete | 删除服务器站点 |
> | Microsoft.OffAzure/ServerSites/refresh/action | 刷新服务器站点中的对象 |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/ServerSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/ServerSites/read | 获取服务器站点的属性 |
> | Microsoft.OffAzure/ServerSites/write | 创建或更新服务器站点 |
> | Microsoft.OffAzure/ServerSites/delete | 删除服务器站点 |
> | Microsoft.OffAzure/ServerSites/refresh/action | 刷新服务器站点中的对象 |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/ServerSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/ServerSites/read | 获取服务器站点的属性 |
> | Microsoft.OffAzure/ServerSites/write | 创建或更新服务器站点 |
> | Microsoft.OffAzure/ServerSites/delete | 删除服务器站点 |
> | Microsoft.OffAzure/ServerSites/refresh/action | 刷新服务器站点中的对象 |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/ServerSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/ServerSites/read | 获取服务器站点的属性 |
> | Microsoft.OffAzure/ServerSites/write | 创建或更新服务器站点 |
> | Microsoft.OffAzure/ServerSites/delete | 删除服务器站点 |
> | Microsoft.OffAzure/ServerSites/refresh/action | 刷新服务器站点中的对象 |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/ServerSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/ServerSites/jobs/read | 获取服务器作业的属性 |
> | Microsoft.OffAzure/ServerSites/jobs/read | 获取服务器作业的属性 |
> | Microsoft.OffAzure/ServerSites/jobs/read | 获取服务器作业的属性 |
> | Microsoft.OffAzure/ServerSites/jobs/read | 获取服务器作业的属性 |
> | Microsoft.OffAzure/ServerSites/jobs/read | 获取服务器作业的属性 |
> | Microsoft.OffAzure/ServerSites/machines/read | 获取服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/write | 写入服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/delete | 删除服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/read | 获取服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/write | 写入服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/delete | 删除服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/read | 获取服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/write | 写入服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/delete | 删除服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/read | 获取服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/write | 写入服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/delete | 删除服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/read | 获取服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/write | 写入服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/machines/delete | 删除服务器计算机的属性 |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | 获取服务器操作状态的属性 |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | 获取服务器操作状态的属性 |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | 获取服务器操作状态的属性 |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | 获取服务器操作状态的属性 |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | 获取服务器操作状态的属性 |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | 获取作为帐户运行的服务器的属性 |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | 获取作为帐户运行的服务器的属性 |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | 获取作为帐户运行的服务器的属性 |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | 获取作为帐户运行的服务器的属性 |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | 获取作为帐户运行的服务器的属性 |
> | Microsoft.OffAzure/ServerSites/summary/read | 获取服务器站点的摘要 |
> | Microsoft.OffAzure/ServerSites/summary/read | 获取服务器站点的摘要 |
> | Microsoft.OffAzure/ServerSites/summary/read | 获取服务器站点的摘要 |
> | Microsoft.OffAzure/ServerSites/summary/read | 获取服务器站点的摘要 |
> | Microsoft.OffAzure/ServerSites/summary/read | 获取服务器站点的摘要 |
> | Microsoft.OffAzure/ServerSites/usage/read | 获取服务器站点的使用情况 |
> | Microsoft.OffAzure/ServerSites/usage/read | 获取服务器站点的使用情况 |
> | Microsoft.OffAzure/ServerSites/usage/read | 获取服务器站点的使用情况 |
> | Microsoft.OffAzure/ServerSites/usage/read | 获取服务器站点的使用情况 |
> | Microsoft.OffAzure/ServerSites/usage/read | 获取服务器站点的使用情况 |
> | Microsoft.OffAzure/VMwareSites/read | 获取 VMware 站点的属性 |
> | Microsoft.OffAzure/VMwareSites/write | 创建或更新 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/delete | 删除 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 刷新 VMware 站点中的对象 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | 将 VMware 应用程序和角色数据导出到 xls |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | 生成计算机列表的粗略地图 |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 生成详细的 VMware 粗略地图 |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 列出所选客户端组的客户端组成员。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 列出所选服务器组的服务器组成员。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 获取所选计算机的应用程序列表信息 |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 导出所选计算机的依赖项信息 |
> | Microsoft.OffAzure/VMwareSites/read | 获取 VMware 站点的属性 |
> | Microsoft.OffAzure/VMwareSites/write | 创建或更新 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/delete | 删除 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 刷新 VMware 站点中的对象 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | 将 VMware 应用程序和角色数据导出到 xls |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | 生成计算机列表的粗略地图 |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 生成详细的 VMware 粗略地图 |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 列出所选客户端组的客户端组成员。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 列出所选服务器组的服务器组成员。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 获取所选计算机的应用程序列表信息 |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 导出所选计算机的依赖项信息 |
> | Microsoft.OffAzure/VMwareSites/read | 获取 VMware 站点的属性 |
> | Microsoft.OffAzure/VMwareSites/write | 创建或更新 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/delete | 删除 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 刷新 VMware 站点中的对象 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | 将 VMware 应用程序和角色数据导出到 xls |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | 生成计算机列表的粗略地图 |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 生成详细的 VMware 粗略地图 |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 列出所选客户端组的客户端组成员。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 列出所选服务器组的服务器组成员。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 获取所选计算机的应用程序列表信息 |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 导出所选计算机的依赖项信息 |
> | Microsoft.OffAzure/VMwareSites/read | 获取 VMware 站点的属性 |
> | Microsoft.OffAzure/VMwareSites/write | 创建或更新 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/delete | 删除 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 刷新 VMware 站点中的对象 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | 将 VMware 应用程序和角色数据导出到 xls |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | 生成计算机列表的粗略地图 |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 生成详细的 VMware 粗略地图 |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 列出所选客户端组的客户端组成员。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 列出所选服务器组的服务器组成员。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 获取所选计算机的应用程序列表信息 |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 导出所选计算机的依赖项信息 |
> | Microsoft.OffAzure/VMwareSites/read | 获取 VMware 站点的属性 |
> | Microsoft.OffAzure/VMwareSites/write | 创建或更新 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/delete | 删除 VMware 站点 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 刷新 VMware 站点中的对象 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | 将 VMware 应用程序和角色数据导出到 xls |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | 更新站点中计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/updateTags/action | 更新站点中计算机的标记 |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | 生成计算机列表的粗略地图 |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 生成详细的 VMware 粗略地图 |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 列出所选客户端组的客户端组成员。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 列出所选服务器组的服务器组成员。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 获取所选计算机的应用程序列表信息 |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 导出所选计算机的依赖项信息 |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | 获取 VMware 资源的运行状况摘要 |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | 获取 VMware 资源的运行状况摘要 |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | 获取 VMware 资源的运行状况摘要 |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | 获取 VMware 资源的运行状况摘要 |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | 获取 VMware 资源的运行状况摘要 |
> | Microsoft.OffAzure/VMwareSites/hosts/read | 获取 VMware 主机的属性 |
> | Microsoft.OffAzure/VMwareSites/hosts/read | 获取 VMware 主机的属性 |
> | Microsoft.OffAzure/VMwareSites/hosts/read | 获取 VMware 主机的属性 |
> | Microsoft.OffAzure/VMwareSites/hosts/read | 获取 VMware 主机的属性 |
> | Microsoft.OffAzure/VMwareSites/hosts/read | 获取 VMware 主机的属性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 获取 VMware 作业的属性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 获取 VMware 作业的属性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 获取 VMware 作业的属性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 获取 VMware 作业的属性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 获取 VMware 作业的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 获取 VMware 计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 启动 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 获取 VMware 计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 启动 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 获取 VMware 计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 启动 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 获取 VMware 计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 启动 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 获取 VMware 计算机的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 启动 VMware 计算机 |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | 获取 VMware 计算机应用程序的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | 获取 VMware 计算机应用程序的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | 获取 VMware 计算机应用程序的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | 获取 VMware 计算机应用程序的属性 |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | 获取 VMware 计算机应用程序的属性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 获取 VMware 操作状态的属性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 获取 VMware 操作状态的属性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 获取 VMware 操作状态的属性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 获取 VMware 操作状态的属性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 获取 VMware 操作状态的属性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 获取作为帐户运行的 VMware 的属性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 获取作为帐户运行的 VMware 的属性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 获取作为帐户运行的 VMware 的属性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 获取作为帐户运行的 VMware 的属性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 获取作为帐户运行的 VMware 的属性 |
> | Microsoft.OffAzure/VMwareSites/summary/read | 获取 VMware 站点的摘要 |
> | Microsoft.OffAzure/VMwareSites/summary/read | 获取 VMware 站点的摘要 |
> | Microsoft.OffAzure/VMwareSites/summary/read | 获取 VMware 站点的摘要 |
> | Microsoft.OffAzure/VMwareSites/summary/read | 获取 VMware 站点的摘要 |
> | Microsoft.OffAzure/VMwareSites/summary/read | 获取 VMware 站点的摘要 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 获取 VMware 的使用情况 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 获取 VMware 的使用情况 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 获取 VMware 的使用情况 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 获取 VMware 的使用情况 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 获取 VMware 的使用情况 |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 获取 VMware vCenter 的属性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 创建或更新 VMware vCenter |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 获取 VMware vCenter 的属性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 创建或更新 VMware vCenter |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 获取 VMware vCenter 的属性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 创建或更新 VMware vCenter |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 获取 VMware vCenter 的属性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 创建或更新 VMware vCenter |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 获取 VMware vCenter 的属性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 创建或更新 VMware vCenter |

## <a name="monitor"></a>监视

### <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

Azure 服务：[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.AlertsManagement/register/action | 订阅注册操作 |
> | Microsoft.AlertsManagement/register/action | 注册 Microsoft 警报管理的订阅 |
> | Microsoft.AlertsManagement/actionRules/read | 获取输入筛选器的所有操作规则。 |
> | Microsoft.AlertsManagement/actionRules/write | 创建或更新给定订阅中的操作规则 |
> | Microsoft.AlertsManagement/actionRules/delete | 删除给定订阅中的操作规则。 |
> | Microsoft.AlertsManagement/alerts/read | 获取输入筛选器的所有警报。 |
> | Microsoft.AlertsManagement/alerts/changestate/action | 更改警报状态。 |
> | Microsoft.AlertsManagement/alerts/history/read | 获取警报的历史记录 |
> | Microsoft.AlertsManagement/alertsList/read | 获取跨订阅的输入筛选器的所有警报 |
> | Microsoft.AlertsManagement/alertsMetaData/read | 获取输入参数的警报元数据。 |
> | Microsoft.AlertsManagement/alertsSummary/read | 获取警报的摘要 |
> | Microsoft.AlertsManagement/alertsSummaryList/read | 获取跨订阅的警报的摘要 |
> | Microsoft.AlertsManagement/Operations/read | 读取提供的操作 |
> | Microsoft.AlertsManagement/resourceHealthAlertRules/write | 创建或更新给定订阅中的资源运行状况警报规则 |
> | Microsoft.AlertsManagement/resourceHealthAlertRules/read | 获取输入筛选器的所有资源运行状况警报规则 |
> | Microsoft.AlertsManagement/resourceHealthAlertRules/delete | 删除给定订阅中的资源运行状况警报规则 |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/write | 创建或更新给定订阅中的智能检测器警报规则 |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/read | 获取输入筛选器的所有智能检测器警报规则 |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | 删除给定订阅中的智能检测器警报规则 |
> | Microsoft.AlertsManagement/smartGroups/read | 获取输入筛选器的所有智能组 |
> | Microsoft.AlertsManagement/smartGroups/changestate/action | 更改智能组的状态 |
> | Microsoft.AlertsManagement/smartGroups/history/read | 获取智能组的历史记录 |

### <a name="microsoftinsights"></a>Microsoft.Insights

Azure 服务：[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Insights/Metrics/Action | 指标操作 |
> | Microsoft.Insights/Register/Action | 注册 Microsoft Insights 提供程序 |
> | Microsoft.Insights/Unregister/Action | 注册 Microsoft Insights 提供程序 |
> | Microsoft.Insights/ListMigrationDate/Action | 返回订阅迁移日期 |
> | Microsoft.Insights/MigrateToNewpricingModel/Action | 将订阅迁移到新的定价模型 |
> | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 将订阅回滚到旧定价模型 |
> | Microsoft.Insights/ActionGroups/Write | 创建或更新操作组 |
> | Microsoft.Insights/ActionGroups/Delete | 删除操作组 |
> | Microsoft.Insights/ActionGroups/Read | 读取操作组 |
> | Microsoft.Insights/ActivityLogAlerts/Write | 创建或更新活动日志警报 |
> | Microsoft.Insights/ActivityLogAlerts/Delete | 删除活动日志警报 |
> | Microsoft.Insights/ActivityLogAlerts/Read | 读取活动日志警报 |
> | Microsoft.Insights/ActivityLogAlerts/Activated/Action | 活动日志警报已激活 |
> | Microsoft.Insights/AlertRules/Write | 创建或更新经典指标警报 |
> | Microsoft.Insights/AlertRules/Delete | 删除经典指标警报 |
> | Microsoft.Insights/AlertRules/Read | 读取经典指标警报 |
> | Microsoft.Insights/AlertRules/Activated/Action | 经典指标警报已激活 |
> | Microsoft.Insights/AlertRules/Resolved/Action | 经典指标警报已解决 |
> | Microsoft.Insights/AlertRules/Throttled/Action | 经典指标预警规则已中止 |
> | Microsoft.Insights/AlertRules/Incidents/Read | 读取经典指标警报事件 |
> | Microsoft.Insights/AutoscaleSettings/Write | 创建或更新自动缩放设置 |
> | Microsoft.Insights/AutoscaleSettings/Delete | 删除自动缩放设置 |
> | Microsoft.Insights/AutoscaleSettings/Read | 读取自动缩放设置 |
> | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 自动缩放纵向扩展已启动 |
> | Microsoft.Insights/AutoscaleSettings/PredictiveScaleup/Action | 预测性自动缩放纵向扩展已启动 |
> | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 自动纵向缩减已启动 |
> | Microsoft.Insights/AutoscaleSettings/PredictiveScaleupResult/Action | 预测性自动缩放纵向扩展已完成 |
> | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 自动缩放纵向扩展已完成 |
> | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 自动纵向缩减已完成 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | 读取资源诊断设置 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | 创建或更新资源诊断设置 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | 读取日志定义 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | 读取指标定义 |
> | Microsoft.Insights/Baseline/Read | 读取指标基线（预览版） |
> | Microsoft.Insights/CalculateBaseline/Read | 计算指标值的基线（预览版） |
> | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights 分析表操作 |
> | Microsoft.Insights/Components/ApiKeys/Action | 生成 Application Insights API 密钥 |
> | Microsoft.Insights/Components/Purge/Action | 从 Application Insights 清除数据 |
> | Microsoft.Insights/Components/DailyCapReached/Action | 已达到 Application Insights 组件的每日上限 |
> | Microsoft.Insights/Components/DailyCapWarningThresholdReached/Action | 已达到 Application Insights 组件的每日上限警告阈值 |
> | Microsoft.Insights/Components/Write | 写入到 Application Insights 组件配置 |
> | Microsoft.Insights/Components/Delete | 删除 Application Insights 组件配置 |
> | Microsoft.Insights/Components/Read | 读取 Application Insights 组件配置 |
> | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights 导出设置操作 |
> | Microsoft.Insights/Components/Move/Action | 将 Application Insights 组件移动到另一个资源组或订阅 |
> | Microsoft.Insights/Components/AnalyticsItems/Delete | 添加 Application Insights 分析项 |
> | Microsoft.Insights/Components/AnalyticsItems/Read | 读取 Application Insights 分析项 |
> | Microsoft.Insights/Components/AnalyticsItems/Write | 写入 Application Insights 分析项 |
> | Microsoft.Insights/Components/AnalyticsTables/Delete | 删除 Application Insights 分析表架构 |
> | Microsoft.Insights/Components/AnalyticsTables/Read | 读取 Application Insights 分析表架构 |
> | Microsoft.Insights/Components/AnalyticsTables/Write | 写入 Application Insights 分析表架构 |
> | Microsoft.Insights/Components/Annotations/Delete | 删除 Application Insights 注释 |
> | Microsoft.Insights/Components/Annotations/Read | 读取 Application Insights 注释 |
> | Microsoft.Insights/Components/Annotations/Write | 写入 Application Insights 注释 |
> | Microsoft.Insights/Components/Api/Read | 读取 Application Insights 组件数据 API |
> | Microsoft.Insights/Components/ApiKeys/Delete | 删除 Application Insights API 密钥 |
> | Microsoft.Insights/Components/ApiKeys/Read | 读取 Application Insights API 密钥 |
> | Microsoft.Insights/Components/BillingPlanForComponent/Read | 读取 Application Insights 组件的计费计划 |
> | Microsoft.Insights/Components/CurrentBillingFeatures/Read | 读取 Application Insights 组件的当前计费计划 |
> | Microsoft.Insights/Components/CurrentBillingFeatures/Write | 写入 Application Insights 组件的当前计费计划 |
> | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | 读取 Application Insights 默认 ALM 集成配置 |
> | Microsoft.Insights/Components/Events/Read | 使用 OData 查询格式从 Application Insights 获取日志 |
> | Microsoft.Insights/Components/ExportConfiguration/Delete | 删除 Application Insights 导出设置 |
> | Microsoft.Insights/Components/ExportConfiguration/Read | 读取 Application Insights 导出设置 |
> | Microsoft.Insights/Components/ExportConfiguration/Write | 写入 Application Insights 导出设置 |
> | Microsoft.Insights/Components/ExtendQueries/Read | 读取 Application Insights 组件扩展查询结果 |
> | Microsoft.Insights/Components/Favorites/Delete | 删除 Application Insights 收藏夹 |
> | Microsoft.Insights/Components/Favorites/Read | 读取 Application Insights 收藏夹 |
> | Microsoft.Insights/Components/Favorites/Write | 写入 Application Insights 收藏夹 |
> | Microsoft.Insights/Components/FeatureCapabilities/Read | 读取 Application Insights 组件功能 |
> | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | 读取 Application Insights 组件可用计费功能 |
> | Microsoft.Insights/Components/GetToken/Read | 读取 Application Insights 组件令牌 |
> | Microsoft.Insights/Components/MetricDefinitions/Read | 读取 Application Insights 组件指标定义 |
> | Microsoft.Insights/Components/Metrics/Read | 读取 Application Insights 组件指标 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Delete | 删除 Application Insights 个人分析项 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Write | 写入 Application Insights 个人分析项 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Read | 读取 Application Insights 个人分析项 |
> | Microsoft.Insights/Components/MyFavorites/Read | 读取 Application Insights 个人收藏夹 |
> | Microsoft.Insights/Components/Operations/Read | 在 Application Insights 中获取长时间运行的操作的状态 |
> | Microsoft.Insights/Components/PricingPlans/Read | 读取 Application Insights 组件定价计划 |
> | Microsoft.Insights/Components/PricingPlans/Write | 写入 Application Insights 组件定价计划 |
> | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | 读取 Application Insights 主动检测配置 |
> | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | 写入 Application Insights 主动检测配置 |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/diagnosticSettings/Read | 读取资源诊断设置 |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/diagnosticSettings/Write | 创建或更新资源诊断设置 |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/logDefinitions/Read | 读取日志定义 |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | 读取指标定义 |
> | Microsoft.Insights/Components/Query/Read | 对 Application Insights 日志运行查询 |
> | Microsoft.Insights/Components/QuotaStatus/Read | 读取 Application Insights 组件配额状态 |
> | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | 读取 Application Insights webtest 位置 |
> | Microsoft.Insights/Components/Webtests/Read | 读取 webtest 配置 |
> | Microsoft.Insights/Components/WorkItemConfigs/Delete | 删除 Application Insights ALM 集成配置 |
> | Microsoft.Insights/Components/WorkItemConfigs/Read | 读取 Application Insights ALM 集成配置 |
> | Microsoft.Insights/Components/WorkItemConfigs/Write | 写入 Application Insights ALM 集成配置 |
> | Microsoft.Insights/DataCollectionEndpoints/Read | 读取数据收集终结点 |
> | Microsoft.Insights/DataCollectionEndpoints/Write | 创建或更新数据收集终结点 |
> | Microsoft.Insights/DataCollectionEndpoints/Delete | 删除数据收集终结点 |
> | Microsoft.Insights/DataCollectionEndpoints/ScopedPrivateLinkProxies/Read | 读取数据收集终结点专用链接代理 |
> | Microsoft.Insights/DataCollectionEndpoints/ScopedPrivateLinkProxies/Write | 创建或更新数据收集终结点专用链接代理 |
> | Microsoft.Insights/DataCollectionEndpoints/ScopedPrivateLinkProxies/Delete | 删除数据收集终结点专用链接代理 |
> | Microsoft.Insights/DataCollectionRuleAssociations/Read | 读取资源与数据收集规则之间的关联 |
> | Microsoft.Insights/DataCollectionRuleAssociations/Write | 创建或更新资源与数据收集规则之间的关联 |
> | Microsoft.Insights/DataCollectionRuleAssociations/Delete | 删除资源与数据收集规则之间的关联 |
> | Microsoft.Insights/DataCollectionRules/Read | 读取数据收集规则 |
> | Microsoft.Insights/DataCollectionRules/Write | 创建或更新数据收集规则 |
> | Microsoft.Insights/DataCollectionRules/Delete | 删除数据收集规则 |
> | Microsoft.Insights/DiagnosticSettings/Write | 创建或更新资源诊断设置 |
> | Microsoft.Insights/DiagnosticSettings/Delete | 删除资源诊断设置 |
> | Microsoft.Insights/DiagnosticSettings/Read | 读取资源诊断设置 |
> | Microsoft.Insights/DiagnosticSettingsCategories/Read | 读取诊断设置类别 |
> | Microsoft.Insights/EventCategories/Read | 读取可用的活动日志事件类别 |
> | Microsoft.Insights/eventtypes/digestevents/Read | 读取管理事件类型摘要 |
> | Microsoft.Insights/eventtypes/values/Read | 读取活动日志事件 |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Write | 创建或更新网络流日志诊断设置 |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | 删除网络流日志诊断设置 |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Read | 读取网络流日志诊断设置 |
> | Microsoft.Insights/generateLiveToken/Read | 实时指标获取令牌 |
> | Microsoft.Insights/ListMigrationDate/Read | 返回订阅迁移日期 |
> | Microsoft.Insights/LogDefinitions/Read | 读取日志定义 |
> | Microsoft.Insights/LogProfiles/Write | 创建或更新活动日志的日志配置文件 |
> | Microsoft.Insights/LogProfiles/Delete | 删除活动日志的日志配置文件 |
> | Microsoft.Insights/LogProfiles/Read | 读取活动日志的日志配置文件 |
> | Microsoft.Insights/Logs/Read | 从所有日志中读取数据 |
> | Microsoft.Insights/Logs/AADDomainServicesAccountLogon/Read | 从 AADDomainServicesAccountLogon 表读取数据 |
> | Microsoft.Insights/Logs/AADDomainServicesAccountManagement/Read | 从 AADDomainServicesAccountManagement 表中读取数据 |
> | Microsoft.Insights/Logs/AADDomainServicesDirectoryServiceAccess/Read | 从 AADDomainServicesDirectoryServiceAccess 表中读取数据 |
> | Microsoft.Insights/Logs/AADDomainServicesLogonLogoff/Read | 从 AADDomainServicesLogonLogoff 表读取数据 |
> | Microsoft.Insights/Logs/AADDomainServicesPolicyChange/Read | 从 AADDomainServicesPolicyChange 表中读取数据 |
> | Microsoft.Insights/Logs/AADDomainServicesPrivilegeUse/Read | 从 AADDomainServicesPrivilegeUse 表中读取数据 |
> | Microsoft.Insights/Logs/AADDomainServicesSystemSecurity/Read | 从 AADDomainServicesSystemSecurity 表中读取数据 |
> | Microsoft.Insights/Logs/AADManagedIdentitySignInLogs/Read | 从 AADManagedIdentitySignInLogs 表中读取数据 |
> | Microsoft.Insights/Logs/AADNonInteractiveUserSignInLogs/Read | 从 AADNonInteractiveUserSignInLogs 表中读取数据 |
> | Microsoft.Insights/Logs/AADServicePrincipalSignInLogs/Read | 从 AADServicePrincipalSignInLogs 表中读取数据 |
> | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | 从 ADAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/AddonAzureBackupAlerts/Read | 从 AddonAzureBackupAlerts 表中读取数据 |
> | Microsoft.Insights/Logs/AddonAzureBackupJobs/Read | 从 AddonAzureBackupJobs 表中读取数据 |
> | Microsoft.Insights/Logs/AddonAzureBackupPolicy/Read | 从 AddonAzureBackupPolicy 表中读取数据 |
> | Microsoft.Insights/Logs/AddonAzureBackupProtectedInstance/Read | 从 AddonAzureBackupProtectedInstance 表中读取数据 |
> | Microsoft.Insights/Logs/AddonAzureBackupStorage/Read | 从 AddonAzureBackupStorage 表中读取数据 |
> | Microsoft.Insights/Logs/ADFActivityRun/Read | 从 ADFActivityRun 表中读取数据 |
> | Microsoft.Insights/Logs/ADFPipelineRun/Read | 从 ADFPipelineRun 表中读取数据 |
> | Microsoft.Insights/Logs/ADFSSISIntegrationRuntimeLogs/Read | 从 ADFSSISIntegrationRuntimeLogs 表读取数据 |
> | Microsoft.Insights/Logs/ADFSSISPackageEventMessageContext/Read | 从 ADFSSISPackageEventMessageContext 表读取数据 |
> | Microsoft.Insights/Logs/ADFSSISPackageEventMessages/Read | 从 ADFSSISPackageEventMessages 表读取数据 |
> | Microsoft.Insights/Logs/ADFSSISPackageExecutableStatistics/Read | 从 ADFSSISPackageExecutableStatistics 表读取数据 |
> | Microsoft.Insights/Logs/ADFSSISPackageExecutionComponentPhases/Read | 从 ADFSSISPackageExecutionComponentPhases 表读取数据 |
> | Microsoft.Insights/Logs/ADFSSISPackageExecutionDataStatistics/Read | 从 ADFSSISPackageExecutionDataStatistics 表读取数据 |
> | Microsoft.Insights/Logs/ADFTriggerRun/Read | 从 ADFTriggerRun 表中读取数据 |
> | Microsoft.Insights/Logs/ADReplicationResult/Read | 从 ADReplicationResult 表读取数据 |
> | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | 从 ADSecurityAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/ADTDigitalTwinsOperation/Read | 读取 ADTDigitalTwinsOperation 表中的数据 |
> | Microsoft.Insights/Logs/ADTEventRoutesOperation/Read | 读取 ADTEventRoutesOperation 表中的数据 |
> | Microsoft.Insights/Logs/ADTModelsOperation/Read | 读取 ADTModelsOperation 表中的数据 |
> | Microsoft.Insights/Logs/ADTQueryOperation/Read | 读取 ADTQueryOperation 表中的数据 |
> | Microsoft.Insights/Logs/AegDeliveryFailureLogs/Read | 从 AegDeliveryFailureLogs 表读取数据 |
> | Microsoft.Insights/Logs/AegPublishFailureLogs/Read | 从 AegPublishFailureLogs 表读取数据 |
> | Microsoft.Insights/Logs/Alert/Read | 从 Alert 表读取数据 |
> | Microsoft.Insights/Logs/AlertHistory/Read | 从 AlertHistory 表读取数据 |
> | Microsoft.Insights/Logs/AmlComputeClusterEvent/Read | 从 AmlComputeClusterEvent 表读取数据 |
> | Microsoft.Insights/Logs/AmlComputeClusterNodeEvent/Read | 从 AmlComputeClusterNodeEvent 表读取数据 |
> | Microsoft.Insights/Logs/AmlComputeCpuGpuUtilization/Read | 从 AmlComputeCpuGpuUtilization 表中读取数据 |
> | Microsoft.Insights/Logs/AmlComputeJobEvent/Read | 从 AmlComputeJobEvent 表读取数据 |
> | Microsoft.Insights/Logs/AmlRunStatusChangedEvent/Read | 从 AmlRunStatusChangedEvent 表中读取数据 |
> | Microsoft.Insights/Logs/ApiManagementGatewayLogs/Read | 从 ApiManagementGatewayLogs 表中读取数据 |
> | Microsoft.Insights/Logs/AppAvailabilityResults/Read | 从 AppAvailabilityResults 表读取数据 |
> | Microsoft.Insights/Logs/AppBrowserTimings/Read | 从 AppBrowserTimings 表读取数据 |
> | Microsoft.Insights/Logs/AppCenterError/Read | 从 AppCenterError 表读取数据 |
> | Microsoft.Insights/Logs/AppDependencies/Read | 从 AppDependencies 表读取数据 |
> | Microsoft.Insights/Logs/AppEvents/Read | 从 AppEvents 表读取数据 |
> | Microsoft.Insights/Logs/AppExceptions/Read | 从 AppExceptions 表读取数据 |
> | Microsoft.Insights/Logs/ApplicationInsights/Read | 从 ApplicationInsights 表读取数据 |
> | Microsoft.Insights/Logs/AppMetrics/Read | 从 AppMetrics 表读取数据 |
> | Microsoft.Insights/Logs/AppPageViews/Read | 从 AppPageViews 表读取数据 |
> | Microsoft.Insights/Logs/AppPerformanceCounters/Read | 从 AppPerformanceCounters 表读取数据 |
> | Microsoft.Insights/Logs/AppPlatformLogsforSpring/Read | 从 AppPlatformLogsforSpring 表读取数据 |
> | Microsoft.Insights/Logs/AppPlatformSystemLogs/Read | 从 AppPlatformSystemLogs 表读取数据 |
> | Microsoft.Insights/Logs/AppRequests/Read | 从 AppRequests 表读取数据 |
> | Microsoft.Insights/Logs/AppServiceAntivirusScanLogs/Read | 从 AppServiceAntivirusScanLogs 表中读取数据 |
> | Microsoft.Insights/Logs/AppServiceAppLogs/Read | 从 AppServiceAppLogs 表读取数据 |
> | Microsoft.Insights/Logs/AppServiceAuditLogs/Read | 从 AppServiceAuditLogs 表读取数据 |
> | Microsoft.Insights/Logs/AppServiceConsoleLogs/Read | 从 AppServiceConsoleLogs 表读取数据 |
> | Microsoft.Insights/Logs/AppServiceEnvironmentPlatformLogs/Read | 从 AppServiceEnvironmentPlatformLogs 表读取数据 |
> | Microsoft.Insights/Logs/AppServiceFileAuditLogs/Read | 从 AppServiceFileAuditLogs 表读取数据 |
> | Microsoft.Insights/Logs/AppServiceHTTPLogs/Read | 从 AppServiceHTTPLogs 表读取数据 |
> | Microsoft.Insights/Logs/AppServicePlatformLogs/Read | 读取 AppServicePlatformLogs 表中的数据 |
> | Microsoft.Insights/Logs/AppSystemEvents/Read | 从 AppSystemEvents 表读取数据 |
> | Microsoft.Insights/Logs/AppTraces/Read | 从 AppTraces 表读取数据 |
> | Microsoft.Insights/Logs/AuditLogs/Read | 从 AuditLogs 表读取数据 |
> | Microsoft.Insights/Logs/AutoscaleEvaluationsLog/Read | 读取 AutoscaleEvaluationsLog 表中的数据 |
> | Microsoft.Insights/Logs/AutoscaleScaleActionsLog/Read | 读取 AutoscaleScaleActionsLog 表中的数据 |
> | Microsoft.Insights/Logs/AWSCloudTrail/Read | 读取 AWSCloudTrail 表中的数据 |
> | Microsoft.Insights/Logs/AzureActivity/Read | 从 AzureActivity 表读取数据 |
> | Microsoft.Insights/Logs/AzureAssessmentRecommendation/Read | 从 AzureAssessmentRecommendation 表中读取数据 |
> | Microsoft.Insights/Logs/AzureDevOpsAuditing/Read | 从 AzureDevOpsAuditing 表读取数据 |
> | Microsoft.Insights/Logs/AzureDiagnostics/Read | 从 AzureDiagnostics 表读取数据 |
> | Microsoft.Insights/Logs/AzureMetrics/Read | 从 AzureMetrics 表读取数据 |
> | Microsoft.Insights/Logs/BaiClusterEvent/Read | 从 BaiClusterEvent 表读取数据 |
> | Microsoft.Insights/Logs/BaiClusterNodeEvent/Read | 从 BaiClusterNodeEvent 表读取数据 |
> | Microsoft.Insights/Logs/BaiJobEvent/Read | 从 BaiJobEvent 表读取数据 |
> | Microsoft.Insights/Logs/BehaviorAnalytics/Read | 读取 BehaviorAnalytics 表中的数据 |
> | Microsoft.Insights/Logs/BlockchainApplicationLog/Read | 从 BlockchainApplicationLog 表中读取数据 |
> | Microsoft.Insights/Logs/BlockchainProxyLog/Read | 从 BlockchainProxyLog 表中读取数据 |
> | Microsoft.Insights/Logs/BoundPort/Read | 从 BoundPort 表读取数据 |
> | Microsoft.Insights/Logs/CommonSecurityLog/Read | 从 CommonSecurityLog 表读取数据 |
> | Microsoft.Insights/Logs/ComputerGroup/Read | 从 ComputerGroup 表读取数据 |
> | Microsoft.Insights/Logs/ConfigurationChange/Read | 从 ConfigurationChange 表读取数据 |
> | Microsoft.Insights/Logs/ConfigurationData/Read | 从 ConfigurationData 表读取数据 |
> | Microsoft.Insights/Logs/ContainerImageInventory/Read | 从 ContainerImageInventory 表读取数据 |
> | Microsoft.Insights/Logs/ContainerInventory/Read | 从 ContainerInventory 表读取数据 |
> | Microsoft.Insights/Logs/ContainerLog/Read | 从 ContainerLog 表读取数据 |
> | Microsoft.Insights/Logs/ContainerNodeInventory/Read | 读取 ContainerNodeInventory 表中的数据 |
> | Microsoft.Insights/Logs/ContainerRegistryLoginEvents/Read | 从 ContainerRegistryLoginEvents 表中读取数据 |
> | Microsoft.Insights/Logs/ContainerRegistryRepositoryEvents/Read | 从 ContainerRegistryRepositoryEvents 表中读取数据 |
> | Microsoft.Insights/Logs/ContainerServiceLog/Read | 从 ContainerServiceLog 表读取数据 |
> | Microsoft.Insights/Logs/CoreAzureBackup/Read | 从 CoreAzureBackup 表中读取数据 |
> | Microsoft.Insights/Logs/DatabricksAccounts/Read | 从 DatabricksAccounts 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksClusters/Read | 从 DatabricksClusters 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksDBFS/Read | 从 DatabricksDBFS 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksInstancePools/Read | 从 DatabricksInstancePools 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksJobs/Read | 从 DatabricksJobs 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksNotebook/Read | 从 DatabricksNotebook 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksSecrets/Read | 从 DatabricksSecrets 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksSQLPermissions/Read | 从 DatabricksSQLPermissions 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksSSH/Read | 从 DatabricksSSH 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksTables/Read | 从 DatabricksTables 表读取数据 |
> | Microsoft.Insights/Logs/DatabricksWorkspace/Read | 从 DatabricksWorkspace 表读取数据 |
> | Microsoft.Insights/Logs/DeviceAppCrash/Read | 从 DeviceAppCrash 表读取数据 |
> | Microsoft.Insights/Logs/DeviceAppLaunch/Read | 从 DeviceAppLaunch 表读取数据 |
> | Microsoft.Insights/Logs/DeviceCalendar/Read | 从 DeviceCalendar 表读取数据 |
> | Microsoft.Insights/Logs/DeviceCleanup/Read | 从 DeviceCleanup 表读取数据 |
> | Microsoft.Insights/Logs/DeviceConnectSession/Read | 从 DeviceConnectSession 表读取数据 |
> | Microsoft.Insights/Logs/DeviceEtw/Read | 从 DeviceEtw 表读取数据 |
> | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | 从 DeviceHardwareHealth 表读取数据 |
> | Microsoft.Insights/Logs/DeviceHealth/Read | 从 DeviceHealth 表读取数据 |
> | Microsoft.Insights/Logs/DeviceHeartbeat/Read | 从 DeviceHeartbeat 表读取数据 |
> | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | 从 DeviceSkypeHeartbeat 表读取数据 |
> | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | 从 DeviceSkypeSignIn 表读取数据 |
> | Microsoft.Insights/Logs/DeviceSleepState/Read | 从 DeviceSleepState 表读取数据 |
> | Microsoft.Insights/Logs/DHAppFailure/Read | 从 DHAppFailure 表读取数据 |
> | Microsoft.Insights/Logs/DHAppReliability/Read | 从 DHAppReliability 表读取数据 |
> | Microsoft.Insights/Logs/DHCPActivity/Read | 从 DHCPActivity 表读取数据 |
> | Microsoft.Insights/Logs/DHDriverReliability/Read | 从 DHDriverReliability 表读取数据 |
> | Microsoft.Insights/Logs/DHLogonFailures/Read | 从 DHLogonFailures 表读取数据 |
> | Microsoft.Insights/Logs/DHLogonMetrics/Read | 从 DHLogonMetrics 表读取数据 |
> | Microsoft.Insights/Logs/DHOSCrashData/Read | 从 DHOSCrashData 表读取数据 |
> | Microsoft.Insights/Logs/DHOSReliability/Read | 从 DHOSReliability 表读取数据 |
> | Microsoft.Insights/Logs/DHWipAppLearning/Read | 从 DHWipAppLearning 表读取数据 |
> | Microsoft.Insights/Logs/DnsEvents/Read | 从 DnsEvents 表读取数据 |
> | Microsoft.Insights/Logs/DnsInventory/Read | 从 DnsInventory 表读取数据 |
> | Microsoft.Insights/Logs/Dynamics365Activity/Read | 读取 Dynamics365Activity 表中的数据 |
> | Microsoft.Insights/Logs/ETWEvent/Read | 从 ETWEvent 表读取数据 |
> | Microsoft.Insights/Logs/Event/Read | 从 Event 表读取数据 |
> | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | 从 ExchangeAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | 从 ExchangeOnlineAssessmentRecommendation 表中读取数据 |
> | Microsoft.Insights/Logs/FailedIngestion/Read | 从 FailedIngestion 表中读取数据 |
> | Microsoft.Insights/Logs/FunctionAppLogs/Read | 从 FunctionAppLogs 表读取数据 |
> | Microsoft.Insights/Logs/Heartbeat/Read | 从 Heartbeat 表读取数据 |
> | Microsoft.Insights/Logs/HuntingBookmark/Read | 读取 HuntingBookmark 表中的数据 |
> | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | 从 IISAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/InboundConnection/Read | 从 InboundConnection 表读取数据 |
> | Microsoft.Insights/Logs/InsightsMetrics/Read | 从 InsightsMetrics 表读取数据 |
> | Microsoft.Insights/Logs/IntuneAuditLogs/Read | 从 IntuneAuditLogs 表读取数据 |
> | Microsoft.Insights/Logs/IntuneDeviceComplianceOrg/Read | 从 IntuneDeviceComplianceOrg 表读取数据 |
> | Microsoft.Insights/Logs/IntuneOperationalLogs/Read | 从 IntuneOperationalLogs 表读取数据 |
> | Microsoft.Insights/Logs/IoTHubDistributedTracing/Read | 从 IoTHubDistributedTracing 表读取数据 |
> | Microsoft.Insights/Logs/KubeEvents/Read | 从 KubeEvents 表读取数据 |
> | Microsoft.Insights/Logs/KubeHealth/Read | 从 KubeHealth 表读取数据 |
> | Microsoft.Insights/Logs/KubeMonAgentEvents/Read | 从 KubeMonAgentEvents 表读取数据 |
> | Microsoft.Insights/Logs/KubeNodeInventory/Read | 从 KubeNodeInventory 表读取数据 |
> | Microsoft.Insights/Logs/KubePodInventory/Read | 从 KubePodInventory 表读取数据 |
> | Microsoft.Insights/Logs/KubeServices/Read | 从 KubeServices 表读取数据 |
> | Microsoft.Insights/Logs/LinuxAuditLog/Read | 从 LinuxAuditLog 表读取数据 |
> | Microsoft.Insights/Logs/MAApplication/Read | 从 MAApplication 表读取数据 |
> | Microsoft.Insights/Logs/MAApplicationHealth/Read | 从 MAApplicationHealth 表读取数据 |
> | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | 从 MAApplicationHealthAlternativeVersions 表读取数据 |
> | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | 从 MAApplicationHealthIssues 表读取数据 |
> | Microsoft.Insights/Logs/MAApplicationInstance/Read | 从 MAApplicationInstance 表读取数据 |
> | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | 从 MAApplicationInstanceReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MAApplicationReadiness/Read | 从 MAApplicationReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MADeploymentPlan/Read | 从 MADeploymentPlan 表读取数据 |
> | Microsoft.Insights/Logs/MADevice/Read | 从 MADevice 表读取数据 |
> | Microsoft.Insights/Logs/MADeviceNotEnrolled/Read | 从 MADeviceNotEnrolled 表读取数据 |
> | Microsoft.Insights/Logs/MADeviceNRT/Read | 从 MADeviceNRT 表读取数据 |
> | Microsoft.Insights/Logs/MADevicePnPHealth/Read | 从 MADevicePnPHealth 表读取数据 |
> | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | 从 MADevicePnPHealthAlternativeVersions 表读取数据 |
> | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | 从 MADevicePnPHealthIssues 表读取数据 |
> | Microsoft.Insights/Logs/MADeviceReadiness/Read | 从 MADeviceReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | 从 MADriverInstanceReadiness 表中读取数据 |
> | Microsoft.Insights/Logs/MADriverReadiness/Read | 从 MADriverReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAddin/Read | 从 MAOfficeAddin 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAddinEntityHealth/Read | 从 MAOfficeAddinEntityHealth 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | 从 MAOfficeAddinHealth 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAddinHealthEventNRT/Read | 从 MAOfficeAddinHealthEventNRT 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | 从 MAOfficeAddinHealthIssues 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | 从 MAOfficeAddinInstance 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | 从 MAOfficeAddinInstanceReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | 从 MAOfficeAddinReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeApp/Read | 从 MAOfficeApp 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAppCrashesNRT/Read | 从 MAOfficeAppCrashesNRT 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | 从 MAOfficeAppHealth 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | 从 MAOfficeAppInstance 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAppInstanceHealth/Read | 从 MAOfficeAppInstanceHealth 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | 从 MAOfficeAppReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeAppSessionsNRT/Read | 从 MAOfficeAppSessionsNRT 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | 从 MAOfficeBuildInfo 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | 从 MAOfficeCurrencyAssessment 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | 从 MAOfficeCurrencyAssessmentDailyCounts 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | 从 MAOfficeDeploymentStatus 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeDeploymentStatusNRT/Read | 从 MAOfficeDeploymentStatusNRT 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeMacroErrorNRT/Read | 从 MAOfficeMacroErrorNRT 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeMacroGlobalHealth/Read | 从 MAOfficeMacroGlobalHealth 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | 从 MAOfficeMacroHealth 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | 从 MAOfficeMacroHealthIssues 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | 从 MAOfficeMacroIssueInstanceReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | 从 MAOfficeMacroIssueReadiness 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | 从 MAOfficeMacroSummary 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeSuite/Read | 从 MAOfficeSuite 表读取数据 |
> | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | 从 MAOfficeSuiteInstance 表读取数据 |
> | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | 从 MAProposedPilotDevices 表读取数据 |
> | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | 从 MAWindowsBuildInfo 表读取数据 |
> | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | 从 MAWindowsCurrencyAssessment 表读取数据 |
> | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | 从 MAWindowsCurrencyAssessmentDailyCounts 表读取数据 |
> | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | 从 MAWindowsDeploymentStatus 表读取数据 |
> | Microsoft.Insights/Logs/MAWindowsDeploymentStatusNRT/Read | 从 MAWindowsDeploymentStatusNRT 表读取数据 |
> | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | 从 MAWindowsSysReqInstanceReadiness 表读取数据 |
> | Microsoft.Insights/Logs/McasShadowItReporting/Read | 从 McasShadowItReporting 表中读取数据 |
> | Microsoft.Insights/Logs/MicrosoftAzureBastionAuditLogs/Read | 从 MicrosoftAzureBastionAuditLogs 表读取数据 |
> | Microsoft.Insights/Logs/MicrosoftDataShareReceivedSnapshotLog/Read | 从 MicrosoftDataShareReceivedSnapshotLog 表读取数据 |
> | Microsoft.Insights/Logs/MicrosoftDataShareSentSnapshotLog/Read | 从 MicrosoftDataShareSentSnapshotLog 表读取数据 |
> | Microsoft.Insights/Logs/MicrosoftDataShareShareLog/Read | 从 MicrosoftDataShareShareLog 表读取数据 |
> | Microsoft.Insights/Logs/MicrosoftDynamicsTelemetryPerformanceLogs/Read | 从 MicrosoftDynamicsTelemetryPerformanceLogs 表读取数据 |
> | Microsoft.Insights/Logs/MicrosoftDynamicsTelemetrySystemMetricsLogs/Read | 从 MicrosoftDynamicsTelemetrySystemMetricsLogs 表读取数据 |
> | Microsoft.Insights/Logs/MicrosoftHealthcareApisAuditLogs/Read | 从 MicrosoftHealthcareApisAuditLogs 表读取数据 |
> | Microsoft.Insights/Logs/NetworkMonitoring/Read | 从 NetworkMonitoring 表读取数据 |
> | Microsoft.Insights/Logs/OfficeActivity/Read | 从 OfficeActivity 表读取数据 |
> | Microsoft.Insights/Logs/Operation/Read | 从 Operation 表读取数据 |
> | Microsoft.Insights/Logs/OutboundConnection/Read | 从 OutboundConnection 表读取数据 |
> | Microsoft.Insights/Logs/Perf/Read | 从 Perf 表读取数据 |
> | Microsoft.Insights/Logs/ProtectionStatus/Read | 从 ProtectionStatus 表读取数据 |
> | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | 从 ReservedAzureCommonFields 表读取数据 |
> | Microsoft.Insights/Logs/ReservedCommonFields/Read | 从 ReservedCommonFields 表读取数据 |
> | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | 从 SCCMAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | 从 SCOMAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/SecurityAlert/Read | 从 SecurityAlert 表读取数据 |
> | Microsoft.Insights/Logs/SecurityBaseline/Read | 从 SecurityBaseline 表读取数据 |
> | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | 从 SecurityBaselineSummary 表读取数据 |
> | Microsoft.Insights/Logs/SecurityDetection/Read | 从 SecurityDetection 表读取数据 |
> | Microsoft.Insights/Logs/SecurityEvent/Read | 从 SecurityEvent 表读取数据 |
> | Microsoft.Insights/Logs/SecurityIncident/Read | 读取 SecurityIncident 表中的数据 |
> | Microsoft.Insights/Logs/SecurityIoTRawEvent/Read | 读取 SecurityIoTRawEvent 表中的数据 |
> | Microsoft.Insights/Logs/SecurityNestedRecommendation/Read | 读取 SecurityNestedRecommendation 表中的数据 |
> | Microsoft.Insights/Logs/SecurityRecommendation/Read | 读取 SecurityRecommendation 表中的数据 |
> | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | 从 ServiceFabricOperationalEvent 表读取数据 |
> | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | 从 ServiceFabricReliableActorEvent 表读取数据 |
> | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | 从 ServiceFabricReliableServiceEvent 表读取数据 |
> | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | 从 SfBAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | 从 SfBOnlineAssessmentRecommendation 表中读取数据 |
> | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | 从 SharePointOnlineAssessmentRecommendation 表中读取数据 |
> | Microsoft.Insights/Logs/SignalRServiceDiagnosticLogs/Read | 从 SignalRServiceDiagnosticLogs 表读取数据 |
> | Microsoft.Insights/Logs/SigninLogs/Read | 从 SigninLogs 表读取数据 |
> | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | 从 SPAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | 从 SQLAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/SqlDataClassification/Read | 从 SqlDataClassification 表读取数据 |
> | Microsoft.Insights/Logs/SQLQueryPerformance/Read | 从 SQLQueryPerformance 表读取数据 |
> | Microsoft.Insights/Logs/SqlVulnerabilityAssessmentResult/Read | 从 SqlVulnerabilityAssessmentResult 表读取数据 |
> | Microsoft.Insights/Logs/StorageBlobLogs/Read | 从 StorageBlobLogs 表读取数据 |
> | Microsoft.Insights/Logs/StorageFileLogs/Read | 从 StorageFileLogs 表读取数据 |
> | Microsoft.Insights/Logs/StorageQueueLogs/Read | 从 StorageQueueLogs 表读取数据 |
> | Microsoft.Insights/Logs/StorageTableLogs/Read | 从 StorageTableLogs 表读取数据 |
> | Microsoft.Insights/Logs/SucceededIngestion/Read | 从 SucceededIngestion 表读取数据 |
> | Microsoft.Insights/Logs/Syslog/Read | 从 Syslog 表读取数据 |
> | Microsoft.Insights/Logs/SysmonEvent/Read | 从 SysmonEvent 表读取数据 |
> | Microsoft.Insights/Logs/Tables.Custom/Read | 从任何自定义日志读取数据 |
> | Microsoft.Insights/Logs/ThreatIntelligenceIndicator/Read | 读取 ThreatIntelligenceIndicator 表中的数据 |
> | Microsoft.Insights/Logs/TSIIngress/Read | 读取 TSIIngress 表中的数据 |
> | Microsoft.Insights/Logs/UAApp/Read | 从 UAApp 表读取数据 |
> | Microsoft.Insights/Logs/UAComputer/Read | 从 UAComputer 表读取数据 |
> | Microsoft.Insights/Logs/UAComputerRank/Read | 从 UAComputerRank 表读取数据 |
> | Microsoft.Insights/Logs/UADriver/Read | 从 UADriver 表读取数据 |
> | Microsoft.Insights/Logs/UADriverProblemCodes/Read | 从 UADriverProblemCodes 表读取数据 |
> | Microsoft.Insights/Logs/UAFeedback/Read | 从 UAFeedback 表读取数据 |
> | Microsoft.Insights/Logs/UAHardwareSecurity/Read | 从 UAHardwareSecurity 表读取数据 |
> | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | 从 UAIESiteDiscovery 表读取数据 |
> | Microsoft.Insights/Logs/UAOfficeAddIn/Read | 从 UAOfficeAddIn 表读取数据 |
> | Microsoft.Insights/Logs/UAProposedActionPlan/Read | 从 UAProposedActionPlan 表读取数据 |
> | Microsoft.Insights/Logs/UASysReqIssue/Read | 从 UASysReqIssue 表读取数据 |
> | Microsoft.Insights/Logs/UAUpgradedComputer/Read | 从 UAUpgradedComputer 表读取数据 |
> | Microsoft.Insights/Logs/Update/Read | 从 Update 表读取数据 |
> | Microsoft.Insights/Logs/UpdateRunProgress/Read | 从 UpdateRunProgress 表读取数据 |
> | Microsoft.Insights/Logs/UpdateSummary/Read | 从 UpdateSummary 表读取数据 |
> | Microsoft.Insights/Logs/Usage/Read | 从 Usage 表读取数据 |
> | Microsoft.Insights/Logs/UserAccessAnalytics/Read | 从 UserAccessAnalytics 表中读取数据 |
> | Microsoft.Insights/Logs/UserPeerAnalytics/Read | 从 UserPeerAnalytics 表中读取数据 |
> | Microsoft.Insights/Logs/VMBoundPort/Read | 从 VMBoundPort 表读取数据 |
> | Microsoft.Insights/Logs/VMComputer/Read | 从 VMComputer 表读取数据 |
> | Microsoft.Insights/Logs/VMConnection/Read | 从 VMConnection 表读取数据 |
> | Microsoft.Insights/Logs/VMProcess/Read | 从 VMProcess 表读取数据 |
> | Microsoft.Insights/Logs/W3CIISLog/Read | 从 W3CIISLog 表读取数据 |
> | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | 从 WaaSDeploymentStatus 表读取数据 |
> | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | 从 WaaSInsiderStatus 表读取数据 |
> | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | 从 WaaSUpdateStatus 表读取数据 |
> | Microsoft.Insights/Logs/WDAVStatus/Read | 从 WDAVStatus 表读取数据 |
> | Microsoft.Insights/Logs/WDAVThreat/Read | 从 WDAVThreat 表读取数据 |
> | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | 从 WindowsClientAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/WindowsEvent/Read | 从 WindowsEvent 表读取数据 |
> | Microsoft.Insights/Logs/WindowsFirewall/Read | 从 WindowsFirewall 表读取数据 |
> | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | 从 WindowsServerAssessmentRecommendation 表读取数据 |
> | Microsoft.Insights/Logs/WireData/Read | 从 WireData 表读取数据 |
> | Microsoft.Insights/Logs/WorkloadMonitoringPerf/Read | 从 WorkloadMonitoringPerf 表中读取数据 |
> | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | 从 WUDOAggregatedStatus 表读取数据 |
> | Microsoft.Insights/Logs/WUDOStatus/Read | 从 WUDOStatus 表读取数据 |
> | Microsoft.Insights/Logs/WVDCheckpoints/Read | 从 WVDCheckpoints 表读取数据 |
> | Microsoft.Insights/Logs/WVDConnections/Read | 从 WVDConnections 表读取数据 |
> | Microsoft.Insights/Logs/WVDErrors/Read | 从 WVDErrors 表读取数据 |
> | Microsoft.Insights/Logs/WVDFeeds/Read | 从 WVDFeeds 表读取数据 |
> | Microsoft.Insights/Logs/WVDHostRegistrations/Read | 从 WVDHostRegistrations 表读取数据 |
> | Microsoft.Insights/Logs/WVDManagement/Read | 从 WVDManagement 表读取数据 |
> | Microsoft.Insights/MetricAlerts/Write | 创建或更新指标警报 |
> | Microsoft.Insights/MetricAlerts/Delete | 删除指标警报 |
> | Microsoft.Insights/MetricAlerts/Read | 读取指标警报 |
> | Microsoft.Insights/MetricAlerts/Status/Read | 读取指标警报状态 |
> | Microsoft.Insights/MetricBaselines/Read | 读取指标基线 |
> | Microsoft.Insights/MetricDefinitions/Read | 读取指标定义 |
> | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | 读取指标定义 |
> | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | 读取指标定义 |
> | Microsoft.Insights/Metricnamespaces/Read | 读取指标命名空间 |
> | Microsoft.Insights/Metrics/Read | 添加指标 |
> | Microsoft.Insights/Metrics/Microsoft.Insights/Read | 添加指标 |
> | Microsoft.Insights/Metrics/providers/Metrics/Read | 添加指标 |
> | Microsoft.Insights/MyWorkbooks/Read | 读取专用工作簿 |
> | Microsoft.Insights/MyWorkbooks/Write | 创建或更新专用工作簿 |
> | Microsoft.Insights/MyWorkbooks/Delete | 删除专用工作簿 |
> | Microsoft.Insights/Operations/Read | 读取操作 |
> | Microsoft.Insights/PrivateLinkScopeOperationStatuses/Read | 读取专用链接范围内的操作状态 |
> | Microsoft.Insights/PrivateLinkScopes/Read | 读取专用链接范围 |
> | Microsoft.Insights/PrivateLinkScopes/Write | 创建或更新专用链接范围 |
> | Microsoft.Insights/PrivateLinkScopes/Delete | 删除专用链接范围 |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Read | 读取专用终结点连接代理 |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Write | 创建或更新专用终结点连接代理 |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Delete | 删除专用终结点连接代理 |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Validate/Action | 验证专用终结点连接代理 |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Read | 读取专用终结点连接 |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Write | 创建或更新专用终结点连接 |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Delete | 删除专用终结点连接 |
> | Microsoft.Insights/PrivateLinkScopes/PrivateLinkResources/Read | 读取专用链接资源 |
> | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Read | 读取专用链接范围内的资源 |
> | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Write | 创建或更新专用链接范围内的资源 |
> | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Delete | 删除专用链接范围内的资源 |
> | Microsoft.Insights/ScheduledQueryRules/Write | 写入计划的查询规则 |
> | Microsoft.Insights/ScheduledQueryRules/Read | 读取计划的查询规则 |
> | Microsoft.Insights/ScheduledQueryRules/Delete | 删除计划的查询规则 |
> | Microsoft.Insights/Tenants/Register/Action | 初始化 Microsoft Insights 提供程序 |
> | Microsoft.Insights/topology/Read | 读取拓扑 |
> | Microsoft.Insights/transactions/Read | 读取事务 |
> | Microsoft.Insights/Webtests/Write | 写入到 webtest 配置 |
> | Microsoft.Insights/Webtests/Delete | 删除 webtest 配置 |
> | Microsoft.Insights/Webtests/Read | 读取 webtest 配置 |
> | Microsoft.Insights/Webtests/GetToken/Read | 读取 webtest 令牌 |
> | Microsoft.Insights/Webtests/MetricDefinitions/Read | 读取 webtest 指标定义 |
> | Microsoft.Insights/Webtests/Metrics/Read | 读取 webtest 指标 |
> | Microsoft.Insights/Workbooks/Write | 创建或更新工作簿 |
> | Microsoft.Insights/Workbooks/Delete | 删除工作簿 |
> | Microsoft.Insights/Workbooks/Read | 读取工作簿 |
> | Microsoft.Insights/Workbooks/Revisions/Read | 获取工作簿修订版本 |
> | **DataAction** | **说明** |
> | Microsoft.Insights/DataCollectionRules/Data/Write | 将数据发送到数据收集规则 |
> | Microsoft.Insights/Metrics/Write | 写入指标 |
> | Microsoft.Insights/Telemetry/Write | 写入遥测数据 |

### <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

Azure 服务：[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.OperationalInsights/register/action | 将订阅注册到资源提供程序。 |
> | microsoft.operationalinsights/register/action | 注册订阅。 |
> | microsoft.operationalinsights/unregister/action | 取消注册订阅。 |
> | microsoft.operationalinsights/querypacks/action | 执行查询包操作。 |
> | microsoft.operationalinsights/availableservicetiers/read | 获取可用的服务层级。 |
> | Microsoft.OperationalInsights/clusters/read | 获取群集 |
> | Microsoft.OperationalInsights/clusters/write | 创建或更新群集 |
> | Microsoft.OperationalInsights/clusters/delete | 删除群集 |
> | Microsoft.OperationalInsights/deletedWorkspaces/read | 删除软删除期间的工作区。 |
> | Microsoft.OperationalInsights/linkTargets/read | 删除软删除期间的工作区。 |
> | microsoft.operationalinsights/locations/operationStatuses/read | 获取 Log Analytics Azure 异步操作状态。 |
> | microsoft.operationalinsights/operations/read | 列出所有可用的 OperationalInsights Rest API 操作。 |
> | microsoft.operationalinsights/querypacks/write | 创建或更新查询包。 |
> | microsoft.operationalinsights/querypacks/read | 获取查询包。 |
> | microsoft.operationalinsights/querypacks/delete | 删除查询包。 |
> | microsoft.operationalinsights/querypacks/queries/action | 对查询包中的查询执行操作。 |
> | microsoft.operationalinsights/querypacks/queries/write | 创建或更新查询包查询。 |
> | microsoft.operationalinsights/querypacks/queries/read | 获取查询包查询。 |
> | microsoft.operationalinsights/querypacks/queries/delete | 删除查询包查询。 |
> | Microsoft.OperationalInsights/workspaces/write | 创建新的工作区，或者通过提供现有工作区中的客户 ID 链接到现有工作区。 |
> | Microsoft.OperationalInsights/workspaces/read | 获取现有工作区 |
> | Microsoft.OperationalInsights/workspaces/delete | 删除工作区。 如果该工作区在创建时已链接到现有工作区，则不会删除它链接到的工作区。 |
> | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | 为工作区生成注册证书。 此证书用于将 Microsoft System Center Operation Manager 连接到工作区。 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | Microsoft.OperationalInsights/workspaces/listKeys/action | 检索工作区的列表密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 执行搜索查询 |
> | Microsoft.OperationalInsights/workspaces/purge/action | 从工作区中删除指定数据 |
> | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 重新生成指定的工作区共享密钥 |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 使用新引擎进行搜索。 |
> | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 获取搜索架构 V2。 |
> | Microsoft.OperationalInsights/workspaces/api/query/action | 使用新引擎进行搜索。 |
> | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 获取搜索架构 V2。 |
> | Microsoft.OperationalInsights/workspaces/availableservicetiers/read | 工作区的所有可用服务层级的列表。 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 获取配置范围 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 设置配置范围 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 删除配置范围 |
> | microsoft.operationalinsights/workspaces/dataExports/read | 获取特定的数据导出。 |
> | microsoft.operationalinsights/workspaces/dataExports/write | 创建或更新数据导出。 |
> | microsoft.operationalinsights/workspaces/dataExports/delete | 删除特定的数据导出。 |
> | Microsoft.OperationalInsights/workspaces/datasources/read | 获取工作区下面的数据源。 |
> | Microsoft.OperationalInsights/workspaces/datasources/write | 在工作区下面创建/更新数据源。 |
> | Microsoft.OperationalInsights/workspaces/datasources/delete | 删除工作区下面的数据源。 |
> | microsoft.operationalinsights/workspaces/features/clientgroups/memebers/read | 获取资源的客户端组成员。 |
> | microsoft.operationalinsights/workspaces/features/generateMap/read | 获取资源的服务映射。 |
> | microsoft.operationalinsights/workspaces/features/machineGroups/read | 获取服务映射计算机组。 |
> | microsoft.operationalinsights/workspaces/features/servergroups/members/read | 获取资源的服务器组成员。 |
> | Microsoft.OperationalInsights/workspaces/gateways/delete | 删除为工作区配置的网关。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 列出给定的工作区可见的所有智能包，并列出是为该工作区启用还是禁用了智能包。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 为给定的工作区启用智能包。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 为给定的工作区禁用智能包。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/read | 获取给定工作区下的链接服务。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/write | 创建/更新给定工作区下的链接服务。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 删除给定工作区下的链接服务。 |
> | Microsoft.OperationalInsights/workspaces/listKeys/read | 检索工作区的列表密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | Microsoft.OperationalInsights/workspaces/managementGroups/read | 获取已连接到此工作区的 System Center Operations Manager 管理组的名称和元数据。 |
> | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | 获取工作区下的指标定义 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/read | 获取工作区的用户通知设置。 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/write | 设置工作区的用户通知设置。 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | 删除工作区的用户通知设置。 |
> | microsoft.operationalinsights/workspaces/operations/read | 获取 OperationalInsights 工作区操作的状态。 |
> | Microsoft.OperationalInsights/workspaces/providers/Microsoft.Insights/diagnosticSettings/Read | 获取资源的诊断设置 |
> | Microsoft.OperationalInsights/workspaces/providers/Microsoft.Insights/diagnosticSettings/Write | 创建或更新资源的诊断设置 |
> | Microsoft.OperationalInsights/workspaces/providers/Microsoft.Insights/logDefinitions/read | 获取工作区的可用日志 |
> | Microsoft.OperationalInsights/workspaces/query/read | 对工作区中的数据运行查询 |
> | Microsoft.OperationalInsights/workspaces/query/AACAudit/read | 从 AACAudit 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AACHttpRequest/read | 从 AACHttpRequest 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | 从 AADDomainServicesAccountLogon 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | 从 AADDomainServicesAccountManagement 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | 从 AADDomainServicesDirectoryServiceAccess 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | 从 AADDomainServicesLogonLogoff 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | 从 AADDomainServicesPolicyChange 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | 从 AADDomainServicesPrivilegeUse 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADManagedIdentitySignInLogs/read | 从 AADManagedIdentitySignInLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADNonInteractiveUserSignInLogs/read | 从 AADNonInteractiveUserSignInLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADProvisioningLogs/read | 从 AADProvisioningLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADRiskyUsers/read | 从 AADRiskyUsers 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADServicePrincipalSignInLogs/read | 从 AADServicePrincipalSignInLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AADUserRiskEvents/read | 从 AADUserRiskEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ABSBotRequests/read | 从 ABSBotRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ABSChannelToBotRequests/read | 从 ABSChannelToBotRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ABSDependenciesRequests/read | 从 ABSDependenciesRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ACICollaborationAudit/read | 从 ACICollaborationAudit 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ACSAuthIncomingOperations/read | 从 ACSAuthIncomingOperations 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ACSBillingUsage/read | 从 ACSBillingUsage 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ACSChatIncomingOperations/read | 从 ACSChatIncomingOperations 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ACSSMSIncomingOperations/read | 从 ACSSMSIncomingOperations 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | 从 ADAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupAlerts/read | 从 AddonAzureBackupAlerts 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupJobs/read | 从 AddonAzureBackupJobs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupPolicy/read | 从 AddonAzureBackupPolicy 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupProtectedInstance/read | 从 AddonAzureBackupProtectedInstance 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupStorage/read | 从 AddonAzureBackupStorage 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | 从 ADFActivityRun 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | 从 ADFPipelineRun 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSandboxActivityRun/read | 从 ADFSandboxActivityRun 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSandboxPipelineRun/read | 从 ADFSandboxPipelineRun 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSignInLogs/read | 从 ADFSSignInLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISIntegrationRuntimeLogs/read | 从 ADFSSISIntegrationRuntimeLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageEventMessageContext/read | 从 ADFSSISPackageEventMessageContext 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageEventMessages/read | 从 ADFSSISPackageEventMessages 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageExecutableStatistics/read | 从 ADFSSISPackageExecutableStatistics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageExecutionComponentPhases/read | 从 ADFSSISPackageExecutionComponentPhases 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageExecutionDataStatistics/read | 从 ADFSSISPackageExecutionDataStatistics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | 从 ADFTriggerRun 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADPAudit/read | 从 ADPAudit 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADPDiagnostics/read | 从 ADPDiagnostics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADPRequests/read | 从 ADPRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | 从 ADReplicationResult 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | 从 ADSecurityAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADTDigitalTwinsOperation/read | 读取 ADTDigitalTwinsOperation 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADTEventRoutesOperation/read | 读取 ADTEventRoutesOperation 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADTModelsOperation/read | 读取 ADTModelsOperation 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADTQueryOperation/read | 读取 ADTQueryOperation 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADXCommand/read | 从 ADXCommand 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADXIngestionBatching/read | 从 ADXIngestionBatching 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADXJournal/read | 从 ADXJournal 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADXQuery/read | 从 ADXQuery 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADXTableDetails/read | 从 ADXTableDetails 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ADXTableUsageStatistics/read | 从 ADXTableUsageStatistics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AegDeliveryFailureLogs/read | 从 AegDeliveryFailureLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AegPublishFailureLogs/read | 从 AegPublishFailureLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AEWAuditLogs/read | 从 AEWAuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Alert/read | 从 Alert 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | 从 AlertHistory 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterEvent/read | 从 AmlComputeClusterEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterNodeEvent/read | 从 AmlComputeClusterNodeEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeCpuGpuUtilization/read | 从 AmlComputeCpuGpuUtilization 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeInstanceEvent/read | 从 AmlComputeInstanceEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeJobEvent/read | 从 AmlComputeJobEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlDataLabelEvent/read | 从 AmlDataLabelEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlDataSetEvent/read | 从 AmlDataSetEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlDataStoreEvent/read | 从 AmlDataStoreEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlDeploymentEvent/read | 从 AmlDeploymentEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlEnvironmentEvent/read | 从 AmlEnvironmentEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlInferencingEvent/read | 从 AmlInferencingEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlModelsEvent/read | 从 AmlModelsEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlOnlineEndpointConsoleLog/read | 从 AmlOnlineEndpointConsoleLog 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlPipelineEvent/read | 从 AmlPipelineEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlRunEvent/read | 从 AmlRunEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AmlRunStatusChangedEvent/read | 从 AmlRunStatusChangedEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Anomalies/read | 从 Anomalies 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ApiManagementGatewayLogs/read | 从 ApiManagementGatewayLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppAvailabilityResults/read | 从 AppAvailabilityResults 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppBrowserTimings/read | 从 AppBrowserTimings 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | 从 AppCenterError 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppDependencies/read | 从 AppDependencies 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppEvents/read | 从 AppEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppExceptions/read | 从 AppExceptions 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | 从 ApplicationInsights 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppMetrics/read | 从 AppMetrics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppPageViews/read | 从 AppPageViews 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppPerformanceCounters/read | 从 AppPerformanceCounters 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppPlatformLogsforSpring/read | 从 AppPlatformLogsforSpring 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppPlatformSystemLogs/read | 从 AppPlatformSystemLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppRequests/read | 从 AppRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceAntivirusScanAuditLogs/read | 从 AppServiceAntivirusScanAuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceAppLogs/read | 从 AppServiceAppLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceAuditLogs/read | 从 AppServiceAuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceConsoleLogs/read | 从 AppServiceConsoleLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceEnvironmentPlatformLogs/read | 从 AppServiceEnvironmentPlatformLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceFileAuditLogs/read | 从 AppServiceFileAuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceHTTPLogs/read | 从 AppServiceHTTPLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceIPSecAuditLogs/read | 从 AppServiceIPSecAuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppServicePlatformLogs/read | 读取 AppServicePlatformLogs 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppSystemEvents/read | 从 AppSystemEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AppTraces/read | 从 AppTraces 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | 从 AuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | 读取 AutoscaleEvaluationsLog 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | 读取 AutoscaleScaleActionsLog 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | 读取 AWSCloudTrail 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/AWSGuardDuty/read | 从 AWSGuardDuty 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AWSVPCFlow/read | 从 AWSVPCFlow 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | 从 AzureActivity 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AzureActivityV2/read | 从 AzureActivityV2 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | 从 AzureAssessmentRecommendation 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AzureDevOpsAuditing/read | 从 AzureDevOpsAuditing 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AzureDiagnostics/read | 从 AzureDiagnostics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AzureDiagnostics/read | 从 AzureDiagnostics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | 从 AzureMetrics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/BaiClusterEvent/read | 从 BaiClusterEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/BaiClusterNodeEvent/read | 从 BaiClusterNodeEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/BaiJobEvent/read | 从 BaiJobEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/BehaviorAnalytics/read | 读取 BehaviorAnalytics 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | 从 BlockchainApplicationLog 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | 从 BlockchainProxyLog 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CDBCassandraRequests/read | 从 CDBCassandraRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CDBControlPlaneRequests/read | 从 CDBControlPlaneRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CDBDataPlaneRequests/read | 从 CDBDataPlaneRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CDBGremlinRequests/read | 从 CDBGremlinRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CDBMongoRequests/read | 从 CDBMongoRequests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CDBPartitionKeyRUConsumption/read | 从 CDBPartitionKeyRUConsumption 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CDBPartitionKeyStatistics/read | 从 CDBPartitionKeyStatistics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CDBQueryRuntimeStatistics/read | 从 CDBQueryRuntimeStatistics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | 从 CommonSecurityLog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | 从 ComputerGroup 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | 从 ConfigurationChange 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | 从 ConfigurationData 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | 从 ContainerImageInventory 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | 从 ContainerInventory 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | 从 ContainerLog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerLogV2/read | 从 ContainerLogV2 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | 读取 ContainerNodeInventory 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryLoginEvents/read | 从 ContainerRegistryLoginEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryRepositoryEvents/read | 从 ContainerRegistryRepositoryEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | 从 ContainerServiceLog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/CoreAzureBackup/read | 从 CoreAzureBackup 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | 从 DatabricksAccounts 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | 从 DatabricksClusters 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | 从 DatabricksDBFS 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksInstancePools/read | 从 DatabricksInstancePools 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | 从 DatabricksJobs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | 从 DatabricksNotebook 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | 从 DatabricksSecrets 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | 从 DatabricksSQLPermissions 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | 从 DatabricksSSH 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | 从 DatabricksWorkspace 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/dependencies/read | 从 dependencies 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | 从 DeviceAppCrash 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | 从 DeviceAppLaunch 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | 从 DeviceCalendar 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | 从 DeviceCleanup 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | 从 DeviceConnectSession 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | 从 DeviceEtw 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceEvents/read | 从 DeviceEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceFileCertificateInfo/read | 从 DeviceFileCertificateInfo 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceFileEvents/read | 从 DeviceFileEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | 从 DeviceHardwareHealth 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | 从 DeviceHealth 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | 从 DeviceHeartbeat 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceImageLoadEvents/read | 从 DeviceImageLoadEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceInfo/read | 从 DeviceInfo 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceLogonEvents/read | 从 DeviceLogonEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceNetworkEvents/read | 从 DeviceNetworkEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceNetworkInfo/read | 从 DeviceNetworkInfo 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceProcessEvents/read | 从 DeviceProcessEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceRegistryEvents/read | 从 DeviceRegistryEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | 从 DeviceSkypeHeartbeat 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | 从 DeviceSkypeSignIn 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | 从 DHAppReliability 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | 从 DHDriverReliability 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | 从 DHLogonFailures 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | 从 DHLogonMetrics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | 从 DHOSCrashData 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | 从 DHOSReliability 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | 从 DHWipAppLearning 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | 从 DnsEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | 从 DnsInventory 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/DynamicEventCollection/read | 从 DynamicEventCollection 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Dynamics365Activity/read | 读取 Dynamics365Activity 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/EmailAttachmentInfo/read | 从 EmailAttachmentInfo 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/EmailEvents/read | 从 EmailEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/EmailPostDeliveryEvents/read | 从 EmailPostDeliveryEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/EmailUrlInfo/read | 从 EmailUrlInfo 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | 从 ETWEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Event/read | 从 Event 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | 从 ExchangeAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | 从 ExchangeOnlineAssessmentRecommendation 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/FailedIngestion/read | 从 FailedIngestion 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/FunctionAppLogs/read | 从 FunctionAppLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightAmbariClusterAlerts/read | 从 HDInsightAmbariClusterAlerts 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightAmbariSystemMetrics/read | 从 HDInsightAmbariSystemMetrics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHadoopAndYarnLogs/read | 从 HDInsightHadoopAndYarnLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHadoopAndYarnMetrics/read | 从 HDInsightHadoopAndYarnMetrics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHBaseLogs/read | 从 HDInsightHBaseLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHBaseMetrics/read | 从 HDInsightHBaseMetrics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHiveAndLLAPLogs/read | 从 HDInsightHiveAndLLAPLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHiveAndLLAPMetrics/read | 从 HDInsightHiveAndLLAPMetrics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHiveQueryAppStats/read | 从 HDInsightHiveQueryAppStats 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHiveTezAppStats/read | 从 HDInsightHiveTezAppStats 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightJupyterNotebookEvents/read | 从 HDInsightJupyterNotebookEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightKafkaLogs/read | 从 HDInsightKafkaLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightKafkaMetrics/read | 从 HDInsightKafkaMetrics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightOozieLogs/read | 从 HDInsightOozieLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightRangerAuditLogs/read | 从 HDInsightRangerAuditLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSecurityLogs/read | 从 HDInsightSecurityLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkApplicationEvents/read | 从 HDInsightSparkApplicationEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkBlockManagerEvents/read | 从 HDInsightSparkBlockManagerEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkEnvironmentEvents/read | 从 HDInsightSparkEnvironmentEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkExecutorEvents/read | 从 HDInsightSparkExecutorEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkJobEvents/read | 从 HDInsightSparkJobEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkLogs/read | 从 HDInsightSparkLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkSQLExecutionEvents/read | 从 HDInsightSparkSQLExecutionEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkStageEvents/read | 从 HDInsightSparkStageEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkStageTaskAccumulables/read | 从 HDInsightSparkStageTaskAccumulables 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkTaskEvents/read | 从 HDInsightSparkTaskEvents 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightStormLogs/read | 从 HDInsightStormLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightStormMetrics/read | 从 HDInsightStormMetrics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightStormTopologyMetrics/read | 从 HDInsightStormTopologyMetrics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HealthStateChangeEvent/read | 从 HealthStateChangeEvent 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | 从 Heartbeat 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | 读取 HuntingBookmark 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/IdentityInfo/read | 从 IdentityInfo 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | 从 IISAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | 从 InsightsMetrics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | 从 IntuneAuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/IntuneDeviceComplianceOrg/read | 从 IntuneDeviceComplianceOrg 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/IntuneDevices/read | 从 IntuneDevices 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | 从 IntuneOperationalLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/IoTHubDistributedTracing/read | 从 IoTHubDistributedTracing 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | 从 KubeEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/KubeHealth/read | 从 KubeHealth 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/KubeMonAgentEvents/read | 从 KubeMonAgentEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | 从 KubeNodeInventory 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | 从 KubePodInventory 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/KubePVInventory/read | 从 KubePVInventory 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | 从 KubeServices 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/LAQueryLogs/read | 从 LAQueryLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | 从 LinuxAuditLog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | 从 MAApplication 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | 从 MAApplicationHealth 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | 从 MAApplicationHealthAlternativeVersions 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | 从 MAApplicationHealthIssues 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | 从 MAApplicationInstance 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | 从 MAApplicationInstanceReadiness 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | 从 MAApplicationReadiness 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | 从 MADeploymentPlan 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MADevice/read | 从 MADevice 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | 从 MADeviceNotEnrolled 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | 从 MADeviceNRT 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | 从 MADeviceReadiness 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | 从 MADriverInstanceReadiness 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | 从 MADriverReadiness 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | 从 MAProposedPilotDevices 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | 从 MAWindowsBuildInfo 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | 从 MAWindowsCurrencyAssessment 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | 从 MAWindowsCurrencyAssessmentDailyCounts 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | 从 MAWindowsDeploymentStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | 从 MAWindowsDeploymentStatusNRT 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/McasShadowItReporting/read | 从 McasShadowItReporting 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MCCEventLogs/read | 从 MCCEventLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MCVPOperationLogs/read | 从 MCVPOperationLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftAzureBastionAuditLogs/read | 从 MicrosoftAzureBastionAuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareReceivedSnapshotLog/read | 从 MicrosoftDataShareReceivedSnapshotLog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareSentSnapshotLog/read | 从 MicrosoftDataShareSentSnapshotLog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareShareLog/read | 从 MicrosoftDataShareShareLog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDynamicsTelemetryPerformanceLogs/read | 从 MicrosoftDynamicsTelemetryPerformanceLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDynamicsTelemetrySystemMetricsLogs/read | 从 MicrosoftDynamicsTelemetrySystemMetricsLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftHealthcareApisAuditLogs/read | 从 MicrosoftHealthcareApisAuditLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | 从 NetworkMonitoring 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/NetworkSessions/read | 从 NetworkSessions 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/NWConnectionMonitorDestinationListenerResult/read | 从 NWConnectionMonitorDestinationListenerResult 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/NWConnectionMonitorPathResult/read | 从 NWConnectionMonitorPathResult 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/NWConnectionMonitorTestResult/read | 从 NWConnectionMonitorTestResult 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | 从 OfficeActivity 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Operation/read | 从 Operation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Perf/read | 从 Perf 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/PowerBIDatasetsTenant/read | 从 PowerBIDatasetsTenant 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/PowerBIDatasetsTenantPreview/read | 从 PowerBIDatasetsTenantPreview 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/PowerBIDatasetsWorkspace/read | 从 PowerBIDatasetsWorkspace 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/PowerBIDatasetsWorkspacePreview/read | 从 PowerBIDatasetsWorkspacePreview 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | 从 ProtectionStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/PurviewDataSensitivityLogs/read | 从 PurviewDataSensitivityLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/PurviewScanStatusLogs/read | 从 PurviewScanStatusLogs 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/requests/read | 从 requests 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | 从 SCCMAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | 从 SCOMAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecureScoreControls/read | 从 SecureScoreControls 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecureScores/read | 从 SecureScores 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | 从 SecurityAlert 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | 从 SecurityBaseline 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | 从 SecurityBaselineSummary 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | 从 SecurityDetection 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | 从 SecurityEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityIncident/read | 读取 SecurityIncident 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | 读取 SecurityIoTRawEvent 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityNestedRecommendation/read | 读取 SecurityNestedRecommendation 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | 读取 SecurityRecommendation 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityRegulatoryCompliance/read | 从 SecurityRegulatoryCompliance 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SentinelHealth/read | 从 SentinelHealth 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | 从 ServiceFabricOperationalEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | 从 ServiceFabricReliableActorEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | 从 ServiceFabricReliableServiceEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | 从 SfBAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | 从 SfBOnlineAssessmentRecommendation 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | 从 SharePointOnlineAssessmentRecommendation 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SignalRServiceDiagnosticLogs/read | 从 SignalRServiceDiagnosticLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | 从 SigninLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | 从 SPAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | 从 SQLAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SqlAtpStatus/read | 从 SqlAtpStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SqlDataClassification/read | 从 SqlDataClassification 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SQLSecurityAuditEvents/read | 从 SQLSecurityAuditEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | 从 SqlVulnerabilityAssessmentResult 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentScanStatus/read | 从 SqlVulnerabilityAssessmentScanStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/StorageBlobLogs/read | 从 StorageBlobLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/StorageFileLogs/read | 从 StorageFileLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/StorageQueueLogs/read | 从 StorageQueueLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/StorageTableLogs/read | 从 StorageTableLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SucceededIngestion/read | 从 SucceededIngestion 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseBigDataPoolApplicationsEnded/read | 从 SynapseBigDataPoolApplicationsEnded 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseBuiltinSqlPoolRequestsEnded/read | 从 SynapseBuiltinSqlPoolRequestsEnded 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseDXCommand/read | 从 SynapseDXCommand 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseDXFailedIngestion/read | 从 SynapseDXFailedIngestion 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseDXIngestionBatching/read | 从 SynapseDXIngestionBatching 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseDXQuery/read | 从 SynapseDXQuery 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseDXSucceededIngestion/read | 从 SynapseDXSucceededIngestion 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseDXTableDetails/read | 从 SynapseDXTableDetails 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseDXTableUsageStatistics/read | 从 SynapseDXTableUsageStatistics 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseGatewayApiRequests/read | 从 SynapseGatewayApiRequests 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseGatewayEvents/read | 从 SynapseGatewayEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationActivityRuns/read | 从 SynapseIntegrationActivityRuns 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationPipelineRuns/read | 从 SynapseIntegrationPipelineRuns 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationTriggerRuns/read | 从 SynapseIntegrationTriggerRuns 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseRBACEvents/read | 从 SynapseRBACEvents 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseRbacOperations/read | 从 SynapseRbacOperations 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolDmsWorkers/read | 从 SynapseSqlPoolDmsWorkers 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolExecRequests/read | 从 SynapseSqlPoolExecRequests 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolRequestSteps/read | 从 SynapseSqlPoolRequestSteps 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolSqlRequests/read | 从 SynapseSqlPoolSqlRequests 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolWaits/read | 从 SynapseSqlPoolWaits 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Syslog/read | 从 Syslog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | 从任何自定义日志读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | 读取 ThreatIntelligenceIndicator 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/TSIIngress/read | 读取 TSIIngress 表中的数据 |
> | Microsoft.OperationalInsights/workspaces/query/UAApp/read | 从 UAApp 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 从 UAComputer 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | 从 UAComputerRank 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UADriver/read | 从 UADriver 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | 从 UADriverProblemCodes 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | 从 UAFeedback 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | 从 UAIESiteDiscovery 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | 从 UAOfficeAddIn 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | 从 UAProposedActionPlan 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | 从 UASysReqIssue 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | 从 UAUpgradedComputer 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Update/read | 从 Update 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | 从 UpdateRunProgress 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | 从 UpdateSummary 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Usage/read | 从 Usage 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UserAccessAnalytics/read | 从 UserAccessAnalytics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/UserPeerAnalytics/read | 从 UserPeerAnalytics 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | 从 VMBoundPort 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/VMComputer/read | 从 VMComputer 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | 从 VMConnection 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/VMProcess/read | 从 VMProcess 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | 从 W3CIISLog 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | 从 WaaSDeploymentStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | 从 WaaSInsiderStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | 从 WaaSUpdateStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/Watchlist/read | 从 Watchlist 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | 从 WDAVStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | 从 WDAVThreat 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | 从 WindowsClientAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | 从 WindowsEvent 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | 从 WindowsFirewall 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | 从 WindowsServerAssessmentRecommendation 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WireData/read | 从 WireData 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WorkloadDiagnosticLogs/read | 从 WorkloadDiagnosticLogs 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | 从 WorkloadMonitoringPerf 表中读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | 从 WUDOAggregatedStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | 从 WUDOStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WVDAgentHealthStatus/read | 从 WVDAgentHealthStatus 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WVDCheckpoints/read | 从 WVDCheckpoints 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WVDConnections/read | 从 WVDConnections 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WVDErrors/read | 从 WVDErrors 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WVDFeeds/read | 从 WVDFeeds 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WVDHostRegistrations/read | 从 WVDHostRegistrations 表读取数据 |
> | Microsoft.OperationalInsights/workspaces/query/WVDManagement/read | 从 WVDManagement 表读取数据 |
> | microsoft.operationalinsights/workspaces/rules/read | 获取所有警报规则。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | 获取保存的搜索查询 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/write | 创建保存的搜索查询 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 删除保存的搜索查询 |
> | microsoft.operationalinsights/workspaces/savedsearches/results/read | 获取保存的搜索结果。 已放弃 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | 获取计划的搜索。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | 删除计划的搜索。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | 创建或更新计划的搜索。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | 获取计划的搜索操作。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | 删除计划的搜索操作。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | 创建或更新计划的搜索操作。 |
> | Microsoft.OperationalInsights/workspaces/schema/read | 获取工作区的搜索架构。  搜索架构包含公开的字段及其类型。 |
> | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/read | 获取范围内的专用链接代理。 |
> | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/write | 放置范围内的专用链接代理。 |
> | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/delete | 删除范围内的专用链接代理。 |
> | microsoft.operationalinsights/workspaces/search/read | 获取搜索结果。 已弃用。 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 创建新的存储配置。 这些配置用于从现有存储帐户中的某个位置提取数据。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | 获取存储配置。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | 删除存储配置。 这会阻止 Microsoft Operational Insights 从存储帐户中读取数据。 |
> | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | 获取工作区的搜索升级转换失败日志 |
> | Microsoft.OperationalInsights/workspaces/usages/read | 获取工作区的使用情况数据，包括工作区读取的数据量。 |
> | microsoft.operationalinsights/workspaces/views/read | 获取工作区视图。 |
> | microsoft.operationalinsights/workspaces/views/write | 创建或更新工作区视图。 |
> | microsoft.operationalinsights/workspaces/views/delete | 删除工作区视图。 |

### <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

Azure 服务：[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.OperationsManagement/register/action | 将订阅注册到资源提供程序。 |
> | Microsoft.OperationsManagement/managementAssociations/write | 创建新的管理关联 |
> | Microsoft.OperationsManagement/managementAssociations/read | 获取现有管理关联 |
> | Microsoft.OperationsManagement/managementAssociations/delete | 删除现有管理关联 |
> | Microsoft.OperationsManagement/managementConfigurations/write | 创建新的管理配置 |
> | Microsoft.OperationsManagement/managementConfigurations/read | 获取现有管理配置 |
> | Microsoft.OperationsManagement/managementConfigurations/delete | 删除现有管理配置 |
> | Microsoft.OperationsManagement/solutions/write | 创建新的 OMS 解决方案 |
> | Microsoft.OperationsManagement/solutions/read | 获取现有的 OMS 解决方案 |
> | Microsoft.OperationsManagement/solutions/delete | 删除现有的 OMS 解决方案 |

### <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

Azure 服务：[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.WorkloadMonitor/register/action | 将订阅注册到 Microsoft.WorkloadMonitor 资源提供程序 |
> | Microsoft.WorkloadMonitor/unregister/action | 从 Microsoft.WorkloadMonitor 资源提供程序中取消注册订阅 |
> | Microsoft.WorkloadMonitor/monitors/read | 获取监视器及其当前运行状态 |
> | Microsoft.WorkloadMonitor/monitors/history/read | 获取特定监视器的运行状况更改的历史记录 |
> | Microsoft.WorkloadMonitor/operations/read | 获取支持的操作列表 |

## <a name="management--governance"></a>管理 + 治理

### <a name="microsoftadvisor"></a>Microsoft.Advisor

Azure 服务：[Azure 顾问](../advisor/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Advisor/generateRecommendations/action | 获取“生成建议”状态 |
> | Microsoft.Advisor/register/action | 注册 Microsoft 顾问的订阅 |
> | Microsoft.Advisor/unregister/action | 取消注册 Microsoft 顾问的订阅 |
> | Microsoft.Advisor/advisorScore/read | 获取给定订阅的评分数据 |
> | Microsoft.Advisor/configurations/read | 获取配置 |
> | Microsoft.Advisor/configurations/write | 创建/更新配置 |
> | Microsoft.Advisor/generateRecommendations/read | 获取“生成建议”状态 |
> | Microsoft.Advisor/metadata/read | 获取元数据 |
> | Microsoft.Advisor/operations/read | 获取 Microsoft 顾问的操作 |
> | Microsoft.Advisor/recommendations/read | 读取建议 |
> | Microsoft.Advisor/recommendations/available/action | Microsoft 顾问中提供了新建议 |
> | Microsoft.Advisor/recommendations/suppressions/read | 获取禁止显示 |
> | Microsoft.Advisor/recommendations/suppressions/write | 创建/更新禁止显示 |
> | Microsoft.Advisor/recommendations/suppressions/delete | 删除禁止显示 |
> | Microsoft.Advisor/suppressions/read | 获取禁止显示 |
> | Microsoft.Advisor/suppressions/write | 创建/更新禁止显示 |
> | Microsoft.Advisor/suppressions/delete | 删除禁止显示 |

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Azure 服务：[Azure Policy](../governance/policy/overview.md)、[Azure RBAC](overview.md)、[Azure 资源管理器](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Authorization/elevateAccess/action | 向调用方授予租户范围的“用户访问管理员”访问权限 |
> | Microsoft.Authorization/classicAdministrators/read | 读取订阅的管理员。 如果在自定义角色中用作 NotAction，则不起作用。 |
> | Microsoft.Authorization/classicAdministrators/write | 在订阅中添加或修改管理员。 |
> | Microsoft.Authorization/classicAdministrators/delete | 从订阅中删除管理员。 |
> | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 获取订阅的管理员操作状态。 |
> | Microsoft.Authorization/denyAssignments/read | 获取拒绝分配的相关信息。 |
> | Microsoft.Authorization/denyAssignments/write | 在指定范围处创建拒绝分配。 |
> | Microsoft.Authorization/denyAssignments/delete | 在指定范围处删除拒绝分配。 |
> | Microsoft.Authorization/locks/read | 获取指定范围的锁。 |
> | Microsoft.Authorization/locks/write | 添加指定范围的锁。 |
> | Microsoft.Authorization/locks/delete | 删除指定范围的锁。 |
> | Microsoft.Authorization/operations/read | 获取操作列表 |
> | Microsoft.Authorization/permissions/read | 列出调用方在给定范围拥有的所有权限。 |
> | Microsoft.Authorization/policies/audit/action | 因 Azure 策略评估影响程度为“audit”而执行的操作 |
> | Microsoft.Authorization/policies/auditIfNotExists/action | 因 Azure 策略评估影响程度为“auditIfNotExists”而执行的操作 |
> | Microsoft.Authorization/policies/deny/action | 因 Azure 策略评估影响程度为“deny”而执行的操作 |
> | Microsoft.Authorization/policies/deployIfNotExists/action | 因 Azure 策略评估影响程度为“deployIfNotExists”而执行的操作 |
> | Microsoft.Authorization/policyAssignments/read | 获取有关策略分配的信息。 |
> | Microsoft.Authorization/policyAssignments/write | 创建指定范围的策略分配。 |
> | Microsoft.Authorization/policyAssignments/delete | 删除指定范围的策略分配。 |
> | Microsoft.Authorization/policyAssignments/exempt/action | 豁免指定范围内的策略分配。 |
> | Microsoft.Authorization/policyAssignments/privateLinkAssociations/read | 获取有关专用链接关联的信息。 |
> | Microsoft.Authorization/policyAssignments/privateLinkAssociations/write | 创建或更新专用链接关联。 |
> | Microsoft.Authorization/policyAssignments/privateLinkAssociations/delete | 删除专用链接关联。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/read | 获取有关资源管理专用链接的信息。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/write | 创建或更新资源管理专用链接。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/delete | 删除资源管理专用链接。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/read | 获取有关专用终结点连接代理的信息。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/write | 创建或更新专用终结点连接代理。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接代理。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/read | 获取有关专用终结点连接的信息。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/write | 创建或更新专用终结点连接。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/delete | 删除专用终结点连接。 |
> | Microsoft.Authorization/policyDefinitions/read | 获取有关策略定义的信息。 |
> | Microsoft.Authorization/policyDefinitions/write | 创建自定义策略定义。 |
> | Microsoft.Authorization/policyDefinitions/delete | 删除策略定义。 |
> | Microsoft.Authorization/policyExemptions/read | 获取有关策略豁免的信息。 |
> | Microsoft.Authorization/policyExemptions/write | 创建指定范围内的策略豁免。 |
> | Microsoft.Authorization/policyExemptions/delete | 删除指定范围内的策略豁免。 |
> | Microsoft.Authorization/policySetDefinitions/read | 获取有关策略集定义的信息。 |
> | Microsoft.Authorization/policySetDefinitions/write | 创建自定义策略集定义。 |
> | Microsoft.Authorization/policySetDefinitions/delete | 删除策略集定义。 |
> | Microsoft.Authorization/providerOperations/read | 获取可在角色定义中使用的所有资源提供程序的操作。 |
> | Microsoft.Authorization/roleAssignments/read | 获取有关角色分配的信息。 |
> | Microsoft.Authorization/roleAssignments/write | 创建指定范围的角色分配。 |
> | Microsoft.Authorization/roleAssignments/delete | 删除指定范围的角色分配。 |
> | Microsoft.Authorization/roleAssignmentScheduleInstances/read | 获取给定范围内的角色分配计划实例。 |
> | Microsoft.Authorization/roleAssignmentScheduleRequests/read | 获取给定范围内的角色分配计划请求。 |
> | Microsoft.Authorization/roleAssignmentScheduleRequests/write | 创建给定范围内的角色分配计划请求。 |
> | Microsoft.Authorization/roleAssignmentScheduleRequests/cancel/action | 取消挂起的角色分配计划请求。 |
> | Microsoft.Authorization/roleAssignmentSchedules/read | 获取给定范围内的角色分配计划。 |
> | Microsoft.Authorization/roleDefinitions/read | 获取有关角色定义的信息。 |
> | Microsoft.Authorization/roleDefinitions/write | 使用指定的权限和可分配的范围创建或更新自定义角色定义。 |
> | Microsoft.Authorization/roleDefinitions/delete | 删除指定的自定义角色定义。 |
> | Microsoft.Authorization/roleEligibilityScheduleInstances/read | 获取给定范围内的角色资格计划实例。 |
> | Microsoft.Authorization/roleEligibilityScheduleRequests/read | 获取给定范围内的角色资格计划请求。 |
> | Microsoft.Authorization/roleEligibilityScheduleRequests/write | 在给定范围内创建角色资格计划请求。 |
> | Microsoft.Authorization/roleEligibilityScheduleRequests/cancel/action | 取消挂起的角色资格计划请求。 |
> | Microsoft.Authorization/roleEligibilitySchedules/read | 获取给定范围内的角色资格计划。 |
> | Microsoft.Authorization/roleManagementPolicies/read | 获取角色管理策略 |
> | Microsoft.Authorization/roleManagementPolicies/write | 更新角色管理策略 |
> | Microsoft.Authorization/roleManagementPolicyAssignments/read | 获取角色管理策略分配 |

### <a name="microsoftautomation"></a>Microsoft.Automation

Azure 服务：[自动化](../automation/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Automation/register/action | 将订阅注册到 Azure Automation |
> | Microsoft.Automation/automationAccounts/convertGraphRunbookContent/action | 将 Graph Runbook 内容转换为其原始序列化格式，反之亦然 |
> | Microsoft.Automation/automationAccounts/webhooks/action | 生成 Azure 自动化 Webhook 的 URI |
> | Microsoft.Automation/automationAccounts/read | 获取 Azure 自动化帐户 |
> | Microsoft.Automation/automationAccounts/write | 创建或更新 Azure 自动化帐户 |
> | Microsoft.Automation/automationAccounts/listKeys/action | 读取自动化帐户的键 |
> | Microsoft.Automation/automationAccounts/delete | 删除 Azure 自动化帐户 |
> | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | 读取 Azure Automation DSC 的注册信息 |
> | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | 写入重新生成 Azure Automation DSC 密钥的请求 |
> | Microsoft.Automation/automationAccounts/certificates/getCount/action | 读取证书的计数 |
> | Microsoft.Automation/automationAccounts/certificates/read | 获取 Azure 自动化证书资产 |
> | Microsoft.Automation/automationAccounts/certificates/write | 创建或更新 Azure 自动化证书资产 |
> | Microsoft.Automation/automationAccounts/certificates/delete | 删除 Azure 自动化证书资产 |
> | Microsoft.Automation/automationAccounts/compilationjobs/write | 写入 Azure Automation DSC 的编译 |
> | Microsoft.Automation/automationAccounts/compilationjobs/read | 读取 Azure Automation DSC 的编译 |
> | Microsoft.Automation/automationAccounts/compilationjobs/write | 写入 Azure Automation DSC 的编译 |
> | Microsoft.Automation/automationAccounts/compilationjobs/read | 读取 Azure Automation DSC 的编译 |
> | Microsoft.Automation/automationAccounts/configurations/read | 获取 Azure 自动化 DSC 的内容 |
> | Microsoft.Automation/automationAccounts/configurations/getCount/action | 读取 Azure Automation DSC 的内容计数 |
> | Microsoft.Automation/automationAccounts/configurations/write | 写入 Azure Automation DSC 的内容 |
> | Microsoft.Automation/automationAccounts/configurations/delete | 删除 Azure Automation DSC 的内容 |
> | Microsoft.Automation/automationAccounts/configurations/content/read | 读取配置媒体内容 |
> | Microsoft.Automation/automationAccounts/connections/read | 获取 Azure 自动化连接资产 |
> | Microsoft.Automation/automationAccounts/connections/getCount/action | 读取连接的计数 |
> | Microsoft.Automation/automationAccounts/connections/write | 创建或更新 Azure 自动化连接资产 |
> | Microsoft.Automation/automationAccounts/connections/delete | 删除 Azure 自动化连接资产 |
> | Microsoft.Automation/automationAccounts/connectionTypes/read | 获取 Azure 自动化连接类型资产 |
> | Microsoft.Automation/automationAccounts/connectionTypes/write | 创建 Azure 自动化连接类型资产 |
> | Microsoft.Automation/automationAccounts/connectionTypes/delete | 删除 Azure 自动化连接类型资产 |
> | Microsoft.Automation/automationAccounts/credentials/read | 获取 Azure 自动化凭据资产 |
> | Microsoft.Automation/automationAccounts/credentials/getCount/action | 读取凭据的计数 |
> | Microsoft.Automation/automationAccounts/credentials/write | 创建或更新 Azure 自动化凭据资产 |
> | Microsoft.Automation/automationAccounts/credentials/delete | 删除 Azure 自动化凭据资产 |
> | Microsoft.Automation/automationAccounts/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Automation/automationAccounts/diagnosticSettings/write | 设置资源的诊断设置 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 读取混合 Runbook 辅助角色资源 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | 删除混合 Runbook 辅助角色资源 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 读取混合 Runbook 辅助角色资源 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | 删除混合 Runbook 辅助角色资源 |
> | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | 执行作业时获取 Azure 自动化 Runbook 的内容 |
> | Microsoft.Automation/automationAccounts/jobs/read | 获取 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/write | 创建 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | 停止 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | 暂停 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | 恢复 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/output/read | 获取作业的输出 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | 获取 Azure 自动化作业流 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | 获取 Azure 自动化作业流 |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | 获取 Azure 自动化作业计划 |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | 创建 Azure 自动化作业计划 |
> | Microsoft.Automation/automationAccounts/jobSchedules/delete | 删除 Azure 自动化作业计划 |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 获取链接到自动化帐户的工作区 |
> | Microsoft.Automation/automationAccounts/logDefinitions/read | 获取自动化帐户的可用日志 |
> | Microsoft.Automation/automationAccounts/modules/read | 获取 Azure 自动化 Powershell 模块 |
> | Microsoft.Automation/automationAccounts/modules/getCount/action | 获取自动化帐户中的 Powershell 模块的计数 |
> | Microsoft.Automation/automationAccounts/modules/write | 创建或更新 Azure 自动化 Powershell 模块 |
> | Microsoft.Automation/automationAccounts/modules/delete | 删除 Azure 自动化 Powershell 模块 |
> | Microsoft.Automation/automationAccounts/modules/activities/read | 获取 Azure 自动化活动 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | 读取 Azure Automation DSC 的节点配置内容 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/read | 读取 Azure Automation DSC 的节点配置 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/write | 写入 Azure Automation DSC 的节点配置 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | 删除 Azure Automation DSC 的节点配置 |
> | Microsoft.Automation/automationAccounts/nodecounts/read | 读取指定类型的节点计数摘要 |
> | Microsoft.Automation/automationAccounts/nodes/read | 读取 Azure Automation DSC 节点 |
> | Microsoft.Automation/automationAccounts/nodes/write | 创建或更新 Azure Automation DSC 节点 |
> | Microsoft.Automation/automationAccounts/nodes/delete | 删除 Azure Automation DSC 节点 |
> | Microsoft.Automation/automationAccounts/nodes/reports/read | 读取 Azure Automation DSC 报表 |
> | Microsoft.Automation/automationAccounts/nodes/reports/content/read | 读取 Azure Automation DSC 报表内容 |
> | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | 获取 Azure 自动化 TypeField |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/read | 读取 Azure 自动化专用终结点连接代理 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/write | 创建 Azure 自动化专用终结点连接代理 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接请求（groupId 验证） |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/delete | 删除 Azure 自动化专用终结点连接代理 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/operationResults/read | 获取 Azure 自动化专用终结点代理操作结果。 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnections/read | 获取 Azure 自动化专用终结点连接状态 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnections/read | 获取 Azure 自动化专用终结点连接状态 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnections/write | 批准或拒绝 Azure 自动化专用终结点连接 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnections/delete | 删除 Azure 自动化专用终结点连接 |
> | Microsoft.Automation/automationAccounts/privateLinkResources/read | 读取专用终结点的组信息 |
> | Microsoft.Automation/automationAccounts/privateLinkResources/read | 读取专用终结点的组信息 |
> | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | 获取自动化指标定义 |
> | Microsoft.Automation/automationAccounts/python2Packages/read | 获取 Azure 自动化 Python 2 包 |
> | Microsoft.Automation/automationAccounts/python2Packages/write | 创建或更新 Azure 自动化 Python 2 包 |
> | Microsoft.Automation/automationAccounts/python2Packages/delete | 删除 Azure 自动化 Python 2 包 |
> | Microsoft.Automation/automationAccounts/python3Packages/read | 获取 Azure 自动化 Python 3 包 |
> | Microsoft.Automation/automationAccounts/python3Packages/write | 创建或更新 Azure 自动化 Python 3 包 |
> | Microsoft.Automation/automationAccounts/python3Packages/delete | 删除 Azure 自动化 Python 3 包 |
> | Microsoft.Automation/automationAccounts/runbooks/read | 获取 Azure 自动化 Runbook |
> | Microsoft.Automation/automationAccounts/runbooks/getCount/action | 获取 Azure 自动化 Runbook 的计数 |
> | Microsoft.Automation/automationAccounts/runbooks/write | 创建或更新 Azure 自动化 Runbook |
> | Microsoft.Automation/automationAccounts/runbooks/delete | 删除 Azure 自动化 Runbook |
> | Microsoft.Automation/automationAccounts/runbooks/publish/action | 发布 Azure 自动化 Runbook 草稿 |
> | Microsoft.Automation/automationAccounts/runbooks/content/read | 获取 Azure 自动化 Runbook 的内容 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/read | 获取 Azure 自动化 Runbook 草稿 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | 撤消对 Azure 自动化 Runbook 草稿的编辑 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/write | 创建 Azure 自动化 runbook 草稿 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | 创建 Azure 自动化 Runbook 草稿的内容 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | 获取 Azure 自动化 Runbook 草稿操作结果 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | 获取 Azure 自动化 Runbook 草稿测试作业 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | 创建 Azure 自动化 Runbook 草稿测试作业 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | 停止 Azure 自动化 Runbook 草稿测试作业 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | 暂停 Azure 自动化 Runbook 草稿测试作业 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | 恢复 Azure 自动化 Runbook 草稿测试作业 |
> | Microsoft.Automation/automationAccounts/schedules/read | 获取 Azure 自动化计划资产 |
> | Microsoft.Automation/automationAccounts/schedules/getCount/action | 获取 Azure 自动化计划的计数 |
> | Microsoft.Automation/automationAccounts/schedules/write | 创建或更新 Azure 自动化计划资产 |
> | Microsoft.Automation/automationAccounts/schedules/delete | 删除 Azure 自动化计划资产 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/read | 获取 Azure 自动化软件更新配置计算机运行 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurationRuns/read | 获取 Azure 自动化软件更新配置运行 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 创建或更新 Azure 自动化软件更新配置 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 获取 Azure 自动化软件更新配置 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 删除 Azure 自动化软件更新配置 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 创建或更新 Azure 自动化软件更新配置 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 获取 Azure 自动化软件更新配置 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 删除 Azure 自动化软件更新配置 |
> | Microsoft.Automation/automationAccounts/statistics/read | 获取 Azure 自动化统计信息 |
> | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | 获取 Azure 自动化更新部署计算机 |
> | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | 获取 Azure 自动化更新管理修补程序作业 |
> | Microsoft.Automation/automationAccounts/usages/read | 获取 Azure 自动化使用情况 |
> | Microsoft.Automation/automationAccounts/variables/read | 读取 Azure 自动化变量资产 |
> | Microsoft.Automation/automationAccounts/variables/write | 创建或更新 Azure 自动化变量资产 |
> | Microsoft.Automation/automationAccounts/variables/delete | 删除 Azure 自动化变量资产 |
> | Microsoft.Automation/automationAccounts/watchers/write | 创建 Azure 自动化观察程序作业 |
> | Microsoft.Automation/automationAccounts/watchers/read | 获取 Azure 自动化观察程序作业 |
> | Microsoft.Automation/automationAccounts/watchers/delete | 删除 Azure 自动化观察程序作业 |
> | Microsoft.Automation/automationAccounts/watchers/start/action | 启动 Azure 自动化观察程序作业 |
> | Microsoft.Automation/automationAccounts/watchers/stop/action | 停止 Azure 自动化观察程序作业 |
> | Microsoft.Automation/automationAccounts/watchers/streams/read | 获取 Azure 自动化观察程序作业流 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | 创建 Azure 自动化观察程序作业操作 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | 获取 Azure 自动化观察程序作业操作 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | 删除 Azure 自动化观察程序作业操作 |
> | Microsoft.Automation/automationAccounts/webhooks/read | 读取 Azure 自动化 Webhook |
> | Microsoft.Automation/automationAccounts/webhooks/write | 创建或更新 Azure 自动化 Webhook |
> | Microsoft.Automation/automationAccounts/webhooks/delete | 删除 Azure 自动化 Webhook  |
> | Microsoft.Automation/operations/read | 获取可对 Azure 自动化资源使用的操作 |

### <a name="microsoftbatch"></a>Microsoft.Batch

Azure 服务：[批处理](../batch/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Batch/register/action | 注册 Batch 资源提供程序的订阅，并启用 Batch 帐户的创建 |
> | Microsoft.Batch/unregister/action | 取消注册 Batch 资源提供程序的订阅，阻止创建 Batch 帐户 |
> | Microsoft.Batch/batchAccounts/read | 列出 Batch 帐户，或获取 Batch 帐户的属性 |
> | Microsoft.Batch/batchAccounts/write | 创建新的 Batch 帐户，或更新现有的 Batch 帐户 |
> | Microsoft.Batch/batchAccounts/delete | 删除 Batch 帐户 |
> | Microsoft.Batch/batchAccounts/listkeys/action | 列出 Batch 帐户的访问密钥 |
> | Microsoft.Batch/batchAccounts/regeneratekeys/action | 再生成 Batch 帐户的访问密钥 |
> | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | 同步针对 Batch 帐户配置的自动存储帐户的访问密钥 |
> | Microsoft.Batch/batchAccounts/applications/read | 列出应用程序，或获取应用程序的属性 |
> | Microsoft.Batch/batchAccounts/applications/write | 创建新的应用程序，或更新现有的应用程序 |
> | Microsoft.Batch/batchAccounts/applications/delete | 删除应用程序 |
> | Microsoft.Batch/batchAccounts/applications/versions/read | 获取应用程序包的属性 |
> | Microsoft.Batch/batchAccounts/applications/versions/write | 创建新的应用程序包，或更新现有的应用程序包 |
> | Microsoft.Batch/batchAccounts/applications/versions/delete | 删除应用程序包 |
> | Microsoft.Batch/batchAccounts/applications/versions/activate/action | 激活应用程序包 |
> | Microsoft.Batch/batchAccounts/certificateOperationResults/read | 获取 Batch 帐户上长时间运行的证书操作的结果 |
> | Microsoft.Batch/batchAccounts/certificates/read | 列出 Batch 帐户上的证书或获取证书的属性 |
> | Microsoft.Batch/batchAccounts/certificates/write | 在 Batch 帐户上创建新的证书或更新现有证书 |
> | Microsoft.Batch/batchAccounts/certificates/delete | 从 Batch 帐户中删除证书 |
> | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | 取消对 Batch 帐户上证书删除失败的操作 |
> | Microsoft.Batch/batchAccounts/operationResults/read | 获取长时间运行的 Batch 帐户操作的结果 |
> | Microsoft.Batch/batchAccounts/outboundNetworkDependenciesEndpoints/read | 列出 Batch 帐户的出站网络依赖项终结点 |
> | Microsoft.Batch/batchAccounts/poolOperationResults/read | 获取 Batch 帐户上长时间运行的池操作的结果 |
> | Microsoft.Batch/batchAccounts/pools/read | 列出 Batch 帐户上的池，或获取池的属性 |
> | Microsoft.Batch/batchAccounts/pools/write | 在 Batch 帐户上创建新池，或更新现有池 |
> | Microsoft.Batch/batchAccounts/pools/delete | 从 Batch 帐户中删除池 |
> | Microsoft.Batch/batchAccounts/pools/stopResize/action | 停止正在对 Batch 帐户池进行的重设大小操作 |
> | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | 禁用 Batch 帐户池的自动缩放 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnectionProxies/validate/action | 验证 Batch 帐户中的专用终结点连接代理 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnectionProxies/write | 在 Batch 帐户中创建新的专用终结点连接代理 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnectionProxies/read | 获取 Batch 帐户中的专用终结点连接代理 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnectionProxies/delete | 删除 Batch 帐户中的专用终结点连接代理 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnectionProxyResults/read | 获取长时间运行的 Batch 帐户专用终结点连接代理操作的结果 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnectionResults/read | 获取长时间运行的 Batch 帐户专用终结点连接操作的结果 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnections/write | 更新 Batch 帐户上现有的专用终结点连接 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnections/read | 在 Batch 帐户上获取专用终结点连接或列出专用终结点连接 |
> | Microsoft.Batch/batchAccounts/privateLinkResources/read | 在 Batch 帐户上获取专用链接资源的属性或列出专用链接资源 |
> | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | 获取 Batch 服务的可用日志 |
> | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | 获取 Batch 服务的可用指标 |
> | Microsoft.Batch/locations/checkNameAvailability/action | 检查帐户名称是否有效且未被使用。 |
> | Microsoft.Batch/locations/accountOperationResults/read | 获取长时间运行的 Batch 帐户操作的结果 |
> | Microsoft.Batch/locations/cloudServiceSkus/read | 列出给定位置的可用 Batch 支持的云服务 VM 大小 |
> | Microsoft.Batch/locations/quotas/read | 获取指定 Azure 区域中指定订阅的 Batch 配额 |
> | Microsoft.Batch/locations/virtualMachineSkus/read | 列出给定位置的可用 Batch 支持的虚拟机 (VM) 大小 |
> | Microsoft.Batch/operations/read | 列出适用于 Microsoft.Batch 资源提供程序的操作 |
> | **DataAction** | **说明** |
> | Microsoft.Batch/batchAccounts/jobs/read | 列出 Batch 帐户上的作业，或获取作业的属性 |
> | Microsoft.Batch/batchAccounts/jobs/write | 在 Batch 帐户上创建新作业，或更新现有作业 |
> | Microsoft.Batch/batchAccounts/jobs/delete | 从 Batch 帐户中删除作业 |
> | Microsoft.Batch/batchAccounts/jobSchedules/read | 列出 Batch 帐户上的作业计划，或获取作业计划的属性 |
> | Microsoft.Batch/batchAccounts/jobSchedules/write | 在 Batch 帐户上创建新作业计划，或更新现有作业计划 |
> | Microsoft.Batch/batchAccounts/jobSchedules/delete | 从 Batch 帐户中删除作业计划 |

### <a name="microsoftbilling"></a>Microsoft.Billing

Azure 服务：[成本管理 + 计费](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Billing/billingAccounts/billingSubscriptions/downloadDocuments/action | 使用列表中的下载链接下载发票 |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Billing/billingProperty/read |  |
> | Microsoft.Billing/billingProperty/write |  |
> | Microsoft.Billing/invoices/read |  |

### <a name="microsoftblueprint"></a>Microsoft.Blueprint

Azure 服务：[Azure 蓝图](../governance/blueprints/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Blueprint/register/action | 注册 Azure 蓝图资源提供程序 |
> | Microsoft.Blueprint/blueprintAssignments/read | 读取任何蓝图项目 |
> | Microsoft.Blueprint/blueprintAssignments/write | 创建或更新任何蓝图项目 |
> | Microsoft.Blueprint/blueprintAssignments/delete | 删除任何蓝图项目 |
> | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | 获取 Azure 蓝图服务主体对象 ID。 |
> | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 读取任何蓝图项目 |
> | Microsoft.Blueprint/blueprints/read | 读取任何蓝图 |
> | Microsoft.Blueprint/blueprints/write | 创建或更新任何蓝图 |
> | Microsoft.Blueprint/blueprints/delete | 删除任何蓝图 |
> | Microsoft.Blueprint/blueprints/artifacts/read | 读取任何蓝图项目 |
> | Microsoft.Blueprint/blueprints/artifacts/write | 创建或更新任何蓝图项目 |
> | Microsoft.Blueprint/blueprints/artifacts/delete | 删除任何蓝图项目 |
> | Microsoft.Blueprint/blueprints/versions/read | 读取任何蓝图 |
> | Microsoft.Blueprint/blueprints/versions/write | 创建或更新任何蓝图 |
> | Microsoft.Blueprint/blueprints/versions/delete | 删除任何蓝图 |
> | Microsoft.Blueprint/blueprints/versions/artifacts/read | 读取任何蓝图项目 |

### <a name="microsoftcapacity"></a>Microsoft.Capacity

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Capacity/calculateprice/action | 计算任何预留价格 |
> | Microsoft.Capacity/checkoffers/action | 检查任何订阅套餐 |
> | Microsoft.Capacity/checkscopes/action | 检查任何订阅 |
> | Microsoft.Capacity/validatereservationorder/action | 验证任何预留 |
> | Microsoft.Capacity/reservationorders/action | 更新任何预订 |
> | Microsoft.Capacity/register/action | 注册容量资源提供程序，并启用容量资源的创建。 |
> | Microsoft.Capacity/unregister/action | 取消注册任何租户 |
> | Microsoft.Capacity/calculateexchange/action | 计算新购买项目的交换金额和价格，并返回策略错误。 |
> | Microsoft.Capacity/exchange/action | 交换任何预留 |
> | Microsoft.Capacity/listSkus/action | 在采用筛选器的情况下以及无任何限制的情况下列出 SKU |
> | Microsoft.Capacity/appliedreservations/read | 读取所有预订 |
> | Microsoft.Capacity/catalogs/read | 读取预留目录 |
> | Microsoft.Capacity/commercialreservationorders/read | 获取在任何租户中创建的预留订单 |
> | Microsoft.Capacity/operations/read | 读取任何操作 |
> | Microsoft.Capacity/reservationorders/changedirectory/action | 更改任何预留的目录 |
> | Microsoft.Capacity/reservationorders/availablescopes/action | 查找任何可用范围 |
> | Microsoft.Capacity/reservationorders/read | 读取所有预订 |
> | Microsoft.Capacity/reservationorders/write | 创建任何预订 |
> | Microsoft.Capacity/reservationorders/delete | 删除任何预订 |
> | Microsoft.Capacity/reservationorders/reservations/action | 更新任何预订 |
> | Microsoft.Capacity/reservationorders/return/action | 返回任何预留 |
> | Microsoft.Capacity/reservationorders/swap/action | 交换任何预留 |
> | Microsoft.Capacity/reservationorders/split/action | 拆分任何预留 |
> | Microsoft.Capacity/reservationorders/merge/action | 合并任何预留 |
> | Microsoft.Capacity/reservationorders/calculaterefund/action | 计算新购买项目的退款金额和价格，并返回策略错误。 |
> | Microsoft.Capacity/reservationorders/mergeoperationresults/read | 轮询任何合并操作 |
> | Microsoft.Capacity/reservationorders/reservations/availablescopes/action | 查找任何可用范围 |
> | Microsoft.Capacity/reservationorders/reservations/read | 读取所有预订 |
> | Microsoft.Capacity/reservationorders/reservations/write | 创建任何预订 |
> | Microsoft.Capacity/reservationorders/reservations/delete | 删除任何预订 |
> | Microsoft.Capacity/reservationorders/reservations/archive/action | 存档处于终端状态（如过期、拆分等）的预留 |
> | Microsoft.Capacity/reservationorders/reservations/unarchive/action | 取消存档以前存档的预留 |
> | Microsoft.Capacity/reservationorders/reservations/revisions/read | 读取所有预订 |
> | Microsoft.Capacity/reservationorders/splitoperationresults/read | 轮询任何拆分操作 |
> | Microsoft.Capacity/resourceProviders/locations/serviceLimits/read | 获取指定资源和位置的当前服务限制或配额 |
> | Microsoft.Capacity/resourceProviders/locations/serviceLimits/write | 为指定资源和位置创建服务限制或配额 |
> | Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read | 获取指定资源和位置的任何服务限制请求 |
> | Microsoft.Capacity/tenants/register/action | 注册任何租户 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Commerce/register/action | 注册 Microsoft Commerce UsageAggregate 的订阅 |
> | Microsoft.Commerce/unregister/action | 取消注册 Microsoft Commerce UsageAggregate 的订阅 |
> | Microsoft.Commerce/RateCard/read | 返回给定订阅的套餐数据、资源/计量元数据和费率。 |
> | Microsoft.Commerce/UsageAggregates/read | 检索订阅的 Microsoft Azure 消耗量。 结果包含特定时间范围内的聚合用量数据、订阅和资源相关信息。 |

### <a name="microsoftconsumption"></a>Microsoft.Consumption

Azure 服务：[成本管理](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Consumption/register/action | 注册到消耗 RP |
> | Microsoft.Consumption/aggregatedcost/read | 列出管理组的 AggregatedCost。 |
> | Microsoft.Consumption/balances/read | 列出管理组的计费周期的使用情况摘要。 |
> | Microsoft.Consumption/budgets/read | 按订阅或管理组列出预算。 |
> | Microsoft.Consumption/budgets/write | 按订阅或管理组创建和更新预算。 |
> | Microsoft.Consumption/budgets/delete | 按订阅或管理组删除预算。 |
> | Microsoft.Consumption/charges/read | 列出费用 |
> | Microsoft.Consumption/credits/read | 列出信用额度 |
> | Microsoft.Consumption/events/read | 列出事件 |
> | Microsoft.Consumption/externalBillingAccounts/tags/read | 列出 EA 和订阅的标记。 |
> | Microsoft.Consumption/externalSubscriptions/tags/read | 列出 EA 和订阅的标记。 |
> | Microsoft.Consumption/forecasts/read | 列出预测 |
> | Microsoft.Consumption/lots/read | 列出批次 |
> | Microsoft.Consumption/marketplaces/read | 列出 EA 和 WebDirect 订阅的市场资源使用情况。 |
> | Microsoft.Consumption/operationresults/read | 列出 operationresults |
> | Microsoft.Consumption/operations/read | 列出 Microsoft.Consumption 资源提供程序支持的所有操作。 |
> | Microsoft.Consumption/operationstatus/read | 列出 operationstatus |
> | Microsoft.Consumption/pricesheets/read | 列出订阅或管理组的 Pricesheets 数据。 |
> | Microsoft.Consumption/reservationDetails/read | 按预订订单或管理组列出保留实例的使用情况详细信息。 详细信息数据为每天每个实例级别。 |
> | Microsoft.Consumption/reservationRecommendations/read | 列出某个订阅的预留实例的单个或共享建议。 |
> | Microsoft.Consumption/reservationSummaries/read | 按预订订单或管理组列出保留实例的使用情况详细信息。 摘要数据为每月或每天级别。 |
> | Microsoft.Consumption/reservationTransactions/read | 按管理组列出预留实例的事务历史记录。 |
> | Microsoft.Consumption/tags/read | 列出 EA 和订阅的标记。 |
> | Microsoft.Consumption/tenants/register/action | 按租户注册 Microsoft.Consumption 的作用域的操作。 |
> | Microsoft.Consumption/tenants/read | 列出租户 |
> | Microsoft.Consumption/terms/read | 列出订阅或管理组的条款。 |
> | Microsoft.Consumption/usageDetails/read | 列出 EA 和 WebDirect 订阅的范围的使用情况详细信息。 |

### <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

Azure 服务：[成本管理](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.CostManagement/query/action | 按范围查询使用情况数据。 |
> | Microsoft.CostManagement/reports/action | 按范围计划使用情况数据的报告。 |
> | Microsoft.CostManagement/exports/action | 运行指定的导出。 |
> | Microsoft.CostManagement/register/action | 按订阅注册 Microsoft.CostManagement 的范围的操作。 |
> | Microsoft.CostManagement/views/action | 创建视图。 |
> | Microsoft.CostManagement/forecast/action | 按范围预测使用情况数据。 |
> | Microsoft.CostManagement/alerts/write | 更新警报。 |
> | Microsoft.CostManagement/alerts/read | 列出警报。 |
> | Microsoft.CostManagement/budgets/read | 按订阅或管理组列出预算。 |
> | Microsoft.CostManagement/cloudConnectors/read | 列出已经过身份验证的用户的 cloudConnector。 |
> | Microsoft.CostManagement/cloudConnectors/write | 创建或更新指定的 cloudConnector。 |
> | Microsoft.CostManagement/cloudConnectors/delete | 删除指定的 cloudConnector。 |
> | Microsoft.CostManagement/dimensions/read | 按范围列出所有受支持的维度。 |
> | Microsoft.CostManagement/exports/read | 按范围列出导出。 |
> | Microsoft.CostManagement/exports/write | 创建或更新指定的导出。 |
> | Microsoft.CostManagement/exports/delete | 删除指定的导出。 |
> | Microsoft.CostManagement/exports/run/action | 运行导出。 |
> | Microsoft.CostManagement/externalBillingAccounts/read | 列出已经过身份验证的用户的 externalBillingAccount。 |
> | Microsoft.CostManagement/externalBillingAccounts/query/action | 查询外部 BillingAccounts 的使用情况数据。 |
> | Microsoft.CostManagement/externalBillingAccounts/forecast/action | 预测外部 BillingAccounts 的使用情况数据。 |
> | Microsoft.CostManagement/externalBillingAccounts/dimensions/read | 列出外部 BillingAccounts 的所有支持的维度。 |
> | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | 列出已经过身份验证的用户的 externalBillingAccount 中的 externalSubscription。 |
> | Microsoft.CostManagement/externalBillingAccounts/forecast/read | 预测外部 BillingAccounts 的使用情况数据。 |
> | Microsoft.CostManagement/externalBillingAccounts/query/read | 查询外部 BillingAccounts 的使用情况数据。 |
> | Microsoft.CostManagement/externalSubscriptions/read | 列出已经过身份验证的用户的 externalSubscription。 |
> | Microsoft.CostManagement/externalSubscriptions/write | 更新 externalSubscription 的关联管理组 |
> | Microsoft.CostManagement/externalSubscriptions/query/action | 查询外部订阅的使用情况数据。 |
> | Microsoft.CostManagement/externalSubscriptions/forecast/action | 预测外部 BillingAccounts 的使用情况数据。 |
> | Microsoft.CostManagement/externalSubscriptions/dimensions/read | 列出外部订阅的所有支持的维度。 |
> | Microsoft.CostManagement/externalSubscriptions/forecast/read | 预测外部 BillingAccounts 的使用情况数据。 |
> | Microsoft.CostManagement/externalSubscriptions/query/read | 查询外部订阅的使用情况数据。 |
> | Microsoft.CostManagement/forecast/read | 按范围预测使用情况数据。 |
> | Microsoft.CostManagement/operations/read | 列出 Microsoft.CostManagement 资源提供程序支持的所有操作。 |
> | Microsoft.CostManagement/query/read | 按范围查询使用情况数据。 |
> | Microsoft.CostManagement/reports/read | 按范围计划使用情况数据的报告。 |
> | Microsoft.CostManagement/tenants/register/action | 按租户注册 Microsoft.CostManagement 的范围的操作。 |
> | Microsoft.CostManagement/views/read | 列出所有保存的视图。 |
> | Microsoft.CostManagement/views/delete | 删除已保存的视图。 |
> | Microsoft.CostManagement/views/write | 更新视图。 |

### <a name="microsoftdataprotection"></a>Microsoft.DataProtection

Azure 服务：Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DataProtection/backupVaults/write | “创建 BackupVault”操作创建“备份保管库”类型的 Azure 资源 |
> | Microsoft.DataProtection/backupVaults/read | “创建 BackupVault”操作创建“备份保管库”类型的 Azure 资源 |
> | Microsoft.DataProtection/backupVaults/delete | “创建 BackupVault”操作创建“备份保管库”类型的 Azure 资源 |
> | Microsoft.DataProtection/backupVaults/read | 获取资源组中备份保管库的列表 |
> | Microsoft.DataProtection/backupVaults/read | 获取订阅中备份保管库的列表 |
> | Microsoft.DataProtection/backupVaults/validateForBackup/action | 验证备份实例的备份 |
> | Microsoft.DataProtection/backupVaults/backupInstances/write | 创建备份实例 |
> | Microsoft.DataProtection/backupVaults/backupInstances/delete | 删除备份实例 |
> | Microsoft.DataProtection/backupVaults/backupInstances/read | 返回备份实例的详细信息 |
> | Microsoft.DataProtection/backupVaults/backupInstances/read | 返回所有备份实例 |
> | Microsoft.DataProtection/backupVaults/backupInstances/backup/action | 对备份实例执行备份 |
> | Microsoft.DataProtection/backupVaults/backupInstances/sync/action | “同步”操作会重试备份实例上的上一次失败操作，使其处于有效状态。 |
> | Microsoft.DataProtection/backupVaults/backupInstances/stopProtection/action | “停止保护”操作会停止备份实例的备份和保留计划。 现有数据将永久保留。 |
> | Microsoft.DataProtection/backupVaults/backupInstances/suspendBackups/action | “暂停备份”操作仅停止备份实例的备份。 保留活动将会继续，因此，数据将根据策略保留。 |
> | Microsoft.DataProtection/backupVaults/backupInstances/resumeProtection/action | 继续保护 ProtectionStopped BI。 |
> | Microsoft.DataProtection/backupVaults/backupInstances/resumeBackups/action | 继续备份 BackupsSuspended BI。 |
> | Microsoft.DataProtection/backupVaults/backupInstances/validateRestore/action | 验证备份实例的还原 |
> | Microsoft.DataProtection/backupVaults/backupInstances/restore/action | 触发对备份实例的还原 |
> | Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges/action | 查找可还原的时间范围 |
> | Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read | 返回恢复点的详细信息 |
> | Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read | 返回所有恢复点 |
> | Microsoft.DataProtection/backupVaults/backupPolicies/write | 创建备份策略 |
> | Microsoft.DataProtection/backupVaults/backupPolicies/delete | 删除备份策略 |
> | Microsoft.DataProtection/backupVaults/backupPolicies/read | 返回备份策略的详细信息 |
> | Microsoft.DataProtection/backupVaults/backupPolicies/read | 返回所有备份策略 |
> | Microsoft.DataProtection/backupVaults/operationResults/read | 获取备份保管库的修补操作的操作结果 |
> | Microsoft.DataProtection/locations/getBackupStatus/action | 检查恢复服务保管库的备份状态 |
> | Microsoft.DataProtection/locations/checkNameAvailability/action | 检查所请求的 BackupVault 名称是否可用 |
> | Microsoft.DataProtection/locations/operationResults/read | 返回备份保管库的备份操作结果。 |
> | Microsoft.DataProtection/locations/operationStatus/read | 返回备份保管库的备份操作状态。 |
> | Microsoft.DataProtection/providers/operations/read | 操作返回资源提供程序的操作列表 |
> | Microsoft.DataProtection/subscriptions/providers/resourceGuards/read | 获取订阅中的 ResourceGuard 的列表 |
> | Microsoft.DataProtection/subscriptions/resourceGroups/providers/resourceGuards/write | “创建 ResourceGuard”操作创建“ResourceGuard”类型的 Azure 资源 |
> | Microsoft.DataProtection/subscriptions/resourceGroups/providers/resourceGuards/read | “获取 ResourceGuard”操作获取表示“ResourceGuard”类型的 Azure 资源的对象 |
> | Microsoft.DataProtection/subscriptions/resourceGroups/providers/resourceGuards/delete | “删除 ResourceGuard”操作删除“ResourceGuard”类型的指定 Azure 资源 |
> | Microsoft.DataProtection/subscriptions/resourceGroups/providers/resourceGuards/read | 获取资源组中的 ResourceGuard 的列表 |
> | Microsoft.DataProtection/subscriptions/resourceGroups/providers/resourceGuards/write | “更新 ResourceGuard”操作更新“ResourceGuard”类型的 Azure 资源 |
> | Microsoft.DataProtection/subscriptions/resourceGroups/providers/resourceGuards/{operationName}/read | 获取 ResourceGuard 操作请求信息 |
> | Microsoft.DataProtection/subscriptions/resourceGroups/providers/resourceGuards/{operationName}/read | 获取 ResourceGuard 默认操作请求信息 |

### <a name="microsoftfeatures"></a>Microsoft.Features

Azure 服务：[Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Features/register/action | 注册某个订阅的功能。 |
> | Microsoft.Features/featureProviders/subscriptionFeatureRegistrations/read | 获取给定资源提供程序中某个订阅的功能注册。 |
> | Microsoft.Features/featureProviders/subscriptionFeatureRegistrations/write | 添加给定资源提供程序中某个订阅的功能注册。 |
> | Microsoft.Features/featureProviders/subscriptionFeatureRegistrations/delete | 删除给定资源提供程序中某个订阅的功能注册。 |
> | Microsoft.Features/features/read | 获取订阅的功能。 |
> | Microsoft.Features/operations/read | 获取操作列表。 |
> | Microsoft.Features/providers/features/read | 获取给定资源提供程序中某个订阅的功能。 |
> | Microsoft.Features/providers/features/register/action | 在给定的资源提供程序中注册某个订阅的功能。 |
> | Microsoft.Features/providers/features/unregister/action | 取消注册给定资源提供程序中的订阅的功能。 |
> | Microsoft.Features/subscriptionFeatureRegistrations/read | 获取某个订阅的功能注册。 |

### <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

Azure 服务：[Azure Policy](../governance/policy/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.GuestConfiguration/register/action | 注册 Microsoft.GuestConfiguration 资源提供程序的订阅。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 创建新的来宾配置分配。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | 获取来宾配置分配。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | 删除来宾配置分配。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | 获取来宾配置分配报告。 |
> | Microsoft.GuestConfiguration/operations/read | 获取 Microsoft.GuestConfiguration 资源提供程序的操作 |

### <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

Azure 服务：[Azure Arc](../azure-arc/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.HybridCompute/register/action | 注册 Microsoft.HybridCompute 资源提供程序的订阅 |
> | Microsoft.HybridCompute/unregister/action | 取消注册 Microsoft.HybridCompute 资源提供程序的订阅 |
> | Microsoft.HybridCompute/locations/operationresults/read | 读取 Microsoft.HybridCompute 资源提供程序的操作状态 |
> | Microsoft.HybridCompute/locations/operationstatus/read | 读取 Microsoft.HybridCompute 资源提供程序的操作状态 |
> | Microsoft.HybridCompute/locations/privateLinkScopes/read | 读取任何 Azure Arc privateLinkScopes 的完整详细信息 |
> | Microsoft.HybridCompute/locations/updateCenterOperationResults/read | 在计算机上读取更新中心操作的状态 |
> | Microsoft.HybridCompute/machines/read | 读取任何 Azure Arc 计算机 |
> | Microsoft.HybridCompute/machines/write | 写入 Azure Arc 计算机 |
> | Microsoft.HybridCompute/machines/delete | 删除 Azure Arc 计算机 |
> | Microsoft.HybridCompute/machines/UpgradeExtensions/action | 升级 Azure Arc 计算机上的扩展 |
> | Microsoft.HybridCompute/machines/assessPatches/action | 评估任何 Azure Arc 计算机以获取缺失的软件补丁 |
> | Microsoft.HybridCompute/machines/installPatches/action | 在任何 Azure Arc 计算机上安装补丁 |
> | Microsoft.HybridCompute/machines/extensions/read | 读取任何 Azure Arc 扩展 |
> | Microsoft.HybridCompute/machines/extensions/write | 安装或更新 Azure Arc 扩展 |
> | Microsoft.HybridCompute/machines/extensions/delete | 删除 Azure Arc 扩展 |
> | Microsoft.HybridCompute/machines/patchAssessmentResults/read | 读取任何 Azure Arc patchAssessmentResults |
> | Microsoft.HybridCompute/machines/patchAssessmentResults/softwarePatches/read | 读取任何 Azure Arc patchAssessmentResults/softwarePatches |
> | Microsoft.HybridCompute/machines/patchInstallationResults/read | 读取任何 Azure Arc patchInstallationResults |
> | Microsoft.HybridCompute/machines/patchInstallationResults/softwarePatches/read | 读取任何 Azure Arc patchInstallationResults/softwarePatches |
> | Microsoft.HybridCompute/operations/read | 读取适用于服务器的 Azure Arc 的所有操作 |
> | Microsoft.HybridCompute/privateLinkScopes/read | 读取任何 Azure Arc privateLinkScopes |
> | Microsoft.HybridCompute/privateLinkScopes/write | 写入 Azure Arc privateLinkScopes |
> | Microsoft.HybridCompute/privateLinkScopes/delete | 删除 Azure Arc privateLinkScopes |
> | Microsoft.HybridCompute/privateLinkScopes/privateEndpointConnectionProxies/read | 读取任何 Azure Arc privateEndpointConnectionProxies |
> | Microsoft.HybridCompute/privateLinkScopes/privateEndpointConnectionProxies/write | 写入 Azure Arc privateEndpointConnectionProxies |
> | Microsoft.HybridCompute/privateLinkScopes/privateEndpointConnectionProxies/delete | 删除 Azure Arc privateEndpointConnectionProxies |
> | Microsoft.HybridCompute/privateLinkScopes/privateEndpointConnectionProxies/validate/action | 验证 Azure Arc privateEndpointConnectionProxies |
> | Microsoft.HybridCompute/privateLinkScopes/privateEndpointConnections/read | 读取任何 Azure Arc privateEndpointConnections |
> | Microsoft.HybridCompute/privateLinkScopes/privateEndpointConnections/write | 写入 Azure Arc privateEndpointConnections |
> | Microsoft.HybridCompute/privateLinkScopes/privateEndpointConnections/delete | 删除 Azure Arc privateEndpointConnections |
> | **DataAction** | **说明** |
> | Microsoft.HybridCompute/machines/login/action | 以普通用户身份登录 Azure Arc 计算机 |
> | Microsoft.HybridCompute/machines/loginAsAdmin/action | 使用 Windows 管理员或 Linux 根用户权限登录 Azure Arc 计算机 |

### <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

Azure 服务：[启用了 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Kubernetes/register/action | 向 Microsoft.Kubernetes 资源提供程序注册订阅 |
> | Microsoft.Kubernetes/unregister/action | 从 Microsoft.Kubernetes 资源提供程序中注销订阅 |
> | Microsoft.Kubernetes/connectedClusters/Read | 读取 connectedClusters |
> | Microsoft.Kubernetes/connectedClusters/Write | 写入 connectedClusters |
> | Microsoft.Kubernetes/connectedClusters/Delete | 删除 connectedClusters |
> | Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action | 列出 clusterUser 凭据 |
> | Microsoft.Kubernetes/locations/operationstatuses/read | 读取操作状态 |
> | Microsoft.Kubernetes/locations/operationstatuses/write | 写入操作状态 |
> | Microsoft.Kubernetes/operations/read | 列出 Microsoft.Kubernetes 资源提供程序中可用的操作 |
> | Microsoft.Kubernetes/RegisteredSubscriptions/read | 读取注册订阅 |
> | **DataAction** | **说明** |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/initializerconfigurations/read | 读取 initializerconfigurations |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/initializerconfigurations/write | 写入 initializerconfigurations |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/initializerconfigurations/delete | 删除 initializerconfigurations |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/read | 读取 mutatingwebhookconfigurations |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/write | 写入 mutatingwebhookconfigurations |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/delete | 删除 mutatingwebhookconfigurations |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/read | 读取 validatingwebhookconfigurations |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/write | 写入 validatingwebhookconfigurations |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/delete | 删除 validatingwebhookconfigurations |
> | Microsoft.Kubernetes/connectedClusters/api/read | 读取 API |
> | Microsoft.Kubernetes/connectedClusters/api/v1/read | 读取 API/v1 |
> | Microsoft.Kubernetes/connectedClusters/apiextensions.k8s.io/customresourcedefinitions/read | 读取 customresourcedefinitions |
> | Microsoft.Kubernetes/connectedClusters/apiextensions.k8s.io/customresourcedefinitions/write | 写入 customresourcedefinitions |
> | Microsoft.Kubernetes/connectedClusters/apiextensions.k8s.io/customresourcedefinitions/delete | 删除 customresourcedefinitions |
> | Microsoft.Kubernetes/connectedClusters/apiregistration.k8s.io/apiservices/read | 读取 apiservices |
> | Microsoft.Kubernetes/connectedClusters/apiregistration.k8s.io/apiservices/write | 写入 apiservices |
> | Microsoft.Kubernetes/connectedClusters/apiregistration.k8s.io/apiservices/delete | 删除 apiservices |
> | Microsoft.Kubernetes/connectedClusters/apis/read | 读取 API |
> | Microsoft.Kubernetes/connectedClusters/apis/admissionregistration.k8s.io/read | 读取 admissionregistration.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/admissionregistration.k8s.io/v1/read | 读取 admissionregistration.k8s.io/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/admissionregistration.k8s.io/v1beta1/read | 读取 admissionregistration.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/apiextensions.k8s.io/read | 读取 apiextensions.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/apiextensions.k8s.io/v1/rea | 读取 apiextensions.k8s.io/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/apiextensions.k8s.io/v1beta1/read | 读取 apiextensions.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/apiregistration.k8s.io/read | 读取 apiregistration.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/apiregistration.k8s.io/v1/read | 读取 apiregistration.k8s.io/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/apiregistration.k8s.io/v1beta1/read | 读取 apiregistration.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/apps/read | 读取 apps |
> | Microsoft.Kubernetes/connectedClusters/apis/apps/v1beta1/read | 读取 apps/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/apps/v1beta2/read | 读取 v1beta2 |
> | Microsoft.Kubernetes/connectedClusters/apis/authentication.k8s.io/read | 读取 authentication.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/authentication.k8s.io/v1/read | 读取 authentication.k8s.io/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/authentication.k8s.io/v1beta1/read | 读取 authentication.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/authorization.k8s.io/read | 读取 authorization.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/authorization.k8s.io/v1/read | 读取 authorization.k8s.io/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/authorization.k8s.io/v1beta1/read | 读取 authorization.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/autoscaling/read | 读取 autoscaling |
> | Microsoft.Kubernetes/connectedClusters/apis/autoscaling/v1/read | 读取 autoscaling/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/autoscaling/v2beta1/read | 读取 autoscaling/v2beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/autoscaling/v2beta2/read | 读取 autoscaling/v2beta2 |
> | Microsoft.Kubernetes/connectedClusters/apis/batch/read | 读取 batch |
> | Microsoft.Kubernetes/connectedClusters/apis/batch/v1/read | 读取batch/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/batch/v1beta1/read | 读取 batch/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/certificates.k8s.io/read | 读取 certificates.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/certificates.k8s.io/v1beta1/read | 读取 certificates.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/coordination.k8s.io/read | 读取 coordination.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/coordination.k8s.io/v1/read | 读取 coordination/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/coordination.k8s.io/v1beta1/read | 读取 coordination.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/events.k8s.io/read | 读取 events.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/events.k8s.io/v1beta1/read | 读取 events.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/extensions/read | 读取 extensions |
> | Microsoft.Kubernetes/connectedClusters/apis/extensions/v1beta1/read | 读取 extensions/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/metrics.k8s.io/read | 读取 metrics.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/metrics.k8s.io/v1beta1/read | 读取 metrics.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/networking.k8s.io/read | 读取 networking.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/networking.k8s.io/v1/read | 读取 networking/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/networking.k8s.io/v1beta1/read | 读取 networking.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/node.k8s.io/read | 读取 node.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/node.k8s.io/v1beta1/read | 读取 node.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/policy/read | 读取 policy |
> | Microsoft.Kubernetes/connectedClusters/apis/policy/v1beta1/read | 读取 policy/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/rbac.authorization.k8s.io/read | 读取 rbac.authorization.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/rbac.authorization.k8s.io/v1/read | 读取 rbac.authorization/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/rbac.authorization.k8s.io/v1beta1/read | 读取 rbac.authorization.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/scheduling.k8s.io/read | 读取 scheduling.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/scheduling.k8s.io/v1/read | 读取 scheduling/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/scheduling.k8s.io/v1beta1/read | 读取 scheduling.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apis/storage.k8s.io/read | 读取 storage.k8s.io |
> | Microsoft.Kubernetes/connectedClusters/apis/storage.k8s.io/v1/read | 读取 storage/v1 |
> | Microsoft.Kubernetes/connectedClusters/apis/storage.k8s.io/v1beta1/read | 读取 storage.k8s.io/v1beta1 |
> | Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read | 读取 controllerrevisions |
> | Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/write | 写入 controllerrevisions |
> | Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/delete | 删除 controllerrevisions |
> | Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read | 读取 daemonsets |
> | Microsoft.Kubernetes/connectedClusters/apps/daemonsets/write | 写入 daemonsets |
> | Microsoft.Kubernetes/connectedClusters/apps/daemonsets/delete | 删除 daemonsets |
> | Microsoft.Kubernetes/connectedClusters/apps/deployments/read | 读取 deployments |
> | Microsoft.Kubernetes/connectedClusters/apps/deployments/write | 写入 deployments |
> | Microsoft.Kubernetes/connectedClusters/apps/deployments/delete | 删除 deployments |
> | Microsoft.Kubernetes/connectedClusters/apps/replicasets/read | 读取 replicasets |
> | Microsoft.Kubernetes/connectedClusters/apps/replicasets/write | 写入 replicasets |
> | Microsoft.Kubernetes/connectedClusters/apps/replicasets/delete | 删除 replicasets |
> | Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read | 读取 statefulsets |
> | Microsoft.Kubernetes/connectedClusters/apps/statefulsets/write | 写入 statefulsets |
> | Microsoft.Kubernetes/connectedClusters/apps/statefulsets/delete | 删除 statefulsets |
> | Microsoft.Kubernetes/connectedClusters/authentication.k8s.io/tokenreviews/write | 写入 tokenreviews |
> | Microsoft.Kubernetes/connectedClusters/authentication.k8s.io/userextras/impersonate/action | 模拟 userextras |
> | Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write | 写入 localsubjectaccessreviews |
> | Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/selfsubjectaccessreviews/write | 写入 selfsubjectaccessreviews |
> | Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/selfsubjectrulesreviews/write | 写入 selfsubjectrulesreviews |
> | Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/subjectaccessreviews/write | 写入 subjectaccessreviews |
> | Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read | 读取 horizontalpodautoscalers |
> | Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/write | 写入 horizontalpodautoscalers |
> | Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/delete | 删除 horizontalpodautoscalers |
> | Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read | 读取 cronjobs |
> | Microsoft.Kubernetes/connectedClusters/batch/cronjobs/write | 写入 cronjobs |
> | Microsoft.Kubernetes/connectedClusters/batch/cronjobs/delete | 删除 cronjobs |
> | Microsoft.Kubernetes/connectedClusters/batch/jobs/read | 读取作业 |
> | Microsoft.Kubernetes/connectedClusters/batch/jobs/write | 写入 jobs |
> | Microsoft.Kubernetes/connectedClusters/batch/jobs/delete | 删除 jobs |
> | Microsoft.Kubernetes/connectedClusters/bindings/write | 写入 bindings |
> | Microsoft.Kubernetes/connectedClusters/certificates.k8s.io/certificatesigningrequests/read | 读取 certificatesigningrequests |
> | Microsoft.Kubernetes/connectedClusters/certificates.k8s.io/certificatesigningrequests/write | 写入 certificatesigningrequests |
> | Microsoft.Kubernetes/connectedClusters/certificates.k8s.io/certificatesigningrequests/delete | 删除 certificatesigningrequests |
> | Microsoft.Kubernetes/connectedClusters/clusterconfig.azure.com/azureclusteridentityrequests/read | 读取 azureclusteridentityrequests |
> | Microsoft.Kubernetes/connectedClusters/clusterconfig.azure.com/azureclusteridentityrequests/write | 写入 azureclusteridentityrequests |
> | Microsoft.Kubernetes/connectedClusters/clusterconfig.azure.com/azureclusteridentityrequests/delete | 删除 azureclusteridentityrequests |
> | Microsoft.Kubernetes/connectedClusters/componentstatuses/read | 读取 componentstatuses |
> | Microsoft.Kubernetes/connectedClusters/componentstatuses/write | 写入 componentstatuses |
> | Microsoft.Kubernetes/connectedClusters/componentstatuses/delete | 删除 componentstatuses |
> | Microsoft.Kubernetes/connectedClusters/configmaps/read | 读取 configmaps |
> | Microsoft.Kubernetes/connectedClusters/configmaps/write | 写入 configmaps |
> | Microsoft.Kubernetes/connectedClusters/configmaps/delete | 删除 configmaps |
> | Microsoft.Kubernetes/connectedClusters/coordination.k8s.io/leases/read | 读取 leases |
> | Microsoft.Kubernetes/connectedClusters/coordination.k8s.io/leases/write | 写入 leases |
> | Microsoft.Kubernetes/connectedClusters/coordination.k8s.io/leases/delete | 删除 leases |
> | Microsoft.Kubernetes/connectedClusters/endpoints/read | 读取 endpoints |
> | Microsoft.Kubernetes/connectedClusters/endpoints/write | 写入 endpoints |
> | Microsoft.Kubernetes/connectedClusters/endpoints/delete | 删除 endpoints |
> | Microsoft.Kubernetes/connectedClusters/events/read | 读取 events |
> | Microsoft.Kubernetes/connectedClusters/events/write | 写入 events |
> | Microsoft.Kubernetes/connectedClusters/events/delete | 删除 events |
> | Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read | 读取 events |
> | Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/write | 写入 events |
> | Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/delete | 删除 events |
> | Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read | 读取 daemonsets |
> | Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/write | 写入 daemonsets |
> | Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/delete | 删除 daemonsets |
> | Microsoft.Kubernetes/connectedClusters/extensions/deployments/read | 读取 deployments |
> | Microsoft.Kubernetes/connectedClusters/extensions/deployments/write | 写入 deployments |
> | Microsoft.Kubernetes/connectedClusters/extensions/deployments/delete | 删除 deployments |
> | Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read | 读取 ingresses |
> | Microsoft.Kubernetes/connectedClusters/extensions/ingresses/write | 写入 ingresses |
> | Microsoft.Kubernetes/connectedClusters/extensions/ingresses/delete | 删除 ingresses |
> | Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read | 读取 networkpolicies |
> | Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/write | 写入 networkpolicies |
> | Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/delete | 删除 networkpolicies |
> | Microsoft.Kubernetes/connectedClusters/extensions/podsecuritypolicies/read | 读取 podsecuritypolicies |
> | Microsoft.Kubernetes/connectedClusters/extensions/podsecuritypolicies/write | 写入 podsecuritypolicies |
> | Microsoft.Kubernetes/connectedClusters/extensions/podsecuritypolicies/delete | 删除 podsecuritypolicies |
> | Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read | 读取 replicasets |
> | Microsoft.Kubernetes/connectedClusters/extensions/replicasets/write | 写入 replicasets |
> | Microsoft.Kubernetes/connectedClusters/extensions/replicasets/delete | 删除 replicasets |
> | Microsoft.Kubernetes/connectedClusters/groups/impersonate/action | 模拟 groups |
> | Microsoft.Kubernetes/connectedClusters/healthz/read | 读取 healthz |
> | Microsoft.Kubernetes/connectedClusters/healthz/autoregister-completion/read | 读取 autoregister-completion |
> | Microsoft.Kubernetes/connectedClusters/healthz/etcd/read | 读取 etcd |
> | Microsoft.Kubernetes/connectedClusters/healthz/log/read | 读取 log |
> | Microsoft.Kubernetes/connectedClusters/healthz/ping/read | 读取 ping |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/apiservice-openapi-controller/read | 读取 apiservice-openapi-controller |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/apiservice-registration-controller/read | 读取 apiservice-registration-controller |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/apiservice-status-available-controller/read | 读取 apiservice-status-available-controller |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/bootstrap-controller/read | 读取 bootstrap-controller |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/ca-registration/read | 读取 ca-registration |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/crd-informer-synced/read | 读取 crd-informer-synced |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/generic-apiserver-start-informers/read | 读取 generic-apiserver-start-informers |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/kube-apiserver-autoregistration/read | 读取 kube-apiserver-autoregistration |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/rbac/bootstrap-roles/read | 读取 bootstrap-roles |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/scheduling/bootstrap-system-priority-classes/read | 读取 bootstrap-system-priority-classes |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/start-apiextensions-controllers/read | 读取 start-apiextensions-controllers |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/start-apiextensions-informers/read | 读取 start-apiextensions-informers |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/start-kube-aggregator-informers/read | 读取 start-kube-aggregator-informers |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/start-kube-apiserver-admission-initializer/read | 读取 start-kube-apiserver-admission-initializer |
> | Microsoft.Kubernetes/connectedClusters/limitranges/read | 读取 limitranges |
> | Microsoft.Kubernetes/connectedClusters/limitranges/write | 写入 limitranges |
> | Microsoft.Kubernetes/connectedClusters/limitranges/delete | 删除 limitranges |
> | Microsoft.Kubernetes/connectedClusters/livez/read | 读取 livez |
> | Microsoft.Kubernetes/connectedClusters/livez/autoregister-completion/read | 读取 autoregister-completion |
> | Microsoft.Kubernetes/connectedClusters/livez/etcd/read | 读取 etcd |
> | Microsoft.Kubernetes/connectedClusters/livez/log/read | 读取 log |
> | Microsoft.Kubernetes/connectedClusters/livez/ping/read | 读取 ping |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/apiservice-openapi-controller/read | 读取 apiservice-openapi-controller |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/apiservice-registration-controller/read | 读取 apiservice-registration-controller |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/apiservice-status-available-controller/read | 读取 apiservice-status-available-controller |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/bootstrap-controller/read | 读取 bootstrap-controller |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/ca-registration/read | 读取 ca-registration |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/crd-informer-synced/read | 读取 crd-informer-synced |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/generic-apiserver-start-informers/read | 读取 generic-apiserver-start-informers |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/kube-apiserver-autoregistration/read | 读取 kube-apiserver-autoregistration |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/rbac/bootstrap-roles/read | 读取 bootstrap-roles |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/scheduling/bootstrap-system-priority-classes/read | 读取 bootstrap-system-priority-classes |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/start-apiextensions-controllers/read | 读取 start-apiextensions-controllers |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/start-apiextensions-informers/read | 读取 start-apiextensions-informers |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/start-kube-aggregator-informers/read | 读取 start-kube-aggregator-informers |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/start-kube-apiserver-admission-initializer/read | 读取 start-kube-apiserver-admission-initializer |
> | Microsoft.Kubernetes/connectedClusters/logs/read | 读取 logs |
> | Microsoft.Kubernetes/connectedClusters/metrics/read | 读取 metrics |
> | Microsoft.Kubernetes/connectedClusters/metrics.k8s.io/nodes/read | 读取 nodes |
> | Microsoft.Kubernetes/connectedClusters/metrics.k8s.io/pods/read | 读取 Pod |
> | Microsoft.Kubernetes/connectedClusters/namespaces/read | 读取 namespaces |
> | Microsoft.Kubernetes/connectedClusters/namespaces/write | 写入 namespaces |
> | Microsoft.Kubernetes/connectedClusters/namespaces/delete | 删除 namespaces |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read | 读取 ingresses |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/write | 写入 ingresses |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/delete | 删除 ingresses |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read | 读取 networkpolicies |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/write | 写入 networkpolicies |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/delete | 删除 networkpolicies |
> | Microsoft.Kubernetes/connectedClusters/node.k8s.io/runtimeclasses/read | 读取 runtimeclasses |
> | Microsoft.Kubernetes/connectedClusters/node.k8s.io/runtimeclasses/write | 写入 runtimeclasses |
> | Microsoft.Kubernetes/connectedClusters/node.k8s.io/runtimeclasses/delete | 删除 runtimeclasses |
> | Microsoft.Kubernetes/connectedClusters/nodes/read | 读取 nodes |
> | Microsoft.Kubernetes/connectedClusters/nodes/write | 写入 nodes |
> | Microsoft.Kubernetes/connectedClusters/nodes/delete | 删除 nodes |
> | Microsoft.Kubernetes/connectedClusters/openapi/v2/read | 读取 v2 |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read | 读取 persistentvolumeclaims |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/write | 写入 persistentvolumeclaims |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/delete | 删除 persistentvolumeclaims |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumes/read | 读取 persistentvolumes |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumes/write | 写入 persistentvolumes |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumes/delete | 删除 persistentvolumes |
> | Microsoft.Kubernetes/connectedClusters/pods/read | 读取 Pod |
> | Microsoft.Kubernetes/connectedClusters/pods/write | 写入 Pod |
> | Microsoft.Kubernetes/connectedClusters/pods/delete | 删除 Pod |
> | Microsoft.Kubernetes/connectedClusters/podtemplates/read | 读取 podtemplates |
> | Microsoft.Kubernetes/connectedClusters/podtemplates/write | 写入 podtemplates |
> | Microsoft.Kubernetes/connectedClusters/podtemplates/delete | 删除 podtemplates |
> | Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read | 读取 poddisruptionbudgets |
> | Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/write | 写入 poddisruptionbudgets |
> | Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/delete | 删除 poddisruptionbudgets |
> | Microsoft.Kubernetes/connectedClusters/policy/podsecuritypolicies/read | 读取 podsecuritypolicies |
> | Microsoft.Kubernetes/connectedClusters/policy/podsecuritypolicies/write | 写入 podsecuritypolicies |
> | Microsoft.Kubernetes/connectedClusters/policy/podsecuritypolicies/delet | 删除 podsecuritypolicies |
> | Microsoft.Kubernetes/connectedClusters/policy/podsecuritypolicies/use/action | 对 podsecuritypolicies 执行操作 |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterrolebindings/read | 读取 clusterrolebindings |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterrolebindings/write | 写入 clusterrolebindings |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterrolebindings/delete | 删除 clusterrolebindings |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/read | 读取 clusterroles |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/write | 写入 clusterroles |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/delete | 删除 clusterroles |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/bind/action | 绑定 clusterroles |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/escalate/action | 上报 |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/read | 读取 rolebindings |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/write | 写入 rolebindings |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/delete | 删除 rolebindings |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/read | 读取 roles |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/write | 写入 roles |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/delete | 删除 roles |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/bind/action | 绑定 roles |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/escalate/action | 上报 roles |
> | Microsoft.Kubernetes/connectedClusters/readyz/read | 读取 readyz |
> | Microsoft.Kubernetes/connectedClusters/readyz/autoregister-completion/read | 读取 autoregister-completion |
> | Microsoft.Kubernetes/connectedClusters/readyz/etcd/read | 读取 etcd |
> | Microsoft.Kubernetes/connectedClusters/readyz/log/read | 读取 log |
> | Microsoft.Kubernetes/connectedClusters/readyz/ping/read | 读取 ping |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/apiservice-openapi-controller/read | 读取 apiservice-openapi-controller |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/apiservice-registration-controller/read | 读取 apiservice-registration-controller |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/apiservice-status-available-controller/read | 读取 apiservice-status-available-controller |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/bootstrap-controller/read | 读取 bootstrap-controller |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/ca-registration/read | 读取 ca-registration |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/crd-informer-synced/read | 读取 crd-informer-synced |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/generic-apiserver-start-informers/read | 读取 generic-apiserver-start-informers |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/kube-apiserver-autoregistration/read | 读取 kube-apiserver-autoregistration |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/rbac/bootstrap-roles/read | 读取 bootstrap-roles |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/scheduling/bootstrap-system-priority-classes/read | 读取 bootstrap-system-priority-classes |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/start-apiextensions-controllers/read | 读取 start-apiextensions-controllers |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/start-apiextensions-informers/read | 读取 start-apiextensions-informers |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/start-kube-aggregator-informers/read | 读取 start-kube-aggregator-informers |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/start-kube-apiserver-admission-initializer/read | 读取 start-kube-apiserver-admission-initializer |
> | Microsoft.Kubernetes/connectedClusters/readyz/shutdown/read | 读取 shutdown |
> | Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read | 读取 replicationcontrollers |
> | Microsoft.Kubernetes/connectedClusters/replicationcontrollers/write | 写入 replicationcontrollers |
> | Microsoft.Kubernetes/connectedClusters/replicationcontrollers/delete | 删除 replicationcontrollers |
> | Microsoft.Kubernetes/connectedClusters/resetMetrics/read | 读取 resetMetrics |
> | Microsoft.Kubernetes/connectedClusters/resourcequotas/read | 读取 resourcequotas |
> | Microsoft.Kubernetes/connectedClusters/resourcequotas/write | 写入 resourcequotas |
> | Microsoft.Kubernetes/connectedClusters/resourcequotas/delete | 删除 resourcequotas |
> | Microsoft.Kubernetes/connectedClusters/scheduling.k8s.io/priorityclasses/read | 读取 priorityclasses |
> | Microsoft.Kubernetes/connectedClusters/scheduling.k8s.io/priorityclasses/write | 写入 priorityclasses |
> | Microsoft.Kubernetes/connectedClusters/scheduling.k8s.io/priorityclasses/delete | 删除 priorityclasses |
> | Microsoft.Kubernetes/connectedClusters/secrets/read | 读取 secrets |
> | Microsoft.Kubernetes/connectedClusters/secrets/write | 写入 secrets |
> | Microsoft.Kubernetes/connectedClusters/secrets/delete | 删除 secrets |
> | Microsoft.Kubernetes/connectedClusters/serviceaccounts/read | 读取 serviceaccounts |
> | Microsoft.Kubernetes/connectedClusters/serviceaccounts/write | 写入 serviceaccounts |
> | Microsoft.Kubernetes/connectedClusters/serviceaccounts/delete | 删除 serviceaccounts |
> | Microsoft.Kubernetes/connectedClusters/serviceaccounts/impersonate/action | 模拟 serviceaccounts |
> | Microsoft.Kubernetes/connectedClusters/services/read | 读取 services |
> | Microsoft.Kubernetes/connectedClusters/services/write | 写入 services |
> | Microsoft.Kubernetes/connectedClusters/services/delete | 删除 services |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csidrivers/read | 读取 csidrivers |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csidrivers/write | 写入 csidrivers |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csidrivers/delete | 删除 csidrivers |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csinodes/read | 读取 csinodes |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csinodes/write | 写入 csinodes |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csinodes/delete | 删除 csinodes |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/storageclasses/read | 读取 storageclasses |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/storageclasses/write | 写入 storageclasses |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/storageclasses/delete | 删除 storageclasses |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/volumeattachments/read | 读取 volumeattachments |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/volumeattachments/write | 写入 volumeattachments |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/volumeattachments/delete | 删除 volumeattachments |
> | Microsoft.Kubernetes/connectedClusters/swagger-api/read | 读取 swagger-api |
> | Microsoft.Kubernetes/connectedClusters/swagger-ui/read | 读取 swagger-ui |
> | Microsoft.Kubernetes/connectedClusters/ui/read | 读取 ui |
> | Microsoft.Kubernetes/connectedClusters/users/impersonate/action | 模拟用户 |
> | Microsoft.Kubernetes/connectedClusters/version/read | 读取 version |

### <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

Azure 服务：[Azure Kubernetes 服务 (AKS)](/azure/aks/)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.KubernetesConfiguration/register/action | 注册 Microsoft.KubernetesConfiguration 资源提供程序订阅。 |
> | Microsoft.KubernetesConfiguration/unregister/action | 取消注册 Microsoft.KubernetesConfiguration 资源提供程序订阅。 |
> | Microsoft.KubernetesConfiguration/extensions/write | 创建或更新扩展资源。 |
> | Microsoft.KubernetesConfiguration/extensions/read | 获取扩展实例资源。 |
> | Microsoft.KubernetesConfiguration/extensions/delete | 删除扩展实例资源。 |
> | Microsoft.KubernetesConfiguration/extensions/operations/read | 获取异步操作状态。 |
> | Microsoft.KubernetesConfiguration/operations/read | 获取 Microsoft.KubernetesConfiguration 资源提供程序的可用操作。 |
> | Microsoft.KubernetesConfiguration/sourceControlConfigurations/write | 创建或更新源控件配置。 |
> | Microsoft.KubernetesConfiguration/sourceControlConfigurations/read | 获取源控制配置。 |
> | Microsoft.KubernetesConfiguration/sourceControlConfigurations/delete | 删除源控制配置。 |

### <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

Azure 服务：[Azure Lighthouse](../lighthouse/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ManagedServices/register/action | 注册到托管服务。 |
> | Microsoft.ManagedServices/unregister/action | 从托管服务取消注册。 |
> | Microsoft.ManagedServices/marketplaceRegistrationDefinitions/read | 检索托管服务注册定义的列表。 |
> | Microsoft.ManagedServices/operations/read | 检索托管服务操作的列表。 |
> | Microsoft.ManagedServices/operationStatuses/read | 读取资源的操作状态。 |
> | Microsoft.ManagedServices/registrationAssignments/read | 检索托管服务注册分配的列表。 |
> | Microsoft.ManagedServices/registrationAssignments/write | 添加或修改托管服务注册分配。 |
> | Microsoft.ManagedServices/registrationAssignments/delete | 删除托管服务注册分配。 |
> | Microsoft.ManagedServices/registrationDefinitions/read | 检索托管服务注册定义的列表。 |
> | Microsoft.ManagedServices/registrationDefinitions/write | 添加或修改托管服务注册定义。 |
> | Microsoft.ManagedServices/registrationDefinitions/delete | 删除托管服务注册定义。 |

### <a name="microsoftmanagement"></a>Microsoft.Management

Azure 服务：[管理组](../governance/management-groups/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Management/checkNameAvailability/action | 检查指定的管理组名称是否有效且唯一。 |
> | Microsoft.Management/getEntities/action | 列出已通过身份验证的用户的所有实体（管理组、订阅等）。 |
> | Microsoft.Management/register/action | 向 Microsoft.Management 注册指定的订阅 |
> | Microsoft.Management/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | Microsoft.Management/managementGroups/write | 创建或更新管理组。 |
> | Microsoft.Management/managementGroups/delete | 删除管理组。 |
> | Microsoft.Management/managementGroups/descendants/read | 获取管理组的所有后代（管理组、订阅）。 |
> | Microsoft.Management/managementGroups/settings/read | 列出现有的管理组层次结构设置。 |
> | Microsoft.Management/managementGroups/settings/write | 创建或更新管理组层次结构设置。 |
> | Microsoft.Management/managementGroups/settings/delete | 删除管理组层次结构设置。 |
> | Microsoft.Management/managementGroups/subscriptions/read | 列出给定管理组下的订阅。 |
> | Microsoft.Management/managementGroups/subscriptions/write | 将现有订阅与管理组关联。 |
> | Microsoft.Management/managementGroups/subscriptions/delete | 从管理组取消关联订阅。 |

### <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

Azure 服务：[Azure Policy](../governance/policy/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.PolicyInsights/register/action | 注册 Microsoft 策略见解资源提供程序，并启用对其执行的操作。 |
> | Microsoft.PolicyInsights/unregister/action | 注销 Microsoft 策略见解资源提供程序。 |
> | Microsoft.PolicyInsights/asyncOperationResults/read | 获取异步操作结果。 |
> | Microsoft.PolicyInsights/attestations/read | 获取符合性状态证明。 |
> | Microsoft.PolicyInsights/attestations/write | 创建或更新符合性状态证明。 |
> | Microsoft.PolicyInsights/attestations/delete | 删除符合性状态证明。 |
> | Microsoft.PolicyInsights/checkPolicyRestrictions/read | 详细了解策略将对资源强制实施的限制。 |
> | Microsoft.PolicyInsights/eventGridFilters/read | 获取用于跟踪要为哪些范围发布状态更改通知的事件网格筛选器。 |
> | Microsoft.PolicyInsights/eventGridFilters/write | 创建或更新事件网格筛选器。 |
> | Microsoft.PolicyInsights/eventGridFilters/delete | 删除事件网格筛选器。 |
> | Microsoft.PolicyInsights/operations/read | 获取 Microsoft.PolicyInsights 命名空间支持的操作 |
> | Microsoft.PolicyInsights/policyEvents/queryResults/action | 查询有关策略事件的信息。 |
> | Microsoft.PolicyInsights/policyEvents/queryResults/read | 查询有关策略事件的信息。 |
> | Microsoft.PolicyInsights/policyMetadata/read | 获取策略元数据资源。 |
> | Microsoft.PolicyInsights/policyStates/queryResults/action | 查询有关策略状态的信息。 |
> | Microsoft.PolicyInsights/policyStates/summarize/action | 查询有关策略最新状态的摘要信息。 |
> | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 为所选范围触发新的符合性评估。 |
> | Microsoft.PolicyInsights/policyStates/queryResults/read | 查询有关策略状态的信息。 |
> | Microsoft.PolicyInsights/policyStates/summarize/read | 查询有关策略最新状态的摘要信息。 |
> | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | 查询有关 DeployIfNotExists 策略所需的资源的信息。 |
> | Microsoft.PolicyInsights/remediations/read | 获取策略修正。 |
> | Microsoft.PolicyInsights/remediations/write | 创建或更新 Microsoft 策略修正。 |
> | Microsoft.PolicyInsights/remediations/delete | 删除策略修正。 |
> | Microsoft.PolicyInsights/remediations/cancel/action | 取消正在进行的 Microsoft 策略修正。 |
> | Microsoft.PolicyInsights/remediations/listDeployments/read | 列出策略修正所需的部署。 |
> | **DataAction** | **说明** |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | 参照数据策略检查给定组件的合规性状态。 |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | 记录资源组件策略事件。 |

### <a name="microsoftportal"></a>Microsoft.Portal

Azure 服务：[Azure 门户](../azure-portal/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Portal/register/action | 注册到门户 |
> | Microsoft.Portal/consoles/delete | 删除 Cloud Shell 实例。 |
> | Microsoft.Portal/consoles/write | 创建或更新 Cloud Shell 实例。 |
> | Microsoft.Portal/consoles/read | 读取 Cloud Shell 实例。 |
> | Microsoft.Portal/dashboards/read | 读取订阅的仪表板。 |
> | Microsoft.Portal/dashboards/write | 向订阅添加仪表板或修改仪表板。 |
> | Microsoft.Portal/dashboards/delete | 从订阅删除仪表板。 |
> | Microsoft.Portal/tenantConfigurations/read | 读取租户配置 |
> | Microsoft.Portal/tenantConfigurations/write | 添加或更新租户配置。 用户必须是租户管理员，才能执行此操作。 |
> | Microsoft.Portal/tenantConfigurations/delete | 删除租户配置。 用户必须是租户管理员，才能执行此操作。 |
> | Microsoft.Portal/usersettings/delete | 删除 Cloud Shell 用户设置。 |
> | Microsoft.Portal/usersettings/write | 创建或更新 Cloud Shell 用户设置。 |
> | Microsoft.Portal/usersettings/read | 读取 Cloud Shell 用户设置。 |

### <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

Azure 服务：[站点恢复](../site-recovery/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.RecoveryServices/register/action | 注册给定资源提供程序的订阅 |
> | Microsoft.RecoveryServices/Locations/backupCrossRegionRestore/action | 触发跨区域还原。 |
> | Microsoft.RecoveryServices/Locations/backupCrrJob/action | 获取恢复服务保管库的次要区域中的跨区域还原作业详细信息。 |
> | Microsoft.RecoveryServices/Locations/backupCrrJobs/action | 列出恢复服务保管库的次要区域中的跨区域还原作业。 |
> | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/Locations/backupStatus/action | 检查恢复服务保管库的备份状态 |
> | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | 验证功能 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/locations/checkNameAvailability/action | “检查资源名称性”是一个 API，用于检查资源名称是否可用 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/Locations/backupAadProperties/read | 获取用于在第三区域进行身份验证的 AAD 属性，以便进行跨区域还原。 |
> | Microsoft.RecoveryServices/Locations/backupCrrOperationResults/read | 返回恢复服务保管库的 CRR 操作结果。 |
> | Microsoft.RecoveryServices/Locations/backupCrrOperationsStatus/read | 返回恢复服务保管库的 CRR 操作状态。 |
> | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | 创建备份受保护项 |
> | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | 返回所有受保护项的列表。 |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 获取给定操作的操作状态 |
> | Microsoft.RecoveryServices/operations/read | 操作返回资源提供程序的操作列表 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 导出作业 |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | 返回恢复服务保管库的安全 PIN 信息。 |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 验证对受保护项的操作 |
> | Microsoft.RecoveryServices/Vaults/write | “创建保管库”操作创建“vault”类型的 Azure 资源 |
> | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/delete | “删除保管库”操作删除“vault”类型的指定 Azure 资源 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | 返回恢复服务保管库的配置。 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/write | 更新恢复服务保管库的配置。 |
> | Microsoft.RecoveryServices/Vaults/backupEncryptionConfigs/read | 获取备份资源加密配置。 |
> | Microsoft.RecoveryServices/Vaults/backupEncryptionConfigs/write | 更新备份资源加密配置 |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | 返回使用保管库注册的所有备份管理服务器。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | 刷新容器列表 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | 删除备份保护意向 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | 获取备份保护意向 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 创建备份保护意向 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 返回操作状态 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationsStatus/read | 返回操作状态 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | 获取所有可保护的容器 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | 删除已注册的容器 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | 在容器内进行工作负载的查询 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 返回所有已注册的容器 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | 创建已注册的容器 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | 获取容器中的所有项 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 获取对保护容器执行的操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationsStatus/read | 获取对保护容器执行的操作的状态。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 对受保护的项执行备份。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 删除受保护的项 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 返回受保护项的对象详细信息 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPointsRecommendedForMove/action | 获取建议移动到其他层级的恢复点 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 创建备份受保护项 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 获取对受保护项执行的操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 返回对受保护项执行的操作的状态。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | 获取跨区域还原所需的 AccessToken。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/move/action | 将恢复点移动到其他层级 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 预配受保护项的即时项恢复 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 获取受保护项的恢复点。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 还原受保护项的恢复点。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 吊销受保护项的即时项恢复 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | 取消作业 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | 返回所有作业对象 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 返回作业操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationsStatus/read | 返回作业操作的状态。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | 返回恢复服务保管库的备份操作结果。 |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | 返回恢复服务保管库的备份操作状态。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | 删除保护策略 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 返回所有保护策略 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 创建保护策略 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 获取策略操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | 获取策略操作的状态。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | 返回所有可保护项的列表。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 返回所有受保护项的列表。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 返回属于订阅的所有容器 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | 列出所有备份保护意向 |
> | Microsoft.RecoveryServices/Vaults/backupResourceGuardProxies/delete | “删除 ResourceGuard 代理”操作删除类型为“ResourceGuard 代理”的指定 Azure 资源 |
> | Microsoft.RecoveryServices/Vaults/backupResourceGuardProxies/read | 获取资源的 ResourceGuard 代理列表 |
> | Microsoft.RecoveryServices/Vaults/backupResourceGuardProxies/read | “获取 ResourceGuard 代理”操作获取表示类型为“ResourceGuard 代理”的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/backupResourceGuardProxies/unlockDelete/action | “解锁删除 ResourceGuard 代理”操作解锁下一删除关键操作 |
> | Microsoft.RecoveryServices/Vaults/backupResourceGuardProxies/write | “创建 ResourceGuard 代理”操作创建类型为“ResourceGuard 代理”的 Azure 资源 |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | 返回恢复服务保管库的存储配置。 |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | 更新恢复服务保管库的存储配置。 |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | 返回恢复服务的受保护项和受保护服务器的摘要。 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | “更新资源证书”操作更新资源/保管库凭据证书。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 解决警报。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | 获取恢复服务保管库通知配置。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | 配置到恢复服务保管库的电子邮件通知。 |
> | Microsoft.RecoveryServices/Vaults/privateEndpointConnectionProxies/delete | 等候几分钟时间，并重试操作。 如果该问题仍然存在，请联系 Microsoft 支持部门。 |
> | Microsoft.RecoveryServices/Vaults/privateEndpointConnectionProxies/read | 获取所有可保护的容器 |
> | Microsoft.RecoveryServices/Vaults/privateEndpointConnectionProxies/validate/action | 获取所有可保护的容器 |
> | Microsoft.RecoveryServices/Vaults/privateEndpointConnectionProxies/write | 获取所有可保护的容器 |
> | Microsoft.RecoveryServices/Vaults/privateEndpointConnectionProxies/operationsStatus/read | 获取所有可保护的容器 |
> | Microsoft.RecoveryServices/Vaults/privateEndpointConnections/delete | 删除专用终结点请求。 此调用由备份管理员执行。 |
> | Microsoft.RecoveryServices/Vaults/privateEndpointConnections/write | 批准或拒绝专用终结点请求。 此调用由备份管理员执行。 |
> | Microsoft.RecoveryServices/Vaults/privateEndpointConnections/operationsStatus/read | 返回专用终结点连接的操作状态。 |
> | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Azure 备份诊断 |
> | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Azure 备份诊断 |
> | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Azure 备份日志 |
> | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Azure 备份指标 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | “注册服务容器”操作可用于向恢复服务注册容器。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | “取消注册容器”操作可用于取消注册容器。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 读取任何警报设置 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | 创建或更新任何警报设置 |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | 读取任何事件 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 读取任何结构 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/write | 创建或更新任何结构 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | 删除结构 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 检查结构的一致性 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | 删除任何结构 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 续订 Fabric 的证书 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | 部署进程服务器映像 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 重新关联网关 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | 将结构迁移到 AAD |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/moveWebApp/action | 移动 WebApp |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/operationresults/read | 跟踪对资源“结构”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 读取任何逻辑网络 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 读取任何网络 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 读取任何网络映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | 创建或更新任何网络映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | 删除任何网络映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 读取任何保护容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 发现可保护项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 创建或更新任何保护容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 删除保护容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 交换保护容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/operationresults/read | 跟踪对资源“保护容器”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 读取任何迁移项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 创建或更新任何迁移项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 删除任何迁移项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/resync/action | 重新同步 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 迁移项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 测试迁移 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 测试迁移清理 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 读取任何迁移恢复点 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | 跟踪对资源“迁移项”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 读取任何可保护项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 读取任何受保护项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 创建或更新任何受保护的项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 删除任何受保护的项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 删除受保护的项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 计划内故障转移 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 故障转移 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 测试故障转移 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 测试故障转移清理 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 故障转移提交 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 重新保护受保护的项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 更新移动服务 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 修复复制 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 应用还原点 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | 提交反馈 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | 添加磁盘 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | 删除磁盘 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCancel/action | 故障转移取消 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateAppliance/action |  |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | 跟踪对资源“受保护的项”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 读取任何复制恢复点 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | 读取任何目标计算大小 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 读取任何保护容器映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 创建或更新任何保护容器映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 删除保护容器映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 删除任何保护容器映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | 跟踪对资源“保护容器映射”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 读取任何恢复服务提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | 创建或更新任何恢复服务提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | 删除恢复服务提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | 删除任何恢复服务提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 刷新提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | 跟踪对资源“恢复服务提供程序”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 读取任何存储分类 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 读取任何存储分类映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | 创建或更新任何存储分类映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | 删除任何存储分类映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/read | 跟踪对资源“存储分类映射”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 读取任何 vCenter |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | 创建或更新任何 vCenter |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | 删除任何 vCenter |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/operationresults/read | 跟踪对资源“vCenters”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | 读取任何作业 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | 取消作业 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | 重新启动作业 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | 恢复作业 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/operationresults/read | 跟踪对资源“作业”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 读取任何迁移项 |
> | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | 读取任何网络映射 |
> | Microsoft.RecoveryServices/vaults/replicationNetworks/read | 读取任何网络 |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | 读取任何保管库复制操作状态 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 读取任何策略 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/write | 创建或更新任何策略 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | 删除任何策略 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/operationresults/read | 跟踪对资源“策略”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 读取任何受保护项 |
> | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 读取任何保护容器映射 |
> | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 读取任何保护容器 |
> | Microsoft.RecoveryServices/vaults/replicationProtectionIntents/read | 读取任何内容  |
> | Microsoft.RecoveryServices/vaults/replicationProtectionIntents/write | 创建或更新任何  |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 读取任何恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 创建或更新任何恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 删除任何恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 计划内故障转移恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 故障转移恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 测试故障转移恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 测试故障转移清理恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 故障转移提交恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 重新保护恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCancel/action | 取消故障转移恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/operationresults/read | 跟踪对资源“恢复计划”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | 读取任何恢复服务提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | 读取任何存储分类映射 |
> | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | 读取任何存储分类 |
> | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | 读取任何内容  |
> | Microsoft.RecoveryServices/vaults/replicationSupportedRegionMappings/read | 读取任何内容  |
> | Microsoft.RecoveryServices/vaults/replicationUsages/read | 读取任何保管库复制使用情况 |
> | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | 读取任何保管库复制运行状况 |
> | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | 刷新保管库运行状况 |
> | Microsoft.RecoveryServices/vaults/replicationVaultHealth/operationresults/read | 跟踪对资源“保管库复制运行状况”执行的异步操作的结果 |
> | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | 读取任何内容  |
> | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | 创建或更新任何  |
> | Microsoft.RecoveryServices/vaults/replicationvCenters/read | 读取任何 vCenter |
> | Microsoft.RecoveryServices/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | Microsoft.RecoveryServices/vaults/usages/read | 读取任何保管库使用情况 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | “保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure 服务：[Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Resources/checkResourceName/action | 检查资源名称的有效性。 |
> | Microsoft.Resources/calculateTemplateHash/action | 计算所提供模板的哈希。 |
> | Microsoft.Resources/checkPolicyCompliance/read | 参照资源策略检查给定资源的符合性状态。 |
> | Microsoft.Resources/deployments/read | 获取或列出部署。 |
> | Microsoft.Resources/deployments/write | 创建或更新部署。 |
> | Microsoft.Resources/deployments/delete | 删除部署。 |
> | Microsoft.Resources/deployments/cancel/action | 取消部署。 |
> | Microsoft.Resources/deployments/validate/action | 验证部署。 |
> | Microsoft.Resources/deployments/whatIf/action | 预测模板部署更改。 |
> | Microsoft.Resources/deployments/exportTemplate/action | 导出部署的模板 |
> | Microsoft.Resources/deployments/operations/read | 获取或列出部署操作。 |
> | Microsoft.Resources/deployments/operationstatuses/read | 获取或列出部署操作状态。 |
> | Microsoft.Resources/deploymentScripts/read | 获取或列出部署脚本 |
> | Microsoft.Resources/deploymentScripts/write | 创建或更新部署脚本 |
> | Microsoft.Resources/deploymentScripts/delete | 删除部署脚本 |
> | Microsoft.Resources/deploymentScripts/logs/read | 获取或列出部署脚本日志 |
> | Microsoft.Resources/links/read | 获取或列出资源链接。 |
> | Microsoft.Resources/links/write | 创建或更新资源链接。 |
> | Microsoft.Resources/links/delete | 删除资源链接。 |
> | Microsoft.Resources/marketplace/purchase/action | 从市场购买资源。 |
> | Microsoft.Resources/providers/read | 获取提供程序的列表。 |
> | Microsoft.Resources/resources/read | 基于筛选器获取资源的列表。 |
> | Microsoft.Resources/subscriptions/read | 获取订阅的列表。 |
> | Microsoft.Resources/subscriptions/locations/read | 获取支持的位置列表。 |
> | Microsoft.Resources/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | Microsoft.Resources/subscriptions/providers/read | 获取或列出资源提供程序。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Resources/subscriptions/resourceGroups/write | 创建或更新资源组。 |
> | Microsoft.Resources/subscriptions/resourceGroups/delete | 删除资源组及其所有资源。 |
> | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | 将资源从一个资源组移到另一个资源组。 |
> | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | 验证是否已将资源从一个资源组移到另一个资源组。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | 获取或列出部署。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | 创建或更新部署。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | 获取或列出部署操作。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | 获取或列出部署操作状态。 |
> | Microsoft.Resources/subscriptions/resourcegroups/resources/read | 获取资源组的资源。 |
> | Microsoft.Resources/subscriptions/resources/read | 获取订阅的资源。 |
> | Microsoft.Resources/subscriptions/tagNames/read | 获取或列出订阅标记。 |
> | Microsoft.Resources/subscriptions/tagNames/write | 添加订阅标记。 |
> | Microsoft.Resources/subscriptions/tagNames/delete | 删除订阅标记。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/read | 获取或列出订阅标记值。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/write | 添加订阅标记值。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | 删除订阅标记值。 |
> | Microsoft.Resources/tags/read | 获取资源上的所有标记。 |
> | Microsoft.Resources/tags/write | 更新资源上的标记，方法是：替换现有标记或将其与新的标记组合并，或者删除现有标记。 |
> | Microsoft.Resources/tags/delete | 删除资源上的所有标记。 |
> | Microsoft.Resources/templateSpecs/read | 获取或列出模板规格 |
> | Microsoft.Resources/templateSpecs/write | 创建或更新模板规格 |
> | Microsoft.Resources/templateSpecs/delete | 删除模板规格 |
> | Microsoft.Resources/templateSpecs/versions/read | 获取或列出模板规格 |
> | Microsoft.Resources/templateSpecs/versions/write | 创建或更新模板规格版本 |
> | Microsoft.Resources/templateSpecs/versions/delete | 删除模板规格版本 |
> | Microsoft.Resources/tenants/read | 获取租户的列表。 |

### <a name="microsoftscheduler"></a>Microsoft.Scheduler

Azure 服务：[计划程序](../scheduler/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Scheduler/jobcollections/read | 获取作业集合 |
> | Microsoft.Scheduler/jobcollections/write | 创建或更新作业集合。 |
> | Microsoft.Scheduler/jobcollections/delete | 删除作业集合。 |
> | Microsoft.Scheduler/jobcollections/enable/action | 启用作业集合。 |
> | Microsoft.Scheduler/jobcollections/disable/action | 禁用作业集合。 |
> | Microsoft.Scheduler/jobcollections/jobs/read | 获取作业。 |
> | Microsoft.Scheduler/jobcollections/jobs/write | 创建或更新作业。 |
> | Microsoft.Scheduler/jobcollections/jobs/delete | 删除作业。 |
> | Microsoft.Scheduler/jobcollections/jobs/run/action | 运行作业。 |
> | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | 基于计划程序作业生成逻辑应用定义。 |
> | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | 获取作业历史记录。 |

### <a name="microsoftsolutions"></a>Microsoft.Solutions

Azure 服务：[Azure 托管应用程序](../azure-resource-manager/managed-applications/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Solutions/register/action | 注册到解决方案。 |
> | Microsoft.Solutions/unregister/action | 从解决方案注销。 |
> | Microsoft.Solutions/applicationDefinitions/read | 检索应用程序定义列表。 |
> | Microsoft.Solutions/applicationDefinitions/write | 添加或修改应用程序定义。 |
> | Microsoft.Solutions/applicationDefinitions/delete | 删除应用程序定义。 |
> | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | 列出应用程序定义的应用程序项目。 |
> | Microsoft.Solutions/applications/read | 检索应用程序列表。 |
> | Microsoft.Solutions/applications/write | 创建应用程序。 |
> | Microsoft.Solutions/applications/delete | 删除应用程序。 |
> | Microsoft.Solutions/applications/refreshPermissions/action | 刷新应用程序权限。 |
> | Microsoft.Solutions/applications/updateAccess/action | 更新应用程序访问权限。 |
> | Microsoft.Solutions/applications/applicationArtifacts/read | 列出应用程序项目。 |
> | Microsoft.Solutions/jitRequests/read | 检索 JitRequest 列表 |
> | Microsoft.Solutions/jitRequests/write | 创建 JitRequest |
> | Microsoft.Solutions/jitRequests/delete | 删除 JitRequest |
> | Microsoft.Solutions/locations/operationStatuses/read | 读取资源的操作状态。 |
> | Microsoft.Solutions/operations/read | 获取操作列表。 |

### <a name="microsoftsubscription"></a>Microsoft.Subscription

Azure 服务：核心

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Subscription/cancel/action | 取消订阅 |
> | Microsoft.Subscription/rename/action | 重命名订阅 |
> | Microsoft.Subscription/enable/action | 重新激活订阅 |
> | Microsoft.Subscription/CreateSubscription/action | 创建 Azure 订阅 |
> | Microsoft.Subscription/register/action | 使用 Microsoft.Subscription 资源提供程序注册订阅 |
> | Microsoft.Subscription/updateTenant/action | 更新订阅的租户 |
> | Microsoft.Subscription/aliases/write | 创建订阅别名 |
> | Microsoft.Subscription/aliases/read | 获取订阅别名 |
> | Microsoft.Subscription/aliases/delete | 删除订阅别名 |
> | Microsoft.Subscription/Subscriptions/write | 创建 Azure 订阅 |

## <a name="intune"></a>Intune

### <a name="microsoftintune"></a>Microsoft.Intune

Azure 服务：Microsoft Monitoring Insights

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.Intune/diagnosticsettings/write | 写入诊断设置 |
> | Microsoft.Intune/diagnosticsettings/read | 读取诊断设置 |
> | Microsoft.Intune/diagnosticsettings/delete | 删除诊断设置 |
> | Microsoft.Intune/diagnosticsettingscategories/read | 读取诊断设置类别 |

## <a name="other"></a>其他

### <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

Azure 服务：[Windows 虚拟桌面](../virtual-desktop/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DesktopVirtualization/register/action | 注册订阅 |
> | Microsoft.DesktopVirtualization/applicationgroups/read | 读取 applicationgroups |
> | Microsoft.DesktopVirtualization/applicationgroups/write | 读取 applicationgroups |
> | Microsoft.DesktopVirtualization/applicationgroups/delete | 删除 applicationgroups |
> | Microsoft.DesktopVirtualization/applicationgroups/applications/read | 读取 applicationgroups/applications |
> | Microsoft.DesktopVirtualization/applicationgroups/applications/write | 写入 applicationgroups/applications |
> | Microsoft.DesktopVirtualization/applicationgroups/applications/delete | 删除 applicationgroups/applications |
> | Microsoft.DesktopVirtualization/applicationgroups/desktops/read | 读取 applicationgroups/desktops |
> | Microsoft.DesktopVirtualization/applicationgroups/desktops/write | 写入 applicationgroups/desktops |
> | Microsoft.DesktopVirtualization/applicationgroups/desktops/delete | 删除 applicationgroups/desktops |
> | Microsoft.DesktopVirtualization/applicationgroups/externaluserassignments/read |  |
> | Microsoft.DesktopVirtualization/applicationgroups/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置 |
> | Microsoft.DesktopVirtualization/applicationgroups/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新诊断设置 |
> | Microsoft.DesktopVirtualization/applicationgroups/providers/Microsoft.Insights/logDefinitions/read | 获取可用日志 |
> | Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/read | 读取“开始”菜单项 |
> | Microsoft.DesktopVirtualization/hostpools/read | 读取 hostpools |
> | Microsoft.DesktopVirtualization/hostpools/write | 写入 hostpools |
> | Microsoft.DesktopVirtualization/hostpools/delete | 删除 hostpools |
> | Microsoft.DesktopVirtualization/hostpools/retrieveRegistrationToken/action | 检索主机池的注册令牌 |
> | Microsoft.DesktopVirtualization/hostpools/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置 |
> | Microsoft.DesktopVirtualization/hostpools/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新诊断设置 |
> | Microsoft.DesktopVirtualization/hostpools/providers/Microsoft.Insights/logDefinitions/read | 获取可用日志 |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/read | 读取 hostpools/sessionhosts |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/write | 写入 hostpools/sessionhosts |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/delete | 删除 hostpools/sessionhosts |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read | 读取 hostpools/sessionhosts/usersessions |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/write | 写入 hostpools/sessionhosts/usersessions |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete | 删除 hostpools/sessionhosts/usersessions |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/disconnect/action | 从会话主机断开用户会话的连接 |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action | 向用户会话发送消息 |
> | Microsoft.DesktopVirtualization/workspaces/read | 读取工作区 |
> | Microsoft.DesktopVirtualization/workspaces/write | 写入工作区 |
> | Microsoft.DesktopVirtualization/workspaces/delete | 删除工作区 |
> | Microsoft.DesktopVirtualization/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置 |
> | Microsoft.DesktopVirtualization/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新诊断设置 |
> | Microsoft.DesktopVirtualization/workspaces/providers/Microsoft.Insights/logDefinitions/read | 获取可用日志 |
> | **DataAction** | **说明** |
> | Microsoft.DesktopVirtualization/applicationgroups/useapplications/action | 使用 ApplicationGroup |

### <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

Azure 服务：[Azure 数字孪生](../digital-twins/index.yml)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.DigitalTwins/register/action | 注册数字孪生资源提供程序的订阅，并允许创建数字孪生实例。 |
> | Microsoft.DigitalTwins/unregister/action | 取消注册数字孪生资源提供程序的订阅 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/read | 读取任何 Microsoft.DigitalTwins/digitalTwinsInstances 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/write | 创建或更新任何 Microsoft.DigitalTwins/digitalTwinsInstances 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/delete | 删除任何 Microsoft.DigitalTwins/digitalTwinsInstances 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/diagnosticSettings/read | 获取资源的诊断设置 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/diagnosticSettings/write | 设置资源的诊断设置 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/endpoints/delete | 删除数字孪生资源的任何终结点 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/endpoints/read | 读取数字孪生资源的任何终结点 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/endpoints/write | 创建或更新数字孪生资源的任何终结点 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/logDefinitions/read | 获取资源的 Azure Monitor 的日志设置 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/metricDefinitions/read | 获取资源的 Azure Monitor 的指标设置 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/operationsResults/read | 读取任何操作结果 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/validate/action | 验证 PrivateEndpointConnectionProxies 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/read | 读取 PrivateEndpointConnectionProxies 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/write | 写入 PrivateEndpointConnectionProxies 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/delete | 删除 PrivateEndpointConnectionProxies 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/operationResults/read | 获取对专用终结点连接代理执行的异步操作的结果 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnections/read | 读取 PrivateEndpointConnection 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnections/write | 写入 PrivateEndpointConnection 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnections/delete | 删除 PrivateEndpointConnection 资源 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnections/operationResults/read | 获取对专用终结点连接执行的异步操作的结果 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateLinkResources/read | 读取数字孪生的 PrivateLinkResources |
> | Microsoft.DigitalTwins/locations/checkNameAvailability/action | 检查数字孪生资源提供程序中资源的名称可用性 |
> | Microsoft.DigitalTwins/locations/operationsResults/read | 读取任何操作结果 |
> | Microsoft.DigitalTwins/operations/read | 读取所有操作 |
> | **DataAction** | **说明** |
> | Microsoft.DigitalTwins/query/action | 查询任何数字孪生图 |
> | Microsoft.DigitalTwins/digitaltwins/read | 读取任何数字孪生 |
> | Microsoft.DigitalTwins/digitaltwins/write | 创建或更新任何数字孪生 |
> | Microsoft.DigitalTwins/digitaltwins/delete | 删除任何数字孪生 |
> | Microsoft.DigitalTwins/digitaltwins/commands/action | 对数字孪生调用任何命令 |
> | Microsoft.DigitalTwins/digitaltwins/relationships/read | 读取任何数字孪生关系 |
> | Microsoft.DigitalTwins/digitaltwins/relationships/write | 创建或更新任何数字孪生关系 |
> | Microsoft.DigitalTwins/digitaltwins/relationships/delete | 删除任何数字孪生关系 |
> | Microsoft.DigitalTwins/eventroutes/read | 读取任何事件路由 |
> | Microsoft.DigitalTwins/eventroutes/delete | 删除任何事件路由 |
> | Microsoft.DigitalTwins/eventroutes/write | 读取或更新任何事件路由 |
> | Microsoft.DigitalTwins/models/read | 读取任何模型 |
> | Microsoft.DigitalTwins/models/write | 创建或更新任何模型 |
> | Microsoft.DigitalTwins/models/delete | 删除任何模型 |

### <a name="microsoftserviceshub"></a>Microsoft.ServicesHub

Azure 服务：[服务中心](/services-hub/)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | Microsoft.ServicesHub/connectors/write | 创建或更新服务中心连接器 |
> | Microsoft.ServicesHub/connectors/read | 查看或列出服务中心连接器 |
> | Microsoft.ServicesHub/connectors/delete | 删除服务中心连接器 |
> | Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action | 列出给定服务中心工作区的评估权利 |
> | Microsoft.ServicesHub/supportOfferingEntitlement/read | 查看给定服务中心工作区的支持产品/服务权利 |
> | Microsoft.ServicesHub/workspaces/read | 列出给定用户的服务中心工作区 |

## <a name="next-steps"></a>后续步骤

- [将资源提供程序与服务匹配](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 内置角色](built-in-roles.md)
- [云采用框架：Azure 中的资源访问管理](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
