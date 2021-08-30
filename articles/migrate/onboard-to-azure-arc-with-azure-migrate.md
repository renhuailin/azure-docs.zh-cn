---
title: 将 VMware 虚拟环境中的本地服务器加入 Azure Arc
description: 将 VMware 虚拟环境中的本地服务器加入 Azure Arc
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/27/2021
ms.openlocfilehash: 675c90218f456fc0f238fcf3b1fb93d2e5a7bc44
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296305"
---
# <a name="onboard-on-premises-servers-in-vmware-virtual-environment-to-azure-arc"></a>将 VMware 虚拟环境中的本地服务器加入 Azure Arc   

本文介绍如何使用Azure Migrate 发现和评估工具将本地 VMware VM 加入到 Azure Arc for Azure 管理中。 

通过 Azure Arc，可将 Azure 管理体验扩展到不太适合迁移的本地服务器，从而使用单一管理平台管理混合 IT 资产。 [详细了解](../azure-arc/servers/overview.md) Azure Arc。 

## <a name="before-you-get-started"></a>准备工作

- [查看要求](./tutorial-discover-vmware.md#prerequisites)，使用 Azure Migrate 发现和评估工具来发现在 VMware 环境中运行的服务器。  
- 准备 [VMware vCenter](./tutorial-discover-vmware.md#prepare-vmware) 以供使用，查看有关执行软件清单的 [VMware 要求](migrate-support-matrix-vmware.md#vmware-requirements)。 软件清单必须是完整的，这样才能开始将已发现的服务器加入 Azure Arc。   
- 在服务器上启动软件清单之前，请查看[应用程序发现要求](migrate-support-matrix-vmware.md#software-inventory-requirements)。 必须在 Windows 服务器上安装 PowerShell 3.0 或更高版本。 
- 验证先决条件，以允许远程连接到已发现服务器的清单，以便将它们加入 Azure Arc。 
    1. 允许到已发现服务器的入站远程连接 
        - 对于 Windows：WinRM 端口 5985 (HTTP) 上的入站连接。 在所有目标 Windows 服务器上，运行“winrm qc”命令，以启用本地计算机上的 WS-Management 协议。 
        - 对于 Linux：在所有目标 Linux 服务器上，允许端口 22 上的入站连接 (SSH)。
        - 也可以将远程计算机（发现的服务器）的 IP 地址添加到设备上的 WinRM TrustedHosts 列表。 
    2. Azure Migrate 设备应具有到目标服务器的网络视线。 
- 请务必验证 [Azure Arc 的先决条件](../azure-arc/servers/agent-overview.md#prerequisites)，并查看以下注意事项：
    - 完成 vCenter Server 发现和软件清单后，才能开始加入到 Azure Arc。 软件清单打开后最多 6 小时就会完成。
    -  在 Arc 加入过程中，将在已发现的服务器上安装 [Azure Arc Hybrid Connected Machine 代理](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)。 请务必提供在服务器上具有管理员权限的凭据来安装和配置代理。 在 Linux 上提供根帐户；在 Windows 上，提供“本地管理员”组中的帐户。 
    - 验证服务器是否正在运行[受支持的操作系统](../azure-arc/servers/agent-overview.md#supported-operating-systems)。
    - 确保 Azure 帐户已被分配了[所需的 Azure 角色](../azure-arc/servers/agent-overview.md#required-permissions)。
    - 如果已发现的服务器通过防火墙或代理服务器进行连接来通过 Internet 通信，请确保[所需的 RUL](../azure-arc/servers/agent-overview.md#networking-configuration) 不会被阻止。
    - 查看 Azure Arc [支持的区域](../azure-arc/servers/overview.md#supported-regions)。 
    - 已启用 Azure Arc 的服务器在一个资源组中支持多达 5,000 个计算机实例。


## <a name="set-up-the-azure-migrate-project"></a>设置 Azure Migrate 项目  

1. 在开始之前，请准备 [Azure 用户帐户](./tutorial-discover-vmware.md#prepare-an-azure-user-account)，并验证订阅中是否拥有[必需的角色](./create-manage-projects.md#verify-permissions)来创建 Azure Migrate 所需的资源。 
2. [运用本文](./create-manage-projects.md)来设置新的 Azure Migrate 项目，并向其添加 Azure Migrate 发现和评估工具。  

    > [!Note]
    > 你还可使用现有迁移项目，并将已发现的服务器清单加入 Azure Arc。为此，请从设备服务器启动设备配置管理器，并确保将服务已更新为最新版本。 [了解详细信息](./migrate-appliance.md#appliance-upgrades) <br/> <br/> 接下来，[按照这些说明](#onboard-to-azure-arc)加入服务器。  

## <a name="deploy-and-register-the-azure-migrate-appliance"></a>部署并注册 Azure Migrate 设备 

“Azure Migrate：发现和评估”使用轻型 Azure Migrate 设备。 设备执行服务器发现并将服务器配置和性能元数据发送到 Azure Migrate。 

在设置设备之前， 

1. [查看](migrate-appliance.md#appliance---vmware) Azure Migrate 设备的部署要求。
2. 查看设备在[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)中需要访问的 Azure URL。 
3. 注意设备的[端口访问要求](migrate-support-matrix-vmware.md#port-access-requirements)。 


下一步，

- 按照本文[设置 Azure Migrate 设备](./tutorial-discover-vmware.md#set-up-the-appliance)来启动 vCenter Server 发现。 若要部署设备，可下载 OVA 模板并将其导入 VMware，创建一个在 vCenter Server 中运行的服务器。  
- 部署设备后，需要先将其注册到项目，然后再启动发现。 按照[这些说明](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)注册设备。 

## <a name="configure-the-appliance-and-start-discovery"></a>配置设备并启动发现  

按照[本文](./tutorial-discover-vmware.md#start-continuous-discovery)配置 Azure Migrate 设备并启动 vCenter Server 发现。 

配置执行发现的设备时，需要在设备配置管理器中指定详细信息：

- 要连接到的 vCenter Server 的详细信息。  
- 用于发现 VMware 环境中的服务器的 vCenter Server 凭据。 
- 具有管理员权限的服务器凭据。 要加入 Azure Arc，需要在服务器上具有管理员权限的凭据来安装和配置 Azure Arc Hybrid Connect Machine 代理。 [详细了解](add-server-credentials.md)如何提供凭据以及我们如何处理凭据。

成功完成发现后，已发现的服务器要大约 15 分钟后就会在门户中显示。


## <a name="onboard-to-azure-arc"></a>加入 Azure Arc

>[!Important]
>在将已发现的服务器加入 Azure Arc 之前，软件清单必须是完整的。 

一旦完成 vCenter Server 发现，将自动启动软件清单（发现的已安装的应用程序）。 在完成软件清单的过程中，将循环访问所提供的服务器凭据，并针对已发现的服务器对其进行验证。 你可在完成软件清单且映射凭据后开始加入。 软件清单打开后最多 6 小时就会完成。  
1. 导航到“加入 Azure Arc”面板。 

    ![Arc 加入](./media/onboard-to-azure-arc-with-azure-migrate/azure-arc-onboarding-panel-after-being-enabled.png)

2. 提供希望在 Azure 中用来管理服务器的订阅和资源组 。

3. 在“区域”下拉列表中，选择用于存储服务器元数据的 Azure 区域。

4. 提供 Azure Active Directory 服务主体详细信息便于大规模加入。 请查看此文章，了解如何[使用 Azure 门户或 Azure PowerShell 创建服务主体。](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) <br/>

    需要以下输入：
    - **目录（租户）ID**：表示 Azure AD 专用实例的 [唯一标识符 (GUID)](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。 
    - **应用程序（客户端）ID**：表示服务主体的应用程序 ID 的 [唯一标识符 (GUID)](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。
    - **服务主体机密（应用程序机密）** ：用于基于密码的身份验证的 [客户端密码](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)。 
    
5. 可选：如果已发现的服务器需要代理器来连接到 Internet，请提供代理服务器 IP 地址/名称和端口号 。 按格式 `http://<proxyURL>:<proxyport>` 输入值。 已发现的服务器使用的代理服务器可能与设备服务器连接到 Internet 所需的代理服务器不同（后者请查看设备配置管理器中的“先决条件”部分）。   

    > [!Note]
    > 不支持设置代理身份验证。 

6. 选择“开始加入”来启动 Azure Arc 加入过程。 加入过程将需要一些时间才能完成。 完成加入后，你将看到完成状态和详细的加入状态报表。    

    ![Arc 加入报表](./media/onboard-to-azure-arc-with-azure-migrate/onboarding-completion-report.png)

    > [!Note]
    > 如果登录过期，请选择“再次登录”。 这将打开包含设备代码的模式。 选择“复制代码并登录”来复制设备代码，并在新的浏览器标签页中打开 Azure 登录提示。如果未显示提示，请确保已在浏览器中禁用了弹出窗口阻止程序。

7. 完成加入后，导航到 [Azure Arc 主页](https://portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/servers)来查看和管理已加入的服务器。   

8. 查看详细的加入报表，了解服务器的加入状态并执行相应操作。 

> [!Note]
> Azure Migrate 设备将启动 WinRM 会话来执行 Azure Arc 加入脚本。 请确保没有任何设置限制对目标服务器上的 WinRM 服务的访问。    


## <a name="next-steps"></a>后续步骤 

- 有关更多信息和错误详情，请查看详细的加入报表。 解决错误（若有）以成功加入服务器。 
- 可选择“开始加入”，重新运行 Azure Arc 加入进程。 对于任何新发现的服务器以及在上次运行中无法成功加入的服务器，将尝试加入。   

## <a name="troubleshooting-azure-arc-onboarding-errors"></a>排查 Azure Arc 加入错误

如果在使用 Azure Migrate 设备加入 Azure Arc 时遇到错误，以下部分可帮助确定可能的原因以及建议的问题解决步骤。 

如果没有看到下面列出的错误代码，或者错误代码以 AZCM 开头，请查看 [Azure Arc 故障排除指南](../azure-arc/servers/troubleshoot-agent-onboard.md)

### <a name="error-60001---unabletoconnecttophysicalserver"></a>错误 60001 - UnableToConnectToPhysicalServer  

可能的原因  
未满足有关连接到服务器的[先决条件](./migrate-support-matrix-physical.md)，或者在连接到服务器时出现网络问题，例如一些代理设置。

**建议的操作**   
- 确保服务器满足[先决条件](#before-you-get-started)和[端口访问要求](./migrate-support-matrix-physical.md)。 
- 将远程计算机（发现的服务器）的 IP 地址添加到 Azure Migrate 设备上的 WinRM TrustedHosts 列表，然后重试该操作。 这是为了允许服务器上的远程入站连接 - Windows：WinRM 端口 5985 (HTTP) 和 Linux：SSH 端口 22 (TCP) 。
- 确保已在设备上选择了正确的身份验证方法来连接到服务器。 
   > [!Note] 
   > Azure Migrate 支持对 Linux 服务器使用基于密码的身份验证和基于 SSH 密钥的身份验证。
- 如果问题仍然存在，请提交 Microsoft 支持案例，并提供设备计算机 ID（可在设备配置管理器的页脚中找到）。     
   

### <a name="error-60002---invalidservercredentials"></a>错误 60002 - InvalidServerCredentials  

可能的原因  
无法连接到服务器。 在设备上提供的凭据有误，或者之前提供的凭据已过期。

**建议的操作**  
- 确保为设备上的服务器提供了正确的凭据。 可使用这些凭据尝试连接到服务器来进行检查。
- 如果添加的凭据不正确或已过期，请在设备上编辑凭据，并重新验证已添加的服务器。 如果验证成功，说明问题已解决。
- 如果问题仍然存在，请提交 Microsoft 支持案例，并提供设备计算机 ID（可在设备配置管理器的页脚中找到）。

### <a name="error-60005---sshoperationtimeout"></a>错误 60005 - SHOperationTimeout  

可能的原因  
- 由于网络延迟问题或服务器上缺少最新更新，操作耗时超过预期。 

**建议的操作**  
- 确保受影响的服务器已安装最新的内核和 OS 更新。
- 确保设备与服务器之间没有网络延迟。 建议将设备和源服务器置于同一域中来避免出现延迟问题。
- 从设备连接到受影响的服务器，并运行[此处所述](./troubleshoot-appliance.md)的命令来检查它们是否返回 null 或空数据。
- 如果问题仍然存在，请提交 Microsoft 支持案例，并提供设备计算机 ID（可在设备配置管理器的页脚中找到）。  

### <a name="error-60108---softwareinventorycredentialnotassociated"></a>错误 60108 - SoftwareInventoryCredentialNotAssociated  

可能的原因  
- 找不到与服务器关联的凭据。

**建议的操作**  
- 软件清单必须是完整的，这样才能开始将已发现的服务器加入 Azure Arc。[了解详细信息](./how-to-discover-applications.md#add-credentials-and-initiate-discovery)
- 确保设备配置管理器上提供的凭据有效，而且可使用这些凭据访问服务器。
- 返回到设备配置管理器，来提供另一组凭据或编辑现有凭据。  

### <a name="error-60109---arcosnotsupported"></a>错误 60109 - ArcOsNotSupported  

可能的原因  
- 服务器托管不支持 Azure Arc 加入的操作系统。

**建议的操作**  
- 查看 Azure Arc [支持的操作系统](../azure-arc/servers/agent-overview.md#supported-operating-systems)。 
 
### <a name="error-10002---scriptexecutiontimedoutonvm"></a>错误 10002 - ScriptExecutionTimedOutOnVm  

可能的原因  
- 加入任务未及时完成。 然后，执行耗时超出预期。 

**建议的操作**  
- 一段时间后，该问题会自动解决。 如果问题持续出现，请联系 Microsoft 支持。  
 
### <a name="error-50000---accessdenied"></a>错误 50000 - AccessDenied 

可能的原因  
- 由于禁止访问服务器，该操作无法完成。 设备上提供的用于访问服务器的用户帐户没有所需权限，或者凭据不正确。 WinRM 错误代码：0x80070005

**建议的操作**  
- 验证可能的原因，然后重试该操作。 如果该问题仍然存在，请联系支持部门。

### <a name="error-9609516000960078---nullresultunhandledexceptionserverunknownerrorunhandlederror"></a>错误 960/951/60009/60078 - NullResult/UnhandledException/ServerUnknownError/UnhandledError

可能的原因  
- 操作由于内部错误而失败。 

**建议的操作**  
- 稍后重试操作。 如果问题仍然存在，请联系支持人员，并提供设备计算机 ID（可在设备配置管理器的页脚中找到）。