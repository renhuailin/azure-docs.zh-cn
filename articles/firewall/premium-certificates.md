---
title: Azure 防火墙高级版证书
description: 若要在 Azure 防火墙高级版中正确配置 TLS 检查，必须配置并安装中间 CA 证书。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: victorh
ms.openlocfilehash: 0826e58f4b61e0a99064226a8488f1c3fb499cd7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738082"
---
# <a name="azure-firewall-premium-certificates"></a>Azure 防火墙高级版证书 

若要正确配置 Azure 防火墙高级版 TLS 检查，必须提供有效的中间 CA 证书，并将其存放在 Azure Key Vault 中。

## <a name="certificates-used-by-azure-firewall-premium"></a>Azure 防火墙高级版使用的证书

典型部署中使用的证书有三种类型：

- 中间 CA 证书（CA 证书）

   证书颁发机构 (CA) 是一个可签署数字证书的受信任组织。 CA 验证申请证书的公司或个人的身份和合法性。 如果验证成功，CA 会颁发已签名的证书。 当服务器在 SSL/TLS 握手期间向客户端（例如你的 Web 浏览器）提供证书时，客户端将尝试根据已知有效签名者的列表来验证签名。 Web 浏览器通常会附带它们完全信任的 CA 列表来识别主机。 如果颁发机构不在该列表中（就像一些对自己的证书进行签名的站点一样），浏览器会警告用户该证书未由公认的颁发机构签名，并询问用户是否希望继续与未经验证的站点通信。

- 服务器证书（网站证书）

   与特定域名相关联的证书。 如果网站拥有有效证书，则意味着证书颁发机构已采取措施来验证该网址确实属于该组织。 键入 URL 或单击指向安全网站的链接时，浏览器会检查证书是否具备以下特征：
   - 网站地址与证书上的地址匹配。
   - 证书由浏览器识别为受信任颁发机构的证书颁发机构签名。
   
   偶尔，用户可能会连接到拥有不受信任证书的服务器。 Azure 防火墙将断开连接，就像服务器终止了连接一样。

- 根 CA 证书（根证书）

   证书颁发机构可以树结构形式颁发多个证书。 根证书是这颗树的最顶层证书。

Azure 防火墙高级版可以拦截出站 HTTP/S 流量并自动为 `www.website.com` 生成服务器证书。 此证书是使用你提供的中间 CA 证书生成的。 最终用户浏览器和客户端应用程序必须信任你的组织根 CA 证书或中间 CA 证书，此过程才会生效。 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="证书过程":::

## <a name="intermediate-ca-certificate-requirements"></a>中间 CA 证书要求

确保 CA 证书符合以下要求：

- 作为 Key Vault 机密部署时，必须使用包含证书和私钥的无密码 PFX (Pkcs12)。

- 必须是单个证书，不应包括整个证书链。  

- 有效期至少为一年。  

- 必须是最小大小为 4096 字节的 RSA 私钥。  

- 必须使用 `KeyCertSign` 标志将 `KeyUsage` 扩展标记为“重要”（RFC 5280；4.2.1.3 密钥用法）。

- 必须将 `BasicContraints` 扩展标记为“重要”（RFC 5280；4.2.1.9 基本约束）。  

- `CA` 标志必须设为 TRUE。

- 路径长度必须大于或等于 1。

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) 是平台托管的机密存储，可以用来保证机密、密钥和 TLS/SSL 证书的安全。 Azure 防火墙高级版支持与 Key Vault 集成，以获取附加到防火墙策略的服务器证书。
 
配置密钥保管库：

- 需要将现有证书及其密钥对导入到密钥保管库。 
- 此外，也可使用密钥保管库机密，该机密将存储为无密码的 base-64 编码的 PFX 文件。  PFX 文件是一个包含私钥和公钥的数字证书。
- 建议使用 CA 证书导入，因为这样可以根据证书到期日期来配置警报。
- 在导入证书或机密以后，需在密钥保管库中定义访问策略，以允许为标识授予对证书/机密的“获取”访问权限。
- 提供的 CA 证书需要受 Azure 工作负载信任。 请确保它们已正确部署。

你可以创建或重用现有的用户分配的托管标识，Azure 防火墙会使用该标识以你的名义从 Key Vault 检索证书。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>在策略中配置证书

若要在防火墙高级策略中配置 CA 证书，请选择你的策略，然后选择“TLS 检查”。 在“TLS 检查”页上选择“已启用” 。 然后在 Azure Key Vault 中选择 CA 证书，如下图所示：

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Azure 防火墙高级版概览图":::
 
> [!IMPORTANT]
> 若要从 Azure 门户查看和配置证书，必须将 Azure 用户帐户添加到 Key Vault 访问策略。 在“机密权限”下授予用户帐户“获取”和“列出”权限  。
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Azure Key Vault 访问策略":::

## <a name="create-your-own-self-signed-ca-certificate"></a>创建你自己的自签名 CA 证书

如果要创建自己的证书来帮助测试和验证 TLS 检查，可以使用以下脚本创建自己的自签名根 CA 和中间 CA。

> [!IMPORTANT]
> 在生产环境中，你应使用企业 PKI 创建中间 CA 证书。 企业 PKI 利用现有的基础结构并处理向所有终结点计算机分发根 CA 的事宜。 有关详细信息，请参阅[部署和配置 Azure 防火墙的企业 CA 证书](premium-deploy-certificates-enterprise-ca.md)。

此脚本有两种版本：
- bash 脚本 `cert.sh` 
- PowerShell 脚本 `cert.ps1` 

 此外，这两个脚本都使用 `openssl.cnf` 配置文件。 若要使用这些脚本，请将 `openssl.cnf`、`cert.sh` 或 `cert.ps1` 的内容复制到本地计算机。

脚本会生成以下文件：
- rootCA.crt/rootCA.key - 根 CA 公共证书和私钥。
- interCA.crt/interCA.key - 中间 CA 公共证书和私钥
- interCA.pfx - 将由防火墙使用的中间 CA pkcs12 包

> [!IMPORTANT]
> rootCA.key 应存储在安全的脱机位置。 脚本会生成有效期为 1024 天的证书。
> 脚本要求在本地计算机上安装 openssl 二进制文件。 有关详细信息，请参阅 https://www.openssl.org/
> 
创建证书后，将其部署到以下位置：
- rootCA.crt - 在终结点计算机上部署（仅限公共证书）。
- interCA.pfx - 在 Key Vault 上作为证书导入并分配给防火墙策略。

### <a name="opensslcnf"></a>openssl.cnf
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Bash 脚本 - cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell - cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="certificate-auto-generation-preview"></a>证书自动生成（预览版）

对于非生产部署，可以使用 Azure 防火墙高级版证书自动生成机制，该机制会自动创建以下三种资源：

- 托管标识
- Key Vault
- 自签名根 CA 证书

只需选择新的预览版托管标识，它会在高级策略中将这三种资源捆绑在一起并设置 TLS 检查。 

:::image type="content" source="media/premium-certificates/auto-gen-certs.png" alt-text="自动生成的证书":::

## <a name="troubleshooting"></a>故障排除

如果你的 CA 证书有效，但你无法在 TLS 检查下访问 FQDN 或 URL，请检查以下各项：

- 确保 Web 服务器证书有效。  

- 确保在客户端操作系统上安装根 CA 证书。  

- 确保浏览器或 HTTPS 客户端包含有效的根证书。 Firefox 和其他一些浏览器可能具有特殊的认证策略。  

- 确保应用程序规则中的 URL 目标类型涵盖正确的路径和目标 HTML 页中嵌入的任何其他超链接。 可使用通配符轻松覆盖所需的完整 URL 路径。  


## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 防火墙高级功能](premium-features.md)
