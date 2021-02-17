---
title: Azure 防火墙高级预览版证书
description: 若要在 Azure 防火墙高级预览版中正确配置 TLS 检查，必须配置并安装中间 CA 证书。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3914a82903c293cf1a8306b5ecc1f542fef83e72
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549588"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Azure 防火墙高级预览版证书 

> [!IMPORTANT]
> Azure 防火墙高级版目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

 若要正确配置 Azure 防火墙高级预览版 TLS 检查，你必须提供有效的中间 CA 证书，并将其放在 Azure 密钥保管库中。

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Azure 防火墙高级预览版使用的证书

典型部署中使用三种类型的证书：

- **(CA 证书的中间 CA 证书)**

   证书颁发机构 (CA) 是受信任的，用于签署数字证书。 CA 用于验证公司或请求证书的个人的身份和合法性。 如果验证成功，CA 会颁发已签名的证书。 当服务器向客户端提供证书时 (例如，web 浏览器在 SSL/TLS 握手期间) ，客户端将尝试根据 *已知的正确* 签名者列表来验证该签名。 Web 浏览器通常会附带其隐式信任以识别主机的 Ca 列表。 如果证书颁发机构不在列表中，就像某些用于对自己的证书进行签名的站点一样，浏览器会提醒用户证书未被识别的颁发机构签名，并询问用户是否要继续与未经验证的站点进行通信。

- **服务器证书 (网站证书)**

   与特定域名关联的证书。 如果某个网站具有有效的证书，则表示证书颁发机构已采取措施来验证该 web 地址是否确实属于该组织。 键入 URL 或跟踪指向安全网站的链接时，浏览器会检查证书的以下特性：
   - 网站地址与证书上的地址匹配。
   - 证书由浏览器识别为 *受信任* 的颁发机构的证书颁发机构签名。
   
   偶尔，用户可能会连接到包含不受信任的证书的服务器。 Azure 防火墙将断开连接，就好像服务器终止了连接一样。

- **根 CA 证书 (根证书)**

   证书颁发机构可以以树结构形式颁发多个证书。 根证书是树的最顶层证书。

Azure 防火墙高级预览版可以拦截出站 HTTP/S 流量并为自动生成服务器证书 `www.website.com` 。 此证书是使用您提供的中间 CA 证书生成的。 最终用户浏览器和客户端应用程序必须信任你的组织根 CA 证书或中间 CA 证书，此过程才会生效。 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="证书进程":::

## <a name="intermediate-ca-certificate-requirements"></a>中间 CA 证书要求

确保 CA 证书符合以下要求：

- 作为 Key Vault 机密部署时，必须使用具有证书和私钥的不带密码的 PFX (Pkcs12) 。

- 它必须是单个证书，不应包括整个证书链。  

- 它必须在一年内有效。  

- 它必须是最小大小为4096字节的 RSA 私钥。  

- 它必须具有 `KeyUsage` 标记为 "关键" 的扩展， `KeyCertSign` (RFC 5280; 4.2.1.3 密钥用法) 。

- 它必须将 `BasicContraints` 扩展标记为关键 (RFC 5280; 4.2.1.9 基本约束) 。  

- `CA`标志必须设置为 TRUE。

- 路径长度必须大于或等于1。

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) 是平台托管的机密存储，可以用来保证机密、密钥和 TLS/SSL 证书的安全。 Azure 防火墙高级版支持与附加到防火墙策略的服务器证书的 Key Vault 集成。
 
若要配置密钥保管库：

- 需要将现有证书及其密钥对导入到密钥保管库。 
- 另外，还可以使用存储为不带密码的64编码的 PFX 文件的密钥保管库机密。  PFX 文件是一个数字证书，其中包含私钥和公钥。
- 建议使用 CA 证书导入，因为这样可以根据证书到期日期来配置警报。
- 导入证书或机密后，需要在密钥保管库中定义访问策略，以允许授予标识获得证书/机密的权限。
- 提供的 CA 证书需要受 Azure 工作负荷的信任。 确保它们已正确部署。

你可以创建或重复使用现有的用户分配的托管标识，Azure 防火墙使用该标识以你的名义 Key Vault 检索证书。 有关详细信息，请参阅 [Azure 资源的托管标识是什么？](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>在策略中配置证书

若要在防火墙高级策略中配置 CA 证书，请选择策略，然后选择 " **TLS 检查" (预览 ")**。 在 " **TLS 检查**" 页上选择 "**启用**"。 然后在 Azure Key Vault 中选择 CA 证书，如下图所示：

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Azure 防火墙高级概述示意图":::
 
> [!IMPORTANT]
> 若要从 Azure 门户查看和配置证书，你必须将 Azure 用户帐户添加到 Key Vault 访问策略。 在 "**机密权限**" 下为用户帐户授予 **Get** 和 **List** 。
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Azure Key Vault 访问策略":::


## <a name="troubleshooting"></a>疑难解答

如果你的 CA 证书有效，但你无法访问 TLS 检查下的 Fqdn 或 Url，请检查以下各项：

- 请确保 web 服务器证书有效。  

- 确保在客户端操作系统上安装根 CA 证书。  

- 确保浏览器或 HTTPS 客户端包含有效的根证书。 Firefox 和其他一些浏览器可能具有特殊的认证策略。  

- 确保应用程序规则中的 URL 目标类型涵盖正确的路径以及在目标 HTML 页面中嵌入的任何其他超链接。 您可以使用通配符轻松地覆盖整个所需的 URL 路径。  


## <a name="next-steps"></a>后续步骤

- [了解有关 Azure 防火墙高级功能的详细信息](premium-features.md)
