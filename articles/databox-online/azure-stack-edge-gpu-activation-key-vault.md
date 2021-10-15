---
title: Azure Key Vault 与 Azure Stack Edge 资源和设备激活的集成
description: 介绍在 Azure Stack Edge Pro 设备的激活过程中，Azure Key Vault 如何与机密管理关联。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 67cddd1839e666d4908706a1bbdaccbbd3565704
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351444"
---
# <a name="manage-azure-stack-edge-secrets-using-azure-key-vault"></a>使用 Azure Key Vault 管理 Azure Stack Edge 机密 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

将 Azure Key Vault 与 Azure Stack Edge 资源集成，以用于管理机密。 本文详细介绍了如何在设备激活期间为 Azure Stack Edge 资源创建 Azure 密钥保管库，然后用其管理机密。 


## <a name="about-key-vault-and-azure-stack-edge"></a>关于 Key Vault 和 Azure Stack Edge

Azure Key Vault 云服务用于安全地存储令牌、密码、证书、API 密钥和其他机密，并控制对它们的访问。 通过 Key Vault，还可以轻松创建和控制用于加密数据的加密密钥。 

Azure Stack Edge 服务与 Key Vault 的集成提供以下优势：

- 存储客户机密。 用于 Azure Stack Edge 服务的其中一个机密是通道完整性密钥 (CIK)。 使用此密钥可以加密机密，该密钥安全地存储在 Key Vault 中。 设备机密（例如 BitLocker 恢复密钥和基板管理控制器 (BMC) 用户密码）也存储在 Key Vault 中。 

    有关详细信息，请参阅[安全地存储机密和密钥](../key-vault/general/overview.md#securely-store-secrets-and-keys)。
- 将加密的客户机密传递到设备。
- 如果设备已关闭，则显示设备机密以方便访问。


## <a name="generate-activation-key-and-create-key-vault"></a>生成激活密钥并创建密钥保管库

在激活密钥生成过程中，将为 Azure Stack Edge 资源创建密钥保管库。 密钥保管库将在 Azure Stack Edge 资源所在的同一资源组中进行创建。 需要对密钥保管库具有参与者权限。 

### <a name="prerequisites-for-key-vault"></a>密钥保管库的先决条件

在激活期间创建密钥保管库之前，必须满足以下先决条件：

- 注册 Microsoft.KeyVault 资源提供程序，之后才能创建 Azure Stack Edge 资源。 如果你有订阅的所有者或参与者访问权限，则系统会自动注册资源提供程序。 密钥保管库将在与 Azure Stack Edge 资源相同的订阅和资源组中进行创建。 

- 在创建 Azure Stack Edge 资源时，还会创建一个在资源生存期内一直存在并与云中的资源提供程序通信的系统分配的托管标识。 

    启用托管标识后，Azure 将为 Azure Stack Edge 资源创建受信任的标识。

### <a name="key-vault-creation"></a>创建密钥保管库

创建资源后，需要使用设备激活资源。 为此，你将从 Azure 门户生成激活密钥。 

生成激活密钥时，将发生以下事件： 

![激活密钥生成流](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-1.png)

- 在 Azure 门户中请求激活密钥。 然后，请求会发送到密钥保管库资源提供程序。 
- 默认情况下，创建并锁定具有访问策略的标准层密钥保管库。 
    - 此密钥保管库使用默认名称或指定的 3 到 24 个字符长度的自定义名称。 不能使用已在使用中的密钥保管库。 
    - 密钥保管库详细信息将存储在服务中。 此密钥保管库用于管理机密，只要有 Azure Stack Edge 资源，该密钥保管库就会一直存在。 

        ![密钥保管库在生成激活密钥期间创建](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)
- 对密钥保管库启用资源锁，以防止意外删除。 还应对密钥保管库启用软删除，这样一来，如果发生意外删除，则可以在 90 天内还原密钥保管库。 有关详细信息，请参阅 [Azure 密钥保管库软删除概述](../key-vault/general/soft-delete-overview.md)。
- 现已启用创建 Azure Stack Edge 资源时创建的系统分配的托管标识。
- 将生成通道完整性密钥 (CIK) 并将其置于密钥保管库中。 CIK 详细信息会显示在服务中。
- 还会在与 Azure Stack Edge 资源相同的作用域中创建区域冗余存储帐户 (ZRS)，并对帐户进行锁定。 
    - 此帐户用于存储审核日志。 
    - 创建存储帐户的运行过程较长，需要几分钟才能完成。
    - 该存储帐户标记有密钥保管库名称。
- 将诊断设置添加到密钥保管库并启用日志记录。 
- 将托管标识添加到密钥保管库访问策略，以允许访问密钥保管库，因为设备将使用此密钥保管库来存储和检索机密。 
- 密钥保管库将使用托管标识对请求进行身份验证，以生成激活密钥。 激活密钥将返回到 Azure 门户。 然后，可以复制此密钥，并在本地 UI 中使用此密钥来激活设备。

> [!NOTE]
> - 如果在将 Azure Key Vault 与 Azure Stack Edge 资源集成之前存在现有 Azure Stack Edge 资源，则不会受到影响。 你可以继续使用现有的 Azure Stack Edge 资源。
> -  创建密钥保管库和存储帐户会增加总体资源成本。 如需详细了解允许的事务和相应费用，请参阅 [Azure Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)和[存储帐户定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

如果遇到与密钥保管库和设备激活相关的任何问题，请参阅[排查设备激活问题](azure-stack-edge-gpu-troubleshoot-activation.md)。


## <a name="view-key-vault-properties"></a>查看密钥保管库属性

生成激活密钥和创建密钥保管库后，你可能想要访问密钥保管库来查看机密、访问策略、诊断和见解。 以下过程一一介绍了这些操作。

### <a name="view-secrets"></a>查看机密

生成激活密钥和创建密钥保管库后，你可能想要访问密钥保管库。 

若要访问密钥保管库并查看机密，请执行以下步骤：

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“安全性”。 
1. 在右窗格的“安全性”下，可以查看“机密”。 
1. 还可以导航到与 Azure Stack Edge 资源关联的密钥保管库。 选择“密钥保管库名称”。 

    ![转到设备密钥保管库](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. 若要查看密钥保管库中存储的机密，请转到“机密”。 通道完整性密钥、BitLocker 恢复密钥和基板管理控制器 (BMC) 用户密码存储在密钥保管库中。 如果设备出现故障，可以通过门户轻松访问 BitLocker 恢复密钥和 BMC 用户密码。
    
    ![查看密钥保管库中的设备机密](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-secrets-1.png)

### <a name="view-managed-identity-access-policies"></a>查看托管标识访问策略

若要访问密钥保管库和托管标识的访问策略，请执行以下步骤：

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“安全性”。 
1. 选择与“密钥保管库名称”对应的链接，以导航到与 Azure Stack Edge 资源关联的密钥保管库。 

    ![转到设备密钥保管库](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. 若要查看与密钥保管库关联的访问策略，请转到“访问策略”。 你可以看到，已向托管标识授予了访问权限。 选择“机密权限”。 你可以看到，托管标识的访问权限仅限于“获取”和“设置”机密。 
    
    ![查看密钥保管库的访问策略](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-access-policies-1.png)

    
### <a name="view-audit-logs"></a>查看审核日志

若要访问密钥保管库并查看诊断设置和审核日志，请执行以下步骤：

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“安全性”。 
1. 选择与“密钥保管库名称”对应的链接，以导航到与 Azure Stack Edge 资源关联的密钥保管库。 

    ![转到设备密钥保管库](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. 若要查看与密钥保管库关联的诊断设置，请转到“诊断设置”。 此设置允许监视何人在何时以何方式访问密钥保管库。 你可以看到已创建诊断设置。 日志会流入另外创建的存储帐户。 密钥保管库中还会创建审核事件。
    
    ![查看密钥保管库的诊断设置](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-diagnostics-settings-1.png)

如果为密钥保管库日志配置了其他存储目标，则可以直接在该存储帐户中查看日志。

### <a name="view-insights"></a>查看见解 

若要访问密钥保管库见解（包括对密钥保管库执行的操作），请执行以下步骤：

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“安全性”。 
1. 选择与“密钥保管库诊断”相对应的链接。 

    ![转到设备密钥保管库](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. “见解”边栏选项卡概述了对密钥保管库执行的操作。

    ![查看密钥保管库的见解](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-insights-1.png) 

### <a name="view-managed-identity-status"></a>查看托管标识状态

若要查看与 Azure Stack Edge 资源关联的系统分配的托管标识的状态，请执行以下步骤：

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“安全性”。 
1. 在右窗格中，转到“系统分配的托管标识”，以查看是否已启用或禁用系统分配的托管标识。

    ![转到设备密钥保管库](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

### <a name="view-key-vault-locks"></a>查看密钥保管库锁

若要访问密钥保管库并查看锁，请执行以下步骤：

1. 在 Azure Stack Edge 资源的 Azure 门户中，转到“安全性”。 
1. 选择与“密钥保管库名称”对应的链接，以导航到与 Azure Stack Edge 资源关联的密钥保管库。 

    ![转到设备密钥保管库](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)
1. 若要查看密钥保管库锁，请转到“锁”。 为防止意外删除，应对密钥保管库启用资源锁。 
    
    ![查看密钥保管库锁](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-locks-1.png)


## <a name="regenerate-activation-key"></a>重新生成激活密钥

在某些情况下，可能需要重新生成激活密钥。 重新生成激活密钥时，将发生以下事件：

1. 在 Azure 门户中请求重新生成激活密钥。 
1. 激活密钥将返回到 Azure 门户。 然后，可以复制此密钥并使用它。 

重新生成激活密钥时，不会访问密钥保管库。 

## <a name="recover-device-secrets"></a>恢复设备机密

如果意外删除了 CIK 或密钥保管库中的机密（例如 BMC 用户密码）已过时，则需要推送设备机密以更新密钥保管库机密。 

请按照以下步骤同步设备机密：  

1. 在 Azure 门户中，转到 Azure Stack Edge 资源，然后转到“安全性”。
1. 在右窗格的顶部命令栏中，选择“同步设备机密”。
1. 会将设备机密推送到密钥保管库，以还原或更新密钥保管库中的机密。 完成同步后，会看到通知。

    ![同步密钥保管库中的设备机密](media/azure-stack-edge-gpu-activation-key-vault/sync-device-secrets-1.png)

## <a name="delete-key-vault"></a>删除密钥保管库

可以通过两种方法删除与 Azure Stack Edge 资源关联的密钥保管库：

- 删除 Azure Stack Edge 资源，并选择同时删除关联的密钥保管库。
- 直接意外删除密钥保管库。

删除 Azure Stack Edge 资源后，密钥保管库也会随之一起删除。 系统会提示进行确认。 如果密钥保管库中存储了其他密钥，且你不想删除此密钥保管库，可以选择不同意。 只有 Azure Stack Edge 资源会被删除，而密钥保管库保持不变。 

按照以下步骤删除 Azure Stack Edge 资源及关联的密钥保管库：

1. 在 Azure 门户中，转到 Azure Stack Edge 资源，然后转到“概述”。
1. 在右窗格中，选择“删除”。 此操作将删除 Azure Stack Edge 资源。

   ![删除 Azure Stack Edge 资源及关联的密钥保管库](media/azure-stack-edge-gpu-activation-key-vault/delete-azure-stack-edge-resource-1.png)  

1. 你会看到“确认”边栏选项卡。 键入 Azure Stack Edge 资源名称。 若要确认删除关联的密钥保管库，请键入“是”。

    ![确认删除 Azure Stack Edge 资源及关联的密钥保管库](media/azure-stack-edge-gpu-activation-key-vault/confirm-delete-azure-stack-edge-resource-1.png)   
1. 选择“删除”。

将删除 Azure Stack Edge 资源及其密钥保管库。

使用 Azure Stack Edge 资源期间，可能会意外删除密钥保管库。 如果发生这种情况，会在 Azure Stack Edge 资源的“安全性”页中引发关键警报。 可导航到此页面来恢复密钥保管库。 


## <a name="recover-key-vault"></a>恢复密钥保管库

如果意外删除或清除了与 Azure Stack Edge 资源关联的密钥保管库，可将其恢复。 如果此密钥保管库用于存储其他密钥，则需要通过还原密钥保管库来恢复这些密钥。

- 删除后的 90 天内，可还原已删除的密钥保管库。
- 如果 90 天清除保护期已过，则无法还原密钥保管库。 相反，则需要创建新的密钥保管库。

删除后的 90 天内，请按照以下步骤来恢复密钥保管库：

- 在 Azure 门户中，转到 Azure Stack Edge 资源的“安全性”页。 你会看到一个通知，指示与资源关联的密钥保管库已被删除。 可选择此通知，也可在“安全性首选项”下选择“重新配置”密钥保管库名称 ，以恢复密钥保管库。

    ![转到“安全性”页](media/azure-stack-edge-gpu-activation-key-vault/security-page-1.png) 

- 在“恢复密钥保管库”边栏选项卡中，选择“配置”。 在恢复过程中，将执行以下操作：  

    ![恢复步骤](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-2.png) 

    - 使用相同名称恢复密钥保管库，并对密钥保管库资源进行锁定。 
    
        > [!NOTE]
        > 如果已删除密钥保管库，且未超过 90 天清除保护期，则在该时间段内，不能使用该密钥保管库名称来创建新的密钥保管库。
    - 创建存储帐户来存储审核日志。 
    - 向系统分配的托管标识授予访问密钥保管库的权限。
    - 将设备机密推送到密钥保管库。 
    
    选择“配置” 。 
 
    ![恢复密钥保管库边栏选项卡](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-1.png)  

    恢复密钥保管库，当恢复完成后，会显示一条通知指示恢复已完成。

如果删除了密钥保管库，且已超过 90 天清除保护期，则可以选择通过上述[恢复密钥过程](#recover-key-vault)创建新的密钥保管库。 在这种情况下，需为密钥保管库提供新的名称。 此时会创建一个新的存储帐户，向托管标识授予访问此密钥保管库的权限，并将设备机密推送到此密钥保管库。
  
<!--To recover the key vault using the follow these steps to [Recover your key vault](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates).-->

## <a name="recover-managed-identity-access"></a>恢复托管标识访问权限

如果删除了系统分配的托管标识访问策略，则当设备无法重新同步密钥保管库机密时，会引发警报。 如果托管标识无权访问密钥保管库，也会引发设备警报。 选择每种情况下的警报，打开“恢复密钥保管库边栏选项卡”并进行重新配置。 此过程应会还原托管标识访问权限。 

![向托管标识授予访问密钥保管库权限流](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-2.png)


## <a name="next-steps"></a>后续步骤

- 详细了解如何[生成激活密钥](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)。
- 在 Azure Stack Edge 设备上[排查密钥保管库错误](azure-stack-edge-gpu-troubleshoot-activation.md)。
