---
title: 教程：使用托管标识访问 Azure Key Vault - Windows - Azure AD
description: 本教程将指导你完成使用 Windows VM 系统分配的托管标识访问 Azure Key Vault 的过程。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190df484e53a350a0fed331c4bcc790f1a7a573c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236192"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>教程：使用 Windows VM 系统分配的托管标识访问 Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍 Windows 虚拟机 (VM) 如何使用系统分配的托管标识来访问 [Azure Key Vault](../../key-vault/general/overview.md)。 作为引导，Key Vault 随后可让客户端应用程序使用机密访问未受 Azure Active Directory (AD) 保护的资源。 托管服务标识由 Azure 自动管理，可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中包含身份验证信息了。

学习如何：

> [!div class="checklist"]
> * 授予 VM 对 Key Vault 中存储的密钥的访问权限
> * 使用 VM 标识获取访问令牌，并使用它来检索 Key Vault 中的密钥 

## <a name="prerequisites"></a>先决条件

- 具备托管标识相关知识。 如果不熟悉 Azure 资源功能的托管标识，请参阅此[概述](overview.md)。 
- 一个 Azure 帐户，[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。
- 在相应范围（订阅或资源组）内具有“所有者”权限，以执行所需的资源创建和角色管理步骤。 如需角色分配方面的帮助，请参阅[分配 Azure 角色以管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。
- 还需要启用了系统分配的托管标识的 Windows 虚拟机。
  - 如需为本教程创建虚拟机，则可以按照标题为[创建启用了系统分配的标识的虚拟机](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)的文章进行操作

## <a name="create-a-key-vault"></a>创建密钥保管库  

本部分说明如何授予 VM 访问密钥保管库中存储的密钥的权限。 使用 Azure 资源的托管标识，代码可以获取访问令牌，对支持 Azure AD 身份验证的资源进行身份验证。但是，并非所有 Azure 服务都支持 Azure AD 身份验证。 若要将 Azure 资源的托管标识用于这些服务，请将服务凭据存储在 Azure Key Vault 中，然后使用 VM 的托管标识访问 Key Vault 以检索凭据。

首先，我们需要创建一个 Key Vault 并授予 VM 的系统分配托管标识对 Key Vault 的访问权限。

1. 打开 Azure [门户](https://portal.azure.com/)
1. 在左侧导航栏的顶部，选择“创建资源”  
1. 在“搜索市场”框中，键入“Key Vault”，然后按 Enter  。  
1. 从结果中选择“Key Vault”。
1. 选择“创建”
1. 为新 Key Vault 提供一个名称。

    ![“创建密钥保管库”屏幕](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. 填写所有必填信息，以确保选择在其中创建了本教程所用虚拟机的订阅和资源组。
1. 选择“查看 + 创建”
1. 选择“创建”

### <a name="create-a-secret"></a>创建机密

接下来，将机密添加到 Key Vault，以便稍后可以使用在 VM 中运行的代码检索此机密。 本教程使用的是 PowerShell，但相同的概念适用于在此虚拟机中执行的任何代码。

1. 导航到新创建的密钥保管库。
1. 选择“密钥”，然后单击“添加”。
1. 选择“生成/导入”
1. 在“创建机密”屏幕的“上传选项”中，将“手动”保留为选中状态  。
1. 输入密钥的名称和值。  该值可以是任何需要的内容。 
1. 明确指定激活日期和到期日期，并将“已启用”设置为“是”。 
1. 单击“创建”以创建密钥。

   ![创建机密](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>授予访问权限

需要对虚拟机使用的托管标识授予访问权限，以便读取将存储在密钥保管库中的机密。

1. 导航到新创建的密钥保管库
1. 在左侧菜单中选择“访问策略”。
1. 选择“添加访问策略”

   ![“密钥保管库创建访问策略”屏幕](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. 在“从模板配置(可选)”下的“添加访问策略”部分的下拉菜单中选择“机密管理”  。
1. 选择“选择主体”，并在搜索字段中输入之前创建的 VM 的名称。  选择结果列表中的 VM，然后选择“选择”。
1. 选择“添加”
1. 选择“保存”。


## <a name="access-data"></a>访问数据  

本部分介绍如何使用 VM 标识获取访问令牌并使用它从密钥保管库中检索机密。 如果未安装 PowerShell 4.3.1 或更高版本，则需要[下载并安装最新版本](/powershell/azure/)。

首先，我们使用 VM 的系统分配托管标识获取访问令牌，向 Key Vault 证明身份：
 
1. 在门户中，导航到“虚拟机”并转到 Windows 虚拟机，然后在“概述”中，单击“连接”。
2. 输入创建 Windows VM 时添加的用户名和密码。  
3. 现在，已经创建了与虚拟机的远程桌面连接，请在远程会话中打开 PowerShell。  
4. 在 PowerShell 中，调用租户上的 Web 请求，为 VM 特定端口中的本地主机获取令牌。  

PowerShell 请求：

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

可以看到如下响应：

![带有令牌响应的请求](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

接下来，从响应中提取访问令牌。  

```powershell
   $KeyVaultToken = $Response.access_token
```

最后，使用 PowerShell 的 Invoke-WebRequest 命令检索之前在 Key Vault 中创建的密钥，在授权标头中传递访问令牌。  将需要 Key Vault 的 URL，该 URL 位于 Key Vault 的“概述”页的“软件包”部分。  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

响应将如下所示： 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

在检索 Key Vault 中的密钥后，可以使用该密钥对需要名称和密码的服务进行身份验证。

## <a name="clean-up-resources"></a>清理资源

需要清理资源时，请访问 [Azure 门户](https://portal.azure.com)，选择“资源组”，找到并选择在本教程中创建的资源组（例如 `mi-test`），然后使用“删除资源组”命令。

此外，也可以通过 [PowerShell 或 CLI](../../azure-resource-manager/management/delete-resource-group.md) 执行此操作

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 Windows VM 系统分配的托管标识来访问 Azure Key Vault。  若要详细了解 Azure Key Vault，请参阅：

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)
