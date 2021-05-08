---
title: 续订 Azure 应用程序网关证书
description: 了解如何续订与应用程序网关侦听器关联的证书。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ea5512f3b454edc2edd66c546629cc5f0b8f465c
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108320882"
---
# <a name="renew-application-gateway-certificates"></a>续订应用程序网关证书

在某种情况下，如果为 TLS/SSL 加密配置了应用程序网关，则需要续订证书。

可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 续订与侦听器关联的证书：

## <a name="azure-portal"></a>Azure 门户

若要从门户续订侦听器证书，请导航到应用程序网关侦听器。 选择具有需要续订的证书的侦听器，然后选择“续订或编辑所选证书”。

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="续订证书":::

上传新的 PFX 证书，为其命名，输入密码，然后选择“保存”。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 续订证书，请使用以下脚本：

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>后续步骤

若要了解如何使用 Azure 应用程序网关配置 TLS 卸载，请参阅[配置 TLS 卸载](./create-ssl-portal.md)
