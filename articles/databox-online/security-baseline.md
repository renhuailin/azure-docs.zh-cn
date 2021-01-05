---
title: Azure Stack Edge 的 Azure 安全基线
description: Azure Stack Edge 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 14e19732423da3842b2f2748a46219691fe222b9
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803147"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Azure Stack Edge 的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指南应用到 Microsoft Azure Stack Edge。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的 **安全控件** 进行分组，以及适用于 Azure Stack 边缘的相关指南。 排除了不适用于 Azure Stack 边缘的 **控件**。

若要查看 Azure Stack 边缘如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure Stack Edge 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](/azure/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

**指南**：客户将 Microsoft 提供的、物理 Azure Stack 的边缘设备部署到专用网络以进行内部访问，并提供更多的选项来对其进行保护。 例如，可以通过客户的内部网络访问 Azure Stack Edge 设备，并且需要客户配置的 IP。 此外，客户还可以选择访问密码来访问设备的用户界面。 

内部流量通过以下方式进一步保护：

- Azure 门户和 SDK 管理 Azure Stack 边缘设备需要 (TLS) 版本1.2 的传输层安全性。

- 任何客户端对设备的访问都是通过本地 web UI 使用标准 TLS 1.2 作为默认安全协议。

- 仅允许授权的 Azure Stack Edge Pro 设备加入客户在其 Azure 订阅中创建的 Azure Stack Edge 服务。

访问所引用的链接可获得更多信息。
 
- [在访问 Azure Stack Edge Pro GPU 设备的 Windows 客户端上配置 TLS 1。2](azure-stack-edge-j-series-configure-tls-settings.md)

- [快速入门-通过 GPU Azure Stack Edge Pro 入门](azure-stack-edge-gpu-quickstart.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

**指南**：客户可以选择点到站点虚拟专用网络 (VPN) 将 Azure Stack Edge 设备从其本地专用网络连接到 Azure 网络。 VPN 为从客户设备到 Azure 的传输层安全性提供了另一层加密机制。 

客户可以通过 Azure 门户或 Azure PowerShell 在其 Azure Stack 边缘设备上配置虚拟专用网络。

- [通过 Azure PowerShell 脚本为 Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 配置 Azure VPN](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [在访问 Azure Stack Edge Pro GPU 设备的 Windows 客户端上配置 TLS 1。2](azure-stack-edge-j-series-configure-tls-settings.md)

- [教程：为 Azure Stack Edge Pro R 配置证书](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立对 Azure 服务的专用网络访问

**指南**：客户可以选择点到站点虚拟专用网络 (VPN) 将 Azure Stack Edge 设备从其本地专用网络连接到 Azure 网络。 VPN 为从客户设备到 Azure 的传输层安全性提供了另一层加密机制。 

- [通过 Azure PowerShell 脚本为 Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 配置 Azure VPN](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [在访问 Azure Stack Edge Pro GPU 设备的 Windows 客户端上配置 TLS 1。2](azure-stack-edge-j-series-configure-tls-settings.md)

- [教程：为 Azure Stack Edge Pro R 配置证书](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保护应用程序和服务不受外部网络攻击

**指南**： Azure Stack Edge 设备合并了标准 Windows Server 网络保护功能，客户无法对其进行配置。

客户可以选择使用网络虚拟设备（例如具有高级分布式拒绝服务 (DDoS) 保护的防火墙）来保护与 Azure Stack Edge 设备连接的专用网络。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (IDS/IPS)

**指南**： Azure Stack Edge 设备使用的终结点均由 Microsoft 管理。 客户负责要部署到其本地系统的任何其他控件。

Azure Stack Edge 设备使用自己的入侵检测功能来保护服务。 

- [了解 Azure Stack 边缘安全性](azure-stack-edge-security.md)

- [Azure Stack 边缘的端口信息](azure-stack-edge-gpu-system-requirements.md)

- [获取硬件和软件入侵检测日志](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指南**：所有 Azure Stack Edge 设备在 Azure Active Directory (Azure AD) 中自动具有系统分配的托管标识。 目前，托管标识用于在 Azure Stack Edge 上承载的虚拟机的云管理。

Azure Stack 边缘设备启动到处于锁定状态以进行本地访问。 对于本地设备管理员帐户，你将需要通过本地 web 用户界面或 PowerShell 界面连接到你的设备，并设置一个强密码。 在安全位置（例如 Azure Key Vault）中存储和管理设备管理员凭据，并根据组织标准旋转管理员密码。

- [通过密码保护 Azure Stack Edge 设备](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 进行应用程序访问

**指南**： Azure Stack Edge 终结点设备不支持单一登录。 但是，你可以选择启用标准 Azure Active Directory (Azure AD 基于) 的单一登录，以安全访问 Azure 云资源。

- [了解 Azure AD 的应用程序 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指南**：多重身份验证是一种强大的身份验证控制，但为 Azure Stack Edge 服务用户提供了选择加入功能。 它可用于支持的方案，如 Azure 门户 Azure Stack 边缘设备的边缘管理。 请注意，对 Azure Stack Edge 设备的本地访问不支持多重身份验证。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指南**：启用 Azure Monitor 以收集 Azure Stack Edge 服务的设备或容器日志。 监视容器，如在设备上的 Kubernetes 群集中运行多个计算应用程序的容器。

此外，还可以在 Azure Stack Edge 设备的本地 web 用户界面中收集包含审核日志的支持包。 请注意，Azure 门户上不提供支持包。
 
- [在 Azure Stack Edge Pro 设备上启用 Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指南**： Azure Active Directory (Azure AD) 条件性访问是用于 Azure Stack Edge 服务进行身份验证的选择加入功能。 Azure Stack Edge 服务是 Azure 门户中的资源，可让你跨不同的地理位置管理 Azure Stack Edge Pro 设备。 

- [什么是条件性访问](../active-directory/conditional-access/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据透露

**指南**：遵循最佳做法来保护凭据，例如：

- 用于在 Azure 中使用 Azure Stack Edge 资源激活设备的激活密钥。
- 登录凭据以访问 Azure Stack Edge 设备。
- 有助于恢复 Azure Stack Edge 设备的关键文件。
- 通道加密密钥

定期轮换存储帐户密钥并对其进行同步，以帮助保护你的存储帐户不受未经授权的用户的保护。

- [Azure Stack Edge Pro 安全和数据保护](azure-stack-edge-security.md)

- [同步存储密钥](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指南**： Azure Stack Edge 解决方案包含多个组件，这些组件具有强大的访问控制权限，可限制对业务关键设备的访问。 你的组织将需要企业协议 (EA) 或云解决方案提供商 (CSP) 或 Microsoft Azure 赞助订阅来配置和管理设备： 

Azure Stack Edge 服务受 Azure 中托管的管理服务的 Azure 安全功能保护。 对于任何软件开发工具包管理操作，你都可以在设备属性中获取资源的加密密钥，但仅当你对资源图形 API 具有适当的权限时，才能查看加密密钥。

Azure Stack Edge Pro 设备是一个本地设备，它通过在本地处理数据，然后将数据发送到 Azure 来帮助转换数据。 你的设备：

- 需要激活密钥才能访问 Azure Stack Edge 服务。
- 由设备密码始终受保护。
- 已启用安全启动。

- 是锁定的设备。 设备基板管理控制器 (BMC) 和 BIOS 受密码保护。 BIOS 受受限用户访问保护。
- 运行 Windows Defender 设备防护。 Device Guard 使你可以仅运行你在代码完整性策略中定义的受信任的应用程序。

遵循最佳做法来保护用于访问 Azure Stack Edge 的所有凭据和机密。 

- [密码最佳做法](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指南**： Azure Stack Edge 有一个名为 "EdgeUser" 的用户，该用户可以配置设备。 它还具有 Azure 资源管理器用户 "EdgeArmUser" 用于设备上的本地 Azure 资源管理器功能。 

应遵循最佳做法来保护：

- 用于访问本地设备的凭据

- SMB 共享凭据。

- 对已配置为使用 NFS 共享的客户端系统的访问权限。

- 使用 Blob REST API 时用于访问本地存储帐户的本地存储帐户密钥。

引用的链接中提供了其他信息。

- [Azure Stack 边缘设备的安全性信息](azure-stack-edge-security.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指南**：安全和隔离的工作站对于敏感角色（如管理员、开发人员和关键服务操作员）的安全性至关重要。 使用带有或不带 Azure 堡垒的高度安全用户工作站执行管理任务。 使用 Azure Active Directory (Azure AD) ，Microsoft Defender 高级威胁防护 (ATP) 和 Microsoft Intune，为管理任务部署安全的托管用户工作站。 

可通过集中管理安全的工作站来强制实施安全配置，包括强身份验证、软件和硬件基线、受限的逻辑和网络访问。

- [了解特权访问工作站](/azure/active-directory/devices/concept-azure-managed-workstation) 

- [部署特权访问工作站](/azure/active-directory/devices/howto-azure-managed-workstation)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/azure/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指南**： Azure Stack Edge 将所有交互数据视为敏感数据，仅限有权访问此数据的授权用户。 应遵循最佳做法来保护用于访问 Azure Stack Edge 服务的凭据。

- [Azure Stack Edge Pro 安全和数据保护](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

**指南**： Azure Stack Edge 为航班中的数据使用安全通道。 这些是：

- 标准 TLS 1.2 用于在设备和 Azure 云之间传输的数据。 不会回退到 TLS 1.1 和更早版本。 如果不支持 TLS 1.2，则将阻止代理通信。 Azure 门户和软件开发工具包 (SDK) 管理还需要 TLS 1.2。

- 当客户端通过浏览器的本地 web 用户界面访问设备时，将使用标准 TLS 1.2 作为默认安全协议

最佳做法是将浏览器配置为使用 TLS 1.2。 在将数据从数据服务器复制时，请使用 SMB 3.0 和加密来保护数据。

- [保护传输中数据的最佳做法](azure-stack-edge-security.md#protect-data-in-flight)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

请注意，若要了解工作负荷和服务，可能需要其他权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指南**：只有获得授权的用户（例如，"EdgeArmUser"）才能通过本地 Azure 资源管理器访问 Azure Stack Edge 设备 api。 用户帐户密码只能在 Azure 门户管理。 

- [设置 Azure 资源管理器密码](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6：仅使用计算资源中经过批准的应用程序

**指南**：你可以在任何本地创建的虚拟机上运行你自己的应用程序。 使用 PowerShell 脚本在堆栈边缘设备上创建本地计算虚拟机。 我们强烈建议你只引入可在本地虚拟机上运行的受信任的应用程序。 

- [如何在 Windows 环境中控制 PowerShell 脚本执行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7.1&amp;viewFallbackFrom=powershell-6&amp;preserve-view=true)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

**指南**： Azure Stack Edge 不提供威胁检测功能。 但是，客户可以收集支持包中的审核日志，以便进行脱机威胁检测和分析。 

- [收集 Azure Stack Edge 设备的支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指南**： Azure Stack 边缘将网络审核日志作为可下载支持包的一部分启用。 可以分析这些日志，为你的 Azure Stack 边缘设备实现半实时监视。

- [Azure Stack Edge 收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指南**：目前不支持对日志进行实时监视 Azure Stack 边缘。 提供了一种收集支持包的功能，该功能允许你分析其中包含的各种日志，例如 Azure Stack Edge Pro 设备的防火墙、软件、硬件入侵和系统事件日志。 请注意，会收集用于入站和出站流量的软件入侵或默认防火墙日志。

- [收集 Azure Stack Edge 的支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指南**：目前不支持对日志进行实时监视 Azure Stack 边缘。 不过，你可以收集支持包，以便分析其中包含的各种日志。  支持包已压缩，并下载到所选的路径。 解压缩包，并查看其中包含的系统日志文件。 你还可以将这些日志发送到安全信息事件管理工具或其他中心存储位置以进行分析。

- [收集 Azure Stack Edge 的支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指南**：无法在 Azure Stack Edge 设备上更改日志存储保持期。 根据需要清除较旧的日志。 可以定期从设备收集支持包，以满足任何要求，以使日志长时间保持不变。 

- [收集 Azure Stack Edge 的支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7：使用批准的时间同步源

**指南**： Azure Stack Edge 使用 time.windows.com，这是 Microsoft 提供的网络时间提供商服务器。  Azure Stack 边缘还允许客户配置其选择的网络时间协议服务器。

- [配置 Azure Stack 边缘设备时间设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指南**：确保组织事件响应计划包含以下过程： 

- 响应安全事件

- 已更新 Azure 云
 
- 定期执行，以确保准备就绪

建议在企业环境中使用标准化的事件响应过程来实现安全性。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件 

**指南**：确保你有创建高质量警报并测量其质量的过程。 这样，你就可以了解过去事件的经验并为分析师的警报设置优先级，防止浪费时间处理误报警报。 基于过去事件的经验、经验证的社区源和工具构建高质量的警报，旨在生成和清理各种警报源的日志记录和关联警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可使用安全中心数据连接器将警报流式传输到 Azure Sentinel。 使用 Azure Sentinel 创建高级警报规则，以生成自动事件以进行调查。 

将安全中心警报和建议导出到 Azure Sentinel，并提供导出功能，以帮助确定 Azure 资源的风险。 建议创建一个流程，以自动方式导出警报和建议，以实现持续的安全性。

- [如何配置导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

**指南**：确保分析师可以查询和使用不同的数据源，以生成在调查潜在事件时所发生的活动的完整视图。 应收集不同的日志类型，以跟踪跨 kill 链的潜在攻击者的活动，以避免出现盲点。  另外，请确保捕获见解和知识以供历史参考。  

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据 - 使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 选择操作系统的本机内存转储功能，以创建正在运行的系统内存的快照。

    - 使用 Azure 服务或第三方软件功能的快照功能来创建正在运行的系统的快照。

Azure Sentinel 提供几乎针对任何日志源的广泛数据分析，并提供一个事例管理门户来管理事件的整个生命周期。 调查过程中的情报信息可与事件相关联，以便进行跟踪和报告。 

- [Windows 计算机的磁盘快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 调查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 使用从 Azure 安全中心分配给每个警报的严重性，帮助你确定应该首先调查的警报的优先级。 严重性取决于安全中心对调查结果或用于发出警报的分析的可信度，以及对导致警报的活动背后存在恶意意图的可信度级别。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：安全状况和漏洞管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)。

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：为计算资源建立安全配置

**指南**： Azure Stack Edge 支持为客户创建的本地虚拟机创建安全配置。 强烈建议使用 Microsoft 提供的准则在创建本地虚拟机时建立安全基线，

- [下载安全符合性工具包中包含的安全基线](/windows/security/threat-protection/windows-security-baselines)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4：为计算资源维护安全配置

**指南**： Azure Stack Edge 不支持为客户创建的本地虚拟机提供安全配置。 强烈建议客户使用安全符合性工具包 (SCT) 来帮助维持其计算资源的安全配置。

Azure Stack 边缘管理的主机操作系统和虚拟机维护其安全配置。 

- [Windows 安全基线](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5：安全存储自定义操作系统和容器映像

**指南**：安全地存储主机操作系统和 Azure Stack 边缘管理的虚拟机。 

客户可以引入自己的虚拟机和容器映像，并负责其安全管理。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7：快速自动修正软件漏洞

**指南**： Azure Stack Edge 提供定期修补程序更新，并在这些更新可用于修补漏洞时向客户发出警报。 客户负责使其设备和虚拟机 (通过最新的修补程序创建) 。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="endpoint-security"></a>终结点安全性

*有关详细信息，请参阅 [Azure 安全基线：终结点安全性](/azure/security/benchmarks/security-controls-v2-endpoint-security)。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用终结点检测和响应 (EDR)

**指南**： Azure Stack Edge 不支持直接) 的终结点检测和响应 (。 但是，你可以收集支持包并检索审核日志。 然后，可以对这些日志进行分析，检查是否有异常活动。 

- [收集 Azure Stack Edge 设备的支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2：使用集中管理的新式反恶意软件

**指南**： Azure Stack Edge 使用 Windows Defender 应用程序控制 (WDAC) ，并 (CI) 仅允许运行预定义的应用程序和脚本的策略。 Windows Defender 实时保护 (RTP) 反恶意软件。 客户可以选择在其创建的计算 Vm 中运行反恶意软件，以在 Azure Stack Edge 设备上本地运行。

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="backup-and-recovery"></a>备份和恢复

有关详细信息，请参阅 [Azure 安全基准：备份和恢复](/azure/security/benchmarks/security-controls-v2-backup-recovery)。

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1：确保定期执行自动备份

**指南**：建议定期备份 Azure Stack Edge 设备，并使用 Azure 备份和其他第三方数据保护解决方案来保护设备上部署的虚拟机中包含的数据。 

第三方数据保护解决方案（例如，Cohesity、Commvault 和 Veritas）还可以为本地 SMB 或 NFS 共享中的数据提供备份解决方案。 

访问所引用的链接可获得更多信息。

- [准备设备故障](azure-stack-edge-gpu-prepare-device-failure.md)

- [保护 Vm 上的文件和文件夹](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="br-2-encrypt-backup-data"></a>BR-2：加密备份数据

**指南**：确保你的备份受到保护，不受攻击。 这应包括对备份进行加密，以防止丧失机密性。  有关详细信息，请参阅选择的备份解决方案以获取详细信息。

- [保护边缘本地共享中的数据](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [保护虚拟机中的文件和文件夹](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：验证所有备份，包括客户管理的密钥

**指南**：定期执行备份的数据还原。 

- [Azure Stack Edge 的恢复过程](azure-stack-edge-gpu-recover-device-failure.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：减少密钥丢失风险

**指南**：确保所有 Azure Stack 的边缘备份，包括根据组织的最佳实践保护任何客户管理的密钥。 Azure Stack 边缘设备与 Azure 存储帐户相关联，用作 Azure 中数据的目标存储库。 

Azure 存储帐户的访问权限由 Azure 订阅以及与该存储帐户关联的 2 512 位存储访问密钥控制。 当 Azure Stack Edge 设备访问存储帐户时，将使用其中一个访问密钥进行身份验证。 其他密钥保留以供定期密钥轮换。 确保将安全最佳做法用于密钥轮换。

- [保护 Azure 存储帐户中 Azure Stack Edge 设备数据](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略 

**指导**：确保为系统和数据的持续监视和保护记录并传达明确的策略。 确定业务关键数据和系统的发现、评估、保护和监视优先级。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   与业务风险相符的数据分类标准

-   安全组织对风险和资产清单的洞察力 

-   安全组织对 Azure 服务使用的审批 

-   资产在其生命周期中的安全性

-   与组织数据分类相符的必需访问控制策略

-   使用 Azure 原生和第三方数据保护功能

-   传输中数据用例和静态数据用例的数据加密要求

-   合适的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议 - 存储、数据和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全基准 - 资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure 安全基准 - 数据保护](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指导**：建立企业范围的策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合来细分对资产的访问。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指导**：持续衡量并缓解你的个人资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指导**：确保为安全组织中的角色和责任记录并传达明确的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全历程培训团队](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

**指导**：建立 Azure 网络安全方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的网络管理和安全职责

-   符合企业分段策略的虚拟网络分段模型

-   各种威胁和攻击场景中的补救策略

-   Internet 边缘及入口和出口策略

-   混合云和本地互连策略

-   最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：
- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](/azure/security/benchmarks/)

- [Azure 网络安全概述](../security/fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

**指导**：建立 Azure 标识和特权访问方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的标识和身份验证系统及其与其他内部和外部标识系统的互连

-   各种用例和条件中的强身份验证方法

-   保护权限高的用户

-   异常用户活动监视和处理  

-   用户标识和访问评审及协调流程

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure 安全基准 - 特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指导**：建立日志记录和威胁响应策略，以快速检测和修正威胁，同时满足合规性要求。 优先为分析人员提供高质量警报和无缝体验，以便他们能够专注于威胁而不是集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   安全运营 (SecOps) 组织的角色和职责 

-   符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

-   日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

-   使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

-   与客户、供应商和公开的利益相关方之间的通信和通知计划

-   使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

-   处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure 安全基准 - 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
