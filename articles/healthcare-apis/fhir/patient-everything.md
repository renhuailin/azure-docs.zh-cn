---
title: 在中使用 patient-everything Azure API for FHIR
description: 本文介绍如何在 Azure API for FHIR 中使用 patient-everything Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: b02a2b582130c439298653d7c2a0512edbb8a23b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756358"
---
# <a name="patient-everything-in-fhir"></a>FHIR 中的 Patient-everything

创建 [$patient一](https://www.hl7.org/fhir/patient-operation-everything.html) 切操作，使患者能够访问其整个记录，或者让提供商或其他用户执行批量数据下载。 此操作用于返回与调用此操作的资源或上下文中所述的一个或多个患者有关的所有信息。  

## <a name="use-patient-everything"></a>使用 patient-everything
若要调用 patient-everything，请使用以下命令：

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
该Azure API for FHIR验证它能否找到与提供的患者 ID 匹配的患者。 如果找到结果，则响应将是包含以下信息的"searchset"类型的捆绑包： 
* [患者资源](https://www.hl7.org/fhir/patient.html) 
*  由患者资源组直接引用的资源 (链接)  
*  患者隔离舱中的 [资源](https://www.hl7.org/fhir/compartmentdefinition-patient.html)
*  [引用患者](https://www.hl7.org/fhir/device.html) 资源的设备资源。 请注意，这限制为 100 台设备。 如果患者有 100 多个设备链接到这些设备，则仅返回 100 台设备。 

 
> [!Note]
> $patient -所有内容现已在 Cosmos DB 支持的开源 FHIR 服务器中提供，Azure API for FHIR 7 月 1 日之前在 Azure API for FHIR 中提供。 FHIR 服务器的功能语句缺少对 $patient-everything 的支持，此处跟踪：问题[1989。](https://github.com/microsoft/fhir-server/issues/1989) 


## <a name="patient-everything-parameters"></a>Patient-everything 参数
Azure API for FHIR 支持以下查询参数。 所有参数都是可选的：

|查询参数        |  说明|
|-----------------------|------------|
| \_type | 允许你指定响应中将包含的资源类型。 例如 \_ ，type=Encounter 将仅 `Encounter` 返回与患者关联的资源。 |
| \_since | 将仅返回自提供的时间以来已修改的资源。 |
| start | 指定开始日期将拉取临床日期在指定的开始日期之后的资源。 如果未提供开始日期，则结束日期之前的所有记录都位于范围内。 |
| end | 指定结束日期将拉取临床日期在指定的结束日期之前的资源。 如果未提供结束日期，则开始日期之后的所有记录都位于范围内。 |

> [!Note]
> 必须为特定患者指定 ID。 如果需要所有患者的所有数据 [，请参阅](export-data.md)$export。 


## <a name="examples-of-patient-everything"></a>所有$patient示例 

下面是一些使用"全部$patient的其他示例。 

若要$patient 2010 到 2020 年之间查询患者"所有内容"，请使用以下调用： 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

若要使用 $patient-everything 来查询患者观察和遇到，请使用以下调用： 
```json
GET {FHIRURL}/Patient/{ID}/$everything_type=Observation,Encounter 
```

若要使用 $patient-everything 查询患者自 2021-05-27T05：00：00Z 以来的"所有内容"，请使用以下调用： 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

如果找到每个呼叫的"患者"，将返回 200 响应，并包含相应资源的捆绑包。

## <a name="next-step"></a>后续步骤
了解如何使用 patient-everything 操作后，可以在搜索指南概述中了解更多搜索选项。

>[!div class="nextstepaction"]
>[FHIR 搜索概述](overview-of-search.md)