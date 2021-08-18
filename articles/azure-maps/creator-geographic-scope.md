---
title: Azure Maps Creator 服务地理范围
description: 了解 Azure Maps 中 Azure Maps Creator 服务的地理映射
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc, references_regions
ms.openlocfilehash: 1cd2c87f919e5dc68d4dfb7dcf6b38da9d9f67a2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725603"
---
# <a name="creator-service-geographic-scope"></a>Creator 服务地理范围

Azure Maps Creator 是一项设定了地理范围的服务。 Creator 提供了一个资源提供程序 API，该 API 在某个给定的 Azure 区域创建在该地理级别部署的 Creator 数据的实例。 从 Azure 区域到地理位置的映射在后台进行，如下表所述。 有关 Azure 区域和地理位置的更多详细信息，请参阅 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies)。

## <a name="data-locations"></a>数据位置

为了进行灾难恢复和获得高可用性，Microsoft 只能将客户数据复制到同一地理区域内的其他区域。 例如，欧洲西部的数据可以复制到欧洲北部，但不能复制到美国。  无论客户选择了哪个地域，Microsoft 都不会控制或限制客户或其最终用户通过 Azure Maps API 从其访问客户数据的位置。  

## <a name="geographic-and-regional-mapping"></a>地理和区域映射

下表描述了地理位置和受支持的 Azure 区域之间的映射，以及各自的地理 API 终结点。 例如，如果在位于美国地域内的“美国西部 2”区域中预配 Creator 帐户，则对转换服务的所有 API 调用都必须发送到 `us.atlas.microsoft.com/conversion/convert`。


| Azure 地理区域（地理位置） | Azure 数据中心（区域） | API 地理终结点 |
|------------------------|----------------------|-------------|
| 欧洲| 欧洲西部、欧洲北部 | eu.atlas.microsoft.com |
|美国 | 美国西部 2、美国东部 2 | us.atlas.microsoft.com |
