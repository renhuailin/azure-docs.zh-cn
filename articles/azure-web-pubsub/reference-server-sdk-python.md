---
title: 参考 - 适用于 Azure Web PubSub 服务的 Python 服务器 SDK
description: 该参考介绍适用于 Azure Web PubSub 服务的 Python 服务器 SDK
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: c394258a49e73055f6848eed87dd15e75bd1c069
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113556"
---
# <a name="python-server-sdk-for-azure-web-pubsub-service"></a>适用于 Azure Web PubSub 服务的 Python 服务器 SDK

库有以下用途：

- 将消息发送到中心和组。
- 将消息发送到特定用户和连接。
- 将用户和连接整理到组中。
- 关闭连接
- 授予/撤销/检查现有连接的权限

[源代码](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [包 (Pypi)][package] | [API 参考文档](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [产品文档][webpubsubservice_docs] |
[示例][samples_ref]

## <a name="getting-started"></a>入门

### <a name="installations-the-package"></a>安装包

```bash
python -m pip install azure-messaging-webpubsubservice
```

#### <a name="prerequisites"></a>先决条件

- 使用此包需要 Python 2.7、3.6 或更高版本。
- 需要 [Azure 订阅][azure_sub]和 [Azure WebPubSub 服务实例][webpubsubservice_docs]才能使用此包。
- 一个现有的 Azure Web PubSub 服务实例。

### <a name="authenticating-the-client"></a>对客户端进行身份验证

若要与 Azure WebPubSub 服务交互，需要创建 [WebPubSubServiceClient][webpubsubservice_client_class] 类的实例。 若要对该服务进行身份验证，需要将 AzureKeyCredential 实例与终结点和访问密钥一起传递。 可在 Azure 门户上找到终结点和访问密钥。

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> client
<WebPubSubServiceClient endpoint:'<endpoint>'>
```

## <a name="examples"></a>示例

### <a name="sending-a-request"></a>发送请求

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> from azure.messaging.webpubsubservice.rest import build_send_to_all_request
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> request = build_send_to_all_request('default', json={ 'Hello':  'webpubsub!' })
>>> request
<HttpRequest [POST], url: '/api/hubs/default/:send?api-version=2020-10-01'>
>>> response = client.send_request(request)
>>> response
<RequestsTransportResponse: 202 Accepted>
>>> response.status_code 
202
>>> with open('file.json', 'r') as f:
>>>    request = build_send_to_all_request('ahub', content=f, content_type='application/json')
>>>    response = client.send_request(request)
>>> print(response)
<RequestsTransportResponse: 202 Accepted>
```

## <a name="key-concepts"></a>关键概念

[!INCLUDE [Terms](includes/terms.md)]

## <a name="troubleshooting"></a>疑难解答

### <a name="logging"></a>日志记录

此 SDK 使用 Python 标准日志记录库。
可将记录打印调试信息配置到 stdout 或所需的任何位置。

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Http 请求和响应详细信息通过此日志记录配置打印到 stdout。

[webpubsubservice_docs]: https://aka.ms/awps/doc
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
