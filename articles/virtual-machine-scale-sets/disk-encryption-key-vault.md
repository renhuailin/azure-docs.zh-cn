---
title: 创建和配置用于 Azure 磁盘加密的密钥保管库
description: 本文介绍创建和配置用于 Azure 磁盘加密的密钥保管库的步骤
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 904a78d9c8fcdc83b7fa837d744cf955ceda8d2b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691383"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>为 Azure 磁盘加密创建和配置密钥保管库

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 统一规模集

Azure Disk Encryption 使用 Azure Key Vault 来控制和管理磁盘加密密钥和机密。  有关 Key Vault 的详细信息，请参阅 [Azure Key Vault 入门](../key-vault/general/overview.md)和[保护 Key Vault](../key-vault/general/secure-your-key-vault.md)。

创建和配置用于 Azure 磁盘加密的密钥保管库涉及三个步骤：

1. 创建资源组（如果需要）。
2. 创建密钥保管库。 
3. 设置密钥保管库高级访问策略。

下面的快速入门说明了这些步骤：

你还可以根据需要生成或导入密钥加密密钥 (KEK)。

## <a name="install-tools-and-connect-to-azure"></a>安装工具并连接到 Azure

可以使用 [Azure CLI](/cli/azure/)、[Azure PowerShell Az 模块](/powershell/azure/)或 [Azure 门户](https://portal.azure.com)来完成本文中的步骤。

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用 Azure CLI 或 Azure PowerShell 之前，必须先连接到 Azure 订阅。 为此，可以[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)、[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps)或在出现提示时向 Azure 门户提供凭据。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密概述](disk-encryption-overview.md)
- [使用 Azure CLI 加密虚拟机规模集](disk-encryption-cli.md)
- [使用 Azure PowerShell 加密虚拟机规模集](disk-encryption-powershell.md)

