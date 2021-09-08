---
title: 如何发布自定义来宾配置包项目
description: 了解如何将来宾配置包文件发布到 Azure blob 存储，并获取用于安全访问的 SAS 令牌。
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: aa20bc3d9c47258c6ebedd2419cf830ba47c1c1c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868311"
---
# <a name="how-to-publish-custom-guest-configuration-package-artifacts"></a>如何发布自定义来宾配置包项目

在开始之前，最好先阅读[来宾配置](../concepts/guest-configuration.md)的概述页面。

来宾配置自定义 .zip 包必须存储在托管计算机可通过 HTTPS 访问的位置。 示例包括 GitHub 存储库、Azure 存储库、Azure 存储或专用数据中心中的 Web 服务器。

支持 `Audit` 和 `AuditandSet` 的配置包以相同的方式发布。 在发布过程中，无需根据包模式执行任何特殊操作。

## <a name="publish-a-configuration-package"></a>发布配置包

用于存储配置包的首选位置是 Azure Blob 存储。
对存储帐户没有特殊要求，但最好将该文件托管在计算机附近的某个区域中。 如果不想公开包，可以在 URL 中包含 [SAS 令牌](../../../storage/common/storage-sas-overview.md)，或在专用网络中为计算机实现[服务终结点](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)。

如果没有存储帐户，请使用以下示例创建一个存储帐户。

```powershell
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

`Publish-GuestConfigurationPackage` 命令将配置包上传到 Azure Blob 存储并检索 SAS 令牌，以便可以安全地对其进行访问。

`Publish-GuestConfigurationPackage` cmdlet 的参数：

- **Path**：要发布的包的位置
- **ResourceGroupName**：存储帐户所在的资源组的名称
- **StorageAccountName**：应在其中发布包的存储帐户的名称
- **StorageContainerName**：（默认：guestconfiguration）存储帐户中的存储容器的名称
- **Force**：覆盖同名存储帐户中的现有包

以下示例将包发布到名为“guestconfiguration”的存储容器。

```powershell
Publish-GuestConfigurationPackage -Path ./MyConfig.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName | % ContentUri
```

## <a name="next-steps"></a>后续步骤

- 从开发环境[测试包项目](./guest-configuration-create-test.md)。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](./guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](./determine-non-compliance.md#compliance-details-for-guest-configuration)。
