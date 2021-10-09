---
title: 在 Azure Key Vault 中创建和检索托管密钥的属性 - Azure PowerShell
description: 本快速入门展示了如何使用 Azure PowerShell 在 Azure Key Vault 中设置和检索托管密钥
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom: devx-track-azurepowershell - mode-api
ms.openlocfilehash: 0658d715c7d7b2e7a34cd8fbd9ec3f76d9a67d10
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550435"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>快速入门：使用 PowerShell 在 Azure Key Vault 中设置和检索托管密钥

在本快速入门中，你将使用 Azure PowerShell 在 Azure Key Vault 中创建一个密钥保管库。 Azure Key Vault 是一项云服务，用作安全的机密存储。 可以安全地存储密钥、密码、证书和其他机密。 有关 Key Vault 的详细信息，可以参阅[概述](../general/overview.md)。 Azure PowerShell 用于通过命令或脚本创建和管理 Azure 资源。 完成该操作后，你将存储密钥。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 键入 `$PSVersionTable.PSVersion` 即可查找版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 来创建与 Azure 的连接。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 在 westus 位置创建一个名为 myResourceGroup 的资源组 。 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

主体 ID 将以“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”格式返回。

## <a name="create-a-managed-hsm"></a>创建托管 HSM

使用 Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) cmdlet 创建一个新的 Key Vault 托管 HSM。 需要提供某些信息：

- 托管 HSM 名称：由 3 到 24 个字符构成的字符串，只能包含数字 (0-9)、字母（a-z、A-Z）和连字符 (-)

  > [!Important]
  > 每个托管 HSM 都必须具有唯一名称。 在以下示例中，将 \<your-unique-managed-hsm-name\> 替换为托管 HSM 的名称。

- 资源组名称：myResourceGroup。
- 位置：EastUS。
- 主体 ID：将在上一部分获得的 Azure Active Directory 主体 ID 传递给“管理员”参数。 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

此 cmdlet 的输出会显示新创建的托管 HSM 的属性。 请记下下面列出的两个属性：

- **托管 HSM 名称**：为上面的 --name 参数提供的名称。
- **保管库 URI**：在本示例中为 https://&lt;your-unique-managed-hsm-name&gt;.vault.azure.net/。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="activate-your-managed-hsm"></a>激活托管 HSM

在激活 HSM 之前，所有数据平面命令都处于禁用状态。 你将无法创建密钥或分配角色。 只有在 create 命令期间分配的指定管理员才能激活 HSM。 必须下载[安全域](security-domain.md)才能激活 HSM。

若要激活 HSM，你需要：
- 至少 3 个 RSA 密钥对（最多 10 个）
- 指定解密安全域所需的最小密钥数（仲裁）

若要激活 HSM，请向 HSM 发送至少 3 个（最多 10 个）RSA 公钥。 HSM 利用这些密钥对安全域进行加密，并将其发回。 一旦成功完成此安全域下载，HSM 便可使用。 还需要指定仲裁，即解密安全域所需的最小私钥数。

下面的示例演示如何使用 `openssl`（适用于 Windows，详见[此处](https://slproweb.com/products/Win32OpenSSL.html)）生成 3 个自签名证书。

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> 安全地创建并存储在此步骤中生成的 RSA 密钥对和安全域文件。

使用 Azure PowerShell [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) cmdlet 下载安全域并激活托管 HSM。 下面的示例使用 3 个 RSA 密钥对（此命令只需要公钥），并将仲裁设置为 2。

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

请安全地存储安全域文件和 RSA 密钥对。 你需要它们进行灾难恢复，还需要用它们再创建一个共享同一安全域的托管 HSM，这样它们才可共享密钥。

成功下载安全域后，HSM 将处于活动状态并可供使用。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库并在其中存储了一个证书。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 请参阅 [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/) 参考
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
