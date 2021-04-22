---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450396"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>将托管标识添加到通信服务解决方案

### <a name="install-the-sdk-packages"></a>安装 SDK 包

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>使用 SDK 包

将以下 `import` 添加到代码中，以使用 Azure 标识。

```python
from azure.identity import DefaultAzureCredential
```

下面的示例使用的是 [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

有关立即使用托管标识身份验证的简单方法，请参阅[使用托管标识授予访问权限](../managed-identity-from-cli.md)

若要深入了解 DefaultAzureCredential 对象如何工作以及如何通过本快速入门中未指定的方式来使用该对象，请参阅[适用于 Python 的 Azure 标识客户端库](https://docs.microsoft.com/python/api/overview/azure/identity-readme)

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

### <a name="list-all-your-purchased-phone-numbers"></a>列出你已购买的所有电话号码

下面的代码示例演示如何使用 Azure 托管标识创建服务客户端对象，然后使用该客户端来查看该资源拥有的所有已购买的电话号码：

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```