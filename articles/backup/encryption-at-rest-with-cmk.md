---
title: 使用客户托管密钥加密备份数据
description: 了解 Azure 备份如何使用客户管理的密钥 (CMK) 加密备份数据 (CMK)。
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f16974d00f4801f288180814daf9ff5ed4558748
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778717"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客户托管密钥加密备份数据

Azure 备份允许使用客户管理的密钥 (CMK)（而不是默认启用的平台管理的密钥）加密备份数据。 用于加密备份数据的密钥必须存储在 [Azure Key Vault](../key-vault/index.yml) 中。

用于加密备份的加密密钥可能与用于源的加密密钥不同。 数据通过基于 AES 256 的数据加密密钥 (DEK) 受到保护，进而又通过密钥 (KEK) 受到保护。 这使你可以完全控制数据和密钥。 若要允许加密，需要向恢复服务保管库授予对 Azure 密钥保管库中加密密钥的访问权限。 你可以在需要时更改密钥。

本文将介绍以下主题：

- 删除恢复服务保管库
- 配置恢复服务保管库以使用客户管理的密钥加密备份数据
- 对使用客户管理的密钥加密的保管库执行备份
- 从备份还原数据

## <a name="before-you-start"></a>准备工作

- 此功能仅支持对新的恢复服务保管库进行加密。 不支持任何包含已注册或已尝试注册的现有项目的保管库。

- 一旦对恢复服务保管库启用该功能，使用客户管理的密钥进行加密则不能改回使用平台管理的密钥（默认）。 可以根据需要更改加密密钥。

- 此功能当前不支持使用 MARS 代理进行备份，因此你可能无法使用 CMK 加密的保管库。 MARS 代理使用基于用户密码的加密。 该功能也不支持对传统 VM 进行备份。

- 此功能与 [Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)无关，后者通过 BitLocker（适用于 Windows）和 DM-Crypt（适用于 Linux）对 VM 磁盘使用基于来宾的加密

- 恢复服务保管库只能使用存储在同一区域的 Azure Key Vault 中的密钥进行加密。 此外，密钥必须仅为 RSA 密钥，并且应处于启用状态。

- 当前不支持跨资源组和订阅移动 CMK 加密的恢复服务保管库。
- 使用客户管理的密钥加密的恢复服务保管库不支持跨区域还原备份的实例。
- 将已使用客户管理的密钥加密的恢复服务保管库移到新租户时，需要更新恢复服务保管库，以重新创建并重新配置保管库的托管标识和 CMK（应位于新租户中）。 如果不执行此操作，备份和还原操作将会失败。 此外，需要重新配置在订阅中设置的任何基于角色的访问控制 (RBAC) 权限。

- 通过 Azure 门户和 PowerShell 可配置此功能。

    >[!NOTE]
    >使用 Az 模块 5.3.0 或更高版本，在恢复服务保管库中使用客户管理的密钥进行备份。
    
    >[!Warning]
    >如果使用 PowerShell 来管理用于 Azure 备份的加密密钥，我们不建议从门户更新密钥。<br>如果从门户更新密钥，则在支持新模型的 PowerShell 更新可用之前，将无法使用 PowerShell 进一步更新加密密钥。 但是，你可以继续从 Azure 门户更新密钥。

如果尚未创建和配置恢复服务保管库，则可以[在此阅读如何执行操作](backup-create-rs-vault.md)。

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>将保管库配置为使用客户管理的密钥进行加密

此部分涉及以下步骤：

1. 为恢复服务保管库启用托管标识

1. 为保管库分配访问 Azure 密钥保管库中加密密钥的权限

1. 在 Azure 密钥保管库中启用软删除和清除保护

1. 为恢复服务保管库分配加密密钥

要达到预期效果，必须按照上述顺序执行所有步骤。 下面将详细讨论各个步骤。

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>为恢复服务保管库启用托管标识

Azure 备份使用系统分配的托管标识和用户分配的托管标识对恢复服务保管库进行身份验证，以访问存储在 Azure 密钥保管库中的加密密钥。 要为恢复服务保管库启用托管标识，请按照以下步骤执行操作。

>[!NOTE]
>启用后，不得禁用托管标识（即使是暂时禁用）。 禁用托管标识可能导致出现不一致的行为。

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>为保管库启用系统分配的托管标识

**在门户中：**

1. 转到“恢复服务保管库”->“标识”

    ![标识设置](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. 导航到“系统分配”选项卡。

1. 将“状态”更改为“开”。

1. 单击“保存”，为保管库启用标识。

此时将生成一个对象 ID，即系统分配的保管库的托管标识。

>[!NOTE]
>启用后，不得禁用托管标识（即使是暂时禁用也不可以）。 禁用托管标识可能导致出现不一致的行为。


**使用 PowerShell：**

使用[Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault)命令启用恢复服务保管库的系统分配托管标识。

示例：

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

输出：

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault-in-preview"></a>将用户分配的托管标识分配给保管库（预览版）

>[!Note]
>- 使用用户分配的托管标识进行 CMK 加密的保管库不支持使用专用终结点进行备份。
>- 尚不支持将限制对特定网络访问的 Azure Key Vault 与用户分配的托管标识一起用于 CMK 加密。

若要为恢复服务保管库分配用户分配的托管标识，请执行以下步骤：

1.  转到“恢复服务保管库”->“标识”

    ![将用户分配的托管标识分配给保管库](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  导航到“用户分配”选项卡。

1.  单击“+ 添加”以添加用户分配的托管标识。

1.  在打开的“添加用户分配的托管标识”边栏选项卡中，为标识选择订阅。

1.  从列表中选择标识。 你还可以按标识或资源组的名称进行筛选。

1.  完成后，单击“添加”以完成标识分配。

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>为恢复服务保管库分配访问 Azure Key Vault 中加密密钥的权限

>[!Note]
>如果使用用户分配的标识，则必须为用户分配的标识分配相同的权限。

现在，需要允许恢复服务保管库访问包含加密密钥的 Azure Key Vault。 通过允许恢复服务保管库的托管标识访问 Key Vault 可完成此任务。

**在门户中**：

1. 转到“Azure Key Vault”->“访问策略”。 继续选择“+添加访问策略”。

    ![添加访问策略](./media/encryption-at-rest-with-cmk/access-policies.png)

1. 在“密钥权限”下，选择“获取”、“列表”、“解包密钥”和“包装密钥”操作。 这样将对密钥指定允许的操作。

    ![分配密钥权限](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. 转到“选择主体”，然后在搜索框中使用其名称或托管标识搜索保管库。 显示后，选择保管库，然后选择窗格底部的“选择”。

    ![选择主体](./media/encryption-at-rest-with-cmk/select-principal.png)

1. 完成后，选择“添加”以添加新的访问策略。

1. 选择“保存”，以保存对 Azure Key Vault 访问策略所做的更改。

>[!NOTE] 
>你还可向包含上述权限的恢复服务保管库分配 RBAC 角色，例如“[密钥保管库加密主管](../key-vault/general/rbac-guide.md#azure-built-in-roles-for-key-vault-data-plane-operations)”角色。<br><br>这些角色可能包含上述权限之外的其他权限。

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>在 Azure 密钥保管库中启用软删除和清除保护

需要在存储加密密钥的 Azure Key Vault 上启用软删除和清除保护。 可以在 Azure Key Vault UI 中执行此操作，如下所示。 （或者，可以在创建 Key Vault 时设置这些属性）。 有关这些 Key Vault 属性的详细信息，请参阅[此处](../key-vault/general/soft-delete-overview.md)。

![启用软删除和清除保护](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

此外，还可以按照以下步骤通过 PowerShell 启用软删除和清除保护：

1. 登录 Azure 帐户。

    ```azurepowershell
    Login-AzAccount
    ```

1. 选择包含你的保管库的订阅。

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. 启用软删除

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. 启用清除保护

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>为 RS 保管库分配加密密钥

>[!NOTE]
> 在继续操作之前，请确保以下几点：
>
> - 上述所有步骤均已成功完成：
>   - 已启用恢复服务保管库的托管标识，并已为其分配所需的权限
>   - 已对 Azure Key Vault 启用软删除和清除保护
> - 要为其启用 CMK 加密的恢复服务保管库不包含任何受保护或已注册项目

确保完成上述各项后，继续为保管库选择加密密钥。

### <a name="to-assign-the-key-in-the-portal"></a>在门户中分配密钥

1. 转到“恢复服务保管库”->“属性”

    ![加密设置](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. 选择“加密设置”下的“更新”。

1. 在“加密设置”窗格中，选择“使用自己的密钥”，然后使用以下方法之一继续指定密钥。 确保要使用的密钥是 RSA 2048 密钥，并处于已启用状态。

    1. 输入对此恢复服务保管库中的数据加密想要使用的“密钥 URI”。 此外，还需要指定包含此密钥的 Azure Key Vault 所在的订阅。 密钥 URI 可以通过 Azure Key Vault 中对应的密钥获取。 确保正确复制密钥 URI。 建议使用提供的带密钥标识符的“复制到剪贴板”按钮。

        >[!NOTE]
        >使用密钥 URI 指定加密密钥时，不会自动旋转密钥。 因此需要手动完成密钥更新，方法是在需要时指定新密钥。

        ![输入密钥 URI](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. 浏览并从密钥选取器窗格的 Key Vault 中选择密钥。

        >[!NOTE]
        >如果使用密钥选取器窗格指定加密密钥，则每当启用密钥的新版本时，都将自动旋转密钥。 [详细了解](#enabling-auto-rotation-of-encryption-keys)有关启用加密密钥自动轮替的信息。

        ![从密钥保管库中选择密钥](./media/encryption-at-rest-with-cmk/key-vault.png)

1. 选择“保存”。

1. 跟踪加密密钥更新的进度和状态：可以使用左侧导航栏上的“备份作业”视图跟踪加密密钥分配的进度和状态。 状态将很快更改为“已完成”。 现在，保管库将使用指定的密钥作为 KEK 加密所有数据。

    ![状态“已完成”](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    加密密钥更新也会记录在保管库的活动日志中。

    ![活动日志](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>使用 PowerShell 分配密钥

使用 [Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) 命令可启用使用客户管理的密钥的加密，并分配或更新要使用的加密密钥。

示例：

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

输出：

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> 想要更新或更改加密密钥时，此过程完全相同。 如果要更新和使用其他密钥保管库（不同于当前使用的密钥保管库）中的密钥，请确保：
>
> - 密钥保管库与恢复服务保管库位于同一区域
>
> - 已对密钥保管库启用软删除和清除保护
>
> - 恢复服务保管库具有访问密钥保管库所需的权限。

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>备份到使用客户管理的密钥加密的保管库

在继续配置保护之前，强烈建议您确保遵守以下清单。 这一点很重要，因为一旦项目配置为备份（或试图配置）到非 CMK 加密的保管库，则无法对其启用使用客户管理的密钥进行加密，而它将继续使用平台管理的密钥。

>[!IMPORTANT]
> 在继续配置保护之前，必须已成功完成以下步骤：
>
>1. 已创建恢复服务保管库
>1. 已启用了恢复服务保管库的系统分配的托管标识，或已为保管库分配用户分配的托管标识
>1. 已向恢复服务保管库（或用户分配的托管标识）分配权限，以便从 Key Vault 访问加密密钥
>1. 已对 Key Vault 启用软删除和清除保护
>1. 已为恢复服务保管库分配有效的加密密钥
>
>在上述所有步骤均已确认后，再继续配置备份。

配置使用客户管理的密钥加密的恢复服务保管库并对其执行备份的过程，与对使用平台管理的密钥加密的保管库执行相应操作的过程相同，体验不变。 这一点对于 [Azure VM 备份](./quick-backup-vm-portal.md)以及 VM 内的工作负荷（例如 [SAP HANA](./tutorial-backup-sap-hana-db.md)、[SQL Server](./tutorial-sql-backup.md) 数据库）备份同样如此。

## <a name="restoring-data-from-backup"></a>从备份还原数据

### <a name="vm-backup"></a>VM 备份

按照[此处](./backup-azure-arm-restore-vms.md)所述的步骤可以还原恢复服务保管库中存储的数据。 从使用客户管理的密钥加密的恢复服务保管库还原时，可以选择使用磁盘加密集 (DES) 对还原的数据加密。

#### <a name="restoring-vm--disk"></a>还原 VM/磁盘

1. 从“快照”恢复点恢复磁盘/VM 时，将使用用于加密源 VM 磁盘的 DES 对还原的数据加密。

1. 从恢复类型为“保管库”的恢复点还原磁盘/VM 时，可以选择使用 DES 对还原的数据加密（还原时指定）。 或者，可以选择继续还原数据而不指定 DES，在这种情况下，将使用 Microsoft 托管密钥对还原数据进行加密。

可以不考虑启动还原时所做的选择，在还原完成后再对还原的磁盘/VM 加密。

![还原点](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>从保管库恢复点还原时选择磁盘加密集

**在门户中**：

磁盘加密集在还原窗格的“加密设置”下指定，如下所示：

1. 在“使用密钥加密磁盘”中，选择“是”。

1. 从下拉列表中，选择要用于还原的磁盘的 DES。 确保有权访问 DES。

>[!NOTE]
>如果要还原使用 Azure 磁盘加密的 VM，则在还原时选择 DES 的功能不可用。

![使用密钥加密磁盘](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**使用 PowerShell**：

将 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) 命令与参数 [`-DiskEncryptionSetId <string>`] 结合使用，[指定 DES](/powershell/module/az.compute/get-azdiskencryptionset) 用于对还原的磁盘进行加密。 有关从 VM 备份还原磁盘的详细信息，请参阅[此文](./backup-azure-vms-automation.md#restore-an-azure-vm)。

示例：

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>还原文件

执行文件还原时，将使用用于目标位置加密的密钥对还原的数据进行加密。

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>还原 Azure VM 中的 SAP HANA/SQL 数据库

从 Azure VM 中运行的备份 SAP HANA/SQL 数据库还原时，将使用目标存储位置使用的加密密钥对还原的数据进行加密。 VM 磁盘加密可能使用客户管理的密钥，也可能使用平台管理的密钥。

## <a name="additional-topics"></a>其他主题

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>在保管库创建（预览版）中使用客户管理的密钥启用加密

>[!NOTE]
>使用客户管理的密钥在保管库创建中启用加密目前为有限的公共预览版，并且需要订阅的允许列表。 若要注册预览版，请填写[表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u)并发送邮件到 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 与我们联系。

如果订阅位于允许列表中，则将显示“备份加密”选项卡。 这将允许你在创建新的恢复服务保管库期间使用客户管理的密钥对备份启用加密。 若要启用加密，请执行以下步骤：

1. 在“基本信息”选项卡旁边的“备份加密”选项卡上，指定要用于加密的加密密钥和标识。

   ![在保管库级别启用加密](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >这些设置仅应用于 Azure 备份，并且是可选的。

1. 选择“使用客户管理的密钥”作为加密类型。

1. 若要指定用于加密的密钥，请选择相应的选项。

   你可以提供加密密钥的 URI，也可以浏览并选择密钥。 在使用“选择密钥保管库”选项指定密钥时，将自动启用加密密钥的自动轮替。 [了解有关自动轮替的详细信息](#enabling-auto-rotation-of-encryption-keys)。 

1. 指定用户分配的托管标识，以管理使用客户管理的密钥进行的加密。 单击“选择”以浏览并选择所需标识。

1. 完成后，可切换到“标记”选项卡添加信息（可选），并继续创建保管库。

### <a name="enabling-auto-rotation-of-encryption-keys"></a>启用加密密钥的自动轮替

在指定必须用于加密备份的客户管理的密钥时，请使用以下方法指定该密钥：

- 输入密钥 URI
- 从密钥保管库中选择

使用“从密钥保管库中选择”选项可帮助启用所选密钥的自动轮替。 这免除了更新到下一版本的手动操作。 但是，使用此选项时请注意：
- 密钥版本更新可能最多需要一小时才能生效。
- 密钥的新版本生效后，旧版本也应在密钥更新生效后至少一个后续备份作业中保持可用（处于已启用状态）。

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>使用 Azure 策略利用客户管理的密钥进行审核和强制加密（预览版）

Azure 备份允许使用 Azure 策略利用客户管理的密钥对恢复服务保管库中的数据进行审核和强制加密。 使用 Azure 策略：

- 此审核策略可用于审核使用客户管理的密钥（密钥为 2021 年 4 月 1 日之后启用）进行加密的保管库。 对于在此日期之前启用 CMK 加密的保管库，该策略可能无法应用，也可能显示误报结果（也就是说，即使已启用 CMK 加密，这些保管库仍可能被报告为不兼容）。
- 若要使用此审核策略对使用 CMK 加密（密钥为 2021 年 4 月 1 日之前启用）的保管库进行审核，请使用 Azure 门户更新加密密钥。 这有助于升级到新模型。 如果不希望更改加密密钥，请通过密钥 URI 或密钥选择选项再次提供相同的密钥。 

   >[!Warning]
    >如果使用 PowerShell 来管理用于 Azure 备份的加密密钥，我们不建议从门户更新密钥。<br>如果从门户更新密钥，则在支持新模型的 PowerShell 更新可用之前，将无法使用 PowerShell 进一步更新加密密钥。 但是，你可以继续从 Azure 门户更新密钥。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>是否可以使用客户管理的密钥加密现有的备份保管库？

不能，只能对新保管库启用 CMK 加密。 因此，保管库不能包含任何受保护的项。 事实上，在启用使用客户管理的密钥加密之前，不得试图向保管库保护任何项。

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>我尝试过向保管库中保护某一项，但失败了，保管库中仍不会包含任何受保护项。 能否为此保管库启用 CMK 加密？

不能，保管库不能尝试向其中保护任何项。

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>我有一个使用 CMK 加密的保管库。 以后是否可以恢复为使用平台管理的密钥进行加密，即使保管库中已有受保护的备份项目？

不能，启用 CMK 加密后，无法再恢复为使用平台管理的密钥。 可以根据需要更改使用的密钥。

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Azure 备份的 CMK 加密是否也适用于 Azure Site Recovery？

不适用，本文仅讨论备份数据的加密。 对于 Azure Site Recovery，需要在服务中可用后单独设置属性。

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>我漏掉了本文中的一个步骤，并继续对数据源进行了保护。 是否仍然可以使用 CMK 加密？

未遵循本文中的步骤操作并继续保护项目，可能导致保管库无法使用客户管理的密钥进行加密。 因此，建议先查看[此清单](#backing-up-to-a-vault-encrypted-with-customer-managed-keys)，再继续保护项目。

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>使用 CMK 加密会增加备份成本吗？

使用 CMK 进行备份加密不会产生任何额外费用。 但是，使用存储密钥的 Azure Key Vault 可能会继续产生费用。

## <a name="next-steps"></a>后续步骤

- [Azure 备份中的安全功能概述](security-overview.md)
