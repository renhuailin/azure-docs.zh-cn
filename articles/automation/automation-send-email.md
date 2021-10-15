---
title: 从 Azure 自动化 runbook 发送电子邮件
description: 本文介绍如何从 runbook 中发送电子邮件。
services: automation
ms.subservice: process-automation
ms.date: 09/21/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3f781b32c000ec42c876fa61a90d9ef70c3eb01c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354560"
---
# <a name="send-an-email-from-am-automation-runbook"></a>从自动化 Runbook 发送电子邮件

可以使用 PowerShell 通过 [SendGrid](https://sendgrid.com/solutions) 从 runbook 发送电子邮件。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* [一个 SendGrid 帐户](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account)。
* SendGrid 发送方验证。 [域或单个发送方](https://sendgrid.com/docs/for-developers/sending-email/sender-identity/) 
* 你的 [SendGrid API 密钥](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key)。

* 至少具有一个用户分配的托管标识的 Azure 自动化帐户。 有关详细信息，请参阅[启用托管标识](./quickstarts/enable-managed-identity.md)。
* 导入到自动化帐户中的 Az 模块：`Az.Accounts` 和 `Az.KeyVault`。 有关详细信息，请参阅[导入 Az 模块](./shared-resources/modules.md#import-az-modules)。
* 在计算机上安装 [Azure Az PowerShell 模块](/powershell/azure/new-azureps-module-az)。 若要安装或升级，请参阅[如何安装 Azure Az PowerShell 模块](/powershell/azure/install-az-ps)。

## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

创建一个 Azure 密钥保管库和[密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md)，该策略允许凭据获取和设置指定密钥保管库中的密钥保管库机密。

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 为以下变量提供适当的值，然后执行脚本。

    ```powershell
    $resourceGroup = "<Resource group>"
    $automationAccount = "<Automation account>"
    $region = "<Region>"
    $SendGridAPIKey = "<SendGrid API key>"
    $VaultName = "<A universally unique vault name>"

    $userAssignedManagedIdentity = "<User-assigned managed identity>"
    ```

1. 创建密钥保管库并分配权限

    ```powershell
    # Create the new key vault
    $newKeyVault = New-AzKeyVault `
        -VaultName $VaultName `
        -ResourceGroupName $resourceGroup `
        -Location $region

    $resourceId = $newKeyVault.ResourceId
    
    # Convert the SendGrid API key into a SecureString
    $Secret = ConvertTo-SecureString -String $SendGridAPIKey `
        -AsPlainText -Force

    Set-AzKeyVaultSecret -VaultName $VaultName `
        -Name 'SendGridAPIKey' `
        -SecretValue $Secret
    
    # Grant Key Vault access to the Automation account's system-assigned managed identity.
    $SA_PrincipalId = (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.PrincipalId

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $SA_PrincipalId `
        -PermissionsToSecrets Set, Get

    # Grant Key Vault access to the user-assigned managed identity.
    $UAMI = Get-AzUserAssignedIdentity `
        -ResourceGroupName $resourceGroup `
        -Name $userAssignedManagedIdentity

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $UAMI.PrincipalId `
        -PermissionsToSecrets Set, Get
    ```

   若要通过其他方式创建 Azure 密钥保管库并存储机密，请参阅[密钥保管库快速入门](../key-vault/index.yml)。

## <a name="assign-permissions-to-managed-identities"></a>向托管标识分配权限

向适当的[托管标识](./automation-security-overview.md#managed-identities-preview)分配权限。 Runbook 可使用自动化帐户的系统分配的托管标识或用户分配的托管标识。 已提供为每个标识分配权限的步骤。 以下步骤使用 PowerShell。 如果你偏向于使用门户，请参阅[使用 Azure 门户分配 Azure 角色](./../role-based-access-control/role-assignments-portal.md)。

1. 使用 PowerShell cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 将角色分配给系统分配的托管标识。

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $SA_PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. 为用户分配的托管标识分配角色。

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId`
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. 对于系统分配的托管标识，请显示 `ClientId` 并记录值供稍后使用。

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-the-runbook-to-send-an-email"></a>创建用于发送电子邮件的 runbook

在创建密钥保管库并存储 `SendGrid` API 密钥后，接下来可以创建 Runbook 来检索 API 密钥和发送电子邮件。 让我们使用一个 Runbook，该 Runbook 使用[系统分配的托管标识](./automation-security-overview.md#managed-identities-preview)向 Azure 进行身份验证，以便从 Azure 密钥保管库检索机密。 我们将该 runbook 命名为 Send-GridMailMessage。 可以修改用于不同方案的 PowerShell 脚本。

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到自动化帐户。

1. 在打开的自动化帐户页面中，选择“流程自动化”下的“Runbook” 

1. 选择“+ 创建 Runbook”。
    1. 将 runbook 命名为 `Send-GridMailMessage`。
    1. 从“Runbook 类型”下拉列表中，选择“PowerShell” 。
    1. 选择“创建”。

   ![创建 Runbook](./media/automation-send-email/automation-send-email-runbook.png)

1. 此时会创建 Runbook 并打开“编辑 PowerShell Runbook”页。
   ![编辑 Runbook](./media/automation-send-email/automation-send-email-edit.png)

1. 将以下 PowerShell 示例复制到“编辑”页中。 确保 `VaultName` 指定你为密钥保管库选择的名称。

    ```powershell
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content,
      [Parameter(Mandatory=$True)]
      [String] $ResourceGroupName
    )

    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context

    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext 

    $VaultName = "<Enter your vault name>"

    $SENDGRID_API_KEY = Get-AzKeyVaultSecret `
        -VaultName $VaultName `
        -Name "SendGridAPIKey" `
        -AsPlainText -DefaultProfile $AzureContext

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

1. 如果希望 Runbook 使用系统分配的托管标识执行，请按原样保留代码。 如果希望使用用户分配的托管标识，则执行以下操作：
    1. 从第 18 行中删除 `$AzureContext = (Connect-AzAccount -Identity).context`，
    1. 将其替换为 `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`，然后
    1. 输入之前获取的客户端 ID。

1. 选择“保存”、“发布”，并在出现系统提示时选择“是”  。

若要验证 runbook 是否成功执行，可以按[测试 runbook](manage-runbooks.md#test-a-runbook) 或[启动 runbook](start-runbooks.md) 下的步骤操作。

如果一开始看不到测试电子邮件，请检查 Junk 和 Spam 文件夹。

## <a name="clean-up-resources"></a>清理资源

1. 如果不再需要该 Runbook，请在 Runbook 列表中选择它，然后选择“删除”。

1. 使用 [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault) cmdlet 删除密钥保管库。

    ```powershell
    $VaultName = "<your KeyVault name>"
    $resourceGroup = "<your ResourceGroup name>"
    Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $resourceGroup
    ```

## <a name="next-steps"></a>后续步骤

* 若要将 runbook 作业数据发送到 Log Analytics 工作区，请参阅[将 Azure 自动化作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。
* 若要监视基本级别的指标和日志，请参阅[使用警报触发 Azure 自动化 runbook](automation-create-alert-triggered-runbook.md)。
