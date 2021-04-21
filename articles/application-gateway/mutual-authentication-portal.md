---
title: 通过门户在 Azure 应用程序网关上配置相互身份验证
description: 了解如何通过门户将应用程序网关配置为使用相互身份验证
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230821"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>通过门户在应用程序网关中配置相互身份验证（预览版）

本文介绍如何使用 Azure 门户在应用程序网关上配置相互身份验证。 相互身份验证是指应用程序网关使用你上传到应用程序网关的客户端证书，对发送请求的客户端进行身份验证。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

若要在应用程序网关中配置相互身份验证，需要将客户端证书上传到网关。 该客户端证书将用于验证客户端向应用程序网关提供的证书。 对于测试，可以使用自签名的证书。 不过，不建议对生产工作负荷使用自签名证书，因为这些证书难以管理，且不完全安全。 

若要了解详细信息，尤其是可上传的客户端证书类型，请参阅[应用程序网关中的相互身份验证概述](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)。

## <a name="create-a-new-application-gateway"></a>创建新的应用程序网关

首先，像往常一样通过门户创建一个新的 Azure 应用程序网关 - 创建过程中无需执行附加的步骤来启用相互身份验证。 有关如何在门户中创建 Azure 应用程序网关的详细信息，请查看[门户快速入门教程](./quick-create-portal.md)。

## <a name="configure-mutual-authentication"></a>配置相互身份验证 

若要在现有的应用程序网关中配置相互身份验证，首先需要在门户中转到“SSL 设置(预览版)”选项卡，然后创建新的 SSL 配置文件。 创建 SSL 配置文件时，会看到两个选项卡：“客户端身份验证”和“SSL 策略”。 在“客户端身份验证”选项卡中可以上传客户端证书。 “SSL 策略”选项卡用于配置侦听器特定的 SSL 策略 - 有关详细信息，请查看[配置侦听器特定的 SSL 策略](./application-gateway-configure-listener-specific-ssl-policy.md)。

> [!IMPORTANT]
> 请确保在一个文件中上传整个客户端 CA 证书链，并保证每个文件中只有一个链。

1. 在门户中搜索“应用程序网关”，选择“应用程序网关”，然后单击现有的应用程序网关。

2. 在左侧菜单中选择“SSL 设置(预览版)”。

3. 单击顶部“SSL 配置文件”旁的加号以创建新的 SSL 配置文件。

4. 在“SSL 配置文件名称”下输入名称。 在此示例中，SSL 配置文件命名为“applicationGatewaySSLProfile”。 

5. 不要离开“客户端身份验证”选项卡。使用“上传新证书”按钮，上传你打算用于在客户端和应用程序网关之间进行相互身份验证的 PEM 证书 。 

    有关如何提取要在此处上传的受信任客户端 CA 证书链的详细信息，请参阅[如何提取受信任的客户端 CA 证书链](./mutual-authentication-certificate-management.md)。

   > [!NOTE]
   > 如果这不是你的第一个 SSL 配置文件，并且你已将其他客户端证书上传到应用程序网关，则可以通过下拉菜单选择在网关上重复使用现有证书。 

6. 仅当你希望应用程序网关验证客户端证书的直接颁发者可分辨名称时，才选中“验证客户端证书颁发者的 DN”框。 

7. 请考虑添加侦听器特定的策略。 参阅[设置侦听器特定的 SSL 策略](./application-gateway-configure-listener-specific-ssl-policy.md)中的说明。

8. 选择“添加”以保存设置。
    > [!div class="mx-imgBorder"]
    > ![将客户端身份验证添加到 SSL 配置文件](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>将 SSL 配置文件与侦听器相关联

现在我们已创建一个配置了相互身份验证的 SSL 配置文件，接下来需要将 SSL 配置文件关联到侦听器，以完成相互身份验证的设置。 

1. 导航到现有的应用程序网关。 如果你刚刚完成了上述步骤，则无需在此处执行任何操作。 

2. 在左侧菜单中选择“侦听器”。 

3. 如果尚未设置 HTTPS 侦听器，请单击“添加侦听器”。 如果已有 HTTPS 侦听器，请在列表中单击它。 

4. 根据要求填写“侦听器名称”、“前端 IP”、“端口”、“协议”及“其他 HTTPS 设置”。

5. 选中“启用 SSL 配置文件”复选框，以便可以选择要与侦听器关联的 SSL 配置文件。 

6. 从下拉列表中选择刚刚创建的 SSL 配置文件。 在此示例中，我们选择了在上述步骤中创建的 SSL 配置文件：applicationGatewaySSLProfile。 

7. 继续根据要求配置侦听器的其余部分。 

8. 单击“添加”以保存已与 SSL 配置文件相关联的新侦听器。 

    > [!div class="mx-imgBorder"]
    > ![将 SSL 配置文件关联到新侦听器](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>续订已过期的客户端 CA 证书

如果客户端 CA 证书已过期，可按照以下步骤更新网关上的证书： 

1. 导航到你的应用程序网关，在左侧菜单中转到“SSL 设置(预览版)”选项卡。 
 
1. 选择使用了已过期客户端证书的现有 SSL 配置文件。 
 
1. 在“客户端身份验证”选项卡中选择“上传新证书”，然后上传新的客户端证书 。 
 
1. 选择已过期证书旁边的垃圾桶图标。 这会从 SSL 配置文件中删除该证书的关联。 

1. 对使用同一个已过期客户端证书的任何其他 SSL 配置文件重复上述步骤 2-4。 在其他 SSL 配置文件的下拉菜单中，你将可以选择在步骤 3 中上传的新证书。

## <a name="next-steps"></a>后续步骤

- [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)