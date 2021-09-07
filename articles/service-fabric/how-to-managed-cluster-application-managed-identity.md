---
title: 在 Service Fabric 托管群集上配置和使用具有托管标识的应用程序
description: 了解如何在 Azure 资源管理器 (ARM) 模板部署的 Azure Service Fabric 托管群集上配置和使用具有托管标识的应用程序。
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 5007f17754572c03ff820ecda1bb09f6e8a8020e
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864747"
---
# <a name="deploy-an-application-with-managed-identity-to-a-service-fabric-managed-cluster"></a>将具有托管标识的应用程序部署到 Service Fabric 托管群集

若要使用托管标识部署 Service Fabric 应用程序，需通过 Azure 资源管理器部署应用程序，通常需要使用 Azure 资源管理器模板。 若要详细了解如何通过 Azure 资源管理器部署 Service Fabric 应用程序，请参阅[使用 Azure 资源管理器将应用程序部署到托管群集](how-to-managed-cluster-app-deployment-template.md)。

> [!NOTE] 
> 
> 未部署为 Azure 资源的应用程序 **不能** 有托管标识。 
>
> 托管群集上的 API 版本 `"2021-05-01"` 支持使用托管标识部署 Service Fabric 应用程序。

此处提供了示例托管群集模板：[Service Fabric 托管群集模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="managed-identity-support-in-service-fabric-managed-cluster"></a>Service Fabric 托管群集中的托管标识支持

使用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)配置 Service Fabric 应用程序并部署到群集时，它将触发 Service Fabric 托管群集上“托管标识令牌服务”的自动配置。 此服务负责使用 Service Fabric 应用程序的托管标识对这些应用程序进行身份验证，以及代表它们获取访问令牌。 启用此服务以后，即可在 Service Fabric Explorer 中左侧窗格的“系统”部分看到它，它以 **fabric:/System/ManagedIdentityTokenService** 名称运行。

>[!NOTE]
>首次使用托管标识部署应用程序时，由于自动群集配置更改，可能预计部署时间会较长（仅限此次）。 区域性群集可能需要 15 分钟，而跨区域群集可能需要 45 分钟。 如果有任何其他部署正在进行中，MITS 配置将等待这些部署先完成。

应用程序资源支持分配 SystemAssigned 或 UserAssigned 两者，并且分配可以按以下代码片段所示完成。

```json
{
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "apiVersion": "2021-05-01",
  "identity": {
    "type": "SystemAssigned",
    "userAssignedIdentities": {}
  },
}

```
[完整的 JSON 参考](/azure/templates/microsoft.servicefabric/2021-05-01/managedclusters/applications?tabs=json)

## <a name="user-assigned-identity"></a>用户分配的标识

若要为应用程序启用用户分配的托管标识，请先将类型为 **userAssigned** 的 **identity** 属性添加到应用程序资源和引用的用户分配标识。 然后将 **managedIdentities** 节添加到 **application** 资源的 **properties** 节中，该资源包含一个易记名称的列表，可以为每个用户分配的标识进行 principalId 映射。 有关用户分配的标识的详细信息，请参阅[创建、列出或删除用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)。

### <a name="application-template"></a>应用程序模板

若要为应用程序启用用户分配的托管标识，请先将类型为 **userAssigned** 的 **identity** 属性添加到应用程序资源和引用的用户分配标识，然后将 **managedIdentities** 对象添加到 **properties** 节中，该节包含一个易记名称的列表，可以为每个用户分配的标识进行 principalId 映射。

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[parameters('applicationVersion')]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "version": "[parameters('applicationVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

在上面的示例中，用户分配标识的资源名称用作应用程序的托管标识的易记名称。 以下示例假定实际的易记名称为“AdminUser”。

### <a name="application-package"></a>应用程序包

1. 对于在 Azure 资源管理器模板的 `managedIdentities` 节中定义的每个标识，请在应用程序清单的 **Principals** 节下添加 `<ManagedIdentity>` 标记。 `Name` 属性需与 `managedIdentities` 节中定义的 `name` 属性匹配。

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. 在 **ServiceManifestImport** 节中，为使用托管标识的服务添加 **IdentityBindingPolicy**。 此策略将 `AdminUser` 标识映射到特定于服务的标识名称，该名称需在以后添加到服务清单中。

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 更新服务清单，将 **ManagedIdentity** 添加到 **Resources** 节中，其名称与应用程序清单的 `IdentityBindingPolicy` 中的 `ServiceIdentityRef` 匹配：

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

### <a name="application-template"></a>应用程序模板

若要为应用程序启用系统分配的托管标识，请将类型为 **systemAssigned** 的 **identity** 属性添加到应用程序资源，如以下示例所示：

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
此属性向 Azure 资源管理器、托管标识和 Service Fabric 资源提供程序分别声明：此资源应该有一个隐式 (`system assigned`) 托管标识。

### <a name="application-and-service-package"></a>应用程序和服务包

1. 更新应用程序清单，在 **Principals** 节添加包含单个条目的 **ManagedIdentity** 元素，如下所示：

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    这会将分配给应用程序作为资源的标识映射到某个易记名称，以便进一步分配给包含该应用程序的服务。 

2. 在 **ServiceManifestImport** 节（对应于已向其分配了托管标识的服务）中添加 **IdentityBindingPolicy** 元素，如下所示：

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    此元素将应用程序的标识分配给服务；没有此分配，服务将无法访问应用程序的标识。 在上面的代码片段中，`SystemAssigned` 标识（保留关键字）映射到服务的定义，采用的易记名称为 `WebAdmin`。

3. 更新服务清单，将 **ManagedIdentity** 元素添加到 **Resources** 节中，其名称与 `ServiceIdentityRef` 设置的值匹配，该设置来自应用程序清单中的 `IdentityBindingPolicy` 定义：

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    这是一个等效映射，等效于将标识映射到服务（如上所述），但却是从服务定义的角度来看。 在这里，标识按其易记名称 (`WebAdmin`) 引用，如应用程序清单所述。

## <a name="next-steps"></a>后续步骤
* [从服务代码中利用 Service Fabric 应用程序的托管标识](how-to-managed-identity-service-fabric-app-code.md)
* [为 Azure Service Fabric 应用程序授予对其他 Azure 资源的访问权限](how-to-grant-access-other-resources.md)