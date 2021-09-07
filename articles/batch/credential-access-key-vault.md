---
title: 使用证书并通过 Batch 安全地访问 Azure Key Vault
description: 了解如何使用 Azure Batch 以编程方式从 Key Vault 访问凭据。
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 20af753813ead55a3107b952a56d92b16135b523
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098959"
---
# <a name="use-certificates-and-securely-access-azure-key-vault-with-batch"></a>使用证书并通过 Batch 安全地访问 Azure Key Vault

本文介绍如何设置 Batch 节点，以便安全地访问 [Azure Key Vault](../key-vault/general/overview.md) 中存储的凭据。

若要从 Batch 节点向 Azure Key Vault 进行身份验证，需要：

- 一个 Azure Active Directory (Azure AD) 凭据
- 一个证书
- 一个 Batch 帐户
- 具有至少一个节点的 Batch 池

> [!IMPORTANT]
> Batch 现在提供了一个改进的选项，用于访问 Azure Key Vault 中存储的凭据。 通过使用一个可访问 Azure Key Vault 中证书的用户分配的托管标识来创建池，你无需将证书内容发送到 Batch 服务，从而增强了安全性。 我们建议使用自动证书轮换，而不使用本主题中介绍的方法。 有关详细信息，请参阅[在 Batch 池中启用自动证书轮换](automatic-certificate-rotation.md)。

## <a name="obtain-a-certificate"></a>获得证书

如果还没有证书，获取证书最简单的方法是使用 `makecert` 命令行工具生成自签名证书。

通常可以在以下路径中找到 `makecert`：`C:\Program Files (x86)\Windows Kits\10\bin\<arch>`。 以管理员身份打开命令提示符，并使用以下示例导航到 `makecert`。

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

接下来，使用 `makecert` 工具创建名为 `batchcertificate.cer` 和 `batchcertificate.pvk` 的自签名证书文件。 使用的公用名称 (CN) 对此应用程序并不重要，但将其设置为告诉你证书的用途是非常有帮助的。

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch 需要 `.pfx` 文件。 使用 [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) 工具将 `makecert` 创建的 `.cer` 和 `.pvk` 文件转换为单个 `.pfx` 文件。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>创建服务主体

将对 Key Vault 的访问权限授予用户或服务主体。  若要以编程方式访问 Key Vault，请将[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)与上一步中创建的证书一起使用。 服务主体必须与 Key Vault 位于同一 Azure AD 租户中。

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

应用程序的 URL 并不重要，因为我们只是将其用于访问 Key Vault。

## <a name="grant-rights-to-key-vault"></a>授予 Key Vault 的权限

在上一步中创建的服务主体需要权限才能从 Key Vault 检索机密。 可以通过 [Azure 门户](../key-vault/general/assign-access-policy-portal.md)或以下 PowerShell 命令授予权限。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>将证书分配给 Batch 帐户

创建 Batch 池，然后转到该池中的“证书”选项卡，并分配所创建的证书。 证书现在位于所有 Batch 节点上。

接下来，将该证书分配到 Batch 帐户。 将证书分配到帐户之后，Batch 就可以将其分配到池，然后分配到节点。 执行此操作最简单的方法是，转到门户中的 Batch 帐户，导航到“证书”，然后选择“添加”。  上传先前生成的 `.pfx` 文件，并提供密码。 完成后，相应的证书将添加到列表，你可以验证指纹。

现在，在创建 Batch 池时，可以导航到池中的“证书”，并将创建的证书分配到该池。 执行此操作时，请确保选择存储位置 LocalMachine。 证书将加载到池中的所有 Batch 节点上。

## <a name="install-azure-powershell"></a>安装 Azure PowerShell

如果计划在节点上使用 PowerShell 脚本访问 Key Vault，则需要安装 Azure PowerShell 库。 如果节点安装了 Windows Management Framework (WMF) 5，则可使用 install-module 命令下载该库。 如果使用的节点未安装 WMF 5，最简单的安装方法是将 Azure PowerShell `.msi` 文件与 Batch 文件捆绑在一起，然后在 Batch 启动脚本的第一部分中调用该安装程序。 有关详细信息，请参阅此示例：

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>访问密钥保管库

现在可以通过在 Batch 节点上运行的脚本访问 Key Vault 了。 若要从脚本访问 Key Vault，你只需使用证书对脚本针对 Azure AD 进行身份验证。 若要在 PowerShell 中执行此操作，请使用以下示例命令。 为“指纹”、“应用 ID”（服务主体的 ID）和“租户 ID”（服务主体所在的租户）指定相应的 GUID。  

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

经过身份验证后，可以像往常一样访问 KeyVault。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

这些是要在脚本中使用的凭据。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Key Vault](../key-vault/general/overview.md)。
- 查看[适用于 Batch 的 Azure 安全基线](security-baseline.md)。
- 了解 Batch 功能，例如[配置对计算节点的访问](pool-endpoint-configuration.md)、[使用 Linux 计算节点](batch-linux-nodes.md)和[使用专用终结点](private-connectivity.md)。
