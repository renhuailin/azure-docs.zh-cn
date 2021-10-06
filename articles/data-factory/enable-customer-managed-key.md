---
title: 使用客户管理的密钥加密 Azure 数据工厂
description: 通过创建自己的密钥 (BYOK) 增强数据工厂安全性
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.subservice: security
ms.topic: quickstart
ms.date: 05/08/2020
ms.reviewer: mariozi
ms.openlocfilehash: 725ebc0dbb8b037dcfcde8d154353fd7cf0a1a59
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208080"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>使用客户管理的密钥加密 Azure 数据工厂

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 数据工厂对静态数据进行加密，其中包括实体定义和在运行期间缓存的所有数据。 默认情况下，使用随机生成并由 Microsoft 管理的密钥加密数据，该密钥只会分配到数据工厂。 为了获得额外的安全保证，现在可以通过 Azure 数据工厂客户管理的密钥功能启用“创建自己的密钥”(BYOK)。 指定客户管理的密钥时，数据工厂会使用工厂系统密钥和 CMK 加密客户数据。 缺少两者中的任何一个都会导致拒绝访问数据和工厂。

存储客户管理的密钥需要 Azure 密钥保管库。 可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure 密钥保管库 API 来生成密钥。 密钥保管库和数据工厂必须位于同一 Azure Active Directory (Azure AD) 租户和同一区域，但可以位于不同订阅中。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>关于客户管理的密钥

下图显示了数据工厂如何借助 Azure Active Directory 和 Azure 密钥保管库使用客户管理的密钥发出请求：

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="该图显示了客户管理的密钥在 Azure 数据工厂中的工作方式。":::

下表说明了图中的编号步骤：

1. Azure 密钥保管库管理员向与数据工厂关联的托管标识授予对加密密钥的权限
1. 数据工厂管理员在工厂中启用客户管理的密钥功能
1. 数据工厂使用与工厂关联的托管标识通过 Azure Active Directory 针对对 Azure 密钥保管库的访问进行身份验证
1. 数据工厂在 Azure 密钥保管库中使用客户密钥包装工厂加密密钥
1. 对于读/写操作，数据工厂会将请求发送到 Azure 密钥保管库，以便展开包帐户加密密钥，从而执行加密和解密操作

可通过两种方法将客户管理的密钥加密添加到数据工厂。 一种是在 Azure 门户中创建工厂期间，另一种是在数据工厂 UI 中创建工厂后。

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>先决条件 - 配置 Azure 密钥保管库并生成密钥

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>在 Azure 密钥保管库上启用“软删除”和“不清除”

要在数据工厂中使用客户管理的密钥，需要在密钥保管库上设置两个属性，即“软删除”和“不清除” 。 可以在新的或现有的密钥保管库上使用 PowerShell 或 Azure CLI 启用这些属性。 若要了解如何在现有的密钥保管库上启用这些属性，请参阅[具有软删除和清除保护的 Azure Key Vault 恢复管理](../key-vault/general/key-vault-recovery.md)

如果通过 Azure 门户新建 Azure 密钥保管库，可通过如下方式启用“软删除”和“不清除” ：

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="显示创建 Key Vault 时启用软删除和清除保护的屏幕截图。":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>授予数据工厂对 Azure 密钥保管库的访问权限

确保 Azure Key Vault 和 Azure 数据工厂位于同一 Azure Active Directory (Azure AD) 租户以及同一区域。 从 Azure Key Vault 访问控制，向数据工厂授予以下权限：获取、展开密钥和包装密钥  。 在数据工厂中启用客户管理的密钥需要这些权限。

* 如果要[在数据工厂 UI 中创建工厂后](#post-factory-creation-in-data-factory-ui)添加客户管理的密钥加密，请确保数据工厂的托管服务标识 (MSI) 具有这三种 Key Vault 权限
* 如果要[在 Azure 门户中创建工厂期间](#during-factory-creation-in-azure-portal)添加客户管理的密钥加密，请确保用户分配的托管标识 (UA-MI) 具有这三种 Key Vault 权限

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="显示如何启用数据工厂对 Key Vault 的访问权限的屏幕截图。":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>在 Azure Key Vault 中生成或上传客户管理的密钥

你可以创建自己的密钥并将其存储在密钥保管库中。 或者，可以使用 Azure Key Vault API 来生成密钥。 数据工厂加密仅支持 2048 位 RSA 密钥。 有关详细信息，请参阅[关于密钥、机密和证书](../key-vault/general/about-keys-secrets-certificates.md)。

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="显示如何生成客户管理的密钥的屏幕截图。":::

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

### <a name="post-factory-creation-in-data-factory-ui"></a>在数据工厂 UI 中创建工厂后

本部分逐步讲解创建工厂后在数据工厂 UI 中添加客户管理的密钥加密的过程。

> [!NOTE]
> 客户管理的密钥只能在空数据工厂上配置。 数据工厂不能包含链接服务、管道以及数据流等任何资源。 建议创建工厂后立即启用客户管理的密钥。

> [!IMPORTANT]
> 此方法不适用于启用了托管虚拟网络的工厂。 如果要加密此类工厂，请考虑[备选路由](#during-factory-creation-in-azure-portal)。

1. 请确保数据工厂的托管服务标识 (MSI) 对 Key Vault 具有获取、展开密钥和包装密钥权限  。

1. 确保数据工厂为空。 数据工厂不能包含链接服务、管道以及数据流等任何资源。 目前，将客户管理的密钥部署到非空工厂将会导致错误。

1. 若要在 Azure 门户中找到密钥 URI，请导航到 Azure 密钥保管库，然后选择“密钥”设置。 选择所需密钥，然后选择该密钥以查看其版本。 选择密钥版本以查看设置

1. 复制密钥标识符字段的值，该字段提供了 URI:::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="从 Key Vault 获取密钥 URI 的屏幕截图。":::

1. 启动 Azure 数据工厂门户，然后使用左侧导航栏跳转到数据工厂管理门户

1. 单击“客户管理的密钥”图标:::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="如何在数据工厂 UI 中启用客户管理的密钥的屏幕截图。":::

1. 输入之前复制的客户管理的密钥的 URI

1. 单击“保存”，然后为数据工厂启用客户管理的密钥加密

### <a name="during-factory-creation-in-azure-portal"></a>在 Azure 门户中创建工厂期间

本部分逐步讲解如何在部署工厂期间在 Azure 门户中添加客户管理的密钥加密。

若要加密工厂，数据工厂需要首先从 Key Vault 中检索客户管理的密钥。 由于工厂部署仍在进行中，因此托管服务标识 (MSI) 尚无法通过 Key Vault 进行身份验证。 因此，若要使用此方法，客户需要将用户分配的托管标识 (UA-MI) 分配到数据工厂。 我们将假定已在 UA-MI 中定义的角色，并通过 Key Vault 进行身份验证。

若要了解有关用户分配的托管标识的详细信息，请参阅[托管标识类型](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)和[用户分配的托管标识的角色分配](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 请确保用户分配的托管标识 (UA-MI) 对 Key Vault 具有获取、展开密钥和包装密钥权限  

1. 在“高级”选项卡下，选中“使用客户托管密钥启用加密”复选框
  :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="用于在 Azure 门户中创建数据工厂的“高级”选项卡的屏幕截图。":::

1. 提供存储在 Key Vault 中的客户管理的密钥的 URL

1. 选择适当的用户分配的托管标识以通过 Key Vault 进行身份验证

1. 继续工厂部署

## <a name="update-key-version"></a>更新密钥版本

新建密钥版本时，请更新数据工厂以使用新版本。 遵循[数据工厂 UI](#post-factory-creation-in-data-factory-ui) 部分中所述的类似步骤，其中包括：

1. 通过 Azure 密钥保管库门户找到新密钥版本的 URI

1. 导航到“客户管理的密钥”设置

1. 替换并粘贴新密钥的 URI

1. 单击“保存”，数据工厂将立即使用新密钥版本进行加密

## <a name="use-a-different-key"></a>使用其他密钥

若要更改用于数据工厂加密的密钥，必须手动更新数据工厂中的设置。 遵循[数据工厂 UI](#post-factory-creation-in-data-factory-ui) 部分中所述的类似步骤，其中包括：

1. 通过 Azure 密钥保管库门户找到新密钥的 URI

1. 导航到“客户管理的密钥”设置

1. 替换并粘贴新密钥的 URI

1. 单击“保存”，数据工厂将立即使用新密钥进行加密

## <a name="disable-customer-managed-keys"></a>禁用客户管理的密钥

按照设计，启用客户管理的密钥功能之后，无法删除其他安全步骤。 我们将始终需要使用客户所提供的密钥来加密工厂和数据。

## <a name="customer-managed-key-and-continuous-integration-and-continuous-deployment"></a>客户托管密钥以及持续集成和持续部署

默认情况下，CMK 配置不包含在中心 Azure 资源管理器 (ARM) 模板中。 若要将客户托管密钥加密设置纳入 ARM 模板，以便进行持续集成 (CI/CD)，请执行以下操作：

1. 确保中心处于 Git 模式
1. 导航到“管理门户-客户托管密钥”部分
1. 选中“包括 ARM 模板”选项

  :::image type="content" source="media/enable-customer-managed-key/07-include-in-template.png" alt-text="将客户托管密钥设置纳入 ARM 模板的屏幕截图。":::

你将在 ARM 模板中添加以下设置。 通过编辑 [Azure 资源管理器参数配置](continuous-integration-delivery-resource-manager-custom-parameters.md)，可在持续集成和交付管道中将这些属性参数化

  :::image type="content" source="media/enable-customer-managed-key/08-template-with-customer-managed-key.png" alt-text="将客户托管密钥设置纳入 Azure 资源管理器模板的屏幕截图。":::

> [!NOTE]
> 将加密设置添加到 ARM 模板会添加一个中心级别设置，该设置将覆盖其他环境中的其他中心级别设置，如 Git 配置。 如果在已提升的环境（如 UAT 或生产）中启用了这些设置，请参阅 [CI/CD 中的全局参数](author-global-parameters.md#cicd)。

## <a name="next-steps"></a>后续步骤

完成相关[教程](tutorial-copy-data-dot-net.md)来了解如何在更多方案中使用数据工厂。
