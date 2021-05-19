---
title: 如何安全运行 OPC 保管库证书管理服务 - Azure | Microsoft Docs
description: 介绍如何安全地在 Azure 中运行 OPC 保管库证书管理服务，并回顾其他要考虑的安全准则。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f54467c03a67797d52ac499ca9ba455c0f75e240
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645485"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>安全运行 OPC 保管库证书管理服务

> [!IMPORTANT]
> 当我们更新本文时，请参阅 [Azure 工业 IoT](https://azure.github.io/Industrial-IoT/) 来了解最新内容。

本文介绍了如何安全地在 Azure 中运行 OPC 保管库证书管理服务，并回顾其他要考虑的安全准则。

## <a name="roles"></a>角色

### <a name="trusted-and-authorized-roles"></a>可信角色和授权角色

OPC 保管库微服务允许不同角色访问服务的不同部分。

> [!IMPORTANT]
> 在部署过程中，对于所有角色，脚本只会添加以用户身份运行部署脚本的用户。 对于生产部署，应查看此角色分配，并按照以下准则进行适当的重新配置。 此任务需要在 Azure Active Directory (Azure AD) 企业应用程序门户中手动分配角色和服务。

### <a name="certificate-management-service-roles"></a>证书管理服务角色

OPC 保管库微服务定义了下列角色：

- **Reader**：默认情况下，租户中任何经过身份验证的用户都具有读取访问权限。 
  - 有权读取应用程序和证书请求。 可以列出并查询应用程序和证书请求。 此外，还可以读取访问设备发现信息和公共证书。
- **Writer**：向用户分配 Writer 角色，以便为某些任务添加写入权限。 
  - 有权读取/写入应用程序和证书请求。 可以注册、更新和注销应用程序。 可以创建证书请求，并获取已批准的私钥和证书。 还可以删除私钥。
- **Approver**：向用户分配 Approver 角色，以便批准或拒绝证书请求。 该角色不包括任何其他角色。
  - 除了能访问 OPC 保管库微服务 API 的 Approver 角色外，用户还必须在 Azure Key Vault 中具有密钥签名权限，以便能够对证书签名。
  - Writer 和 Approver 角色应分配给不同的用户。
  - Approver 的主要角色是批准证书请求的生成和拒绝。
- **Administrator**：向用户分配 Administrator 角色，以管理证书组。 该角色不支持 Approver 角色，但包含 Writer 角色。
  - 管理员可以通过颁发新的证书吊销列表 (CRL) 来管理证书组、更改配置和吊销应用程序证书。
  - 理想情况下，Writer、Approver 和 Administrator 角色应分配给不同的用户。 为了进一步提高安全性，具有 Approver 或 Administrator 角色的用户还需要在 Key Vault 中具有密钥签名权限，以便颁发证书或续订证书颁发者 CA 证书。
  - 除了微服务管理角色以外，该角色包括但不限于：
    - 管理 CA 安全做法实施的责任。
    - 管理证书的生成、吊销和挂起。 
    - 加密密钥生命周期管理（例如，续订证书颁发者 CA 密钥）。
    - 安装、配置和维护操作 CA 的服务。
    - 服务的日常操作。 
    - CA 及数据库备份和恢复。

### <a name="other-role-assignments"></a>其他角色分配

运行服务时，还应考虑以下角色：

- 与外部根证书颁发机构签订证书采购合同的业务所有者（例如，当所有者从外部 CA 购买证书，或操作从属于外部 CA 的某个 CA 时）。
- 证书颁发机构的开发和验证。
- 对审核记录进行审查。
- 帮助支持 CA 或管理物理和云设施，但不是直接受信任以执行 CA 操作的人员，属于 *授权* 角色。 还必须记录授权角色中的人员所允许执行的任务集。

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>每季度审查受信任角色和授权角色的成员身份

至少按季度审查受信任角色和授权角色的成员身份。 确保每个角色中的人员量（对于手动流程）或服务标识（对于自动流程）处于最低限度。

### <a name="role-separation-between-certificate-requester-and-approver"></a>证书请求者和审批者之间的角色分离

证书颁发过程中，必须在证书请求者和证书审批者角色之间强制执行角色分离（人员或自动化系统）。 证书颁发必须由证书审批者角色授权，该角色将核查证书请求者是否有权获取证书。 持有证书审批者角色的人员必须是正式授权的人员。

### <a name="restrict-assignment-of-privileged-roles"></a>限制特权角色的分配

应限制特权角色的分配（例如，授权管理员和审批者组的成员身份），只有一组有限的授权人员。 如果特权角色发生任何更改，必须在 24 小时内撤销访问权限。 最后，按季度审查特权角色分配，并删除任何不需要或过期的分配。

### <a name="privileged-roles-should-use-two-factor-authentication"></a>特权角色应使用双因素身份验证

Approver 和 Administrator 对服务进行交互式登录时，应使用多重身份验证（也称为双因素身份验证）。

## <a name="certificate-service-operation-guidelines"></a>证书服务操作准则

### <a name="operational-contacts"></a>操作联系人

证书服务必须对文件具有最新的安全响应计划，包括详细的操作事件响应联系人。

### <a name="security-updates"></a>安全更新

必须持续监视所有系统，并使用最新的安全更新进行更新。

> [!IMPORTANT]
> OPC 保管库服务的 GitHub 存储库中会持续更新安全修补程序。 监视这些更新，并定期将它们应用到服务。

### <a name="security-monitoring"></a>安全监视

订阅或实施适当的安全监视。 例如，订阅中央监视解决方案（例如 Azure 安全中心或 Microsoft 365 监视解决方案），并适当地进行配置，以确保将安全事件传输到监视解决方案。

> [!IMPORTANT]
> OPC 保管库服务默认部署了 [Azure Application Insights](../azure-monitor/app/devops.md) 作为监视解决方案。 强烈建议添加安全解决方案（例如 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)）。

### <a name="assess-the-security-of-open-source-software-components"></a>评估开源软件组件的安全性

产品或服务中使用的所有开源组件中不得有任何中等或更高程度的安全漏洞。

> [!IMPORTANT]
> 在持续集成构建过程中，OPC 保管库服务的 GitHub 存储库将扫描所有组件中有无漏洞。 在 GitHub 上监视这些更新，并定期将它们应用到服务。

### <a name="maintain-an-inventory"></a>维护清单

维护所有生产主机的资产清单（包括永久性虚拟机）、设备、所有内部 IP 地址范围、VIP 和公共 DNS 域名。 每次添加或删除系统、设备 IP 地址、VIP 或公共 DNS 域时，都必须在 30 天内更新清单。

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>默认 Azure OPC 保管库微服务生产部署的清单 

在 Azure 中：
- **应用服务计划**：服务主机的应用服务计划。 默认为 S1。
- 适用于微服务的 **应用服务**：OPC 保管库服务主机。
- 适用于示例应用程序的 **应用服务**：OPC 保管库示例应用程序主机。
- **Key Vault 标准**：用于存储 Web 服务的机密信息和 Azure Cosmos DB 密钥。
- **Key Vault 高级版**：托管证书颁发者 CA 密钥、用于对服务签名，以及用于应用程序私钥的保管库配置和存储。
- **Azure Cosmos DB**：用于应用程序和证书请求的数据库。 
- **Application Insights**：（可选）用于 Web 服务和应用程序的监视解决方案。
- **Azure AD 应用程序注册**：注册示例应用程序、服务和边缘模块。

对于云服务，应记录部署服务时使用的所有主机名、资源组、资源名称、订阅 ID 和租户 ID。 

在 Azure IoT Edge 或本地 IoT Edge 服务器中：
- **OPC 保管库 IoT Edge 模块**：用于支持工厂网络 OPC UA 全局发现服务器。 

对于 IoT Edge 设备，应记录主机名和 IP 地址。 

### <a name="document-the-certification-authorities-cas"></a>记录证书颁发机构 (CA)

CA 层次结构文档必须包含全部有操作的 CA。 这包括所有相关的从属 CA、父级 CA 和根 CA，即使不受该服务管理也是如此。 无需使用正式文档，可以详尽提供所有未过期的 CA 证书。

> [!NOTE]
> OPC 保管库示例应用程序支持下载该服务中使用和生成的所有证书，以作记录之用。

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>记录所有证书颁发机构 (CA) 颁发的证书

详尽提供过去 12 个月内颁发的所有证书。

> [!NOTE]
> OPC 保管库示例应用程序支持下载该服务中使用和生成的所有证书，以作记录之用。

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>记录安全删除加密密钥的标准操作过程

在 CA 的生存期内，可能很少发生密钥删除。 所以，无需向任何用户分配 Key Vault 证书删除权限，而且没有公开任何用于删除证书颁发者 CA 证书的 API。 为了安全删除证书颁发机构加密密钥，手动标准操作过程只能在 Azure 门户中直接访问 Key Vault。 也可以在 Key Vault 中删除证书组。 若要确保立即删除，请禁用 [Key Vault 软删除](../key-vault/general/soft-delete-overview.md)功能。

## <a name="certificates"></a>证书

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>证书必须符合最低证书配置文件

OPC 保管库服务是一种联机 CA，用于向订阅服务器颁发最终实体证书。 OPC 保管库微服务在默认实施中遵循这些准则。

- 所有证书都必须包含以下 X.509 字段，如下所示：
  - 版本字段的内容必须是 v3。 
  - serialNumber 字段的内容必须包含从 FIPS（美国联邦信息处理标准）140 批准的随机数生成器获取的至少 8 个字节的熵。<br>
    > [!IMPORTANT]
    > OPC 保管库序列号默认是 20 个字节，并且是从操作系统加密随机数生成器获取的。 在 Windows 设备上，随机数生成器是获得 FIPS 140 批准的，但在 Linux 上不是。 选择使用 Linux VM 或 Linux Docker 容器的服务部署时，需要考虑到这一点，因为 Linux 上的基础技术 OpenSSL 并未获得 FIPS 140 批准。
  - issuerUniqueID 和 subjectUniqueID 字段不得存在。
  - 根据 IETF RFC 5280，最终实体证书必须使用基本约束扩展进行标识。
  - 对于颁发 CA 证书，pathLenConstraint 字段必须设置为 0。 
  - “扩展密钥用法”扩展必须存在，并且必须包含最小集的扩展密钥用法对象标识符 (OID)。 不得指定 anyExtendedKeyUsage OID (2.5.29.37.0)。 
  - CRL 分发点 (CDP) 扩展必须存在于证书颁发者 CA 证书中。<br>
    > [!IMPORTANT]
    > CDP 扩展存在于 OPC 保管库 CA 证书中。 不过，OPC UA 设备使用自定义方法来分发 CRL。
  - “颁发机构信息访问”扩展必须存在于订阅服务器证书中。<br>
    > [!IMPORTANT]
    > “颁发机构信息访问”扩展存在于 OPC 保管库订阅服务器证书中。 不过，OPC UA 设备使用自定义方法来分发证书颁发者 CA 信息。
- 必须使用经批准的非对称算法、密钥长度、哈希函数和填充模式。
  - RSA 和 SHA-2 是唯一受支持的算法。
  - RSA 可用于加密、密钥交换和签名。
  - RSA 加密必须只使用 OAEP、RSA-KEM 或 RSA-PSS 填充模式。
  - 密钥长度需大于或等于 2048 位。
  - 使用 SHA-2 系列的哈希算法（SHA256、SHA384 和 SHA512）。
  - 当典型生存期大于或等于 20 年时，RSA 根 CA 密钥必须为 4096 位或更高。
  - RSA 证书颁发者 CA 密钥必须至少为 2048 位。 如果 CA 证书到期日期在 2030 年之后，则 CA 密钥必须为 4096 位或更高。
- 证书生存期
  - 根 CA 证书：根 CA 的最长证书有效期不能超过 25 年。
  - 子 CA 或联机证书颁发者 CA 证书：对于联机状态且仅颁发订阅服务器证书的 CA，最长证书有效期不能超过 6 年。 对于这些 CA，相关的专用签名密钥的使用时间不得超过 3 年，否则将无法颁发新证书。<br>
    > [!IMPORTANT]
    > 如果证书颁发者证书是在没有外部根 CA 的默认 OPC 保管库微服务中生成的，则其处理方式与联机子 CA 相同，具有相应要求和生存期。 默认生存期设置为 5 年，密钥长度大于或等于 2048。
  - 所有非对称密钥的生存期最长为 5 年，建议生存期为 1 年。<br>
    > [!IMPORTANT]
    > 默认情况下，使用 OPC 保管库颁发的应用程序证书的生存期为 2 年，并且应每年替换一次。 
  - 续订证书时，将使用新的密钥进行续订。
- 应用程序实例证书中特定于 OPC UA 的扩展
  - subjectAltName 扩展包括应用程序 URL 和主机名。 这些可能还包括 FQDN、IPv4 和 IPv6 地址。
  - keyUsage 包括 digitalSignature、nonRepudiation、keyEncipherment 和 dataEncipherment。
  - extendedKeyUsage 包括 serverAuth 和 clientAuth。
  - authorityKeyIdentifier 是在已签名的证书中指定的。

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 密钥和证书必须满足最低要求

- **私钥**：RSA 密钥必须至少为 2048 位。 如果 CA 证书到期日期在 2030 年之后，则 CA 密钥必须为 4096 位或更高。
- **生存期**：对于联机状态且仅颁发订阅服务器证书的 CA，最长证书有效期不能超过 6 年。 对于这些 CA，相关的专用签名密钥的使用时间不得超过 3 年，否则将无法颁发新证书。

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>使用硬件安全模块保护 CA 密钥

OpcVault 使用 Azure Key Vault 高级版，密钥由 FIPS 140-2 级别 2 硬件安全模块 (HSM) 保护。 

Key Vault 使用的加密模块（HSM 或软件）经过 FIPS 验证。 创建或导入为受 HSM 保护的密钥在 HSM 内处理，经验证达到 FIPS 140-2 级别 2。 创建或导入为受软件保护的密钥在加密模块内处理，经验证达到 FIPS 140-2 级别 1。

## <a name="operational-practices"></a>操作做法

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>记录并维护证书注册的标准操作 PKI 做法

记录并维护有关 CA 如何颁发证书的标准操作过程 (SOP)，包括： 
- 如何对订阅服务器进行标识和身份验证。 
- 如何对证书请求进行处理和验证（如果适用，还包括如何处理证书续订和重新生成密钥请求）。 
- 如何将已颁发的证书分发到订阅服务器。 

OPC 保管库微服务 SOP 在 [OPC 体系结构](overview-opc-vault-architecture.md)和[管理 OPC 保管库证书服务](howto-opc-vault-manage.md)中有所描述。 这些做法遵循“OPC 统一体系结构规范第 12 部分：发现和全局服务”。


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>记录并维护证书吊销的标准操作 PKI 做法

证书吊销过程在 [OPC 保管库体系结构](overview-opc-vault-architecture.md)和[管理 OPC 保管库证书服务](howto-opc-vault-manage.md)中有所描述。
    
### <a name="document-ca-key-generation-ceremony"></a>记录 CA 密钥生成过程 

归功于 Azure Key Vault 中的安全存储，OPC 保管库微服务中的证书颁发者 CA 密钥生成得以简化。 有关详细信息，请参阅[管理 OPC 保管库证书服务](howto-opc-vault-manage.md)。

但是，在使用外部根证书颁发机构时，CA 密钥生成过程必须符合以下要求。

对于至少包含以下各项的已记录脚本，必须执行 CA 密钥生成过程： 
- 角色和参与者职责的定义。
- 关于执行 CA 密钥生成过程的批准。
- 过程中需要的加密硬件和激活材料。
- 硬件准备（包括资产/配置信息更新和签署）。
- 操作系统安装。
- CA 密钥生成过程中执行的特定步骤，例如： 
  - CA 应用程序安装和配置。
  - CA 密钥生成。
  - CA 密钥备份。
  - CA 证书签名。
  - 导入服务的受保护 HSM 中的签名密钥。
  - CA 系统关闭。
  - 准备存储材料。


## <a name="next-steps"></a>后续步骤

现在，你已了解如何安全管理 OPC 保管库，可以：

> [!div class="nextstepaction"]
> [利用 OPC 保管库保护 OPC UA 设备安全](howto-opc-vault-secure.md)