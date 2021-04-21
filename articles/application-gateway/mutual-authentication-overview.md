---
title: Azure 应用程序网关上的相互身份验证概述
description: 本文概述应用程序网关上的相互身份验证。
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230824"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>应用程序网关中的相互身份验证概述（预览版）

相互身份验证或客户端身份验证允许应用程序网关对发送请求的客户端进行身份验证。 通常，只有客户端对应用程序网关进行身份验证；相互身份验证允许客户端和应用程序网关对彼此进行身份验证。 

> [!NOTE]
> 建议将 TLS 1.2 与相互身份验证配合使用，因为将来会强制要求使用 TLS 1.2。 

## <a name="mutual-authentication"></a>相互身份验证

应用程序网关支持基于证书的相互身份验证，在其中可以将受信任的客户端 CA 证书上传到应用程序网关，网关将使用该证书对向网关发送请求的客户端进行身份验证。 随着各行业中 IoT 用例的增加和安全要求的提高，相互身份验证提供了一种用于管理和控制哪些客户端可与应用程序网关通信的方式。 

若要配置相互身份验证，必须将受信任的客户端 CA 证书上传为 SSL 配置文件的客户端身份验证部分。 然后，需要将 SSL 配置文件关联到侦听器，以完成相互身份验证的配置。 上传的客户端证书中必须始终有一个根 CA 证书。 也可以上传证书链，但该链除了包含所需数量的中间 CA 证书以外，还必须包含一个根 CA 证书。 

例如，如果你的客户端证书包含一个根 CA 证书、多个中间 CA 证书和一个叶证书，请确保在一个文件中将根 CA 证书和所有中间 CA 证书上传到应用程序网关。 有关如何提取受信任客户端 CA 证书的详细信息，请参阅[如何提取受信任的客户端 CA 证书](./mutual-authentication-certificate-management.md)。

如果你要上传包含根 CA 证书和中间 CA 证书的证书链，必须将该证书链作为 PEM 或 CER 文件上传到网关。 

> [!NOTE] 
> 只能在 Standard_v2 和 WAF_v2 SKU 上使用相互身份验证。 

### <a name="certificates-supported-for-mutual-authentication"></a>相互身份验证支持的证书

应用程序网关支持以下类型的证书：

- CA（证书颁发机构）证书：CA 证书是证书颁发机构 (CA) 颁发的数字证书
- 自签名 CA 证书：客户端浏览器不信任这些证书，并且会警告用户，指出虚拟服务的证书不是信任链的一部分。 自签名 CA 证书适合用于测试，或者适合这样的环境：管理员控制客户端并且可以安全绕过浏览器的安全警报。 切勿将自签名 CA 证书用于生产工作负载。

有关如何设置相互身份验证的详细信息，请参阅[为应用程序网关配置相互身份验证](./mutual-authentication-portal.md)。

> [!IMPORTANT]
> 使用相互身份验证时，请确保将整个受信任客户端 CA 证书链上传到应用程序网关。 

## <a name="additional-client-authentication-validation"></a>其他有关客户端身份验证的验证

### <a name="verify-client-certificate-dn"></a>验证客户端证书 DN

可以选择验证客户端证书的直接颁发者，并仅允许应用程序网关信任该颁发者。 此选项默认处于关闭状态，但可以通过门户、PowerShell 或 Azure CLI 启用此选项。 

如果你选择启用应用程序网关来验证客户端证书的直接颁发者，请参阅下文了解如何确定要从上传的证书中提取哪些内容作为客户端证书颁发者 DN。 
* 场景 1：证书链包含：根证书 - 中间证书 - 叶证书 
    * 中间证书的使用者名称是将由应用程序网关提取为客户端证书颁发者 DN 并要用作验证依据的内容。 
* 场景 2：证书链包含：根证书 - 中间证书 1 - 中间证书 2 - 叶证书
    * 中间证书 2 的使用者名称是将提取为客户端证书颁发者 DN 并要用作验证依据的内容。 
* 场景 3：证书链包含：根证书 - 叶证书 
    * 要作为客户端证书颁发者 DN 提取并使用的根证书使用者名称。
* 场景 4：同一文件中包含多个具有相同长度的证书链。 链 1 包含：根证书 - 中间证书 1 - 叶证书。 链 2 包含：根证书 - 中间证书 2 - 叶证书。 
    * 将提取中间证书 1 的使用者名称作为客户端证书颁发者 DN。  
* 场景 5：同一文件中包含多个具有不同长度的证书链。 链 1 包含：根证书 - 中间证书 1 - 叶证书。 链 2 包含：根证书 - 中间证书 2 - 中间证书 3 - 叶证书。 
    * 将提取中间证书 3 的使用者名称作为客户端证书颁发者 DN。 

> [!IMPORTANT]
> 建议每个文件仅上传一个证书链。 如果你要启用验证客户端证书 DN 的功能，这一点特别重要。 如果在一个文件中上传多个证书链，最终会出现场景 4 或 5 中所述的情况，并且在提供的客户端证书与应用程序网关从链中提取的客户端证书颁发者 DN 不匹配的情况下，将来可能会出现问题。 

有关如何提取受信任客户端 CA 证书链的详细信息，请参阅[如何提取受信任的客户端 CA 证书链](./mutual-authentication-certificate-management.md)。

## <a name="server-variables"></a>服务器变量 

使用相互身份验证时，可以使用其他服务器变量将有关客户端证书的信息传递到应用程序网关后面的后端服务器。 有关可用服务器变量及其用法的详细信息，请查看[服务器变量](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview)。

## <a name="next-steps"></a>后续步骤

了解相互身份验证后，请转到[在 PowerShell 中配置使用相互身份验证的应用程序网关](./mutual-authentication-powershell.md)，以创建一个使用相互身份验证的应用程序网关。 

