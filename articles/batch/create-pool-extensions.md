---
title: 对 Batch 池使用扩展
description: 扩展是一种小型应用程序，用于帮助在 Batch 计算节点上进行预配后配置和设置。
ms.topic: how-to
ms.date: 08/06/2021
ms.openlocfilehash: b9c34dd5d191253352181158ece29218657f8d75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722637"
---
# <a name="use-extensions-with-batch-pools"></a>对 Batch 池使用扩展

扩展是一种小型应用程序，用于帮助在 Batch 计算节点上进行预配后配置和设置。 可选择 Azure Batch 允许的任何扩展，并在预配计算节点时将其安装在这些节点上。 之后，扩展就可执行其预期操作。

可查看所用扩展的实时状态，并检索它们返回的信息，以实现任何检测、更正或诊断功能。

## <a name="prerequisites"></a>先决条件

- 使用扩展的池必须采用[虚拟机配置](nodes-and-pools.md#virtual-machine-configuration)。
- CustomScript 扩展类型是为 Azure Batch 服务保留的，不能被替代。
- 某些扩展可能需要可在计算节点的上下文中访问的池级托管标识才能正常运行。 请参阅[在 Batch 池中配置托管标识](managed-identity-pools.md)（如果适用于扩展）。

### <a name="supported-extensions"></a>支持的扩展

目前可在创建 Batch 池时安装以下扩展：

- 适用于 [Linux](../virtual-machines/extensions/key-vault-linux.md) 和 [Windows](../virtual-machines/extensions/key-vault-windows.md) 的 Azure Key Vault 扩展
- 适用于 [Linux](../virtual-machines/extensions/oms-linux.md) 和 [Windows](../virtual-machines/extensions/oms-windows.md) 的日志分析和监视扩展

可通过创建支持请求来请求对其他发布服务器和/或扩展类型的支持。

## <a name="create-a-pool-with-extensions"></a>创建使用扩展的池

下面的示例会创建一个使用 Azure Key Vault 扩展的 Linux 节点 Batch 池。

REST API URI

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

请求正文

```json
{
    "name": "test1",
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
                        "name": "secretext",
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
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>从池中获取扩展数据

下面的示例从 Azure Key Vault 扩展检索数据。

REST API URI

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

响应正文

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>后续步骤

- 了解[将应用程序和数据复制到池节点](batch-applications-to-pool-nodes.md)的各种方法。
- 详细了解如何使用[节点和池](nodes-and-pools.md)。
