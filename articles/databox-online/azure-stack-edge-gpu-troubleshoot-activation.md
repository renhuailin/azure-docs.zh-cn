---
title: 使用 Azure 门户排查与 Azure Stack Edge Pro GPU 相关的激活错误 | Microsoft Docs
description: 介绍如何排查 Azure Stack Edge Pro GPU 激活问题以及与密钥保管库相关的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 1f6729fc0a723a21f66380a397a222bef23cba9e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750315"
---
# <a name="troubleshoot-activation-or-secret-deletion-issues-on-azure-key-vault-for-your-azure-stack-edge-pro-gpu-device"></a>排查 Azure Stack Edge Pro GPU 设备的 Azure 密钥保管库的激活或机密删除问题 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何排查 Azure Stack Edge Pro GPU 设备上的激活问题。 


## <a name="activation-errors"></a>激活错误

下表汇总了与设备激活相关的错误以及推荐的相应解决方法。

| 错误消息| 推荐的解决方案 |
|------------------------------------------------------|--------------------------------------|
| 如果在使用激活密钥激活设备前删除了用于激活的 Azure 密钥保管库，则会收到此错误。 <br> ![密钥保管库错误 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | 如果已删除密钥保管库，你可以将其还原，前提是保管库仍在“清除保护”持续时间内。 请按照[恢复密钥保管库](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)中的步骤操作。 <br>如果“清除保护”持续时间已过，则需要通过“[恢复密钥保管库](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)”边栏选项卡创建新的密钥保管库。 |
| 如果在激活设备后删除了 Azure 密钥保管库，然后尝试执行涉及加密的任何操作（例如添加用户、添加共享或配置计算），则会收到此错误  。 <br> ![密钥保管库错误 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | 如果已删除密钥保管库，你可以将其还原，前提是保管库仍在“清除保护”持续时间内。 请按照[恢复密钥保管库](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)中的步骤操作。 <br>如果“清除保护”持续时间已过，则需要根据[恢复密钥保管库](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)中所述创建新的密钥保管库。 |
| 如果因任何错误导致激活密钥生成失败，则会收到此错误。 通知包括更多详细信息。 <br> ![密钥保管库错误 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | 请确保[访问防火墙保护下的 Azure 密钥保管库](../key-vault/general/access-behind-firewall.md)中指定的端口和 URL 在防火墙上是打开的，以便你能够访问密钥保管库。 等待几分钟，然后重试该操作。 如果问题持续出现，请联系 Microsoft 支持。 |
| 如果用户拥有只读权限，则无法生成激活密钥，并且系统将显示此错误。 <br> ![密钥保管库错误 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | 这可能是因为你没有正确的访问权限，或者未注册 `Microsoft.KeyVault`。<li>请确保你在资源组级别拥有用于 Azure Stack Edge 资源的所有者或参与者访问权限。</li><li>确保已注册 `Microsoft.KeyVault` 资源提供程序。 若要注册资源提供程序，请访问用于 Azure Stack Edge 资源的订阅。 转到“资源提供程序”，搜索 Microsoft.KeyVault，选择它，然后选择“注册”。 有关详细信息，请参阅[注册资源提供程序](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。</li> |


## <a name="unregistered-resource-provider-errors"></a>“资源提供程序未注册”错误

| 错误消息| 推荐的解决方案 |
|------------------------------------------------------|--------------------------------------|
| 如果密钥保管库资源提供程序未注册，则在生成激活密钥期间创建密钥保管库时会出现错误。 <br> <!--![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 不会生成激活密钥。 <br>确保已注册 `Microsoft.KeyVault` 资源提供程序。 若要注册资源提供程序，请访问用于 Azure Stack Edge 资源的订阅。 转到“资源提供程序”，搜索 Microsoft.KeyVault，选择它，然后选择“注册”。 <br>有关详细信息，请参阅[注册资源提供程序](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。</li> |
| 如果存储资源提供程序未注册，则在为审核日志创建存储帐户时会出现错误。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 此错误不是阻塞性错误，系统会生成激活密钥。 <br>存储资源提供程序通常会自动注册，但如果未注册，请按照[注册资源提供程序](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)中的步骤为订阅注册 `Microsoft.Storage`。  |

## <a name="key-vault-or-secret-deletion-errors"></a>密钥保管库或机密删除错误

| 错误消息| 推荐的解决方案 |
|------------------------------------------------------|--------------------------------------|
| 如果删除 Azure 密钥保管库的通道完整性密钥，然后尝试执行涉及加密的任何操作（例如“添加用户”、“添加共享”或“配置计算”），则会收到此错误  。  |如果密钥保管库中的通道完整性密钥已删除，但密钥仍在“清除保护”持续时间内，请按照[撤消删除密钥保管库密钥](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)中的步骤进行操作。 <br>如果清除保护期已过，并且你已备份了密钥，则可以从备份中复原密钥。 否则，将无法恢复该密钥。 联系 Microsoft 支持部门了解后续步骤。 |
<!--|与 Noopur 核对后删除此注释 - 客户无需执行任何操作 - 如果在激活设备之前删除了 Azure 密钥保管库中的通道完整性密钥，并且重新生成了激活密钥，则会收到此错误。 <br> ![密钥保管库错误 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png) | 将重新创建通道完整性密钥并更新元数据。
|-->

## <a name="audit-logging-errors"></a>审核日志记录错误

| 错误消息| 推荐的解决方案 |
|------------------------------------------------------|--------------------------------------|
| 如果无法为密钥保管库创建诊断设置，则会出现此错误。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 此错误不是阻塞性错误，系统会生成激活密钥。 <br> 可以手动[创建诊断设置用于存储审核日志](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs)。 |
| 如果存储帐户创建失败（例如，因为已存在具有指定名称的帐户），则会出现此错误。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 可以手动创建存储帐户，并将其链接到密钥保管库上的诊断设置。 然后，此帐户会用来存储审核日志。 <br> 有关详细信息，请参阅[为日志创建存储帐户](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs)。  |
|如果删除了 Azure Stack Edge 资源的系统分配的托管标识，则会出现此错误。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Azure Stack Edge 资源的“安全性”边栏选项卡中会显示警报。 选择此警报，以[通过“恢复密钥保管库”边栏选项卡创建新的托管标识](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)  |
| 如果托管标识无权访问密钥保管库，则会出现此错误。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Azure Stack Edge 资源的“安全性”边栏选项卡中会显示警报。 选择此警报，以[通过“恢复密钥保管库”边栏选项卡向托管标识授予对密钥保管库的访问权限](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)。  |

## <a name="resource-move-errors"></a>资源移动错误

| 错误消息| 推荐的解决方案 |
|------------------------------------------------------|--------------------------------------|
| 如果跨资源组或订阅移动密钥保管库资源，则会出现此错误。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 系统对密钥保管库资源移动操作的处理方式等同于密钥保管库删除操作。 如果密钥保管库仍在“清除保护”持续时间内，则你可以还原该保管库。 如果“清除保护”持续时间已过，则需要创建新的密钥保管库。 有关上述任一情况的详细信息，请参阅[恢复密钥保管库](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)。    |
| 如果跨租户移动你使用的订阅，则会出现此错误。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 重新配置托管标识并创建新的密钥保管库。 还可以移动密钥保管库资源，在这种情况下，只需重新配置托管标识。 对于上述每种情况，请参阅[恢复密钥保管库](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)。   |
| 如果跨资源组或订阅移动用于审核日志的存储帐户资源，则不会出现错误。  | 可以[创建新的存储帐户，并将其配置为存储审核日志](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs)。 |

## <a name="other-errors"></a>其他错误

| 错误消息| 推荐的解决方案 |
|------------------------------------------------------|--------------------------------------|
| 如果为密钥保管库配置了网络限制，则会出现此错误。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 由于存在网络限制，服务无法区分删除了密钥保管库或密钥保管库不可访问的情况。 在每种情况下，你都会定向到“[恢复密钥保管库](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)”边栏选项卡。     |


## <a name="next-steps"></a>后续步骤

- [安装 Azure Stack Edge Pro with GPU](azure-stack-edge-gpu-deploy-install.md)。
