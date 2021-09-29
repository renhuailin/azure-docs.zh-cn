---
title: 关于 Azure Key Vault 证书续订
description: 本文介绍如何续订 Azure Key Vault 证书。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: b2eeca4bb6f5d8af01aa283446961b56d27918d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761662"
---
# <a name="renew-your-azure-key-vault-certificates"></a>续订 Azure Key Vault 证书

通过 Azure Key Vault，可轻松地为网络预配、管理和部署数字证书，并支持应用程序的安全通信。 若要了解证书的详细信息，请参阅[关于 Azure Key Vault 证书](./about-certificates.md)。

通过使用生存期较短的证书或增加证书轮换的频率，可以帮助防止未经授权的用户访问应用程序。

本文介绍如何续订 Azure Key Vault 证书。

## <a name="get-notified-about-certificate-expiration"></a>获取有关证书过期的通知
若要获取有关证书生存期事件的通知，需要添加证书联系人。 证书联系人包含联系人信息以发送由证书生存期事件触发的通知。 密钥保管库中的所有证书共享联系人信息。 如果保管库中的任何证书发生事件，所有指定联系人都会收到通知。

### <a name="steps-to-set-certificate-notifications"></a>证书通知的设置步骤：
首先，将证书联系人添加到密钥保管库中。 可以使用 Azure 门户或 PowerShell cmdlet [Add-AzKeyVaultCertificateContact](/powershell/module/az.keyvault/add-azkeyvaultcertificatecontact) 添加。

其次，配置希望收到证书过期通知的时间。 若要配置证书的生命周期属性，请参阅[在 Key Vault 中配置证书自动轮换](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)。

如果证书的策略设置为自动续订，则在发生以下事件时发送通知。

- 证书续订之前
- 证书续订之后，指出是否已成功续订证书，或是否存在错误，需要手动续订证书。  

  如果你将证书策略设置为手动续订（仅限电子邮件），系统会在你需要续订证书时发送通知。  

在 Key Vault 中，有三种类别的证书：
-    通过集成证书颁发机构 (CA)（如 DigiCert 或 GlobalSign）创建的证书
-    通过非集成 CA 创建的证书
-    自签名证书

## <a name="renew-an-integrated-ca-certificate"></a>续订集成 CA 证书 
Azure Key Vault 处理由受信任的 Microsoft 证书颁发机构 DigiCert 和 GlobalSign 颁发的证书的端到端维护。 了解如何[将受信任的 CA 与 Key Vault 集成](./how-to-integrate-certificate-authority.md)。

## <a name="renew-a-nonintegrated-ca-certificate"></a>续订非集成 CA 证书 
通过使用 Azure Key Vault，可导入来自任何 CA 的证书，这一优点使你能够与多个 Azure 资源集成并简化部署。 如果你担心无法跟踪证书的到期日期，或者更糟的是，你发现某个证书已过期，那么你的密钥保管库可帮助你保持最新状态。 对于非集成 CA 证书，密钥保管库允许你设置即将过期的电子邮件通知。 此类通知也可为多个用户设置。

> [!IMPORTANT]
> 证书是受版本控制的对象。 如果当前版本即将过期，则需要创建新版本。 从概念上讲，每个新版本都是一个新证书，它由一个密钥和将该密钥与标识联系起来的 Blob 组成。 使用非合作伙伴 CA 时，密钥保管库将生成一个键/值对，并返回证书签名请求 (CSR)。

若要续订非集成 CA 证书，请执行以下操作：

1. 登录到 Azure 门户，然后打开要续订的证书。
1. 在证书窗格中，选择“新版本”。
1. 选择“证书操作”。
1. 选择“下载 CSR”，将 CSR 文件下载到本地驱动器。
1. 将 CSR 发送到所选的 CA 以对请求进行签名。
1. 返回已签名的请求，并在相同证书操作窗格中选择“合并 CSR”。

> [!NOTE]
> 将已签名的 CSR 与你创建的相同 CSR 请求合并，这点很重要。 否则，密钥将不匹配。

有关创建新 CSR 的详细信息，请参阅[在 Key Vault 中创建和合并 CSR](create-certificate-signing-request.md)。

## <a name="renew-a-self-signed-certificate"></a>续订自签名证书

Azure Key Vault 还处理自签名证书的自动续订。 若要详细了解如何更改颁发策略和更新证书的生命周期属性，请参阅[在 Key Vault 中配置证书自动轮换](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)。

## <a name="troubleshoot"></a>疑难解答
* 如果颁发的证书在 Azure 门户中处于“已禁用”状态，转到“证书操作”查看该证书的错误消息。
* 错误类型“用于获取证书的 CSR 已被使用。 请尝试使用新的 CSR 生成新的证书。”
  转到证书的“高级策略”部分，检查是否关闭了“续订时重用密钥”选项。


## <a name="frequently-asked-questions"></a>常见问题

如何测试证书的自动轮换功能？

创建一个有效期为 1 个月的自签名证书，然后将其轮换的生存期操作设置为 1% 。 你应该能够查看在接下来几天内创建的证书版本历史记录。
  
在自动续订证书后是否复制标记？

是的，在自动续订后复制标记。

## <a name="next-steps"></a>后续步骤
*    [将 Key Vault 与 DigiCert 证书颁发机构集成](how-to-integrate-certificate-authority.md)
*    [教程：配置 Key Vault 中的证书自动轮换](tutorial-rotate-certificates.md)
