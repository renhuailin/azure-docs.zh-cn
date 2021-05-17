---
title: 如何轮换 Azure Web PubSub 服务的访问密钥
description: 概述客户为何需要定期轮换访问密钥，以及如何进行轮换。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 73c31051a177dc16711048bec984a90c786a28f2
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166866"
---
# <a name="how-to-rotate-access-key-for-azure-web-pubsub-service"></a>如何轮换 Azure Web PubSub 服务的访问密钥

每个 Azure Web PubSub 服务实例都有一对访问密钥，称为主密钥和辅助密钥。 向服务发出请求时，这些密钥用于对客户端进行身份验证。 密钥与实例终结点 URL 相关联。 请保护好密钥并定期轮换。 你有两个访问密钥，因此，在重新生成其中的一个密钥时，可以使用另一个密钥来保持连接。

## <a name="why-rotate-access-keys"></a>为何要轮换访问密钥？

出于安全原因和合规性要求，请定期轮换访问密钥。

## <a name="regenerate-access-keys"></a>重新生成访问密钥

1. 转到 [Azure 门户](https://portal.azure.com/)并使用自己的凭据登录。

1. 在要重新生成其密钥的 Azure Web PubSub 服务实例中找到“密钥”部分。

1. 在导航菜单中选择“密钥”。

1. 选择“重新生成主密钥”或“重新生成辅助密钥”。

   此时将创建并显示新的密钥和相应的连接字符串。

Azure Web PubSub 服务正式发布后，你还可使用 Azure CLI 来重新生成密钥。

## <a name="update-configurations-with-new-connection-strings"></a>使用新的连接字符串更新配置

1. 复制新生成的连接字符串。

1. 将所有配置更新为使用新连接字符串。

1. 根据需要重启应用程序。

## <a name="forced-access-key-regeneration"></a>强制重新生成访问密钥

在某些情况下，Azure Web PubSub 服务可能会强制要求重新生成访问密钥。 该服务会通过电子邮件和门户通知告知客户。 如果你收到此信件或者遇到访问密钥导致的服务故障，请遵照本指南中的说明轮换密钥。