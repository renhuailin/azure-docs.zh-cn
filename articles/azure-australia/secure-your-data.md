---
title: Azure 澳大利亚的数据安全性
description: 在澳大利亚区域根据澳大利亚政府的具体政策、法规和法律要求配置 Azure。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: d90dc7dd316e79407069a0c00f9d63659688a640
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "117029168"
---
# <a name="data-security-in-azure-australia"></a>Azure 澳大利亚的数据安全性

用于保护客户数据安全的首要原则包括：

* 使用加密保护数据
* 管理机密
* 限制数据访问

## <a name="encrypting-your-data"></a>对数据进行加密

数据加密可以应用于磁盘级别（静态）、数据库中（静态和传输中）、应用程序中（传输中）和网络中（传输中）。 可以通过几种方式在 Azure 中实现加密：

|服务/功能|说明|
|---|---|
|存储服务加密|Azure 存储服务加密可在存储帐户级别启用，使块 Blob 和页 Blob 可以在写入 Azure 存储时自动加密。 从 Azure 存储读取数据时，存储服务会在返回数据之前将其解密。 使用 SSE 可以保护数据，而无需修改代码或将代码添加到任何应用程序。|
|Azure 磁盘加密|使用 Azure 磁盘加密对 Azure 虚拟机使用的 OS 磁盘和数据磁盘进行加密。 与 Azure Key Vault 集成可让你控制并帮助你管理磁盘加密密钥。|
|客户端应用程序级别加密|客户端加密内置于 Java 和 .NET 存储客户端库，这些库利用 Azure Key Vault API 让加密变得简单。 使用 Azure Key Vault 可以为使用 Azure Active Directory 的特定个人授予对 Azure Key Vault 中机密的访问权限。|
|传输中加密|此基本加密可用于与 Azure 澳大利亚的连接，支持传输层安全性 (TLS) 1.2 协议和 X.509 证书。 美国联邦信息处理标准 (FIPS) 140-2 级别 1 的加密算法还可用于 Azure 澳大利亚数据中心之间的基础结构网络连接。  Windows Server 2016、Windows 10、Windows Server 2012 R2、Windows 8.1 和 Azure 文件共享可以使用 SMB 3.0 在 VM 和文件共享之间进行加密。 在将数据传输到客户端应用程序中的存储之前，使用客户端加密对数据进行加密，并在从存储传出后对数据进行解密。|
|IaaS VM|使用 Azure 磁盘加密。 启用存储服务加密来加密在 Azure 存储中用于备份这些磁盘的 VHD 文件，但只会加密新写入的数据。 这意味着，如果创建 VM，并对保存 VHD 文件的存储帐户启用存储服务加密，则只对更改内容进行加密，而不会加密原始 VHD 文件。|
|客户端加密|这是加密数据的最安全方法，因为它会在传输前加密数据，并加密静态数据。 但是，它需要使用存储将代码添加到应用程序，而你可能不想要这样做。 在这些情况下，可以针对传输中的数据使用 HTTPS，并使用存储服务加密来加密静态数据。 客户端加密还会在客户端上产生更多负载，这一点必须在可伸缩性计划中加以考虑，特别是要加密并传输大量数据时。|
|

有关 Azure 中加密选项的详细信息，请参阅[存储安全指南](../storage/blobs/security-recommendations.md)。

## <a name="protecting-data-by-managing-secrets"></a>通过管理机密来保护数据

安全的密钥管理对在云中保护数据必不可少。 客户应尽量简化密钥管理，并对云应用程序和服务用来加密数据的密钥保持控制。

### <a name="managing-secrets"></a>管理机密

* 使用密钥保管库可在最大程度上减少密匙暴露在硬编码配置文件、脚本或源代码的风险。 Azure 密钥保管库加密密钥（如 Azure 磁盘加密的加密密钥）和机密（如密码）的方式是将它们存放在 FIPS 140-2 Level 2 已验证的硬件安全模块 (HSM) 中。 为了提升可靠性，可以在这些 HSM 中导入或生成密钥。
* 应用程序代码和模板应只包含机密的 URI 引用（这意味着实际机密不在代码、配置或源代码存储库中）。 这样可防止他人对内部或外部存储库（例如 GitHub 中的 harvest-bot）进行密钥钓鱼攻击。
* 利用 Key Vault 中强大的 Azure RBAC 控制。 如果受信任的操作人员离开公司或转到公司内的某个新团队，应阻止他们访问机密信息。  

有关详细信息，请参阅 [Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>隔离以限制数据访问

隔离是有关使用边界、分段和容器来限制仅对授权用户、服务和应用程序的数据访问。 例如，租户之间的隔离是一种用于多租户云平台（如 Microsoft Azure）的基本安全机制。 逻辑隔离有助于防止一个租户干扰任何其他租户的操作。

#### <a name="per-customer-isolation"></a>按客户隔离

Azure 通过第 2 层 VLAN 隔离、访问控制列表、负载均衡器和 IP 筛选器实现网络访问控制和隔离。

客户可以在订阅、资源组、虚拟网络和子网之间进一步隔离其资源。

有关 Microsoft Azure 中隔离的详细信息，请参阅 [Azure 公有云中的隔离](../security/fundamentals/isolation-choices.md)。

## <a name="next-steps"></a>后续步骤

查看关于 [Azure VPN 网关](vpn-gateway.md)的文章