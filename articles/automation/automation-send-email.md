---
title: 从 Azure 自动化 runbook 发送电子邮件
description: 本文介绍如何从 runbook 中发送电子邮件。
services: automation
ms.subservice: process-automation
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 622bff79d48ae707e2b32556e05dad658a0322bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730546"
---
# <a name="send-an-email-from-a-runbook"></a>从 Runbook 发送电子邮件

可以使用 PowerShell 通过 [SendGrid](https://sendgrid.com/solutions) 从 runbook 发送电子邮件。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有 Azure 订阅，可以[激活 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [一个 SendGrid 帐户](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account)。
* 已在发送网格中配置了发送方验证。 [域或单个发送方](https://sendgrid.com/docs/for-developers/sending-email/sender-identity/) 
* 具有 Az 模块的[自动化帐户](./index.yml)。
* 用于存储和执行 runbook 的[运行方式帐户](./automation-security-overview.md#run-as-accounts)。

## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

可以使用以下 PowerShell 脚本创建 Azure Key Vault。 将变量值替换为特定于环境的值。 单击代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。 也可复制代码并在本地运行它，前提是已在本地计算机上安装 [Az 模块](/powershell/azure/install-az-ps)。 此脚本还会创建一个 [Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)，该策略允许运行方式帐户获取和设置指定密钥保管库中的密钥保管库机密。

> [!NOTE]
> 若要检索 API 密钥，请使用[查找 SendGrid API 密钥](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key)中的步骤。

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

若要通过其他方式创建 Azure 密钥保管库并存储机密，请参阅[密钥保管库快速入门](../key-vault/index.yml)。

## <a name="import-required-modules-into-your-automation-account"></a>将所需模块导入自动化帐户

若要在 runbook 中使用 Azure 密钥保管库，必须将以下模块导入到自动化帐户中：

* [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

有关说明，请参阅[导入 Az 模块](shared-resources/modules.md#import-az-modules)。

## <a name="create-the-runbook-to-send-an-email"></a>创建用于发送电子邮件的 runbook

在创建密钥保管库并存储 `SendGrid` API 密钥后，即可创建 runbook 来检索 API 密钥和发送电子邮件。 让我们使用 runbook，该 runbook 使用 `AzureRunAsConnection` 以[运行方式帐户](./automation-security-overview.md#run-as-accounts)身份向 Azure 进行身份验证，以便从 Azure 密钥保管库检索机密。 我们将该 runbook 命名为 Send-GridMailMessage。 你可以修改用作示例的 PowerShell 脚本，并将其重用于不同的方案。

1. 转到 Azure 自动化帐户。
2. 在“过程自动化”下，选择“Runbook”。 
3. 在 Runbook 列表的顶部选择“+ 创建 Runbook”。
4. 在“添加 Runbook”页上，输入 Send-GridMailMessage 作为 runbook 名称。 对于 runbook 类型，选择“PowerShell”。 然后选择“创建”。
   ![创建 Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. 此时会创建 Runbook 并打开“编辑 PowerShell Runbook”页。
   ![编辑 Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. 将以下 PowerShell 示例复制到“编辑”页中。 确保 `VaultName` 指定你为密钥保管库选择的名称。

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey" -AsPlainText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. 选择“发布”以保存并发布 Runbook。

若要验证 runbook 是否成功执行，可以按[测试 runbook](manage-runbooks.md#test-a-runbook) 或[启动 runbook](start-runbooks.md) 下的步骤操作。

如果一开始看不到测试电子邮件，请检查 Junk 和 Spam 文件夹。

## <a name="clean-up-resources-after-the-email-operation"></a>在电子邮件操作后清理资源

1. 如果不再需要该 runbook，请在 runbook 列表中选中它，然后单击“删除”。

2. 使用 [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault) cmdlet 删除密钥保管库。

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

* 若要将 runbook 作业数据发送到 Log Analytics 工作区，请参阅[将 Azure 自动化作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。
* 若要监视基本级别的指标和日志，请参阅[使用警报触发 Azure 自动化 runbook](automation-create-alert-triggered-runbook.md)。
* 若要纠正 runbook 操作期间出现的问题，请参阅[排查 runbook 问题](./troubleshoot/runbooks.md)。