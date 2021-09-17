---
title: 为 Azure VM 设置 WinRM 访问权限
description: 设置 WinRM 访问，与 Resource Manager 部署模型中创建的 Azure 虚拟机一起使用。
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2016
ms.author: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32241529dc97f97fe3dfce2f1b7cb015dc952475
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688474"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>为 Azure Resource Manager 中的虚拟机设置 WinRM 访问权限
适用于：:heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 


为 VM 设置 WinRM 连接需执行以下步骤

1. 创建密钥保管库
2. 创建自签名证书
3. 将自签名证书上传到密钥保管库
4. 获取密钥保管库中自签名证书的 URL
5. 创建 VM 时引用自签名证书 URL



## <a name="step-1-create-a-key-vault"></a>步骤 1：创建密钥保管库
可使用以下命令来创建密钥保管库

```azurepowershell
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>步骤 2：创建自签名证书
可使用此 PowerShell 脚本创建自签名证书

```azurepowershell
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>步骤 3：将自签名证书上传到密钥保管库
将证书上传到在步骤 1 中创建的密钥保管库之前，需将其转换为 Microsoft.Compute 资源提供程序可识别的格式。 以下 PowerShell 脚本将允许执行该操作

```azurepowershell
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

[System.Collections.HashTable]$TableForJSON = @{
    "data"     = $fileContentEncoded;
    "dataType" = "pfx";
    "password" = "<password>";
}
[System.String]$jsonObject = $TableForJSON | ConvertTo-Json
$jsonEncoded = [System.Convert]::ToBase64String($jsonObject)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>步骤 4：获取密钥保管库中自签名证书的 URL
预配 VM 时，Microsoft.Compute 资源提供程序需要指向密钥保管库中密钥的 URL。 这会使 Microsoft.Compute 资源提供程序能够下载密钥，并在 VM 上创建等效证书。

> [!NOTE]
> 密钥 URL 还需要包含版本。 示例 URL 如下所示：https:\//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>模板
可使用以下代码获取模板中 URL 的链接

```json
"certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"
```

#### <a name="powershell"></a>PowerShell
可使用以下 PowerShell 命令获取此 URL

```azurepowershell
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
```

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>步骤 5：创建 VM 时引用自签名证书 URL
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager 模板
通过模板创建 VM 时，在密钥部分和 winRM 部分中引用该证书，如下所示：

```json
"osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "<resource id of the Key Vault containing the secret>"
          },
          "vaultCertificates": [
            {
              "certificateUrl": "<URL for the certificate you got in Step 4>",
              "certificateStore": "<Name of the certificate store on the VM>"
            }
          ]
        }
      ],
      "windowsConfiguration": {
        ...
        "winRM": {
          "listeners": [
            {
              "protocol": "http"
            },
            {
              "protocol": "https",
              "certificateUrl": "<URL for the certificate you got in Step 4>"
            }
          ]
        },
        ...
      }
    },
```

针对上述内容的示例模板可在此处 ([vm-winrm-keyvault-windows](https://azure.microsoft.com/resources/templates/vm-winrm-keyvault-windows/)) 找到

此模板的源代码可在 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows) 上找到

#### <a name="powershell"></a>PowerShell
```azurepowershell
$vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
$credential = Get-Credential
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
$sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
$CertificateStore = "My"
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL
```

## <a name="step-6-connecting-to-the-vm"></a>步骤 6：连接到 VM
需要先确保用户的计算机针对 WinRM 远程管理进行了配置，才能连接到 VM。 以管理员身份启动 PowerShell 并执行以下命令以确保已完成设置。

```azurepowershell
Enable-PSRemoting -Force
```

> [!NOTE]
> 如果以上命令无效，可能需要确保 WinRM 服务正在运行。 可使用 `Get-Service WinRM`
>
>

设置完成后，即可使用以下命令连接到 VM

```azurepowershell
Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
```
