---
title: Azure Data Lake Storage Gen1 与 Blob 存储的比较
description: 了解 Azure Data Lake Storage Gen1 和 Azure Blob 存储在一些大数据处理的关键方面之间的差异。
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: normesta
ms.openlocfilehash: 33e7ab0acdba7511561c141b08e67b4646c975e4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676022"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>比较 Azure Data Lake Storage Gen1 与 Azure Blob 存储

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

本文中的表总结了 Azure Data Lake Storage Gen1 和 Azure Blob 存储在一些大数据处理的关键方面之间的差异。 Azure Blob 存储是一个常规用途和可扩展的对象存储，适用于多种存储方案。 Azure Data Lake Storage Gen1 是一个针对大数据分析工作负荷进行了优化的超大规模存储库。

| 类别 | Azure Data Lake Storage Gen1 | Azure Blob 存储 |
| -------- | ---------------------------- | ------------------ |
| 目的 |大数据分析工作负荷的优化存储 |用于多种存储方案（包括大数据分析）的常规用途对象存储 |
| 用例 |Batch、交互式流分析和机器学习数据，例如日志文件、IoT 数据、点击流、大型数据集 |任何类型的文本或二进制数据，例如应用程序后端、备份数据、流式处理媒体存储和常规用途数据。 另外，还完全支持分析工作负荷；Batch、交互式流分析和机器学习数据，例如日志文件、IoT 数据、点击流、大型数据集 |
| 关键概念 |Data Lake Storage Gen1 帐户包含文件夹，而这些文件夹也会相应地将数据存储为文件 |存储帐户包含容器，而这些容器也会相应地包含 blob 形式的数据 |
| 结构 |分层文件系统 |具有平面命名空间的对象存储 |
| API |基于 HTTPS 的 REST API |基于 HTTP/HTTPS 的 REST API |
| 服务器端 API |[WebHDFS-compatible REST API](/rest/api/datalakestore/)（兼容 WebHDFS 的 REST API） |[Azure Blob Storage REST API](/rest/api/storageservices/Blob-Service-REST-API)（Azure blob 存储 REST API） |
| Hadoop 文件系统客户端 |是 |是 |
| 数据操作 - 身份验证 |基于 [Azure Active Directory 标识](../active-directory/develop/authentication-vs-authorization.md) |基于共享机密 - [帐户访问密钥](../storage/common/storage-account-keys-manage.md)和[共享访问签名密钥](../storage/common/storage-sas-overview.md)。 |
| 数据操作 - 身份验证协议 |[OpenID Connect](https://openid.net/connect/)。 调用必须包含 Azure Active Directory 发布的有效的 JWT（JSON Web 令牌）。|基于哈希的消息验证码 (HMAC)。 调用必须包含 Base64 编码的 SHA-256 哈希作为 HTTP 请求的一部分。 |
| 数据操作 - 授权 |POSIX 访问控制列表 (ACL)。  可在文件和文件夹级别设置基于 Azure Active Directory 标识的 ACL。 |对于帐户级别授权 – 使用[帐户访问密钥](../storage/common/storage-account-keys-manage.md)<br>对于帐户、容器 或 blob 授权 - 使用[共享访问签名密钥](../storage/common/storage-sas-overview.md) |
| 数据操作 - 审核 |可用。 详细信息参见[此处](data-lake-store-diagnostic-logs.md)。 |可用 |
| 静态数据加密 |<ul><li>透明版服务器端</li> <ul><li>服务托管密钥</li><li>Azure KeyVault 中客户托管的密钥</li></ul></ul> |<ul><li>透明版服务器端</li> <ul><li>服务托管密钥</li><li>Azure KeyVault 中客户托管的密钥（预览版）</li></ul><li>客户端加密</li></ul> |
| 管理操作（例如 Account Create） |用于帐户管理的 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) |用于帐户管理的 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) |
| Developer SDK |.NET、Java、Python、Node.js |.NET、Java、Python、Node.js、C++、Ruby、PHP、Go、Android、iOS |
| 分析工作负荷性能 |并行分析工作负荷的优化性能。 高吞吐量和 IOPS。 |并行分析工作负荷的优化性能。 |
| 大小限制 |无帐户大小、文件大小或文件数量限制 |有关具体的限制，请参阅[标准存储帐户的可伸缩性目标](../storage/common/scalability-targets-standard-account.md)和 [Blob 存储的可伸缩性和性能目标](../storage/blobs/scalability-targets.md)。 可以联系 [Azure 支持](https://azure.microsoft.com/support/faq/)来增大帐户限制阈值 |
| 异地冗余 |本地冗余（一个 Azure 区域中数据的多个副本） |本地冗余 (LRS)、区域冗余 (ZRS)、全局冗余 (GRS)、读取访问全局冗余 (RA-GRS)。 详细信息参见[此处](../storage/common/storage-redundancy.md) |
| 服务状态 |正式发布 |正式发布 |
| 区域可用性 |参见[此处](https://azure.microsoft.com/regions/#services) |在所有 Azure 区域中可用 |
| 价格 |参阅[定价](https://azure.microsoft.com/pricing/details/data-lake-store/) |参阅[定价](https://azure.microsoft.com/pricing/details/storage/) |