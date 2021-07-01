---
title: 使用患者-Azure API for FHIR 中的所有内容
description: 本文介绍如何使用 Azure API for FHIR 中的 "患者-所有" 操作
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: ad7da9305d9bde65f7c393295b806957414a05d8
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135602"
---
# <a name="patient-everything-in-fhir"></a>FHIR 中的 Patient-everything

" [$Patient 所有](https://www.hl7.org/fhir/patient-operation-everything.html) " 操作用于提供患者，使其能够访问整个记录，或者提供给提供商或其他用户执行批量数据下载。 此操作用于返回与调用此操作的资源或上下文中描述的一个或多个患者相关的所有信息。  

## <a name="use-patient-everything"></a>使用患者-一切
若要调用患者，请使用以下命令：

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
适用于 FHIR 的 Azure API 验证它是否可以找到与提供的患者 ID 匹配的患者。 如果找到结果，响应将是类型为 "searchset" 的捆绑，其中包含以下信息： 
* [患者资源](https://www.hl7.org/fhir/patient.html) 
*  患者资源直接引用的资源 (除 link)  
*  [患者隔离舱](https://www.hl7.org/fhir/compartmentdefinition-patient.html)中的资源
*  引用患者资源的[设备资源](https://www.hl7.org/fhir/device.html)。 这限制为100设备。 如果患者具有超过100个设备的链接，则仅返回100。 


## <a name="patient-everything-parameters"></a>患者-所有参数
Azure API for FHIR 支持以下查询参数。 所有参数都是可选的：

|查询参数        |  说明|
|-----------------------|------------|
| \_type | 允许您指定响应中将包含的资源类型。 例如， \_ type = 会仅返回 `Encounter` 与患者关联的资源。 |
| \_since | 将仅返回自提供的时间以来修改的资源。 |
| start | 指定开始日期将拉取其临床日期在指定的开始日期之后的资源。 如果未提供开始日期，则在结束日期之前的所有记录都在范围内。 |
| end | 指定结束日期会在其临床日期早于指定结束日期的资源中拉取。 如果未提供结束日期，则开始日期之后的所有记录都在范围内。 |

> [!Note]
> 您必须指定特定患者的 ID。 如果需要所有患者的所有数据，请参阅 [$export](export-data.md)。 


## <a name="examples-of-patient-everything"></a>$Patient 的示例-所有 

下面是使用 $patient 所有操作的一些示例。 

若要使用 $patient-所有查询患者的 "一切"，2010和2020之间，请使用以下调用： 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

若要使用 $patient-若要查询患者的观察和遇到的内容，请使用以下调用： 
```json
GET {FHIRURL}/Patient/{ID}/$everything_type=Observation,Encounter 
```

若要使用 $patient-除了查询患者的 "所有内容"，自27T05：00：00Z 后，请使用以下调用： 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

如果找到每个调用的患者，会收到一个200响应，其中包含相应的资源的捆绑包。

## <a name="next-step"></a>后续步骤
现在，你已了解如何使用患者所有操作，你可以在搜索指南概述中了解更多搜索选项。

>[!div class="nextstepaction"]
>[FHIR 搜索概述](overview-of-search.md)