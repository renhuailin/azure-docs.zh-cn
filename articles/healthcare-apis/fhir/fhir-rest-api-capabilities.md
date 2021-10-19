---
title: FHIR Rest API 功能，适用于 Azure 医疗保健 Api FHIR 服务
description: 本文介绍 Azure 医疗保健 Api FHIR 服务的 RESTful 交互和功能。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: bca8eb3714a685c9477f512a38406cb77f1c6886
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187400"
---
# <a name="fhir-rest-api-capabilities-for-azure-healthcare-apis-fhir-service"></a>FHIR Rest API 功能，适用于 Azure 医疗保健 Api FHIR 服务

在本文中，我们将介绍 Azure 医疗保健 Api FHIR service (特此称为 FHIR service) 的 RESTful 交互的一些细微差别。


## <a name="conditional-createupdate"></a>条件创建/更新

FHIR 服务支持由 FHIR 规范定义的创建、条件创建、更新和条件更新。 在这些方案中，有一个有用的标头是 [if-match](https://www.hl7.org/fhir/http.html#concurrency) 标头。 在 `If-Match` 进行更新之前，将使用标头并验证要更新的版本。 如果与 `ETag` 预期不符 `ETag` ，则会生成错误消息 *412 前提条件失败*。 

## <a name="delete"></a>删除

FHIR 规范定义的[删除](https://www.hl7.org/fhir/http.html#delete)在删除后，对资源执行的后续非特定于版本的读取将返回 410 HTTP 状态代码，并且不再通过搜索找到资源。 此外，FHIR 服务可让你完全删除 (包括资源) 所有历史记录。 若要完全删除资源，可将参数设置 `hardDelete` 传递为 true (`DELETE {server}/{resource}/{id}?hardDelete=true`)。 如果未传递此参数或将其设置 `hardDelete` 为 false，则资源的历史版本仍将可用。

> [!NOTE]
> 如果只想删除历史记录，FHIR 服务支持自定义操作， `$purge-history` 这允许你删除资源的历史记录。 

## <a name="conditional-delete"></a>条件删除

除了删除外，FHIR 服务还支持条件删除，这允许您传递搜索条件以删除资源。 默认情况下，条件删除允许您一次删除一个项。 你还可以指定 `_count` 参数，以便一次删除多达100项。 下面是使用条件删除的一些示例。

若要使用条件删除删除单个项，则必须指定返回单个项的搜索条件。

DELETE `https://{{hostname}}/Patient?identifier=1032704`

可以执行相同的搜索，但必须包括 hardDelete = true，才能删除所有历史记录。

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

如果要删除多个资源，可以包括 `_count=100` ，这将删除与搜索条件匹配的最多100个资源。

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>修补程序和条件修补程序

当你只需要更新 FHIR 资源的一部分时，修补程序是一个重要的 RESTful 操作。 使用 Patch 可以指定要在资源中更新的元素 () ，而无需更新整个记录。 FHIR 定义了三种类型的方法，可在 FHIR 中修补资源： JSON Patch、XML Patch 和 FHIR 路径修补程序。 FHIR 服务支持 JSON 修补和条件 JSON 修补 (，这允许你根据搜索条件而不是 ID) 修补资源。 若要演练使用 JSON 修补程序的一些示例，请参阅示例 [REST 文件](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http)。

> [!NOTE]
> 当使用 `PATCH` STU3 时，如果请求历史记录捆绑，则已修补的资源 `Bundle.entry.request.method` 将映射到 `PUT` 。 这是因为，STU3 不包含 `PATCH` [HTTPVerb 值集中](http://hl7.org/fhir/STU3/valueset-http-verb.html)谓词的定义。

### <a name="testing-patch"></a>测试修补程序

在修补程序中，有一个测试操作允许您在执行修补程序之前验证条件是否为 true。 例如，如果想要将患者设置为死者，则仅在其未标记为) 死者的情况下 (，你可以使用以下示例： 

修补 `http://{FHIR-SERVICE-NAME}/Patient/{PatientID}` 内容-类型： `application/json-patch+json`

```
[
    {
        "op": "test",
        "path": "/deceasedBoolean",
        "value": false
    },
    {
        "op": "replace",
        "path": "/deceasedBoolean",
        "value": true
    }
]

```

### <a name="patch-in-bundles"></a>包中的修补程序

默认情况下，捆绑资源中不支持 JSON 修补程序。 这是因为，捆绑包仅支持 FHIR 资源，JSON 修补程序不是 FHIR 资源。 若要解决此情况，我们将在执行捆绑包时，将具有内容类型的二进制资源视为 `"application/json-patch+json"` JSON 字符串的 base64 编码。 有关此解决方法的信息，请登录到 [Zulip](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request)。 

在下面的示例中，我们想要将患者上的性别更改为 "女性"。 我们已采用 JSON 修补程序 `[{"op":"replace","path":"/gender","value":"female"}]` ，并将其编码为 base64。

发布 `https://{FHIR-SERVICE-NAME}/` 内容-类型： `application/json`

```
{
    "resourceType": "Bundle",
    "id": "bundle-batch",
    "type": "batch",
    "entry": [
        {
            "fullUrl": "Patient/{PatientID}",
            "resource": {
                "resourceType": "Binary",
                "contentType": "application/json-patch+json",
                "data": "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            "request": { 
                "method": "PATCH",
                "url": "Patient/{PatientID}"
            }
        }
    ]
}

```

## <a name="next-steps"></a>后续步骤

本文介绍了 FHIR 服务的某些 REST 功能。 接下来，你可以了解有关在 FHIR 服务中搜索资源的关键方面的详细信息。 

>[!div class="nextstepaction"]
>[FHIR 搜索概述](overview-of-search.md)

 (FHIR&#174;) 是 [HL7](https://hl7.org/fhir/) 的注册商标，并与 HL7 的权限一起使用。


