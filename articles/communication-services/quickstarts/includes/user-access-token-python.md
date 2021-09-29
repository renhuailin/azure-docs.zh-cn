---
title: include 文件
description: include 文件
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/13/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 0d69e35af1cc7adc30a7b616897f3147f9a04281
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677264"
---
> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/access-tokens-quickstart) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7 或 3.6+。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

1. 打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. 使用文本编辑器在项目根目录中创建名为 issue-access-tokens.py 的文件，并添加程序的结构，包括基本异常处理。 将在以下部分中将此快速入门的所有源代码添加到此文件。

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>安装包

如果仍在应用程序目录中，可以使用 `pip install` 命令安装适用于 Python 包的 Azure 通信服务标识 SDK。

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串实例化 `CommunicationIdentityClient`。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

在 `try` 块内添加此代码：

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

或者，如果你设置了 Azure Active Directory (AD) 应用程序，请参阅[使用服务主体](../identity/service-principal.md)，你还可以使用 AD 进行身份验证。
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

## <a name="create-an-identity"></a>创建标识

Azure 通信服务维护轻量级标识目录。 使用 `create_user` 方法可在目录中创建具有唯一 `Id` 的新项。 存储收到的标识，并映射到应用程序的用户。 例如，将它们存储在应用程序服务器的数据库中。 稍后颁发访问令牌时需要该标识。

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.properties['id'])
```

## <a name="issue-access-tokens"></a>颁发访问令牌

使用 `get_token` 方法为已存在的通信服务标识颁发访问令牌。 参数 `scopes` 定义一组基元，用于授权此访问令牌。 请参阅[受支持的操作列表](../../concepts/authentication.md)。 参数 `CommunicationUserIdentifier` 的新实例可以基于 Azure 通信服务标识的字符串表示形式构造。

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

访问令牌是短期凭据，需要重新颁发。 如果不重新颁发，可能会导致应用程序用户的体验中断。 `expires_on` 响应属性指示访问令牌的生存期。

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>在同一请求中创建标识并颁发访问令牌

使用 `create_user_and_token` 方法创建通信服务标识并为其颁发访问令牌。 参数 `scopes` 定义一组基元，用于授权此访问令牌。 请参阅[受支持的操作列表](../../concepts/authentication.md)。

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0]
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nCreated an identity with ID: " + identity.properties['id'])
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>刷新访问令牌

若要刷新访问令牌，请使用 `CommunicationUserIdentifier` 对象重新颁发：

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token(identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>撤销访问令牌

在某些情况下可能会显式撤销访问令牌。 例如，当应用程序的用户更改在向服务进行身份验证时所使用的密码时。 `revoke_tokens` 方法使颁发给标识的所有活动访问令牌无效。

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.properties['id'])
```

## <a name="delete-an-identity"></a>删除标识

删除标识将撤销所有活动访问令牌，并阻止你为标识颁发访问令牌。 它还会删除与标识关联的所有保存的内容。

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.properties['id'])
```

## <a name="run-the-code"></a>运行代码

在控制台提示符下，导航到包含 issue-access-tokens.py 文件的目录，然后执行以下 `python` 命令来运行应用。

```console
python ./issue-access-tokens.py
```
