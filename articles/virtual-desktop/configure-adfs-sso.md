---
title: 配置 Azure 虚拟桌面 AD FS 单一登录 - Azure
description: 如何为 Azure 虚拟桌面环境配置 AD FS 单一登录。
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/28/2021
ms.author: helohr
ms.openlocfilehash: c85186d8338918dbcf2af56abd959f5cbff6ad56
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967285"
---
# <a name="configure-ad-fs-single-sign-on-for-azure-virtual-desktop"></a>为 Azure 虚拟桌面配置 AD FS 单一登录

> [!IMPORTANT]
> AD FS 单一登录目前为公共预览版。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文将引导你完成配置 Azure 虚拟桌面的 Active Directory 联合身份验证服务 (AD FS) 单一登录 (SSO) 的过程。

> [!NOTE]
> Azure 虚拟桌面（经典）不支持此功能。

## <a name="requirements"></a>要求

> [!IMPORTANT]
> 在公共预览版期间，必须将主机池配置到[验证环境](create-validation-host-pool.md)中。

在配置 AD FS 单一登录之前，必须在环境中运行以下安装程序：

* 必须部署 Active Directory 证书服务 (CA) 角色。 所有运行该角色的服务器都必须已加入域，已安装最新的 Windows 更新，并已被配置为[企业证书颁发机构](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731183%28v%3dws.10%29)。
* 必须部署 Active Directory 联合身份验证服务 (AD FS) 角色。 运行此角色的所有服务器都必须已加入域，已安装最新的 Windows 更新，并运行 Windows Server 2016 或更高版本。 若要开始设置此角色，请参阅我们的[联合身份验证教程](../active-directory/hybrid/tutorial-federation.md)。
* 建议设置 Web 应用程序代理角色，以保护环境与 AD FS 服务器的连接。 运行此角色的所有服务器都必须已安装最新的 Windows 更新，并运行 Windows Server 2016 或更高版本。 若要开始设置此角色，请参阅此 [Web 应用程序代理指南](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383662(v=ws.11))。
* 必须部署 Azure AD Connect 才能将用户同步到 Azure AD。 必须在[联合身份验证模式](../active-directory/connect/active-directory-aadconnect-get-started-custom.md)下配置 Azure AD Connect。
* 在 AD FS 服务器上为 Azure 虚拟桌面[设置 PowerShell 环境](powershell-module.md)。
* 使用 Windows 10 20H1 或 20H2 连接到 Azure 虚拟桌面时，必须安装适用于 Windows 10 的 2021-04 累积更新 (KB5001330) 或更高版本，单一登录才能正常运行。

> [!NOTE]
> Azure AD 域服务不支持此解决方案。 必须使用 Active Directory 域控制器。

## <a name="supported-clients"></a>支持的客户端

以下 Azure 虚拟桌面客户端支持此功能：

* [Windows 桌面客户端](connect-windows-7-10.md)
* [Web 客户端](connect-web.md)

## <a name="configure-the-certificate-authority-to-issue-certificates"></a>配置证书颁发机构以颁发证书

必须正确创建以下证书模板，使 AD FS 可以使用 SSO：

* 首先，需要创建“Exchange 注册代理(脱机请求)”证书模板。 AD FS 将使用 Exchange 注册代理证书模板代表用户请求证书。
* 还需要创建智能卡登录证书模板，AD FS 将使用该模板创建登录证书。

创建完这些证书模板之后，你需要在证书颁发机构上启用模板，使 AD FS 可以请求它们。

> [!NOTE]
> 此解决方案为每个用户登录生成新的短期证书，如果你有很多用户，它可能会在一段时间内填写证书颁发机构数据库。 你可以通过[为非持久性证书处理设置 CA](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ff934598(v=ws.10)) 来避免这种情况。

### <a name="create-the-enrollment-agent-certificate-template"></a>创建注册代理证书模板

根据你的环境，你可能已由于其他目的（如 Windows Hello 企业版、登录证书或 VPN 证书）配置了注册代理证书模板。 如果是这样，则需要将其修改为支持 SSO。 如果不是，则可以创建新模板。

若要确定你是否已在使用注册代理证书模板，请在 AD FS 服务器上运行以下 PowerShell 命令，并查看是否返回值。 如果为空，则创建新的注册代理证书模板。 否则，请记住名称并更新现有的注册代理证书模板。

```powershell
Import-Module adfs
(Get-AdfsCertificateAuthority).EnrollmentAgentCertificateTemplateName
```

创建新的注册代理证书模板的步骤：

1. 在证书颁发机构上，从“开始”菜单运行 mmc.exe 以启动 Microsoft 管理控制台。
2. 选择“文件...” > “添加/删除管理单元...” > “证书模板” > “添加 >” > “确定”查看证书模板列表。
3. 展开“证书模板”，右键单击“Exchange 注册代理(脱机请求)”，然后选择“复制模板”。
4. 选择“常规”选项卡，然后在“模板显示名称”字段中输入“ADFS 注册代理”。 这会自动将模板名称设置为“ADFSEnrollmentAgent”。
5. 选择“安全”选项卡，然后选择“添加...”。
6. 接下来，依次选择“对象类型...”、“服务帐户”和“确定”。
7. 输入 AD FS 的服务帐户名称，然后选择“确定”。
   * 在独立的 AD FS 设置中，服务帐户将被命名为“adfssvc$”
   * 如果你是使用 Azure AD Connect 设置 AD FS，服务帐户将被命名为“aadcsvc$”
8. 添加完服务帐户并且帐户在“安全”选项卡中可见后，在“组或用户名”窗格中选择它，在“AD FS 服务帐户的权限”窗格中为“注册”和“自动注册”选择“允许”，然后选择“确定”进行保存。

   :::image type="content" source="media/adfs-enrollment-properties-security.png" alt-text="显示正确配置后的注册代理证书模板的“安全”选项卡的屏幕截图。":::

更新现有注册代理证书模板的步骤：

1. 在证书颁发机构上，从“开始”菜单运行 mmc.exe 以启动 Microsoft 管理控制台。
2. 选择“文件...” > “添加/删除管理单元...” > “证书模板” > “添加 >” > “确定”查看证书模板列表。
3. 展开“证书模板”，双击与 AD FS 服务器上配置的模板对应的模板。 在“常规”选项卡上，模板名称应与上面找到的名称一致。
4. 选择“安全”选项卡，然后选择“添加...”。
5. 接下来，依次选择“对象类型...”、“服务帐户”和“确定”。
6. 输入 AD FS 的服务帐户名称，然后选择“确定”。
   * 在独立的 AD FS 设置中，服务帐户将被命名为“adfssvc$”
   * 如果你是使用 Azure AD Connect 设置 AD FS，服务帐户将被命名为“aadcsvc$”
7. 添加完服务帐户并且帐户在“安全”选项卡中可见后，在“组或用户名”窗格中选择它，在“AD FS 服务帐户的权限”窗格中为“注册”和“自动注册”选择“允许”，然后选择“确定”进行保存。

### <a name="create-the-smartcard-logon-certificate-template"></a>创建智能卡登录证书模板

创建智能卡登录证书模板的步骤：

1. 在证书颁发机构上，从“开始”菜单运行 mmc.exe 以启动 Microsoft 管理控制台。
2. 选择“文件...” > “添加/删除管理单元...” > “证书模板” > “添加” > “确定”查看证书模板列表。
3. 展开“证书模板”，右键单击“智能卡登录”，并选择“复制模板”。
4. 选择“常规”选项卡，然后在“模板显示名称”字段中输入“ADFS SSO”。 这会自动将模板名称设置为“ADFSSSO”。
   > [!NOTE]
   > 由于此证书是按需申请的，我们建议将有效期缩短为 8 小时，续订期缩短为 1 小时。

5. 选择“使用者名称”选项卡，然后选择“在请求中提供”。 看到警告消息时，请选择“确定”。

   :::image type="content" source="media/adfs-sso-properties-subject-inline.png" alt-text="显示 SSO 证书模板的“使用者名称”选项卡以及正确配置后的外观的屏幕截图。" lightbox="media/adfs-sso-properties-subject-expanded.png":::

6. 选择“颁发要求”选项卡。
7. 选择“授权签名的数量”，并输入值 1。

   :::image type="content" source="media/adfs-sso-properties-issuance-inline.png" alt-text="显示 SSO 证书模板的“颁发要求”选项卡以及正确配置后的外观的屏幕截图。" lightbox="media/adfs-sso-properties-issuance-expanded.png":::

8. 对于“应用程序策略”，选择“证书请求代理”。
9.  选择“安全”选项卡，然后选择“添加...”。
10. 依次选择“对象类型...”、“服务帐户”和“确定”。
11. 输入 AD FS 的服务帐户名称，就像在[创建注册代理证书模板](#create-the-enrollment-agent-certificate-template)部分中所做的那样。
    * 在独立的 AD FS 设置中，服务帐户将被命名为“adfssvc$”
    * 如果你是使用 Azure AD Connect 设置 AD FS，服务帐户将被命名为“aadcsvc$”
12. 添加完服务帐户并且帐户在“安全”选项卡中可见后，在“组或用户名”窗格中选择它，为“注册”和“自动注册”选择“允许”，然后选择“确定”进行保存。

   :::image type="content" source="media/adfs-sso-properties-security.png" alt-text="显示正确配置后的 SSO 证书模板的“安全”选项卡的屏幕截图。":::

### <a name="enable-the-new-certificate-templates"></a>启用新的证书模板：

启用新的证书模板的步骤：

1. 在证书颁发机构上，从“开始”菜单运行 mmc.exe 以启动 Microsoft 管理控制台。
2. 选择“文件...” > “添加/删除管理单元...” > “证书颁发机构” > “添加 >” > “完成”>“确定”以查看证书颁发机构。
3. 展开左侧窗格中的“证书颁发机构”并打开“证书模板”。
4. 在显示证书模板列表的中间窗格中右键单击，选择“新建”，然后选择“要颁发的证书模板”。
5. 选择“ADFS 注册代理”和“ADFS SSO”，然后选择“确定”。 中间窗格中应会显示两个模板。

   :::image type="content" source="media/adfs-certificate-templates.png" alt-text="显示可以颁发的证书模板列表（包括新的 ADFS 注册代理和 ADFS SSO）的屏幕截图。":::

   > [!NOTE]
   > 如果你已配置注册代理证书模板，则只需添加 ADFS SSO 模板。

## <a name="configure-the-ad-fs-servers"></a>配置 AD FS 服务器

必须将 Active Directory 联合身份验证服务 (AD FS) 服务器配置为使用新证书模板并设置信赖方信任以支持 SSO。

有了 AD FS 服务器和 Azure 虚拟桌面服务之间的信赖方信任，就可以将单一登录证书请求正确转发到域环境。

配置单 AD FS 单一登录时，必须选择共享密钥或证书：

* 如果你有单个 AD FS 服务器，则可以选择共享密钥或证书。
* 如果你有多个 AD FS 服务器，则需要选择证书。

用于生成登录 Windows 时所用的令牌的共享密钥或证书必须安全地存储在 [Azure 密钥保管库](../key-vault/general/overview.md)中。 你可以将机密存储在现有密钥保管库中，也可以部署一个新的密钥保管库。 在任一情况下，都必须确保设置正确的访问策略，使 Azure 虚拟桌面服务可以访问它。

使用证书时，可以使用任何常规用途证书，并且对使用者名称或使用者备用名称 (SAN) 没有要求。 虽然不是必需的，但建议创建一个由有效的证书颁发机构颁发的证书。 此证书可以直接在 Azure 密钥保管库中创建，并且需要具有可导出的私钥。 可以使用以下脚本导出公钥并将它用于配置 AD FS 服务器。 请注意，此证书与 AD FS SSL 证书不同，后者必须具有正确的使用者名称和有效的证书颁发机构。

[PowerShell 库](https://www.powershellgallery.com/packages/ConfigureWVDSSO)中提供的 PowerShell 脚本 ConfigureWVDSSO.ps1 将为信赖方信任配置 AD FS 服务器，并在需要时安装证书。

此脚本只有一个必需参数 ADFSAuthority，它是解析到 AD FS 并使用“/adfs”作为其后缀的 URL。 例如 `https://adfs.contoso.com/adfs`。

1. 在 AD FS VM 上，运行以下 PowerShell cmdlet，将 AD FS 配置为使用上一节中的证书模板：
  
   ```powershell
   Set-AdfsCertificateAuthority -EnrollmentAgentCertificateTemplate "ADFSEnrollmentAgent" -LogonCertificateTemplate "ADFSSSO" -EnrollmentAgent
   ```
 
   > [!NOTE]
   > 如果你已配置 EnrollmentAgentCertificateTemplate，请确保使用现有模板名称而不是 ADFSEnrollmentAgent。

2. 运行 ConfigureWVDSSO.ps1 脚本。
   > [!NOTE]
   > 需要 `$config` 变量值来完成下一部分说明，因此不要关闭用来完成前面说明的 PowerShell 窗口。 你可以继续使用同一 PowerShell 窗口，也可以在启动新的 PowerShell 会话时将其保持打开状态。
   
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

3. 通过运行以下 PowerShell cmdlet 在 Azure 密钥保管库上设置访问策略：

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName "<Key Vault Name>" -ServicePrincipalName 9cdead84-a844-4324-93f2-b2e6bb768d07 -PermissionsToSecrets get -PermissionsToKeys sign
   ```

4. 将共享密钥或证书存储在 Azure 密钥保管库中，标记包含允许使用该机密的订阅 ID 的逗号分隔列表。

   * 如果你是在密钥保管库中使用共享密钥，请运行以下 PowerShell cmdlet 来存储共享密钥并设置标记：

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Set-AzKeyVaultSecret -VaultName "<Key Vault Name>" -Name "adfsssosecret" -SecretValue (ConvertTo-SecureString -String $config.SSOClientSecret  -AsPlainText -Force) -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]}
     ```

   * 如果你的证书已在密钥保管库中，请运行以下 PowerShell cmdlet 来设置标记：

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>"
     $secret = Update-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "<Certificate Name>" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -PassThru
     ```

   * 如果你有本地证书，请运行以下 PowerShell cmdlet 以在密钥保管库中导入证书并设置标记：

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Import-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "adfsssosecret" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -FilePath "<Path to pfx>" -Password (ConvertTo-SecureString -String "<pfx password>"  -AsPlainText -Force)
     ```

> [!NOTE]
> 可以选择通过更改 [AD FS 单一登录设置](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#keep-me-signed-in-for-unauthenticated-devices)来配置提示用户输入凭据的频率。 默认情况下，在未注册的设备上，用户将每 8 小时收到一次提示。

## <a name="configure-your-azure-virtual-desktop-host-pool"></a>配置 Azure 虚拟桌面主机池

> [!IMPORTANT]
> 在公共预览版期间，必须将主机池配置到[验证环境](create-validation-host-pool.md)中。

现在可以在 Azure 虚拟桌面主机池上配置 AD FS SSO 参数了。 为此，请为 Azure 虚拟桌面[设置 PowerShell 环境](powershell-module.md)（如果还没有），并连接到你的帐户。

然后，通过在 AD FS VM 上的同一 PowerShell 窗口中运行以下两个 cmdlet 之一，为主机池更新 SSO 信息：

* 如果你是在密钥保管库中使用共享密钥，请运行以下 PowerShell cmdlet：

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType SharedKeyInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > 需要设置 SsoClientId 属性，以匹配要在其中部署 SSO 的 Azure 云。 在 Azure 商业云中，此属性应设置为 `https://www.wvd.microsoft.com`。 但是，此属性所需的设置对于其他云（如 Azure 政府云）会有所不同。

* 如果你是在密钥保管库中使用证书，请运行以下 PowerShell cmdlet：

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType CertificateInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > 需要设置 SsoClientId 属性，以匹配要在其中部署 SSO 的 Azure 云。 在 Azure 商业云中，此属性应设置为 `https://www.wvd.microsoft.com`。 但是，此属性所需的设置对于其他云（如 Azure 政府云）会有所不同。

### <a name="configure-additional-host-pools"></a>配置其他主机池

当你需要配置其他主机池时，可以检索用于配置现有主机池的设置，以设置新主机池。

若要从现有主机池中检索设置，请打开 PowerShell 窗口并运行以下 cmdlet：

```powershell
Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" | fl *
```

可以按照以下步骤，使用相同的 SsoClientId、SsoClientSecretKeyVaultPath、SsoSecretType 和 SsoadfsAuthority 值[配置 Azure 虚拟桌面主机池](#configure-your-azure-virtual-desktop-host-pool)。

## <a name="removing-sso"></a>删除 SSO

若要在主机池上禁用 SSO，请运行以下 cmdlet：

```powershell
Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority ''
```

此外，如果还需要 AD FS 服务器上禁用 SSO，请运行以下 cmdlet：

```powershell
Install-Script UnConfigureWVDSSO
UnConfigureWVDSSO.ps1 -WvdWebAppAppIDUri "<WVD Web App URI>" -WvdClientAppApplicationID "a85cf173-4192-42f8-81fa-777a763e6e2c"
```

> [!NOTE]
> WvdWebAppAppIDUri 属性需要与你要部署的 Azure 云匹配。 在 Azure 商业云中，此属性为 `https://www.wvd.microsoft.com`。 对于其他云（如 Azure 政府云），此属性会有所不同。

## <a name="next-steps"></a>后续步骤

现在你已经配置了单一登录，可以登录到支持的 Azure 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 如需了解如何使用新凭据连接到会话，请参阅以下文章：

* [使用 Windows 桌面客户端进行连接](connect-windows-7-10.md)
* [使用 Web 客户端进行连接](connect-web.md)