---
title: 为应用程序授予对其他 Azure 资源的访问权限
description: 本文介绍如何为启用了托管标识的 Service Fabric 应用程序授予对支持基于 Azure Active Directory 身份验证的其他 Azure 资源的访问权限。
ms.topic: article
ms.date: 12/09/2019
ms.custom: subject-rbac-steps
ms.openlocfilehash: ac383c3d517bbbab2b25c12dc4a06b4fe63e1b89
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122865044"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>为 Service Fabric 应用程序的托管标识授予对 Azure 资源的访问权限

在应用程序可以使用其托管标识访问其他资源之前，必须为该标识授予对所要访问的受保护 Azure 资源的权限。 授予权限通常是 Azure 服务的“控制平面”中执行的一项管理操作，该服务拥有通过 Azure 资源管理器路由的受保护资源。这会强制实施任何适用的基于角色的访问检查。

具体的步骤顺序取决于要访问的 Azure 资源的类型，以及用于授予权限的语言/客户端。 本文的余下内容假设已将用户分配的标识分配到应用程序，其中包含一些典型示例供你参考，但本主题的参考内容不可能做到详尽；有关授予权限的最新说明，请参阅相应 Azure 服务的文档。  

## <a name="granting-access-to-azure-storage"></a>授予对 Azure 存储的访问权限
可以使用 Service Fabric 应用程序的托管标识（在本例中为用户分配的标识）来检索 Azure 存储 Blob 中的数据。 通过在“resource-group”范围将[存储 Blob 数据读者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色分配给应用程序的托管标识，为标识授予存储帐户所需的权限。

有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。


## <a name="granting-access-to-azure-key-vault"></a>授予对 Azure Key Vault 的访问权限
与访问存储类似，可以利用 Service Fabric 应用程序的托管标识来访问 Azure Key Vault。 在 Azure 门户中授予访问权限的步骤类似于上面所列的步骤，这里不再重复。 有关差异，请参考下图。

![Key Vault 访问策略](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

以下示例演示如何通过模板部署授予对保管库的访问权限；请将以下代码片段添加为模板的 `resources` 元素下的另一个条目。 此示例演示如何为用户分配的标识类型和系统分配的标识类型分别授予访问权限 - 请选择适用的一个。

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
对于系统分配的托管标识：
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

有关更多详细信息，请参阅[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>后续步骤
* [使用托管标识将 Service Fabric 应用程序部署到托管群集](how-to-managed-cluster-application-managed-identity.md)
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
