---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657604"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>将托管标识添加到通信服务解决方案

### <a name="install-the-client-library-packages"></a>安装客户端库包

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>使用客户端库包

将以下 `import` 代码添加到代码以使用 Azure 标识。

```python
from azure.identity import DefaultAzureCredential
```

下面的示例使用的是 [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

若要在开发环境中注册应用程序并设置环境变量，请参阅 [使用托管标识授予访问权限](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>创建标识并颁发令牌

下面的代码示例演示如何创建具有托管标识的服务客户端对象，然后使用客户端为新用户颁发令牌：

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>使用 Azure 托管标识发送短信

下面的代码示例演示如何使用 Azure 托管标识创建服务客户端对象，然后使用客户端发送短信：

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解身份验证](../../concepts/authentication.md)

你可能还想要：

- [详细了解 Azure 基于角色的访问控制](../../../../articles/role-based-access-control/index.yml)
- [详细了解用于 Python 的 Azure 标识库](/net/api/overview/azure/identity-readme)
- [创建用户访问令牌](../../quickstarts/access-tokens.md)
- [发送短信](../../quickstarts/telephony-sms/send.md)
- [了解有关短信的详细信息](../../concepts/telephony-sms/concepts.md)