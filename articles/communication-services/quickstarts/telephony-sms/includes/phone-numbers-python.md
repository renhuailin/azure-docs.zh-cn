---
ms.openlocfilehash: d07614d79410c388c5b93d635564e2720de7e11e
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113659518"
---
> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/phone-numbers-quickstart) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 或更高版本。
- 已部署的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

打开终端或命令窗口，为应用程序创建一个新目录，并导航到该目录。

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

使用文本编辑器在项目根目录下创建 phone_numbers_sample.py 文件，然后添加以下代码。 我们将在以下各个部分中添加剩余的快速入门代码。

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>安装包

仍在应用程序目录中时，使用 `pip install` 命令安装适用于 Python 包的 Azure 通信服务管理客户端库。

```console
pip install azure-communication-phonenumbers
```

## <a name="authenticate-the-phone-numbers-client"></a>对电话号码客户端进行身份验证

`PhoneNumbersClient` 可以使用 Azure Active Directory 身份验证。 使用 `DefaultAzureCredential` 对象是开始使用 Azure Active Directory 的最简单方法，你可以使用 `pip install` 命令进行安装。

```console
pip install azure-identity
```

创建 `DefaultAzureCredential` 对象要求你使用已注册 Azure AD 应用程序中的对应值将 `AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET` 和 `AZURE_TENANT_ID` 设置为环境变量。

若要快速了解如何获取这些环境变量，你可以使用 [CLI 快速入门中的设置服务主体](../../identity/service-principal-from-cli.md)。

安装 `azure-identity` 库后，可以继续对客户端进行身份验证。

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

此外，你还可以使用来自通信资源的终结点和访问密钥进行身份验证。

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>函数

`PhoneNumbersClient` 经过身份验证后，我们可以开始处理它可以执行的不同功能。

### <a name="search-for-available-phone-numbers"></a>搜索可用的电话号码

为了购买电话号码，必须首先搜索可用的电话号码。 若要搜索电话号码，请提供区号、分配类型、[电话号码功能](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)、[电话号码类型](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)和数量（默认数量为 1）。 请注意，对于免费电话号码类型，可以选择是否提供区号。

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>购买电话号码

电话号码的搜索结果是 `PhoneNumberSearchResult` 条。 此结果包含一个 `searchId`，可将其传递给采购编号 API 以获取搜索中的数量。 请注意，调用购买电话号码 API 将导致系统向你的 Azure 帐户收费。

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient,
    PhoneNumberCapabilityType,
    PhoneNumberAssignmentType,
    PhoneNumberType,
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>获取购买的电话号码

购买号码后，你可以从客户端进行检索。

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

你还可以检索所有购买的电话号码。

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>更新电话号码功能

你可以更新以前购买的电话号码功能。
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>发布电话号码

你可以发布购买的电话号码。

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>运行代码

在控制台提示下，导航到包含 phone_numbers_sample.py 文件的目录，然后执行以下 Python 命令来运行应用程序。

```console
python phone_numbers_sample.py
```