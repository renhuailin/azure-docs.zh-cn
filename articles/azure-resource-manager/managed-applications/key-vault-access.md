---
title: 部署托管应用时使用 Key Vault
description: 演示在部署托管应用程序时如何使用 Azure Key Vault 中的访问机密
author: tfitzmac
ms.custom: subject-rbac-steps
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: tomfitz
ms.openlocfilehash: d5a6dc1de2ee574a69b8dab746a24bcf82b44d64
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253166"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>部署 Azure 托管应用程序时访问 Key Vault 机密

在部署过程中，需要将安全值（例如密码）作为参数传递时，可从 [Azure 密钥保管库](../../key-vault/general/overview.md)检索值。 若要在部署托管应用程序时访问 Key Vault，必须授予对 **设备资源提供程序** 服务主体的访问权限。 托管应用程序服务使用此标识来运行操作。 若要在部署过程中从密钥保管库成功检索某个值，服务主体必须能够访问密钥保管库。

本文介绍如何配置 Key Vault 以与托管应用程序一起使用。

## <a name="enable-template-deployment"></a>启用模板部署

1. 在门户中，选择 Key Vault。

1. 选择“访问策略”。   

   ![选择访问策略](./media/key-vault-access/select-access-policies.png)

1. 选择“单击以显示高级访问策略”。

   ![显示高级访问策略](./media/key-vault-access/advanced.png)

1. 选择“启用对 Azure 资源管理器的访问以进行模板部署”。 然后选择“保存”。

   ![启用模板部署](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>将服务添加为参与者

将“参与者”角色分配给密钥保管库范围内的“设备资源提供程序”用户 。

有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

## <a name="reference-key-vault-secret"></a>引用 Key Vault 机密

若要将 Key Vault 中的机密传递给托管应用程序中的模板，必须使用[链接或嵌套模板](../templates/linked-templates.md)并在链接或嵌套模板的参数中引用 Key Vault。 提供 Key Vault 的资源 ID 和机密名称。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>后续步骤

已将 Key Vault 配置为在部署托管应用程序期间可访问。

* 有关从 Key Vault 传递值作为模板参数的信息，请参阅[使用 Azure Key Vault 在部署期间传递安全参数值](../templates/key-vault-parameter.md)。
* 有关托管应用程序示例，请参阅 [Azure 托管应用程序的示例项目](sample-projects.md)。
* 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
