---
title: 为 Azure Data Lake Storage Gen2 创建存储帐户
description: 了解如何创建与 Azure Data Lake Storage Gen2 配合使用的存储帐户。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624331"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>创建与 Azure Data Lake Storage Gen2 配合使用的存储帐户

若要使用 Data Lake Storage Gen2 功能，请创建具有分层命名空间的存储帐户。

## <a name="choose-a-storage-account-type"></a>选择存储帐户类型

以下类型的存储帐户支持 Data Lake Storage 功能：

- 常规用途 v2
- BlockBlobStorage

有关如何在它们之间进行选择的信息，请参阅[存储帐户概述](../common/storage-account-overview.md)。

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>创建具有分层命名空间的存储帐户

在启用“分层命名空间”设置的情况下创建[常规用途 V2 帐户](../common/storage-account-create.md)或 [BlockBlobStorage](storage-blob-create-account-block-blob.md) 帐户。

在创建帐户时解锁 Data Lake Storage 功能，方法如下：在“创建存储帐户”页面的“高级”选项卡中启用“分层命名空间”设置。   必须在创建帐户时启用此设置。 无法在以后启用它。

下图显示了“创建存储帐户”页中的此设置。

> [!div class="mx-imgBorder"]
> ![分层命名空间设置](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

如果你有一个要与 Data Lake Storage 配合使用的现有存储帐户，并且已禁用了分层命名空间设置，则必须将数据迁移到启用了该设置的新存储帐户。

> [!NOTE]
> 无法同时启用数据保护和分层命名空间 。

## <a name="next-steps"></a>后续步骤

- [存储帐户概述](../common/storage-account-overview.md)
- [使用 Azure Data Lake Storage Gen2 满足大数据需求](data-lake-storage-data-scenarios.md)
- [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)
