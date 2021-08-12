---
title: 在 Service Fabric 托管群集中使用应用程序机密
description: 了解 Azure Service Fabric 应用程序机密以及如何收集在托管群集中使用所需的信息
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 820fb2a116ba5343a2f2126950a7f5d5896ddee3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950118"
---
# <a name="use-application-secrets-in-service-fabric-managed-clusters"></a>在 Service Fabric 托管群集中使用应用程序机密

机密可以是任何敏感信息，例如存储连接字符串、密码或其他不应以明文形式处理的值。 本文使用 Azure Key Vault 来管理密钥和机密，因为它适用于 Service Fabric 托管群集。 但是，在应用程序中 *使用* 机密的方式不区分云平台，因此可让应用程序部署到托管在任何位置的群集。

建议通过[服务配置包][config-package]来管理服务配置设置。 可以通过包含运行状况验证和自动回滚的托管滚动升级机制来控制配置包版本以及对其进行更新。 这比全局配置更有优势，因为可以减少全局服务中断的可能性。 加密的机密也不例外。 通过 Service Fabric 的内置功能，可以使用证书加密来加密和解密配置包 Settings.xml 文件中的值。

下图演示了 Service Fabric 应用程序中机密管理的基本流程：

![机密管理概述][overview]

此流程包括四个主要步骤：

1. 获取数据加密证书。
2. 在群集中安装证书。
3. 在部署应用程序时使用证书加密机密值，并将其注入服务的 Settings.xml 配置文件。
4. 通过使用相同的加密证书进行解密，从 Settings.xml 中读取加密值。 

[Azure Key Vault][key-vault-get-started] 在此处用作证书的安全存储位置，也可用于将证书安装在 Azure 中的 Service Fabric 托管群集节点上。

有关如何实施应用程序机密的示例，请参阅[管理应用程序机密](service-fabric-application-secret-management.md)。

另外，我们也支持 [KeyVaultReference](service-fabric-keyvault-references.md)。 通过 Service Fabric KeyVaultReference 支持，只需通过引用存储在 Key Vault 中的机密 URL，就可以轻松地将机密部署到应用程序

## <a name="create-a-data-encipherment-certificate"></a>创建数据加密证书
若要创建自己的 Key Vault 并设置证书，请使用 [Azure CLI、PowerShell、门户等][key-vault-certs]按照 Azure Key Vault 中的说明进行操作。

>[!NOTE]
> 必须[针对模板部署启用](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) Key Vault，使计算资源提供程序能够从中获取证书并将其安装在群集节点上。

## <a name="install-the-certificate-in-your-cluster"></a>在群集中安装证书
此证书必须安装在群集中的每个节点上，Service Fabric 托管群集可帮助简化此过程。 托管集群服务可以将特定于版本的机密推送到节点，以帮助安装不会经常更改的机密，例如向节点安装专用根 CA。 对于大多数生产工作负载，我们建议使用 [KeyVault 扩展][key-vault-windows]。 Key Vault VM 扩展提供 Azure Key Vault 中存储的证书的自动刷新和静态版本。

对于托管群集，你需要三个值，两个来自 Azure Key Vault，还有一个是节点上的本地存储名称（由你决定）。

参数： 
* 源保管库：这是 
    * 例如：/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1
* 证书 URL：这是完整的对象标识符，不区分大小写且不可变
    * https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}
* 证书存储：这是节点上将放置证书的本地证书存储
    * 节点上的证书存储名称，例如：“MY”

Service Fabric 托管群集支持两种将特定于版本的机密添加到节点的方法。

1. 仅在初始群集创建期间，使用门户将上述值插入此区域：

![门户机密输入][sfmc-secrets]

2. 在创建期间或任何时候，使用 Azure 资源管理器

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "properties": {
        "vmSecrets": [
          {
            "sourceVault": {
              "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
            },
            "vaultCertificates": [
              {
                "certificateStore": "MY",
                "certificateUrl": "https://mykeyvault1.vault.azure.net/certificates/{certificatename}/{secret-version}"
              }
            ]
          }
        ]
    }    
}
```


<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[key-vault-certs]: ../key-vault/certificates/quick-create-cli.md
[config-package]: service-fabric-application-and-service-manifests.md
[key-vault-windows]: ../virtual-machines/extensions/key-vault-windows.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
[sfmc-secrets]:./media/how-to-managed-cluster-application-secrets/sfmc-secrets.png