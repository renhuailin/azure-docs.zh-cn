---
title: 将扩展与批处理池一起使用
description: 扩展是一种小型应用程序，用于简化批处理计算节点上的预配配置和设置。
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416969"
---
# <a name="use-extensions-with-batch-pools"></a>将扩展与批处理池一起使用

扩展是一种小型应用程序，用于简化批处理计算节点上的预配配置和设置。 你可以选择 Azure Batch 允许的任何扩展，并在预配计算节点时将其安装在计算节点上。 之后，扩展可以执行其预期操作。

您可以查看您使用的扩展的实时状态，并检索它们返回的信息，以寻求任何检测、更正或诊断功能。

## <a name="prerequisites"></a>先决条件

- 具有扩展的池必须使用 [虚拟机配置](nodes-and-pools.md#virtual-machine-configuration)。
- CustomScript 扩展类型是为 Azure Batch 服务而保留的，不能被重写。

### <a name="supported-extensions"></a>支持的扩展

在创建 Batch 池时，当前可以安装以下扩展。 

- 适用于[Linux](../virtual-machines/extensions/key-vault-linux.md)和[Windows](../virtual-machines/extensions/key-vault-windows.md)的 Azure Key Vault 扩展
- 适用于[Linux](../virtual-machines/extensions/oms-linux.md)和[Windows](../virtual-machines/extensions/oms-windows.md)的 Log analytics 和监视扩展
- Azure 安全包

您可以通过打开支持请求来请求对其他发布服务器和/或扩展类型的支持。

## <a name="create-a-pool-with-extensions"></a>使用扩展创建池

下面的示例创建一个使用 Azure Key Vault 扩展的 Linux 节点的批处理池。

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

- 了解 [将应用程序和数据复制到池节点](batch-applications-to-pool-nodes.md)的各种方式。
- 了解有关使用 [节点和池](nodes-and-pools.md)的详细信息。
