---
title: 完整模式删除
description: 显示资源类型如何在 Azure 资源管理器模板中进行完全模式删除。
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: e0c67bfcda81ad128e0018c4ab37c4b0cbe680f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184019"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>针对完全模式部署的 Azure 资源删除

本文描述了资源类型如何在不是以完全模式部署的模板中进行删除。

标记为 **Yes** 的资源类型不在以完全模式部署的模板中时，将会删除该类型。

标记为 **No** 的资源类型不在模板中时不会自动删除；但是，如果删除了父资源，则会删除它们。 有关此行为的完整描述，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。

如果部署到[模板中的多个资源组](./deploy-to-resource-group.md)，则可以删除部署操作中指定的资源组中的资源。 辅助资源组中的资源不会被删除。

资源按资源提供程序命名空间列出。 若要将资源提供程序命名空间与其 Azure 服务名称匹配，请参阅 [Azure 服务的资源提供程序](../management/azure-services-resource-providers.md)。

> [!NOTE]
> 在完全模式下部署模板之前，请始终使用 [what-if 操作](template-deploy-what-if.md)。 如果显示哪些资源将被创建、删除或修改。 使用 what-if 以避免意外删除资源。
跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
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
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DomainServices | 是 |
> | DomainServices / oucontainer | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | supportProviders | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
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
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | advisorScore | 否 |
> | 配置 | 否 |
> | generateRecommendations | 否 |
> | metadata | 否 |
> | 建议 | 否 |
> | 禁止显示 | 否 |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | farmBeats | 是 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | actionRules | 是 |
> | alerts | 否 |
> | alertsList | 否 |
> | alertsMetaData | 否 |
> | alertsSummary | 否 |
> | alertsSummaryList | 否 |
> | smartDetectorAlertRules | 是 |
> | smartGroups | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | servers | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | reportFeedback | 否 |
> | 服务 | 是 |
> | validateServiceName | 否 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | configurationStores | 是 |
> | configurationStores/eventGridFilters | 否 |
> | configurationStores/keyValues | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | Spring | 是 |
> | Spring/apps | 否 |
> | Spring/apps/deployments | 否 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | attestationProviders | 是 |
> | defaultProviders | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | 否 |
> | accessReviewScheduleSettings | 否 |
> | classicAdministrators | 否 |
> | dataAliases | 否 |
> | denyAssignments | 否 |
> | elevateAccess | 否 |
> | findOrphanRoleAssignments | 否 |
> | 锁定 | 否 |
> | 权限 | 否 |
> | policyAssignments | 否 |
> | policyDefinitions | 否 |
> | policyExemptions | 否 |
> | policySetDefinitions | 否 |
> | privateLinkAssociations | 否 |
> | providerOperations | 否 |
> | resourceManagementPrivateLinks | 是 |
> | roleAssignments | 否 |
> | roleAssignmentsUsageMetrics | 否 |
> | roleDefinitions | 否 |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | configurationProfileAssignments | 否 |
> | configurationProfilePreferences | 是 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | automationAccounts | 是 |
> | automationAccounts / configurations | 是 |
> | automationAccounts / jobs | 否 |
> | automationAccounts / privateEndpointConnectionProxies | 否 |
> | automationAccounts / privateEndpointConnections | 否 |
> | automationAccounts / privateLinkResources | 否 |
> | automationAccounts / runbooks | 是 |
> | automationAccounts / softwareUpdateConfigurations | 否 |
> | automationAccounts / webhooks | 否 |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | privateClouds | 是 |
> | privateClouds / addons | 否 |
> | privateClouds / authorizations | 否 |
> | privateClouds / clusters | 否 |
> | privateClouds / globalReachConnections | 否 |
> | privateClouds / hcxEnterpriseSites | 否 |
> | privateClouds / workloadNetworks | 否 |
> | privateClouds / workloadNetworks / dhcpConfigurations | 否 |
> | privateClouds / workloadNetworks / gateways | 否 |
> | privateClouds / workloadNetworks / portMirroringProfiles | 否 |
> | privateClouds / workloadNetworks / segments | 否 |
> | privateClouds / workloadNetworks / virtualMachines | 否 |
> | privateClouds / workloadNetworks / vmGroups | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | environments | 否 |
> | environments / accounts | 否 |
> | environments / accounts / namespaces | 否 |
> | environments / accounts / namespaces / configurations | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | b2cDirectories | 是 |
> | b2ctenants | 否 |
> | guestUsages | 是 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dataControllers | 是 |
> | postgresInstances | 是 |
> | sqlManagedInstances | 是 |
> | sqlServerInstances | 是 |
> | sqlServerRegistrations | 是 |
> | sqlServerRegistrations / sqlServers | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | cloudManifestFiles | 否 |
> | edgeSubscriptions | 是 |
> | linkedSubscriptions | 是 |
> | registrations | 是 |
> | registrations / customerSubscriptions | 否 |
> | registrations / products | 否 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | bareMetalInstances | 是 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | batchAccounts | 是 |
> | batchAccounts / certificates | 否 |
> | batchAccounts / pools | 否 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | billingAccounts | 否 |
> | billingAccounts / agreements | 否 |
> | billingAccounts / billingPermissions | 否 |
> | billingAccounts / billingProfiles | 否 |
> | billingAccounts / billingProfiles / billingPermissions | 否 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 否 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 否 |
> | billingAccounts / billingProfiles / billingSubscriptions | 否 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 否 |
> | billingAccounts / billingProfiles / customers | 否 |
> | billingAccounts / billingProfiles / instructions | 否 |
> | billingAccounts / billingProfiles / invoices | 否 |
> | billingAccounts / billingProfiles / invoices / pricesheet | 否 |
> | billingAccounts / billingProfiles / invoices / transactions | 否 |
> | billingAccounts / billingProfiles / invoiceSections | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 否 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 否 |
> | billingAccounts / billingProfiles / invoiceSections / products | 否 |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | 否 |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | 否 |
> | billingaccounts / billingprofiles / invoicesections / transactions | 否 |
> | billingaccounts / billingprofiles / invoicesections / transfers | 否 |
> | billingaccounts / billingprofiles / patchoperations | 否 |
> | billingaccounts / billingprofiles / paymentmethods | 否 |
> | billingaccounts / billingprofiles / policies | 否 |
> | billingaccounts / billingprofiles / pricesheet | 否 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 否 |
> | billingaccounts / billingprofiles / products | 否 |
> | billingAccounts / billingProfiles / reservations | 否 |
> | billingaccounts / billingprofiles / transactions | 否 |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | 否 |
> | billingAccounts / billingRoleAssignments | 否 |
> | billingAccounts / billingRoleDefinitions | 否 |
> | billingAccounts / billingSubscriptions | 否 |
> | billingaccounts / billingsubscriptions / invoices | 否 |
> | billingAccounts / createBillingRoleAssignment | 否 |
> | billingAccounts / createInvoiceSectionOperations | 否 |
> | billingAccounts / customers | 否 |
> | billingAccounts / customers / billingPermissions | 否 |
> | billingAccounts / customers / billingSubscriptions | 否 |
> | billingAccounts / customers / initiateTransfer | 否 |
> | billingAccounts / customers / policies | 否 |
> | billingAccounts / customers / products | 否 |
> | billingAccounts / customers / transactions | 否 |
> | billingAccounts / customers / transfers | 否 |
> | billingAccounts / departments | 否 |
> | billingAccounts / departments / billingPermissions | 否 |
> | billingAccounts / departments / billingRoleAssignments | 否 |
> | billingAccounts / departments / billingRoleDefinitions | 否 |
> | billingAccounts / enrollmentAccounts | 否 |
> | billingAccounts / enrollmentAccounts / billingPermissions | 否 |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | 否 |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | 否 |
> | billingAccounts / invoices | 否 |
> | billingAccounts / invoices / transactions | 否 |
> | billingAccounts / invoiceSections | 否 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | 否 |
> | billingAccounts / invoiceSections / elevate | 否 |
> | billingAccounts / invoiceSections / initiateTransfer | 否 |
> | billingAccounts / invoiceSections / patchOperations | 否 |
> | billingAccounts / invoiceSections / productMoveOperations | 否 |
> | billingAccounts / invoiceSections / products | 否 |
> | billingAccounts / invoiceSections / products / transfer | 否 |
> | billingAccounts / invoiceSections / products / updateAutoRenew | 否 |
> | billingAccounts / invoiceSections / transactions | 否 |
> | billingAccounts / invoiceSections / transfers | 否 |
> | billingAccounts / lineOfCredit | 否 |
> | billingAccounts / patchOperations | 否 |
> | billingAccounts / paymentMethods | 否 |
> | billingaccounts / products | 否 |
> | billingAccounts / reservations | 否 |
> | billingAccounts / transactions | 否 |
> | billingPeriods | 否 |
> | billingPermissions | 否 |
> | billingProperty | 否 |
> | billingRoleAssignments | 否 |
> | billingRoleDefinitions | 否 |
> | createBillingRoleAssignment | 否 |
> | departments | 否 |
> | enrollmentAccounts | 否 |
> | invoices | 否 |
> | transfers | 否 |
> | transfers / acceptTransfer | 否 |
> | transfers / declineTransfer | 否 |
> | transfers / operationStatus | 否 |
> | transfers / validateTransfer | 否 |
> | validateAddress | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | mapApis | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | blockchainMembers | 是 |
> | cordaMembers | 是 |
> | watchers | 是 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | TokenServices | 是 |
> | TokenServices / BlockchainNetworks | 否 |
> | TokenServices / Groups | 否 |
> | TokenServices / Groups / Accounts | 否 |
> | TokenServices / TokenTemplates | 否 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | blueprintAssignments | 否 |
> | blueprintAssignments / assignmentOperations | 否 |
> | blueprintAssignments / operations | 否 |
> | blueprints | 否 |
> | blueprints / artifacts | 否 |
> | blueprints / versions | 否 |
> | blueprints / versions / artifacts | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | botServices | 是 |
> | botServices/channels | 否 |
> | botServices / connections | 否 |
> | 语言 | 否 |
> | 模板 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | Redis | 是 |
> | Redis/EventGridFilters | 否 |
> | Redis/privateEndpointConnectionProxies | 否 |
> | Redis/privateEndpointConnectionProxies/validate | 否 |
> | Redis/privateEndpointConnections | 否 |
> | Redis/privateLinkResources | 否 |
> | redisEnterprise | 是 |
> | RedisEnterprise / privateEndpointConnectionProxies | 否 |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | 否 |
> | RedisEnterprise / privateEndpointConnections | 否 |
> | RedisEnterprise / privateLinkResources | 否 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | appliedReservations | 否 |
> | autoQuotaIncrease | 否 |
> | calculateExchange | 否 |
> | calculatePrice | 否 |
> | calculatePurchasePrice | 否 |
> | catalogs | 否 |
> | commercialReservationOrders | 否 |
> | 交易所 | 否 |
> | ownReservations | 否 |
> | placePurchaseOrder | 否 |
> | reservationOrders | 否 |
> | reservationOrders / calculateRefund | 否 |
> | reservationOrders / merge | 否 |
> | reservationOrders / reservations | 否 |
> | reservationOrders / reservations / revisions | 否 |
> | reservationOrders / return | 否 |
> | reservationOrders / split | 否 |
> | reservationOrders / swap | 否 |
> | reservations | 否 |
> | resourceProviders | 否 |
> | resources | 否 |
> | validateReservationOrder | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 |
> | CdnWebApplicationFirewallPolicies | 是 |
> | edgenodes | 否 |
> | 配置文件 | 是 |
> | profiles/endpoints | 是 |
> | profiles / endpoints / customdomains | 否 |
> | profiles / endpoints / origingroups | 否 |
> | profiles / endpoints / origins | 否 |
> | validateProbe | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | certificateOrders | 是 |
> | certificateOrders / certificates | 否 |
> | validateCertificateRegistrationInformation | 否 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 个人资料 | 否 |
> | resourceChanges | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | domainNames | 是 |
> | domainNames / capabilities | 否 |
> | domainNames / internalLoadBalancers | 否 |
> | domainNames / serviceCertificates | 否 |
> | domainNames / slots | 否 |
> | domainNames / slots / roles | 否 |
> | domainNames / slots / roles / metricDefinitions | 否 |
> | domainNames / slots / roles / metrics | 否 |
> | moveSubscriptionResources | 否 |
> | operatingSystemFamilies | 否 |
> | operatingSystems | 否 |
> | quotas | 否 |
> | resourceTypes | 否 |
> | validateSubscriptionMoveAvailability | 否 |
> | virtualMachines | 是 |
> | virtualMachines / diagnosticSettings | 否 |
> | virtualMachines / metricDefinitions | 否 |
> | virtualMachines / metrics | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | expressRouteCrossConnections | 否 |
> | expressRouteCrossConnections / peerings | 否 |
> | gatewaySupportedDevices | 否 |
> | networkSecurityGroups | 是 |
> | quotas | 否 |
> | reservedIps | 是 |
> | virtualNetworks | 是 |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | 否 |
> | virtualNetworks / virtualNetworkPeerings | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | disks | 否 |
> | images | 否 |
> | osImages | 否 |
> | osPlatformImages | 否 |
> | publicImages | 否 |
> | quotas | 否 |
> | storageAccounts | 是 |
> | storageAccounts / blobServices | 否 |
> | storageAccounts / fileServices | 否 |
> | storageAccounts / metricDefinitions | 否 |
> | storageAccounts / metrics | 否 |
> | storageAccounts / queueServices | 否 |
> | storageAccounts / services | 否 |
> | storageAccounts / services / diagnosticSettings | 否 |
> | storageAccounts / services / metricDefinitions | 否 |
> | storageAccounts / services / metrics | 否 |
> | storageAccounts / tableServices | 否 |
> | storageAccounts / vmImages | 否 |
> | vmImages | 否 |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | plans | 是 |
> | registeredSubscriptions | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts / privateEndpointConnectionProxies | 否 |
> | accounts / privateEndpointConnections | 否 |
> | accounts / privateLinkResources | 否 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | RateCard | 否 |
> | UsageAggregates | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | availabilitySets | 是 |
> | cloudServices | 是 |
> | cloudServices / networkInterfaces | 否 |
> | cloudServices / publicIPAddresses | 否 |
> | cloudServices / roleInstances | 否 |
> | cloudServices / roleInstances / networkInterfaces | 否 |
> | cloudServices / roles | 否 |
> | diskAccesses | 是 |
> | diskEncryptionSets | 是 |
> | disks | 是 |
> | galleries | 是 |
> | galleries / applications | 否 |
> | galleries / applications / versions | 否 |
> | galleries/images | 否 |
> | galleries/images/versions | 否 |
> | hostGroups | 是 |
> | hostGroups / hosts | 是 |
> | images | 是 |
> | proximityPlacementGroups | 是 |
> | restorePointCollections | 是 |
> | restorePointCollections / restorePoints | 否 |
> | sharedVMExtensions | 是 |
> | sharedVMExtensions / versions | 否 |
> | sharedVMImages | 是 |
> | sharedVMImages / versions | 否 |
> | snapshots | 是 |
> | sshPublicKeys | 是 |
> | virtualMachines | 是 |
> | virtualMachines / extensions | 是 |
> | virtualMachines / metricDefinitions | 否 |
> | virtualMachines/runCommands | 是 |
> | virtualMachineScaleSets | 是 |
> | virtualMachineScaleSets / extensions | 否 |
> | virtualMachineScaleSets / networkInterfaces | 否 |
> | virtualMachineScaleSets / publicIPAddresses | 否 |
> | virtualMachineScaleSets / virtualMachines | 否 |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | 否 |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | CacheNodes | 是 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | AggregatedCost | 否 |
> | 余额 | 否 |
> | 预算 | 否 |
> | Charges | 否 |
> | CostTags | 否 |
> | credits | 否 |
> | events | 否 |
> | 预测 | 否 |
> | lots | 否 |
> | 市场 | 否 |
> | Pricesheets | 否 |
> | products | 否 |
> | ReservationDetails | 否 |
> | ReservationRecommendationDetails | 否 |
> | ReservationRecommendations | 否 |
> | ReservationSummaries | 否 |
> | ReservationTransactions | 否 |
> | 标记 | 否 |
> | tenants | 否 |
> | 术语 | 否 |
> | UsageDetails | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | containerGroups | 是 |
> | serviceAssociationLinks | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | registries | 是 |
> | registries / agentPools | 是 |
> | registries / builds | 否 |
> | registries / builds / cancel | 否 |
> | registries / builds / getLogLink | 否 |
> | registries / buildTasks | 是 |
> | registries / buildTasks / steps | 否 |
> | registries / eventGridFilters | 否 |
> | registries/exportPipelines | 否 |
> | registries / generateCredentials | 否 |
> | registries / getBuildSourceUploadUrl | 否 |
> | registries / GetCredentials | 否 |
> | registries / importImage | 否 |
> | registries/importPipelines | 否 |
> | registries/pipelineRuns | 否 |
> | registries / privateEndpointConnectionProxies | 否 |
> | registries / privateEndpointConnectionProxies / validate | 否 |
> | registries / privateEndpointConnections | 否 |
> | registries / privateLinkResources | 否 |
> | registries / queueBuild | 否 |
> | registries / regenerateCredential | 否 |
> | registries / regenerateCredentials | 否 |
> | registries/replications | 是 |
> | registries / runs | 否 |
> | registries / runs / cancel | 否 |
> | registries / scheduleRun | 否 |
> | registries / scopeMaps | 否 |
> | registries / taskRuns | 否 |
> | registries/tasks | 是 |
> | registries / tokens | 否 |
> | registries / updatePolicies | 否 |
> | registries/webhooks | 是 |
> | registries / webhooks / getCallbackConfig | 否 |
> | registries / webhooks / ping | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | containerServices | 是 |
> | managedClusters | 是 |
> | openShiftManagedClusters | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 警报 | 否 |
> | BillingAccounts | 否 |
> | 预算 | 否 |
> | CloudConnectors | 否 |
> | 连接器 | 是 |
> | costAllocationRules | 否 |
> | Departments | 否 |
> | 维度 | 否 |
> | EnrollmentAccounts | 否 |
> | 导出 | 否 |
> | ExternalBillingAccounts | 否 |
> | ExternalBillingAccounts / Alerts | 否 |
> | ExternalBillingAccounts / Dimensions | 否 |
> | ExternalBillingAccounts / Forecast | 否 |
> | ExternalBillingAccounts / Query | 否 |
> | ExternalSubscriptions | 否 |
> | ExternalSubscriptions / Alerts | 否 |
> | ExternalSubscriptions / Dimensions | 否 |
> | ExternalSubscriptions / Forecast | 否 |
> | ExternalSubscriptions / Query | 否 |
> | 预测 | 否 |
> | 洞察力 | 否 |
> | 查询 | 否 |
> | register | 否 |
> | Reportconfigs | 否 |
> | 报表 | 否 |
> | 设置 | 否 |
> | showbackRules | 否 |
> | 视图 | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 请求 | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | associations | 否 |
> | resourceProviders | 是 |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | instances | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | jobs | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | workspaces | 是 |
> | workspaces/dbWorkspaces | 否 |
> | workspaces/virtualNetworkPeerings | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | catalogs | 是 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dataFactories | 是 |
> | dataFactories / diagnosticSettings | 否 |
> | dataFactories / metricDefinitions | 否 |
> | dataFactorySchema | 否 |
> | factories | 是 |
> | factories / integrationRuntimes | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts/dataLakeStoreAccounts | 否 |
> | accounts/storageAccounts | 否 |
> | accounts / storageAccounts / containers | 否 |
> | accounts / transferAnalyticsUnits | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts / eventGridFilters | 否 |
> | accounts/firewallRules | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | services | 是 |
> | services/projects | 是 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | BackupVaults | 是 |
> | ResourceOperationGateKeepers | 是 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts / shares | 否 |
> | accounts / shares / datasets | 否 |
> | accounts / shares / invitations | 否 |
> | accounts / shares / providersharesubscriptions | 否 |
> | accounts / shares / synchronizationSettings | 否 |
> | accounts / sharesubscriptions | 否 |
> | accounts / sharesubscriptions / consumerSourceDataSets | 否 |
> | accounts / sharesubscriptions / datasetmappings | 否 |
> | accounts / sharesubscriptions / triggers | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | servers / privateEndpointConnectionProxies | 否 |
> | servers / privateEndpointConnections | 否 |
> | servers / privateLinkResources | 否 |
> | servers / queryTexts | 否 |
> | servers / recoverableServers | 否 |
> | servers / start | 否 |
> | servers / stop | 否 |
> | servers / topQueryStatistics | 否 |
> | servers / virtualNetworkRules | 否 |
> | servers / waitStatistics | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | flexibleServers | 是 |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | servers / privateEndpointConnectionProxies | 否 |
> | servers / privateEndpointConnections | 否 |
> | servers / privateLinkResources | 否 |
> | servers / queryTexts | 否 |
> | servers / recoverableServers | 否 |
> | servers / start | 否 |
> | servers / stop | 否 |
> | servers / topQueryStatistics | 否 |
> | servers / upgrade | 否 |
> | servers / virtualNetworkRules | 否 |
> | servers / waitStatistics | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | flexibleServers | 是 |
> | serverGroups | 是 |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | servers / privateEndpointConnectionProxies | 否 |
> | servers / privateEndpointConnections | 否 |
> | servers / privateLinkResources | 否 |
> | servers / queryTexts | 否 |
> | servers / recoverableServers | 否 |
> | servers / topQueryStatistics | 否 |
> | servers / virtualNetworkRules | 否 |
> | servers / waitStatistics | 否 |
> | serversv2 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | artifactSources | 是 |
> | rollouts | 是 |
> | serviceTopologies | 是 |
> | serviceTopologies / services | 是 |
> | serviceTopologies / services / serviceUnits | 是 |
> | steps | 是 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applicationgroups | 是 |
> | applicationgroups / applications | 否 |
> | applicationgroups / desktops | 否 |
> | applicationgroups / startmenuitems | 否 |
> | hostpools | 是 |
> | hostpools / msixpackages | 否 |
> | hostpools / sessionhosts | 否 |
> | hostpools / sessionhosts / usersessions | 否 |
> | hostpools / usersessions | 否 |
> | workspaces | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | ElasticPools | 是 |
> | ElasticPools / IotHubTenants | 是 |
> | ElasticPools / IotHubTenants / securitySettings | 否 |
> | IotHubs | 是 |
> | IotHubs / eventGridFilters | 否 |
> | IotHubs / securitySettings | 否 |
> | ProvisioningServices | 是 |
> | usages | 否 |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts / instances | 是 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | pipelines | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 控制器 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | labcenters | 是 |
> | labs | 是 |
> | labs / environments | 是 |
> | labs / serviceRunners | 是 |
> | labs / virtualMachines | 是 |
> | schedules | 是 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | digitalTwinsInstances | 是 |
> | digitalTwinsInstances / endpoints | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | databaseAccountNames | 否 |
> | databaseAccounts | 是 |
> | restorableDatabaseAccounts | 否 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | domains | 是 |
> | domains / domainOwnershipidentifiers | 否 |
> | generateSsoRequest | 否 |
> | topLevelDomains | 否 |
> | validateDomainRegistrationInformation | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | lcsprojects | 否 |
> | lcsprojects / clouddeployments | 否 |
> | lcsprojects / connectors | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | services | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | domains | 是 |
> | domains / topics | 否 |
> | eventSubscriptions | 否 |
> | extensionTopics | 否 |
> | partnerNamespaces | 是 |
> | partnerNamespaces / eventChannels | 否 |
> | partnerRegistrations | 是 |
> | partnerTopics | 是 |
> | partnerTopics / eventSubscriptions | 否 |
> | systemTopics | 是 |
> | systemTopics / eventSubscriptions | 否 |
> | topics | 是 |
> | topicTypes | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | namespaces | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / disasterrecoveryconfigs | 否 |
> | namespaces / eventhubs | 否 |
> | namespaces / eventhubs / authorizationrules | 否 |
> | namespaces / eventhubs / consumergroups | 否 |
> | namespaces / networkrulesets | 否 |
> | namespaces / privateEndpointConnections | 否 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | experimentWorkspaces | 是 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | namespaces | 是 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | featureProviders | 否 |
> | features | 否 |
> | providers | 否 |
> | subscriptionFeatureRegistrations | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | enroll | 否 |
> | galleryitems | 否 |
> | generateartifactaccessuri | 否 |
> | myareas | 否 |
> | myareas / areas | 否 |
> | myareas / areas / areas | 否 |
> | myareas / areas / areas / galleryitems | 否 |
> | myareas / areas / galleryitems | 否 |
> | myareas / galleryitems | 否 |
> | register | 否 |
> | resources | 否 |
> | retrieveresourcesbyid | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | autoManagedAccounts | 是 |
> | autoManagedVmConfigurationProfiles | 是 |
> | configurationProfileAssignments | 否 |
> | guestConfigurationAssignments | 否 |
> | software | 否 |
> | softwareUpdateProfile | 否 |
> | softwareUpdates | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hanainstances | 是 |
> | sapMonitors | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dedicatedHSMs | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters/applications | 否 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | services | 是 |
> | services / iomtconnectors | 否 |
> | services / iomtconnectors / connections | 否 |
> | services / iomtconnectors / mappings | 否 |
> | services / privateEndpointConnectionProxies | 否 |
> | services / privateEndpointConnections | 否 |
> | services / privateLinkResources | 否 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | machines | 是 |
> | machines / assessPatches | 否 |
> | machines / extensions | 是 |
> | machines / installPatches | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dataManagers | 是 |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 设备 | 是 |
> | networkFunctions | 是 |
> | networkFunctionVendors | 否 |
> | registeredSubscriptions | 否 |
> | vendors | 否 |
> | vendors / vendorSkus | 否 |
> | vendors / vendorSkus / previewSubscriptions | 否 |
> | virtualNetworkFunctions | 是 |
> | virtualNetworkFunctionVendors | 否 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | components | 是 |
> | networkScopes | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | jobs | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | appTemplates | 否 |
> | IoTApps | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 图形 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | deletedVaults | 否 |
> | hsmPools | 是 |
> | managedHSMs | 是 |
> | vaults | 是 |
> | vaults / accessPolicies | 否 |
> | vaults / eventGridFilters | 否 |
> | vaults / keys | 否 |
> | vaults / keys / versions | 否 |
> | vaults / secrets | 否 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | connectedClusters | 是 |
> | registeredSubscriptions | 否 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 扩展 | 否 |
> | sourceControlConfigurations | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters / attacheddatabaseconfigurations | 否 |
> | clusters / databases | 否 |
> | clusters / databases / dataconnections | 否 |
> | clusters / databases / eventhubconnections | 否 |
> | clusters / databases / principalassignments | 否 |
> | clusters / dataconnections | 否 |
> | clusters / principalassignments | 否 |
> | clusters / sharedidentities | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | labaccounts | 是 |
> | users | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hostingEnvironments | 是 |
> | integrationAccounts | 是 |
> | integrationServiceEnvironments | 是 |
> | integrationServiceEnvironments / managedApis | 是 |
> | isolatedEnvironments | 是 |
> | workflows | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | commitmentPlans | 是 |
> | webServices | 是 |
> | 工作区 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | workspaces | 是 |
> | workspaces/batchEndpoints | 是 |
> | workspaces/batchEndpoints/deployments | 是 |
> | workspaces/codes | 否 |
> | workspaces/codes/versions | 否 |
> | workspaces / computes | 否 |
> | workspaces/datastores | 否 |
> | workspaces / eventGridFilters | 否 |
> | workspaces/jobs | 否 |
> | workspaces/labelingJobs | 否 |
> | workspaces / linkedServices | 否 |
> | workspaces/models | 否 |
> | workspaces/models/versions | 否 |
> | workspaces/onlineEndpoints | 是 |
> | workspaces/onlineEndpoints/deployments | 是 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applyUpdates | 否 |
> | configurationAssignments | 否 |
> | maintenanceConfigurations | 是 |
> | publicMaintenanceConfigurations | 否 |
> | updates | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 标识 | 否 |
> | userAssignedIdentities | 是 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managedNetworks | 是 |
> | managedNetworks / managedNetworkGroups | 是 |
> | managedNetworks / managedNetworkPeeringPolicies | 是 |
> | 通知 | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 否 |
> | registrationAssignments | 否 |
> | registrationDefinitions | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | getEntities | 否 |
> | managementGroups | 否 |
> | managementGroups / settings | 否 |
> | resources | 否 |
> | startTenantBackfill | 否 |
> | tenantBackfillStatus | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts / eventGridFilters | 否 |
> | accounts / privateAtlases | 是 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | macc | 否 |
> | offers | 否 |
> | offerTypes | 否 |
> | offerTypes / publishers | 否 |
> | offerTypes / publishers / offers | 否 |
> | offerTypes / publishers / offers / plans | 否 |
> | offerTypes / publishers / offers / plans / agreements | 否 |
> | offerTypes / publishers / offers / plans / configs | 否 |
> | offerTypes / publishers / offers / plans / configs / importImage | 否 |
> | privategalleryitems | 否 |
> | privateStoreClient | 否 |
> | privateStores | 否 |
> | privateStores / offers | 否 |
> | products | 否 |
> | publishers | 否 |
> | publishers / offers | 否 |
> | publishers / offers / amendments | 否 |
> | register | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | classicDevServices | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 协议 | 否 |
> | offertypes | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | mediaservices | 是 |
> | mediaservices / accountFilters | 否 |
> | mediaservices / assets | 否 |
> | mediaservices / assets / assetFilters | 否 |
> | mediaservices / contentKeyPolicies | 否 |
> | mediaservices / eventGridFilters | 否 |
> | mediaservices / liveEventOperations | 否 |
> | mediaservices / liveEvents | 是 |
> | mediaservices / liveEvents / liveOutputs | 否 |
> | mediaservices / liveOutputOperations | 否 |
> | mediaservices / mediaGraphs | 否 |
> | mediaservices / privateEndpointConnectionOperations | 否 |
> | mediaservices / privateEndpointConnectionProxies | 否 |
> | mediaservices / privateEndpointConnections | 否 |
> | mediaservices / streamingEndpointOperations | 否 |
> | mediaservices / streamingEndpoints | 是 |
> | mediaservices / streamingLocators | 否 |
> | mediaservices / streamingPolicies | 否 |
> | mediaservices / transforms | 否 |
> | mediaservices / transforms / jobs | 否 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | appClusters | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | assessmentProjects | 是 |
> | migrateprojects | 是 |
> | moveCollections | 是 |
> | projects | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | 是 |
> | objectUnderstandingAccounts | 是 |
> | remoteRenderingAccounts | 是 |
> | spatialAnchorsAccounts | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | netAppAccounts | 是 |
> | netAppAccounts / accountBackups | 否 |
> | netAppAccounts / capacityPools | 是 |
> | netAppAccounts / capacityPools / volumes | 是 |
> | netAppAccounts / capacityPools / volumes / snapshots | 否 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applicationGateways | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 |
> | applicationSecurityGroups | 是 |
> | azureFirewallFqdnTags | 否 |
> | azureFirewalls | 是 |
> | bastionHosts | 是 |
> | bgpServiceCommunities | 否 |
> | connections | 是 |
> | ddosCustomPolicies | 是 |
> | ddosProtectionPlans | 是 |
> | dnsOperationStatuses | 否 |
> | dnszones | 是 |
> | dnszones / A | 否 |
> | dnszones / AAAA | 否 |
> | dnszones / all | 否 |
> | dnszones / CAA | 否 |
> | dnszones / CNAME | 否 |
> | dnszones / MX | 否 |
> | dnszones / NS | 否 |
> | dnszones / PTR | 否 |
> | dnszones / recordsets | 否 |
> | dnszones / SOA | 否 |
> | dnszones / SRV | 否 |
> | dnszones / TXT | 否 |
> | expressRouteCircuits | 是 |
> | expressRouteCrossConnections | 是 |
> | expressRouteGateways | 是 |
> | expressRoutePorts | 是 |
> | expressRouteServiceProviders | 否 |
> | firewallPolicies | 是 |
> | frontdoors | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是 |
> | getDnsResourceReference | 否 |
> | internalNotify | 否 |
> | ipGroups | 是 |
> | loadBalancers | 是 |
> | localNetworkGateways | 是 |
> | natGateways | 是 |
> | networkIntentPolicies | 是 |
> | networkInterfaces | 是 |
> | networkProfiles | 是 |
> | networkSecurityGroups | 是 |
> | networkWatchers | 是 |
> | networkWatchers / connectionMonitors | 是 |
> | networkWatchers / flowLogs | 是 |
> | networkWatchers / lenses | 是 |
> | networkWatchers / pingMeshes | 是 |
> | p2sVpnGateways | 是 |
> | privateDnsOperationStatuses | 否 |
> | privateDnsZones | 是 |
> | privateDnsZones / A | 否 |
> | privateDnsZones / AAAA | 否 |
> | privateDnsZones / all | 否 |
> | privateDnsZones / CNAME | 否 |
> | privateDnsZones / MX | 否 |
> | privateDnsZones / PTR | 否 |
> | privateDnsZones / SOA | 否 |
> | privateDnsZones / SRV | 否 |
> | privateDnsZones / TXT | 否 |
> | privateDnsZones / virtualNetworkLinks | 是 |
> | privateEndpoints | 是 |
> | privateLinkServices | 是 |
> | publicIPAddresses | 是 |
> | publicIPPrefixes | 是 |
> | routeFilters | 是 |
> | routeTables | 是 |
> | serviceEndpointPolicies | 是 |
> | trafficManagerGeographicHierarchies | 否 |
> | trafficmanagerprofiles | 是 |
> | trafficmanagerprofiles / heatMaps | 否 |
> | trafficManagerUserMetricsKeys | 否 |
> | virtualHubs | 是 |
> | virtualNetworkGateways | 是 |
> | virtualNetworks | 是 |
> | virtualNetworks/subnets | 否 |
> | virtualNetworkTaps | 是 |
> | virtualWans | 是 |
> | vpnGateways | 是 |
> | vpnSites | 是 |
> | webApplicationFirewallPolicies | 是 |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | NotebookProxies | 否 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | namespaces | 是 |
> | namespaces / notificationHubs | 是 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | osNamespaces | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | HyperVsites | 是 |
> | ImportSites | 是 |
> | MasterSites | 是 |
> | ServerSites | 是 |
> | VMwareSites | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | deletedWorkspaces | 否 |
> | linkTargets | 否 |
> | storageInsightConfigs | 否 |
> | workspaces | 是 |
> | workspaces / dataExports | 否 |
> | workspaces / dataSources | 否 |
> | workspaces / linkedServices | 否 |
> | workspaces / linkedStorageAccounts | 否 |
> | workspaces / metadata | 否 |
> | workspaces / query | 否 |
> | workspaces / scopedPrivateLinkProxies | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations | 是 |
> | solutions | 是 |
> | 视图 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | legacyPeerings | 否 |
> | peerAsns | 否 |
> | peerings | 是 |
> | peeringServiceCountries | 否 |
> | peeringServiceProviders | 否 |
> | peeringServices | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | attestations | 否 |
> | policyEvents | 否 |
> | policyMetadata | 否 |
> | policyStates | 否 |
> | policyTrackedResources | 否 |
> | remediations | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | consoles | 否 |
> | dashboards | 是 |
> | userSettings | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | 是 |
> | tenants | 是 |
> | tenants / workspaces | 否 |
> | workspaceCollections | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capacities | 是 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | deletedAccounts | 否 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | providerRegistrations | 否 |
> | providerRegistrations / defaultRollouts | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 |
> | rollouts | 是 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 工作区 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | backupProtectedItems | 否 |
> | vaults | 是 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | OpenShiftClusters | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | namespaces | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / hybridconnections | 否 |
> | namespaces / hybridconnections / authorizationrules | 否 |
> | namespaces / privateEndpointConnections | 否 |
> | namespaces / wcfrelays | 否 |
> | namespaces / wcfrelays / authorizationrules | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 查询 | 是 |
> | resourceChangeDetails | 否 |
> | resourceChanges | 否 |
> | resources | 否 |
> | resourcesHistory | 否 |
> | subscriptionsStatus | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | availabilityStatuses | 否 |
> | childAvailabilityStatuses | 否 |
> | childResources | 否 |
> | emergingissues | 否 |
> | events | 否 |
> | impactedResources | 否 |
> | metadata | 否 |
> | 通知 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | calculateTemplateHash | 否 |
> | deployments | 否 |
> | deployments / operations | 否 |
> | deploymentScripts | 是 |
> | deploymentScripts / logs | 否 |
> | 链接 | 否 |
> | notifyResourceJobs | 否 |
> | providers | 否 |
> | resourceGroups | 否 |
> | subscriptions | 否 |
> | templateSpecs | 是 |
> | templateSpecs / versions | 是 |
> | tenants | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applications | 是 |
> | saasresources | 否 |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clouds | 是 |
> | VirtualMachines | 是 |
> | VirtualMachineTemplates | 是 |
> | VirtualNetworks | 是 |
> | vmmservers | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 否 |
> | searchServices | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 否 |
> | advancedThreatProtectionSettings | 否 |
> | alerts | 否 |
> | alertsSuppressionRules | 否 |
> | allowedConnections | 否 |
> | applicationWhitelistings | 否 |
> | assessmentMetadata | 否 |
> | assessments | 否 |
> | autoDismissAlertsRules | 否 |
> | automations | 是 |
> | AutoProvisioningSettings | 否 |
> | Compliances | 否 |
> | 连接器 | 否 |
> | dataCollectionAgents | 否 |
> | deviceSecurityGroups | 否 |
> | discoveredSecuritySolutions | 否 |
> | externalSecuritySolutions | 否 |
> | InformationProtectionPolicies | 否 |
> | iotDefenderSettings | 否 |
> | iotSecuritySolutions | 是 |
> | iotSecuritySolutions / analyticsModels | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 |
> | iotSecuritySolutions / iotAlerts | 否 |
> | iotSecuritySolutions / iotAlertTypes | 否 |
> | iotSecuritySolutions / iotRecommendations | 否 |
> | iotSecuritySolutions / iotRecommendationTypes | 否 |
> | iotSensors | 否 |
> | jitNetworkAccessPolicies | 否 |
> | jitPolicies | 否 |
> | 策略 | 否 |
> | pricings | 否 |
> | regulatoryComplianceStandards | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 |
> | secureScoreControlDefinitions | 否 |
> | secureScoreControls | 否 |
> | secureScores | 否 |
> | secureScores / secureScoreControls | 否 |
> | securityContacts | 否 |
> | securitySolutions | 否 |
> | securitySolutionsReferenceData | 否 |
> | securityStatuses | 否 |
> | securityStatusesSummaries | 否 |
> | serverVulnerabilityAssessments | 否 |
> | 设置 | 否 |
> | sqlVulnerabilityAssessments | 否 |
> | subAssessments | 否 |
> | 任务 | 否 |
> | topologies | 否 |
> | workspaceSettings | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | aggregations | 否 |
> | alertRules | 否 |
> | alertRuleTemplates | 否 |
> | automationRules | 否 |
> | bookmarks | 否 |
> | cases | 否 |
> | dataConnectors | 否 |
> | dataConnectorsCheckRequirements | 否 |
> | 实体 | 否 |
> | entityQueries | 否 |
> | incidents | 否 |
> | officeConsents | 否 |
> | 设置 | 否 |
> | threatIntelligence | 否 |
> | watchlists | 否 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | consoleServices | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | namespaces | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / disasterrecoveryconfigs | 否 |
> | namespaces / eventgridfilters | 否 |
> | namespaces / networkrulesets | 否 |
> | namespaces / privateEndpointConnections | 否 |
> | namespaces / queues | 否 |
> | namespaces / queues / authorizationrules | 否 |
> | namespaces / topics | 否 |
> | namespaces / topics / authorizationrules | 否 |
> | namespaces / topics / subscriptions | 否 |
> | namespaces / topics / subscriptions / rules | 否 |
> | premiumMessagingRegions | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applications | 是 |
> | clusters | 是 |
> | clusters/applications | 否 |
> | containerGroups | 是 |
> | containerGroupSets | 是 |
> | edgeclusters | 是 |
> | edgeclusters / applications | 否 |
> | managedclusters | 是 |
> | managedclusters / nodetypes | 否 |
> | networks | 是 |
> | secretstores | 是 |
> | secretstores / certificates | 否 |
> | secretstores / secrets | 否 |
> | volumes | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applications | 是 |
> | containerGroups | 是 |
> | gateways | 是 |
> | networks | 是 |
> | 机密 | 是 |
> | volumes | 是 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | providerRegistrations | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 |
> | rollouts | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | SignalR | 是 |
> | SignalR / eventGridFilters | 否 |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts / accountQuotaPolicies | 否 |
> | accounts/groupPolicies | 否 |
> | accounts/jobs | 否 |
> | accounts/storageContainers | 否 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hybridUseBenefits | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applicationDefinitions | 是 |
> | applications | 是 |
> | jitRequests | 是 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managedInstances | 是 |
> | managedInstances / databases | 是 |
> | managedInstances / databases / backupShortTermRetentionPolicies | 否 |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | 否 |
> | managedInstances / databases / vulnerabilityAssessments | 否 |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | 否 |
> | managedInstances / encryptionProtector | 否 |
> | managedInstances / keys | 否 |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 否 |
> | managedInstances / vulnerabilityAssessments | 否 |
> | servers | 是 |
> | servers / administrators | 否 |
> | servers / communicationLinks | 否 |
> | servers/databases | 是 |
> | servers / encryptionProtector | 否 |
> | servers / firewallRules | 否 |
> | servers / keys | 否 |
> | servers / restorableDroppedDatabases | 否 |
> | servers / serviceobjectives | 否 |
> | servers / tdeCertificates | 否 |
> | virtualClusters | 否 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | 是 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 否 |
> | SqlVirtualMachines | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | deletedAccounts | 否 |
> | storageAccounts | 是 |
> | storageAccounts / blobServices | 否 |
> | storageAccounts / fileServices | 否 |
> | storageAccounts / queueServices | 否 |
> | storageAccounts / services | 否 |
> | storageAccounts / services / metricDefinitions | 否 |
> | storageAccounts / tableServices | 否 |
> | usages | 否 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | caches | 是 |
> | caches / storageTargets | 否 |
> | usageModels | 否 |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | replicationGroups | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices/syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices / workflows | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices/syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices / workflows | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices/syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices / workflows | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managers | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters/privateEndpoints | 否 |
> | streamingjobs | 是 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | acceptChangeTenant | 否 |
> | aliases | 否 |
> | cancel | 否 |
> | changeTenantRequest | 否 |
> | changeTenantStatus | 否 |
> | CreateSubscription | 否 |
> | enable | 否 |
> | 重命名 | 否 |
> | SubscriptionDefinitions | 否 |
> | SubscriptionOperations | 否 |
> | subscriptions | 否 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | privateLinkHubs | 是 |
> | workspaces | 是 |
> | workspaces/bigDataPools | 是 |
> | workspaces / operationStatuses | 否 |
> | workspaces / sqlDatabases | 是 |
> | workspaces / sqlPools | 是 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | environments | 是 |
> | environments / accessPolicies | 否 |
> | environments / eventsources | 是 |
> | environments / referenceDataSets | 是 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | stores | 是 |
> | stores / accessPolicies | 否 |
> | stores / services | 否 |
> | stores / services / tokens | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | imageTemplates | 是 |
> | imageTemplates / runOutputs | 否 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | ArcZones | 是 |
> | ResourcePools | 是 |
> | VCenters | 是 |
> | VirtualMachines | 是 |
> | VirtualMachineTemplates | 是 |
> | VirtualNetworks | 是 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | 是 |
> | dedicatedCloudServices | 是 |
> | virtualMachines | 是 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 设备 | 是 |
> | registeredSubscriptions | 否 |
> | vendors | 否 |
> | vendors / skus | 否 |
> | vendors / vnfs | 否 |
> | virtualNetworkFunctionSkus | 否 |
> | vnfs | 是 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | plans | 是 |
> | registeredSubscriptions | 否 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | apiManagementAccounts | 否 |
> | apiManagementAccounts / apiAcls | 否 |
> | apiManagementAccounts / apis | 否 |
> | apiManagementAccounts / apis / apiAcls | 否 |
> | apiManagementAccounts / apis / connectionAcls | 否 |
> | apiManagementAccounts / apis / connections | 否 |
> | apiManagementAccounts / apis / connections / connectionAcls | 否 |
> | apiManagementAccounts / apis / localizedDefinitions | 否 |
> | apiManagementAccounts / connectionAcls | 否 |
> | apiManagementAccounts / connections | 否 |
> | billingMeters | 否 |
> | certificates | 是 |
> | connectionGateways | 是 |
> | connections | 是 |
> | customApis | 是 |
> | deletedSites | 否 |
> | hostingEnvironments | 是 |
> | hostingEnvironments / eventGridFilters | 否 |
> | hostingEnvironments / multiRolePools | 否 |
> | hostingEnvironments / workerPools | 否 |
> | kubeEnvironments | 是 |
> | publishingUsers | 否 |
> | 建议 | 否 |
> | resourceHealthMetadata | 否 |
> | runtimes | 否 |
> | serverFarms | 是 |
> | serverFarms / eventGridFilters | 否 |
> | serverFarms / firstPartyApps | 否 |
> | serverFarms / firstPartyApps / keyVaultSettings | 否 |
> | sites | 是 |
> | sites/config  | 否 |
> | sites / eventGridFilters | 否 |
> | sites / hostNameBindings | 否 |
> | sites / networkConfig | 否 |
> | sites/premieraddons | 是 |
> | sites/slots | 是 |
> | sites / slots / eventGridFilters | 否 |
> | sites / slots / hostNameBindings | 否 |
> | sites / slots / networkConfig | 否 |
> | sourceControls | 否 |
> | staticSites | 是 |
> | validate | 否 |
> | verifyHostingEnvironmentVnet | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | multipleActivationKeys | 是 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DeviceServices | 是 |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 工作负荷 | 是 |
> | workloads / instances | 否 |
> | workloads / versions | 否 |
> | workloads / versions / artifacts | 否 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | components | 否 |
> | componentsSummary | 否 |
> | monitorInstances | 否 |
> | monitorInstancesSummary | 否 |
> | monitors | 否 |
> | notificationSettings | 否 |

## <a name="next-steps"></a>后续步骤

若要以逗号分隔值文件的形式获取同一数据，请下载 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)。