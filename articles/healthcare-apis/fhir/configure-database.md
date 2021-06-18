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
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283944"
---
# <a name="configure-database-settings"></a>配置数据库设置 

Azure API for FHIR 使用数据库来存储其数据。 基础数据库的性能取决于在服务预配期间或已预配服务后在数据设置中选择的请求单位 (RU) 数。

用于 FHIR 的 Azure API 借用 Cosmos DB 中的 ru 概念 (参阅 [Azure Cosmos DB) 中的请求单位](../../cosmos-db/request-units.md) ，设置基础数据库的性能。 

必须预配吞吐量，以确保始终有足够的系统资源可用于数据库。 您的应用程序所需的 ru 数取决于所执行的操作。 操作范围从简单的读取和写入到更复杂的查询。 

> [!NOTE]
> 由于不同的操作使用不同的 RU 数，我们返回响应标头的每个 API 调用中的实际使用的 RU 数。 这样，便可以分析应用程序使用的 ru 数。

## <a name="update-throughput"></a>更新吞吐量

若要在 Azure 门户中更改此设置，请导航到用于 FHIR 的 Azure API 并打开数据库边栏选项卡。 接下来，根据性能需求将预配的吞吐量更改为所需的值。 最多可以将值更改为 10000 RU/s。 如果需要更高的值，请联系 Azure 支持。

如果数据库吞吐量大于 10000 RU/s，或者数据库中存储的数据大于 50 GB，则客户端应用程序必须能够处理继续标记。 每增加 10000 RU/秒的吞吐量或存储的数据量大于 50 GB 时，将在数据库中创建新的分区。 多个分区创建一个多页面响应，其中使用继续标记实现分页。

> [!NOTE] 
> 较高的值意味着更高的 Azure API 用于 FHIR 的吞吐量和更高的服务成本。

![配置 Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何更新适用于 FHIR 的 Azure API 的 RUs。 若要了解如何将客户托管的密钥配置为数据库设置：

>[!div class="nextstepaction"]
>[配置客户管理的密钥](customer-managed-key.md)

或者，你可以部署用于 FHIR 的完全托管的 Azure API：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)