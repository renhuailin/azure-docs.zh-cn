---
title: 使用 Azure 门户排查与 Azure Stack Edge Pro GPU 相关的激活错误 | Microsoft Docs
description: 介绍如何排查 Azure Stack Edge Pro GPU 激活问题以及与密钥保管库相关的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 66e62bffe28cc10bd49e1456fdd6e2ac1faebf6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442139"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>排查 Azure Stack Edge Pro GPU 设备上的激活问题 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何排查 Azure Stack Edge Pro GPU 设备上的激活问题。 


## <a name="activation-errors"></a>激活错误

下表汇总了与设备激活相关的错误以及推荐的相应解决方法。

| 错误消息| 推荐的解决方案 |
|------------------------------------------------------|--------------------------------------|
| 如果在使用激活密钥激活设备前删除了用于激活的 Azure 密钥保管库，则会收到此错误。 <br> ![密钥保管库错误 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | 如果已删除密钥保管库，则可以在保管库处于清除保护期时将其恢复。 请按照[恢复密钥保管库](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)中的步骤操作。 <br>如果清除保护期已过，则无法恢复密钥保管库。 联系 Microsoft 支持部门了解后续步骤。 |
| 如果在激活设备后删除了 Azure 密钥保管库，然后尝试执行涉及加密的任何操作（例如添加用户、添加共享、配置计算），则会收到此错误  。 <br> ![密钥保管库错误 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | 如果已删除密钥保管库，则可以在保管库处于清除保护期时将其恢复。 请按照“恢复密钥保管库”中的步骤操作。 <br>如果清除保护期已过，则无法恢复密钥保管库。 联系 Microsoft 支持部门了解后续步骤。 |
| 如果删除 Azure 密钥保管库的通道完整性密钥后，尝试执行涉及加密的任何操作（例如添加用户、添加共享、配置计算），则会收到此错误  。 <br> ![密钥保管库错误 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | 如果密钥保管库中的通道完整性密钥已删除，但仍处于清除保护期，请按照[撤消删除密钥保管库密钥](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)中的步骤进行操作。 <br>如果清除保护期已过，并且你备份了密钥，则可以通过备份还原，否则将无法恢复密钥。 联系 Microsoft 支持部门了解后续步骤。 |
| 如果因任何错误导致激活密钥生成失败，则会收到此错误。 通知中显示了更多详细信息。 <br> ![密钥保管库错误 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | 请确保[访问防火墙保护下的 Azure 密钥保管库](../key-vault/general/access-behind-firewall.md)中指定的端口和 URL 在防火墙上是打开的，以便访问密钥保管库。 等待几分钟，然后重试该操作。 如果问题持续出现，请联系 Microsoft 支持。 |
| 如果用户拥有只读权限，则无法生成激活密钥，并且系统将显示此错误。 <br> ![密钥保管库错误 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | 这可能是因为你没有正确的访问权限，或者未注册 Microsoft.KeyVault。<li>请确保你在资源组级别拥有用于 Azure Stack Edge 资源的所有者或参与者访问权限。</li><li>确保注册了 Microsoft.KeyVault 资源提供程序。 若要注册资源提供程序，请访问用于 Azure Stack Edge 资源的订阅。 转到“资源提供程序”，搜索 Microsoft.KeyVault，选择它，然后选择“注册”。</li> |

## <a name="next-steps"></a>后续步骤

- 详细了解如何[排查设备问题](azure-stack-edge-gpu-troubleshoot.md)。
