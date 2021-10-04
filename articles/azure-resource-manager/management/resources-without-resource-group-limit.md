---
title: 没有 800 计数限制的资源
description: 列出可以在一个资源组中有 800 多个实例的 Azure 资源类型。
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 123148a863f74a6490a524ecc2d39bc44e65299d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807053"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>没有每个资源组 800 个实例限制的资源

默认情况下，就单个资源类型来说，可以在每个资源组中部署最多 800 个实例。 不过，某些资源类型没有 800 个实例的限制。 本文列出了可以在一个资源组中有 800 多个实例的 Azure 资源类型。 所有其他资源类型有 800 个实例的限制。

就某些资源类型来说，若要移除 800 个实例的限制，需联系支持部门。 这些资源类型已在本文中注明。

某些资源对每个区域的实例数有限制。 此限制与每个资源组 800 个实例不同。 若要检查每个区域的实例，请使用 Azure 门户。 在左侧窗格中选择你的订阅和“使用情况 + 配额”。 有关详细信息，请参阅[根据限制检查资源使用情况](../../networking/check-usage-against-limits.md)。 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

* resourceHealthAlertRules
* smartDetectorAlertRules

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* linkedSubscriptions
* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* snapshots
* virtualMachineScaleSets - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registries/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registries/buildTasks/steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* instances

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* serverGroupsv2
* servers
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* schedules

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* namespaces

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

* 计算机 - 最多支持 5,000 个实例
* 计算机/扩展 - 支持的 VM 扩展实例数没有限制

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts
* scheduledqueryrules

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots
* netAppAccounts/snapshotPolicies
* netAppAccounts/volumeGroups

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* autoScaleVCores - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。
* capacities - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。

## <a name="microsoftrelay"></a>Microsoft.Relay

* namespaces

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* namespaces

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* accounts
* accounts/accountQuotaPolicies
* accounts/groupPolicies
* accounts/jobs
* accounts/models
* accounts/storageContainers

## <a name="microsoftsql"></a>Microsoft.Sql

* servers/databases

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

* streamingjobs - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。

## <a name="next-steps"></a>后续步骤

有关配额和限制的完整列表，请参阅 [Azure 订阅和服务限制、配额与约束](azure-subscription-service-limits.md)。
