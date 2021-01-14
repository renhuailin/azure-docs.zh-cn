---
title: 使用 Azure 门户管理 Azure Data Box 的客户托管密钥
description: 了解如何使用 Azure 门户来创建和管理 Azure Data Box 的 Azure Key Vault 客户管理的密钥。 客户管理的密钥使你可以创建、轮换、禁用和撤消访问控制。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 80a6824edb92d8337481f592cbbf5eb23255b383
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185523"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>将 Azure 密钥保管库中的客户管理的密钥用于 Azure Data Box

Azure Data Box 通过加密密钥来保护设备解锁密钥 (也称为设备密码) ，后者用于锁定设备。 默认情况下，此加密密钥是 Microsoft 托管密钥。 对于其他控制，可以使用客户管理的密钥。

使用客户托管的密钥不会影响设备上数据的加密方式。 它仅影响设备解锁密钥的加密方式。

若要在订单过程中保持此级别的控制，请在创建订单时使用客户管理的密钥。 有关详细信息，请参阅 [教程： Order Azure Data Box](data-box-deploy-ordered.md)。

本文介绍如何在 [Azure 门户](https://portal.azure.com/)中为现有 Data Box 顺序启用客户管理的密钥。 你将了解如何为当前客户管理的密钥更改密钥保管库、密钥、版本或标识，或切换回使用 Microsoft 托管密钥。

本文适用于 Azure Data Box 和 Azure Data Box Heavy 设备。

## <a name="requirements"></a>要求

Data Box 订单的客户托管的密钥必须满足以下要求：

- 必须创建密钥，并将其存储在包含 **软删除** 且未启用 **清除** 的 Azure Key Vault 中。 有关详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)。 创建或更新订单时，可以创建密钥保管库和密钥。

- 此密钥必须是2048或更大大小的 RSA 密钥。

## <a name="enable-key"></a>启用密钥

若要为 Azure 门户中的现有 Data Box 订单启用客户托管的密钥，请执行以下步骤：

1. 请在 " **概述** " 屏幕上查找 Data Box 顺序。

    ![Data Box 订单的概述屏幕-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. 请参阅 " **设置" > 加密**"，并选择" **客户管理的密钥**"。 然后选择 " **选择密钥和密钥保管库**"。

    ![选择 "客户管理的密钥加密" 选项](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   在 Azure Key Vault 屏幕上的 " **选择密钥** " 上，将自动填充订阅。

 3. 对于 **密钥保管库**，可以从下拉列表中选择现有的密钥保管库，也可以选择 " **新建** " 并创建新的密钥保管库。

     ![选择客户管理的密钥时的密钥保管库选项](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     若要创建新的密钥保管库，请在 " **创建新的密钥保管库** " 屏幕上输入订阅、资源组、密钥保管库名称和其他信息。 在 " **恢复选项**" 中，确保已启用 **软删除** 和 **清除保护** 。 然后选择“查看 + 创建”。

      ![查看并创建 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      查看密钥保管库的信息，然后选择“创建”。 等待几分钟，直到密钥保管库创建完成。

       ![创建具有设置的 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. 在 " **从 Azure Key Vault 中选择密钥** " 屏幕上，可以从密钥保管库中选择一个现有密钥，或者创建一个新密钥。

    ![从 Azure Key Vault 中选择密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   如果要创建新的密钥，请选择“新建”。 必须使用 RSA 密钥。 大小可以是 2048 或更大。

    ![在 Azure Key Vault 中创建新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    输入新密钥的名称，接受其他默认值，然后选择“创建”。 系统会通知你已在密钥保管库中创建密钥。

    ![命名新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. 对于 " **版本**"，可以从下拉列表中选择现有的密钥版本。

    ![选择新密钥的版本](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    如果要生成新的密钥版本，请选择 " **新建**"。

    ![打开用于创建新密钥版本的对话框](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    选择新密钥版本的设置，然后选择“创建”。

    ![创建新的密钥版本](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. 选择 key vault、密钥和密钥版本后，选择 " **选择**"。

    ![Azure Key Vault 中的密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    " **加密类型** " 设置显示所选的密钥保管库和密钥。

    ![用于客户管理的密钥的密钥和密钥保管库](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. 选择要用于管理此资源的客户托管密钥的标识的类型。 您可以使用在创建订单时生成的 **系统分配** 的标识，也可以选择用户分配的标识。

    用户分配的标识是一个独立的资源，可用于管理对资源的访问。 有关详细信息，请参阅[托管标识类型](/azure/active-directory/managed-identities-azure-resources/overview)。

    ![选择标识类型](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    若要分配用户标识，请选择 " **用户分配**"。 然后选择 " **选择用户标识**"，并选择要使用的托管标识。

    ![选择要使用的标识](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    你无法在此处创建新的用户标识。 若要了解如何创建一个角色，请参阅 [使用 Azure 门户创建、列出、删除或分配用户分配的托管标识](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)。

    所选用户标识显示在 " **加密类型** " 设置中。

    ![加密类型设置中显示的所选用户标识](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. 选择 " **保存** " 以保存更新的 **加密类型** 设置。

     ![保存客户托管的密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    密钥 URL 在“加密类型”下显示。

    ![客户管理的密钥 URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>更改密钥

若要更改当前使用的客户托管密钥的密钥保管库、密钥和/或密钥版本，请执行以下步骤：

1. 在 Data Box 订单的 "**概述**" 屏幕上，转到 "**设置**  >  " "**加密**"，然后单击 "**更改密钥**"。

    ![客户托管的密钥的 Data Box 订单概述屏幕-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. 选择 " **选择其他密钥保管库和密钥**"。

    ![Data Box 订单的 "概述" 屏幕上，选择其他密钥和密钥保管库选项](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. " **从 key vault 中选择密钥** " 屏幕会显示订阅，但不会显示密钥保管库、密钥或密钥版本。 你可以进行以下任何更改：

   - 从同一密钥保管库中选择一个不同的密钥。 在选择密钥和版本之前，需要选择密钥保管库。

   - 选择一个不同的密钥保管库并分配一个新密钥。

   - 更改当前项的版本。
   
    完成更改后，选择 " **选择**"。

    ![选择加密选项-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. 选择“保存”。

    ![保存更新的加密设置-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>更改标识

若要更改用于管理对客户托管的密钥的访问以实现此顺序的标识，请执行以下步骤：

1. 在已完成的 Data Box 订单的 "**概述**" 屏幕上，中转到 "**设置**" "  >  **加密**"。

2. 进行以下更改之一：

     - 若要更改为其他用户标识，请单击 " **选择不同的用户标识**"。 然后在屏幕右侧的面板中选择一个不同的标识，然后选择 " **选择**"。

       ![用于为客户托管的密钥更改用户分配的标识的选项](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - 若要切换到在创建订单时生成的系统分配的标识，请选择 "由 **选择标识类型****分配的系统**"。

     ![更改为客户托管密钥的系统分配选项](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. 选择“保存”。

    ![保存更新的加密设置-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>使用 Microsoft 托管密钥

若要从使用客户管理的密钥更改为你的订单的 Microsoft 托管密钥，请执行以下步骤：

1. 在已完成的 Data Box 订单的 "**概述**" 屏幕上，中转到 "**设置**" "  >  **加密**"。

2. 选择 " **类型**"，选择 " **Microsoft 托管密钥**"。

    ![Data Box 顺序的概述屏幕-5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. 选择“保存”。

    ![为 Microsoft 托管密钥保存更新的加密设置](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>排查错误

如果收到与客户管理的密钥相关的任何错误，请使用下表排除故障。

| 错误代码| 错误详细信息| 可恢复？|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| 无法提取密钥，因为客户管理的密钥已被禁用。| 是，通过启用密钥版本。|
| SsemUserErrorEncryptionKeyExpired| 无法提取密钥，因为客户管理的密钥已到期。| 是，通过启用密钥版本。|
| SsemUserErrorKeyDetailsNotFound| 无法提取密钥，因为无法找客户管理的密钥。| 如果删除了密钥保管库，则无法恢复客户管理的密钥。  如果将密钥保管库迁移到了其他租户，请参阅[订阅移动后更改密钥保管库租户 ID](../key-vault/general/move-subscription.md)。 如果删除了密钥保管库：<ol><li>是，如果它处于清除保护期内，请使用[恢复密钥保管库](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)中的步骤。</li><li>否，如果超出了清除保护期。</li></ol><br>如果密钥保管库进行了租户迁移，则可以使用以下步骤之一进行恢复： <ol><li>将密钥保管库还原回旧租户。</li><li>设置 `Identity = None`，然后将值设置回 `Identity = SystemAssigned`。 这将在新标识创建后删除并重新创建该标识。 在密钥保管库的“访问策略”中为新标识启用 `Get`、`Wrap` 和 `Unwrap` 权限。</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | 应用了客户管理的密钥，但密钥访问权限尚未授予或已被吊销，或者无法访问密钥保管库，因为启用了防火墙。 | 将所选标识添加到密钥保管库，以启用对客户托管密钥的访问权限。 如果 key vault 启用了防火墙，请切换到系统分配的标识，然后添加客户管理的密钥。 有关详细信息，请参阅如何 [启用密钥](#enable-key)。 |
| SsemUserErrorKeyVaultDetailsNotFound| 无法提取密钥，因为找不到与客户管理的密钥关联的密钥保管库。 | 如果删除了密钥保管库，则无法恢复客户管理的密钥。  如果将密钥保管库迁移到了其他租户，请参阅[订阅移动后更改密钥保管库租户 ID](../key-vault/general/move-subscription.md)。 如果删除了密钥保管库：<ol><li>是，如果它处于清除保护期内，请使用[恢复密钥保管库](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)中的步骤。</li><li>否，如果超出了清除保护期。</li></ol><br>如果密钥保管库进行了租户迁移，则可以使用以下步骤之一进行恢复： <ol><li>将密钥保管库还原回旧租户。</li><li>设置 `Identity = None`，然后将值设置回 `Identity = SystemAssigned`。 这将在新标识创建后删除并重新创建该标识。 在密钥保管库的“访问策略”中为新标识启用 `Get`、`Wrap` 和 `Unwrap` 权限。</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | 无法提取密钥，因为无法找客户管理的密钥。| 是，检查以下事项： <ol><li>密钥保管库在访问策略中是否仍具有 MSI。</li><li>标识的类型为“系统分配”。</li><li>对密钥保管库的访问策略中的标识启用获取、包装和解包权限。</li></ol>|
| SsemUserErrorUserAssignedLimitReached | 添加新的用户分配的标识失败，因为已达到可添加的用户分配标识总数限制。 | 请用更少的用户标识重试该操作，或在重试之前从资源中删除一些用户分配的标识。 |
| SsemUserErrorCrossTenantIdentityAccessForbidden | 托管标识访问操作失败。 <br> 注意：这适用于将订阅移到不同租户的情况。 客户必须手动将标识移动到新租户。 PFA mail 了解更多详细信息。 | 请将所选标识移动到订阅所在的新租户。 有关详细信息，请参阅如何 [启用密钥](#enable-key)。 |
| SsemUserErrorKekUserIdentityNotFound | 应用了客户管理的密钥，但在 active directory 中找不到具有密钥访问权限的用户分配的标识。 <br> 注意：在从 Azure 中删除用户标识时，这种情况很适用。| 请尝试向密钥保管库添加另一个用户分配的标识，以启用对客户托管密钥的访问。 有关详细信息，请参阅如何 [启用密钥](#enable-key)。 |
| SsemUserErrorUserAssignedIdentityAbsent | 无法提取密钥，因为无法找客户管理的密钥。 | 无法访问客户管理的密钥。  (与密钥关联的用户分配的标识) 已删除或 UAI 类型已更改。 |
| SsemUserErrorCrossTenantIdentityAccessForbidden | 托管标识访问操作失败。 <br> 注意：这适用于将订阅移到不同租户的情况。 客户必须手动将标识移动到新租户。 PFA mail 了解更多详细信息。 | 请尝试向密钥保管库添加另一个用户分配的标识，以启用对客户托管密钥的访问。 有关详细信息，请参阅如何 [启用密钥](#enable-key)。|
| SsemUserErrorKeyVaultBadRequestException | 应用了客户管理的密钥，但密钥访问权限尚未授予或已被吊销，或者无法访问密钥保管库，因为启用了防火墙。 | 将所选标识添加到密钥保管库，以启用对客户托管密钥的访问权限。 如果 key vault 启用了防火墙，请切换到系统分配的标识，然后添加客户管理的密钥。 有关详细信息，请参阅如何 [启用密钥](#enable-key)。 |
| 常规错误  | 无法提取密钥。| 这是一般性错误。 请联系 Microsoft 支持部门以解决此错误并确定后续步骤。|

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库？](../key-vault/general/overview.md)
- [快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索机密](../key-vault/secrets/quick-create-portal.md)
