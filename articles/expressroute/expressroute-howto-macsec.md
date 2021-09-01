---
title: Azure ExpressRoute：配置 MACsec
description: 本文可帮助你配置 MACsec 来保护边缘路由器与 Microsoft 边缘路由器之间的连接。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 445010e345e92913f32705fc801a6521703ba84f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228749"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>在 ExpressRoute Direct 端口上配置 MACsec

本文可帮助你配置 MACsec，来使用 PowerShell 保护边缘路由器和 Microsoft 边缘路由器之间的连接。

## <a name="before-you-begin"></a>准备阶段

在开始配置之前，请确认以下事项：

* 你了解 [ExpressRoute Direct 预配工作流](expressroute-erdirect-about.md)。
* 已创建 [ExpressRoute Direct 端口资源](expressroute-howto-erdirect.md)。
* 如果要在本地运行 PowerShell，请验证计算机上是否安装了最新版本的 Azure PowerShell。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>登录并选择正确的订阅

若要启动配置，请登录到你的 Azure 帐户，选择要使用的订阅。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1.创建 Azure Key Vault、MACsec 机密和用户标识

1. 创建 Key Vault 实例以在新资源组中存储 MACsec 机密。

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    如果已有密钥保管库或资源组，则可以重复使用它们。 但是，应在现有密钥保管库上启用[软删除功能](../key-vault/general/soft-delete-overview.md)，这一点非常重要。 如果未启用软删除，可以使用以下命令启用：

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 创建用户标识。

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    如果 New-AzUserAssignedIdentity 未被识别为有效的 PowerShell cmdlet，请安装以下模块（在管理员模式下），然后重新运行上述命令。

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. 创建连接关联密钥 (CAK) 和连接关联密钥名称 (CKN)，并将其存储在密钥保管库中。

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
   > [!NOTE]
   > CKN 必须是长度为 64 的十六进制数字的均匀字符串（0-9，A-F）。
   >
   > CAK 长度取决于指定的密码套件：
   >
   > * 对于 GcmAes128，CAK 必须是长度为 32 的十六进制数字的均匀字符串（0-9，A-F）。
   >
   > * 对于 GcmAes256，CAK 必须是长度为 64 的十六进制数字的均匀字符串（0-9，A-F）。
   >

4. 将 GET 权限分配给用户标识。

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   现在，此标识可以从密钥保管库中获取机密（例如 CAK 和 CKN）。
5. 设置 ExpressRoute 使用的此用户标识。

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2.在 ExpressRoute Direct 端口上配置 MACsec

### <a name="to-enable-macsec"></a>以启用 MACsec

每个 ExpressRoute Direct 实例都有两个物理端口。 可以选择同时在两个端口上启用 MACsec，或一次在一个端口上启用 MACsec。 如果已开始使用 ExpressRoute Direct，一次执行一个端口（在为另一个端口提供服务的同时将流量切换到活动端口）有助于最大限度地减少中断。

1. 设置 MACsec 机密和密码，将用户标识与端口关联，以便 ExpressRoute 管理代码可以根据需要访问 MACsec 机密。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. （可选）如果端口处于“管理关闭”状态，可以运行以下命令来打开端口。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    此时，Microsoft 端的 ExpressRoute Direct 端口上启用了 MACsec。 如果尚未在边缘设备上配置它，可以立即使用相同的 MACsec 机密和密码对其进行配置。

### <a name="to-disable-macsec"></a>要禁用 MACsec

如果 ExpressRoute Direct 实例上不再需要 MACsec，可以运行以下命令来禁用它。

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

此时，Microsoft 端的 ExpressRoute Direct 端口上禁用了 MACsec。

### <a name="test-connectivity"></a>测试连接
在 ExpressRoute Direct 端口上配置 MACsec（包括 MACsec 密钥更新）后，请[检查](expressroute-troubleshooting-expressroute-overview.md)线路的 BGP 会话是否启动并运行。 如果端口上没有任何线路，请先创建一个，然后设置线路的 Azure 专用对等互连或 Microsoft 对等互连。 如果在网络设备和 Microsoft 网络设备之间 MACsec 配置错误（包括 MACsec 密钥不匹配），则不会看到第 2 层的 ARP 解析和第 3 层的 BGP 建立。 如果所有内容都配置正确，应该会看到 BGP 路由在两个方向上正确播发通告，并且应用程序数据流会相应通过 ExpressRoute。

## <a name="next-steps"></a>后续步骤
1. [在 ExpressRoute Direct 上创建 ExpressRoute 线路](expressroute-howto-erdirect.md)
2. [将 ExpressRoute 线路链接到 Azure 虚拟网络](expressroute-howto-linkvnet-arm.md)
3. [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
