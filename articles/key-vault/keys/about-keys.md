---
title: 关于密钥 - Azure Key Vault
description: 概述了 Azure Key Vault REST 接口，并面向开发人员提供了有关密钥的详细信息。
services: key-vault
author: mbaldwin
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: mbaldwin
ms.openlocfilehash: 602c146b014d2c129deb7a6cb4539e2cf9260d0c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471728"
---
# <a name="about-keys"></a>关于密钥

Azure Key Vault 提供了两种类型的资源来存储和管理加密密钥。 保管库支持受软件保护和受 HSM（硬件安全模块）保护的密钥。 托管 HSM 仅支持受 HSM 保护的密钥。 

|资源类型|密钥保护方法|数据平面终结点基 URL|
|--|--|--|
| **保管库** | 受软件保护<br/><br/>和<br/><br/>受 HSM 保护（含高级 SKU）</li></ul> | https://{vault-name}.vault.azure.net |
| **托管 HSM** | 受 HSM 保护 | https://{hsm-name}.managedhsm.azure.net |
||||

- **保管库** - 保管库提供低成本、易部署、多租户、区域复原（若可用）且高度可用的密钥管理解决方案，适用于最常见的云应用程序方案。
- **托管 HSM** - 托管 HSM 提供单租户、区域复原（若可用）且高度可用的 HSM 来存储和管理加密密钥。 非常适用于处理重要密钥的应用程序和使用方案。 还有助于满足最严格的安全性、合规性和法规要求。 

> [!NOTE]
> 除了加密密钥外，保管库还能让你存储和管理多种类型的对象（如机密、证书和存储帐户密钥）。

Key Vault 中的加密密钥表示为 JSON Web 密钥 [JWK] 对象。 JavaScript 对象表示法 (JSON) 和 JavaScript 对象签名和加密 (JOSE) 规范如下：

-   [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web 加密 (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web 算法 (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web 签名 (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

此外，还扩展了基本 JWK/JWA 规范，以启用对于 Azure Key Vault 和托管 HSM 实现独有的密钥类型。 

受 HSM 保护的密钥（也称为 HSM 密钥）在 HSM（硬件安全模块）中进行处理，并且始终保留 HSM 保护边界。 

- 保管库使用 FIPS 140-2 级别 2 验证的 HSM 来保护共享 HSM 后端基础结构中的 HSM 密钥。 
- 托管 HSM 使用已经过“FIPS 140-2 级别 3”验证的 HSM 模块来保护密钥。 每个 HSM 池都是独立的单租户实例，它有自己的[安全域](../managed-hsm/security-domain.md)，提供与共享同一硬件基础结构的其他所有 HSM 之间的完全加密隔离。

这些密钥在单租户 HSM 池中受到保护。 可通过软形式或从受支持的 HSM 设备导出来导入 RSA、EC 和对称密钥。 也可在 HSM 池中生成密钥。 当通过 [BYOK（创建自己的密钥）规范](../keys/byok-specification.md)中所述的方法导入 HSM 密钥时，可将密钥材料安全地传输到托管 HSM 池中。 

有关地理边界的详细信息，请参阅 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>密钥类型和保护方法

Key Vault 支持 RSA 和 EC 密钥。 托管 HSM 支持 RSA、EC 和对称密钥。 

### <a name="hsm-protected-keys"></a>HSM 保护的密钥

|密钥类型|保管库（仅适用于高级 SKU）|托管 HSM|
|--|--|--|
|**EC-HSM**：椭圆曲线密钥 | 支持 | 支持|
|**RSA-HSM**：RSA 密钥|支持|支持|
|**oct-HSM**：对称密钥|不支持|支持|
|||

### <a name="software-protected-keys"></a>受软件保护的密钥

|密钥类型|保管库|托管 HSM|
|--|--|--|
**RSA**：“受软件保护的”RSA 密钥|支持|不支持
**EC**：“受软件保护的”椭圆曲线密钥|支持|不支持
|||

### <a name="compliance"></a>合规性

|密钥类型和目标|合规性|
|---|---|
|保管库中受软件保护的密钥（高级 SKU 和标准 SKU） | FIPS 140-2 级别 1|
|保管库中受 HSM 保护的密钥（高级 SKU）| FIPS 140-2 级别 2|
|托管 HSM 中受 HSM 保护的密钥|FIPS 140-2 级别 3|
|||



有关每种密钥类型、算法、操作、属性和标记的详细信息，请参阅[密钥类型、算法和操作](about-keys-details.md)。

## <a name="next-steps"></a>后续步骤
- [关于 Key Vault](../general/overview.md)
- [关于托管 HSM](../managed-hsm/overview.md)
- [关于机密](../secrets/about-secrets.md)
- [关于证书](../certificates/about-certificates.md)
- [Key Vault REST API 概述](../general/about-keys-secrets-certificates.md)
- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
