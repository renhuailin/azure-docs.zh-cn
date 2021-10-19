---
title: 患者-一切
description: 本文介绍如何使用患者的所有操作
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: 3aaed02f7de7e8a4495415b15d893fd7b357fc63
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595002"
---
# <a name="using-patient-everything-in-fhir-service"></a>使用 $patient-FHIR 服务中的所有内容

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[$patient-everything](https://www.hl7.org/fhir/patient-operation-everything.html) 操作用于为患者提供对其完整记录的访问权限，或让提供商或其他用户能够批量下载数据。 此操作用于返回与调用此操作的资源或上下文中所述的一个或多个患者相关的所有信息。  

## <a name="use-patient-everything"></a>使用 patient-everything
若要调用 patient-everything，请使用以下命令：

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Azure 医疗保健 Api 中的 FHIR 服务 (特此称为 FHIR 服务) 验证它是否可以找到与提供的患者 ID 匹配的患者。 如果找到相关结果，则响应将是包含以下信息的“searchset”类型的捆绑包： 
* [患者资源](https://www.hl7.org/fhir/patient.html) 
* 患者资源直接引用的资源（链接除外） 
* [患者隔离舱](https://www.hl7.org/fhir/compartmentdefinition-patient.html)中的资源
* 引用患者资源的[设备资源](https://www.hl7.org/fhir/device.html)。 限制为 100 台设备。 如果患者关联的设备超过 100 台，则仅返回 100 台设备的信息。 


## <a name="patient-everything-parameters"></a>patient-everything 参数
FHIR 服务支持以下查询参数。 所有参数都是可选的：

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
GET {FHIRURL}/Patient/{ID}/$everything?_type=Observation,Encounter 
```

若要使用 $patient-everything 查询患者自 2021-05-27T05:00:00Z 以来的“所有内容”，请使用以下调用： 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

如果找到每个调用的患者，你会收到一个包含 `Bundle` 相应资源的200响应。

## <a name="next-step"></a>下一步
现在，你已了解如何使用 patient-everything 操作，你还可以在搜索指南概述中了解更多搜索选项。

>[!div class="nextstepaction"]
>[FHIR 搜索概述](overview-of-search.md)
