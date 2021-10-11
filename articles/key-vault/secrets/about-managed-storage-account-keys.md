---
title: 关于 Azure Key Vault 托管存储帐户密钥 - Azure Key Vault
description: Azure Key Vault 托管存储帐户密钥概述。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 10/01/2021
ms.author: mbaldwin
ms.openlocfilehash: e2b72dd6960232dd3b9afd05ff19edccaeface12
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390649"
---
# <a name="about-azure-key-vault-managed-storage-account-keys"></a>关于 Azure Key Vault 托管存储帐户密钥

> [!IMPORTANT]
> 我们建议使用 Azure 存储与 Azure Active Directory (Azure AD) 的集成，这是 Microsoft 推出的基于云的标识和访问管理服务。 Azure AD 集成适用于 [Azure Blob 和队列](../../storage/blobs/authorize-access-azure-active-directory.md)，提供对 Azure 存储的基于 OAuth2 令牌的访问（类似于 Azure Key Vault）。 Azure AD 允许使用应用程序标识或用户标识（而不是存储帐户凭据）对客户端应用程序进行身份验证。 在 Azure 上运行时，可以使用 [Azure AD 托管标识](../../active-directory/managed-identities-azure-resources/index.yml)。 托管标识消除了客户端身份验证的需要，并可以在应用程序中存储凭据，或者将凭据与应用程序一同存储。 仅在无法实施 Azure AD 身份验证的情况下使用以下解决方案。

Azure 存储帐户使用由帐户名和密钥构成的凭据。 密钥是自动生成的，充当密码而不是加密密钥。 Key Vault 通过在存储帐户中定期重新生成存储帐户密钥来管理存储帐户密钥，并提供共享访问签名令牌，以便对存储帐户中的资源进行委托访问。

可以使用 Key Vault 托管的存储帐户密钥功能列出（同步） Azure 存储帐户中的密钥，并定期重新生成（轮换）密钥。 可以管理存储帐户和经典存储帐户的密钥。

## <a name="azure-storage-account-key-management"></a>Azure 存储帐户密钥管理

Key Vault 可以管理 [Azure 存储帐户](../../storage/common/storage-account-overview.md)密钥：

- 在内部，Key Vault 可以使用 Azure 存储帐户列出（同步）密钥。 
- Key Vault 定期重新生成（轮换）密钥。
- 响应调用方时永远不会返回密钥值。
- Key Vault 管理存储帐户和经典存储帐户的密钥。

有关详细信息，请参阅：
- [存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)
- [Azure Key Vault 中的存储帐户密钥管理](../secrets/overview-storage-keys.md)


## <a name="storage-account-access-control"></a>存储帐户访问控制

授权用户或应用程序主体对托管的存储帐户执行操作时，可以使用以下权限：  

- 针对托管存储帐户和 SaS 定义操作的权限
  - *get*：获取有关存储帐户的信息 
  - *list*：列出 Key Vault 托管的存储帐户
  - *update*：更新存储帐户
  - *delete*：删除存储帐户  
  - *recover*：恢复删除的存储帐户
  - *backup*：备份存储帐户
  - *restore*：将备份存储帐户还原到 Key Vault
  - *set*：创建或更新存储帐户
  - *regeneratekey*：为存储帐户重写指定的密钥值
  - *getsas*：获取有关存储帐户的 SAS 定义的信息
  - *listsas*：列出存储帐户的存储 SAS 定义
  - *deletesas*：从存储帐户中删除 SAS 定义
  - *setsas*：创建或更新存储帐户的新 SAS 定义/属性

- 针对特权操作的权限
  - *purge*：清除（永久删除）托管存储帐户

有关详细信息，请参阅 [Key Vault REST API 中的存储帐户操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>后续步骤

- [使用 Key Vault 和 Azure CLI 管理存储帐户密钥](overview-storage-keys.md)
- [关于 Key Vault](../general/overview.md)
- [关于键、密钥和证书](../general/about-keys-secrets-certificates.md)
- [Key Vault 中机密管理的最佳做法](secrets-best-practices.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
