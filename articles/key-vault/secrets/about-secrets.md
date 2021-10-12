---
title: 关于 Azure Key Vault 机密 - Azure Key Vault
description: Azure Key Vault 机密概述。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 5583596e399456461b2fc157a193b83b596180ee
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387385"
---
# <a name="about-azure-key-vault-secrets"></a>关于 Azure Key Vault 机密

[Key Vault](../general/overview.md) 提供了安全的通用存储机密，例如密码和数据库连接字符串。

从开发人员的角度来看，Key Vault API 接受机密值并将其作为字符串返回。 在内部，Key Vault 存储机密并将其作为八位字节序列（8 位字节）管理，每个字节的最大大小为 25k 字节。 Key Vault 服务不提供机密的语义。 它只是接受数据，然后加密和存储该数据，最后返回机密标识符（“id”）。 该标识符可用于稍后检索机密。  

对于高度敏感的数据，客户端应考虑对数据进行额外的保护。 例如，在 Key Vault 中存储数据之前，使用单独的保护密钥加密数据。  

Key Vault 还支持机密的 contentType 字段。 客户端可以指定机密的内容类型，以帮助在检索时解释机密数据。 此字段的最大长度为 255 个字符。 建议用于解释机密数据的提示。 例如，实现可以将密码和证书都存储为机密，然后使用此字段进行区分。 没有预定义的值。  

## <a name="encryption"></a>加密

Key Vault 中的所有机密均已加密存储。 此加密是透明的，不需要用户执行任何操作。 Azure Key Vault 服务会在你添加机密时对其进行加密，并在你读取机密时自动对其进行解密。 加密密钥对于每个密钥保管库来说是唯一的。

## <a name="secret-attributes"></a>机密属性

除机密数据外，还可以指定以下属性：  

- *exp*：IntDate，可选，默认值为 **forever**。 exp（过期时间）属性标识在不应检索机密数据当时或之后的过期时间，[特定情况](#date-time-controlled-operations)除外。 此字段仅供参考，因为它通知密钥保管库服务用户可能无法使用特定机密。 其值必须是包含 IntDate 值的数字。   
- *nbf*：IntDate，可选，默认值为 **now**。 nbf（非过去）属性标识在不应检索机密数据之前的时间，[特定情况](#date-time-controlled-operations)除外。 此字段仅供参考。 其值必须是包含 IntDate 值的数字。 
- enabled：布尔型，可选，默认值为 true。 此属性指定是否可以检索机密数据。 enabled 属性与 nbf 和 exp 结合使用，如果在 nbf 和 exp 之间出现操作，只有在 enabled 设置为 true 时，才允许该操作   。 nbf 和 exp 时段外的操作会自动禁止，[特定情况](#date-time-controlled-operations)除外 。  

在包含机密属性的任何响应中还包括以下其他只读属性：  

- *created*：IntDate，可选。 created 属性指示创建此版本的机密的时间。 如果机密在添加此属性之前创建，此值为 NULL。 其值必须是包含 IntDate 值的数字。  
- *updated*：IntDate，可选。 updated 属性指示更新此版本的机密的时间。 如果机密上次更新的时间早于添加此属性的时间，此值为 NULL。 其值必须是包含 IntDate 值的数字。

有关每种密钥保管库对象类型的通用属性的信息，请参阅 [Azure Key Vault 密钥、机密和证书概述](../general/about-keys-secrets-certificates.md)

### <a name="date-time-controlled-operations"></a>日期时间控制的操作

机密的获取操作在 nbf / exp 时段外适合尚未生效的机密和过期的机密 。 对于尚未生效的机密，调用机密的“获取”操作可用于测试目的。 检索（获取）过期的密钥可以用于恢复操作。

## <a name="secret-access-control"></a>机密访问控制

Key Vault 中托管的机密的访问控制是在包含这些机密的 Key Vault 级别提供的。 在同一 Key Vault 中，机密的访问控制策略不同于密钥的访问控制策略。 用户可以创建一个或多个保管库来保存机密，并且需要维护方案相应的机密分段和管理。   

在保管库上的机密访问控制条目中可以按主体使用以下权限，这些权限对机密对象上允许的操作采取严密的镜像操作：  

- 针对机密管理操作的权限
  - *get*：读取机密  
  - *list*：列出 Key Vault 中存储的机密或机密版本  
  - *set*：创建机密  
  - *delete*：删除机密  
  - *recover*：恢复已删除的机密
  - *backup*：备份密钥保管库中的机密
  - *restore*：将备份机密还原到密钥保管库

- 针对特权操作的权限
  - *purge*：清除（永久删除）已删除的机密

有关使用机密的详细信息，请参阅 [Key Vault REST API 中的机密操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。 

用于在 Key Vault 中控制访问的操作指南：
- [使用 CLI 分配 Key Vault 访问策略](../general/assign-access-policy-cli.md)
- [使用 PowerShell 分配 Key Vault 访问策略](../general/assign-access-policy-powershell.md)
- [使用 Azure 门户分配 Key Vault 访问策略](../general/assign-access-policy-portal.md)
- [使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限](../general/rbac-guide.md)

## <a name="secret-tags"></a>机密标记  
可以用标记的形式指定其他特定于应用程序的元数据。 Key Vault 支持多达 15 种标记，每种标记可以有 256 个字符的名称和 256 个字符的值。  

>[!Note]
>如果调用方具有“列表”或“获取”权限，则调用方可以读取标记。

## <a name="usage-scenarios"></a>使用方案

| 何时使用 | 示例 |
|--------------|-------------|
|安全存储、管理生命周期并监视用于服务到服务通信的凭据，例如密码、访问密钥和服务主体客户端机密。  | - [将 Azure Key Vault 用于虚拟机](../general/tutorial-net-virtual-machine.md)<br> - [将 Azure Key Vault 用于 Azure Web 应用](../general/tutorial-net-create-vault-azure-web-app.md) |

## <a name="next-steps"></a>后续步骤

- [Key Vault 中机密管理的最佳做法](secrets-best-practices.md)
- [关于 Key Vault](../general/overview.md)
- [关于键、密钥和证书](../general/about-keys-secrets-certificates.md)
- [分配 Key Vault 访问策略](../general/assign-access-policy.md)
- [使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限](../general/rbac-guide.md)
- [保护对密钥保管库的访问](../general/security-features.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
