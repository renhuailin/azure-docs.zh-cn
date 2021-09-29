---
title: 使用门户配置端到端 TLS 加密
titleSuffix: Azure Application Gateway
description: 了解如何使用 Azure 门户来创建启用端到端 TLS 加密的应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2d19ccb3ea40d916ba6fbf53e87a3d9f2f2e1c2a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744823"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>通过门户使用应用程序网关配置端到端 TLS

本文介绍了如何使用 Azure 门户通过 Azure 应用程序网关 v1 SKU 配置端到端传输层安全性 (TLS) 加密（旧称为“安全套接字层 (SSL) 加密”）。

> [!NOTE]
> 应用程序网关 v2 SKU 需要受信任的根证书才能启用端到端配置。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

若要使用应用程序网关来配置端到端 TLS，需要获取网关的证书。 后端服务器也需要证书。 网关证书用于派生符合 TLS 协议规范的对称密钥。 然后，可以使用该对称密钥来加密和解密发送到网关的流量。 

对于端到端 TLS 加密，必须在应用程序网关中允许正确的后端服务器。 若要允许这种访问，请将后端服务器的公共证书（也称为“身份验证证书”(v1) 或“受信任的根证书”(v2)）上传到应用程序网关。 添加证书后，可确保应用程序网关仅与已知后端实例通信。 此配置可进一步保护端到端通信。

若要了解详细信息，请参阅[概述：使用应用程序网关实现 TLS 终止和端到端 TLS](./ssl-overview.md)。

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>新建启用端到端 TLS 的应用程序网关

若要新建启用端到端 TLS 加密的应用程序网关，首先需要在新建应用程序网关时启用 TLS 终止。 此操作为客户端与应用程序网关之间的通信启用 TLS 加密。 然后，需要在 HTTP 设置中，将后端服务器的证书放入“安全接收方”列表。 此配置为应用程序网关与后端服务器之间的通信启用 TLS 加密。 这实现了端到端 TLS 加密。

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>在新建应用程序网关时启用 TLS 终止

若要了解详细信息，请参阅[在新建应用程序网关时启用 TLS 终止](./create-ssl-portal.md)。

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>添加后端服务器的身份验证/根证书

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 在左侧菜单中选择“HTTP 设置”。 当你创建应用程序网关时，Azure 自动创建了默认 HTTP 设置 **appGatewayBackendHttpSettings**。 

3. 选择“appGatewayBackendHttpSettings”。

4. 在“协议”下，选择“HTTPS”。 随后会显示“后端身份验证证书”或“受信任的根证书”窗格。

5. 选择“新建”。

6. 在“名称”字段中输入适当的名称。

7. 在“上传 CER 证书”框中选择证书文件。

   对于 Standard 和 WAF (v1) 应用程序网关，应以 .cer 格式上传后端服务器证书的公钥。

   ![添加证书](./media/end-to-end-ssl-portal/addcert.png)

   对于 Standard_v2 和 WAF_v2 应用程序网关，应以 .cer 格式上传后端服务器证书的根证书。 如果后端证书由已知的证书颁发机构 (CA) 颁发，则你可以选中“使用已知的 CA 证书”复选框，而无需上传证书。

   ![添加受信任的根证书](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![根证书](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. 选择“保存”。

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>为现有应用程序网关启用端到端 TLS

若要为现有应用程序网关配置端到端 TLS 加密，必须先在侦听器中启用 TLS 终止。 此操作为客户端与应用程序网关之间的通信启用 TLS 加密。 然后，将后端服务器的这些证书放入 HTTP 设置中的“安全接收方”列表内。 此配置为应用程序网关与后端服务器之间的通信启用 TLS 加密。 这实现了端到端 TLS 加密。

需要使用带有 HTTPS 协议和证书的侦听器来启用 TLS 终止。 可以使用符合这些条件的现有侦听器，或者创建新的侦听器。 如果选择前一个选项，则可以忽略以下“在现有应用程序网关中启用 TLS 终止”部分，并直接转到“为后端服务器添加身份验证/受信任的根证书”部分。

如果选择后一个选项，请应用以下过程中的步骤。
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>在现有应用程序网关中启用 TLS 终止

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 在左侧菜单中选择“侦听器”。

3. 根据要求选择“基本”或“多站点”侦听器。

4. 在“协议”下，选择“HTTPS”。 随后会显示“证书”窗格。

5. 上传要用于客户端与应用程序网关之间的 TLS 终止的 PFX 证书。

   > [!NOTE]
   > 对于测试，可以使用自签名的证书。 不过，不建议对生产工作负荷使用自签名证书，因为这些证书难以管理，且不完全安全。 有关详细信息，请参阅[创建自签名证书](./create-ssl-portal.md#create-a-self-signed-certificate)。

6. 根据要求添加“侦听器”的其他所需设置。

7. 选择“确定”进行保存。

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>添加后端服务器的身份验证/受信任的根证书

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 在左侧菜单中选择“HTTP 设置”。 可将证书放入现有后端 HTTP 设置中的“安全接收方”列表内，或创建新的 HTTP 设置。 （在下一步骤，默认 HTTP 设置 **appGatewayBackendHttpSettings** 的证书将添加到“安全接收方”列表中。）

3. 选择“appGatewayBackendHttpSettings”。

4. 在“协议”下，选择“HTTPS”。 随后会显示“后端身份验证证书”或“受信任的根证书”窗格。 

5. 选择“新建”。

6. 在“名称”字段中输入适当的名称。

7. 在“上传 CER 证书”框中选择证书文件。

   对于 Standard 和 WAF (v1) 应用程序网关，应以 .cer 格式上传后端服务器证书的公钥。

   ![添加证书](./media/end-to-end-ssl-portal/addcert.png)

   对于 Standard_v2 和 WAF_v2 应用程序网关，应以 .cer 格式上传后端服务器证书的根证书。 如果后端证书由已知的 CA 颁发，则你可以选中“使用已知的 CA 证书”复选框，而无需上传证书。

   ![添加受信任的根证书](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. 选择“保存”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)