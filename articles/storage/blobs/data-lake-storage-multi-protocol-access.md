---
title: Azure Data Lake Storage 多协议访问 | Microsoft Docs
description: 使用 Blob API 和将 Blob API 与 Azure Data Lake Storage Gen2 配合使用的应用程序。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5f13c6c48ed8f6f0eaa6fcfed3a43fa5f4064e6e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481121"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage 的多协议访问

Blob API 现在兼容有分层命名空间的帐户。 这会对有分层命名空间的帐户解锁包含工具、应用程序、服务以及多个 Blob 存储功能在内的生态系统。

你可能一直必须保留独立的存储解决方案进行对象存储和分析存储，直到最近这种情况才发生改变。 这是因为 Azure Data Lake Storage Gen2 的生态系统支持有限。 它访问 Blob 服务功能（例如诊断日志记录）的权限也有限。 分段存储解决方案难以维护，因为必须在帐户之间移动数据才能完成各种方案。 你再也不需要这样做了。

可以借助 Data Lake Storage 多协议访问，使用由工具、应用程序和服务组成的生态系统来处理数据。 这还包括第三方工具和应用程序。 可以将它们指向具有分层命名空间的帐户，不需修改它们。 这些应用程序即使在调用 Blob API 的情况下也“按原样”运行，因为 Blob API 现在可以对具有分层命名空间的帐户中的数据进行操作。

Blob 存储功能（例如[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](storage-blob-storage-tiers.md)和 [Blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)）现在适用于具有分层命名空间的帐户。 因此，可以在 Blob 存储帐户上启用分层命名空间，不会失去对这些重要功能的访问权限。 

> [!NOTE]
> Data Lake Storage 多协议访问已公开发布，在所有区域提供。 多协议访问启用的某些 Azure 服务或 Blob 存储功能仍为预览版。  这些文章汇总了对 Blob 存储功能和 Azure 服务集成的当前支持。 
>
> [Azure 存储帐户中的 Blob 存储功能支持](storage-feature-support-in-storage-accounts.md)
>
>[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data Lake Storage 多协议访问的工作原理

Blob API 和 Data Lake Storage Gen2 API 可以对具有分层命名空间的存储帐户中的相同数据进行操作。 Data Lake Storage Gen2 通过分层命名空间来路由 Blob API，因此你可以获得一流目录操作和兼容 POSIX 的访问控制列表 (ACL) 的优势。 

![Data Lake Storage 多协议访问概念](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的现有工具和应用程序会自动获得这些优势。 开发人员不需修改它们。 Data Lake Storage Gen2 会以一致方式应用目录和文件级别 ACL，而不管工具和应用程序使用什么协议来访问数据。 

## <a name="see-also"></a>另请参阅

- [Azure 存储帐户中的 Blob 存储功能支持](storage-feature-support-in-storage-accounts.md)
- [支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)
- [支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)




