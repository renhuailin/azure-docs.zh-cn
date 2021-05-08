---
title: Azure 数据目录开发人员示例
description: 本文概述了数据目录 REST API 可用的开发人员示例。
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 15b48bc41e230ca5b9003675e2caab25741bcbfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674761"
---
# <a name="azure-data-catalog-developer-samples"></a>Azure 数据目录开发人员示例

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

使用数据目录 REST API 开始开发 Azure 数据目录应用。 数据目录 REST API 是基于 REST 的 API，提供对数据目录资源的编程访问，以编程方式注册、批注和搜索数据资产。

## <a name="samples-available-on-githubcom"></a>可在 GitHub.com 上找到示例

* [Azure 数据目录入门](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   此入门示例演示如何通过 Azure AD 进行身份验证，以使用数据目录 REST API 注册、搜索和删除数据资产。
   
* [使用服务主体的 Azure 数据目录入门](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   此示例演示如何使用数据目录 REST API 注册、搜索和删除数据资产。 此示例使用服务主体身份验证。

* [用于 Azure 数据目录的导入/导出工具](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   此示例演示如何使用数据目录 REST API 从 Azure 数据目录中提取资产，并将它们序列化到某文件。 还会演示如何采用序列化为 JSON 的资产并将它们推送到目录。 它支持使用搜索查询导出目录的子集。

* [在 Azure 数据目录中批量注册和批注](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   此示例演示如何使用数据目录 REST API 和 Open XML 从 Excel 工作簿批量注册数据资产。
  
* [将术语表术语批量导入到 Azure 数据目录](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   此示例演示如何从 CSV 文件将术语表术语导入到 ADC 术语表。

* [将关系批量导入到 Azure 数据目录](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   此示例演示如何以编程方式将关系信息从 CSV 文件导入到数据目录中。

* [将关系发布到 Azure 数据目录](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   此示例演示如何以编程方式将关系信息发布到数据目录。
   
## <a name="next-steps"></a>后续步骤
[Azure 数据目录 REST API 参考](/rest/api/datacatalog/)
