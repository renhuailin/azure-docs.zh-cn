---
title: 配置实验室标识
description: 了解如何在 Azure 开发测试实验室配置实验室标识。
ms.topic: how-to
ms.date: 08/20/2020
ms.openlocfilehash: 3cc14d87cfea331d8a11e09071005c23be942405
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638722"
---
# <a name="configure-a-lab-identity"></a>配置实验室标识

生成云应用程序时需要应对的常见挑战是，如何管理代码中用于云服务身份验证的凭据。 保护这些凭据是一项重要任务。 理想情况下，这些凭据永远不会出现在开发者工作站上，也不会被签入源代码管理系统中。 虽然 Azure Key Vault 可用于安全存储凭据、机密以及其他密钥，但代码必须通过 Key Vault 的身份验证才能检索它们。 

Azure Active Directory (Azure AD) 中的 Azure 资源托管标识功能可以解决此问题。 此功能为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。 详细了解 [Azure 中的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 

托管标识分为两种类型： 

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识  

系统分配的托管标识 ****  可直接在 Azure 服务实例上启用。 启用标识后，Azure 将在实例的订阅信任的 Azure AD 租户中创建实例的标识。 创建标识后，系统会将凭据预配到实例。 系统分配标识的生命周期直接绑定到启用它的 Azure 服务实例。 如果实例遭删除，Azure 会自动清理 Azure AD 中的凭据和标识。 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>使用实验室系统分配标识的方案  

每个开发测试实验室在创建时都会获得一个系统分配的标识，该标识在实验室的整个生命周期内一直有效。 系统分配的标识用于以下目的：  

- 所有用于启动多 VM 和/或平台即服务环境的基于 [Azure 资源管理器](devtest-lab-create-environment-from-arm.md) 的部署，都将使用实验室的系统分配标识执行  
- 通过实验室的系统分配标识，支持使用客户托管密钥对实验室磁盘进行磁盘加密。 通过提供对实验室标识的显式访问权限来访问磁盘加密集，实验室可以代表你对所有虚拟机磁盘进行加密。 详细了解如何使用客户托管密钥为实验室磁盘[启用磁盘加密](encrypt-disks-customer-managed-keys.md)。  

### <a name="configure-identity"></a>配置标识

本部分演示如何配置实验室的标识策略。

> [!NOTE]
> 对于在 2020 年 10 月 8 日之前创建的实验室，系统分配的标识将设置为“关”。 作为实验室所有者，如果打算使用实验室来实现上一部分所列的目的，可以将其打开。  
>
> 对于在 2020 年 10 月 8 日之后创建的新实验室，实验室的系统分配标识默认设置为“开”，并且实验室所有者在该实验室的生命周期内不能将其关闭。  

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“开发测试实验室”。
1. 从实验室列表中，选择所需的实验室。
1. 选择“配置和策略”**配置和策略** -> “标识（预览版）”。 

> [!div class="mx-imgBorder"]
> ![配置标识](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识  

用户分配的托管标识作为独立的 Azure 资源创建。 在创建过程中，Azure 会在由所用订阅信任的 Azure AD 租户中创建一个标识。 在创建标识后，可以将标识分配到一个或多个 Azure 服务实例。 用户分配标识的生命周期与它所分配到的 Azure 服务实例的生命周期是分开管理的。 

开发测试实验室对于虚拟机和基于 Azure 资源管理器的环境均支持用户分配的标识。  有关详细信息，请参阅以下主题：

- [添加用户分配的标识以部署实验室 Azure 资源管理器环境](use-managed-identities-environments.md)
- [添加用户分配的标识以部署实验室虚拟机](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>后续步骤

查看[配置成本管理](devtest-lab-configure-cost-management.md)