---
title: 配置 Azure 虚拟桌面 AD FS 单一登录 - Azure
description: 如何为 Azure 虚拟桌面环境配置 AD FS 单一登录。
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/30/2021
ms.author: helohr
ms.openlocfilehash: 3c6e61754d9332cbdfbea6b971363c1b0d6cb4fe
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289421"
---
# <a name="configure-ad-fs-single-sign-on-for-azure-virtual-desktop"></a>为 Azure 虚拟桌面配置 AD FS 单一登录

本文引导你完成为 Azure 虚拟桌面配置 Active Directory 联合身份验证服务 (AD FS) 单一登录 (SSO) 的过程。

> [!NOTE]
> Azure 虚拟桌面（经典）不支持此功能。

## <a name="requirements"></a>要求

在配置 AD FS 单一登录之前，你的环境中必须已在运行以下设置：

* 必须部署 Active Directory 证书服务 (CA) 角色。 运行该角色的所有服务器必须已加入域、已安装最新的 Windows 更新，并已配置为[企业证书颁发机构](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731183%28v%3dws.10%29)。
* 必须部署“Active Directory 联合身份验证服务 (AD FS)”角色。 运行此角色的所有服务器都必须已加入域，已安装最新的 Windows 更新，并运行 Windows Server 2016 或更高版本。 若要开始设置此角色，请参阅我们的[联合身份验证教程](../active-directory/hybrid/tutorial-federation.md)。
* 建议设置 Web 应用程序代理角色，以保护环境与 AD FS 服务器的连接。 运行此角色的所有服务器必须已安装最新的 Windows 更新，并运行 Windows Server 2016 或更高版本。 请参阅此 [Web 应用程序代理指南](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383662(v=ws.11))开始设置此角色。
* 必须部署 Azure AD Connect 以将用户同步到 Azure AD。 必须在[联合模式](../active-directory/hybrid/how-to-connect-install-custom.md)下配置 Azure AD Connect。
* 在 AD FS 服务器上为 Azure 虚拟桌面[设置 PowerShell 环境](powershell-module.md)。
* 使用 Windows 10 20H1 或 20H2 连接到 Azure 虚拟桌面时，必须安装 Windows 10 的 2021-04 累积更新 (KB5001330) 或更高版本才能使单一登录正常运行。

> [!NOTE]
> Azure AD 域服务不支持此解决方案。 必须使用一个 Active Directory 域控制器。

## <a name="supported-clients"></a>支持的客户端

以下 Azure 虚拟桌面客户端支持此功能：

* [Windows 桌面客户端](./user-documentation/connect-windows-7-10.md)
* [Web 客户端](./user-documentation/connect-web.md)

## <a name="configure-the-certificate-authority-to-issue-certificates"></a>配置证书颁发机构以颁发证书

必须正确创建以下证书模板，使 AD FS 能够使用 SSO：

* 首先，需要创建“Exchange 注册代理(脱机请求)”证书模板。 AD FS 使用“Exchange 注册代理”证书模板代表用户请求证书。
* 此外，需要创建“智能卡登录”证书模板，AD FS 将使用该模板创建登录证书。

创建这些证书模板之后，需要对证书颁发机构启用这些模板，使 AD FS 可以请求它们。

> [!NOTE]
> 每次用户登录时，此解决方案都会生成新的短期证书，如果存在大量用户，则一段时间后，可能会填满证书颁发机构数据库。 [设置一个 CA 用于非持久证书处理](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ff934598(v=ws.10))可以避免此问题。

### <a name="create-the-enrollment-agent-certificate-template"></a>创建注册代理证书模板

视你的环境而定，你可能已出于其他目的配置了一个注册代理证书模板，例如 Windows Hello 企业版、登录证书或 VPN 证书。 如果是这样，需要将其修改为支持 SSO。 如果不是这样，则可以创建新模板。

若要确定是否已使用了注册代理证书模板，请在 AD FS 服务器上运行以下 PowerShell 命令，并查看是否返回了值。 如果输出为空，请创建新的注册代理证书模板。 否则，请记住现有注册代理证书模板的名称并更新该模板。

```powershell
Import-Module adfs
(Get-AdfsCertificateAuthority).EnrollmentAgentCertificateTemplateName
```

若要创建新的注册代理证书模板，请执行以下操作：

1. 在证书颁发机构中，通过“开始”菜单运行 mmc.exe 以启动“Microsoft 管理控制台” 。
2. 选择“文件...” > “添加/删除管理单元...” > “证书模板” > “添加 >” > “确定”以查看证书模板列表    。
3. 展开“证书模板”，右键单击“Exchange 注册代理(脱机请求)”并选择“复制模板”  。
4. 选择“常规”选项卡，然后在“模板显示名称”字段中输入“ADFS 注册代理” 。 这会自动将模板名称设置为“ADFSEnrollmentAgent”。
5. 选择“安全性”选项卡，然后选择“添加...” 。
6. 接下来，依次选择“对象类型...”、“服务帐户”、“确定”  。
7. 输入 AD FS 的服务帐户名称，然后选择“确定”。
   * 在独立的 AD FS 设置中，服务帐户将命名为“adfssvc$”
   * 如果使用 Azure AD Connect 设置了 AD FS，则服务帐户将命名为“aadcsvc$”
8. 在添加了服务帐户并且它在“安全性”选项卡中可见之后，请在“组或用户名”窗格中将它选中，在“AD FS 服务帐户的权限”窗格中对“注册”和“自动注册”都选择“允许”，然后选择“确定”以保存    。

   :::image type="content" source="media/adfs-enrollment-properties-security.png" alt-text="屏幕截图，其中显示了在经过正确配置后的“注册代理”证书模板的“安全性”选项卡。":::

若要更新现有的注册代理证书模板，请执行以下操作：

1. 在证书颁发机构中，通过“开始”菜单运行 mmc.exe 以启动“Microsoft 管理控制台” 。
2. 选择“文件...” > “添加/删除管理单元...” > “证书模板” > “添加 >” > “确定”以查看证书模板列表    。
3. 展开“证书模板”，双击与 AD FS 服务器上配置的模板对应的模板。 在“常规”选项卡上，模板名称应与上面找到的名称相匹配。
4. 选择“安全性”选项卡，然后选择“添加...” 。
5. 接下来，依次选择“对象类型...”、“服务帐户”、“确定”  。
6. 输入 AD FS 的服务帐户名称，然后选择“确定”。
   * 在独立的 AD FS 设置中，服务帐户将命名为“adfssvc$”
   * 如果使用 Azure AD Connect 设置了 AD FS，则服务帐户将命名为“aadcsvc$”
7. 在添加了服务帐户并且它在“安全性”选项卡中可见之后，请在“组或用户名”窗格中将它选中，在“AD FS 服务帐户的权限”窗格中对“注册”和“自动注册”都选择“允许”，然后选择“确定”以保存    。

### <a name="create-the-smartcard-logon-certificate-template"></a>创建“智能卡登录”证书模板

若要创建“智能卡登录”证书模板，请执行以下操作：

1. 在证书颁发机构中，通过“开始”菜单运行 mmc.exe 以启动“Microsoft 管理控制台” 。
2. 选择“文件...” > “添加/删除管理单元...” > “证书模板” > “添加” > “确定”以查看证书模板列表    。
3. 展开“证书模板”，右键单击“智能卡登录”并选择“复制模板”  。
4. 选择“常规”选项卡，然后在“模板显示名称”字段中输入“ADFS SSO” 。 这会自动将模板名称设置为“ADFSSSO”。
   > [!NOTE]
   > 由于此证书是按需请求的，因此我们建议将有效期缩短为 8 小时，将续订期缩短为 1 小时。

5. 选择“使用者名称”选项卡，然后选择“在请求中提供” 。 看到警告消息时，选择“确定”。

   :::image type="content" source="media/adfs-sso-properties-subject-inline.png" alt-text="显示 SSO 证书模板的“使用者名称”选项卡以及在正确配置后其外观的屏幕截图。" lightbox="media/adfs-sso-properties-subject-expanded.png":::

6. 选择“颁发要求”选项卡。
7. 选择“此数量的已授权签名”并输入值 1 。

   :::image type="content" source="media/adfs-sso-properties-issuance-inline.png" alt-text="显示 SSO 证书模板的“颁发要求”选项卡以及在经过正确配置后其外观的屏幕截图。" lightbox="media/adfs-sso-properties-issuance-expanded.png":::

8. 对于“应用程序策略”，请选择“证书请求代理” 。
9.  选择“安全性”选项卡，然后选择“添加...” 。
10. 依次选择“对象类型...”、“服务帐户”、“确定”  。
11. 像在[创建注册代理证书模板](#create-the-enrollment-agent-certificate-template)部分中那样，输入 AD FS 的服务帐户名称。
    * 在独立的 AD FS 设置中，服务帐户将命名为“adfssvc$”
    * 如果使用 Azure AD Connect 设置了 AD FS，则服务帐户将命名为“aadcsvc$”
12. 在添加了服务帐户并且它在“安全性”选项卡中可见之后，请在“组或用户名”窗格中将它选中，对“注册”和“自动注册”都选择“允许”，然后选择“确定”以保存   。

   :::image type="content" source="media/adfs-sso-properties-security.png" alt-text="屏幕截图，其中显示了在经过正确配置后的 SSO 证书模板的“安全性”选项卡。":::

### <a name="enable-the-new-certificate-templates"></a>启用新证书模板：

若要启用新证书模板，请执行以下操作：

1. 在证书颁发机构中，通过“开始”菜单运行 mmc.exe 以启动“Microsoft 管理控制台” 。
2. 选择“文件...” > “添加/删除管理单元...” > “证书颁发机构” > “添加 >” > “完成”>“确定”以查看“证书颁发机构”     。
3. 在左侧窗格中展开“证书颁发机构”并打开“证书模板”。
4. 右键单击显示了证书模板列表的中间窗格，选择“新建”，然后选择“可颁发的证书模板” 。
5. 选择“ADFS 注册代理”和“ADFS SSO”，然后选择“确定”  。 中间窗格中应会显示这两个模板。

   :::image type="content" source="media/adfs-certificate-templates.png" alt-text="显示可颁发的证书模板列表的屏幕截图，其中包括新的“ADFS 注册代理”和“ADFS SSO”。":::

   > [!NOTE]
   > 如果已配置注册代理证书模板，则只需添加 ADFS SSO 模板。

## <a name="configure-the-ad-fs-servers"></a>配置 AD FS 服务器

必须将 Active Directory 联合身份验证服务 (AD FS) 服务器配置为使用新证书模板，并将信赖方信任设置为支持 SSO。

AD FS 服务器与 Azure 虚拟桌面服务之间的信赖方信任使得单一登录证书请求可以正确转发到域环境。

配置 AD FS 单一登录时，必须选择共享密钥或证书：

* 如果只有一个 AD FS 服务器，则可以选择共享密钥或证书。
* 如果有多个 AD FS 服务器，则必须选择证书。

用于生成 Windows 登录令牌的共享密钥或证书必须安全存储在 [Azure 密钥保管库](../key-vault/general/overview.md)中。 可将机密存储在现有的密钥保管库中，或者部署新的密钥保管库。 无论采取哪种做法，都必须确保设置正确的访问策略，使 Azure 虚拟桌面服务能够访问该机密。

使用证书时，可以使用任何常规用途证书，并且在使用者名称或使用者可选名称 (SAN) 方面没有要求。 尽管不一定非要这样做，但建议创建由有效证书颁发机构颁发的证书。 此证书可以直接在 Azure 密钥保管库中创建，需要包含可导出的私钥。 可使用以下脚本导出公钥并使用它来配置 AD FS 服务器。 请注意，此证书不同于 AD FS SSL 证书，后者必须具有正确的使用者名称和有效的证书颁发机构。

[PowerShell 库](https://www.powershellgallery.com/packages/ConfigureWVDSSO)中提供的 PowerShell 脚本 ConfigureWVDSSO.ps1 将为信赖方信任配置 AD FS 服务器，并根据需要安装证书。

此脚本只有一个必需的参数 ADFSAuthority，此参数是解析为你的 AD FS 并使用“/adfs”作为后缀的 URL。 例如 `https://adfs.contoso.com/adfs`。

1. 在 AD FS VM 上，运行以下 PowerShell cmdlet 将 AD FS 配置为使用在上一部分创建的证书模板：
  
   ```powershell
   Set-AdfsCertificateAuthority -EnrollmentAgentCertificateTemplate "ADFSEnrollmentAgent" -LogonCertificateTemplate "ADFSSSO" -EnrollmentAgent
   ```
 
   > [!NOTE]
   > 如果已配置 EnrollmentAgentCertificateTemplate，请确保使用现有的模板名称而不是 ADFSEnrollmentAgent。

2. 运行 ConfigureWVDSSO.ps1 脚本。
   > [!NOTE]
   > 需要指定 `$config` 变量值才能完成说明的下一部分，因此请不要关闭完成前面的说明时所用的 PowerShell 窗口。 可以继续使用同一个 PowerShell 窗口，或者在启动新的 PowerShell 会话时将此窗口保持打开状态。
   
   * 如果在密钥保管库中使用共享密钥，请在 AD FS 服务器上运行以下 PowerShell cmdlet，将 ADFSServiceUrl 替换为完整 URL 以访问 AD FS 服务：

     ```powershell
     Install-Script ConfigureWVDSSO
     $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" [-WvdWebAppAppIDUri "<WVD Web App URI>"] [-RdWebURL "<RDWeb URL>"]
     ```

     > [!NOTE]
     > 需要 WvdWebAppAppIDUri 和 RdWebURL 属性来配置主权云（如 Azure 政府）中的环境。 在 Azure 商业云中，这些属性分别自动设置为 `https://www.wvd.microsoft.com` 和 `https://rdweb.wvd.microsoft.com`。

   * 如果你是在密钥保管库中使用证书，请在 AD FS 服务器上运行以下 PowerShell cmdlet，将 ADFSServiceUrl 替换为完整 URL 以访问 AD FS 服务：

     ```powershell
     Install-Script ConfigureWVDSSO
     $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" -UseCert -CertPath "<Path to the pfx file>" -CertPassword <Password to the pfx file> [-WvdWebAppAppIDUri "<WVD Web App URI>"] [-RdWebURL "<RDWeb URL>"]
     ```

     > [!NOTE]
     > 需要 WvdWebAppAppIDUri 和 RdWebURL 属性来配置主权云（如 Azure 政府）中的环境。 在 Azure 商业云中，这些属性分别自动设置为 `https://www.wvd.microsoft.com` 和 `https://rdweb.wvd.microsoft.com`。

3. 运行以下 PowerShell cmdlet 对 Azure 密钥保管库设置访问策略：

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName "<Key Vault Name>" -ServicePrincipalName 9cdead84-a844-4324-93f2-b2e6bb768d07 -PermissionsToSecrets get -PermissionsToKeys sign
   ```

4. 将共享密钥或证书存储在 Azure 密钥保管库中并为其设置一个标记，该标记包含有权使用该机密的订阅 ID 的逗号分隔列表。

   * 如果使用密钥保管库中的共享密钥，请运行以下 PowerShell cmdlet 来存储共享密钥并设置标记：

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Set-AzKeyVaultSecret -VaultName "<Key Vault Name>" -Name "adfsssosecret" -SecretValue (ConvertTo-SecureString -String $config.SSOClientSecret  -AsPlainText -Force) -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]}
     ```

   * 如果证书已在该密钥保管库中，请运行以下 PowerShell cmdlet 设置标记：

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>"
     $secret = Update-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "<Certificate Name>" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -PassThru
     ```

   * 如果你有本地证书，请运行以下 PowerShell cmdlet 在密钥保管库中导入该证书并设置标记：

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Import-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "adfsssosecret" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -FilePath "<Path to pfx>" -Password (ConvertTo-SecureString -String "<pfx password>"  -AsPlainText -Force)
     ```

> [!NOTE]
> 可以选择性地通过更改 [AD FS 单一登录设置](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#keep-me-signed-in-for-unauthenticated-devices)，配置提示用户输入凭据的频率。 默认情况下，在未注册的设备上，将每隔 8 小时提示用户一次。

## <a name="configure-your-azure-virtual-desktop-host-pool"></a>配置 Azure 虚拟桌面主机池

现在需要在 Azure 虚拟桌面主机池中配置 AD FS SSO 参数。 为此，请为 Azure 虚拟桌面[设置 PowerShell 环境](powershell-module.md)（如果尚未这样做）并连接到你的帐户。

然后，通过在 AD FS VM 上的同一个 PowerShell 窗口中运行以下两个 cmdlet 之一来更新主机池的 SSO 信息：

* 如果使用密钥保管库中的共享密钥，请运行以下 PowerShell cmdlet：

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType SharedKeyInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > 需要设置 SsoClientId 属性，使之与部署 SSO 的 Azure 云相匹配。 在 Azure 商业云中，此属性应设置为 `https://www.wvd.microsoft.com`。 但是，此属性所需的设置对于其他云（如 Azure 政府云）会有所不同。

* 如果你是在密钥保管库中使用证书，请运行以下 PowerShell cmdlet：

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType CertificateInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > 需要设置 SsoClientId 属性，使之与部署 SSO 的 Azure 云相匹配。 在 Azure 商业云中，此属性应设置为 `https://www.wvd.microsoft.com`。 但是，此属性所需的设置对于其他云（如 Azure 政府云）会有所不同。

### <a name="configure-additional-host-pools"></a>配置其他主机池

需要配置其他主机池时，可以检索用于配置现有主机池的设置以设置新主机池。

若要从现有主机池中检索设置，请打开 PowerShell 窗口并运行以下 cmdlet：

```powershell
Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" | fl *
```

可以遵循[配置 Azure 虚拟桌面主机池](#configure-your-azure-virtual-desktop-host-pool)的步骤并使用相同的 SsoClientId、SsoClientSecretKeyVaultPath、SsoSecretType 和 SsoadfsAuthority 值进行操作   。

## <a name="removing-sso"></a>删除 SSO

若要在主机池中禁用 SSO，请运行以下 cmdlet：

```powershell
Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority ''
```

如果你还想要在 AD FS 服务器中禁用 SSO，请运行以下 cmdlet：

```powershell
Install-Script UnConfigureWVDSSO
UnConfigureWVDSSO.ps1 -WvdWebAppAppIDUri "<WVD Web App URI>" -WvdClientAppApplicationID "a85cf173-4192-42f8-81fa-777a763e6e2c"
```

> [!NOTE]
> WvdWebAppAppIDUri 属性需要与部署到的 Azure 云相匹配。 在 Azure 商业云中，此属性为 `https://www.wvd.microsoft.com`。 对于其他云（如 Azure 政府云），此属性会有所不同。

## <a name="next-steps"></a>后续步骤

配置单一登录后，可以登录到支持的 Azure 虚拟桌面客户端，在运行用户会话的过程中测试单一登录。 若要了解如何使用新凭据连接到会话，请查看以下文章：

* [使用 Windows 桌面客户端进行连接](./user-documentation/connect-windows-7-10.md)
* [使用 Web 客户端进行连接](./user-documentation/connect-web.md)