---
title: Microsoft Azure IoT 设备预配服务中的安全性终结点
description: 概念 - 如何控制后端应用对 IoT 设备预配服务 (DPS) 的访问权限。 包括安全令牌的相关信息。
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: v-stharr
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cf2934c57441176034d28a7b60e33c639977e62d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779590"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>控制对 Azure IoT 中心设备预配服务的访问

本文介绍可用于保护 IoT 设备预配服务的选项。 预配服务使用身份验证和权限向每个终结点授予访问权限 。 权限使身份验证过程能够根据功能限制对服务实例的访问。

本文介绍：

* 由预配服务用来验证[服务和设备 REST API](/rest/api/iot-dps/) 的权限的身份验证过程和令牌。

* 可向后端应用授予的用来访问该服务 API 的不同权限。

## <a name="authentication"></a>身份验证

设备 API 支持基于密钥和基于 X.509 证书的设备身份验证。  

服务 API 支持对后端应用进行基于密钥的身份验证。  

使用基于密钥的身份验证时，设备预配服务将使用安全令牌对服务进行身份验证，以避免在线发送密钥。 并且安全令牌的有效期和范围有限。 Azure IoT 设备预配 SDK 无需任何特殊配置即可自动生成令牌。  

在某些情况下，可能需要直接使用 HTTP 设备预配服务 REST API，而不使用 SDK。 以下部分介绍如何直接对 REST API 进行身份验证。

## <a name="device-api-authentication"></a>设备 API 身份验证

设备使用[设备 API](/rest/api/iot-dps/device/runtime-registration) 向设备预配服务证明身份和接收 IoT 中心连接。

>[!NOTE]
>若要接收已经过身份验证的连接，首先必须通过注册在设备预配服务中注册设备。 使用服务 API 以编程方式通过注册来注册设备。

在预配过程中，设备必须向设备 API 进行身份验证。 设备的身份验证方法是在设置注册组或单个注册时定义的。 无论采用哪种身份验证方法，设备都必须向以下 URL 发出 HTTPS PUT 以预配自身。

```http
    https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01
```
如果使用基于密钥的身份验证，将在 HTTP 授权请求头中传递以下格式的安全令牌： ****  

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

### <a name="security-token-structure-for-key-based-authentication"></a>基于密钥的身份验证的安全令牌结构

安全令牌在 HTTP 授权请求头中以下面的格式传递： ****  

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

预期值为：

| 值  | 说明 |
|:-------|:------------|
| `{signature}`  |  `{URL-encoded-resourceURI} + "\n" + expiry` 格式的 HMAC-SHA256 签名字符串。重要说明：密钥从 base64 解码，并用作密钥来执行 HMAC-SHA256 计算 **** 。 |
| `{expiry}`  | 从纪元 1970 年 1 月 1日 00:00:00 UTC 时间至今秒数的 UTF8 字符串。  |
| `{URL-encoded-resourceURI}`   |`{ID_Scope}/registrations/{registration_id}` 的小写 URL 编码 |
| `{policyName}`  | 对于设备 API，此策略始终为“registration”。 |

以下 Python 代码片段演示了一个名为  `generate_sas_token` 的函数，该函数使用对称密钥身份验证类型从单个注册的输入  `uri`、`key`、`policy_name`、`expiry` 计算令牌。

```python

from base64 import b64encode, b64decode, encode 
from hashlib import sha256 
from time import time 
from urllib.parse import quote_plus, urlencode 
from hmac import HMAC 

 def generate_sas_token(uri, key, policy_name, expiry=3600): 
    ttl = time() + expiry 
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl)) 
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest()) 

    rawtoken = { 
        'sr' :  uri, 
        'sig': signature, 
        'se' : str(int(ttl)), 
        'skn' : policy_name 
    } 

    return 'SharedAccessSignature ' + urlencode(rawtoken) 

print(generate_sas_token("myIdScope/registrations/mydeviceregistrationid", "00mysymmetrickey", "registration"))

```

结果应类似于以下输出：

```bash

SharedAccessSignature sr=myIdScope%2Fregistrations%2Fmydeviceregistrationid&sig=SDpdbUNk%2F1DSjEpeb29BLVe6gRDZI7T41Y4BPsHHoUg%3D&se=1630175722&skn=registration 
```

以下示例演示了随后如何使用共享访问签名向设备 API 进行身份验证。  

```python

curl -L -i -X PUT -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -H 'Authorization: [token]' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

如果使用基于对称密钥的注册组，则首先需要使用注册组密钥生成一个 `device symmetric` 密钥。 使用注册组主密钥或辅助密钥来计算设备注册 ID 的 HMAC-SHA256。 然后，结果将转换为 Base64 格式以获取派生的设备密钥。 若要查看代码示例，请参阅[如何使用对称密钥注册组预配设备](how-to-legacy-device-symm-key.md?tabs=linux%22%20%5Cl%20%22derive-a-device-key)。 派生设备对称密钥后，可以使用前面的示例注册设备。

>[!WARNING]
>为了避免在设备代码中包含组主密钥，应在设备外部完成设备密钥派生过程。

### <a name="certificate-based-authentication"></a>基于证书的身份验证 

如果已经为基于 X.509 证书的身份验证设置了单个注册或注册组，则设备需要使用其颁发的 X.509 证书来向设备 API 证明身份。 有关如何设置注册和生成设备证书的信息，请参阅以下文章。

* 快速入门 - [使用 Python 将模拟的 X.509 设备预配到 Azure IoT 中心](quick-create-simulated-device-x509-python.md?tabs=linux)

* 快速入门 - [使用 Node.js 将模拟的 X.509 设备预配到 Azure IoT 中心](quick-create-simulated-device-x509-node.md)

* 快速入门 - [使用 Python 将 X.509 设备注册到 Azure 设备预配服务](quick-enroll-device-x509-python.md)

* 快速入门 - [使用 Node.js 将 X.509 设备注册到 Azure 设备预配服务](quick-enroll-device-x509-node.md)

以下示例演示在设置注册并颁发设备证书后，如何使用设备的 X.509 证书向设备 API 进行身份验证。

```bash

curl -L -i -X PUT –cert ./[device_cert].pem –key ./[device_cert_private_key].pem -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

## <a name="service-api-authentication"></a>服务 API 身份验证

[服务 API](/rest/api/iot-dps/service/device-registration-state) 用于检索注册状态和删除设备注册。 该服务也由后端应用用来以编程方式管理[单个组](/rest/api/iot-dps/service/individual-enrollment)和[注册组](/rest/api/iot-dps/service/enrollment-group)。 服务 API 支持对后端应用进行基于密钥的身份验证。  

必须拥有适当的权限才能访问任何服务 API 终结点。 例如，后端应用必须包含一个带安全凭据的令牌，以及它发送给该服务的每条消息。

Azure IoT 中心设备预配服务通过针对共享访问策略验证令牌，授予对终结点的访问权限。 安全凭据（例如对称密钥）永远不会通过网络发送。

### <a name="access-control-and-permissions"></a>访问控制和权限

可以通过以下方式授予 [权限](#device-provisioning-service-permissions) ：

* 共享访问授权策略。 共享访问策略可以授予任意[权限](#device-provisioning-service-permissions)组合。 可在 [Azure 门户][lnk-management-portal]中定义策略，也可使用[设备预配服务 REST API][lnk-resource-provider-apis] 以编程方式进行定义。 新建的预配服务有以下默认策略：

* provisioningserviceowner：包含所有权限的策略。 有关详细信息，请参阅[权限](#device-provisioning-service-permissions)。

> [!NOTE]
> 如同 [Azure 资源管理器][lnk-azure-resource-manager]中的所有提供程序一样，设备预配服务资源提供程序也通过 Azure 订阅受到保护。

有关如何构造和使用安全令牌的详细信息，请参阅下一部分。

HTTP 是唯一受支持的协议，它通过在“Authorization”请求标头中包含有效的令牌来实现身份验证。

#### <a name="example"></a>示例

```bash
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Azure IoT 设备预配服务 SDK][lnk-sdks] 在连接到服务时自动生成令牌。

### <a name="security-tokens"></a>安全令牌

设备预配服务使用安全令牌对服务进行身份验证，以避免在线发送密钥。 并且安全令牌的有效期和范围有限。 [Azure IoT 设备预配服务 SDK][lnk-sdks] 无需任何特殊配置即可自动生成令牌。 在某些情况下，确实需要用户生成并直接使用安全令牌。 这种情况包括直接使用 HTTP。

### <a name="security-token-structure"></a>安全令牌结构

可使用安全令牌向服务授予限时访问 IoT 设备预配服务中特定功能的权限。 要获取连接到预配服务的权限，服务必须发送使用共享访问或对称密钥进行签名的安全令牌。

使用共享访问密钥进行签名的令牌可授权访问与共享访问策略权限相关的所有功能。 

安全令牌采用以下格式：

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

以下是预期值：

| 值 | 说明 |
| --- | --- |
| {signature} |HMAC-SHA256 签名字符串的格式为： `{URL-encoded-resourceURI} + "\n" + expiry`。 重要说明：密钥是从 base64 解码的，用作密钥来执行 HMAC-SHA256 计算。|
| {expiry} |从纪元 1970 年 1 月 1日 00:00:00 UTC 时间至今秒数的 UTF8 字符串。 |
| {URL-encoded-resourceURI} | 小写资源 URI 的小写 URL 编码。 此令牌可访问的终结点的 URI 前缀（按分段），以 IoT 设备预配服务的主机名开头（无协议）。 例如，`mydps.azure-devices-provisioning.net`。 |
| {policyName} |此令牌所引用的共享访问策略名称。 |

>[!NOTE]
>URI 前缀是按分段而不是按字符计算的。 例如，`/a/b` 是 `/a/b/c` 的前缀，而不是 `/a/bc` 的前缀。

以下 Node.js 代码片段显示名为 **generateSasToken** 的函数，该函数通过输入 `resourceUri, signingKey, policyName, expiresInMins` 计算令牌。 以下各节将详细讲解如何初始化不同令牌用例的不同输入。

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

作为对照，用于生成安全令牌的等效 Python 代码是：

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> 由于 IoT 设备预配服务计算机会验证令牌的有效期，因此生成令牌的计算机的时间偏差必须很小。

### <a name="use-security-tokens-from-service-components"></a>使用服务组件提供的安全令牌

如前所述，服务组件使用共享访问策略只能生成安全令牌，授予适当权限。

以下是终结点上显示的服务功能：

| 端点 | 功能 |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |向设备注册操作提供设备预配服务。 |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |提供用于管理设备注册组的操作。 |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |提供用于检索和管理设备注册状态的操作。 |


例如，使用名为 enrollmentread 的预创建共享访问策略生成的服务将使用以下参数创建令牌`enrollmentread`：

* 资源 URI： `{mydps}.azure-devices-provisioning.net`，
* 签名密钥： `enrollmentread` 策略的密钥之一，
* 策略名称： `enrollmentread`，
* 任何过期 time.backn

![在门户中为设备预配服务实例创建共享访问策略][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

授权读取所有注册记录的结果将是：

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>参考主题：

以下参考主题详细删除如何控制对 IoT 设备预配服务的访问。

### <a name="device-provisioning-service-permissions"></a>设备预配服务权限

下表列出了可用于控制对 IoT 设备预配服务的访问的权限。

| 权限 | 注释 |
| --- | --- |
| **ServiceConfig** |授予用于更改服务配置的访问权限。 <br/>后端云服务将使用此权限。 |
| EnrollmentRead |授予对设备注册和注册组的读取访问权限。 <br/>后端云服务将使用此权限。 |
| EnrollmentWrite |授予对设备注册和注册组的写入访问权限。 <br/>后端云服务将使用此权限。 |
| RegistrationStatusRead |授予对设备注册状态的读取访问权限。 <br/>后端云服务将使用此权限。 |
| RegistrationStatusWrite  |授予对设备注册状态的删除访问权限。 <br/>后端云服务将使用此权限。 |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/