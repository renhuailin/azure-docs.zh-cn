---
title: 资源的标记支持
description: 显示支持标记的 Azure资源类型。 提供所有 Azure 服务的详细信息。
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: 4f15fea365fc242f3c7253a39b6a877dd672deb1
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453282"
---
# <a name="tag-support-for-azure-resources"></a>Azure 资源的标记支持
本文介绍某一资源类型是否支持[标记](tag-resources.md)。 标记为“支持标记”的列指示资源类型是否具有标记的属性。 标记为“在成本报表中标记”的列指示该资源类型是否将标记传递给成本报表。 可以通过[成本管理成本分析](../../cost-management-billing/costs/group-filter.md)和 [Azure 账单发票和每日使用情况数据](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)中的标记查看成本。

若要以逗号分隔值文件的形式获取同一数据，请下载 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
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
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
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
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
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
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
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
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
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
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - Microsoft.Purview
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
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
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
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
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 是 | 是 |
> | DomainServices / oucontainer | 否 | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 否 | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 否 | 否 |
> | addsservices | 否 | 否 |
> | 代理 | 否 | 否 |
> | anonymousapiusers | 否 | 否 |
> | 配置 | 否 | 否 |
> | 日志 | 否 | 否 |
> | 报表 | 否 | 否 |
> | servicehealthmetrics | 否 | 否 |
> | services | 否 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | advisorScore | 否 | 否 |
> | 配置 | 否 | 否 |
> | generateRecommendations | 否 | 否 |
> | metadata | 否 | 否 |
> | 建议 | 否 | 否 |
> | 禁止显示 | 否 | 否 |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | farmBeats | 是 | 是 |
> | farmBeats / eventGridFilters | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | actionRules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertsList | 否 | 否 |
> | alertsMetaData | 否 | 否 |
> | alertsSummary | 否 | 否 |
> | alertsSummaryList | 否 | 否 |
> | migrateFromSmartDetection | 否 | 否 |
> | resourceHealthAlertRules | 是 | 是 |
> | smartDetectorAlertRules | 是 | 是 |
> | smartGroups | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | deletedServices | 否 | 否 |
> | getDomainOwnershipIdentifier | 否 | 否 |
> | reportFeedback | 否 | 否 |
> | 服务 | 是 | 是 |
> | validateServiceName | 否 | 否 |

> [!NOTE]
> Azure API 管理仅支持为每个服务创建最多 15 个标记名称/值对。

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | migrateProjects | 是 | 是 |
> | migrateProjects / assessments | 否 | 否 |
> | migrateProjects / assessments / assessedApplications | 否 | 否 |
> | migrateProjects / assessments / assessedApplications / machines | 否 | 否 |
> | migrateProjects / assessments / assessedMachines | 否 | 否 |
> | migrateProjects / assessments / assessedMachines / applications | 否 | 否 |
> | migrateProjects / assessments / machinesToAssess | 否 | 否 |
> | migrateProjects / sites | 否 | 否 |
> | migrateProjects / sites / applianceConfigurations | 否 | 否 |
> | migrateProjects / sites / machines | 否 | 否 |
> | osVersions | 否 | 否 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 是 | 否 |
> | configurationStores/eventGridFilters | 否 | 否 |
> | configurationStores/keyValues | 否 | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | Spring | 是 | 是 |
> | Spring/apps | 否 | 否 |
> | Spring/apps/deployments | 否 | 否 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 是 | 是 |
> | defaultProviders | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | 否 | 否 |
> | accessReviewScheduleSettings | 否 | 否 |
> | classicAdministrators | 否 | 否 |
> | dataAliases | 否 | 否 |
> | dataPolicyManifests | 否 | 否 |
> | denyAssignments | 否 | 否 |
> | elevateAccess | 否 | 否 |
> | findOrphanRoleAssignments | 否 | 否 |
> | 锁定 | 否 | 否 |
> | 权限 | 否 | 否 |
> | policyAssignments | 否 | 否 |
> | policyDefinitions | 否 | 否 |
> | policyExemptions | 否 | 否 |
> | policySetDefinitions | 否 | 否 |
> | privateLinkAssociations | 否 | 否 |
> | providerOperations | 否 | 否 |
> | resourceManagementPrivateLinks | 是 | 是 |
> | roleAssignmentApprovals | 否 | 否 |
> | roleAssignments | 否 | 否 |
> | roleAssignmentScheduleInstances | 否 | 否 |
> | roleAssignmentScheduleRequests | 否 | 否 |
> | roleAssignmentSchedules | 否 | 否 |
> | roleAssignmentsUsageMetrics | 否 | 否 |
> | roleDefinitions | 否 | 否 |
> | roleEligibilityScheduleInstances | 否 | 否 |
> | roleEligibilityScheduleRequests | 否 | 否 |
> | roleEligibilitySchedules | 否 | 否 |
> | roleManagementPolicies | 否 | 否 |
> | roleManagementPolicyAssignments | 否 | 否 |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | configurationProfileAssignments | 否 | 否 |
> | configurationProfilePreferences | 是 | 是 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 是 | 是 |
> | automationAccounts / configurations | 是 | 是 |
> | automationAccounts / jobs | 否 | 否 |
> | automationAccounts / privateEndpointConnectionProxies | 否 | 否 |
> | automationAccounts / privateEndpointConnections | 否 | 否 |
> | automationAccounts / privateLinkResources | 否 | 否 |
> | automationAccounts / runbooks | 是 | 是 |
> | automationAccounts / softwareUpdateConfigurations | 否 | 否 |
> | automationAccounts / webhooks | 否 | 否 |

> [!NOTE]
> Azure 自动化仅支持为每个自动化资源创建最多 15 个标记名称/值对。

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | privateClouds | 是 | 是 |
> | privateClouds / addons | 否 | 否 |
> | privateClouds / authorizations | 否 | 否 |
> | privateClouds / cloudLinks | 否 | 否 |
> | privateClouds / clusters | 否 | 否 |
> | privateClouds / clusters / datastores | 否 | 否 |
> | privateClouds / globalReachConnections | 否 | 否 |
> | privateClouds / hcxEnterpriseSites | 否 | 否 |
> | privateClouds / scriptExecutions | 否 | 否 |
> | privateClouds / scriptPackages | 否 | 否 |
> | privateClouds / scriptPackages / scriptCmdlets | 否 | 否 |
> | privateClouds / workloadNetworks | 否 | 否 |
> | privateClouds / workloadNetworks / dhcpConfigurations | 否 | 否 |
> | privateClouds / workloadNetworks / dnsServices | 否 | 否 |
> | privateClouds / workloadNetworks / dnsZones | 否 | 否 |
> | privateClouds / workloadNetworks / gateways | 否 | 否 |
> | privateClouds / workloadNetworks / portMirroringProfiles | 否 | 否 |
> | privateClouds / workloadNetworks / segments | 否 | 否 |
> | privateClouds / workloadNetworks / virtualMachines | 否 | 否 |
> | privateClouds / workloadNetworks / vmGroups | 否 | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | environments | 否 | 否 |
> | environments / accounts | 否 | 否 |
> | environments / accounts / namespaces | 否 | 否 |
> | environments / accounts / namespaces / configurations | 否 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 是 | 否 |
> | b2ctenants | 否 | 否 |
> | guestUsages | 是 | 是 |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dataControllers | 是 | 是 |
> | dataWarehouseInstances | 是 | 是 |
> | postgresInstances | 是 | 是 |
> | sqlManagedInstances | 是 | 是 |
> | sqlServerInstances | 是 | 是 |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | autopilotEnvironments | 是 | 是 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | sqlServerRegistrations | 是 | 是 |
> | sqlServerRegistrations / sqlServers | 否 | 否 |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | catalogs | 是 | 是 |
> | catalogs / products | 是 | 是 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | 否 | 否 |
> | edgeSubscriptions | 是 | 是 |
> | linkedSubscriptions | 是 | 是 |
> | registrations | 是 | 是 |
> | registrations / customerSubscriptions | 否 | 否 |
> | registrations / products | 否 | 否 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | galleryImages | 是 | 是 |
> | networkInterfaces | 是 | 是 |
> | virtualHardDisks | 是 | 是 |
> | virtualMachines | 是 | 是 |
> | virtualNetworks | 是 | 是 |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | 是 | 是 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 是 | 是 |
> | batchAccounts / certificates | 否 | 否 |
> | batchAccounts / pools | 否 | 否 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 否 | 否 |
> | billingAccounts / agreements | 否 | 否 |
> | billingAccounts / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles | 否 | 否 |
> | billingAccounts / billingProfiles / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingProfiles / billingSubscriptions | 否 | 否 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts / billingProfiles / customers | 否 | 否 |
> | billingAccounts / billingProfiles / instructions | 否 | 否 |
> | billingAccounts / billingProfiles / invoices | 否 | 否 |
> | billingAccounts / billingProfiles / invoices / pricesheet | 否 | 否 |
> | billingAccounts / billingProfiles / invoices / transactions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / products | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / transactions | 否 | 否 |
> | billingaccounts / billingprofiles / invoicesections / transfers | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | 否 | 否 |
> | billingaccounts / billingprofiles / patchoperations | 否 | 否 |
> | billingaccounts / billingprofiles / paymentmethods | 否 | 否 |
> | billingaccounts / billingprofiles / policies | 否 | 否 |
> | billingaccounts / billingprofiles / pricesheet | 否 | 否 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 否 | 否 |
> | billingaccounts / billingprofiles / products | 否 | 否 |
> | billingAccounts / billingProfiles / reservations | 否 | 否 |
> | billingaccounts / billingprofiles / transactions | 否 | 否 |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | 否 | 否 |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | 否 | 否 |
> | billingAccounts / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingSubscriptions | 否 | 否 |
> | billingAccounts / billingSubscriptions / elevateRole | 否 | 否 |
> | billingaccounts / billingsubscriptions / invoices | 否 | 否 |
> | billingAccounts / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts / createInvoiceSectionOperations | 否 | 否 |
> | billingAccounts / customers | 否 | 否 |
> | billingAccounts / customers / billingPermissions | 否 | 否 |
> | billingAccounts / customers / billingSubscriptions | 否 | 否 |
> | billingAccounts / customers / initiateTransfer | 否 | 否 |
> | billingAccounts / customers / policies | 否 | 否 |
> | billingAccounts / customers / products | 否 | 否 |
> | billingAccounts / customers / transactions | 否 | 否 |
> | billingAccounts / customers / transfers | 否 | 否 |
> | billingAccounts / departments | 否 | 否 |
> | billingAccounts / departments / billingPermissions | 否 | 否 |
> | billingAccounts / departments / billingRoleAssignments | 否 | 否 |
> | billingAccounts / departments / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / departments / billingSubscriptions | 否 | 否 |
> | billingAccounts / enrollmentAccounts | 否 | 否 |
> | billingAccounts / enrollmentAccounts / billingPermissions | 否 | 否 |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | 否 | 否 |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | 否 | 否 |
> | billingAccounts / invoices | 否 | 否 |
> | billingAccounts / invoices / transactions | 否 | 否 |
> | billingAccounts / invoices / transactionSummary | 否 | 否 |
> | billingAccounts / invoiceSections | 否 | 否 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 否 | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions | 否 | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | 否 | 否 |
> | billingAccounts / invoiceSections / elevate | 否 | 否 |
> | billingAccounts / invoiceSections / initiateTransfer | 否 | 否 |
> | billingAccounts / invoiceSections / patchOperations | 否 | 否 |
> | billingAccounts / invoiceSections / productMoveOperations | 否 | 否 |
> | billingAccounts / invoiceSections / products | 否 | 否 |
> | billingAccounts / invoiceSections / products / transfer | 否 | 否 |
> | billingAccounts / invoiceSections / products / updateAutoRenew | 否 | 否 |
> | billingAccounts / invoiceSections / transactions | 否 | 否 |
> | billingAccounts / invoiceSections / transfers | 否 | 否 |
> | billingAccounts / lineOfCredit | 否 | 否 |
> | billingAccounts / patchOperations | 否 | 否 |
> | billingAccounts / payableOverage | 否 | 否 |
> | billingAccounts / paymentMethods | 否 | 否 |
> | billingAccounts / payNow | 否 | 否 |
> | billingaccounts / products | 否 | 否 |
> | billingAccounts / reservations | 否 | 否 |
> | billingAccounts / transactions | 否 | 否 |
> | billingPeriods | 否 | 否 |
> | billingPermissions | 否 | 否 |
> | billingProperty | 否 | 否 |
> | billingRoleAssignments | 否 | 否 |
> | billingRoleDefinitions | 否 | 否 |
> | createBillingRoleAssignment | 否 | 否 |
> | departments | 否 | 否 |
> | enrollmentAccounts | 否 | 否 |
> | invoices | 否 | 否 |
> | 提升 | 否 | 否 |
> | transfers | 否 | 否 |
> | transfers / acceptTransfer | 否 | 否 |
> | transfers / declineTransfer | 否 | 否 |
> | transfers / operationStatus | 否 | 否 |
> | transfers / validateTransfer | 否 | 否 |
> | validateAddress | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | mapApis | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 是 | 是 |
> | cordaMembers | 是 | 是 |
> | watchers | 是 | 是 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | TokenServices | 是 | 是 |
> | TokenServices / BlockchainNetworks | 否 | 否 |
> | TokenServices / Groups | 否 | 否 |
> | TokenServices / Groups / Accounts | 否 | 否 |
> | TokenServices / TokenTemplates | 否 | 否 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 否 | 否 |
> | blueprintAssignments / assignmentOperations | 否 | 否 |
> | blueprintAssignments / operations | 否 | 否 |
> | blueprints | 否 | 否 |
> | blueprints / artifacts | 否 | 否 |
> | blueprints / versions | 否 | 否 |
> | blueprints / versions / artifacts | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | botServices | 是 | 是 |
> | botServices/channels | 否 | 否 |
> | botServices / connections | 否 | 否 |
> | hostSettings | 否 | 否 |
> | 语言 | 否 | 否 |
> | 模板 | 否 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | Redis | 是 | 是 |
> | Redis/EventGridFilters | 否 | 否 |
> | Redis/privateEndpointConnectionProxies | 否 | 否 |
> | Redis/privateEndpointConnectionProxies/validate | 否 | 否 |
> | Redis/privateEndpointConnections | 否 | 否 |
> | Redis/privateLinkResources | 否 | 否 |
> | redisEnterprise | 是 | 是 |
> | redisEnterprise / databases | 否 | 否 |
> | RedisEnterprise / privateEndpointConnectionProxies | 否 | 否 |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | 否 | 否 |
> | RedisEnterprise / privateEndpointConnections | 否 | 否 |
> | RedisEnterprise / privateLinkResources | 否 | 否 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 否 | 否 |
> | autoQuotaIncrease | 否 | 否 |
> | calculateExchange | 否 | 否 |
> | calculatePrice | 否 | 否 |
> | calculatePurchasePrice | 否 | 否 |
> | catalogs | 否 | 否 |
> | commercialReservationOrders | 否 | 否 |
> | 交易所 | 否 | 否 |
> | ownReservations | 否 | 否 |
> | placePurchaseOrder | 否 | 否 |
> | reservationOrders | 否 | 否 |
> | reservationOrders / calculateRefund | 否 | 否 |
> | reservationOrders / merge | 否 | 否 |
> | reservationOrders / reservations | 否 | 否 |
> | reservationOrders / reservations / revisions | 否 | 否 |
> | reservationOrders / return | 否 | 否 |
> | reservationOrders / split | 否 | 否 |
> | reservationOrders / swap | 否 | 否 |
> | reservations | 否 | 否 |
> | resourceProviders | 否 | 否 |
> | resources | 否 | 否 |
> | validateReservationOrder | 否 | 否 |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | sites | 是 | 是 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 | 否 |
> | CdnWebApplicationFirewallPolicies | 是 | 是 |
> | edgenodes | 否 | 否 |
> | 配置文件 | 是 | 是 |
> | profiles / afdendpoints | 是 | 是 |
> | profiles / afdendpoints / routes | 否 | 否 |
> | profiles / customdomains | 否 | 否 |
> | profiles/endpoints | 是 | 是 |
> | profiles / endpoints / customdomains | 否 | 否 |
> | profiles / endpoints / origingroups | 否 | 否 |
> | profiles / endpoints / origins | 否 | 否 |
> | profiles / origingroups | 否 | 否 |
> | profiles / origingroups / origins | 否 | 否 |
> | profiles / rulesets | 否 | 否 |
> | profiles / rulesets / rules | 否 | 否 |
> | profiles / secrets | 否 | 否 |
> | profiles / securitypolicies | 否 | 否 |
> | validateProbe | 否 | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 是 | 是 |
> | certificateOrders / certificates | 否 | 否 |
> | validateCertificateRegistrationInformation | 否 | 否 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 更改 | 否 | 否 |
> | 个人资料 | 否 | 否 |
> | resourceChanges | 否 | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | domainNames | 否 | 否 |
> | domainNames / capabilities | 否 | 否 |
> | domainNames / internalLoadBalancers | 否 | 否 |
> | domainNames / serviceCertificates | 否 | 否 |
> | domainNames / slots | 否 | 否 |
> | domainNames / slots / roles | 否 | 否 |
> | domainNames / slots / roles / metricDefinitions | 否 | 否 |
> | domainNames / slots / roles / metrics | 否 | 否 |
> | moveSubscriptionResources | 否 | 否 |
> | operatingSystemFamilies | 否 | 否 |
> | operatingSystems | 否 | 否 |
> | quotas | 否 | 否 |
> | resourceTypes | 否 | 否 |
> | validateSubscriptionMoveAvailability | 否 | 否 |
> | virtualMachines | 否 | 否 |
> | virtualMachines / diagnosticSettings | 否 | 否 |
> | virtualMachines / metricDefinitions | 否 | 否 |
> | virtualMachines / metrics | 否 | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 否 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | expressRouteCrossConnections | 否 | 否 |
> | expressRouteCrossConnections / peerings | 否 | 否 |
> | gatewaySupportedDevices | 否 | 否 |
> | networkSecurityGroups | 否 | 否 |
> | quotas | 否 | 否 |
> | reservedIps | 否 | 否 |
> | virtualNetworks | 否 | 否 |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | 否 | 否 |
> | virtualNetworks / virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | disks | 否 | 否 |
> | images | 否 | 否 |
> | osImages | 否 | 否 |
> | osPlatformImages | 否 | 否 |
> | publicImages | 否 | 否 |
> | quotas | 否 | 否 |
> | storageAccounts | 否 | 否 |
> | storageAccounts / blobServices | 否 | 否 |
> | storageAccounts / fileServices | 否 | 否 |
> | storageAccounts / metricDefinitions | 否 | 否 |
> | storageAccounts / metrics | 否 | 否 |
> | storageAccounts / queueServices | 否 | 否 |
> | storageAccounts / services | 否 | 否 |
> | storageAccounts / services / diagnosticSettings | 否 | 否 |
> | storageAccounts / services / metricDefinitions | 否 | 否 |
> | storageAccounts / services / metrics | 否 | 否 |
> | storageAccounts / tableServices | 否 | 否 |
> | storageAccounts / vmImages | 否 | 否 |
> | vmImages | 否 | 否 |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | nodes | 是 | 是 |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | plans | 是 | 否 |
> | registeredSubscriptions | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / privateEndpointConnectionProxies | 否 | 否 |
> | accounts / privateEndpointConnections | 否 | 否 |
> | accounts / privateLinkResources | 否 | 否 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | RateCard | 否 | 否 |
> | UsageAggregates | 否 | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 是 | 是 |
> | cloudServices | 是 | 是 |
> | cloudServices / networkInterfaces | 否 | 否 |
> | cloudServices / publicIPAddresses | 否 | 否 |
> | cloudServices / roleInstances | 否 | 否 |
> | cloudServices / roleInstances / networkInterfaces | 否 | 否 |
> | cloudServices / roles | 否 | 否 |
> | diskAccesses | 是 | 是 |
> | diskEncryptionSets | 是 | 是 |
> | disks | 是 | 是 |
> | galleries | 是 | 是 |
> | galleries / applications | 否 | 否 |
> | galleries / applications / versions | 否 | 否 |
> | galleries/images | 否 | 否 |
> | galleries/images/versions | 否 | 否 |
> | hostGroups | 是 | 是 |
> | hostGroups / hosts | 是 | 是 |
> | images | 是 | 是 |
> | proximityPlacementGroups | 是 | 是 |
> | restorePointCollections | 是 | 是 |
> | restorePointCollections / restorePoints | 否 | 否 |
> | sharedVMExtensions | 是 | 是 |
> | sharedVMExtensions / versions | 否 | 否 |
> | sharedVMImages | 是 | 是 |
> | sharedVMImages / versions | 否 | 否 |
> | snapshots | 是 | 是 |
> | sshPublicKeys | 是 | 是 |
> | virtualMachines | 是 | 是 |
> | virtualMachines / extensions | 是 | 是 |
> | virtualMachines / metricDefinitions | 否 | 否 |
> | virtualMachines/runCommands | 是 | 是 |
> | virtualMachineScaleSets | 是 | 是 |
> | virtualMachineScaleSets / extensions | 否 | 否 |
> | virtualMachineScaleSets / networkInterfaces | 否 | 否 |
> | virtualMachineScaleSets / publicIPAddresses | 是 | 否 |
> | virtualMachineScaleSets / virtualMachines | 否 | 否 |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | 否 | 否 |

> [!NOTE]
> 不能将标记添加到已标记为“通用化”的虚拟机。 使用 [Set-AzVm -Generalized](/powershell/module/Az.Compute/Set-AzVM) 或 [az vm generalize](/cli/azure/vm#az_vm_generalize) 将虚拟机标记为“通用化”。

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | CacheNodes | 是 | 是 |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | platformAccounts | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | ResourcePools | 是 | 是 |
> | VCenters | 是 | 是 |
> | VCenters / InventoryItems | 否 | 否 |
> | VirtualMachines | 是 | 是 |
> | VirtualMachines / Extensions | 是 | 是 |
> | VirtualMachines / GuestAgents | 否 | 否 |
> | VirtualMachines / HybridIdentityMetadata | 否 | 否 |
> | VirtualMachineTemplates | 是 | 是 |
> | VirtualNetworks | 是 | 是 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 否 | 否 |
> | 余额 | 否 | 否 |
> | 预算 | 否 | 否 |
> | Charges | 否 | 否 |
> | CostTags | 否 | 否 |
> | credits | 否 | 否 |
> | 活动 | 否 | 否 |
> | 预测 | 否 | 否 |
> | lots | 否 | 否 |
> | 市场 | 否 | 否 |
> | Pricesheets | 否 | 否 |
> | products | 否 | 否 |
> | ReservationDetails | 否 | 否 |
> | ReservationRecommendationDetails | 否 | 否 |
> | ReservationRecommendations | 否 | 否 |
> | ReservationSummaries | 否 | 否 |
> | ReservationTransactions | 否 | 否 |
> | 标记 | 否 | 否 |
> | tenants | 否 | 否 |
> | 术语 | 否 | 否 |
> | UsageDetails | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 是 | 是 |
> | serviceAssociationLinks | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | registries | 是 | 是 |
> | registries / agentPools | 是 | 是 |
> | registries / builds | 否 | 否 |
> | registries / builds / cancel | 否 | 否 |
> | registries / builds / getLogLink | 否 | 否 |
> | registries / buildTasks | 是 | 是 |
> | registries / buildTasks / steps | 否 | 否 |
> | registries / connectedRegistries | 否 | 否 |
> | registries / connectedRegistries / deactivate | 否 | 否 |
> | registries / eventGridFilters | 否 | 否 |
> | registries/exportPipelines | 否 | 否 |
> | registries / generateCredentials | 否 | 否 |
> | registries / getBuildSourceUploadUrl | 否 | 否 |
> | registries / GetCredentials | 否 | 否 |
> | registries / importImage | 否 | 否 |
> | registries/importPipelines | 否 | 否 |
> | registries/pipelineRuns | 否 | 否 |
> | registries / privateEndpointConnectionProxies | 否 | 否 |
> | registries / privateEndpointConnectionProxies / validate | 否 | 否 |
> | registries / privateEndpointConnections | 否 | 否 |
> | registries / privateLinkResources | 否 | 否 |
> | registries / queueBuild | 否 | 否 |
> | registries / regenerateCredential | 否 | 否 |
> | registries / regenerateCredentials | 否 | 否 |
> | registries/replications | 是 | 是 |
> | registries / runs | 否 | 否 |
> | registries / runs / cancel | 否 | 否 |
> | registries / scheduleRun | 否 | 否 |
> | registries / scopeMaps | 否 | 否 |
> | registries / taskRuns | 否 | 否 |
> | registries/tasks | 是 | 是 |
> | registries / tokens | 否 | 否 |
> | registries / updatePolicies | 否 | 否 |
> | registries/webhooks | 是 | 是 |
> | registries / webhooks / getCallbackConfig | 否 | 否 |
> | registries / webhooks / ping | 否 | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | containerServices | 是 | 是 |
> | managedClusters | 是 | 是 |
> | ManagedClusters / eventGridFilters | 否 | 否 |
> | openShiftManagedClusters | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 警报 | 否 | 否 |
> | BillingAccounts | 否 | 否 |
> | 预算 | 否 | 否 |
> | CloudConnectors | 否 | 否 |
> | 连接器 | 是 | 是 |
> | costAllocationRules | 否 | 否 |
> | Departments | 否 | 否 |
> | 维度 | 否 | 否 |
> | EnrollmentAccounts | 否 | 否 |
> | 导出 | 否 | 否 |
> | ExternalBillingAccounts | 否 | 否 |
> | ExternalBillingAccounts / Alerts | 否 | 否 |
> | ExternalBillingAccounts / Dimensions | 否 | 否 |
> | ExternalBillingAccounts / Forecast | 否 | 否 |
> | ExternalBillingAccounts / Query | 否 | 否 |
> | ExternalSubscriptions | 否 | 否 |
> | ExternalSubscriptions / Alerts | 否 | 否 |
> | ExternalSubscriptions / Dimensions | 否 | 否 |
> | ExternalSubscriptions / Forecast | 否 | 否 |
> | ExternalSubscriptions / Query | 否 | 否 |
> | fetchPrices | 否 | 否 |
> | 预测 | 否 | 否 |
> | GenerateDetailedCostReport | 否 | 否 |
> | GenerateReservationDetailsReport | 否 | 否 |
> | 洞察力 | 否 | 否 |
> | 查询 | 否 | 否 |
> | register | 否 | 否 |
> | Reportconfigs | 否 | 否 |
> | 报表 | 否 | 否 |
> | ScheduledActions | 否 | 否 |
> | 设置 | 否 | 否 |
> | showbackRules | 否 | 否 |
> | 视图 | 否 | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DisableLockbox | 否 | 否 |
> | EnableLockbox | 否 | 否 |
> | 请求 | 否 | 否 |
> | TenantOptedIn | 否 | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | associations | 否 | 否 |
> | resourceProviders | 是 | 是 |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | instances | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 是 | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 是 |
> | workspaces/dbWorkspaces | 否 | 否 |
> | workspaces/virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | catalogs | 是 | 是 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 是 | 是 |
> | dataFactories / diagnosticSettings | 否 | 否 |
> | dataFactories / metricDefinitions | 否 | 否 |
> | dataFactorySchema | 否 | 否 |
> | factories | 是 | 是 |
> | factories / integrationRuntimes | 否 | 否 |

> [!NOTE]
> 如果数据工厂中有 Azure-SSIS 集成运行时，其运行成本将使用数据工厂标记进行标记。 必须停止并重新开始运行 Azure-SSIS 集成运行时，才能将新的数据工厂标记应用于其运行成本。

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts/dataLakeStoreAccounts | 否 | 否 |
> | accounts/storageAccounts | 否 | 否 |
> | accounts / storageAccounts / containers | 否 | 否 |
> | accounts / transferAnalyticsUnits | 否 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / eventGridFilters | 否 | 否 |
> | accounts/firewallRules | 否 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations | 否 | 否 |
> | services | 是 | 是 |
> | services/projects | 是 | 是 |
> | SqlMigrationServices | 是 | 是 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | BackupVaults | 是 | 是 |
> | ResourceGuards | 是 | 是 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / shares | 否 | 否 |
> | accounts / shares / datasets | 否 | 否 |
> | accounts / shares / invitations | 否 | 否 |
> | accounts / shares / providersharesubscriptions | 否 | 否 |
> | accounts / shares / synchronizationSettings | 否 | 否 |
> | accounts / sharesubscriptions | 否 | 否 |
> | accounts / sharesubscriptions / consumerSourceDataSets | 否 | 否 |
> | accounts / sharesubscriptions / datasetmappings | 否 | 否 |
> | accounts / sharesubscriptions / triggers | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / resetQueryPerformanceInsightData | 否 | 否 |
> | servers / start | 否 | 否 |
> | servers / stop | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | flexibleServers | 是 | 是 |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / resetQueryPerformanceInsightData | 否 | 否 |
> | servers / start | 否 | 否 |
> | servers / stop | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / upgrade | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | flexibleServers | 是 | 是 |
> | serverGroups | 是 | 是 |
> | serverGroupsv2 | 是 | 是 |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / resetQueryPerformanceInsightData | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |
> | serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 是 | 是 |
> | rollouts | 是 | 是 |
> | serviceTopologies | 是 | 是 |
> | serviceTopologies / services | 是 | 是 |
> | serviceTopologies / services / serviceUnits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 是 | 是 |
> | applicationgroups / applications | 否 | 否 |
> | applicationgroups / desktops | 否 | 否 |
> | applicationgroups / startmenuitems | 否 | 否 |
> | hostpools | 是 | 是 |
> | hostpools / msixpackages | 否 | 否 |
> | hostpools / sessionhosts | 否 | 否 |
> | hostpools / sessionhosts / usersessions | 否 | 否 |
> | hostpools / usersessions | 否 | 否 |
> | scalingPlans | 是 | 是 |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 是 | 是 |
> | ElasticPools / IotHubTenants | 是 | 是 |
> | ElasticPools / IotHubTenants / securitySettings | 否 | 否 |
> | IotHubs | 是 | 是 |
> | IotHubs / eventGridFilters | 否 | 否 |
> | IotHubs / securitySettings | 否 | 否 |
> | ProvisioningServices | 是 | 是 |
> | usages | 否 | 否 |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / instances | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | pipelines | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 控制器 | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | labcenters | 是 | 是 |
> | labs | 是 | 是 |
> | labs / environments | 是 | 是 |
> | labs / serviceRunners | 是 | 是 |
> | labs / virtualMachines | 是 | 是 |
> | schedules | 是 | 是 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | 是 | 是 |
> | digitalTwinsInstances / endpoints | 否 | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | 是 | 是 |
> | databaseAccountNames | 否 | 否 |
> | databaseAccounts | 是 | 是 |
> | restorableDatabaseAccounts | 否 | 否 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | domains / domainOwnershipidentifiers | 否 | 否 |
> | generateSsoRequest | 否 | 否 |
> | topLevelDomains | 否 | 否 |
> | validateDomainRegistrationInformation | 否 | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 否 | 否 |
> | lcsprojects / clouddeployments | 否 | 否 |
> | lcsprojects / connectors | 否 | 否 |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 地址 | 是 | 是 |
> | orderCollections | 是 | 是 |
> | 订单 | 是 | 是 |
> | productFamiliesMetadata | 否 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | services | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | domains / topics | 否 | 否 |
> | eventSubscriptions | 否 | 否 |
> | extensionTopics | 否 | 否 |
> | partnerNamespaces | 是 | 是 |
> | partnerNamespaces / eventChannels | 否 | 否 |
> | partnerRegistrations | 是 | 是 |
> | partnerTopics | 是 | 是 |
> | partnerTopics / eventSubscriptions | 否 | 否 |
> | systemTopics | 是 | 是 |
> | systemTopics / eventSubscriptions | 否 | 否 |
> | topics | 是 | 是 |
> | topicTypes | 否 | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / eventhubs | 否 | 否 |
> | namespaces / eventhubs / authorizationrules | 否 | 否 |
> | namespaces / eventhubs / consumergroups | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |
> | namespaces / privateEndpointConnections | 否 | 否 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | 是 | 是 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 是 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | featureConfigurations | 否 | 否 |
> | featureProviderNamespaces | 否 | 否 |
> | featureProviders | 否 | 否 |
> | features | 否 | 否 |
> | providers | 否 | 否 |
> | subscriptionFeatureRegistrations | 否 | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | enroll | 否 | 否 |
> | galleryitems | 否 | 否 |
> | generateartifactaccessuri | 否 | 否 |
> | myareas | 否 | 否 |
> | myareas / areas | 否 | 否 |
> | myareas / areas / areas | 否 | 否 |
> | myareas / areas / areas / galleryitems | 否 | 否 |
> | myareas / areas / galleryitems | 否 | 否 |
> | myareas / galleryitems | 否 | 否 |
> | register | 否 | 否 |
> | resources | 否 | 否 |
> | retrieveresourcesbyid | 否 | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | 是 | 是 |
> | autoManagedVmConfigurationProfiles | 是 | 是 |
> | configurationProfileAssignments | 否 | 否 |
> | guestConfigurationAssignments | 否 | 否 |
> | software | 否 | 否 |
> | softwareUpdateProfile | 否 | 否 |
> | softwareUpdates | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hanainstances | 是 | 是 |
> | sapMonitors | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusterPools | 是 | 是 |
> | clusterPools / clusters | 是 | 是 |
> | clusters | 是 | 是 |
> | clusters/applications | 否 | 否 |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | healthBots | 是 | 是 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | services | 是 | 是 |
> | services / iomtconnectors | 否 | 否 |
> | services / iomtconnectors / connections | 否 | 否 |
> | services / iomtconnectors / mappings | 否 | 否 |
> | services / privateEndpointConnectionProxies | 否 | 否 |
> | services / privateEndpointConnections | 否 | 否 |
> | services / privateLinkResources | 否 | 否 |
> | workspaces | 是 | 是 |
> | workspaces / dicomservices | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | machines | 是 | 是 |
> | machines / assessPatches | 否 | 否 |
> | machines / extensions | 是 | 是 |
> | machines / installPatches | 否 | 否 |
> | machines / privateLinkScopes | 否 | 否 |
> | privateLinkScopes | 是 | 是 |
> | privateLinkScopes / privateEndpointConnectionProxies | 否 | 否 |
> | privateLinkScopes / privateEndpointConnections | 否 | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 是 | 是 |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 设备 | 是 | 是 |
> | networkfunctions | 是 | 是 |
> | networkFunctionVendors | 否 | 否 |
> | registeredSubscriptions | 否 | 否 |
> | 供应商 | 否 | 否 |
> | Vendors / vendorskus | 否 | 否 |
> | Vendors / vendorskus / previewsubscriptions | 否 | 否 |
> | virtualNetworkFunctions | 是 | 是 |
> | virtualNetworkFunctionVendors | 否 | 否 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | components | 是 | 是 |
> | networkScopes | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | actionGroups | 是 | 否 |
> | activityLogAlerts | 是 | 是 |
> | alertrules | 是 | 是 |
> | autoscalesettings | 是 | 是 |
> | components | 是 | 是 |
> | components / linkedStorageAccounts | 否 | 否 |
> | components / ProactiveDetectionConfigs | 否 | 否 |
> | diagnosticSettings | 否 | 否 |
> | guestDiagnosticSettings | 是 | 是 |
> | guestDiagnosticSettingsAssociation | 是 | 是 |
> | logprofiles | 是 | 是 |
> | metricAlerts | 是 | 是 |
> | privateLinkScopes | 是 | 是 |
> | privateLinkScopes / privateEndpointConnections | 否 | 否 |
> | privateLinkScopes / scopedResources | 否 | 否 |
> | queryPacks | 是 | 是 |
> | queryPacks / queries | 否 | 否 |
> | scheduledQueryRules | 是 | 是 |
> | webtests | 是 | 是 |
> | workbooks | 是 | 是 |
> | workbooktemplates | 是 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 否 | 否 |
> | IoTApps | 是 | 是 |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | defenderSettings | 否 | 否 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 图形 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | 否 | 否 |
> | deletedVaults | 否 | 否 |
> | hsmPools | 是 | 是 |
> | managedHSMs | 是 | 是 |
> | vaults | 是 | 是 |
> | vaults / accessPolicies | 否 | 否 |
> | vaults / eventGridFilters | 否 | 否 |
> | vaults / keys | 否 | 否 |
> | vaults / keys / versions | 否 | 否 |
> | vaults / secrets | 否 | 否 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | connectedClusters | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 扩展 | 否 | 否 |
> | sourceControlConfigurations | 否 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters / attacheddatabaseconfigurations | 否 | 否 |
> | clusters / databases | 否 | 否 |
> | clusters / databases / dataconnections | 否 | 否 |
> | clusters / databases / eventhubconnections | 否 | 否 |
> | clusters / databases / principalassignments | 否 | 否 |
> | clusters / databases / scripts | 否 | 否 |
> | clusters / dataconnections | 否 | 否 |
> | clusters / principalassignments | 否 | 否 |
> | clusters / sharedidentities | 否 | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 是 | 否 |
> | labplans | 是 | 是 |
> | labs | 是 | 是 |
> | users | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 是 | 是 |
> | integrationAccounts | 是 | 是 |
> | integrationServiceEnvironments | 是 | 是 |
> | integrationServiceEnvironments / managedApis | 否 | 否 |
> | isolatedEnvironments | 是 | 是 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 是 | 是 |
> | webServices | 是 | 是 |
> | 工作区 | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | modelinventories | 是 | 是 |
> | virtualclusters | 是 | 是 |
> | workspaces | 是 | 是 |
> | workspaces/batchEndpoints | 是 | 是 |
> | workspaces/batchEndpoints/deployments | 是 | 是 |
> | workspaces / batchEndpoints / deployments / jobs | 否 | 否 |
> | workspaces / batchEndpoints / jobs | 否 | 否 |
> | workspaces/codes | 否 | 否 |
> | workspaces/codes/versions | 否 | 否 |
> | workspaces / computes | 否 | 否 |
> | workspaces / data | 否 | 否 |
> | workspaces/datastores | 否 | 否 |
> | workspaces / environments | 否 | 否 |
> | workspaces / eventGridFilters | 否 | 否 |
> | workspaces/jobs | 否 | 否 |
> | workspaces/labelingJobs | 否 | 否 |
> | workspaces / linkedServices | 否 | 否 |
> | workspaces/models | 否 | 否 |
> | workspaces/models/versions | 否 | 否 |
> | workspaces/onlineEndpoints | 是 | 是 |
> | workspaces/onlineEndpoints/deployments | 是 | 是 |

> [!NOTE]
> 工作区标记不会传播到计算群集和计算实例。

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applyUpdates | 否 | 否 |
> | configurationAssignments | 否 | 否 |
> | maintenanceConfigurations | 是 | 是 |
> | publicMaintenanceConfigurations | 否 | 否 |
> | updates | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 标识 | 否 | 否 |
> | userAssignedIdentities | 是 | 是 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managedNetworks | 是 | 是 |
> | managedNetworks / managedNetworkGroups | 是 | 是 |
> | managedNetworks / managedNetworkPeeringPolicies | 是 | 是 |
> | 通知 | 是 | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 否 | 否 |
> | registrationAssignments | 否 | 否 |
> | registrationDefinitions | 否 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | getEntities | 否 | 否 |
> | managementGroups | 否 | 否 |
> | managementGroups / settings | 否 | 否 |
> | resources | 否 | 否 |
> | startTenantBackfill | 否 | 否 |
> | tenantBackfillStatus | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / creators | 是 | 是 |
> | accounts / eventGridFilters | 否 | 否 |
> | accounts / privateAtlases | 是 | 是 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | macc | 否 | 否 |
> | offers | 否 | 否 |
> | offerTypes | 否 | 否 |
> | offerTypes / publishers | 否 | 否 |
> | offerTypes / publishers / offers | 否 | 否 |
> | offerTypes / publishers / offers / plans | 否 | 否 |
> | offerTypes / publishers / offers / plans / agreements | 否 | 否 |
> | offerTypes / publishers / offers / plans / configs | 否 | 否 |
> | offerTypes / publishers / offers / plans / configs / importImage | 否 | 否 |
> | privategalleryitems | 否 | 否 |
> | privateStoreClient | 否 | 否 |
> | privateStores | 否 | 否 |
> | privateStores / AdminRequestApprovals | 否 | 否 |
> | privateStores / offers | 否 | 否 |
> | privateStores / offers / acknowledgeNotification | 否 | 否 |
> | privateStores / queryNotificationsState | 否 | 否 |
> | privateStores / RequestApprovals | 否 | 否 |
> | privateStores / requestApprovals / query | 否 | 否 |
> | privateStores / requestApprovals / withdrawPlan | 否 | 否 |
> | products | 否 | 否 |
> | publishers | 否 | 否 |
> | publishers / offers | 否 | 否 |
> | publishers / offers / amendments | 否 | 否 |
> | register | 否 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 协议 | 否 | 否 |
> | offertypes | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 是 | 是 |
> | mediaservices / accountFilters | 否 | 否 |
> | mediaservices / assets | 否 | 否 |
> | mediaservices / assets / assetFilters | 否 | 否 |
> | mediaservices / contentKeyPolicies | 否 | 否 |
> | mediaservices / eventGridFilters | 否 | 否 |
> | mediaservices / graphInstances | 否 | 否 |
> | mediaservices / graphTopologies | 否 | 否 |
> | mediaservices / liveEventOperations | 否 | 否 |
> | mediaservices / liveEvents | 是 | 是 |
> | mediaservices / liveEvents / liveOutputs | 否 | 否 |
> | mediaservices / liveOutputOperations | 否 | 否 |
> | mediaservices / mediaGraphs | 否 | 否 |
> | mediaservices / privateEndpointConnectionOperations | 否 | 否 |
> | mediaservices / privateEndpointConnectionProxies | 否 | 否 |
> | mediaservices / privateEndpointConnections | 否 | 否 |
> | mediaservices / streamingEndpointOperations | 否 | 否 |
> | mediaservices / streamingEndpoints | 是 | 是 |
> | mediaservices / streamingLocators | 否 | 否 |
> | mediaservices / streamingPolicies | 否 | 否 |
> | mediaservices / transforms | 否 | 否 |
> | mediaservices / transforms / jobs | 否 | 否 |
> | videoAnalyzers | 是 | 是 |
> | videoAnalyzers / edgeModules | 否 | 否 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appClusters | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | moveCollections | 是 | 是 |
> | projects | 是 | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 是 | 是 |
> | objectAnchorsAccounts | 是 | 是 |
> | objectUnderstandingAccounts | 是 | 是 |
> | remoteRenderingAccounts | 是 | 是 |
> | spatialAnchorsAccounts | 是 | 是 |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | networks | 是 | 是 |
> | networks / sites | 是 | 是 |
> | packetCores | 是 | 是 |
> | sims | 是 | 是 |
> | sims / simProfiles | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 是 | 否 |
> | netAppAccounts / accountBackups | 否 | 否 |
> | netAppAccounts / capacityPools | 是 | 是 |
> | netAppAccounts / capacityPools / volumes | 是 | 否 |
> | netAppAccounts / capacityPools / volumes / snapshots | 否 | 否 |
> | netAppAccounts / volumeGroups | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 是 | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 | 是 |
> | applicationSecurityGroups | 是 | 是 |
> | azureFirewallFqdnTags | 否 | 否 |
> | azureFirewalls | 是 | 否 |
> | bastionHosts | 是 | 否 |
> | bgpServiceCommunities | 否 | 否 |
> | connections | 是 | 是 |
> | ddosCustomPolicies | 是 | 是 |
> | ddosProtectionPlans | 是 | 是 |
> | dnsOperationStatuses | 否 | 否 |
> | dnszones | 是 | 是 |
> | dnszones / A | 否 | 否 |
> | dnszones / AAAA | 否 | 否 |
> | dnszones / all | 否 | 否 |
> | dnszones / CAA | 否 | 否 |
> | dnszones / CNAME | 否 | 否 |
> | dnszones / MX | 否 | 否 |
> | dnszones / NS | 否 | 否 |
> | dnszones / PTR | 否 | 否 |
> | dnszones / recordsets | 否 | 否 |
> | dnszones / SOA | 否 | 否 |
> | dnszones / SRV | 否 | 否 |
> | dnszones / TXT | 否 | 否 |
> | expressRouteCircuits | 是 | 是 |
> | expressRouteCrossConnections | 是 | 是 |
> | expressRouteGateways | 是 | 是 |
> | expressRoutePorts | 是 | 是 |
> | expressRouteServiceProviders | 否 | 否 |
> | firewallPolicies | 是 | 是 |
> | frontdoors | 是，但受限制（请参见[下面的注释](#frontdoor)） | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 是，但受限制（请参见[下面的注释](#frontdoor)） | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是，但受限制（请参见[下面的注释](#frontdoor)） | 是 |
> | getDnsResourceReference | 否 | 否 |
> | internalNotify | 否 | 否 |
> | ipGroups | 是 | 是 |
> | loadBalancers | 是 | 是 |
> | localNetworkGateways | 是 | 是 |
> | natGateways | 是 | 是 |
> | networkIntentPolicies | 是 | 是 |
> | networkInterfaces | 是 | 是 |
> | networkProfiles | 是 | 是 |
> | networkSecurityGroups | 是 | 是 |
> | networkWatchers | 是 | 是 |
> | networkWatchers / connectionMonitors | 是 | 否 |
> | networkWatchers / flowLogs | 是 | 否 |
> | networkWatchers / lenses | 是 | 否 |
> | networkWatchers / pingMeshes | 是 | 否 |
> | p2sVpnGateways | 是 | 是 |
> | privateDnsOperationStatuses | 否 | 否 |
> | privateDnsZones | 是 | 是 |
> | privateDnsZones / A | 否 | 否 |
> | privateDnsZones / AAAA | 否 | 否 |
> | privateDnsZones / all | 否 | 否 |
> | privateDnsZones / CNAME | 否 | 否 |
> | privateDnsZones / MX | 否 | 否 |
> | privateDnsZones / PTR | 否 | 否 |
> | privateDnsZones / SOA | 否 | 否 |
> | privateDnsZones / SRV | 否 | 否 |
> | privateDnsZones / TXT | 否 | 否 |
> | privateDnsZones / virtualNetworkLinks | 是 | 是 |
> | privateEndpoints | 是 | 是 |
> | privateLinkServices | 是 | 是 |
> | publicIPAddresses | 是 | 是 |
> | publicIPPrefixes | 是 | 是 |
> | routeFilters | 是 | 是 |
> | routeTables | 是 | 是 |
> | serviceEndpointPolicies | 是 | 是 |
> | trafficManagerGeographicHierarchies | 否 | 否 |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles/heatMaps | 否 | 否 |
> | trafficManagerUserMetricsKeys | 否 | 否 |
> | virtualHubs | 是 | 是 |
> | virtualNetworkGateways | 是 | 是 |
> | virtualNetworks | 是 | 是 |
> | virtualNetworks/subnets | 否 | 否 |
> | virtualNetworkTaps | 是 | 是 |
> | virtualWans | 是 | 否 |
> | vpnGateways | 是 | 是 |
> | vpnSites | 是 | 是 |
> | webApplicationFirewallPolicies | 是 | 是 |

<a id="frontdoor"></a>

> [!NOTE]
> 对于 Azure Front Door 服务，可以在创建资源时应用标记，但目前不支持更新或添加标记。


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | 否 | 否 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 否 |
> | namespaces / notificationHubs | 是 | 否 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | osNamespaces | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 是 | 是 |
> | ImportSites | 是 | 是 |
> | MasterSites | 是 | 是 |
> | ServerSites | 是 | 是 |
> | VMwareSites | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | deletedWorkspaces | 否 | 否 |
> | linkTargets | 否 | 否 |
> | querypacks | 是 | 是 |
> | storageInsightConfigs | 否 | 否 |
> | workspaces | 是 | 是 |
> | workspaces / dataExports | 否 | 否 |
> | workspaces / dataSources | 否 | 否 |
> | workspaces / linkedServices | 否 | 否 |
> | workspaces / linkedStorageAccounts | 否 | 否 |
> | workspaces / metadata | 否 | 否 |
> | workspaces / query | 否 | 否 |
> | workspaces / scopedPrivateLinkProxies | 否 | 否 |
> | workspaces / storageInsightConfigs | 否 | 否 |
> | workspaces / tables | 否 | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 视图 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | 否 | 否 |
> | legacyPeerings | 否 | 否 |
> | peerAsns | 否 | 否 |
> | peerings | 是 | 是 |
> | peeringServiceCountries | 否 | 否 |
> | peeringServiceProviders | 否 | 否 |
> | peeringServices | 是 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | attestations | 否 | 否 |
> | eventGridFilters | 否 | 否 |
> | policyEvents | 否 | 否 |
> | policyMetadata | 否 | 否 |
> | policyStates | 否 | 否 |
> | policyTrackedResources | 否 | 否 |
> | remediations | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | consoles | 否 | 否 |
> | dashboards | 是 | 是 |
> | tenantconfigurations | 否 | 否 |
> | userSettings | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | 是 | 是 |
> | tenants | 是 | 是 |
> | tenants / workspaces | 否 | 否 |
> | workspaceCollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores | 是 | 是 |
> | capacities | 是 | 是 |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | enterprisePolicies | 是 | 是 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | deletedAccounts | 否 | 否 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 否 | 否 |
> | providerRegistrations / customRollouts | 否 | 否 |
> | providerRegistrations / defaultRollouts | 否 | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 | 否 |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | deletedAccounts | 否 | 否 |
> | getDefaultAccount | 否 | 否 |
> | removeDefaultAccount | 否 | 否 |
> | setDefaultAccount | 否 | 否 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 工作区 | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 否 | 否 |
> | vaults | 是 | 是 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / hybridconnections | 否 | 否 |
> | namespaces / hybridconnections / authorizationrules | 否 | 否 |
> | namespaces / privateEndpointConnections | 否 | 否 |
> | namespaces / wcfrelays | 否 | 否 |
> | namespaces / wcfrelays / authorizationrules | 否 | 否 |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appliances | 是 | 是 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 查询 | 是 | 是 |
> | resourceChangeDetails | 否 | 否 |
> | resourceChanges | 否 | 否 |
> | resources | 否 | 否 |
> | resourcesHistory | 否 | 否 |
> | subscriptionsStatus | 否 | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 否 | 否 |
> | childAvailabilityStatuses | 否 | 否 |
> | childResources | 否 | 否 |
> | emergingissues | 否 | 否 |
> | 活动 | 否 | 否 |
> | impactedResources | 否 | 否 |
> | metadata | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | deployments | 是 | 否 |
> | deployments / operations | 否 | 否 |
> | deploymentScripts | 是 | 是 |
> | deploymentScripts / logs | 否 | 否 |
> | 链接 | 否 | 否 |
> | providers | 否 | 否 |
> | resourceGroups | 是 | 否 |
> | subscriptions | 是 | 否 |
> | templateSpecs | 是 | 是 |
> | templateSpecs / versions | 是 | 是 |
> | tenants | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applications | 是 | 是 |
> | resources | 是 | 是 |
> | saasresources | 否 | 否 |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clouds | 是 | 是 |
> | VirtualMachines | 是 | 是 |
> | VirtualMachineTemplates | 是 | 是 |
> | VirtualNetworks | 是 | 是 |
> | vmmservers | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 否 | 否 |
> | searchServices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 否 | 否 |
> | advancedThreatProtectionSettings | 否 | 否 |
> | alerts | 否 | 否 |
> | alertsSuppressionRules | 否 | 否 |
> | allowedConnections | 否 | 否 |
> | applicationWhitelistings | 否 | 否 |
> | assessmentMetadata | 否 | 否 |
> | assessments | 否 | 否 |
> | autoDismissAlertsRules | 否 | 否 |
> | automations | 是 | 是 |
> | AutoProvisioningSettings | 否 | 否 |
> | Compliances | 否 | 否 |
> | 连接器 | 否 | 否 |
> | dataCollectionAgents | 否 | 否 |
> | devices | 否 | 否 |
> | deviceSecurityGroups | 否 | 否 |
> | discoveredSecuritySolutions | 否 | 否 |
> | externalSecuritySolutions | 否 | 否 |
> | InformationProtectionPolicies | 否 | 否 |
> | ingestionSettings | 否 | 否 |
> | insights | 否 | 否 |
> | iotAlerts | 否 | 否 |
> | iotAlertTypes | 否 | 否 |
> | iotDefenderSettings | 否 | 否 |
> | iotRecommendations | 否 | 否 |
> | iotRecommendationTypes | 否 | 否 |
> | iotSecuritySolutions | 是 | 是 |
> | iotSecuritySolutions / analyticsModels | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 | 否 |
> | iotSecuritySolutions / iotAlerts | 否 | 否 |
> | iotSecuritySolutions / iotAlertTypes | 否 | 否 |
> | iotSecuritySolutions / iotRecommendations | 否 | 否 |
> | iotSecuritySolutions / iotRecommendationTypes | 否 | 否 |
> | iotSensors | 否 | 否 |
> | iotSites | 否 | 否 |
> | jitNetworkAccessPolicies | 否 | 否 |
> | jitPolicies | 否 | 否 |
> | onPremiseIotSensors | 否 | 否 |
> | 策略 | 否 | 否 |
> | pricings | 否 | 否 |
> | regulatoryComplianceStandards | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 | 否 |
> | secureScoreControlDefinitions | 否 | 否 |
> | secureScoreControls | 否 | 否 |
> | secureScores | 否 | 否 |
> | secureScores / secureScoreControls | 否 | 否 |
> | securityContacts | 否 | 否 |
> | securitySolutions | 否 | 否 |
> | securitySolutionsReferenceData | 否 | 否 |
> | securityStatuses | 否 | 否 |
> | securityStatusesSummaries | 否 | 否 |
> | serverVulnerabilityAssessments | 否 | 否 |
> | 设置 | 否 | 否 |
> | sqlVulnerabilityAssessments | 否 | 否 |
> | subAssessments | 否 | 否 |
> | 任务 | 否 | 否 |
> | topologies | 否 | 否 |
> | workspaceSettings | 否 | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | aggregations | 否 | 否 |
> | alertRules | 否 | 否 |
> | alertRuleTemplates | 否 | 否 |
> | automationRules | 否 | 否 |
> | bookmarks | 否 | 否 |
> | cases | 否 | 否 |
> | dataConnectors | 否 | 否 |
> | dataConnectorsCheckRequirements | 否 | 否 |
> | enrichment | 否 | 否 |
> | 实体 | 否 | 否 |
> | entityQueries | 否 | 否 |
> | entityQueryTemplates | 否 | 否 |
> | incidents | 否 | 否 |
> | officeConsents | 否 | 否 |
> | 设置 | 否 | 否 |
> | threatIntelligence | 否 | 否 |
> | watchlists | 否 | 否 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | consoleServices | 否 | 否 |
> | serialPorts | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / eventgridfilters | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |
> | namespaces / privateEndpointConnections | 否 | 否 |
> | namespaces / queues | 否 | 否 |
> | namespaces / queues / authorizationrules | 否 | 否 |
> | namespaces / topics | 否 | 否 |
> | namespaces / topics / authorizationrules | 否 | 否 |
> | namespaces / topics / subscriptions | 否 | 否 |
> | namespaces / topics / subscriptions / rules | 否 | 否 |
> | premiumMessagingRegions | 否 | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applications | 是 | 是 |
> | clusters | 是 | 是 |
> | clusters/applications | 否 | 否 |
> | containerGroups | 是 | 是 |
> | containerGroupSets | 是 | 是 |
> | edgeclusters | 是 | 是 |
> | edgeclusters / applications | 否 | 否 |
> | managedclusters | 是 | 是 |
> | managedclusters / applications | 否 | 否 |
> | managedclusters / applications / services | 否 | 否 |
> | managedclusters / applicationTypes | 否 | 否 |
> | managedclusters / applicationTypes / versions | 否 | 否 |
> | managedclusters / nodetypes | 否 | 否 |
> | networks | 是 | 是 |
> | secretstores | 是 | 是 |
> | secretstores / certificates | 否 | 否 |
> | secretstores / secrets | 否 | 否 |
> | volumes | 是 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applications | 是 | 是 |
> | containerGroups | 是 | 是 |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | 机密 | 是 | 是 |
> | volumes | 是 | 是 |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | linkers | 否 | 否 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 否 | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 | 否 |
> | rollouts | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | SignalR | 是 | 是 |
> | SignalR / eventGridFilters | 否 | 否 |
> | WebPubSub | 是 | 是 |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / accountQuotaPolicies | 否 | 否 |
> | accounts/groupPolicies | 否 | 否 |
> | accounts/jobs | 否 | 否 |
> | accounts/storageContainers | 否 | 否 |
> | images | 否 | 否 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 是 | 是 |
> | applications | 是 | 是 |
> | jitRequests | 是 | 是 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance / longtermRetentionDatabase / longtermRetentionBackup | 否 | 否 |
> | longtermRetentionServer / longtermRetentionDatabase / longtermRetentionBackup | 否 | 否 |
> | managedInstances | 是 | 是 |
> | managedInstances / databases | 否 | 否 |
> | managedInstances / databases / backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | 否 | 否 |
> | managedInstances / databases / vulnerabilityAssessments | 否 | 否 |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | 否 | 否 |
> | managedInstances / encryptionProtector | 否 | 否 |
> | managedInstances / keys | 否 | 否 |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances / vulnerabilityAssessments | 否 | 否 |
> | servers | 是 | 是 |
> | servers / administrators | 否 | 否 |
> | servers / communicationLinks | 否 | 否 |
> | servers/databases | 是（请参见[下面的注释](#sqlnote)） | 是 |
> | servers / encryptionProtector | 否 | 否 |
> | servers / firewallRules | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / restorableDroppedDatabases | 否 | 否 |
> | servers / serviceobjectives | 否 | 否 |
> | servers / tdeCertificates | 否 | 否 |
> | virtualClusters | 否 | 否 |

<a id="sqlnote"></a>

> [!NOTE]
> Master 数据库不支持标记，但其他数据库（包括 Azure Synapse Analytics 数据库）支持标记。 Azure Synapse Analytics 数据库必须处于活动（而非暂停）状态。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 是 | 是 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 否 | 否 |
> | SqlVirtualMachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | 否 | 否 |
> | storageAccounts | 是 | 是 |
> | storageAccounts / blobServices | 否 | 否 |
> | storageAccounts / fileServices | 否 | 否 |
> | storageAccounts / queueServices | 否 | 否 |
> | storageAccounts / services | 否 | 否 |
> | storageAccounts / services / metricDefinitions | 否 | 否 |
> | storageAccounts / tableServices | 否 | 否 |
> | usages | 否 | 否 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | 是 | 是 |
> | caches | 是 | 是 |
> | caches / storageTargets | 否 | 否 |
> | usageModels | 否 | 否 |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices/syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices / workflows | 否 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices/syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices / workflows | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices/syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices / workflows | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managers | 是 | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters/privateEndpoints | 否 | 否 |
> | streamingjobs | 是（见下方备注） | 是 |

> [!NOTE]
> Streamingjobs 运行时无法添加标记。 停止要添加标记的资源。

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | 否 | 否 |
> | acceptOwnership | 否 | 否 |
> | acceptOwnershipStatus | 否 | 否 |
> | aliases | 否 | 否 |
> | cancel | 否 | 否 |
> | changeTenantRequest | 否 | 否 |
> | changeTenantStatus | 否 | 否 |
> | CreateSubscription | 否 | 否 |
> | enable | 否 | 否 |
> | 策略 | 否 | 否 |
> | 重命名 | 否 | 否 |
> | SubscriptionDefinitions | 否 | 否 |
> | SubscriptionOperations | 否 | 否 |
> | subscriptions | 否 | 否 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | 是 | 是 |
> | workspaces | 是 | 是 |
> | workspaces/bigDataPools | 是 | 是 |
> | workspaces / operationStatuses | 否 | 否 |
> | workspaces / sqlDatabases | 是 | 是 |
> | workspaces / sqlPools | 是 | 是 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | environments | 是 | 否 |
> | environments / accessPolicies | 否 | 否 |
> | environments / eventsources | 是 | 否 |
> | environments / privateEndpointConnectionProxies | 否 | 否 |
> | environments / privateEndpointConnections | 否 | 否 |
> | environments / privateLinkResources | 否 | 否 |
> | environments / referenceDataSets | 是 | 否 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | stores | 是 | 是 |
> | stores / accessPolicies | 否 | 否 |
> | stores / services | 否 | 否 |
> | stores / services / tokens | 否 | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | imageTemplates | 是 | 是 |
> | imageTemplates / runOutputs | 否 | 否 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | ArcZones | 是 | 是 |
> | ResourcePools | 是 | 是 |
> | VCenters | 是 | 是 |
> | VCenters / InventoryItems | 否 | 否 |
> | virtualmachines | 是 | 是 |
> | VirtualMachineTemplates | 是 | 是 |
> | VirtualNetworks | 是 | 是 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 是 | 是 |
> | dedicatedCloudServices | 是 | 是 |
> | virtualMachines | 是 | 是 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 设备 | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |
> | vendors | 否 | 否 |
> | vendors / skus | 否 | 否 |
> | vendors / vnfs | 否 | 否 |
> | virtualNetworkFunctionSkus | 否 | 否 |
> | vnfs | 是 | 是 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 否 |
> | plans | 是 | 否 |
> | registeredSubscriptions | 否 | 否 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 否 | 否 |
> | apiManagementAccounts / apiAcls | 否 | 否 |
> | apiManagementAccounts / apis | 否 | 否 |
> | apiManagementAccounts / apis / apiAcls | 否 | 否 |
> | apiManagementAccounts / apis / connectionAcls | 否 | 否 |
> | apiManagementAccounts / apis / connections | 否 | 否 |
> | apiManagementAccounts / apis / connections / connectionAcls | 否 | 否 |
> | apiManagementAccounts / apis / localizedDefinitions | 否 | 否 |
> | apiManagementAccounts / connectionAcls | 否 | 否 |
> | apiManagementAccounts / connections | 否 | 否 |
> | billingMeters | 否 | 否 |
> | certificates | 是 | 是 |
> | connectionGateways | 是 | 是 |
> | connections | 是 | 是 |
> | customApis | 是 | 是 |
> | deletedSites | 否 | 否 |
> | functionAppStacks | 否 | 否 |
> | generateGithubAccessTokenForAppserviceCLI | 否 | 否 |
> | hostingEnvironments | 是 | 是 |
> | hostingEnvironments / eventGridFilters | 否 | 否 |
> | hostingEnvironments / multiRolePools | 否 | 否 |
> | hostingEnvironments / workerPools | 否 | 否 |
> | kubeEnvironments | 是 | 是 |
> | publishingUsers | 否 | 否 |
> | 建议 | 否 | 否 |
> | resourceHealthMetadata | 否 | 否 |
> | runtimes | 否 | 否 |
> | serverFarms | 是 | 是 |
> | serverFarms / eventGridFilters | 否 | 否 |
> | serverFarms / firstPartyApps | 否 | 否 |
> | serverFarms / firstPartyApps / keyVaultSettings | 否 | 否 |
> | sites | 是 | 是 |
> | sites / config  | 否 | 否 |
> | sites / eventGridFilters | 否 | 否 |
> | sites / hostNameBindings | 否 | 否 |
> | sites / networkConfig | 否 | 否 |
> | sites/premieraddons | 是 | 是 |
> | sites/slots | 是 | 是 |
> | sites / slots / eventGridFilters | 否 | 否 |
> | sites / slots / hostNameBindings | 否 | 否 |
> | sites / slots / networkConfig | 否 | 否 |
> | sourceControls | 否 | 否 |
> | staticSites | 是 | 是 |
> | validate | 否 | 否 |
> | verifyHostingEnvironmentVnet | 否 | 否 |
> | webAppStacks | 否 | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | 是 | 是 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 是 | 是 |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | migrationAgents | 是 | 是 |
> | 工作负荷 | 是 | 是 |
> | workloads / instances | 否 | 否 |
> | workloads / versions | 否 | 否 |
> | workloads / versions / artifacts | 否 | 否 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | monitors | 否 | 否 |

## <a name="next-steps"></a>后续步骤

若要了解如何将标记应用于资源，请参见[使用标记来组织 Azure 资源](tag-resources.md)。
