---
title: 支持的 Azure 资源管理器资源类型
description: 提供 Azure Resource Graph 和更改历史记录支持的 Azure 资源管理器资源类型的列表。
ms.date: 09/03/2021
ms.topic: reference
ms.custom: generated
ms.openlocfilehash: 9dfdd77d400dfa91ec26df23d323f8290dc97da0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536060"
---
# <a name="azure-resource-graph-table-and-resource-type-reference"></a>Azure Resource Graph 表格和资源类型参考

Azure Resource Graph 支持 [Azure 资源管理器](../../../azure-resource-manager/management/overview.md)的以下资源类型。 每项资源都是 Resource Graph 中表格的一部分 。

## <a name="advisorresources"></a>advisorresources

有关此表的示例查询，请参阅 [advisorresources 的 Resource Graph 示例查询](../samples/samples-by-table.md#advisorresources)。

- microsoft.advisor/configurations
- microsoft.advisor/recommendations
  - 示例查询：[从 Azure 顾问获取成本节约情况摘要](../samples/samples-by-category.md#get-cost-savings-summary-from-azure-advisor)
  - 示例查询：[列出未运行代理的最新发布版且已启用 Arc 的服务器](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version)
- microsoft.advisor/recommendations/suppressions
- microsoft.advisor/suppressions

## <a name="alertsmanagementresources"></a>alertsmanagementresources

- microsoft.alertsmanagement/alerts

## <a name="desktopvirtualizationresources"></a>desktopvirtualizationresources

- microsoft.desktopvirtualization/hostpools/sessionhosts

## <a name="extendedlocationresources"></a>extendedlocationresources

有关此表的示例查询，请参阅 [Resource Graph 的 extendedlocationresources 示例查询](../samples/samples-by-table.md#extendedlocationresources)。

- microsoft.extendedlocation/customlocations/enabledresourcetypes
  - 示例查询：[获取已启用 Azure Arc 的自定义位置支持的资源类型](../samples/samples-by-category.md#get-enabled-resource-types-for-azure-arc-enabled-custom-locations)
  - 示例查询：[列出支持 VMware 或 SCVMM 的已启用 Azure Arc 的自定义位置](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)

## <a name="guestconfigurationresources"></a>guestconfigurationresources

有关此表的示例查询，请参阅 [guestconfigurationresources 的 Resource Graph 示例查询](../samples/samples-by-table.md#guestconfigurationresources)。

- microsoft.guestconfiguration/guestconfigurationassignments
  - 示例查询：[统计来宾配置策略范围内的计算机](../samples/samples-by-category.md#count-machines-in-scope-of-guest-configuration-policies)
  - 示例查询：[不合规来宾配置分配计数](../samples/samples-by-category.md#count-of-non-compliant-guest-configuration-assignments)
  - 示例查询：[查找计算机不符合来宾配置分配的所有原因](../samples/samples-by-category.md#find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments)

## <a name="healthresources"></a>healthresources

有关此表的示例查询，请参阅[healthresources 的 Resource Graph 示例查询](../samples/samples-by-table.md#healthresources)。

- microsoft.resourcehealth/availabilitystatuses
  - 示例查询：[按可用性状态和订阅 ID 统计虚拟机](../samples/samples-by-category.md#count-of-virtual-machines-by-availability-state-and-subscription-id)
  - 示例查询：[按资源 ID 列出的虚拟机和关联的可用性状态的列表](../samples/samples-by-category.md#list-of-virtual-machines-and-associated-availability-states-by-resource-ids)
  - 示例查询：[按资源 ID 和资源组的可用性状态和电源状态列出的虚拟机列表](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - 示例查询：[资源 ID 不可用的虚拟机列表](../samples/samples-by-category.md#list-of-virtual-machines-that-are-not-available-by-resource-ids)

## <a name="iotsecurityresources"></a>iotsecurityresources

- microsoft.iotsecurity/locations/devicegroups/alerts
- microsoft.iotsecurity/locations/devicegroups/devices
- microsoft.iotsecurity/locations/devicegroups/recommendations
- microsoft.iotsecurity/onpremisesensors
- microsoft.iotsecurity/sensors
- microsoft.iotsecurity/sites

## <a name="kubernetesconfigurationresources"></a>kubernetesconfigurationresources

有关此表的示例查询，请参阅 [Resource Graph 的 kubernetesconfigurationresources 示例查询](../samples/samples-by-table.md#kubernetesconfigurationresources)。

- microsoft.kubernetesconfiguration/extensions
  - 示例查询：[列出所有具有 Azure Monitor 扩展的已启用 Azure Arc 的 Kubernetes 群集](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-with-azure-monitor-extension)
  - 示例查询：[列出所有没有 Azure Monitor 扩展的已启用 Azure Arc 的 Kubernetes 群集](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
- microsoft.kubernetesconfiguration/sourcecontrolconfigurations

## <a name="maintenanceresources"></a>maintenanceresources

- microsoft.maintenance/applyupdates
- microsoft.maintenance/configurationassignments
- microsoft.maintenance/updates

## <a name="patchassessmentresources"></a>patchassessmentresources

有关此表的示例查询，请参阅 [patchassessmentresources 的 Resource Graph 示例查询](../samples/samples-by-table.md#patchassessmentresources)。

- microsoft.compute/virtualmachines/patchassessmentresults
- microsoft.compute/virtualmachines/patchassessmentresults/softwarepatches
- microsoft.hybridcompute/machines/patchassessmentresults
- microsoft.hybridcompute/machines/patchassessmentresults/softwarepatches

## <a name="patchinstallationresources"></a>patchinstallationresources

- microsoft.compute/virtualmachines/patchinstallationresults
- microsoft.compute/virtualmachines/patchinstallationresults/softwarepatches
- microsoft.hybridcompute/machines/patchinstallationresults
- microsoft.hybridcompute/machines/patchinstallationresults/softwarepatches

## <a name="policyresources"></a>policyresources

有关此表的示例查询，请参阅 [policyresources 的 Resource Graph 示例查询](../samples/samples-by-table.md#policyresources)。

- microsoft.policyinsights/policystates
  - 示例查询：[策略分配的合规性](../samples/samples-by-category.md#compliance-by-policy-assignment)
  - 示例查询：[资源类型的合规性](../samples/samples-by-category.md#compliance-by-resource-type)
  - 示例查询：[列出所有不合规的资源](../samples/samples-by-category.md#list-all-non-compliant-resources)
  - 示例查询：[按状态汇总资源合规性](../samples/samples-by-category.md#summarize-resource-compliance-by-state)
  - 示例查询：[按每个位置的状态汇总资源合规性](../samples/samples-by-category.md#summarize-resource-compliance-by-state-per-location)

## <a name="recoveryservicesresources"></a>recoveryservicesresources

- microsoft.dataprotection/backupvaults/backupinstances
- microsoft.dataprotection/backupvaults/backupjobs
- microsoft.dataprotection/backupvaults/backuppolicies
- microsoft.recoveryservices/vaults/alerts
- Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems（备份项）
- microsoft.recoveryservices/vaults/backupjobs
- microsoft.recoveryservices/vaults/backuppolicies

## <a name="resourcecontainers"></a>resourcecontainers

有关此表的示例查询，请参阅 [resourcecontainers 的 Resource Graph 示例查询](../samples/samples-by-table.md#resourcecontainers)。

- microsoft.management/managementgroups
  - 示例查询：[每个管理组的订阅计数](../samples/samples-by-category.md#count-of-subscriptions-per-management-group)
  - 示例查询：[列出指定管理组的所有管理组上级](../samples/samples-by-category.md#list-all-management-group-ancestors-for-a-specified-management-group)
- microsoft.resources/subscriptions（订阅）
  - 示例查询：[每个管理组的订阅计数](../samples/samples-by-category.md#count-of-subscriptions-per-management-group)
  - 示例查询：[具有订阅名称的密钥保管库](../samples/samples-by-category.md#key-vaults-with-subscription-name)
  - 示例查询：[列出指定订阅的所有管理组上级](../samples/samples-by-category.md#list-all-management-group-ancestors-for-a-specified-subscription)
  - 示例查询：[列出指定管理组包含的所有订阅](../samples/samples-by-category.md#list-all-subscriptions-under-a-specified-management-group)
  - 示例查询：[从结果中删除列](../samples/samples-by-category.md#remove-columns-from-results)
  - 示例查询：[每个管理组的安全功能分数](../samples/samples-by-category.md#secure-score-per-management-group)
- Microsoft.Resources/subscriptions/resourceGroups（资源组）
  - 示例查询：[在资源组上查找具有不区分大小写的特定标记的存储帐户](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - 示例查询：[在资源组上查找具有区分大小写的特定标记的存储帐户](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)

## <a name="resources"></a>resources

有关此表的示例查询，请参阅 [Resource Graph 的 resources 示例查询](../samples/samples-by-table.md#resources)。

- 84codes.CloudAMQP/servers (CloudAMQP)
- Citrix.Services/XenAppEssentials (Citrix Virtual Apps Essentials)
- Citrix.Services/XenDesktopEssentials (Citrix Virtual Desktops Essentials)
- conexlink.mycloudit/accounts
- crypteron.datasecurity/apps
- dynatrace.observability/monitors
- GitHub.Enterprise/accounts (GitHub AE)
- gridpro.evops/accounts
- gridpro.evops/accounts/eventrules
- gridpro.evops/accounts/requesttemplates
- gridpro.evops/accounts/views
- hive.streaming/services
- incapsula.waf/accounts
- LiveArena.Broadcast/services（LiveArena 广播）
- Mailjet.Email/services（Mailjet 电子邮件服务）
- Microsoft.AAD/domainServices（Azure AD 域服务）
- microsoft.aadiam/azureadmetrics
- microsoft.aadiam/privateLinkForAzureAD（Azure AD 专用链接）
- microsoft.aadiam/tenants
- Microsoft.AgFoodPlatform/farmBeats (Azure FarmBeats)
- microsoft.aisupercomputer/accounts
- microsoft.aisupercomputer/accounts/jobgroups
- microsoft.aisupercomputer/accounts/jobgroups/jobs
- microsoft.alertsmanagement/actionrules
- microsoft.alertsmanagement/resourcehealthalertrules
- microsoft.alertsmanagement/smartdetectoralertrules
- Microsoft.AnalysisServices/servers (Analysis Services)
- Microsoft.AnyBuild/clusters（AnyBuild 群集）
- Microsoft.ApiManagement/service（API 管理服务）
- microsoft.appassessment/migrateprojects
- Microsoft.AppConfiguration/configurationStores（应用配置）
- Microsoft.AppPlatform/Spring (Azure Spring Cloud)
- microsoft.archive/collections
- Microsoft.Attestation/attestationProviders（证明提供程序）
- Microsoft.Authorization/resourceManagementPrivateLinks（资源管理专用链接）
- microsoft.automanage/accounts
- microsoft.automanage/configurationprofilepreferences
- microsoft.automanage/configurationprofiles
- Microsoft.Automation/AutomationAccounts（自动化帐户）
- microsoft.automation/automationaccounts/configurations
- Microsoft.Automation/automationAccounts/runbooks (Runbook)
- microsoft.autonomousdevelopmentplatform/accounts
- Microsoft.AutonomousSystems/workspaces (Bonsai)
- Microsoft.AVS/privateClouds（AVS 私有云）
- microsoft.azconfig/configurationstores
- Microsoft.AzureActiveDirectory/b2cDirectories（B2C 租户）
- Microsoft.AzureActiveDirectory/guestUsages（来宾使用情况）
- Microsoft.AzureArcData/dataControllers（Azure Arc 数据控制器）
- Microsoft.AzureArcData/postgresInstances（已启用 Azure Arc 的超大规模 PostgreSQL 服务器组）
- Microsoft.AzureArcData/sqlManagedInstances（SQL 托管实例 - Azure Arc）
- Microsoft.AzureArcData/sqlServerInstances (SQL Server - Azure Arc)
- microsoft.azurecis/autopilotenvironments
- microsoft.azuredata/datacontrollers
- microsoft.azuredata/hybriddatamanagers
- microsoft.azuredata/postgresinstances
- microsoft.azuredata/sqlbigdataclusters
- microsoft.azuredata/sqlinstances
- microsoft.azuredata/sqlmanagedinstances
- microsoft.azuredata/sqlserverinstances
- Microsoft.AzureData/sqlServerRegistrations（SQL Server 注册表）
- Microsoft.AzurePercept/accounts（Azure Percept 帐户）
- microsoft.azuresphere/catalogs
- microsoft.azuresphere/catalogs/products
- microsoft.azuresphere/catalogs/products/devicegroups
- microsoft.azurestack/edgesubscriptions
- microsoft.azurestack/linkedsubscriptions
- Microsoft.Azurestack/registrations (Azure Stack Hub)
- Microsoft.AzureStackHCI/clusters (Azure Stack HCI)
- microsoft.azurestackhci/galleryimages
- microsoft.azurestackhci/networkinterfaces
- microsoft.azurestackhci/virtualharddisks
- Microsoft.AzureStackHci/virtualMachines（Azure Stack HCI 虚拟机 - Azure Arc）
- microsoft.azurestackhci/virtualnetworks
- microsoft.backupsolutions/vmwareapplications
- microsoft.baremetal/consoleconnections
- Microsoft.BareMetal/crayServers（Cray 服务器）
- Microsoft.BareMetal/monitoringServers（监视服务器）
- Microsoft.BareMetalInfrastructure/bareMetalInstances（裸机实例）
- Microsoft.Batch/batchAccounts（Batch 帐户）
- microsoft.batchai/clusters
- microsoft.batchai/fileservers
- microsoft.batchai/jobs
- microsoft.batchai/workspaces
- Microsoft.Bing/accounts（必应资源）
- microsoft.bingmaps/mapapis
- microsoft.biztalkservices/biztalk
- Microsoft.Blockchain/blockchainMembers（Azure 区块链服务）
- Microsoft.Blockchain/cordaMembers (Corda)
- Microsoft.Blockchain/watchers（区块链数据管理器）
- Microsoft.BotService/botServices（机器人服务）
- Microsoft.Cache/Redis (Azure Cache for Redis)
- Microsoft.Cache/RedisEnterprise (Redis Enterprise)
- microsoft.cascade/sites
- Microsoft.Cdn/CdnWebApplicationFirewallPolicies（Web 应用程序防火墙策略 (WAF)）
- microsoft.cdn/profiles（Front Door 标准/高级（预览版））
- Microsoft.Cdn/Profiles/AfdEndpoints（终结点）
- microsoft.cdn/profiles/endpoints（终结点）
- Microsoft.CertificateRegistration/certificateOrders（应用服务证书）
- microsoft.chaos/chaosexperiments（混沌试验）
- microsoft.chaos/experiments
- microsoft.classicCompute/domainNames（云服务[经典]）
- Microsoft.ClassicCompute/VirtualMachines（虚拟机[经典]）
- Microsoft.ClassicNetwork/networkSecurityGroups（网络安全组[经典]）
- Microsoft.ClassicNetwork/reservedIps（保留 IP 地址[经典]）
- Microsoft.ClassicNetwork/virtualNetworks（虚拟网络[经典]）
- Microsoft.ClassicStorage/StorageAccounts（存储帐户[经典]）
- microsoft.cloudes/accounts
- microsoft.cloudsearch/indexes
- Microsoft.CloudTest/accounts（CloudTest 帐户）
- Microsoft.CloudTest/hostedpools（1ES 托管池）
- Microsoft.CloudTest/images（CloudTest 映像）
- Microsoft.CloudTest/pools（CloudTest 池）
- Microsoft.ClusterStor/nodes (ClusterStors)
- microsoft.codesigning/codesigningaccounts
- microsoft.codespaces/plans
- Microsoft.Cognition/syntheticsAccounts（Synthetics 帐户）
- Microsoft.CognitiveServices/accounts（认知服务）
- Microsoft.Compute/availabilitySets （可用性集）
- Microsoft.Compute/capacityReservationGroups（产能预留组）
- microsoft.compute/capacityreservationgroups/capacityreservations
- microsoft.compute/capacityreservations
- Microsoft.Compute/cloudServices（云服务[外延支持]）
- Microsoft.Compute/diskAccesses（磁盘访问）
- Microsoft.Compute/diskEncryptionSets（磁盘加密集）
- Microsoft.Compute/disks（磁盘）
- Microsoft.Compute/galleries（共享映像库）
- Microsoft.Compute/galleries/applications（Gallery 应用程序）
- Microsoft.Compute/galleries/applications/versions（Gallery 应用程序版本）
- Microsoft.Compute/galleries/images（映像定义）
- Microsoft.Compute/galleries/images/versions（映像版本）
- Microsoft.Compute/hostgroups（主机组）
- Microsoft.Compute/hostgroups/hosts（主机）
- Microsoft.Compute/images（映像）
- Microsoft.Compute/ProximityPlacementGroups（邻近放置组）
- Microsoft.Compute/restorePointCollections（还原点集合）
- microsoft.compute/sharedvmextensions
- microsoft.compute/sharedvmextensions/versions
- microsoft.compute/sharedvmimages
- microsoft.compute/sharedvmimages/versions
- Microsoft.Compute/snapshots（快照）
- Microsoft.Compute/sshPublicKeys（SSH 密钥）
- microsoft.compute/swiftlets
- Microsoft.Compute/VirtualMachines（虚拟机）
  - 示例查询：[按电源状态统计虚拟机](../samples/samples-by-category.md#count-of-virtual-machines-by-power-state)
  - 示例查询：[按 OS 类型统计虚拟机](../samples/samples-by-category.md#count-virtual-machines-by-os-type)
  - 示例查询：[按 OS 类型统计具有扩展的虚拟机](../samples/samples-by-category.md#count-virtual-machines-by-os-type-with-extend)
  - 示例查询：[列出安装在虚拟机上的所有扩展](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
  - 示例查询：[列出未运行的机器和上一个合规性状态](../samples/samples-by-category.md#list-machines-that-are-not-running-and-the-last-compliance-status)
  - 示例查询：[按资源 ID 和资源组的可用性状态和电源状态列出的虚拟机列表](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - 示例查询：[列出虚拟机及其网络接口和公共 IP](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
  - 示例查询：[按降序显示按名称排序的所有虚拟机](../samples/samples-by-category.md#show-all-virtual-machines-ordered-by-name-in-descending-order)
  - 示例查询：[按名称及其 OS 类型显示前五个虚拟机](../samples/samples-by-category.md#show-first-five-virtual-machines-by-name-and-their-os-type)
  - 示例查询：[按电源状态扩展属性汇总虚拟机](../samples/samples-by-category.md#summarize-virtual-machine-by-the-power-states-extended-property)
  - 示例查询：[正则表达式匹配的虚拟机](../samples/samples-by-category.md#virtual-machines-matched-by-regex)
- microsoft.compute/virtualmachines/extensions
  - 示例查询：[列出安装在虚拟机上的所有扩展](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
- microsoft.compute/virtualmachines/runcommands
- Microsoft.Compute/virtualMachineScaleSets（虚拟机规模集）
  - 示例查询：[获取虚拟机规模集容量和大小](../samples/samples-by-category.md#get-virtual-machine-scale-set-capacity-and-size)
- Microsoft.ConfidentialLedger/ledgers（机密账本）
- Microsoft.Confluent/organizations（Confluent 组织）
- Microsoft.ConnectedCache/cacheNodes（联网缓存资源）
- Microsoft.ConnectedVehicle/platformAccounts（联网车载平台）
- microsoft.connectedvmwarevsphere/clusters
- microsoft.connectedvmwarevsphere/datastores
- microsoft.connectedvmwarevsphere/resourcepools
- Microsoft.connectedVMwareVSphere/vCenters (VMware vCenter)
- Microsoft.ConnectedVMwarevSphere/VirtualMachines（VMware + AVS 虚拟机）
- microsoft.connectedvmwarevsphere/virtualmachines/extensions
- microsoft.connectedvmwarevsphere/virtualmachinetemplates
- microsoft.connectedvmwarevsphere/virtualnetworks
- Microsoft.ContainerInstance/containerGroups（容器实例）
- Microsoft.ContainerRegistry/registries（容器注册表）
- microsoft.containerregistry/registries/agentpools
- microsoft.containerregistry/registries/buildtasks
- Microsoft.ContainerRegistry/registries/replications（容器注册表复制）
- microsoft.containerregistry/registries/taskruns
- microsoft.containerregistry/registries/tasks
- Microsoft.ContainerRegistry/registries/webhooks（容器注册表 Webhook）
- microsoft.containerservice/containerservices
- Microsoft.ContainerService/managedClusters（Kubernetes 服务）
  - 示例查询：[列出转移 Azure 订阅时受影响的资源](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.containerservice/openshiftmanagedclusters
- microsoft.contoso/clusters
- microsoft.contoso/employees
- microsoft.contoso/installations
- microsoft.contoso/towers
- microsoft.costmanagement/connectors
- microsoft.customproviders/resourceproviders
- microsoft.d365customerinsights/instances
- Microsoft.Dashboard/grafana（Grafana 工作区）
- Microsoft.DataBox/jobs (Azure Data Box)
- Microsoft.DataBoxEdge/dataBoxEdgeDevices (Azure Stack Edge / Data Box Gateway)
- Microsoft.Databricks/workspaces（Azure Databricks 服务）
- Microsoft.DataCatalog/catalogs（数据目录）
- microsoft.datacatalog/datacatalogs
- Microsoft.DataCollaboration/workspaces（项目 CI）
- Microsoft.Datadog/monitors (Datadog)
- Microsoft.DataFactory/dataFactories（数据工厂）
- Microsoft.DataFactory/factories（数据工厂 (V2)）
- Microsoft.DataLakeAnalytics/accounts (Data Lake Analytics)
- Microsoft.DataLakeStore/accounts (Data Lake Storage Gen1)
  - 示例查询：[列出转移 Azure 订阅时受影响的资源](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.datamigration/controllers
- Microsoft.DataMigration/services（Azure 数据库迁移服务）
- Microsoft.DataMigration/services/projects（Azure 数据库迁移项目）
- microsoft.datamigration/slots
- microsoft.datamigration/sqlmigrationservices
- Microsoft.DataProtection/BackupVaults（备份保管库）
- Microsoft.DataProtection/resourceGuards（Resource Guards（预览版））
- microsoft.dataprotection/resourceoperationgatekeepers
- microsoft.datareplication/replicationfabrics
- microsoft.datareplication/replicationvaults
- Microsoft.DataShare/accounts（数据共享）
- Microsoft.DBforMariaDB/servers（Azure Database for MariaDB 服务器）
- Microsoft.DBforMySQL/flexibleServers（Azure Database for MySQL 灵活服务器）
- Microsoft.DBforMySQL/servers（Azure Database for MySQL 服务器）
- Microsoft.DBforPostgreSQL/flexibleServers（Azure Database for PostgreSQL 灵活服务器）
- Microsoft.DBforPostgreSQL/serverGroups（Azure Database for PostgreSQL 服务器组）
- Microsoft.DBforPostgreSQL/serverGroupsv2（Azure Database for PostgreSQL 服务器组）
- Microsoft.DBforPostgreSQL/servers（Azure Database for PostgreSQL 服务器）
- Microsoft.DBforPostgreSQL/serversv2（Azure Database for PostgreSQL 服务器 v2）
- microsoft.dbforpostgresql/singleservers
- microsoft.delegatednetwork/controller
- microsoft.delegatednetwork/delegatedsubnets
- microsoft.delegatednetwork/orchestratorinstances
- microsoft.delegatednetwork/orchestrators
- microsoft.deploymentmanager/artifactsources
- Microsoft.DeploymentManager/Rollouts（推出）
- microsoft.deploymentmanager/servicetopologies
- microsoft.deploymentmanager/servicetopologies/services
- microsoft.deploymentmanager/servicetopologies/services/serviceunits
- microsoft.deploymentmanager/steps
- Microsoft.DesktopVirtualization/ApplicationGroups（应用程序组）
- Microsoft.DesktopVirtualization/HostPools（主机池）
- Microsoft.DesktopVirtualization/ScalingPlans（缩放计划）
- Microsoft.DesktopVirtualization/Workspaces（工作区）
- microsoft.devices/elasticpools
- microsoft.devices/elasticpools/iothubtenants
- Microsoft.Devices/IotHubs（IoT 中心）
- Microsoft.Devices/ProvisioningServices（设备预配服务）
- Microsoft.DeviceUpdate/Accounts（IoT 中心设备更新）
- microsoft.deviceupdate/accounts/instances
- microsoft.devops/pipelines（DevOps 初学者）
- microsoft.devspaces/controllers
- microsoft.devtestlab/labcenters
- Microsoft.DevTestLab/labs（开发测试实验室）
- microsoft.devtestlab/labs/servicerunners
- Microsoft.DevTestLab/labs/virtualMachines（虚拟机）
- microsoft.devtestlab/schedules
- Microsoft.DigitalTwins/digitalTwinsInstances（Azure 数字孪生）
- Microsoft.DocumentDB/cassandraClusters (Azure Managed Instance for Apache Cassandra)
- Microsoft.DocumentDb/databaseAccounts（Azure Cosmos DB 帐户）
  - 示例查询：[列出具有特定写入位置的 Cosmos DB](../samples/samples-by-category.md#list-azure-cosmos-db-with-specific-write-locations)
- Microsoft.DomainRegistration/domains（应用服务域）
- microsoft.dynamics365fraudprotection/instances
- Microsoft.EdgeOrder/addresses（Azure Edge Hardware Center 地址）
- microsoft.edgeorder/ordercollections
- Microsoft.EdgeOrder/orderItems (Azure Edge Hardware Center)
- microsoft.edgeorder/orders
- Microsoft.Elastic/monitors (Elasticsearch (Elastic Cloud))
- microsoft.enterpriseknowledgegraph/services
- Microsoft.EventGrid/domains（事件网格域）
- Microsoft.EventGrid/partnerNamespaces（事件网格合作伙伴命名空间）
- Microsoft.EventGrid/partnerRegistrations（事件网格合作伙伴注册）
- Microsoft.EventGrid/partnerTopics（事件网格合作伙伴主题）
- Microsoft.EventGrid/systemTopics（事件网格系统主题）
- Microsoft.EventGrid/topics（事件网格主题）
- Microsoft.EventHub/clusters（事件中心群集）
- Microsoft.EventHub/namespaces（事件中心命名空间）
- Microsoft.Experimentation/experimentWorkspaces（试验工作区）
- Microsoft.ExtendedLocation/CustomLocations（自定义位置）
  - 示例查询：[列出支持 VMware 或 SCVMM 的已启用 Azure Arc 的自定义位置](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)
- microsoft.falcon/namespaces
- Microsoft.Fidalgo/devcenters (Fidalgo DevCenters)
- microsoft.fidalgo/machinedefinitions
- microsoft.fidalgo/networksettings
- Microsoft.Fidalgo/projects（Fidalgo 项目）
- Microsoft.Fidalgo/projects/environments（Fidalgo 环境）
- Microsoft.FluidRelay/fluidRelayServers (Fluid Relay)
- microsoft.footprintmonitoring/profiles
- microsoft.gaming/titles
- Microsoft.Genomics/accounts（基因组学帐户）
- microsoft.guestconfiguration/automanagedaccounts
- Microsoft.HanaOnAzure/hanaInstances（Azure 上的 SAP HANA）
- Microsoft.HanaOnAzure/sapMonitors（适用于 SAP 的 Azure Monitor 解决方案）
- microsoft.hardwaresecuritymodules/dedicatedhsms
- Microsoft.HDInsight/clusterpools（HDInsight 群集池）
- Microsoft.HDInsight/clusterpools/clusters（HDInsight gen2 群集）
- microsoft.hdinsight/clusterpools/clusters/sessionclusters
- Microsoft.HDInsight/clusters（HDInsight 群集）
- Microsoft.HealthBot/healthBots (Azure Health Bot)
- Microsoft.HealthcareApis/services (Azure API for FHIR)
- microsoft.healthcareapis/services/privateendpointconnections
- Microsoft.HealthcareApis/workspaces（Healthcare APIs 工作区）
- Microsoft.HealthcareApis/workspaces/dicomservices（DICOM 服务）
- Microsoft.HealthcareApis/workspaces/fhirservices（FHIR 服务）
- Microsoft.HealthcareApis/workspaces/iotconnectors（IoT 连接器）
- Microsoft.HpcWorkbench/instances（HPC Workbench（预览版））
- Microsoft.HybridCompute/machines（服务器 - Azure Arc）
  - 示例查询：[按域获取已启用 Arc 的服务器的计数和百分比](../samples/samples-by-category.md#get-count-and-percentage-of-arc-enabled-servers-by-domain)
  - 示例查询：[列出已启用 Azure Arc 的服务器上安装的所有扩展](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
  - 示例查询：[列出未运行代理的最新发布版且已启用 Arc 的服务器](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version)
- microsoft.hybridcompute/machines/extensions
  - 示例查询：[列出已启用 Azure Arc 的服务器上安装的所有扩展](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
- Microsoft.HybridCompute/privateLinkScopes（Azure Arc 专用链接范围）
- Microsoft.HybridData/dataManagers（StorSimple 数据管理器）
- Microsoft.HybridNetwork/devices（Azure 网络功能管理器 - 设备（预览版））
- Microsoft.HybridNetwork/networkFunctions（Azure 网络功能管理器 - 网络功能（预览版））
- microsoft.hybridnetwork/virtualnetworkfunctions
- Microsoft.ImportExport/jobs（导入/导出作业）
- microsoft.industrydatalifecycle/basemodels
- microsoft.industrydatalifecycle/custodiancollaboratives
- microsoft.industrydatalifecycle/dataconsumercollaboratives
- microsoft.industrydatalifecycle/derivedmodels
- microsoft.industrydatalifecycle/membercollaboratives
- microsoft.industrydatalifecycle/modelmappings
- microsoft.industrydatalifecycle/pipelinesets
- microsoft.insights/actiongroups
- microsoft.insights/activitylogalerts
- microsoft.insights/alertrules
- microsoft.insights/autoscalesettings
- microsoft.insights/components (Application Insights)
- microsoft.insights/datacollectionendpoints（数据收集终结点）
- microsoft.insights/datacollectionrules（数据收集规则）
- microsoft.insights/guestdiagnosticsettings
- microsoft.insights/metricalerts
- microsoft.insights/notificationgroups
- microsoft.insights/notificationrules
- Microsoft.Insights/privateLinkScopes（Azure Monitor 专用链接范围）
- microsoft.insights/querypacks
- microsoft.insights/scheduledqueryrules
- microsoft.insights/webtests（可用性测试）
- microsoft.insights/workbooks（Azure 工作簿）
- microsoft.insights/workbooktemplates（Azure 工作簿模板）
- Microsoft.IntelligentITDigitalTwin/digitalTwins (Minervas)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/assets（资产）
- Microsoft.IntelligentITDigitalTwin/digitalTwins/executionPlans（部署）
- Microsoft.IntelligentITDigitalTwin/digitalTwins/testPlans（套件）
- Microsoft.IntelligentITDigitalTwin/digitalTwins/tests（脚本）
- Microsoft.IoTCentral/IoTApps（IoT Central 应用程序）
- microsoft.iotspaces/graph
- microsoft.keyvault/hsmpools
- microsoft.keyvault/managedhsms
- Microsoft.KeyVault/vaults（密钥保管库）
  - 示例查询：[统计密钥保管库资源](../samples/samples-by-category.md#count-key-vault-resources)
  - 示例查询：[具有订阅名称的密钥保管库](../samples/samples-by-category.md#key-vaults-with-subscription-name)
  - 示例查询：[列出转移 Azure 订阅时受影响的资源](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- Microsoft.Kubernetes/connectedClusters (Kubernetes - Azure Arc)
  - 示例查询：[列出所有没有 Azure Monitor 扩展的已启用 Azure Arc 的 Kubernetes 群集](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
  - 示例查询：[列出所有已启用 Azure Arc 的 Kubernetes 资源](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-resources)
- Microsoft.Kusto/clusters（Azure 数据资源管理器群集）
- Microsoft.Kusto/clusters/databases（Azure 数据资源管理器数据库）
- Microsoft.LabServices/labAccounts（实验室服务）
- microsoft.labservices/labplans
- microsoft.labservices/labs
- Microsoft.LoadTestService/LoadTests（云原生负载测试）
- Microsoft.Logic/integrationAccounts（集成帐户）
- Microsoft.Logic/integrationServiceEnvironments（集成服务环境）
- Microsoft.Logic/integrationServiceEnvironments/managedApis（托管连接器）
- Microsoft.Logic/workflows（逻辑应用）
- Microsoft.Logz/monitors（Logz 主帐户）
- Microsoft.Logz/monitors/accounts（Logz 子帐户）
- Microsoft.MachineLearning/commitmentPlans（机器学习工作室（经典）Web 服务计划）
- Microsoft.MachineLearning/webServices（机器学习工作室（经典）Web 服务）
- Microsoft.MachineLearning/workspaces（机器学习工作室（经典）工作区）
- microsoft.machinelearningcompute/operationalizationclusters
- microsoft.machinelearningexperimentation/accounts/workspaces
- microsoft.machinelearningservices/aisysteminventories
- microsoft.machinelearningservices/modelinventories
- microsoft.machinelearningservices/modelinventory
- microsoft.machinelearningservices/virtualclusters
- Microsoft.MachineLearningServices/workspaces（机器学习）
- microsoft.machinelearningservices/workspaces/batchendpoints
- microsoft.machinelearningservices/workspaces/batchendpoints/deployments
- microsoft.machinelearningservices/workspaces/inferenceendpoints
- microsoft.machinelearningservices/workspaces/inferenceendpoints/deployments
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints（机器学习联机终结点）
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments（机器学习联机部署）
- Microsoft.Maintenance/maintenanceConfigurations（维护配置）
- microsoft.maintenance/maintenancepolicies
- microsoft.managedidentity/groups
- Microsoft.ManagedIdentity/userAssignedIdentities（托管标识）
  - 示例查询：[列出转移 Azure 订阅时受影响的资源](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.managednetwork/managednetworkgroups
- microsoft.managednetwork/managednetworkpeeringpolicies
- microsoft.managednetwork/managednetworks
- microsoft.managednetwork/managednetworks/managednetworkgroups
- microsoft.managednetwork/managednetworks/managednetworkpeeringpolicies
- Microsoft.Maps/accounts（Azure Maps 帐户）
- Microsoft.Maps/accounts/creators（Azure Maps 创建者资源）
- microsoft.maps/accounts/privateatlases
- Microsoft.MarketplaceApps/classicDevServices（传统开发服务）
- microsoft.media/mediaservices（媒体服务）
- microsoft.media/mediaservices/liveevents（直播活动）
- microsoft.media/mediaservices/streamingEndpoints（流式处理终结点）
- microsoft.media/mediaservices/transforms
- microsoft.media/videoanalyzers（视频分析器）
- microsoft.microservices4spring/appclusters
- microsoft.migrate/assessmentprojects
- microsoft.migrate/migrateprojects
- microsoft.migrate/movecollections
- Microsoft.Migrate/projects（迁移项目）
- Microsoft.MixedReality/holographicsBroadcastAccounts（全息广播帐户）
- Microsoft.MixedReality/objectAnchorsAccounts（Object Anchors 帐户）
- Microsoft.MixedReality/objectUnderstandingAccounts（物体理解帐户）
- Microsoft.MixedReality/remoteRenderingAccounts（远程渲染帐户）
- Microsoft.MixedReality/spatialAnchorsAccounts（空间定位点帐户）
- microsoft.mixedreality/surfacereconstructionaccounts
- Microsoft.MobileNetwork/mobileNetworks（移动网络）
- microsoft.mobilenetwork/mobilenetworks/datanetworks
- microsoft.mobilenetwork/mobilenetworks/services
- microsoft.mobilenetwork/mobilenetworks/simpolicies
- Microsoft.MobileNetwork/mobileNetworks/sites（移动网络站点）
- microsoft.mobilenetwork/mobilenetworks/slices
- microsoft.mobilenetwork/networks
- microsoft.mobilenetwork/networks/sites
- Microsoft.MobileNetwork/packetCoreControlPlanes（适用于网络功能的 Arc – Packet Cores）
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes/attacheddatanetworks
- Microsoft.MobileNetwork/sims (Sims)
- microsoft.mobilenetwork/sims/simprofiles
- Microsoft.NetApp/netAppAccounts（NetApp 帐户）
- microsoft.netapp/netappaccounts/backuppolicies
- Microsoft.NetApp/netAppAccounts/capacityPools（容量池）
- Microsoft.NetApp/netAppAccounts/capacityPools/Volumes（卷）
- microsoft.netapp/netappaccounts/capacitypools/volumes/mounttargets
- Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots（快照）
- Microsoft.NetApp/netAppAccounts/snapshotPolicies（快照策略）
- Microsoft.Network/applicationGateways（应用程序网关数）
- Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies（Web 应用程序防火墙策略 (WAF)）
- Microsoft.Network/applicationSecurityGroups（应用程序安全组）
- Microsoft.Network/azureFirewalls（防火墙）
- Microsoft.Network/bastionHosts (Bastions)
- Microsoft.Network/connections（连接）
- Microsoft.Network/customIpPrefixes（自定义 IP 前缀）
- microsoft.network/ddoscustompolicies
- Microsoft.Network/ddosProtectionPlans（DDoS 防护计划）
- microsoft.network/dnsforwardingrulesets
- microsoft.network/dnsresolvers
- Microsoft.Network/dnsZones（DNS 区域）
- microsoft.network/dscpconfigurations
- Microsoft.Network/expressRouteCircuits（ExpressRoute 线路）
- microsoft.network/expressroutecrossconnections
- microsoft.network/expressroutegateways
- Microsoft.Network/expressRoutePorts (ExpressRoute Direct)
- Microsoft.Network/firewallPolicies（防火墙策略）
- Microsoft.Network/frontdoors (Front Door)
- Microsoft.Network/FrontDoorWebApplicationFirewallPolicies（Web 应用程序防火墙策略 (WAF)）
- microsoft.network/ipallocations
- Microsoft.Network/ipGroups（IP 组）
- Microsoft.Network/LoadBalancers（负载均衡器）
- Microsoft.Network/localnetworkgateways（本地网关）
- microsoft.network/mastercustomipprefixes
- Microsoft.Network/natGateways（NAT 网关）
- Microsoft.Network/NetworkExperimentProfiles（Internet 分析器配置文件）
- microsoft.network/networkintentpolicies
- Microsoft.Network/networkinterfaces（网络接口）
  - 示例查询：[获取网络接口的虚拟网络和子网](../samples/samples-by-category.md#get-virtual-networks-and-subnets-of-network-interfaces)
  - 示例查询：[列出虚拟机及其网络接口和公共 IP](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/networkManagers（网络管理器）
- microsoft.network/networkprofiles
- Microsoft.Network/NetworkSecurityGroups（网络安全组）
  - 示例查询：[显示未关联的网络安全组](../samples/samples-by-category.md#show-unassociated-network-security-groups)
- microsoft.network/networksecurityperimeters
- microsoft.network/networkvirtualappliances
- microsoft.network/networkwatchers（网络观察程序）
- microsoft.network/networkwatchers/connectionmonitors
- microsoft.network/networkwatchers/flowlogs（NSG 流日志）
- microsoft.network/networkwatchers/lenses
- microsoft.network/networkwatchers/pingmeshes
- microsoft.network/p2svpngateways
- Microsoft.Network/privateDnsZones （专用 DNS 区域）
- microsoft.network/privatednszones/virtualnetworklinks
- microsoft.network/privateendpointredirectmaps
- Microsoft.Network/privateEndpoints（专用终结点）
- Microsoft.Network/privateLinkServices（专用链接服务）
- Microsoft.Network/PublicIpAddresses（公共 IP 地址）
  - 示例查询：[列出虚拟机及其网络接口和公共 IP](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/publicIpPrefixes（公共 IP 前缀）
- Microsoft.Network/routeFilters（路由筛选器）
- Microsoft.Network/routeTables（路由表）
- microsoft.network/sampleresources
- microsoft.network/securitypartnerproviders
- Microsoft.Network/serviceEndpointPolicies（服务终结点策略）
- Microsoft.Network/trafficmanagerprofiles（流量管理器配置文件）
- microsoft.network/virtualhubs
- microsoft.network/virtualhubs/bgpconnections
- microsoft.network/virtualhubs/ipconfigurations
- Microsoft.Network/virtualNetworkGateways（虚拟网络网关）
- Microsoft.Network/virtualNetworks（虚拟网络）
- microsoft.network/virtualnetworktaps
- microsoft.network/virtualrouters
- Microsoft.Network/virtualWans（虚拟 WAN）
- microsoft.network/vpngateways
- microsoft.network/vpnserverconfigurations
- microsoft.network/vpnsites
- microsoft.networkfunction/azuretrafficcollectors
- Microsoft.NotificationHubs/namespaces（通知中心命名空间）
- Microsoft.NotificationHubs/namespaces/notificationHubs（通知中心）
- microsoft.nutanix/interfaces
- microsoft.nutanix/nodes
- microsoft.objectstore/osnamespaces
- microsoft.offazure/hypervsites
- microsoft.offazure/importsites
- microsoft.offazure/mastersites
- microsoft.offazure/serversites
- microsoft.offazure/vmwaresites
- microsoft.openlogisticsplatform/applicationworkspaces
- Microsoft.OpenLogisticsPlatform/workspaces（开放供应链平台）
- microsoft.operationalinsights/clusters
- Microsoft.OperationalInsights/querypacks（Log Analytics 查询包）
- Microsoft.OperationalInsights/workspaces（Log Analytics 工作区）
- Microsoft.OperationsManagement/solutions（解决方案）
- microsoft.operationsmanagement/views
- Microsoft.Orbital/contactProfiles（联系人配置文件）
- microsoft.orbital/groundstations
- microsoft.orbital/orbitalendpoints
- microsoft.orbital/orbitalgateways
- microsoft.orbital/orbitalgateways/orbitall2connections
- microsoft.orbital/orbitalgateways/orbitall3connections
- Microsoft.Orbital/spacecrafts（航天器）
- Microsoft.Peering/peerings（对等互连）
- Microsoft.Peering/peeringServices（对等互连服务）
- Microsoft.Portal/dashboards（共享仪表板）
- microsoft.portalsdk/rootresources
- microsoft.powerbi/privatelinkservicesforpowerbi
- microsoft.powerbi/tenants
- microsoft.powerbi/workspacecollections
- microsoft.powerbidedicated/autoscalevcores
- Microsoft.PowerBIDedicated/capacities (Power BI Embedded)
- microsoft.powerplatform/accounts
- microsoft.powerplatform/enterprisepolicies
- microsoft.projectbabylon/accounts
- Microsoft.Purview/Accounts（Purview 帐户）
- Microsoft.Quantum/Workspaces（Quantum 工作区）
- Microsoft.RecommendationsService/accounts（智能建议帐户）
- Microsoft.RecommendationsService/accounts/modeling（建模）
- Microsoft.RecommendationsService/accounts/serviceEndpoints（服务终结点）
- Microsoft.RecoveryServices/vaults（恢复服务保管库）
- Microsoft.RedHatOpenShift/openShiftClusters（OpenShift 群集）
- Microsoft.Relay/namespaces（中继）
- microsoft.remoteapp/collections
- microsoft.resiliency/chaosexperiments
- Microsoft.ResourceConnector/Appliances（资源网桥）
- Microsoft.resourcegraph/queries（Resource Graph 查询）
- Microsoft.Resources/deploymentScripts（部署脚本）
- Microsoft.Resources/templateSpecs（模板规格）
- microsoft.resources/templatespecs/versions
- Microsoft.SaaS/applications（服务型软件（经典））
- Microsoft.SaaS/resources (SaaS)
- Microsoft.Scheduler/jobCollections（计划程序作业集合）
- Microsoft.Scom/managedInstances (Aquila Resources)
- microsoft.scvmm/clouds
- Microsoft.scvmm/virtualMachines（SCVMM 虚拟机 - Azure Arc）
- microsoft.scvmm/virtualmachinetemplates
- microsoft.scvmm/virtualnetworks
- microsoft.scvmm/vmmservers
- Microsoft.Search/searchServices（搜索服务）
- microsoft.security/automations
- microsoft.security/iotsecuritysolutions
- microsoft.security/securityconnectors
- Microsoft.SecurityDetonation/chambers（安全引爆区域）
- Microsoft.ServiceBus/namespaces（服务总线命名空间）
- Microsoft.ServiceFabric/clusters（Service Fabric 群集）
- microsoft.servicefabric/containergroupsets
- Microsoft.ServiceFabric/managedclusters（Service Fabric 托管群集）
- microsoft.servicefabricmesh/applications
- microsoft.servicefabricmesh/gateways
- microsoft.servicefabricmesh/networks
- microsoft.servicefabricmesh/secrets
- microsoft.servicefabricmesh/volumes
- Microsoft.ServicesHub/connectors（服务中心连接器）
- Microsoft.SignalRService/SignalR (SignalR)
- Microsoft.SignalRService/WebPubSub（Web PubSub 服务）
- microsoft.singularity/accounts
- microsoft.skytap/nodes
- microsoft.solutions/appliancedefinitions
- microsoft.solutions/appliances
- Microsoft.Solutions/applicationDefinitions（服务目录托管应用程序定义）
- Microsoft.Solutions/applications（托管应用程序）
- microsoft.solutions/jitrequests
- microsoft.spoolservice/spools
- Microsoft.Sql/instancePools（实例池）
- Microsoft.Sql/managedInstances（SQL 托管实例）
- Microsoft.Sql/managedInstances/databases（托管数据库）
- Microsoft.Sql/servers（SQL 服务器）
- Microsoft.Sql/servers/databases（SQL 数据库）
  - 示例查询：[列出转移 Azure 订阅时受影响的资源](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
  - 示例查询：[列出 SQL 数据库及其弹性池](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- Microsoft.Sql/servers/elasticpools（SQL 弹性池）
  - 示例查询：[列出 SQL 数据库及其弹性池](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- microsoft.sql/servers/jobaccounts
- Microsoft.Sql/servers/jobAgents（弹性作业代理）
- Microsoft.Sql/virtualClusters（虚拟群集）
- microsoft.sqlvirtualmachine/sqlvirtualmachinegroups
- Microsoft.SqlVirtualMachine/SqlVirtualMachines（SQL 虚拟机）
- microsoft.sqlvm/dwvm
- Microsoft.Storage/StorageAccounts（存储帐户）
  - 示例查询：[在资源组上查找具有不区分大小写的特定标记的存储帐户](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - 示例查询：[在资源组上查找具有区分大小写的特定标记的存储帐户](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)
  - 示例查询：[列出具有特定标记值的所有存储帐户](../samples/samples-by-category.md#list-all-storage-accounts-with-specific-tag-value)
  - 示例查询：[列出转移 Azure 订阅时受影响的资源](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.storagecache/amlfilesystems
- Microsoft.StorageCache/caches（HPC 缓存）
- Microsoft.StoragePool/diskPools（磁盘池）
- Microsoft.StorageSync/storageSyncServices（存储同步服务）
- Microsoft.StorageSyncDev/storageSyncServices（存储同步服务）
- Microsoft.StorageSyncInt/storageSyncServices（存储同步服务）
- Microsoft.StorSimple/Managers（StorSimple 设备管理器）
- Microsoft.StreamAnalytics/clusters（流分析群集）
- Microsoft.StreamAnalytics/StreamingJobs（流分析作业）
- microsoft.swiftlet/virtualmachines
- microsoft.swiftlet/virtualmachinesnapshots
- Microsoft.Synapse/privateLinkHubs（Azure Synapse Analytics [专用链接中心]）
- Microsoft.Synapse/workspaces (Azure Synapse Analytics)
- Microsoft.Synapse/workspaces/bigDataPools（Apache Spark 池）
- microsoft.synapse/workspaces/eventstreams
- Microsoft.Synapse/workspaces/kustopools（数据资源管理器池（预览版））
- microsoft.synapse/workspaces/sqldatabases
- Microsoft.Synapse/workspaces/sqlPools（专用 SQL 池）
- microsoft.terraformoss/providerregistrations
- Microsoft.TestBase/testBaseAccounts（Test Base 帐户）
- microsoft.testbase/testbaseaccounts/packages
- microsoft.testbase/testbases
- Microsoft.TimeSeriesInsights/environments（时序见解环境）
- Microsoft.TimeSeriesInsights/environments/eventsources（时序见解事件源）
- Microsoft.TimeSeriesInsights/environments/referenceDataSets（时序见解参考数据集）
- microsoft.token/stores
- microsoft.tokenvault/vaults
- Microsoft.VideoIndexer/accounts（用于媒体的视频分析器）
- Microsoft.VirtualMachineImages/imageTemplates（映像模板）
- microsoft.visualstudio/account（Azure DevOps 组织）
- microsoft.visualstudio/account/extension
- microsoft.visualstudio/account/project（DevOps 初学者）
- microsoft.vmware/arczones
- microsoft.vmware/resourcepools
- microsoft.vmware/vcenters
- microsoft.vmware/virtualmachines
- microsoft.vmware/virtualmachinetemplates
- microsoft.vmware/virtualnetworks
- Microsoft.VMwareCloudSimple/dedicatedCloudNodes（CloudSimple 节点）
- Microsoft.VMwareCloudSimple/dedicatedCloudServices（CloudSimple 服务）
- Microsoft.VMwareCloudSimple/virtualMachines（CloudSimple 虚拟机）
- microsoft.vmwareonazure/privateclouds
- microsoft.vmwarevirtustream/privateclouds
- microsoft.vsonline/accounts
- Microsoft.VSOnline/Plans（Visual Studio Online 计划）
- microsoft.web/apimanagementaccounts
- microsoft.web/apimanagementaccounts/apis
- microsoft.web/certificates
- Microsoft.Web/connectionGateways（本地数据网关）
- Microsoft.Web/connections（API 连接）
- Microsoft.Web/customApis（逻辑应用自定义连接器）
- Microsoft.Web/HostingEnvironments（应用服务环境）
- Microsoft.Web/KubeEnvironments（应用服务 Kubernetes 环境）
- Microsoft.Web/serverFarms（应用服务计划）
- Microsoft.Web/sites（应用服务）
- microsoft.web/sites/premieraddons
- Microsoft.Web/sites/slots（应用服务[槽]）
- Microsoft.Web/StaticSites (Static Web Apps)
- Microsoft.Web/WorkerApps（容器应用）
- Microsoft.WindowsESU/multipleActivationKeys（Windows 多次激活密钥）
- Microsoft.WindowsIoT/DeviceServices (Windows 10 IoT Core Services)
- microsoft.workloadbuilder/migrationagents
- microsoft.workloadbuilder/workloads
- myget.packagemanagement/services
- NGINX.NGINXPLUS/nginxDeployments（NGINX 部署）
- Paraleap.CloudMonix/services (CloudMonix)
- Pokitdok.Platform/services（PokitDok 平台）
- Providers.Test/statefulIbizaEngines（应用程序评估）
- RavenHq.Db/databases (RavenHQ)
- Raygun.CrashReporting/apps (Raygun)
- Sendgrid.Email/accounts（SendGrid 帐户）
- sparkpost.basic/services
- stackify.retrace/services
- test.shoebox/testresources
- test.shoebox/testresources2
- TrendMicro.DeepSecurity/accounts（深度安全 SaaS）
- u2uconsult.theidentityhub/services
- Wandisco.Fusion/fusionGroups（LiveData 平面）
- Wandisco.Fusion/fusionGroups/azureZones（Azure 区域）
- Wandisco.Fusion/fusionGroups/azureZones/plugins（插件）
- Wandisco.Fusion/fusionGroups/hiveReplicationRules（Hive 复制规则）
- Wandisco.Fusion/fusionGroups/managedOnPremZones（本地区域）
- wandisco.fusion/fusiongroups/onpremzones
- Wandisco.Fusion/fusionGroups/replicationRules（复制规则）
- Wandisco.Fusion/migrators（LiveData 迁移程序）
- Wandisco.Fusion/migrators/exclusionTemplates（排除）
- Wandisco.Fusion/migrators/liveDataMigrations（迁移）
- Wandisco.Fusion/migrators/metadataMigrations（元数据迁移）
- Wandisco.Fusion/migrators/metadataTargets（元数据目标）
- Wandisco.Fusion/migrators/pathMappings（路径映射）
- Wandisco.Fusion/migrators/targets（目标）

## <a name="securityresources"></a>securityresources

有关此表的示例查询，请参阅 [securityresources 的 Resource Graph 示例查询](../samples/samples-by-table.md#securityresources)。

- microsoft.security/assessments
  - 示例查询：[根据建议统计运行正常、运行不正常和不合规的资源](../samples/samples-by-category.md#count-healthy-unhealthy-and-not-applicable-resources-per-recommendation)
  - 示例查询：[列出 Azure 安全中心建议](../samples/samples-by-category.md#list-azure-security-center-recommendations)
  - 示例查询：[列出容器注册表漏洞评估结果](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - 示例查询：[列出 Qualys 漏洞评估结果](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/assessments/subassessments
  - 示例查询：[列出容器注册表漏洞评估结果](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - 示例查询：[列出 Qualys 漏洞评估结果](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/insights/classification（数据敏感度安全见解（预览版））
- microsoft.security/iotalerts
  - 示例查询：[获取中心的所有 IoT 警报（按类型筛选）](../samples/samples-by-category.md#get-all-iot-alerts-on-hub-filtered-by-type)
  - 示例查询：[获取特定的 IoT 警报](../samples/samples-by-category.md#get-specific-iot-alert)
- microsoft.security/locations/alerts（安全警报）
- microsoft.security/pricings
  - 示例查询：[显示每个订阅的 Azure Defender 定价层](../samples/samples-by-category.md#show-azure-defender-pricing-tier-per-subscription)
- microsoft.security/regulatorycompliancestandards
  - 示例查询：[每个合规性标准的合规性状态](../samples/samples-by-category.md#regulatory-compliance-state-per-compliance-standard)
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments
  - 示例查询：[法规符合性评估状态](../samples/samples-by-category.md#regulatory-compliance-assessments-state)
- microsoft.security/securescores
  - 示例查询：[每个管理组的安全功能分数](../samples/samples-by-category.md#secure-score-per-management-group)
  - 示例查询：[每个订阅的安全功能分数](../samples/samples-by-category.md#secure-score-per-subscription)
- microsoft.security/securescores/securescorecontrols
  - 示例查询：[控制每个订阅的安全功能分数](../samples/samples-by-category.md#controls-secure-score-per-subscription)
- microsoft.security/softwareinventories
- microsoft.security/softwareinventory

## <a name="servicehealthresources"></a>servicehealthresources

有关此表的示例查询，请参阅 [servicehealthresources 的 Resource Graph 示例查询](../samples/samples-by-table.md#servicehealthresources)。

- microsoft.resourcehealth/events
  - 示例查询：[活动服务运行状况事件订阅影响](../samples/samples-by-category.md#active-service-health-event-subscription-impact)
  - 示例查询：[所有活动的运行状况公告事件](../samples/samples-by-category.md#all-active-health-advisory-events)
  - 示例查询：[所有活动的计划内维护事件](../samples/samples-by-category.md#all-active-planned-maintenance-events)
  - 示例查询：[所有活动的服务运行状况事件](../samples/samples-by-category.md#all-active-service-health-events)
  - 示例查询：[所有活动的服务问题事件](../samples/samples-by-category.md#all-active-service-issue-events)

## <a name="workloadmonitorresources"></a>workloadmonitorresources

- microsoft.workloadmonitor/monitors

## <a name="next-steps"></a>后续步骤

- 详细了解[查询语言](../concepts/query-language.md)。
- 详细了解如何[浏览资源](../concepts/explore-resources.md)。
- 查看[初级查询](../samples/starter.md)的示例。
