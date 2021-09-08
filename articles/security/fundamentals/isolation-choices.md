---
title: Azure 公有云中的隔离 | Microsoft Docs
description: 了解 Azure 如何针对恶意和非恶意用户提供隔离，并为架构师提供各种隔离选项。
services: security
documentationcenter: na
author: TomSh
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2021
ms.author: terrylan
ms.openlocfilehash: 3ef9f5cb6e0175a501b05da6e79194da76b18dae
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257243"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Azure 公有云中的隔离

可以通过 Azure 在共享的物理基础结构上运行应用程序和虚拟机 (VM)。 在云环境中运行应用程序的一个主要经济动机是可由多个客户分摊共享资源的成本。 这种多租户的做法在不同客户间多路复用资源，提高了效率并降低了成本。 遗憾的是，这种做法也带来了风险，会导致通过共享物理服务器和其他基础结构资源来运行敏感应用程序和 VM，而这些 VM 可能属于任意或潜在恶意用户。

本文概述了 Azure 以何种方式同时针对恶意和非恶意用户提供隔离，并向架构师提供了多种隔离选项，指导他们构建云解决方案。

## <a name="tenant-level-isolation"></a>租户级别隔离

云计算的一个主要优势是同时跨多位客户使用共享的通用基础结构的概念，可带来规模效益。 这种概念称为多租户。 Microsoft 始终致力于确保 Microsoft Cloud Azure 的多租户体系结构支持安全、保密性、隐私、完整性和可用性标准。

在启用云的工作区中，可以将“租户”定义为拥有并管理该云服务的特定实例的客户端或组织。 使用 Microsoft Azure 提供的标识平台，租户只是组织在注册 Microsoft 云服务时接收并拥有的 Azure Active Directory (Azure AD) 专用实例。

每个 Azure AD 目录都是独特的，独立于其他 Azure AD 目录。 就像公司办公大楼是组织特有的安全资产一样，根据设计，Azure AD 目录也是仅供组织使用的安全资产。 Azure AD 体系结构隔离了客户数据和身份信息，避免混合存放。 这意味着，一个 Azure AD 目录的用户和管理员不可能意外或恶意性地访问另一目录中的数据。

### <a name="azure-tenancy"></a>Azure 租户

Azure 租户（Azure 订阅）是指 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) 中的“客户/账单”关系和唯一的[租户](../../active-directory/develop/quickstart-create-new-tenant.md)。 Microsoft Azure 中的租户级别隔离是使用 Azure Active Directory 及其提供的 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)实现的。 每个 Azure 订阅都会与一个 Azure Active Directory (AD) 目录关联。

该目录中的用户、组和应用程序可以管理 Azure 订阅中的资源。 可以使用 Azure 门户、Azure 命令行工具及 Azure 管理 API 来分配这些访问权限。 从逻辑上讲，Azure AD 租户是使用安全边界隔离的，这样，任何客户都不能访问或入侵联合租户，而无论其行为是恶意的还是偶然的。 在“裸机”服务器上运行的 Azure AD 是在分隔的网络段中隔离的，主机级别数据包筛选和 Windows 防火墙在该网络段中阻止不需要的连接和流量。

:::image type="content" source="media/isolation-choices/azure-isolation-fig-1.svg" alt-text="显示 Azure 租户的关系图。" border="false":::

- 访问 Azure AD 中的数据需要通过安全令牌服务 (STS) 进行用户身份验证。 授权系统使用有关用户存在性、已启用状态和角色的信息，确定此用户在此会话中对目标租户的访问请求是否已获授权。

- 租户是离散的容器，它们之间没有任何关系。

- 除非租户管理员通过联合身份验证或预配来自其他租户的用户帐户授予访问权限，否则不允许跨租户访问。

- 限制了对危及 Azure AD 服务的服务器的物理访问，以及对 Azure 后端系统的直接访问。

- Azure AD 用户无权访问物理资产或位置，因此他们不可能绕过下述逻辑 Azure RBAC 策略检查。

为了满足诊断和维护需求，需要使用采用实时特权提升系统的操作模型。 Azure AD Privileged Identity Management (PIM) 引入了有资格管理员的概念。[有资格管理员](../../active-directory/privileged-identity-management/pim-configure.md)应是不时（但不是每天）需要特权访问的用户。 该角色处于非活动状态，直到用户需要访问权限，然后他们完成激活过程，并在预定的时间内成为活动管理员。

![Azure AD 特权标识管理](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory 在其自己受保护的容器中托管每个租户，使用的策略和权限针对各租户单独拥有和管理的容器，并保存在该容器内。

从门户一直到永久性存储，租户容器的概念深入贯彻于目录服务的每一层。

即使多个 Azure Active Directory 租户的元数据存储在同一个物理磁盘中，除目录服务定义的容器外，各容器之间仍没有任何关系，而目录服务是由租户管理员指定的。

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure 基于角色的访问控制 (Azure RBAC)

[Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 提供针对 Azure 的精细访问权限管理，有助于共享 Azure 订阅中提供的各种组件。 借助 Azure RBAC，可分隔组织内的职责，并根据用户进行作业的需求授予访问权限。 可以仅允许某些操作，而不是向每个人提供对 Azure 订阅或资源不受限制的权限。

Azure RBAC 有三种适用于所有资源类型的基本角色：

- **所有者** 对所有资源具有完全访问权限，包括将访问权限委派给其他用户的权限。

- **参与者** 可以创建和管理所有类型的 Azure 资源，但不能将访问权限授予其他用户。

- **读者** 可以查看现有的 Azure 资源。

![Azure 基于角色的访问控制 (Azure RBAC)](./media/isolation-choices/azure-isolation-fig3.png)

可以通过 Azure 中的其他 Azure 角色对特定的 Azure 资源进行管理。 例如，虚拟机参与者角色允许用户创建和管理虚拟机。 但不会向用户授予对虚拟机连接的 Azure 虚拟网络或子网的访问权限。

[Azure 内置角色](../../role-based-access-control/built-in-roles.md)列出了 Azure 中可用的角色。 它指定每个内置角色向用户授予的操作和范围。 若要定义自己的角色以便进一步控制，请参阅如何生成 [Azure RBAC 中的自定义角色](../../role-based-access-control/custom-roles.md)。

Azure Active Directory 的其他部分功能包括:

- 使用 Azure AD 即可对 SaaS 应用程序启用 SSO，不管这些应用程序在何处托管。 某些应用程序会与 Azure AD 联合起来进行身份验证，其他应用程序则使用密码 SSO。 联合应用程序还可以支持用户预配和[密码存储](https://www.techopedia.com/definition/31415/password-vault)。

- 对 [Azure 存储](https://azure.microsoft.com/services/storage/)中的数据进行访问可以通过身份验证来控制。 每个存储帐户都有一个主密钥（[存储帐户密钥](../../storage/common/storage-account-create.md)，简称 SAK）和一个辅助密钥（共享访问签名，简称 SAS）。

- Azure AD 通过联合身份验证（使用 [Active Directory 联合身份验证服务](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)）、同步以及本地目录复制方式提供标识即服务。

- [Azure 多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)要求用户使用移动应用、电话呼叫或短信验证登录。 它可以与 Azure AD 配合使用，帮助通过多重身份验证服务器来保护本地资源，还用于使用 SDK 的自定义应用程序和目录。

- [Azure AD 域服务](https://azure.microsoft.com/services/active-directory-ds/)可让用户将 Azure 虚拟机加入一个 Active Directory 域，且无需部署域控制器。 用户可以使用其公司的 Active Directory 凭据登录到这些虚拟机中，并使用组策略管理已加入域的虚拟机，以便在所有 Azure 虚拟机上强制实施安全基准措施。

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 提供高度可用的全局性标识管理服务，该服务适用于面向用户且可通过缩放来处理数以亿计的标识的应用程序。 它可以跨移动平台和 Web 平台进行集成。 使用者只需使用现有社交帐户或创建凭据，即可通过可自定义的体验登录到所有应用程序。

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>与 Microsoft 管理员和数据删除隔离

Microsoft 采取强硬措施保护数据免受不适当的访问或未经授权的用户使用。 这些操作过程和控制由[联机服务条款](https://aka.ms/Online-Services-Terms)提供支持，该条款提供有关管理数据访问权限的合同承诺。

- Microsoft 工程师没有访问云端数据的默认权限。 而是在必要时在管理监督下获取访问权限。 将谨慎控制并记录该访问权限，并在不再需要时撤回。
- Microsoft 可能雇佣其他公司代表其提供有限的服务。 分包商访问客户数据可能只是为了提供服务，这些服务是 Microsoft 雇佣他们来提供的，并且 Microsoft 禁止他们将这些数据用于其他用途。 此外，受合同限制，他们必须维护客户信息的机密性。

对于经过审核认证（如 ISO/IEC 27001）的企业服务，Microsoft 和认证审核公司会定期进行验证，仅出于合法的商业目的对这些服务访问的资产执行样本审计。 用户始终都可随时访问自己的客户数据，而不论出于什么原因。

如果用户删除了任何数据，Microsoft Azure 也将删除该数据，包括所有缓存或备份副本。 对于范围内服务，该删除操作会在保留期结束后 90 天内进行。 （[联机服务条款](https://aka.ms/Online-Services-Terms)的数据处理条款部分对范围内服务进行了定义。）

如果用于存储的磁盘驱动器发生硬件故障，在 Microsoft 将其送回给制造商进行更换或修复前，将安全[擦除或销毁](https://microsoft.com/trustcenter/privacy/you-own-your-data)该磁盘驱动器上的数据。 驱动器上的数据会被覆盖，以确保无法通过任何方式恢复数据。

## <a name="compute-isolation"></a>计算隔离

Microsoft Azure 提供各种基于云的计算服务，包括大量计算实例和服务，它们可根据应用程序或企业的需求自动扩展和缩减。 这些计算实例和服务提供多个级别的隔离来保护数据，且不会降低客户所需配置的灵活性。

### <a name="isolated-virtual-machine-sizes"></a>独立虚拟机大小

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>专用主机

除了前面部分所述的独立主机以外，Azure 还提供了专用主机。 Azure 中的专用主机是一项服务，它提供能够承载一个或多个虚拟机的物理服务器，专用于单个 Azure 订阅。 专用主机在物理服务器级别提供硬件隔离。 不会在你的主机上放置任何其他 VM。 专用主机部署在相同的数据中心，与其他非隔离主机共享相同的网络和底层存储基础结构。 有关详细信息，请参阅 [Azure 专用主机](../../virtual-machines/dedicated-hosts.md)的详细概述。

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>根 VM 和来宾 VM 之间的 Hyper-V 和根 OS 隔离

Azure 的计算平台以计算机虚拟化为基础，这意味着所有客户代码都在 Hyper-V 虚拟机中执行。 在每个 Azure 节点（或网络终结点）上，都有一个虚拟机监控程序在硬件上直接运行，并将节点分为数目不定的来宾虚拟机 (VM)。

![根 VM 和来宾 VM 之间的 Hyper-V 和根 OS 隔离](./media/isolation-choices/azure-isolation-fig4.jpg)

每个节点还有一个特殊的根 VM，用于运行主机 OS。 关键边界由虚拟机监控程序和根 OS 管理，用于将根 VM 与来宾 VM 隔离，以及将各来宾 VM 彼此隔离。 虚拟机监控程序/根 OS 配对充分利用 Microsoft 在操作系统安全性方面的数十年经验以及来自 Hyper-V 的最新信息，实现了各来宾 VM 之间的强大隔离。

Azure 平台使用虚拟化的环境。 用户实例作为无法访问物理主机服务器的独立虚拟机运行。

Azure 的虚拟机监控程序相当于微内核，可将所有硬件访问请求从来宾虚拟机传递到主机，以便使用名为“VM 总线”的共享内存界面进行处理。 这样可以防止用户获取对系统的原始读取/写入/执行访问权限，减轻共享系统资源的风险。

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>高级 VM 布局算法和侧信道攻击防护

任何跨 VM 攻击都包括两个步骤：在同一主机上放置一个攻击者控制的 VM 作为牺牲品 VM 之一，并破坏隔离边界以窃取敏感的牺牲品信息，或者故意或因贪婪影响其性能。 Microsoft Azure 通过使用高级 VM 布局算法同时针对这两个步骤提供保护，并防止所有已知侧信道攻击，包括干扰性邻居 VM。

### <a name="the-azure-fabric-controller"></a>Azure 结构控制器

Azure 结构控制器负责将基础结构资源分配到租户工作负荷，并管理从主机到虚拟机的单向通信。 Azure 结构控制器的 VM 布局算法高度复杂，并且作为物理主机级别几乎不可能预测。

![Azure 结构控制器](./media/isolation-choices/azure-isolation-fig5.png)

Azure 虚拟机监控程序会在虚拟机之间强制实施内存和流程的隔离，并通过安全方式将网络流量路由到来宾 OS 租户。 这样可以避免 VM 级别的侧信道攻击。

在 Azure 中，根 VM 是特殊的：它运行称为根 OS 的强化操作系统，并托管了结构代理 (FA) 。 而 FA 又用于管理客户 VM 上来宾操作系统内的来宾代理 (GA)。 FA 还可管理存储节点。

Azure 虚拟机监控程序、根 OS/FA 和客户 VM/GA 的集合包含一个计算节点。 FA 由结构控制器 (FC) 托管，位于计算节点和存储节点外部（计算和存储群集由单独的 FC 托管）。 如果客户在运行应用程序的同时更新其配置文件，FC 将与 FA 进行通信，然后 FA 将联系 GA，通知应用程序配置已更改。 出现硬件故障时，FC 会自动查找可用硬件并在该处重启 VM。

![Azure 结构控制器](./media/isolation-choices/azure-isolation-fig6.jpg)

结构控制器到代理的通信是单向的。 代理实施受 SSL 保护的服务，仅响应来自控制器的请求。 它不能发起与控制器或其他特权内部节点的连接。 FC 将处理所有响应，就像是它们不受信任一样。

![结构控制器](./media/isolation-choices/azure-isolation-fig7.png)

扩展了根 VM 与来宾 VM，以及来宾 VM 之间的隔离。 计算节点也独立于存储阶段，可增强保护。

虚拟机监控程序和主机 OS 提供了网络数据包筛选器，可帮助确保不受信任的虚拟机无法产生欺骗性流量或接收并非发送给它们的流量，也无法将流量定向到受保护的基础结构终结点，或发送/接收不适当的广播流量。

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>结构控制器代理为隔离 VM 而配置的其他规则

默认情况下，在创建虚拟机时，会阻止所有流量，结构控制器代理会配置数据包筛选器，添加规则和例外以允许经授权的流量。

进行编程的规则有两类：

- **计算机配置或基础结构规则：** 默认情况下，将阻止所有通信。 在例外情况下，可以允许虚拟机发送和接收 DHCP 和 DNS 流量。 虚拟机还可以将流量发送到“公共”Internet 以及同一 Azure 虚拟网络和 OS 激活服务器内的其他虚拟机。 虚拟机的传出目标允许列表不包括 Azure 路由器子网、Azure 管理以及其他 Microsoft 属性。
- **角色配置文件：** 根据租户的服务模型定义入站访问控制列表 (ACL)。

### <a name="vlan-isolation"></a>VLAN 隔离

每个群集中有三个 VLAN：

![VLAN 隔离](./media/isolation-choices/azure-isolation-fig8.jpg)

- 主 VLAN – 互连不受信任的客户节点
- FC VLAN – 包含受信任的 FC 及支持的系统
- 设备 VLAN – 包含受信任的网络和其他基础结构设备

允许从 FC VLAN 到主 VLAN 的通信，但不能启动从主 VLAN 到 FC VLAN 的通信。 还会阻止从主 VLAN 到设备 VLAN 的通信。 这可确保即使运行客户代码的节点遭到破坏， FC 或设备 VLAN 上的节点也不会受到攻击。

## <a name="storage-isolation"></a>存储隔离

### <a name="logical-isolation-between-compute-and-storage"></a>计算和存储之间的逻辑隔离

作为其基本设计的一部分，Microsoft Azure 将基于 VM 的计算与存储分隔开。 这种分隔可实现计算和存储的自主扩展，使提供多租户和隔离变得更简单。

因此，Azure 存储在单独的硬件上运行，且没有与 Azure 计算建立网络连接，从逻辑上讲时例外。 这意味着，创建虚拟磁盘后，不会针对整体容量分配磁盘空间。 而是会创建一个表格，用于将虚拟磁盘上的地址映射到物理磁盘上的区域中，且该表最初为空。 **客户首次在虚拟磁盘上写入数据时，将分配物理磁盘上的空间，且指向它的指针将位于表中。**

### <a name="isolation-using-storage-access-control"></a>使用存储访问控制的隔离

**Azure 存储中的访问控制** 具有简单的访问控制模型。 每个 Azure 订阅都可以创建一个或多个存储帐户。 每个存储帐户都具有一个密钥，用于控制对该存储帐户中所有数据的访问权限。

![使用存储访问控制的隔离](./media/isolation-choices/azure-isolation-fig9.png)

可以通过 [SAS（共享访问签名）](../../storage/common/storage-sas-overview.md)令牌来控制 **对 Azure 存储数据（包括表）的访问权限**，该令牌可授予限定的访问权限。 SAS 是根据查询模板 (URL) 创建的，且使用 [SAK（存储帐户密钥）](/previous-versions/azure/reference/ee460785(v=azure.100))进行签名。 可以将该[签名 URL](../../storage/common/storage-sas-overview.md) 提供给另一个进程（即委托进程），后者随后可以填充查询的详细信息并发出存储服务请求。 使用 SAS，可以向客户端授予基于时间的访问权限，无需泄露存储帐户的密钥。

使用 SAS，意味着可以授权客户端在指定时间段内，以一组指定权限有限访问存储帐户中的对象。 可以授予这些有限的权限，而不必共享帐户访问密钥。

### <a name="ip-level-storage-isolation"></a>IP 级别存储隔离

可以为受信任客户端建立防火墙，定义 IP 地址范围。 使用 IP 地址范围，只有 IP 地址在定义范围内的客户端才可以连接到 [Azure 存储](../../storage/blobs/security-recommendations.md)。

可通过网络机制防止未经授权的用户访问 IP 存储数据，该机制用于分配到 IP 存储的专用流量或专用流量隧道。

### <a name="encryption"></a>Encryption

Azure 提供了以下加密类型来保护数据：

- 传输中加密
- 静态加密

#### <a name="encryption-in-transit"></a>传输中加密

传输中加密是通过网络传输数据时用于保护数据的机制。 在 Azure 存储中，可以使用以下加密方式来保护数据：

- [传输级别加密](../../storage/blobs/security-recommendations.md)，例如从 Azure 存储传入或传出数据时使用的 HTTPS。
- [线路加密](../../storage/blobs/security-recommendations.md)，例如 Azure 文件共享的 SMB 3.0 加密。
- [客户端加密](../../storage/blobs/security-recommendations.md)，在将数据传输到存储之前加密数据，以及从存储传出数据后解密数据。

#### <a name="encryption-at-rest"></a>静态加密

对许多组织而言， [静态数据加密](isolation-choices.md) 是实现数据隐私性、合规性和数据所有权的必要措施。 有三项 Azure 功能可提供“静态”数据加密：

- [存储服务加密](../../storage/blobs/security-recommendations.md)可以请求存储服务在将数据写入 Azure 存储时自动加密数据。
- [客户端加密](../../storage/blobs/security-recommendations.md) 也提供静态加密功能。
- [Azure 磁盘加密](./azure-disk-encryption-vms-vmss.md) 允许加密 IaaS 虚拟机使用的 OS 磁盘和数据磁盘。

#### <a name="azure-disk-encryption"></a>Azure 磁盘加密

适用于虚拟机 (VM) 的 [Azure 磁盘加密](./azure-disk-encryption-vms-vmss.md)通过使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 中控制的密钥和策略加密 VM 磁盘（包括引导磁盘和数据磁盘），帮助解决企业的安全和符合性要求。

适用于 Windows 的磁盘加密解决方案是基于 [Microsoft BitLocker 驱动器加密](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11))技术，Linux 解决方案基于 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)。

在 Microsoft Azure 中启用 IaaS VM 时，该解决方案支持以下 IaaS VM 方案：

- 与 Azure Key Vault 集成
- 标准层 VM：A、D、DS、G 和 GS 等系列 IaaS VM
- 在 Windows 和 Linux IaaS VM 上启用加密
- 在 Windows IaaS VM 的 OS 和数据驱动器上禁用加密
- 在 Linux IaaS VM 的数据驱动器上禁用加密
- 在运行 Windows 客户端 OS 的 IaaS VM 上启用加密
- 在包含安装路径的卷上启用加密
- 在使用 [mdadm](https://en.wikipedia.org/wiki/Mdadm) 配置了磁盘条带化 (RAID) 的 Linux VM 上启用加密
- 使用 [LVM（逻辑卷管理器）](/windows/win32/fileio/about-volume-management)对 Linux VM 上的数据磁盘启用加密
- 在使用存储空间配置的 Windows VM 上启用加密
- 支持所有 Azure 公共区域

该解决方案不支持版本中的以下方案、功能和技术：

- 基本层 IaaS VM
- 在 Linux IaaS VM 的 OS 驱动器上禁用加密
- 使用经典 VM 创建方法创建的 IaaS VM
- 与本地密钥管理服务集成
- Azure 文件（文件共享系统）、网络文件系统 (NFS)、动态卷，以及配置了基于软件的 RAID 系统的 Windows VM

## <a name="sql-database-isolation"></a>SQL 数据库隔离

SQL 数据库是 Microsoft 云中的关系型数据库服务，它基于行业领先的 Microsoft SQL Server 引擎，能够处理任务关键型工作负荷。 SQL 数据库在联网时基于地理位置/区域提供帐户级别的可预测数据隔离，几乎不用人工管理。

### <a name="sql-database-application-model"></a>SQL 数据库应用程序模型

[Microsoft SQL 数据库](../../azure-sql/database/single-database-create-quickstart.md)是一项基于云的关系数据库服务，是根据 SQL Server 技术构建的。 它提供由 Microsoft 在云端托管的多租户数据库服务，该服务高度可用并且可缩放。

从应用程序的角度来看，SQL 数据库提供了以下层次结构：每个级别都包含以下一对多的级别。

![SQL 数据库应用程序模型](./media/isolation-choices/azure-isolation-fig10.png)

帐户和订阅是用于将计费和管理关联的 Microsoft Azure 平台。

逻辑 SQL 服务器和数据库是特定于 SQL 数据库的概念，通过使用 SQL 数据库以及提供的 OData 和 TSQL 接口或者通过 Azure 门户进行管理。

SQL 数据库中的服务器不是物理实例或 VM 实例，而是数据库、共享管理和安全策略的集合，它们存储在所谓的“逻辑主”数据库中。

![SQL 数据库](./media/isolation-choices/azure-isolation-fig11.png)

逻辑主数据库包括：

- 用于连接到服务器的 SQL 登录名
- 防火墙规则

同一服务器中数据库的计费和使用情况相关信息不保证位于群集中的同一物理实例中，应用程序在连接时必须提供目标数据库名称。

从客户的角度看，服务器是在某个地理区域中创建的，但实际上，服务器是在该区域内的一个群集中创建的。

### <a name="isolation-through-network-topology"></a>通过网络拓扑实现的隔离

创建服务器并注册其 DNS 名称后，该 DNS 名称指向该服务器所在的特定数据中心内所谓的“网关 VIP”地址。

在 VIP（虚拟 IP 地址）后面，有一个无状态网关服务的集合。 通常，多个数据源（主数据库、用户数据库等）之间需要协调时，将涉及到网关。 网关服务可实现以下功能：

- **TDS 连接代理。** 这包括查找后端群集中的用户数据库，实现登录序列，并将 TDS 数据包转发到后端并返回。
- **数据库管理。** 这包括采用工作流集合来执行数据库的创建/更改/删除操作。 可通过探查 TDS 数据包或显式 OData API 调用数据库操作。
- 创建/更改/删除登录/用户操作
- 通过 OData API 进行的服务器管理操作

![通过网络拓扑实现的隔离](./media/isolation-choices/azure-isolation-fig12.png)

网关后面的层称为“后端”。 这是以高度可用的方式存储所有数据的位置。 每段数据都被认为属于某个“分区”或“故障转移单元”，并且有至少三个副本。 副本由 SQL Server 引擎存储和复制，并由通常称为“结构”的故障转移系统进行管理。

通常，作为安全预防措施，后端系统不会与其他系统进行出站通信。 这会保留到前端（网关）层中的系统。 作为深层防御机制，网关层计算机对后端计算机具有有限的特权，可最大限度减少攻击。

### <a name="isolation-by-machine-function-and-access"></a>按计算机功能和访问权限的隔离

SQL 数据库由针对不同计算机功能运行的服务组成。 按照流量在后端只进不出的一般原则，SQL 数据库分为“后端”云数据库和“前端”（网关/管理）环境。前端环境可与其他服务外部进行通信，而在后端只有有限的权限（足以调用进行调用所需的入口点）。

## <a name="networking-isolation"></a>网络隔离

Azure 部署具有多层网络隔离。 下图显示了 Azure 提供给客户的各种网络隔离层。 这些层同时属于 Azure 平台本身的本机功能和客户定义的功能。 对于来自 Internet 的入站流量，Azure DDoS 提供针对 Azure 的大规模攻击的隔离。 下一层隔离是客户定义的公共 IP 地址（终结点），可以根据这些终结点确定哪些流量可以通过云服务进入虚拟网络。 本机 Azure 虚拟网络隔离可确保与其他所有网络完全隔离，而且流量只能流经用户配置的路径和方法。 这些路径和方法就是下一个安全层，在该层中，可以使用 NSG、UDR 和网络虚拟设备来创建隔离边界，以保护受保护网络中的应用程序部署。

![网络隔离](./media/isolation-choices/azure-isolation-fig13.png)

**流量隔离**：[虚拟网络](../../virtual-network/virtual-networks-overview.md)是 Azure 平台上的流量隔离边界。 一个虚拟网络中的虚拟机 (VM) 无法与不同虚拟网络中的 VM 直接通信，即使这两个虚拟网络是由同一个客户所创建。 隔离是一个非常关键的属性，可确保客户 VM 与通信在虚拟网络中保持私密性。

[子网](../../virtual-network/virtual-networks-overview.md)基于 IP 范围在虚拟网络中提供额外的隔离层。 使用虚拟网络中的 IP 地址，可以将虚拟网络划分成多个子网，以方便进行组织和提高安全性。 部署到 VNet 的子网（不管是相同的子网还是不同的子网）中的 VM 和 PaaS 角色实例可以互相通信，不需任何额外的配置。 还可以配置[网络安全组 (NSG)](../../virtual-network/virtual-networks-overview.md)，以便根据 NSG 的访问控制列表 (ACL) 中配置的规则允许或拒绝到某个 VM 实例的网络流量。 NSG 可以与子网或该子网中的各个 VM 实例相关联。 当 NSG 与子网关联时，ACL 规则将应用于该子网中的所有 VM 实例。

## <a name="next-steps"></a>后续步骤

- 了解[适用于 Microsoft Azure 虚拟网络中的计算机的网络隔离选项](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)。 它包括经典的前端和后端方案，其中特定后端网络或子网中的计算机可能只允许某些客户端或其他计算机根据 IP 地址允许列表连接到特定终结点。

- 了解 [Azure 中的虚拟机隔离](../../virtual-machines/isolation.md)。 Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。