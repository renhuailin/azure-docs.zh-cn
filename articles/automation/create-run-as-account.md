---
title: 创建 Azure 自动化运行方式帐户
description: 本文介绍如何使用 PowerShell 或 Azure 门户创建 Azure 自动化运行方式帐户。
services: automation
ms.subservice: process-automation
ms.date: 05/17/2021
ms.topic: conceptual
ms.openlocfilehash: 7ed16e9bbd1071e4831e814e617d69dc4a638510
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078249"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>如何创建 Azure 自动化运行方式帐户

Azure 自动化中的运行方式帐户提供身份验证，以使用自动化 runbook 和其他自动化功能管理 Azure 资源管理器或 Azure 经典部署模型上的资源。 本文介绍如何从 Azure 门户或 Azure PowerShell 创建运行方式帐户或经典运行方式帐户。

在 Azure 门户中创建运行方式帐户或经典运行方式帐户时，默认使用自签名证书。 如果要使用企业或第三方证书颁发机构颁发的证书 (CA)，可以使用[用于创建运行方式帐户的 PowerShell 脚本](#powershell-script-to-create-a-run-as-account)。

## <a name="create-account-in-azure-portal"></a>在 Azure 门户中创建帐户

请执行以下步骤，在 Azure 门户中更新 Azure 自动化帐户。 运行方式帐户和经典运行方式帐户是单独创建的。 如果不需管理经典资源，可以只创建 Azure 运行方式帐户。

1. 以订阅管理员角色成员和订阅共同管理员的帐户登录 Azure 门户。

2. 搜索并选择“自动化帐户”。

3. 在“自动化帐户”页上，从列表中选择你的自动化帐户。

4. 在左侧窗格中的“帐户设置”部分选择“运行方式帐户” 。

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="选择“运行方式帐户”选项。":::

5. 根据所需帐户，使用“+ Azure 运行方式帐户”或“+ Azure 经典运行方式帐户”窗格 。 查看概述信息后，单击“创建”。

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="选择创建运行方式帐户的选项":::

6. 在 Azure 创建运行方式帐户时，可以在菜单的“通知”下面跟踪进度。 此外还显示一个横幅，指出正在创建帐户。 此过程可能需要几分钟才能完成。

## <a name="create-account-using-powershell"></a>使用 PowerShell 创建帐户

以下列表提供了使用提供的脚本在 PowerShell 中创建运行方式帐户所要满足的要求。 这些要求适用于这两种类型的运行方式帐户。

* 装有 Azure 资源管理器模块 3.4.1 和更高版本的 Windows 10 或 Windows Server 2016。 PowerShell 脚本不支持早期版本的 Windows。
* Azure PowerShell 6.2.4 或更高版本。 有关信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/install-az-ps)。
* 一个自动化帐户，将其作为 `AutomationAccountName` 和 `ApplicationDisplayName` 参数的值引用。
* 与[配置运行方式帐户时所需的权限](automation-security-overview.md#permissions)中所列权限相当的权限。

如果打算使用企业或第三方证书颁发机构 (CA) 的证书，则需满足自动化对证书提出的以下配置要求：

   * 指定提供程序：Microsoft 增强型 RSA 和 AES 加密提供程序
   * 标记为可导出
   * 配置为使用 SHA256 算法
   * 以 `*.pfx` 或 `*.cer` 格式保存。

若要获取 PowerShell 脚本的必需参数 `AutomationAccountName`、`SubscriptionId` 和 `ResourceGroupName` 的值，请完成以下步骤。

1. 登录到 Azure 门户。

1. 搜索并选择“自动化帐户”。

1. 在“自动化帐户”页上，从列表中选择你的自动化帐户。

1. 在左窗格中选择“属性”。

1. 记下“属性”页上的“名称”、“订阅 ID”和“资源组”的值   。

   ![自动化帐户属性页](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>用于创建运行方式帐户的 PowerShell 脚本

PowerShell 脚本包含对多个配置的支持。

* 使用自签名证书创建运行方式帐户和/或经典运行方式帐户。
* 使用企业或第三方证书颁发机构 (CA) 颁发的证书创建运行方式帐户和/或经典运行方式帐户。
* 在 Azure 政府云中使用自签名证书创建运行方式帐户和/或经典运行方式帐户。

1. 使用以下命令下载脚本并保存到本地文件夹。

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. 利用提升的用户权限启动 PowerShell，并导航到包含该脚本的文件夹。

3. 运行以下命令之一，根据需要创建运行方式帐户和/或经典运行方式帐户。

    * 使用自签名证书创建运行方式帐户。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * 使用自签名证书创建运行方式帐户和经典运行方式帐户。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * 使用企业证书创建运行方式帐户和经典运行方式帐户。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        如果已使用企业公共证书（.cer 文件）创建了经典运行方式帐户，则使用此证书。 脚本创建证书并将它保存到计算机上用于执行 PowerShell 会话的用户配置文件 `%USERPROFILE%\AppData\Local\Temp` 下的临时文件文件夹中。 请参阅[将管理 API 证书上传到 Azure 门户](../cloud-services/cloud-services-configure-ssl-certificate-portal.md)。

    * 在 Azure 政府版云中使用自签名证书创建运行方式帐户和经典运行方式帐户

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. 执行脚本后，系统会提示在 Azure 上进行身份验证。 请以订阅管理员角色成员的帐户登录。 如果要创建经典运行方式帐户，则帐户必须是订阅共同管理员。

## <a name="next-steps"></a>后续步骤

* 若要开始使用 PowerShell Runbook，请参阅[教程：创建 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。

* 若要开始使用 Python 3 runbook，请参阅[教程：创建 Python 3 runbook](learn/automation-tutorial-runbook-textual-python-3.md)。
