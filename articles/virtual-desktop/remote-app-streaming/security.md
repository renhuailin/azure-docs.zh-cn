---
title: 跨组织应用的安全指南 Azure 虚拟桌面 - Azure
description: 介绍如何在多个组织中保护托管在 Azure 虚拟桌面中的应用。
author: Heidilohr
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 6ddb30832db4e504e57296d00db45125f8514afd
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178523"
---
# <a name="security-guidelines-for-cross-organizational-apps"></a>跨组织应用的安全指南

在 Azure 虚拟桌面上托管或流式传输应用时，你会接触到组织内部和外部的各类用户。 因此，请务必了解如何保证部署安全，以确保组织和客户的安全。 本指南将帮助你大致了解可能的安全问题以及如何解决这些问题。

## <a name="shared-responsibility"></a>共担责任

在 Azure 虚拟桌面之前，本地虚拟化解决方案（如远程桌面服务）需要授予用户对网关、代理、Web 访问等角色的访问权限。 这些角色必须是完全冗余的，并能够处理高峰容量。 管理员会将这些角色作为 Windows Server OS 的一部分进行安装，并且它们必须加入域，且特定端口可访问公共连接。 为了保证部署安全，管理员必须不断地确保基础结构中的所有内容保持最新状态。

同时，Azure 虚拟桌面代表客户管理部分服务。 具体而言，Microsoft 将基础结构部件作为服务的一部分进行托管和管理。 合作伙伴和客户不再需要手动管理所需的基础结构来使用户能够访问会话主机虚拟机 (VM)。 该服务还提供了反向连接等内置高级安全功能，从而降低了允许用户从任何位置访问其远程桌面所涉及的风险。

为了保持服务的灵活性，会话主机托管在合作伙伴或客户的 Azure 订阅中。 这使客户能够将该服务与其他 Azure 服务集成，并使客户能够使用 ExpressRoute 或虚拟专用网络 (VPN) 连接本地网络基础结构。

和许多云服务一样，你和 Microsoft 之间有[一系列共担的安全责任](../../security/fundamentals/shared-responsibility.md)。 使用 Azure 虚拟桌面时，请务必了解，虽然服务的某些部分受到保护，但对于其他部分，你需要根据组织的安全需求自行配置。

需要在以下区域配置安全性：

- 最终用户设备
- 应用程序安全性
- 会话主机操作系统
- 部署配置
- 网络控制措施

如需了解如何配置这些区域，请查看我们的[安全性最佳做法](./../security-guide.md)。

## <a name="combined-microsoft-security-platform"></a>组合的 Microsoft 安全平台

可以通过使用 Microsoft 365、Azure 和 Azure 虚拟桌面中的安全功能和控件来保护工作负载。

用户通过 Internet 连接到服务时，Azure Active Directory (Azure AD) 会对用户的凭据进行身份验证，且启用保护功能（如[条件访问](../../active-directory/conditional-access/overview.md)和[多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)）。 这些功能极大地降低了用户凭据被盗用的风险。

Azure 虚拟桌面具有[反向连接](../network-connectivity.md#reverse-connect-transport)等功能，使用户无需打开入站端口即可访问会话主机。 此功能的设计考虑到了可伸缩性和服务，因此应该不会限制你扩展会话主机的能力。 还可以将现有 GPO 和此功能结合使用，应用附加安全性，支持加入 Active Directory 的 VM，或支持可能涉及 Azure Active Directory 加入场景、[Microsoft Endpoint Manager](/mem/intune/fundamentals/windows-virtual-desktop-multi-session) 的 Windows 10 会话主机。

## <a name="defense-in-depth"></a>深层防御

鉴于如今的威胁环境，需要在设计时考虑安全方法。 理想情况下，你将需要构建一系列安全机制和在整个计算机网络中分层的控件，以保护数据和网络不被泄露或攻击。 美国网络安全和基础结构安全机构 (CISA) 将这种安全设计称为“深度防御”。 若要详细了解深度防御，请转到 [CISA 网站](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth)。

## <a name="session-host-operating-systems"></a>会话主机操作系统

Azure 虚拟桌面支持以下操作系统：

- Windows 7
- Windows 10 企业版
- Windows Server 2012 R2、2016 和 2019
- Windows 10 Enterprise 多会话

Windows 10 企业版多会话是一种 Azure 专用 OS，它充当远程桌面会话主机 (RDSH)，支持多个并发交互式会话，与 Windows Server 非常类似。 Windows 10 环境为用户提供了一种熟悉且一致的体验。 服务的基于用户的 Windows 许可还会降低整个组织的成本，因为 IT 无需不断购买 RDS 客户端访问许可证 (CAL)。 由于拥有这些优点，Windows 10 企业版多会话是 Azure 虚拟桌面中会话主机的最广泛部署的 OS。

## <a name="security-boundaries"></a>安全边界

安全边界利用不同的信任级别分隔安全域的代码和数据。 例如，内核模式和用户模式之间通常存在安全边界。 大多数 Microsoft 软件和服务都依赖于多个安全边界来隔离网络上的设备、VM 和设备上的应用程序。 下表列出了 Windows 的每个安全边界以及它们对总体安全性的作用。

| 安全边界             | 边界的作用      |
|-----------------------------------|--------------------------------------|
| 网络边界              | 未经授权的网络终结点无法访问或篡改客户设备上的代码和数据。                                                                   |
| 内核边界               | 非管理用户模式进程无法访问或篡改内核代码和数据。 管理员到内核不是安全边界。                             |
| 进程边界               | 未经授权的用户模式进程无法访问或篡改其他进程的代码和数据。                                                                      |
| AppContainer 沙盒边界  | 基于 AppContainer 的沙盒进程无法访问或篡改基于容器功能的沙盒之外的代码和数据。                               |
| 用户边界                  | 用户无法在未经授权的情况下访问或篡改其他用户的代码和数据。                                                                           |
| 会话边界               | 用户会话无法在未经授权的情况下访问或篡改其他用户会话。                                                                    |
| Web 浏览器边界           | 未经授权的网站不能违反同源策略，也不能访问或篡改 Microsoft Edge Web 浏览器沙盒的本机代码和数据。       |
| 虚拟机边界       | 未经授权的 Hyper-V 来宾虚拟机无法访问或篡改其他来宾虚拟机的代码和数据；这包括 Hyper-V 独立容器。 |
| 虚拟安全模式 (VSM) 边界  | 在 VSM 可信进程或 enclave 外部运行的代码无法访问或篡改受信任进程中的数据和代码。                              |

## <a name="security-features"></a>安全功能

安全功能基于安全边界构建，以增强对特定威胁的防护。 但是，在某些情况下，可能会受到各种设计上的限制，使安全功能无法完全保护部署。

Azure 虚拟桌面支持 Windows 中提供的大多数安全功能。 依赖于硬件功能的安全功能（如 (v)TPM 或嵌套虚拟化）可能需要来自 Azure 虚拟桌面团队的单独支持声明。

若要详细了解安全功能支持和服务，请参阅 [Microsoft 的 Windows 安全服务标准](https://www.microsoft.com/msrc/windows-security-servicing-criteria)。

## <a name="recommended-security-boundaries-for-azure-virtual-desktop-scenarios"></a>针对 Azure 虚拟桌面场景的建议安全边界

还需要根据具体情况对安全边界做出一些选择。 例如，如果组织中的某个用户需要使用本地管理权限才能安装应用，则你需要为他们提供个人桌面，而不是共享的 RDSH。 我们不建议在多会话共用场景中为用户提供本地管理员特权，因为这些用户可以跨越会话或 NTFS 数据权限的安全边界，关闭多会话 VM，或执行可能中断服务或导致数据丢失的其他操作。

同一组织中的用户（例如拥有不需要管理员特权的应用的知识型员工）非常适合使用多会话远程桌面会话主机（例如 Windows 10 企业版多会话）。 由于多个用户可以共享单个 VM，因此这些会话主机可降低组织成本，仅产生单个 OS 的开销成本。 通过使用配置文件技术（如 FSLogix），可以为用户分配主机池中的任何 VM，而不会发现任何服务中断。 借助此功能，还可以通过在非高峰期执行关闭 VM 等操作来优化成本。

如果你的情况要求不同组织的用户连接到你的部署，则建议你为标识服务（如 Active Directory 和 Azure AD）使用单独的租户。 还建议使用单独的订阅来托管 Azure 资源（如 Azure 虚拟桌面）和 VM。

下表列出了针对每种场景的建议。

| 信任级别场景                                 | 建议的解决方案                |
|------------------------------------------------------|-------------------------------------|
| 一个组织中具有标准特权的用户 | Windows 10 Enterprise 多会话 |
| 用户需要管理权限             | 个人桌面 (VDI)             |
| 来自不同组织连接的用户        | 单独的 Azure 订阅         |

我们来看一下一些示例场景建议。

### <a name="should-i-share-identity-resources-to-reduce-costs"></a>我应该通过共享标识资源来降低成本吗？

目前不建议在 Azure 虚拟桌面中使用共享标识系统。 建议在单独的 Azure 订阅中部署单独的标识资源。 这些资源包括 Active Directory、Azure AD 和 VM 工作负载。 为单个组织工作的每个用户都需要额外的基础结构和相关维护成本，但出于安全目的，这是目前最可行的解决方案。 

### <a name="should-i-share-a-multi-session-remote-desktop-rd-session-host-vm-to-reduce-costs"></a>我应该通过共享多会话远程桌面 (RD) 会话主机 VM 来降低成本吗？

多会话 RD 会话主机通过共享硬件资源（如用户之间的 CPU 和内存）来节约成本。 通过此资源共享，你可以针对峰值容量进行设计，即使所有用户不太可能同时需要最大资源也是如此。 在共享多会话环境中，共享和分配硬件资源，以减少使用量与成本之间的差距。

在许多情况下，使用多会话是一种降低成本的可接受方法，但是否建议这样做取决于同时访问共享多会话实例的用户之间的信任级别。 通常，属于同一组织的用户具有足够且一致同意的信任关系。 例如，员工相互协作并可以访问彼此个人信息的部门或工作组是具有高度信任级别的组织。

Windows 使用安全边界和控件来确保用户进程和数据在会话之间隔离。 但是，Windows 仍然提供对用户正在处理的实例的访问权限。

此部署将受益于安全深度策略，该策略增加了更多的安全边界，防止组织内外的用户未经授权访问其他用户的个人信息。 发生未经授权的数据访问是由于系统管理员在配置过程中出错（例如未公开的安全漏洞或尚未修补的已知漏洞）。

另一方面，Microsoft 不支持向为不同公司或竞争对手公司工作的用户授予对同一多会话环境的访问权限。 这些场景具有若干安全边界，这些边界可能会被攻击或滥用，例如网络、内核、进程、用户或会话。 单个安全漏洞可能会导致未经授权的数据和凭据被盗、个人信息泄漏、标识盗用和其他问题。 虚拟化环境提供商负责提供设计良好的系统，这些系统具有多个强大的安全边界，并根据需要启用额外的安全功能。

减少这些潜在威胁需要防故障配置、修补程序管理设计流程和定期修补程序部署计划。 最好遵循深度防御原则，使环境保持独立。

## <a name="next-steps"></a>后续步骤

如需有关为 Azure 虚拟桌面部署配置安全性的建议指南，请参阅[安全性最佳做法](./../security-guide.md)。