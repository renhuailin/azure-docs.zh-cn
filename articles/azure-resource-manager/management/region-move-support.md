---
title: 跨区域移动 Azure 资源的支持
description: 列出可跨 Azure 区域移动的 Azure 资源类型
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 49c5828e02bf96a536ff14f6b84e81f7adbe3090
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806907"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>跨区域移动 Azure 资源的支持

本文确认了是否支持将某种 Azure 资源类型移到另一个 Azure 区域。 

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | domainservices | 否 | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | diagnosticsettings | 否 |
> | diagnosticsettingscategories | 否 |
> | privatelinkforazuread | 否 |
> | tenants |  否 |

## <a name="microsoftaddons"></a>微软.加载项

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | supportproviders | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | aadsupportcases | 否 |
> | addsservices | 否 | 
> | 代理 | 否 | 
> | anonymousapiusers | 否 |
> | 配置 | 否 | 
> | 日志 | 否 | 
> | 报表 | 否 | 
> | servicehealthmetrics | 否 | 
> | services | 否 | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | 配置 | 否 | 
> | generaterecommendations | 否 |
> | metadata | 否 |
> | 建议 | 否 |
> | 禁止显示 | 否 | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | actionrules | 否 | 
> | alerts | 否 | 
> | alertslist | 否 | 
> | alertsmetadata | 否 | 
> | alertssummary | 否 | 
> | alertssummarylist | 否 | 
> | smartdetectoralertrules | 否 | 
> | smartgroups | 否 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | servers | 否 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | reportfeedback | 否 |
> | 服务 |  是（使用模板） <br/><br/> [跨区域移动 API 管理](../../api-management/api-management-howto-migrate.md)。 | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | configurationstores | 否 | 
> | configurationstores / eventgridfilters | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | spring | 否 | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | apiapps | 是（使用模板）<br/><br/> [将应用服务应用移动到其他区域](../../app-service/manage-move-across-regions.md) | 
> | appidentities | 否 | 
> | gateways | 否 | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | attestationproviders | 否 | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | classicadministrators | 否 | 
> | dataaliases | 否 | 
> | denyassignments | 否 | 
> | elevateaccess | 否 | 
> | findorphanroleassignments | 否 | 
> | 锁定 | 否 | 
> | 权限 | 否 | 
> | policyassignments | 否 | 
> | policydefinitions | 否 | 
> | policysetdefinitions | 否 | 
> | privatelinkassociations | 否 | 
> | resourcemanagementprivatelinks | 否 | 
> | roleassignments | 否 | 
> | roleassignmentsusagemetrics | 否 | 
> | roledefinitions | 否 | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | automationaccounts | 是（使用模板） <br/><br/> [使用异地复制](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | 否 | 
> | automationaccounts/runbooks | 否 | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 订阅 |
> | ------------- | ----------- | 
> | privateclouds | 否 | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | b2cdirectories | 否 | 
> | b2ctenants | 否 | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | datacontrollers | 否 | 
> | hybriddatamanagers | 否 | 
> | postgresinstances | 否 | 
> | sqlinstances | 否 | 
> | sqlmanagedinstances | 否 |
> | sqlserverinstances | 否 | 
> | sqlserverregistrations | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | cloudmanifestfiles | 否 |
> | registrations | 否 | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | clusters | 否 | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch 帐户不能直接从一个区域移到另一个区域，但你可以使用模板来导出模板，对其进行修改，然后将模板部署到新区域。 <br/><br/> 了解如何[跨区域移动 Batch 帐户](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | billingaccounts | 否 | 
> | billingperiods | 否 | 
> | billingpermissions | 否 | 
> | billingproperty | 否 | 
> | billingroleassignments | 否 | 
> | billingroledefinitions | 否 | 
> | departments | 否 | 
> | enrollmentaccounts | 否 | 
> | invoices | 否 | 
> | transfers | 否 | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | mapapis | 否 | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | biztalk | 否 | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | blockchainmembers | 否 <br/><br/> 区块链网络不能具有不同区域中的节点。 
> | cordamembers | 否 |
> | watchers | 否 | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | tokenservices | 否 |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | blueprintassignments | 否 | 
> | blueprints | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | botservices | 否 | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | redis | 否 | 
> | redisenterprise | 否 | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | appliedreservations | 否 | 
> | calculateexchange | 否 | 
> | calculateprice | 否 | 
> | calculatepurchaseprice | 否 | 
> | catalogs | 否 | 
> | commercialreservationorders | 否 | 
> | 交易所 | 否 |
> | reservationorders | 否 | 
> | reservations | 否 | 
> | resources | 否 | 
> | validatereservationorder | 否 | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | 否 |
> | edgenodes | 否
> | 配置文件 | 否 | 
> | profiles/endpoints | 否 | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | certificateorders | 否 | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | capabilities | 否 | 
> | domainnames | 是 | 否 |
> | quotas | 否 | 
> | resourcetypes | 否 |
> | validatesubscriptionmoveavailability | 否 | 
> | virtualmachines | 否 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | 否 | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | capabilities | 否 | 
> | expressroutecrossconnections | 否 | 
> | expressroutecrossconnections / peerings | 否 | 
> | gatewaysupporteddevices | 否 | 
> | networksecuritygroups | 否 |
> | quotas | 否 |
> | reservedips | 否 | 
> | virtualnetworks | 否 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | disks | 否 | 
> | images | 否 | 
> | osimages | 否 | 
> | osplatformimages | 否 | 
> | publicimages | 否 | 
> | quotas | 否 | 
> | storageaccounts | 是 |  
> | vmimages | 否 |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | 操作 | 否 | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 
> | 认知搜索 | 支持手动步骤。<br/><br/> 了解如何 [将 Azure 认知搜索服务转移到另一个区域](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | ratecard | 否 | 
> | usageaggregates | 否 | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | availabilitysets | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动可用性集。 | 
> | diskaccesses | 否 |
> | diskencryptionsets | 否 | 
> | disks | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动 azure vm 和相关磁盘。 | 
> | galleries | 否 | 
> | galleries/images | 否 | 
> | galleries/images/versions | 否 | 
> | hostgroups | 否 | 
> | hostgroups/hosts | 否 | 
> | images | 否 | 
> | proximityplacementgroups | 否 | 
> | restorepointcollections | 否 | 
> | sharedvmimages | 否 | 
> | sharedvmimages/versions | 否 | 
> | snapshots | 否 | 
> | sshpublickeys | 否 |
> | virtualmachines | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动 azure vm。 | 
> | virtualmachines/extensions | 否 | 
> | virtualmachinescalesets | 否 | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | aggregatedcost | 否 | 
> | balances | 否 | 
> | budgets | 否 | 
> | charges | 否 | 
> | costtags | 否 | 
> | credits | 否 | 
> | events | 否 | 
> | forecasts | 否 | 
> | lots | 否 | 
> | marketplaces | 否 | 
> | pricesheets | 否 | 
> | products | 否 | 
> | reservationdetails | 否 | 
> | reservationrecommendationdetails | 否 | 
> | reservationrecommendations | 否 | 
> | reservationsummaries | 否 | 
> | reservationtransactions | 否 | 
> | 标记 | 否 | 
> | tenants | 否 | 
> | terms | 否 | 
> | usagedetails | 否 | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | containergroups | 否 | 
> | serviceassociationlinks | 否 |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | registries | 否 |  
> | registries/agentpools | 否 | 
> | registries/buildtasks | 否 |  
> | registries/replications | 否 | 
> | registries/tasks | 否 |  
> | registries/webhooks | 否 | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | containerservices | 否 |
> | managedclusters | 否 | 
> | openshiftmanagedclusters | 否 | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applications | 否 | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | alerts | 否 | 
> | billingaccounts | 否 | 
> | budgets | 否 | 
> | cloudconnectors | 否 | 
> | 连接器 | 否 | 
> | departments | 否 | 
> | dimensions | 否 | 
> | enrollmentaccounts | 否 | 
> | exports | 否 | 
> | externalbillingaccounts | 否 | 
> | 预测 | 否 | 
> | query | 否 | 
> | register | 否 | 
> | reportconfigs | 否 | 
> | 报表 | 否 | 
> | 设置 | 否 | 
> | showbackrules | 否 | 
> | 视图 | 否 | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | hubs | 否 |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | 请求 | 否 | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | associations | 否 |
> | resourceproviders | 否 | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | jobs | 否 | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | availableskus | 否 |
> | databoxedgedevices | 否 | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | workspaces | 否 | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | catalogs | 否 | 
> | datacatalogs | 否 | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | connectionmanagers | 否 | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | packages | 否 | 
> | plans | 否 | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | datafactories | 否 | 
> | factories | 否 |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | datalakeaccounts | 否 | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | services | 否 | 
> | services/projects | 否 | 
> | slots | 否 | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | ---------- |
> | backupvaults | 否 | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | servers | 可以使用跨区域只读副本移动现有服务器。 [了解详细信息](../../postgresql/howto-move-regions-portal.md)。<br/><br/> 如果为服务预配了异地冗余备份存储，则可以使用异地还原在其他区域中进行还原。 [了解详细信息](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)。

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | servers | 可以使用跨区域只读副本移动现有服务器。 [了解详细信息](../../mysql/howto-move-regions-portal.md)。

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | servergroups | 否 | 
> | servers | 可以使用跨区域只读副本移动现有服务器。 [了解详细信息](../../postgresql/howto-move-regions-portal.md)。
> | serversv2 | 否 | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | artifactsources | 否 | 
> | rollouts | 否 |  
> | servicetopologies | 否 | 
> | servicetopologies / services | 否 |  
> | servicetopologies / services / serviceunits | 否 | 
> | steps | 否 | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | applicationgroups | 否 | 
> | workspaces | 否 | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | elasticpools | 否。 资源未公开。
> | elasticpools/iothubtenants | 否。 资源未公开。
> | iothubs | 是的。 [了解详细信息](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | 否 | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | 控制器 | 否 | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | 控制器 | 否 | 
> | AKS 群集 | 否<br/><br/> [了解](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) 有关移动到另一个区域的详细信息。

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | labcenters | 否 | 
> | labs | 否 | 
> | labs / environments | 否 |  
> | labs / servicerunners | 否 | 
> | labs / virtualmachines | 否 |  
> | schedules | 否 |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | 是，通过重新创建新区域中的资源。 [了解详细信息](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | databaseaccounts | 否 | 
> | databaseaccounts | 否 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | domains | 否 | 
> | generatessorequest | 否 | 
> | topleveldomains | 否 | 
> | validatedomainregistrationinformation | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | services | 否 |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | domains | 否 | 
> | eventsubscriptions | 否 |
> | extensiontopics | 否 | 
> | partnernamespaces | 否 | 
> | partnerregistrations | 否 | 
> | partnertopics | 否 | 
> | systemtopics | 否 | 
> | topics | 否 | 
> | topictypes | 否 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters | 否 |  
> | namespaces | 是（带模板）<br/><br/> [将事件中心命名空间移到另一个区域](../../event-hubs/move-across-regions.md) | 
> | sku | 否 |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | experimentworkspaces | 否 | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | namespaces | 否 | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | featureproviders | 否 | 
> | features | 否 | 
> | providers | 否 | 
> | subscriptionfeatureregistrations | 否 | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | automanagedaccounts | 否 | 
> | automanagedvmconfigurationprofiles | 否 | 
> | guestconfigurationassignments | 否 | 
> | software | 否 | 
> | softwareupdateprofile | 否 | 
> | softwareupdates | 否 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | hanainstances | 否 | 
> | sapmonitors | 否 |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | dedicatedhsms | 否 | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters | 否 | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | services | 否 |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | machines | 否 | 
> | machines / extensions | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | datamanagers |  否 | 

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | devices | 否 | 
> | vnfs | 否 | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | components | 否 | 
> | networkscopes | 否 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | jobs |  否 | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否。 [了解详细信息](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region)。
> | actiongroups |  否 | 
> | activitylogalerts | 否 | 
> | alertrules |  否 | 
> | autoscalesettings |  否 | 
> | baseline | 否 |
> | components |  否 |  
> | datacollectionrules | 否 | 
> | diagnosticsettings | 否 | 
> | diagnosticsettingscategories | 否 | 
> | eventcategories | 否 | 
> | eventtypes | 否 | 
> | extendeddiagnosticsettings | 否 | |
> | guestdiagnosticsettings | 否 | 
> | listmigrationdate | 否 | 
> | logdefinitions | 否 | 
> | logprofiles | 否 | 
> | 日志 | 否 | 否 |
> | metricalerts | 否 | 
> | metricbaselines | 否 | 
> | metricbatch | 否 | 
> | metricdefinitions | 否 | 
> | metricnamespaces | 否 | 
> | 指标 | 否 | 
> | migratealertrules | 否 |
> | migratetonewpricingmodel | 否 | 
> | myworkbooks | 否 |
> | notificationgroups | 否 | 
> | privatelinkscopes | 否 |
> | rollbacktolegacypricingmodel | 否 |
> | scheduledqueryrules |  否 | 
> | 拓扑 | 否 |
> | 事务 | 否 |
> | vminsightsonboardingstatuses | 否 |
> | webtests |  否 | 
> | webtests/gettestresultfile | 否 |
> | workbooks |  否 |  
> | workbooktemplates | 否 |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | apptemplates | 否 | 
> | iotapps | 否 | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> |  iothub |  是 (克隆中心)  <br/><br/> [将 IoT 中心克隆到另一个区域](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 |
> | ------------- | ----------- | 
> | graph | 否 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | deletedvaults | 否 |
> | hsmpools | 否 | 
> | managedhsms | 否 |
> | vaults |  否 | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | connectedclusters | 否 | 
> | registeredsubscriptions | 否 | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | 否 | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters |  否 |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | labaccounts | 否 | 
> | users | 否 | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 不是，它是一种全球服务。

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | hostingenvironments | 否 | 
> | integrationaccounts |  否 |  
> | integrationserviceenvironments | 否 | 
> | integrationserviceenvironments/managedapis | 否 |
> | isolatedenvironments | 否 | 
> | workflows |  否 |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | commitmentplans |  否 | 
> | webservices |  否 | 
> | workspaces |  否 | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | operationalizationclusters |  否 | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 
> | teamaccounts | 否 | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | workspaces | 否 | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | configurationassignments | 是的。 [了解详细信息](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | 是的。 [了解详细信息](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | 否 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | identities | 否 | 
> | userassignedidentities | 否 | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | managednetworks | 否 | 
> | managednetworks / managednetworkgroups | 否 |
> | managednetworks / managednetworkpeeringpolicies | 否 | 
> | 通知 | 否 | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | 否 | 
> | registrationassignments | 否 |
> | registrationdefinitions | 否 | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | getentities | 否 | 
> | managementgroups | 否 | 
> | managementgroups / settings | 否 | 
> | resources | 否 | 
> | starttenantbackfill | 否 | 
> | tenantbackfillstatus | 否 | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts |  不，Azure Maps 是地理空间服务。 
> | accounts / privateatlases | 否

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | offers | 否 | 
> | offertypes | 否 | 
> | privategalleryitems | 否 | 
> | privatestoreclient | 否 | 
> | privatestores | 否 | 
> | products | 否 | 
> | publishers | 否 | 
> | register | 否 | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | classicdevservices | 否 | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | 协议 | 否 | 
> | offertypes | 否 | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | mediaservices |  否 | 
> | mediaservices/liveevents |  否 | 
> | mediaservices/streamingendpoints |  否 | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | appclusters | 否 | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | assessmentprojects | 否 | 
> | migrateprojects | 否 | 
> | movecollections | 否
> | projects | 否 | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | 否 | 
> | objectunderstandingaccounts | 否 | 
> | remoterenderingaccounts | 否 | 
> | spatialanchorsaccounts | 否 | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | netappaccounts | 否 | 
> | netappaccounts / capacitypools | 否 | 
> | netappaccounts / capacitypools / volumes | 否 | 
> | netappaccounts / capacitypools / volumes / mounttargets | 否 | 
> | netappaccounts / capacitypools / volumes / snapshots | 否 | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applicationgateways | 否 |
> | applicationgatewaywebapplicationfirewallpolicies | 否 | 
> | applicationsecuritygroups |  否 |  
> | azurefirewalls |  否 |  
> | bastionhosts | 否 | 
> | bgpservicecommunities | 否 |
> | connections |  否 | 
> | ddoscustompolicies |  否 | 
> | ddosprotectionplans | 否 | 
> | dnszones |  否 | 
> | expressroutecircuits | 否 | 
> | expressroutegateways | 否 | 
> | expressrouteserviceproviders | 否 | 
> | firewallpolicies | 否 |
> | frontdoors | 否 | 
> | ipallocations | 否 |
> | ipgroups | 否 |
> | loadbalancers | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 来移动内部和外部负载均衡器。 |
> | localnetworkgateways |  否 | 
> | natgateways |  否 | 
> | networkexperimentprofiles | 否 |
> | networkintentpolicies |  否 | 
> | networkinterfaces | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动 nic。 | 
> | networkprofiles | 否 | 
> | networksecuritygroups | 是 <br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) (NGSs) 移动网络安全组。 | 
> | networkwatchers |  否 |  
> | networkwatchers/connectionmonitors |  否 | 
> | networkwatchers/flowlogs |  否 | 
> | networkwatchers/pingmeshes |  否 | 
> | p2svpngateways | 否 | 
> | privatednszones |  否 |  
> | privatednszones/virtualnetworklinks | 否 |> | privatednszonesinternal | 否 |
> | privateendpointredirectmaps | 否 |
> | privateendpoints | 否 | 
> | privatelinkservices | 否 | 
> | publicipaddresses | 是<br/><br/> 使用 [Azure 资源移动器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移动公共 IP 地址。 |
> | publicipprefixes | 否 | 
> | routefilters | 否 | 
> | routetables |  否 | 
> | securitypartnerproviders | 否 |
> | serviceendpointpolicies |  否 | 
> | trafficmanagergeographichierarchies | 否 | 
> | trafficmanagerprofiles |  否 | 
> | trafficmanagerusermetricskeys | 否 |
> | virtualhubs | 否 | 
> | virtualnetworkgateways |  否 |  
> | virtualnetworks |  否 | 
> | virtualnetworktaps | 否 | 
> | virtualwans | 否 | 
> | vpngateways（虚拟 WAN） | 否 | 
> | vpnsites（虚拟 WAN） | 否 | 
> | vpnsites（虚拟 WAN） | 否 |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | namespaces |  否 | 
> | namespaces/notificationhubs |  否 |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | osnamespaces | 否 | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | hypervsites | 否 | 
> | importsites | 否 | 
> | serversites | 否 | 
> | vmwaresites | 否 | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters | 否 | 
> | deletedworkspaces | 否 | 
> | linktargets | 否 | 
> | storageinsightconfigs | 否 |
> | workspaces | 否 |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations |  否 | 
> | solutions | 否 |
> | 视图 |  否 | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | legacypeerings | 否 | 
> | peerasns | 否 | 
> | peeringlocations | 否 | 
> | peerings | 否 | 
> | peeringservicecountries | 否 | 
> | peeringservicelocations | 否 | 
> | peeringserviceproviders | 否 | 
> | peeringservices | 否 | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | policyevents | 否 | 
> | policystates | 否 | 
> | policytrackedresources | 否 | 
> | remediations | 否 | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | consoles | 否 |
> | dashboards | 否 | 
> | usersettings | 否 | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | workspacecollections |  否 | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | capacities |  否 | 

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | availableaccounts | 否 | 
> | providerregistrations | 否 | 
> | rollouts | 否 | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | workspaces | 否 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | replicationeligibilityresults | 否 |
> | vaults | 否。<br/><br/> 不支持跨 Azure 区域移动 Azure 备份的恢复服务保管库。<br/><br/> 在 Azure Site Recovery 的恢复服务保管库中，可以在目标区域中[禁用并重新创建保管库](../../site-recovery/move-vaults-across-regions.md)。 | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | openshiftclusters | 否 | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | namespaces |  否 | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | 查询 |  否 |  
> | resourcechangedetails | 否 | 
> | resourcechanges | 否 | 
> | resources | 否 | 
> | resourceshistory | 否 | 
> | subscriptionsstatus | 否 | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | childresources | 否 | 
> | emergingissues | 否 | 
> | events | 否 | 
> | metadata | 否 | 
> | 通知 | 否 | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 |
> | ------------- | ----------- |
> | deploymentScripts |  是<br/><br/>[将 Microsoft .Resources 资源移动到新区域](microsoft-resources-move-regions.md) |
> | templateSpecs |  是<br/><br/>[将 Microsoft .Resources 资源移动到新区域](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applications |  否 | 
> | saasresources | 否 | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | resourcehealthmetadata | 否 |
> | searchservices |  否 | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | 否 | 
> | advancedthreatprotectionsettings | 否 | 
> | alerts | 否 | 
> | allowedconnections | 否 | 
> | applicationwhitelistings | 否 | 
> | assessmentmetadata | 否 | 
> | assessments | 否 | 
> | autodismissalertsrules | 否 | 
> | automations | 否 | 
> | autoprovisioningsettings | 否 |
> | complianceresults | 否 | 
> | compliances | 否 | 
> | datacollectionagents | 否 | 
> | devicesecuritygroups | 否 | 
> | discoveredsecuritysolutions | 否 | 
> | externalsecuritysolutions | 否 | 
> | informationprotectionpolicies | 否 | 
> | iotsecuritysolutions | 否 | 
> | iotsecuritysolutions / analyticsmodels | 否 | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 否 | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 否 | 
> | jitnetworkaccesspolicies | 否 | 
> | 策略 | 否 | 
> | pricings | 否 | 
> | regulatorycompliancestandards | 否 | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | 否 | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 否 | 
> | securitycontacts | 否 | 
> | securitysolutions | 否 | 
> | securitysolutionsreferencedata | 否 | 
> | securitystatuses | 否 | 
> | securitystatusessummaries | 否 | 
> | servervulnerabilityassessments | 否 | 
> | 设置 | 否 | 
> | subassessments | 否 |
> | 任务 | 否 | 
> | topologies | 否 | 
> | workspacesettings | 否 | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | aggregations | 否 | 
> | alertrules | 否 | 
> | alertruletemplates | 否 | 
> | automationrules | 否 |
> | cases | 否 | 
> | dataconnectors | 否 | 
> | 实体 | 否 | 
> | entityqueries | 否 |
> | incidents | 否 | 
> | officeconsents | 否 | 
> | 设置 | 否 | 
> | threatintelligence | 否 | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | consoleservices | 否 | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | gateways | 否 | 
> | nodes | 否 | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | namespaces |  否 | 
> | premiummessagingregions | 否 | 
> | sku | 否 | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applications | 否 | 
> | clusters |  否 |  
> | containergroups | 否 | 
> | containergroupsets | 否 | 
> | edgeclusters | 否 | 
> | managedclusters | 否 |
> | networks | 否 | 
> | secretstores | 否 | 
> | volumes | 否 | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | applications |  否 | 
> | containergroups | 否 | 
> | gateways |  否 | 
> | networks |  否 | 
> | 机密 |  否 | 
> | volumes |  否 |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | rollouts | 否 | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | signalr |  否 |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | hybridusebenefits | 否 | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | appliancedefinitions | 否 | 
> | appliances | 否 | 
> | jitrequests | 否 | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | instancepools | 否 | 
> | locations | 否 |
> | managedinstances | 是 <br/><br/> [详细了解](../../azure-sql/database/move-resources-across-regions.md)如何在区域之间移动托管实例。 | 
> | managedinstances/databases | 是 | 
> | servers | 是 | 
> | servers/databases | 是 <br/><br/> [详细了解](../../azure-sql/database/move-resources-across-regions.md)如何在区域之间移动数据库。<br/><br/> [详细了解](../../resource-mover/tutorial-move-region-sql.md) 如何使用 Azure 资源移动器移动 azure SQL 数据库。  | 
> | servers/elasticpools | 是 <br/><br/> [详细了解](../../azure-sql/database/move-resources-across-regions.md)如何在区域之间移动弹性池。<br/><br/> [详细了解](../../resource-mover/tutorial-move-region-sql.md) 如何使用 Azure 资源移动器移动 azure SQL 弹性池。  | 
> | virtualclusters | 是 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  否 |  
> | sqlvirtualmachines |  否 |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storageaccounts | 是<br/><br/> [将 Azure 存储帐户移到另一个区域](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | caches | 否 | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storagesyncservices |  否 | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storagesyncservices | 否 | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | storagesyncservices | 否 | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | managers | 否 | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | clusters | 否 |
> | streamingjobs |  否 |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | environments | 否 | 
> | instances | 否 | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | subscriptions | 否 | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | services | 否 | 
> | supporttickets | 否 | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | workspaces | 否 | 
> | workspaces / bigdatapools | 否 | 
> | workspaces / sqlpools | 否 | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | environments |  否 | 
> | environments / eventsources |  否 |  
> | environments/referencedatasets |  否 | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | stores | 否 | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | imagetemplates | 否 | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | account |  否 | 
> | account / extension |  否 | 
> | account / project |  否 | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | arczones | 否 | 
> | resourcepools | 否 | 
> | vcenters | 否 | 
> | virtualmachines | 否 | 
> | virtualmachinetemplates | 否 | 
> | virtualnetworks | 否 | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | 否 | 
> | dedicatedcloudservices | 否 | 
> | virtualmachines | 否 | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | devices | 否 | 
> | vnfs | 否 | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | accounts | 否 | 
> | plans | 否 | 
> | registeredsubscriptions | 否 |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | availablestacks | 否 | 
> | billingmeters | 否 | 
> | certificates | 否 | 
> | connectiongateways |  否 |  
> | connections |  否 |  
> | customapis |  否 | 
> | deletedsites | 否 | 
> | deploymentlocations | 否 | 
> | georegions | 否 | 
> | hostingenvironments | 否 | 
> | kubeenvironments | 否 | 
> | publishingusers | 否 |
> | 建议 | 否 | 
> | resourcehealthmetadata | 否 | 
> | runtimes | 否 | 
> | serverfarms | 否 |  
> | serverfarms / eventgridfilters | N
> | sites |  否 | 
> | sites/premieraddons |  否 |  
> | sites/slots |  否 |  
> | sourcecontrols | 否 |
> | staticsites | 否 | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | multipleactivationkeys | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- |
> | deviceservices | 否 | 

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | 工作负荷 | 否 | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 区域移动 | 
> | ------------- | ----------- | 
> | components | 否 |
> | componentssummary | 否 | 
> | monitorinstances | 否 | 
> | monitorinstancessummary | 否 | 
> | monitors | 否 | 
## <a name="third-party-services"></a>第三方服务

第三方服务目前不支持移动操作。

## <a name="next-steps"></a>后续步骤

[详细了解](../../resource-mover/overview.md) 资源移动器服务。

