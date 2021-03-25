---
title: 适用于 Azure Stack Edge 的 Azure 安全基线
description: Azure Stack Edge 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指导和资源。
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 460fd66ed4651248639334caa55eb8facbce866d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452367"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>适用于 Azure Stack Edge 的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指导应用于 Microsoft Azure Stack Edge。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控制”分组，这些控制按适用于 Azure Stack Edge 的 Azure 安全基准和相关的指导定义。 排除了不适用于 Azure Stack Edge 的“控制”。

若要查看 Azure Stack Edge 如何完全映射到 Azure 安全基准，请参阅[完整的 Azure Stack Edge 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-controls-v2-network-security.md)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

**指导**：客户将 Microsoft 提供的物理 Azure Stack Edge 设备部署到其专用网络以用于内部访问，并且可以使用相应的选项来对该设备进行进一步的保护。 例如，Azure Stack Edge 设备可供通过客户的内部网络访问，并且需要客户配置的 IP。 此外，客户还可以选择一个访问密码来访问设备的用户界面。 

可以通过以下方式进一步保护内部流量：

- 通过 Azure 门户和 SDK 管理 Azure Stack Edge 设备需要使用传输层安全性 (TLS) 版本 1.2。

- 任何客户端对该设备的访问都是使用标准 TLS 1.2 作为默认安全协议通过本地 Web UI 进行的。

- 仅允许经授权的 Azure Stack Edge Pro 设备加入客户在其 Azure 订阅中创建的 Azure Stack Edge 服务。

访问所引用的链接可获得更多信息。
 
- [在访问 Azure Stack Edge Pro GPU 设备的 Windows 客户端上配置 TLS 1.2](azure-stack-edge-j-series-configure-tls-settings.md)

- [快速入门 - 开始使用 Azure Stack Edge Pro with GPU](azure-stack-edge-gpu-quickstart.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

**指导**：客户可以选择一个点到站点虚拟专用网络 (VPN) 来将 Azure Stack Edge 设备从其本地专用网络连接到 Azure 网络。 在客户设备到 Azure 的传输层安全性的基础之上，VPN 为移动中的数据提供了又一层加密。 

客户可以通过 Azure 门户或 Azure PowerShell 在其 Azure Stack Edge 设备上配置虚拟专用网络。

- [通过 Azure PowerShell 脚本为 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 配置 Azure VPN](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [在访问 Azure Stack Edge Pro GPU 设备的 Windows 客户端上配置 TLS 1.2](azure-stack-edge-j-series-configure-tls-settings.md)

- [教程：为 Azure Stack Edge Pro R 配置证书](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立对 Azure 服务的专用网络访问

**指导**：客户可以选择一个点到站点虚拟专用网络 (VPN) 来将 Azure Stack Edge 设备从其本地专用网络连接到 Azure 网络。 在客户设备到 Azure 的传输层安全性的基础之上，VPN 为移动中的数据提供了又一层加密。 

- [通过 Azure PowerShell 脚本为 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 配置 Azure VPN](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [在访问 Azure Stack Edge Pro GPU 设备的 Windows 客户端上配置 TLS 1.2](azure-stack-edge-j-series-configure-tls-settings.md)

- [教程：为 Azure Stack Edge Pro R 配置证书](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保护应用程序和服务不受外部网络攻击

**指导**：Azure Stack Edge 设备引入了标准 Windows Server 网络保护功能，客户无法对这些功能进行配置。

客户可以选择使用网络虚拟设备（例如，提供高级分布式拒绝服务 (DDoS) 保护的防火墙）来保护与 Azure Stack Edge 设备连接的专用网络，使其免受外部攻击。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (IDS/IPS)

**指导**：Azure Stack Edge 设备使用的终结点均由 Microsoft 管理。 客户负责实施他们想要部署到其本地系统的任何其他控制。

Azure Stack Edge 设备使用其自己的入侵检测功能来保护服务。 

- [了解 Azure Stack Edge 安全性](azure-stack-edge-security.md)

- [Azure Stack Edge 的端口信息](azure-stack-edge-gpu-system-requirements.md)

- [获取硬件和软件入侵检测日志](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](../security/benchmarks/security-controls-v2-identity-management.md)。

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指导**：所有 Azure Stack Edge 设备在 Azure Active Directory (Azure AD) 中自动具有系统分配的托管标识。 目前，托管标识用于 Azure Stack Edge 上承载的虚拟机的云管理。

Azure Stack Edge 设备将启动到锁定状态以进行本地访问。 对于本地设备管理员帐户，你将需要通过本地 Web 用户界面或 PowerShell 界面连接到你的设备，并设置一个强密码。 请在一个安全的位置（例如 Azure 密钥保管库）中存储和管理你的设备管理员凭据，并根据你的组织标准轮换管理员密码。

- [通过密码保护 Azure Stack Edge 设备](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 进行应用程序访问

**指导**：Azure Stack Edge 终结点设备不支持单一登录。 但是，你可以选择启用标准的基于 Azure Active Directory (Azure AD) 的单一登录来保护对 Azure 云资源的访问。

- [了解使用 Azure AD 的应用程序 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指导**：多重身份验证是一种强大的身份验证控制，但对于 Azure Stack Edge 服务用户而言，这是一项选择加入功能。 它可用于受支持的场景，例如，在 Azure 门户中对 Azure Stack Edge 设备进行边缘管理。 请注意，对 Azure Stack Edge 设备的本地访问不支持多重身份验证。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指导**：启用 Azure Monitor 以收集 Azure Stack Edge 服务的设备或容器日志。 对容器（例如，在设备上的 Kubernetes 群集中运行多个计算应用程序的容器）进行监视。

此外，还可以在 Azure Stack Edge 设备的本地 Web 用户界面中收集包含审核日志的支持包。 请注意，Azure 门户上不提供该支持包。
 
- [在 Azure Stack Edge Pro 设备上启用 Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指导**：Azure Active Directory (Azure AD) 条件访问是用于向 Azure Stack Edge 服务进行身份验证的一项选择加入功能。 Azure Stack Edge 服务是 Azure 门户中的一种资源，可用于从不同的地理位置管理 Azure Stack Edge Pro 设备。 

- [什么是条件访问](../active-directory/conditional-access/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据透露

**指导**：遵循最佳做法来保护凭据，例如：

- 用于在 Azure 中激活包含 Azure Stack Edge 资源的设备的激活密钥。
- 用于访问 Azure Stack Edge 设备的登录凭据。
- 有助于恢复 Azure Stack Edge 设备的密钥文件。
- 通道加密密钥

定期轮换并同步你的存储帐户密钥，以帮助保护你的存储帐户，使未经授权的用户无法访问它。

- [Azure Stack Edge Pro 安全性和数据保护](azure-stack-edge-security.md)

- [同步存储密钥](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](../security/benchmarks/security-controls-v2-privileged-access.md)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指导**：Azure Stack Edge 解决方案的多个组件包含强访问控制，可用于限制对业务关键设备的访问。 你的组织将需要使用一个企业协议 (EA) 或云解决方案提供商 (CSP) 或 Microsoft Azure 赞助订阅来配置和管理设备： 

作为 Azure 中承载的一项管理服务，Azure Stack Edge 服务受 Azure 安全功能保护。 对于任何软件开发工具包管理操作，你都可以在设备属性中获取资源的加密密钥，但只有当你对 Resource Graph API 具有适当的权限时，才能查看加密密钥。

Azure Stack Edge Pro 设备是一种本地设备，它在本地处理数据，然后将其发送到 Azure，从而帮助你转换数据。 你的设备：

- 需要使用激活密钥来访问 Azure Stack Edge 服务。
- 始终受设备密码保护。
- 已启用了安全启动。

- 是一个锁定的设备。 设备基板管理控制器 (BMC) 和 BIOS 受密码保护。 BIOS 受保护，仅可供有限的用户访问。
- 运行 Windows Defender Device Guard。 使用 Device Guard，你可以仅运行你在代码完整性策略中定义的受信任应用程序。

遵循最佳做法来保护用于访问 Azure Stack Edge 的所有凭据和机密。 

- [密码最佳做法](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指导**：Azure Stack Edge 有一个名为“EdgeUser”的用户，该用户可以对设备进行配置。 它还具有 Azure 资源管理器用户“EdgeArmUser”，用于在设备上执行本地 Azure 资源管理器功能。 

应当遵循最佳做法来保护：

- 用于访问本地设备的凭据

- SMB 共享凭据。

- 对已配置为使用 NFS 共享的客户端系统的访问。

- 使用 Blob REST API 时用于访问本地存储帐户的本地存储帐户密钥。

可以访问所引用的链接来获得更多信息。

- [有关 Azure Stack Edge 设备安全性的信息](azure-stack-edge-security.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指导**：安全的独立工作站对于确保敏感角色（如管理员、开发人员和关键服务操作员）的安全至关重要。 请使用受到严密保护的用户工作站（带或不带 Azure Bastion）来执行管理任务。 使用 Azure Active Directory (Azure AD)、Microsoft Defender 高级威胁防护 (ATP) 和 Microsoft Intune 来部署安全的托管用户工作站，以便执行管理任务。 

可通过集中管理安全的工作站来强制实施安全配置，包括强身份验证、软件和硬件基线、受限的逻辑和网络访问。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [部署特权访问工作站](/security/compass/privileged-access-deployment)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-controls-v2-data-protection.md)。

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指导**：Azure Stack Edge 将所有互动数据都视为敏感数据，只允许经授权的用户访问该数据。 你应当遵循最佳做法来保护用于访问 Azure Stack Edge 服务的凭据。

- [Azure Stack Edge Pro 安全性和数据保护](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

**指导**：Azure Stack Edge 为传输中的数据使用安全通道。 其中包括：

- 标准 TLS 1.2，用于在设备与 Azure 云之间传输的数据。 不能回退到 TLS 1.1 和更早版本。 如果不支持 TLS 1.2，则代理通信将被阻止。 Azure 门户和软件开发工具包 (SDK) 管理也需要 TLS 1.2。

- 当客户端通过浏览器的本地 Web 用户界面访问设备时，将使用标准 TLS 1.2 作为默认安全协议

最佳做法是将浏览器配置为使用 TLS 1.2。 从数据服务器复制数据时，请使用具有加密功能的 SMB 3.0 来保护数据。

- [保护传输中数据的最佳做法](azure-stack-edge-security.md#protect-data-in-flight)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](../security/benchmarks/security-controls-v2-asset-management.md)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意，若要查看工作负载和服务的相关信息，可能需要更多权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指导**：只有经授权的用户（例如“EdgeArmUser”）才能通过本地 Azure 资源管理器访问 Azure Stack Edge 设备 API。 只能在 Azure 门户中管理用户帐户密码。 

- [设置 Azure 资源管理器密码](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6：仅使用计算资源中经过批准的应用程序

**指导**：你可以在任何在本地创建的虚拟机上运行你自己的应用程序。 使用 PowerShell 脚本在 Stack Edge 设备上创建本地计算虚拟机。 强烈建议你仅引入要在本地虚拟机上运行的受信任应用程序。 

- [如何在 Windows 环境中控制 PowerShell 脚本的执行](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](../security/benchmarks/security-controls-v2-logging-threat-detection.md)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

**指导**：Azure Stack Edge 未提供威胁检测功能。 但是，客户可以在一个支持包中收集审核日志，以便进行脱机威胁检测和分析。 

- [收集 Azure Stack Edge 设备的支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指导**：Azure Stack Edge 已将网络审核日志作为可下载支持包的一部分启用。 可以分析这些日志，以针对你的 Azure Stack Edge 设备实施半实时监视。

- [Azure Stack Edge 会收集一个支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指导**：目前不支持通过日志实时监视 Azure Stack Edge。 有一项功能可用来收集支持包，让你能够分析其中包含的各种日志（例如 Azure Stack Edge Pro 设备的防火墙、软件、硬件入侵和系统事件日志）。 请注意，会针对入站和出站流量收集软件入侵或默认防火墙日志。

- [为 Azure Stack Edge 收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：目前不支持通过日志实时监视 Azure Stack Edge。 不过，你可以收集一个支持包，以便分析其中包含的各种日志。  该支持包是压缩的，并会下载到你选择的路径。 可以解压缩该包并查看其中包含的系统日志文件。 你还可以将这些日志发送到安全信息事件管理工具或其他中央存储位置以进行分析。

- [为 Azure Stack Edge 收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指导**：无法在 Azure Stack Edge 设备上更改日志存储保留期。 请根据需要清除较旧的日志。 你可以定期从设备收集支持包，以满足将日志保留更长时间的任何要求。 

- [为 Azure Stack Edge 收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7：使用批准的时间同步源

**指导**：Azure Stack Edge 使用 time.windows.com，这是 Microsoft 提供的网络时间提供程序服务器。  Azure Stack Edge 还允许客户配置他们选择的网络时间协议服务器。

- [配置 Azure Stack Edge 设备时间设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-controls-v2-incident-response.md)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指导**：确保你的组织的事件响应计划包含具有以下用途或特征的流程： 

- 用来响应安全事件

- 已针对 Azure 云进行了更新
 
- 定期演练，以确保准备就绪

建议在整个企业环境中使用标准化的事件响应流程来实现安全性。

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

**指导**：确保制定一个流程来创建高质量警报并衡量警报质量。 这样，便可以从过去的事件中吸取经验并为分析人员设定警报优先级，防止浪费时间来处理误报的警报。 根据你从过去事件中获得的经验、经过验证的社区来源以及专用于通过日志记录和关联为各种警报源生成和清除警报的工具，来构建高质量的警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可使用安全中心数据连接器将警报流式传输到 Azure Sentinel。 使用 Azure Sentinel 创建高级警报规则，以生成自动事件供调查。 

使用导出功能将安全中心警报和建议导出到 Azure Sentinel 中，以帮助识别 Azure 资源的风险。 建议你创建一个流程，以自动化方式导出警报和建议，从而实现持续的安全性。

- [如何配置导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

**指导**：确保分析人员可以查询和使用各种数据源，以生成在调查潜在事件时所发生的活动的完整视图。 应收集各种各样的日志，以跟踪整个攻击链中潜在攻击者的活动，避免出现盲点。  另外，请确保捕获见解和知识以用作历史参考。  

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据 - 使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 选择操作系统的原生内存转储功能，以创建正在运行的系统的内存快照。

    - 使用 Azure 服务的快照功能或第三方软件功能来创建正在运行的系统的快照。

Azure Sentinel 提供几乎针对任何日志源的广泛数据分析，并提供一个事例管理门户来管理事件的整个生命周期。 调查过程中的情报信息可与事件相关联，以便进行跟踪和报告。 

- [Windows 计算机的磁盘快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 调查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 使用安全中心为每个警报分配的严重性，可以帮助你确定应当首先调查哪些警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的可信度，以及对导致警报的活动背后存在恶意意图的可信度级别。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

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

有关详细信息，请参阅 [Azure 安全基准：安全状况和漏洞管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)。

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：为计算资源建立安全配置

**指导**：Azure Stack Edge 支持为客户创建的本地虚拟机创建安全配置。 强烈建议在创建本地虚拟机时使用 Microsoft 提供的准则来建立安全基线。

- [下载安全合规性工具包中包含的安全基线](/windows/security/threat-protection/windows-security-baselines)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4：为计算资源维护安全配置

**指导**：Azure Stack Edge 不支持维护客户已创建的本地虚拟机的安全配置。 强烈建议客户使用安全合规性工具包 (SCT) 来帮助维护其计算资源的安全配置。

Azure Stack Edge 管理的主机操作系统和虚拟机会维护其自己的安全配置。 

- [Windows 安全基线](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5：安全存储自定义操作系统和容器映像

**指导**：Azure Stack Edge 管理的主机操作系统和虚拟机会安全地存储。 

客户可以引入自己的虚拟机和容器映像，并负责安全地对其进行管理。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7：快速自动修正软件漏洞

**指导**：Azure Stack Edge 会定期提供修补程序更新，并在这些更新可用于修补漏洞时提醒客户。 客户负责通过最新的修补程序使其设备和虚拟机（由客户创建）保持最新。

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

*有关详细信息，请参阅 [Azure 安全基线：终结点安全性](../security/benchmarks/security-controls-v2-endpoint-security.md)。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用终结点检测和响应 (EDR)

**指导**：Azure Stack Edge 不能直接支持终结点检测和响应 (EDR)。 但是，你可以收集一个支持包并检索审核日志。 然后，可以对这些日志进行分析，以检查是否存在异常活动。 

- [收集 Azure Stack Edge 设备的支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2：使用集中管理的新式反恶意软件

**指导**：Azure Stack Edge 将 Windows Defender 应用程序控制 (WDAC) 和一个严格的代码完整性 (CI) 策略配合使用，以仅允许预定义的应用程序和脚本运行。 它还启用了 Windows Defender 实时保护 (RTP) 反恶意软件。 客户可以选择在其创建的用于在 Azure Stack Edge 设备本地运行的计算 VM 中运行反恶意软件。

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="backup-and-recovery"></a>备份和恢复

有关详细信息，请参阅 [Azure 安全基准：备份和恢复](../security/benchmarks/security-controls-v2-backup-recovery.md)。

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1：确保定期执行自动备份

**指导**：建议定期备份 Azure Stack Edge 设备，并且可以使用 Azure 备份和其他第三方数据保护解决方案来执行此备份，以保护设备上部署的虚拟机中包含的数据。 

第三方数据保护解决方案（例如，Cohesity、Commvault 和 Veritas）还可以为本地 SMB 或 NFS 共享中的数据提供备份解决方案。 

访问所引用的链接可获得更多信息。

- [为应对设备故障做好准备](azure-stack-edge-gpu-prepare-device-failure.md)

- [保护 VM 上的文件和文件夹](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="br-2-encrypt-backup-data"></a>BR-2：加密备份数据

**指导**：确保备份受到保护，免遭攻击。 这应包括对备份进行加密，以防止丧失机密性。  有关详细信息，请参阅你选择的备份解决方案。

- [保护 Edge 本地共享中的数据](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [保护虚拟机中的文件和文件夹](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：验证所有备份，包括客户管理的密钥

**指导**：定期对你的备份执行数据还原。 

- [Azure Stack Edge 的恢复过程](azure-stack-edge-gpu-recover-device-failure.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：减少密钥丢失风险

**指导**：确保遵循组织的最佳做法来保护所有 Azure Stack Edge 备份，包括客户管理的任何密钥。 Azure Stack Edge 设备与一个 Azure 存储帐户相关联，该帐户用作 Azure 中数据的目标存储库。 

对该 Azure 存储帐户的访问权限由与该存储帐户关联的 Azure 订阅和两个 512 位存储访问密钥控制。 Azure Stack Edge 设备访问存储帐户时，将使用其中的一个访问密钥进行身份验证。 另一个密钥将保留，以用于定期密钥轮换。 请确保使用安全性最佳做法进行密钥轮换。

- [保护 Azure 存储帐户中的 Azure Stack Edge 设备数据](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](../security/benchmarks/security-controls-v2-governance-strategy.md)。

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
- [Azure 安全体系结构建议 - 存储、数据和加密](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure 安全基准 - 资产管理](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure 安全基准 - 数据保护](../security/benchmarks/security-controls-v2-data-protection.md)

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

- [Azure 安全基准 - 状况和漏洞管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

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

- [Azure 安全基准 - 网络安全](../security/benchmarks/index.yml)

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

- [Azure 安全基准 - 标识管理](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure 安全基准 - 特权访问](../security/benchmarks/security-controls-v2-privileged-access.md)

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

- [Azure 安全基准 - 日志记录和威胁检测](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure 安全基准 - 事件响应](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)
