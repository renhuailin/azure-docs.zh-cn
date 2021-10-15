---
title: Bicep 函数 - 资源
description: 介绍 Bicep 文件中用于检索有关资源的值的函数。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 4cfbac80e9783dd9424a4b2ee63607fb6f2a7f17
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361909"
---
# <a name="resource-functions-for-bicep"></a>Bicep 的资源函数

本文介绍用于获取资源值的 Bicep 函数。

若要从当前部署中获取值，请参阅[部署值函数](./bicep-functions-deployment.md)。

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

返回某个[扩展资源](../management/extension-resource-types.md)的资源 ID，该资源属于适用于其他资源的资源类型，是对其功能的补充。

命名空间：[az](bicep-functions.md#namespaces-for-functions)。

extensionResourceId 函数在 Bicep 文件中可用，但通常不需要它。 请改用资源的符号名称并访问 `id` 属性。

此函数返回的资源 ID 的基本格式为：

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

作用域段因扩展的资源而异。

当扩展资源应用到某个 **资源** 时，资源 ID 以下述格式返回：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

当扩展资源应用到某个 **资源组** 时，格式为：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

当扩展资源应用到某个 **订阅** 时，格式为：

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

当扩展资源应用到某个 **管理组** 时，格式为：

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

部署到管理组的自定义策略定义是作为扩展资源实现的。 若要创建和分配策略，请将以下 Bicep 文件部署到管理组。

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

---

内置策略定义是租户级别的资源。 有关部署内置策略定义的示例，请参阅 [tenantResourceId](#tenantresourceid)。

## <a name="getsecret"></a>getSecret

`keyVaultName.getSecret(secretName)`

从 Azure Key Vault 返回机密。 `getSecret` 函数只能在 `Microsoft.KeyVault/vaults` 资源上调用。 使用此函数将机密传递给 Bicep 模块的安全字符串参数。 此函数只能与带 `@secure()` 修饰器的参数一起使用。

密钥保管库必须将 `enabledForTemplateDeployment` 设置为 `true`。 部署 Bicep 文件的用户必须有权访问该机密。 有关详细信息，请参阅[在部署 Bicep 过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。

不需要[命名空间限定符](bicep-functions.md#namespaces-for-functions)，因为此函数与资源类型配合使用。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| secretName | 是 | 字符串 | 密钥保管库中存储的机密的名称。 |

### <a name="return-value"></a>返回值

机密名称的机密值。

### <a name="example"></a>示例

以下 Bicep 文件用作模块。  其中包含一个定义有 `@secure()` 修饰器的 adminPassword 参数。

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  ...
}
```

以下 Bicep 文件使用前面用作模块的 Bicep 文件。 Bicep 文件引用现有的密钥保管库，并调用 `getSecret` 函数来检索密钥保管库机密，然后将值作为参数传递到模块中。

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`resourceName.list([apiVersion], [functionValues])`

可以使用以 `list` 开头的操作为任何资源类型调用 list 函数。 以下是几个常见示例：`list`、`listKeys`、`listKeyValue` 和 `listSecrets`。

此函数的语法因列表操作的名称而异。 返回的值也因操作而异。 Bicep 目前不支持 `list*` 函数的完成和验证。

对于 Bicep 0.4.412 或更高版本，可以使用[访问器运算符](operators-access.md#function-accessor)调用 list 函数。 例如 `stg.listKeys()`。

不需要[命名空间限定符](bicep-functions.md#namespaces-for-functions)，因为此函数与资源类型配合使用。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| apiVersion |否 |字符串 |如果未提供此参数，将使用资源的 API 版本。 仅当需要使用特定版本来运行函数时，才提供自定义 API 版本。 使用 yyyy-mm-dd 格式。 |
| functionValues |否 |object | 具有函数值的对象。 仅为支持接收具有参数值的对象的函数提供此对象，例如存储帐户上的 listAccountSas。 本文中演示了传递函数值的示例。 |

### <a name="valid-uses"></a>有效使用

列表函数可以在资源定义的属性中使用。 请不要使用在 Bicep 文件的 outputs 节中公开敏感信息的 list 函数。 输出值存储在部署历史记录中，可能会被恶意用户检索到。

与[属性循环](./loop-properties.md)一起使用时，可以将 list 函数用于 `input`，因为该表达式是分配给资源属性的。 不能将它们与 `count` 一起使用，因为必须在解析 list 函数之前确定计数。

如果在有条件部署的资源中使用 **list** 函数，则会对该函数进行评估，即使资源尚未部署。 如果 list 函数引用的资源不存在，系统会显示错误。 使用[条件表达式 ?: 运算符](./operators-logical.md#conditional-expression--)确保仅在部署资源时计算函数。

### <a name="return-value"></a>返回值

返回的对象因使用的列表函数而异。 例如，用于存储帐户的 listKeys 返回以下格式：

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

其他列表函数具有不同的返回格式。 要查看函数的格式，请将其包含在 outputs 节中，如示例 Bicep 文件中所示。

### <a name="list-example"></a>List 示例

以下示例部署一个存储帐户，然后对该存储帐户调用 listKeys。 该键在为[部署脚本](../templates/deployment-script-template.md)设置值时使用。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'dscript${uniqueString(resourceGroup().id)}'
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource dScript 'Microsoft.Resources/deploymentScripts@2019-10-01-preview' = {
  name: 'scriptWithStorage'
  location: location
  ...
  properties: {
    azCliVersion: '2.0.80'
    storageAccountSettings: {
      storageAccountName: stg.name
      storageAccountKey: stg.listKeys().keys[0].value
    }
    ...
  }
}
```

下一个示例演示采用参数的列表函数。 在本例中，函数为 listAccountSas。 请为到期时间传递一个对象。 到期时间必须是将来的时间。

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: stg.listAccountSas('2021-04-01', accountSasProperties).accountSasToken
```

### <a name="implementations"></a>实现形式

下表中显示 list* 的可能用途。

| 资源类型 | 函数名称 |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/authorization-server/list-secrets) |
| Microsoft.ApiManagement/service/gateways | [listKeys](/rest/api/apimanagement/2021-04-01-preview/gateway/list-keys) |
| Microsoft.ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/identity-provider/list-secrets) |
| Microsoft.ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2021-04-01-preview/named-value/list-value) |
| Microsoft.ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/openid-connect-provider/list-secrets) |
| Microsoft.ApiManagement/service/subscriptions | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/subscription/list-secrets) |
| Microsoft.AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/Spring | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | listoutputfiles |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/get-build-source-upload-url) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/agentpools | listQueueStatus |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/2020-09-01/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/2020-09-01/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-04-15/database-accounts/list-connection-strings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2021-04-15/database-accounts/list-keys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-04-15/notebook-workspaces/list-connection-info) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/compute/list-keys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/compute/list-nodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspaces/list-keys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Microsoft.RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/2021-04-01-preview/admin-keys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/2021-04-01-preview/query-keys/list-by-search-service) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/stable/namespaces-authorization-rules/list-keys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/preview/queues-authorization-rules/list-keys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20%E2%80%93%20authorization%20rules/list-keys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Synapse/workspaces/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

若要确定哪些资源类型具有列表操作，请使用以下选项：

* 查看资源提供程序的 [REST API 操作](/rest/api/)，并查找列表操作。 例如，存储帐户具有 [listKeys 操作](/rest/api/storagerp/storageaccounts)。
* 使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell cmdlet。 以下示例获取存储帐户的所有列表操作：

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* 使用以下 Azure CLI 命令，仅筛选列表操作：

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

确定资源类型是否支持某一地区的区域。

命名空间：[az](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| providerNamespace | 是 | 字符串 | 要检查是否有区域支持的资源类型的资源提供程序命名空间。 |
| resourceType | 是 | 字符串 | 要检查是否有区域支持的资源类型。 |
| location | 是 | 字符串 | 要检查是否有区域支持的地区。 |
| numberOfZones | 否 | integer | 要返回的逻辑区域数。 默认值为 1。 该数字必须是 1 到 3 的正整数。  对于单区域资源，请使用 1。 对于多区域资源，该值必须小于或等于受支持区域的数量。 |
| offset | 否 | integer | 起始逻辑区域的偏移量。 如果 offset 加上 numberOfZones 超过受支持区域的数量，函数将返回错误。 |

### <a name="return-value"></a>返回值

具有受支持区域的数组。 如果使用 offset 和 numberOfZones 的默认值，支持区域的资源类型和地区返回以下数组：

```json
[
    "1"
]
```

当 `numberOfZones` 参数设置为 3 时，它将返回：

```json
[
    "1",
    "2",
    "3"
]
```

如果资源类型或地区不支持区域，则返回空数组。

```json
[
]
```

### <a name="pickzones-example"></a>pickZones 示例

以下 Bicep 文件显示了使用 pickZones 函数的三个结果。

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

上述示例的输出返回三个数组。

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| 受支持 | array | [ "1" ] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

可以使用 pickZones 的响应来确定是为区域提供 null，还是将虚拟机分配给不同的区域。

## <a name="providers"></a>providers

提供程序函数已弃用。 我们不再建议使用它。 如果你使用了此函数来获取资源提供程序的 API 版本，建议在模板中提供特定的 API 版本。 如果版本之间的属性发生更改，则使用动态返回的 API 版本可能会破坏模板。

命名空间：[az](bicep-functions.md#namespaces-for-functions)。

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

返回表示资源的运行时状态的对象。

命名空间：[az](bicep-functions.md#namespaces-for-functions)。

reference 函数在 Bicep 文件中可用，但通常不需要它。 请改用资源的符号名称。

以下示例部署一个存储帐户。 它使用存储帐户的符号名称 `stg` 返回属性。

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

若要从未部署在模板中的现有资源获取属性，请使用 `existing` 关键字：

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
output blobAddress string = stg.properties.primaryEndpoints.blob
```

有关详细信息，请参阅[引用资源](./compare-template-syntax.md#reference-resources)和 [JSON 模板 reference 函数](../templates/template-functions-resource.md#reference)。


## <a name="resourceid"></a>ResourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

返回资源的唯一标识符。

命名空间：[az](bicep-functions.md#namespaces-for-functions)。

resourceId 函数在 Bicep 文件中可用，但通常不需要它。 请改用资源的符号名称并访问 `id` 属性。

如果资源名称不确定或未设置在同一个 Bicep 文件中，请使用此函数。 返回的标识符的格式因部署是在资源组、订阅、管理组还是租户的范围内进行而不同。

例如：

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageID string = stg.id
```

若要获取未在 Bicep 文件中部署的资源的资源 ID，请使用 existing 关键字。

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
    name: storageAccountName
}

output storageID string = stg.id
```

有关详细信息，请参阅 [JSON 模板 resourceId 函数](../templates/template-functions-resource.md#resourceid)

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

返回在订阅级别部署的资源的唯一标识符。

命名空间：[az](bicep-functions.md#namespaces-for-functions)。

subscriptionResourceId 函数在 Bicep 文件中可用，但通常不需要它。 请改用资源的符号名称并访问 `id` 属性。

使用以下格式返回标识符：

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>备注

我们使用此函数获取[部署到订阅](deploy-to-subscription.md)而不是资源组的资源的资源 ID。 返回的 ID 不同于 [resourceId](#resourceid) 函数返回的值，区别在于不包含资源组值。

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID 示例

以下 Bicep 文件分配一个内置角色。 可以将它部署到资源组或订阅。 它使用 subscriptionResourceId 函数获取内置角色的资源 ID。

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: guid(resourceGroup().id, principalId, roleDefinitionId[builtInRoleType].id)
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

返回在租户级别部署的资源的唯一标识符。

命名空间：[az](bicep-functions.md#namespaces-for-functions)。

tenantResourceId 函数在 Bicep 文件中可用，但通常不需要它。 请改用资源的符号名称并访问 `id` 属性。

使用以下格式返回标识符：

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

内置策略定义是租户级别的资源。 若要部署引用内置策略定义的策略分配，请使用 tenantResourceId 函数。

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

## <a name="next-steps"></a>后续步骤

* 若要从当前部署中获取值，请参阅[部署值函数](./bicep-functions-deployment.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Bicep 中部署多个资源实例](./loop-resources.md)。
