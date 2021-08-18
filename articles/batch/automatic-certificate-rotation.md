---
title: 在 Batch 池中启用自动证书轮换
description: 你可以创建包含托管标识和自动续订证书的 Batch 池。
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: 9a8f4d785c9a411df6c867724f057b1dc9b29722
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362411"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>在 Batch 池中启用自动证书轮换

 你可以创建包含自动续订证书的 Batch 池。 为此，要创建的池必须包含有权访问 [Azure 密钥保管库](../key-vault/general/overview.md)中证书的[用户分配的托管标识](managed-identity-pools.md)。

## <a name="create-a-user-assigned-identity"></a>创建用户分配的标识

首先，在与 Batch 帐户相同的租户中[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。 此托管标识不需要位于同一资源组中，甚至不需要位于同一订阅中。

确保记下用户分配的托管标识的“客户端 ID”。 稍后将需要此值。

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="显示 Azure 门户中用户分配的托管标识的客户端 ID 的屏幕截图。":::

## <a name="create-your-certificate"></a>创建证书

接下来，需要创建证书并将其添加到 Azure 密钥保管库。 如果尚未创建密钥保管库，则需要先执行此操作。 有关说明，请参阅[快速入门：使用 Azure 门户在 Azure 密钥保管库中设置和检索证书](../key-vault/certificates/quick-create-portal.md)。

创建证书时，请确保将“生存期操作类型”设置为自动续订，并指定证书应在多少天后续订。

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Azure 门户中证书创建屏幕的屏幕截图。":::

创建证书后，记下其“机密标识符”。 稍后将需要此值。

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="显示证书机密标识符的屏幕截图。":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>在 Azure 密钥保管库中添加访问策略

在密钥保管库中，分配密钥保管库访问策略，以允许使用用户分配的托管标识访问机密和证书。 有关详细说明，请参阅[使用 Azure 门户分配密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md)。

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>创建包含用户分配的托管标识的 Batch 池

使用 [Batch .NET 管理库](/dotnet/api/overview/azure/batch#management-library)创建包含托管标识的 Batch 池。 有关详细信息，请参阅[在 Batch 池中配置托管标识](managed-identity-pools.md)。

以下示例使用 Batch Management REST API 来创建池。 请确保 `observedCertificates` 使用证书的“机密标识符”，`msiClientId` 使用托管标识的“客户端 ID”，并替换下面的示例数据。

REST API URI

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

请求正文

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>验证证书

若要确认已成功部署证书，请登录到计算节点。 应该会看到与下面类似的输出：

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
- 了解如何配合使用[客户管理的密钥和用户管理的标识](batch-customer-managed-key.md)。
