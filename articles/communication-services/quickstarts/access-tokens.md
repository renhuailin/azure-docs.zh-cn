---
title: 快速入门 - 创建和管理访问令牌
titleSuffix: An Azure Communication Services quickstart
description: 了解如何使用 Azure 通信服务标识 SDK 管理标识和访问令牌。
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: identity
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 4b83fa7aec50d11c6ec4eac0baf2c15b95e29e6c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677263"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>快速入门：创建和管理访问令牌

通过使用通信服务标识 SDK，开始使用 Azure 通信服务。 使用该服务可以创建标识并管理访问令牌。 标识表示 Azure 通信服务（例如，用户或设备）中的应用程序的实体。 访问令牌允许聊天 SDK 和呼叫 SDK 直接向 Azure 通信服务进行身份验证。 建议在服务器端服务上生成访问令牌。 然后，访问令牌用于在客户端设备上初始化通信服务 SDK。

本教程图像中显示的任何价格仅用于演示用途。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

应用的输出描述每个已完成的操作：
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](./create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

> [!div class="checklist"]
> * 管理标识
> * 颁发访问令牌
> * 使用通信服务标识 SDK


> [!div class="nextstepaction"]
> [添加对应用的音频呼叫](./voice-video-calling/getting-started-with-calling.md)

你可能还想要：

 - [了解身份验证](../concepts/authentication.md)
 - [向应用中添加聊天](./chat/get-started.md)
 - [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
