---
title: 在 Azure API for FHIR 中使用 patient-everything
description: 本文介绍如何在 Azure API for FHIR 中使用 patient-everything 操作
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: 935084c12551dcbebecf3f10bb0e6eff19eff45f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778682"
---
# <a name="patient-everything-in-fhir"></a>FHIR 中的 Patient-everything

[$patient-everything](https://www.hl7.org/fhir/patient-operation-everything.html) 操作用于为患者提供对其完整记录的访问权限，或让提供商或其他用户能够批量下载数据。 此操作用于返回与调用此操作的资源或上下文中所述的一个或多个患者相关的所有信息。  

## <a name="use-patient-everything"></a>使用 patient-everything
若要调用 patient-everything，请使用以下命令：

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Azure API for FHIR 验证其是否能找到与所提供的患者 ID 匹配的患者。 如果找到相关结果，则响应将是包含以下信息的“searchset”类型的捆绑包： 
* [患者资源](https://www.hl7.org/fhir/patient.html) 
*  患者资源直接引用的资源（链接除外） 
*  [患者隔离舱](https://www.hl7.org/fhir/compartmentdefinition-patient.html)中的资源
*  引用患者资源的[设备资源](https://www.hl7.org/fhir/device.html)。 限制为 100 台设备。 如果患者关联的设备超过 100 台，则仅返回 100 台设备的信息。 


## <a name="patient-everything-parameters"></a>patient-everything 参数
Azure API for FHIR 支持以下查询参数。 所有参数都是可选的：

|查询参数        |  说明|
|-----------------------|------------|
| \_type | 允许指定要包含在响应中的资源类型。 例如，\_type=Encounter 将仅返回与患者相关联的 `Encounter` 资源。 |
| \_since | 仅返回在所提供时间后修改的资源。 |
| start | 指定开始日期将拉取那些临床日期晚于指定开始日期的资源。 如果未提供开始日期，那么结束日期前的所有记录都处于范围内。 |
| end | 指定结束日期将拉取那些临床日期早于指定结束日期的资源。 如果未提供结束日期，那么开始日期后的所有记录都将处于范围内。 |

> [!Note]
> 必须指定特定患者的 ID。 如果需要所有患者的所有数据，请参阅 [$导出](../data-transformation/export-data.md)。 


## <a name="examples-of-patient-everything"></a>$patient-everything 示例 

下面是使用 $patient-everything 操作的一些示例。 

若要使用 $patient-everything 查询患者 2010 到 2020 年间的“所有内容”，请使用以下调用： 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

若要使用 $patient-everything 查询患者的“观察结果”和“所遇问题”，请使用以下调用： 
```json
GET {FHIRURL}/Patient/{ID}/$everything_type=Observation,Encounter 
```

若要使用 $patient-everything 查询患者自 2021-05-27T05:00:00Z 以来的“所有内容”，请使用以下调用： 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

如果找到每个调用的患者，则会返回一个包含相应资源捆绑包的 200 响应。

## <a name="next-step"></a>下一步
现在，你已了解如何使用 patient-everything 操作，你还可以在搜索指南概述中了解更多搜索选项。

>[!div class="nextstepaction"]
>[FHIR 搜索概述](overview-of-search.md)