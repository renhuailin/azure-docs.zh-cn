---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: fc7157fad4227686ab4fddfe6ce3119119827c6b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723385"
---
| properties | 说明 |
|:--- |:---|
|**identity / type** | 用来发出请求的身份验证的类型。 例如 `OAuth`、`Kerberos`、`SAS Key`、`Account Key` 或 `Anonymous` |
|**identity / tokenHash**|用于请求的身份验证令牌的 SHA-256 哈希。 <br>如果身份验证类型为 `Account Key`，则格式为“key1 \| key2 (密钥的 SHA256 哈希)”。 例如：`key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`。 <br>如果身份验证类型为 `SAS Key`，则格式为“key1 \| key2 (密钥的 SHA 256 哈希),SasSignature(SAS 令牌的 SHA 256 哈希)”。 例如：`key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`。 如果身份验证类型为 `OAuth`，则格式为“OAuth 令牌的 SHA 256 哈希”。 例如：`B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DD25C`<br> 对于其他身份验证类型，没有 tokenHash 字段。 |
|**authorization / action** | 分配给请求的操作。 |
|**authorization / roleAssignmentId** | 角色分配 ID。 例如：`4e2521b7-13be-4363-aeda-111111111111`。|
|**authorization / roleDefinitionId** | 角色定义 ID。 例如：`ba92f5b4-2d11-453d-a403-111111111111"`。|
|**principals / id** | 安全主体的 ID。 例如：`a4711f3a-254f-4cfb-8a2d-111111111111`。|
|**principals / type** | 安全主体的类型。 例如：`ServicePrincipal`。 |
|**requester / appID** | 用作请求者的 Open Authorization (OAuth) 应用程序 ID。 <br> 例如：`d3f7d5fe-e64a-4e4e-871d-333333333333`。|
|**requester / audience** | 请求的 OAuth 受众。 例如：`https://storage.azure.com`。 |
|**requester / objectId** | 请求者的 OAuth 对象 ID。 对于 Kerberos 身份验证，此项表示已经过 Kerberos 身份验证的用户的对象标识符。 例如：`0e0bf547-55e5-465c-91b7-2873712b249c`。 |
|**requester / tenantId** | 标识的 OAuth 租户 ID。 例如：`72f988bf-86f1-41af-91ab-222222222222`。|
|**requester / tokenIssuer** | OAuth 令牌颁发者。 例如：`https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`。|
|**requester / upn** | 请求者的用户主体名称 (UPN)。 例如：`someone@contoso.com`。 |
|**requester / userName** | 此字段为保留字段，仅供内部使用。|
