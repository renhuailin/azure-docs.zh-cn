---
title: 安装 Azure AD Connect 预配代理
description: 了解如何安装 Azure AD Connect 预配代理，以及如何在 Azure 门户中对其进行配置。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 660d233c4fc0690799e8ec25e6a07e516035a90b
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506324"
---
# <a name="install-the-azure-ad-connect-provisioning-agent"></a>安装 Azure AD Connect 预配代理
本文将指导你完成 Azure Active Directory (Azure AD) Connect 预配代理的安装过程，以及如何在 Azure 门户中对其进行初始配置。

>[!IMPORTANT]
>以下安装说明假定所有[先决条件](how-to-prerequisites.md)均已具备。

>[!NOTE]
>本文介绍了如何使用向导安装预配代理。 有关使用命令行界面 (CLI) 安装 Azure AD Connect 预配代理的信息，请参阅[使用 CLI 和 PowerShell 安装 Azure AD Connect 预配代理](how-to-install-pshell.md)。

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
组托管服务帐户 (gMSA) 是一种托管的域帐户，提供自动密码管理、简化的服务主体名称 (SPN) 管理以及将管理委派给其他管理员的功能。 它还在多个服务器上扩展了此功能。 Azure AD Connect 云同步支持并建议使用组托管服务帐户运行此代理。 有关组托管服务帐户的详细信息，请参阅[组托管服务帐户](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。


### <a name="upgrade-an-existing-agent-to-use-the-gmsa"></a>升级现有代理以使用 gMSA
若要升级现有代理以使用在安装过程中创建的组托管服务帐户，只需通过运行 AADConnectProvisioningAgent.msi 将代理服务更新至最新版本即可。 现在，请再次运行安装向导，并在出现提示时提供用于创建帐户的凭据。

## <a name="install-the-agent"></a>安装代理

若要安装此代理，请执行以下步骤：

 1. 以企业管理员权限登录到要使用的服务器。
 1. 登录到 Azure 门户，然后选择“Azure Active Directory”。
 1. 在左侧菜单中，选择“Azure AD Connect”。
 1. 选择“管理云同步” > “查看所有代理” 
 1. 从 Azure 门户下载 Azure AD Connect 预配代理。
 
    ![显示“下载本地代理”的屏幕截图。](media/how-to-install/install-9.png)</br>
 1. 接受条款，然后选择“下载”。
 1. 运行 Azure AD Connect 预配安装程序 AADConnectProvisioningAgentSetup.msi。
 1. 在“Microsoft Azure AD Connect 预配代理包”屏幕上，接受许可条款，然后选择“安装”。
 
    ![显示“Microsoft Azure AD Connect 预配代理包”界面的屏幕截图。](media/how-to-install/install-1.png)</br>
 1. 此操作完成后，配置向导将启动。 使用 Azure AD 全局管理员帐户登录。
 1. 在“配置服务帐户”屏幕上，选择“创建 gMSA”或“使用自定义 gMSA”。 如果允许代理创建帐户，则该帐户将命名为 provAgentgMSA$。 如果指定“使用自定义 gMSA”，系统会提示你提供此帐户。
 1. 输入用于创建将用于运行代理服务的组托管服务帐户的域管理员凭据。 选择“下一步”。
  
    ![显示“创建 gMSA”选项的屏幕截图。](media/how-to-install/install-12.png)</br>
 1. 在“Connect Active Directory”屏幕上，选择“添加目录”。 然后使用你的 Active Directory 管理员帐户登录。 此操作将添加本地目录。 
 1. （可选）通过选中“选择域控制器优先级”复选框以及对域控制器列表进行排序，可以对代理将要使用的域控制器的首选项进行管理。 选择“确定”。
 
    ![显示域控制器排序的屏幕截图。](media/how-to-install/install-2a.png)</br>
 1. 选择“下一步”。
 
    ![显示“连接 Active Directory”屏幕的屏幕截图。](media/how-to-install/install-3a.png)</br>
 1. 在“代理安装”屏幕上，确认设置和将要创建的帐户，然后选择“确认”。
 
    ![显示“确认”设置的屏幕截图。](media/how-to-install/install-11.png)</br>
 1. 完成此操作后，应该会看到“代理安装已完成。” 选择“退出”。
 
    ![显示“配置完成”屏幕的屏幕截图。](media/how-to-install/install-4a.png)</br>
 1. 如果仍看到初始“Microsoft Azure AD Connect 预配代理包”屏幕，则选择“关闭”。

## <a name="verify-agent-installation"></a>验证代理安装
代理验证是在 Azure 门户中以及在运行该代理的本地服务器上进行的。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证
若要验证 Azure 是否能够识别到代理，请执行以下步骤：

 1. 登录到 Azure 门户。
 1. 在左侧选择“Azure Active Directory” > “Azure AD Connect”。 在中心位置选择“管理云同步”。

    ![显示 Azure 门户的屏幕截图。](media/how-to-install/install-6.png)</br>

 1. 在“Azure AD Connect 云同步”屏幕上，选择“查看所有代理”。

    ![显示“查看所有代理”选项的屏幕截图。](media/how-to-install/install-7.png)</br>
 
 1. 在“本地预配代理”屏幕上，将看到已安装的代理。 验证相关的代理是否在该屏幕上，并且标记为“活动”。

    ![显示“本地预配代理”屏幕的屏幕截图。](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>在本地服务器上
若要验证代理是否正在运行，请执行以下步骤：

1. 使用管理员帐户登录到服务器。
1. 导航到“服务”或选择“开始” > “运行” > “Services.msc”来打开“服务”。
1. 确保“Microsoft Azure AD Connect 代理更新程序”和“Microsoft Azure AD Connect 预配代理”包含在“服务”中，并且其状态为“正在运行”。

    ![显示“服务”屏幕的屏幕截图。](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>代理已安装，但在它开始同步用户之前，必须先配置和启用此代理。 若要配置新代理，请参阅[为 Azure AD Connect 云同步创建新配置](how-to-configure.md)。

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
- [为 Azure AD Connect 云同步创建新配置](how-to-configure.md)。

