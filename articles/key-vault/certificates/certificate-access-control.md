---
title: 关于 Azure Key Vault 证书访问控制
description: Azure Key Vault 证书访问控制概述
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128519"
---
# <a name="certificate-access-control"></a>证书访问控制

 证书的访问控制由 Key Vault 托管，并且由包含这些证书的 Key Vault 提供。 在同一 Key Vault 中，证书的访问控制策略不同于密钥和机密的访问控制策略。 用户可以创建一个或多个保管库来保存证书，以维护方案相应的证书分段和管理。  

 在密钥保管库上的机密访问控制条目中可以按主体使用以下权限，这些权限对机密对象上允许的操作采取严密的镜像操作：  

- 针对证书管理操作的权限
  - **get** ：获取最新版本的证书或任何版本的证书
  - **list** ：列出最新版本的证书或任何版本的证书  
  - **update** ：更新证书
  - **create** ：创建 Key Vault 证书
  - **import** ：将证书材料导入到 Key Vault 证书
  - **delete** ：删除证书、策略及其所有版本  
  - **recover** ：恢复已删除的证书
  - **backup** ：备份密钥保管库中的证书
  - **restore** ：将备份证书还原到密钥保管库
  - **managecontacts** ：管理 Key Vault 证书联系人  
  - **manageissuers** ：管理 Key Vault 证书颁发机构/颁发者
  - **getissuers** ：获取证书的颁发机构/颁发者
  - **listissuers** ：列出证书的颁发机构/颁发者  
  - **setissuers** ：创建或更新 Key Vault 证书的颁发机构/颁发者  
  - **deleteissuers** ：删除 Key Vault 证书的颁发机构/颁发者  
 
- 针对特权操作的权限
  - **purge** ：清除（永久删除）已删除的证书

有关详细信息，请参阅 [Key Vault REST API 中的证书操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="troubleshoot"></a>故障排除
由于缺少访问策略，可能会出现错误。 例如 ```Error type : Access denied or user is unauthorized to create certificate```。要解决此错误，需要添加证书/创建权限。

## <a name="next-steps"></a>后续步骤

- [关于 Key Vault](../general/overview.md)
- [关于键、密钥和证书](../general/about-keys-secrets-certificates.md)
- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
