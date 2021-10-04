---
title: 加密实验室使用的 Azure 存储帐户
description: 了解如何配置 Azure 开发测试实验室中的实验室使用的 Azure 存储的加密
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 337cd45b20b9fe52bb487a3b36bde905541f840b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552578"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>加密 Azure 开发测试实验室中的实验室使用的 Azure 存储
在 Azure 开发测试实验室中创建的每个实验室都是使用关联的 Azure 存储帐户创建的。 存储帐户用于以下用途： 

- 存储可以用来创建虚拟机的[公式](devtest-lab-manage-formulas.md)文档。
- 存储项目结果，包括在应用项目时生成的部署和扩展日志。 
- [上传虚拟硬盘 (VHD) 以在实验室中创建自定义映像。](devtest-lab-create-template.md)
- 缓存常用的[项目](add-artifact-vm.md)和 [Azure 资源管理器模板](devtest-lab-create-environment-from-arm.md)，以在虚拟机/环境创建过程中更快地进行检索。

> [!NOTE]
> 上述信息对于实验室的运行至关重要。 除非显式删除，否则会存储上述信息以在实验室（和实验室资源）的整个生命周期中使用。 手动删除这些资源可能会导致创建实验室 VM 时出错，以及/或者公式损坏，无法在将来使用。 

## <a name="locate-the-storage-account-and-view-its-contents"></a>找到存储帐户并查看其内容

1. 在实验室的“主页”上，选择“概述”页上的“资源组” 。 你应会看到包含实验室的资源组的“资源组”页。 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="在“概述”页上选择资源组":::
1. 选择实验室的 Azure 存储帐户。 实验室存储帐户的命名约定为 `a<labNameWithoutInvalidCharacters><4-digit number>`。 例如，如果实验室名称为 `contosolab`，则存储帐户名称可以为 `acontosolab7576`。 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="在实验室的资源组中选择存储帐户":::
3. 在“存储帐户”页的左侧菜单上选择“存储资源管理器(预览版)”，然后选择“BLOB 容器”以查找与实验室相关的相关内容。 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="存储资源管理器（预览版）" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>加密实验室存储帐户
在将数据保存到云时，Azure 存储会自动加密数据。 Azure 存储加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 有关详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。

实验室存储帐户中的数据使用 Microsoft 管理的密钥进行加密。 可以依赖于使用 Microsoft 托管的密钥来加密数据，也可以使用你自己的密钥来管理加密。 如果你选择使用自己的密钥来管理实验室的存储帐户的加密，则可以使用 Azure Key Vault 指定客户管理的密钥，以便在 Blob 存储和 Azure 文件存储中对数据进行加密/解密。 有关客户管理的密钥的详细信息，请参阅[将客户管理的密钥与 Azure Key Vault 配合使用来管理 Azure 存储加密](../storage/common/customer-managed-keys-overview.md)。

若要了解如何为 Azure 存储加密配置客户管理的密钥，请参阅以下文章： 

- [Azure 门户](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>管理 Azure Blob 存储生命周期
如前所述，实验室的存储帐户中存储的信息对于实验室无错运行至关重要。 除非显式删除，否则，在实验室或特定实验室虚拟机的生命周期内，此数据将持续保留在实验室的存储帐户中，具体取决于数据类型。

### <a name="uploaded-vhds"></a>已上传的 VHD
这些 VHD 用来创建自定义映像。 删除它们会导致无法再从这些 VHD 创建自定义映像。

### <a name="artifacts-cache"></a>项目缓存
每次应用项目时都会重新创建这些缓存。 它们将使用各自引用的存储库中的最新内容进行刷新。 因此，如果你删除此信息以节省与存储相关的费用，效果将是暂时的。

### <a name="azure-resource-manager-template-cache"></a>Azure 资源管理器模板缓存
每次在实验室中连接并启动基于 Azure 资源管理器的模板存储库时，都会重新创建这些缓存。 它们将使用各自引用的存储库中的最新内容进行刷新。 因此，如果你删除此信息以节省与存储相关的费用，效果将是暂时的。

### <a name="formulas"></a>公式
这些文档用于支持从现有 VM 创建公式以及从公式创建 VM 的选项。 删除这些公式文档可能会导致在执行以下操作时出错：

- 从现有实验室 VM 创建公式
- 创建或更新公式 
- 从公式创建 VM。

### <a name="artifact-results"></a>项目结果
应用项目时，各个项目结果的大小可能会随着时间的推移而增大，具体取决于实验室 VM 上运行的项目的数量和类型。 因此，作为实验室所有者，你可能需要控制此类文档的生命周期。 有关详细信息，请参阅[管理 Azure Blob 存储生命周期](../storage/blobs/lifecycle-management-overview.md)。

> [!IMPORTANT]
> 建议执行此步骤，以减少与 Azure 存储帐户关联的费用。 

例如，以下规则用于专门为项目结果设置一个 90 天过期规则。 它确保定期从存储帐户中回收较旧的项目结果。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤
若要了解如何为 Azure 存储加密配置客户管理的密钥，请参阅以下文章： 

- [Azure 门户](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)