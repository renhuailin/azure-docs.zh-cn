---
title: Azure 数据工厂中的数据冗余 | Microsoft Docs
description: 了解 Azure 数据工厂中的元数据冗余机制
author: nabhishek
ms.reviewer: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 9d1c22b9ac6912f99838733a4326cb4082f49a6c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369690"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure 数据工厂数据冗余**

Azure 数据工厂数据包括元数据（管道、数据集、链接服务、集成运行时和触发器）以及监视数据（管道、触发器和活动运行）。 

在除巴西南部和东南亚) 以外的所有 (区域中，Azure 数据工厂数据将存储在 [配对区域](../best-practices-availability-paired-regions.md#azure-regional-pairs) 并进行复制，以防止元数据丢失。 在发生区域性数据中心故障时，Microsoft 可能会启动 Azure 数据工厂实例的区域故障转移。 在大多数情况下，你不需要执行任何操作。 Microsoft 管理的故障转移完成后，你将能够在故障转移区域中访问 Azure 数据工厂。 

由于巴西南部和东南亚的数据派驻要求，Azure 数据工厂数据仅存储在 [本地区域](../storage/common/storage-redundancy.md#locally-redundant-storage)。 对于东南亚，所有数据都存储在新加坡。 对于巴西南部，所有数据都存储在巴西。 当区域由于严重的灾难而丢失时，Microsoft 将无法恢复 Azure 数据工厂数据。  

> [!NOTE]
> Microsoft 管理的故障转移不适用于自承载集成运行时 (SHIR)，因为此基础结构通常由客户管理。 如果 SHIR 是在 Azure VM 上设置的，建议使用 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 来处理到另一个区域的 [Azure VM 故障转移](../site-recovery/azure-to-azure-architecture.md)。



## <a name="using-source-control-in-azure-data-factory"></a>**在 Azure 数据工厂中使用源代码管理**

若要确保能够跟踪和审核对 Azure 数据工厂元数据所做的更改，应考虑设置 Azure 数据工厂的源代码管理。 它还可让你访问管道、数据集、链接服务和触发器的元数据 JSON 文件。 使用 azure 数据工厂，可以 (Azure DevOps 和 GitHub) 使用不同的 Git 存储库。 

 了解如何 [在 Azure 数据工厂中设置源代码管理](./source-control.md)。 

> [!NOTE]
> 如果发生灾难 (会丢失区域) ，可以手动或以自动方式预配新的数据工厂。 创建新数据工厂后，可以从现有 Git 存储库还原管道、数据集和链接服务 JSON。 



## <a name="data-stores"></a>**数据存储**

使用 Azure 数据工厂可以在本地和云中的数据存储之间移动数据。 若要确保数据存储的业务连续性，应参阅这些数据存储中的每一个的业务连续性建议。 

 

## <a name="see-also"></a>请参阅

- [Azure 区域对](../best-practices-availability-paired-regions.md)
- [Azure 中的数据驻留](https://azure.microsoft.com/global-infrastructure/data-residency/)