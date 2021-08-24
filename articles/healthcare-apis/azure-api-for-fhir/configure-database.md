---
title: 在 Azure API for FHIR 中配置数据库设置
description: 本文介绍如何在 Azure API for FHIR 中配置数据库设置
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: zxue
ms.openlocfilehash: 1874db9ba1cbefa4fb3bf749aeaccafc23171d37
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778501"
---
# <a name="configure-database-settings"></a>配置数据库设置 

Azure API for FHIR 使用数据库来存储其数据。 基础数据库的性能取决于在服务预配期间或已预配服务后在数据设置中选择的请求单位 (RU) 数。

在设置基础数据库的性能时，Azure API for FHIR 从 Cosmos DB 借用了“RU”这一概念（请参阅 [Azure Cosmos DB 中的请求单位](../../cosmos-db/request-units.md)）。 

必须预配吞吐量才能确保始终为数据库提供足够的系统资源。 应用程序需要多少 RU 取决于你执行的操作。 操作可以是简单的读取和写入，也可以是较复杂的查询。 

> [!NOTE]
> 由于不同操作使用的 RU 数不同，因此我们在响应头中返回每个 API 调用中使用的实际 RU 数。 这样，你便可以分析应用程序使用的 RU 数。

## <a name="update-throughput"></a>更新吞吐量

若要在 Azure 门户中更改此设置，请导航到 Azure API for FHIR 并打开“数据库”边栏选项卡。 接下来，请根据性能需求，将“预配的吞吐量”更改为所需的值。 可以更改该值，该值最大可以为 10,000 RU/秒。 如果需要更大的值，请联系 Azure 支持。

如果数据库吞吐量大于 10,000 RU/秒，或者数据库中存储的数据大于 50 GB，则客户端应用程序必须能够处理延续令牌。 每增加 10000 RU/秒的吞吐量时，或者存储的数据量大于 50 GB 时，系统就会在数据库中创建新的分区。 多个分区会产生一个多页面响应，在其中会使用延续令牌实现分页。

> [!NOTE] 
> 值越高意味着 Azure API for FHIR 吞吐量越高，服务成本越高。

![配置 Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何更新 Azure API for FHIR 的 RU。 若要了解如何将客户管理的密钥配置为数据库设置，请参阅：

>[!div class="nextstepaction"]
>[配置客户管理的密钥](customer-managed-key.md)

也可部署完全托管的 Azure API for FHIR：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)