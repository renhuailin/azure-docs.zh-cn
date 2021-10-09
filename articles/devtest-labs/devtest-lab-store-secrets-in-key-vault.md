---
title: 在密钥保管库中存储机密
description: 了解如何在 Azure Key Vault 中存储机密，并在创建 VM、公式或环境时使用这些机密。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2aaa177c895b57a07ed94de48081de69beedcda8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596016"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>在 Azure 开发测试实验室的 Key Vault 中存储机密
使用 Azure 开发测试实验室时可能需要输入复杂的机密：Windows VM 的密码、Linux VM 的公共 SSH 密钥或个人访问令牌，以通过项目克隆 Git 存储库。 机密通常很长并且具有随机字符。 因此，输入它们可能会非常棘手且不方便，尤其是如果多次使用相同的机密。

为解决此问题并还将机密保存在安全位置，开发测试实验室支持在 [Azure Key Vault](../key-vault/general/overview.md) 中存储机密。 当用户第一次保存机密时，开发测试实验室服务会自动在包含实验室的同一资源组中创建密钥保管库，并将机密存储在密钥保管库中。 开发测试实验室为每个用户创建单独的密钥保管库。 

请注意，实验室用户首先需要创建实验室虚拟机，然后才能在密钥保管库中创建机密。 这是因为，开发测试实验室服务需要将实验室用户与有效的用户文档关联，然后用户才能在其密钥保管库中创建和存储机密。 


## <a name="save-a-secret-in-azure-key-vault"></a>将机密保存在 Azure Key Vault 中
若要在 Azure Key Vault 中保存机密，请执行以下步骤：

1. 在左侧菜单中选择“我的机密”。
2. 输入机密的 **名称**。 在创建 VM、公式或环境时，你会在下拉列表中看到此名称。 
3. 输入机密作为 **值**。

    ![存储机密](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>使用 Azure Key Vault 中的机密
当需要输入机密以创建 VM、公式或环境时，可以手动输入机密或从 Key Vault 中选择已保存的机密。 若要使用存储在 Key Vault 中的机密，请执行以下操作：

1. 选择“使用已保存的机密”。 
2. 从“选取机密”的下拉列表中选择机密。 

    ![在 VM 中使用机密](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>在 Azure 资源管理器模板中使用机密
可以在用于创建 VM 的 Azure 资源管理器模板中指定机密名称，如以下示例所示：

![在公式或环境中使用机密](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>后续步骤

- [使用机密创建 VM](devtest-lab-add-vm.md) 
- [使用机密创建公式](devtest-lab-manage-formulas.md)
- [使用机密创建环境](devtest-lab-create-environment-from-arm.md)
