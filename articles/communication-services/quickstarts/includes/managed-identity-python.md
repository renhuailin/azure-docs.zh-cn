---
ms.openlocfilehash: 50707b46445803ee27118ee72b90a237a3e76200
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020993"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>将托管标识添加到通信服务解决方案

### <a name="install-the-client-library-packages"></a>安装客户端库包

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>使用客户端库包

将以下 `import` 添加到代码中，以使用 Azure 标识。

```python
from azure.identity import DefaultAzureCredential
```

下面的示例使用的是 [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

若要在开发环境中注册应用程序并设置环境变量，请参阅[使用托管标识授予访问权限](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>创建标识并颁发令牌

下面的代码示例演示如何使用托管标识创建服务客户端对象，然后使用客户端为新用户颁发令牌：

```python
from azure.communication.identity import CommunicationIdentityClient 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>使用 Azure 托管标识发送短信

下面的代码示例演示如何使用 Azure 托管标识创建服务客户端对象，然后使用客户端发送短信：

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```
