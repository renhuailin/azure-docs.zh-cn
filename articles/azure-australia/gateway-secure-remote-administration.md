---
title: 在 Azure 澳大利亚实现网关安全远程管理
description: 有关在澳大利亚区域根据澳大利亚政府的具体政策、法规和法律要求配置安全远程管理的指导。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 169ce346fa9023b43e46c92f010ea24630c4e63d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "117029186"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>在 Azure 澳大利亚实现网关的安全远程管理

安全可控地进行管理活动对于任何系统的可用性和完整性来说至关重要。 应使用安全连接在安全的设备中进行管理活动，并由强身份验证和授权过程作为支持。 安全远程管理可确保只有经授权的管理员可执行授权操作。

本文介绍了如何为 Azure 中托管的 Internet 可访问的系统实现安全远程管理功能，此类系统符合澳大利亚网络安全中心 (ACSC) 使用者指南和 ACSC 的信息安全手册 (ISM) 的意图。

## <a name="australian-cyber-security-centre-acsc-requirements"></a>澳大利亚网络安全中心 (ACSC) 要求

ISM 中定义了英联邦系统的整体安全要求。 为帮助英联邦实体提供安全管理，ACSC 已发布 [ACSC 保护：安全管理](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

本文档介绍了安全管理的重要性，并建议一种实现安全管理环境的方法。 本文档介绍了安全管理解决方案的元素，如下所示：

|元素   |说明   |
|---|---|
|特权访问控制   |控制对特权帐户的访问是一种基本的安全控制，可保护特权帐户不被滥用。 访问控制方法将包括“最小特权”和“需要拥有”的概念，以及管理服务帐户和人员变动的过程和程序。   |
|多重身份验证   |在用户名和密码之外实施附加的身份验证因素（如物理令牌或智能卡），可以帮助保护关键资产。 如果对手入侵了特权帐户的凭据，由于所有管理操作都需要首先经过某种形式的多重身份验证，可大大减少后果。|
|特权工作站|使用已知的安全环境来执行管理任务，由于实施了额外的安全控制，可以降低网络遭入侵的风险。|
|日志记录和审核   |自动生成、收集和分析来自工作站、服务器、网络设备和跳转盒的安全与管理相关事件，将能够检测到入侵和试图入侵的行为。 自动化使组织能够更迅速地做出响应，减少入侵的影响。|
|网络分段和分离|将网络划分为多个逻辑区域（如不同的安全域），并通过限制可以从一个区域流向另一个区域的数据类型来进一步分离这些逻辑网络，从而限制横向移动。 分段可防止对手获得对其他资源的访问权限。|
|跳转盒|跳转盒是强化的远程访问服务器，通常利用 Microsoft 远程桌面服务或安全外壳 (SSH) 软件。 跳转盒作为管理员访问关键系统的跳板，在专用主机上执行所有管理操作。|

本文提供了一个参考体系结构，说明如何利用上述元素对部署在 Azure 中的系统进行安全管理。

## <a name="architecture"></a>体系结构

要提供安全管理功能，需要多个组件协同工作，形成一套完整的解决方案。 在此处提供的参考体系结构中，这些组件映射到 [ACSC 保护：安全管理](https://www.acsc.gov.au/publications/protect/secure-administration.htm)中所述的元素

![Azure 安全远程管理体系结构](media/remote-admin.png)

## <a name="components"></a>组件

该体系结构旨在确保仅为特权帐户授予必要的权限，并进行安全地标识，然后只允许通过授权设备以及使用受控且经过审核的安全通信机制来访问管理界面。

|解决方案| 组件|元素|
|---|---|---|
|安全设备 |<ul><li>特权工作站</li><li>移动设备</li><li>Microsoft Intune</li><li>组策略</li><li>跳转服务器/堡垒主机</li><li>实时 (JIT) 管理</li></ul> |<ul><li>特权工作站</li><li>跳转盒</li></ul>|
|安全通信 |<ul><li>Azure 门户</li><li>Azure VPN 网关</li><li>远程桌面 (RD) 网关</li><li>网络安全组 (NSG)</li></ul> |<ul><li>网络分段和分离</li></ul>|
|强身份验证 |<ul><li>域控制器 (DC)</li><li>Azure Active Directory (Azure AD)</li><li>网络策略服务器 (NPS)</li><li>Azure AD MFA</li></ul> |<ul><li>多重身份验证</li></ul> |
|强授权 |<ul><li>Identity and Access Management (IAM)</li><li>Privileged Identity Management (PIM)</li><li>条件性访问</li></ul>|<ul><li>特权访问控制</li></ul>|
|||

>[!NOTE]
>有关日志记录和审核元素的详细信息，请参阅关于[网关日志记录、审核和可见性](gateway-log-audit-visibility.md)的文章

## <a name="administration-workflow"></a>管理工作流

管理部署在 Azure 的系统分为两个不同类别：管理 Azure 配置和管理部署在 Azure 的工作负载。 Azure 配置是通过 Azure 门户进行的，工作负载管理是通过远程桌面协议 (RDP)、安全外壳 (SSH) 等管理机制完成的。对于 PaaS 功能，则使用 SQL Management Studio 等工具。

获取管理权限的过程包含多个步骤，涉及该体系结构中所列的组件，并且在获取 Azure 工作负载的访问权限之前，需要先获取 Azure 门户和 Azure 配置的访问权限。

>[!NOTE]
> 此处所述的步骤是使用 Azure 的图形用户界面 (GUI) 组件的一般过程。 还可以使用其他界面（如 PowerShell）完成这些步骤。

### <a name="azure-configuration-and-azure-portal-access"></a>Azure 配置和 Azure 门户访问

|步骤 |说明 |
|---|---|
|特权工作站登录 |管理员使用管理凭据登录特权工作站。 组策略控件可防止非管理帐户向特权工作站进行身份验证，并防止管理帐户向非特权工作站进行身份验证。 Microsoft Intune 管理特权工作站的符合性，以确保其安装了最新的软件补丁、反恶意软件并满足其他符合性要求。 |
|Azure 门户登录 |管理员打开 Web 浏览器进入 Azure 门户，Azure 门户使用传输层安全性 (TLS) 进行加密，并使用管理凭据登录。 身份验证请求直接通过 Azure Active Directory 进行处理，或者通过 Active Directory 联合身份验证服务 (AD FS) 或直通身份验证进行处理。 |
|Azure AD MFA |Azure AD MFA 向特权帐户的注册移动设备发送身份验证请求。 此类移动设备由 Intune 管理，以确保符合安全要求。 管理员必须先对移动设备进行身份验证，然后使用 PIN 或生物识别系统对 Microsoft Authenticator 应用进行身份验证，最后才能向 Azure AD MFA 授权身份验证尝试。 |
|条件性访问 |条件访问策略检查身份验证尝试，以确保其符合必需的要求，如连接来源的 IP 地址、特权帐户的组成员身份，以及 Intune 报告的特权工作站的管理和符合性状态。 |
|Privileged Identity Management (PIM) |管理员现在可以通过 Azure 门户激活或请求激活其通过 PIM 获取授权的特权角色。 PIM 确保特权帐户不具有任何长期的管理权限，并且所有特权访问请求只限于执行管理任务所需的时间。 PIM 还为审核目的提供所有请求和激活的记录。 |
|标识和访问管理|安全标识特权帐户并激活角色后，管理员便可以访问通过标识和访问管理为其分配权限的 Azure 订阅和资源。|

特权帐户完成获取 Azure 门户管理访问权限的步骤后，便可以配置对工作负载的访问并进行管理连接。

### <a name="azure-workload-administration"></a>Azure 工作负载管理

|步骤 |说明|
|---|---|
|实时 (JIT) 访问|为了获取对虚拟机的访问权限，管理员使用 JIT 请求 RDP 访问权限，以便从 RD 网关 IP 地址访问跳转服务器，并通过 RDP 或 SSH 从跳转服务器访问相关的工作负载虚拟机。|
|Azure VPN 网关|管理员现在从其特权工作站到 Azure VPN 网关建立点到站点 IPSec VPN 连接，Azure VPN 网关执行证书身份验证以建立连接。|
|RD 网关|管理员现在尝试用远程桌面连接配置中指定的 RD 网关与跳转服务器进行 RDP 连接。 RD 网关具有专用 IP 地址，可通过 Azure VPN 网关连接访问。 RD 网关上的策略控制特权帐户是否有权访问请求的跳转服务器。 RD 网关提示管理员提供凭据，并将身份验证请求转发给网络策略服务器 (NPS)。|
|网络策略服务器 (NPS)|NPS 接收来自 RD 网关的身份验证请求，并使用 Active Directory 验证用户名和密码，然后向 Azure Active Directory 发送请求，触发 Azure AD MFA 身份验证请求。|
|Azure AD MFA|Azure AD MFA 向特权帐户的注册移动设备发送身份验证请求。 此类移动设备由 Intune 管理，以确保符合安全要求。 管理员必须先对移动设备进行身份验证，然后使用 PIN 或生物识别系统对 Microsoft Authenticator 应用进行身份验证，最后才能向 Azure AD MFA 授权身份验证尝试。|
|跳转服务器|成功完成身份验证后，RDP 连接便会使用传输层安全性 (TLS) 进行加密，然后通过加密的 IPSec 隧道发送至 Azure VPN 网关，通过 RD 网关，再到跳转服务器。 现在，管理员可以通过 RDP 或 SSH 从跳转服务器访问 JIT 请求中指定的工作负载虚拟机。|

## <a name="general-guidance"></a>一般指南

在实施本文所列的组件时，适用下列一般指南：

* 验证服务的区域可用性，确保所有数据保留在授权位置，并部署到澳大利亚中部或澳大利亚中部 2 区域，作为受保护工作负载的首选项

* 请参阅发布的“Azure - ACSC 认证报告（2018 年，受保护）”，了解各个服务的认证状态，并根据“ACSC 使用者指南 - Microsoft Azure（受保护）”，对报告中未包含的任何组件进行自我评估

* 确保网络连接和任何必要的代理配置，以访问 Azure AD、ADFS 和 PTA 等必要的身份验证组件

* 使用 Azure Policy 来监视并执行符合性要求

* 确保虚拟机（尤其是 Active Directory 域控制器）存储在加密的存储帐户中，并使用 Azure 磁盘加密

* 创建并维护可靠的标识和管理特权管理过程与治理，以支持本文所列的技术控制措施

|资源|URL|
|---|---|
|澳大利亚法规和政策合规性文档|[澳大利亚法规和政策合规性文档](https://aka.ms/au-irap)|
|Azure 产品 - 澳大利亚区域性和非区域性|[Azure 产品 - 澳大利亚区域性和非区域性](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|缓解网络安全事件的策略|[缓解网络安全事件的策略](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC 保护：安全管理|[ACSC 保护：安全管理](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|操作指南：使用网络策略服务器 (NPS) 扩展和 Azure AD 集成远程桌面网关基础结构|[将 RD 网关与 NPS 和 Azure AD 集成](../active-directory/authentication/howto-mfa-nps-extension-rdg.md)|

## <a name="component-guidance"></a>组件指南

本部分介绍每个组件的用途及其在整个安全远程管理体系结构中发挥的作用。 此外，还提供了用于访问参考文档、指南和教程等有用资源的其他链接。

## <a name="secure-devices"></a>保护设备

特权用户用于执行管理功能的物理设备是恶意行动者的重要目标。 维护物理设备的安全性和完整性，并确保其免受恶意软件攻击并免遭入侵，是提供安全远程管理功能的关键部分。 这涉及到 ACSC 的“缓解网络安全事件的八大基本策略”中指定的高优先级安全配置，如应用筛选、修补应用、应用强化和修补操作系统。 必须安装、配置、审核、验证并报告这些功能，以确保设备状态符合组织要求。

### <a name="privileged-workstation"></a>特权工作站

特权工作站是经过强化的计算机，可用于执行管理职责，并且只有管理帐户才能访问。 特权工作站应具有相应的策略和配置，以限制可运行的软件，并限制其对网络资源和 Internet 的访问。此外，在设备被盗或遭入侵的情况下，凭据应受到保护。

|资源|链接|
|---|---|
|特权访问工作站体系结构概述|[https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/](/security/compass/privileged-access-deployment)|
|保护特权访问参考资料|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>移动设备

由于移动设备的便携性和大小，其意外丢失或被盗的风险更高，因此需要妥善保护。 移动设备提供强大的附加身份验证因素，因为其能够针对设备访问强制执行身份验证，通过位置服务实现可跟踪性，具有加密功能，并支持远程擦除。 当使用移动设备作为 Azure 的附加身份验证因素时，应将设备配置为使用支持 PIN 或生物识别身份验的 Microsoft Authenticator 应用，而不是通过电话呼叫或短信进行身份验证。

|资源|链接|
|---|---|
|Azure AD 身份验证方法|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](../active-directory/authentication/concept-authentication-methods.md)|
|如何使用 Microsoft Authenticator 应用|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune 是企业移动性 + 安全性的组件，可用于管理移动设备和应用。 它与其他组件紧密集成，比如与 Azure Active Directory 集成以实现标识和访问控制，与 Azure 信息保护集成以实现数据保护。 Intune 为工作站和移动设备提供策略，以设置访问资源的符合性要求，并提供报告和审核功能，以深入了解管理设备的状态。

|资源|链接|
|---|---|
|Microsoft Intune 文档|[https://docs.microsoft.com/intune/](/intune/)|
|Intune 中的设备符合性入门|[https://docs.microsoft.com/intune/device-compliance-get-started](/intune/device-compliance-get-started)|

### <a name="group-policy"></a>组策略

组策略用于控制操作系统和应用的配置。 安全策略控制系统的身份验证、授权和审核设置。 组策略用于强化特权工作站、保护管理凭据，并限制非特权帐户访问特权设备。

|资源|链接|
|---|---|
|允许本地登录的组策略设置|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>跳转服务器/堡垒主机

跳转服务器/堡垒主机是一种集中管理点。 它拥有履行管理职责所需的工具，还拥有连接管理端口上的资源所需的网络访问权限。 在本文中，跳转服务器是管理虚拟机工作负载的中心点，但也可以配置为管理平台即服务 (PaaS) 功能（如 SQL）的授权点。 可以使用标识和网络控制，根据服务限制对 PaaS 功能的访问。

|资源|链接|
|---|---|
|实现安全管理主机|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>实时 (JIT) 访问

JIT 是一种 Azure 安全中心功能，利用网络安全组 (NSG) 来阻止对虚拟机上 RDP 和 SSH 等管理协议的访问。 托管在虚拟机上的应用继续正常运行，但要获取管理访问权限，必须要求只能在设定的时间段授予。 所有请求都应记录下来，以进行审核。

|资源 |链接 |
|---|---|
|管理实时 (JIT) 访问|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](../security-center/security-center-just-in-time.md)|
|自动执行 Azure 实时 VM 访问|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](/archive/blogs/motiba/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>安全通信

管理活动的通信流量可能包含高度敏感的信息（如管理凭据），必须进行相应的管理和保护。 提供安全通信需要可靠的加密功能，以防止窃听和网络分段，并且需要限制，以将管理流量限制在授权的终结点，并在系统遭入侵时控制横向移动。

### <a name="azure-portal"></a>Azure 门户

与 Azure 门户的通信使用传输层安全性 (TLS) 加密，并且 Azure 门户的使用已获得 ACSC 认证。 英联邦实体应遵循“ACSC 使用者指南中的建议，配置其 Web 浏览器，以确保使用最新版本的 TLS 和支持的加密算法。

|资源 |链接 |
|---|---|
|Azure 加密概述 - 传输中加密|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Azure VPN 网关

Azure VPN 网关提供从特权工作站到 Azure 的安全加密连接。 Azure VPN 网关已获得 ACSC 认证，可提供安全的 IPSec 通信。 英联邦实体应根据 ACSC 使用者指南、ACSC 认证报告和其他具体指导配置 Azure VPN 网关。

|资源 |链接 |
|---|---|
|关于点到站点连接|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](../vpn-gateway/point-to-site-about.md)|
|Azure VPN 网关加密详细信息|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](../vpn-gateway/vpn-gateway-about-compliance-crypto.md)|
|Azure VPN 网关配置|[Azure VPN 网关配置](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>远程桌面 (RD) 网关

RD 网关是一种安全机制，用于控制和授权与系统的 RDP 连接。 其工作原理是将 RDP 流量封装在安全超文本传输协议 (HTTPS) 中并使用 TLS 进行加密。 TLS 为管理流量提供额外一层的安全保护。

|资源 |链接 |
|---|---|
|远程桌面服务体系结构|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>网络安全组 (NSG)

NSG 用作访问控制列表 (ACL)，控制进出子网或虚拟机的网络流量。 NSG 提供网络分段，并提供一种机制来控制和限制系统之间允许的通信流。 NSG 是实时 (JIT) 管理的核心组件，用于允许或拒绝对管理协议的访问。

|资源 |链接 |
|---|---|
|Azure 安全组概述|[https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|操作指南：规划虚拟网络|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md)|

## <a name="strong-authentication"></a>强身份验证

在授予系统访问权限之前安全地标识特权用户是安全管理的核心组件。 必须建立相应的机制来保护与特权帐户相关的凭据，并防止恶意行动者通过模拟或盗用凭据获取系统的访问权限。

### <a name="domain-controller-dc"></a>域控制器 (DC)

概括而言，DC 保留了 Active Directory 数据库的副本，其中包含域中的所有用户、计算机和组。 DC 为用户和计算机执行身份验证。 该体系结构中的 DC 作为托管在 Azure 中的虚拟机，并为连接到跳转服务器和工作负载虚拟机的特权帐户提供身份验证服务。

|资源 |链接 |
|---|---|
|Active Directory 域服务概述|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD 是 Azure 的身份验证服务。 它包含云

标识，为 Azure 环境提供身份验证和授权。 Azure AD 可通过 Azure AD Connect 与 Active Directory 同步，并可通过 Active Directory 联合身份验证服务 (AD FS) 和 Azure AD Connect 提供联合身份验证。 Azure AD 是安全管理的核心组件。

|资源 |链接 |
|---|---|
|Azure Active Directory 文档|[https://docs.microsoft.com/azure/active-directory](../active-directory/index.yml)|
|混合标识文档|[https://docs.microsoft.com/azure/active-directory/hybrid](../active-directory/hybrid/index.yml)|

### <a name="network-policy-server-nps"></a>网络策略服务器 (NPS)

NPS 是一种身份验证与策略服务器，提供高级身份验证和授权过程。 该体系结构中的 NPS 服务器用于将 Azure AD MFA 身份验证与 RD 网关身份验证请求集成。 NPS 具有特定的插件，支持与 Azure AD 中的 Azure AD MFA 集成。

|资源 |链接 |
|---|---|
|网络策略服务器文档|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-ad-mfa"></a>Azure AD MFA

Azure AD MFA 是 Azure Active Directory 中提供的身份验证服务，支持用户名和密码以外的身份验证请求，以访问 Azure 门户等云资源。 Azure AD MFA 支持众多身份验证方法，并且该体系结构利用 Microsoft Authenticator 应用来增强安全性，并与 NPS 集成。

|资源 |链接 |
|---|---|
|工作原理：Azure AD 多重身份验证|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](../active-directory/authentication/concept-mfa-howitworks.md)|
|操作指南：部署基于云的 Azure AD 多重身份验证|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](../active-directory/authentication/howto-mfa-getstarted.md)|

## <a name="strong-authorisation"></a>强授权

安全标识特权帐户后，便可以授予其对资源的访问权限。 实施授权控制并管理分配给特定帐户的特权。 强授权过程与 ACSC 的“缓解网络安全事件的八大基本策略”中的“限制管理权限”相一致。

### <a name="identity-and-access-management"></a>标识和访问管理

在 Azure 中执行特权操作的访问权限基于分配给该帐户的角色。 Azure 提供大量细化的角色，这些角色拥有特定权限来执行特定的任务。 可以在多个级别（如订阅或资源组）为这些角色授权。 角色分配和权限管理基于 Azure Active Directory 中的帐户和组，并通过 Azure 中的访问控制 (IAM) 进行管理。

|资源 |链接 |
|---|---|
|Azure 基于角色的访问控制 (Azure RBAC)|[https://docs.microsoft.com/azure/role-based-access-control](../role-based-access-control/index.yml)|
|了解角色定义|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](../role-based-access-control/role-definitions.md)|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM 是 Azure Active Directory 的一个组件，用于控制对特权角色的访问。 特权帐户不需要永久或长期的特权访问，而是可以授予在一个时间段内请求特权访问的能力，以便完成特权活动。 PIM 针对维护和限制特权访问以及日志记录和审核提供额外的控制，以跟踪特权使用的情况。

|资源 |链接 |
|---|---|
|Privileged Identity Management (PIM) 文档|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](../active-directory/privileged-identity-management/index.yml)|
|开始使用 PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](../active-directory/privileged-identity-management/pim-getting-started.md)|

### <a name="conditional-access"></a>条件性访问

条件访问是 Azure Active Directory 的一个组件，允许或拒绝基于条件的资源访问。 此类条件可以基于网络位置、设备类型、符合性状态、组成员身份等。 条件访问用于通过 Intune 和管理帐户的组成员身份强制实施 MFA、设备管理和符合性。

|资源 |链接 |
|---|---|
|条件访问文档|[https://docs.microsoft.com/azure/active-directory/conditional-access](../active-directory/conditional-access/index.yml)|
|操作指南：要求托管设备通过条件访问进行云应用的访问|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](../active-directory/conditional-access/require-managed-devices.md)|

## <a name="next-steps"></a>后续步骤

查看有关[网关入口流量管理和控制](gateway-ingress-traffic.md)的文章，详细了解如何控制流量流经 Azure 中的网关组件。