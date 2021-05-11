---
title: 了解如何将预留折扣应用到 Azure 存储服务 | Microsoft Docs
description: 了解如何将预留容量折扣应用到 Azure Blob 存储、Azure 文件存储和 Azure Data Lake Storage Gen2 资源。
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024024"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>了解如何将预留折扣应用到 Azure 存储服务 
使用 Azure 存储服务，可通过预留容量来节省存储成本。 Azure Blob 存储、Azure 文件存储和 Azure Data Lake Storage Gen2 支持预留实例。 购买预留容量后，预留折扣自动应用于与预留条款匹配的存储资源。 预留折扣仅适用于存储容量。 带宽和请求费率按即用即付费率收费。

有关 Azure Blob 存储和 Azure Data Lake Storage Gen2 预留容量的详细信息，请参阅[借助预留容量优化 Blob 存储的成本](../../storage/blobs/storage-blob-reserved-capacity.md)。 有关 Azure 文件存储预留容量的详细信息，请参阅[借助预留容量优化 Azure 文件存储的成本](../../storage/files/files-reserve-capacity.md)。

有关 Azure Blob 存储预留定价的信息，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)和 [Azure Data Lake Storage Gen2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)。 有关 Azure 文件存储预留定价的信息，请参阅 [Azure 文件存储定价](https://azure.microsoft.com/pricing/details/storage/files)。

## <a name="how-the-reservation-discount-is-applied"></a>如何应用预留折扣
预留容量折扣按小时应用于受支持的存储资源。

预留容量折扣是一种“不用就会失效”的折扣。 如果没有任何块 blob、Azure 文件共享或 Azure Data Lake Storage Gen2 资源达到某个小时的预留期限，则会丢失该小时的预留量。 不能结转未使用的预留小时数。

删除资源时，预留折扣自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失。

## <a name="discount-examples"></a>折扣示例
以下示例演示如何根据部署应用预留容量折扣。

假设你在美国西部 2 区域购买了一年期 100 TiB 的预留容量。 预留适用于热访问层中的本地冗余存储 (LRS) blob 存储。

假设此示例预留的成本为 18,540 美元。 你可以选择提前支付全部金额或者在接下来的十二个月每月固定分期付款 1,545 美元。

就这些示例而言，假设你已注册了按月预留付款计划。 以下场景说明预留容量未充分使用或过量使用会发生的情况。

### <a name="underusing-your-capacity"></a>未充分使用容量
假设在预留期内的某一个小时，你只使用了 100 TiB 预留容量中的 80 TiB。 剩余的 20 TiB 未在该小时应用，也不会继续结转。

### <a name="overusing-your-capacity"></a>超额使用容量
假设在预留期内的某一个小时，你使用了 101 TiB 的存储容量。 预留折扣应用于 100 TiB 的数据，该小时剩余的 1 TiB 按即用即付费率收费。 如果在接下来的一小时内，你的使用量变为 100 TiB，则预留涵盖所有使用量。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们
如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [借助预留容量优化 Blob 存储的成本](../../storage/blobs/storage-blob-reserved-capacity.md)
- [使用预留容量优化 Azure 文件存储的成本](../../storage/files/files-reserve-capacity.md)
- [什么是 Azure 预订？](save-compute-costs-reservations.md)
