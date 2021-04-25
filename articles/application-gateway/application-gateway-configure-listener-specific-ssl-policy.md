---
title: 通过门户在 Azure 应用程序网关上配置特定于侦听器的 SSL 策略
description: 了解如何通过门户在 Azure 应用程序网关上配置特定于侦听器的 SSL 策略
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230838"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>通过门户（预览版）在 Azure 应用程序网关上配置特定于侦听器的 SSL 策略

本文介绍如何使用 Azure 门户在应用程序网关上配置特定于侦听器的 SSL 策略。 使用特定于侦听器的 SSL 策略，你可以为特定侦听器配置不同的 SSL 策略。 但你仍然可以设置默认 SSL 策略，只要该策略未被特定于侦听器的 SSL 策略所覆盖，所有侦听器均会使用这一策略。 

> [!NOTE]
> 只有 Standard_v2 SKU 和 WAF_v2 SKU 支持特定于侦听器的策略，因为这些策略是 SSL 配置文件的组成部分，而只有 v2 网关才支持 SSL 配置文件。 



如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-new-application-gateway"></a>创建新的应用程序网关

首先，像往常一样，通过门户新建一个 Azure 应用程序网关，新建过程中无需执行其他步骤来配置特定于侦听器的 SSL 策略。 有关如何通过门户创建 Azure 应用程序网关的详细信息，请参阅[门户快速入门教程](./quick-create-portal.md)。

## <a name="set-up-a-listener-specific-ssl-policy"></a>设置特定于侦听器的 SSL 策略

若要设置特定于侦听器的 SSL 策略，需先前往门户中的“SSL 设置（预览版）”选项卡，然后新建 SSL 配置文件。 创建 SSL 配置文件时，你可以看到两个选项卡：“客户端身份验证”和“SSL 策略”。 “SSL 策略”选项卡用于配置特定于侦听器的 SSL 策略。 在“客户端身份验证”选项卡下，你可以上传客户端证书进行相互身份验证。有关详细信息，请参阅[配置相互身份验证](./mutual-authentication-portal.md)。

> [!NOTE]
> 建议使用 TLS 1.2，因为将来会要求使用 TLS 1.2。 

1. 在门户中搜索“应用程序网关”，选择“应用程序网关”，然后单击现有“应用程序网关”。

2. 从左侧菜单中选择“SSL 设置（预览版）”。

3. 单击顶部“SSL 配置文件”旁的加号，新建 SSL 配置文件。

4. 在“SSL 配置文件名称”栏输入名称。 在此示例中，SSL 配置文件暂命名为“applicationGatewaySSLProfile”。 

5. 前往“SSL 策略”选项卡，勾选“启用特定于侦听器的 SSL 策略”复选框。 

6. 根据需要设置特定于侦听器的 SSL 策略。 你既可以选择预定义的 SSL 策略，也可以根据需要自定义 SSL 策略。 有关 SSL 策略的详细信息，请访问 [SSL 策略概述](./application-gateway-ssl-policy-overview.md)。 推荐使用 TLS 1.2

7. 点击“添加”以保存策略。

    > [!NOTE]
    > 你无需在 SSL 配置文件上配置客户端身份验证，即可将其关联到侦听器。 你只能在 SSL 配置文件中配置客户端身份验证或特定于侦听器的 SSL 策略，或同时配置二者。  

    ![将特定于侦听器的 SSL 策略添加到 SSL 配置文件](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>关联 SSL 配置文件与侦听器

由于已经创建了包含特定于侦听器的 SSL 策略的 SSL 配置文件，因此需要关联 SSL 配置文件与侦听器，使特定于侦听器的策略生效。 

1. 导航到现有应用程序网关。 如果你刚刚完成了上述步骤，则无需在此处执行任何操作。 

2. 在左侧菜单中选择“侦听器”。 

3. 如果尚未设置 HTTPS 侦听器，请单击“添加侦听器”。 如果已有 HTTPS 侦听器，请在列表中单击它。 

4. 根据需要填写“侦听器名称”、“前端 IP”、“端口”、“协议”及“其他 HTTPS 设置”。

5. 选中“启用 SSL 配置文件”复选框，以便选择需与侦听器关联的“SSL 配置文件”。 

6. 从下拉列表中选择已创建的 SSL 配置文件。 在此示例中，选择在上述步骤中创建的 SSL 配置文件“applicationGatewaySSLProfile”。 

7. 继续根据需要配置侦听器的其余部分。 

8. 单击“添加”以保存已与 SSL 配置文件相关联的新侦听器。 

    ![将 SSL 配置文件关联到新侦听器](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)