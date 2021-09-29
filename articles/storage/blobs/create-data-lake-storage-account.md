---
title: 为 Azure Data Lake Storage Gen2 创建存储帐户
description: 了解如何创建与 Azure Data Lake Storage Gen2 配合使用的存储帐户。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 04/27/2021
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a2d9c23e5a6819c9e74cea10e78caab242d5cb35
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600316"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>创建与 Azure Data Lake Storage Gen2 配合使用的存储帐户

若要使用 Data Lake Storage Gen2 功能，请创建具有分层命名空间的存储帐户。

有关分步指南，请参阅[创建存储帐户](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)。

创建帐户时，请确保选择本文中所述的选项。

## <a name="choose-a-storage-account-type"></a>选择存储帐户类型

以下类型的存储帐户支持 Data Lake Storage 功能：

- 标准常规用途 v2
- 高级块 blob

有关如何在它们之间进行选择的信息，请参阅[存储帐户概述](../common/storage-account-overview.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)。

可以在“创建存储帐户”页面的“基本”选项卡中，选择这两种类型的帐户。

若要创建标准的常规用途 v2 帐户，请选择“标准”。

若要创建高级块 blob 帐户，请选择“高级”。 然后，在“高级帐户类型”下拉列表中，选择“块 blob”。

> [!div class="mx-imgBorder"]
> ![高级块 blob 选项](./media/create-data-lake-storage-account/premium-block-blob-option.png)

## <a name="enable-the-hierarchical-namespace"></a>启用分层命名空间

若要解锁 Data Lake Storage 功能，方法如下：在“创建存储帐户”页面的“高级”选项卡中启用“分层命名空间”设置  。 必须在创建帐户时启用此设置。 无法在以后启用它。

下图显示了“创建存储帐户”页中的此设置。

> [!div class="mx-imgBorder"]
> ![分层命名空间设置](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

如果你有一个要与 Data Lake Storage 配合使用的现有存储帐户，并且已禁用了分层命名空间设置，则必须将数据迁移到启用了该设置的新存储帐户。

> [!NOTE]
> 无法同时启用数据保护和分层命名空间。

## <a name="next-steps"></a>后续步骤

- [存储帐户概述](../common/storage-account-overview.md)
- [使用 Azure Data Lake Storage Gen2 满足大数据需求](data-lake-storage-data-scenarios.md)
- [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)
