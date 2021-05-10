---
title: Windows 10 企业版多会话常见问题解答 - Azure
description: 关于使用面向 Windows 虚拟桌面的 Windows 10 企业版多会话的常见问题解答和最佳做法。
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5c03d3f9769aec0736d23f18372701e08ad93dac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802788"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 Enterprise 多会话常见问题解答

本文解答了 Windows 10 企业版多会话的常见问题，并说明了相应的最佳做法。

## <a name="what-is-windows-10-enterprise-multi-session"></a>什么是 Windows 10 企业版多会话？

Windows 10 企业版多会话，以前称为适用于虚拟桌面的 Windows 10 企业版 (EVD)，是一种新型远程桌面会话主机，允许多个并发交互式会话。 以前，只有 Windows Server 支持此功能。 此功能为用户提供了一个熟悉的 Windows 10 体验，同时 IT 能够从多会话的成本优势中获益，并使用现有的每用户 Windows 许可，而不是 RDS 客户端访问许可证 (CAL)。 有关许可证和定价的详细信息，请参阅 [Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>在 Windows 10 企业版多会话中，有多少用户可以同时进行交互式会话？

可以同时处于活动状态的交互式会话数量取决于系统的硬件资源（vCPU、内存、磁盘和 vGPU）、用户登录到会话时使用其应用的方式，以及系统的工作负载繁重情况。 我们建议你验证系统的性能，了解可以在 Windows 10 企业版多会话上拥有的用户数量。 若要了解详细信息，请参阅 [Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>为什么我的应用程序将 Windows 10 企业版多会话报告为服务器操作系统？

Windows 10 企业版多会话是 Windows 10 企业版的虚拟版本。 其中一个区别是，此操作系统 (OS) 报告的 [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) 值为 3，这与 Windows Server 的值相同。 这一特性可让 OS 与现有的 RDSH 管理工具、RDSH 多会话感知应用程序，以及针对 RDSH 环境的大多数低级别系统性能优化保持兼容。 某些应用程序安装程序会阻止 Windows 10 多会话上的安装，具体取决于它们是否检测到 ProductType 设置为“客户端”。 如果你的应用无法安装，请联系应用程序供应商，获取更新的版本。

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>能否在本地运行 Windows 10 企业版多会话？

Windows 10 企业版多会话无法在本地生产环境中运行，因为它已针对适用于 Azure 的 Windows 虚拟桌面服务进行了优化。 在 Azure 之外出于生产目的运行 Windows 10 企业版多会话违反了许可协议。 Windows 10 企业版多会话不会针对本地密钥管理服务 (KMS) 激活。

## <a name="can-i-upgrade-a-windows-10-vm-to-windows-10-enterprise-multi-session"></a>是否可以将 Windows 10 VM 升级到 Windows 10 企业版多会话？

不是。 目前无法将运行 Windows 10 专业版或企业版的现有虚拟机 (VM) 升级到 Windows 10 企业版多会话。 另外，如果部署了 Windows 10 企业版多会话 VM，然后将产品密钥更新到另一个版本，则无法将该 VM 切换回 Windows 10 企业版多会话，需要重新部署该 VM。

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>如何为组织自定义 Windows 10 企业版多会话映像？

你可以使用 Windows 10 企业版多会话在 Azure 中启动 VM，通过安装 LOB 应用程序来自定义该 VM，对其进行 sysprep/generalize 操作，然后使用 Azure 门户创建一个映像。

首先，使用 Windows 10 企业版多会话在 Azure 中创建一个 VM。 你可以直接下载 VHD，而不是在 Azure 中启动 VM。 之后，你将能够使用已下载的 VHD 在已启用 Hyper-V 的 Windows 10 电脑上创建新的第 1 代 VM。

通过安装 LOB 应用程序，根据需要自定义映像，并对映像进行 sysprep。 完成自定义后，将包含 VHD 的映像上传到 Azure。 然后，从 Azure 市场获取 Windows 虚拟桌面，并使用它来部署包含自定义映像的新主机池。

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>部署后如何管理 Windows 10 企业版多会话？

你可以使用任何支持的配置工具，但我们建议使用 Configuration Manager 版本 1906，因为它支持 Windows 10 企业版多会话。 我们目前正在努力提供 Microsoft Intune 支持。

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 企业版多会话能否加入 Azure Active Directory (AD)？

目前支持将 Windows 10 企业版多会话加入混合 Azure AD。 在 Windows 10 企业版多会话加入域后，使用现有组策略对象启用 Azure AD 注册。 有关详细信息，请参阅[规划混合 Azure Active Directory 加入实现](../active-directory/devices/hybrid-azuread-join-plan.md)。

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>在哪里可以找到 Windows 10 企业版多会话映像？

Windows 10 企业版多会话位于 Azure 库。 若要查找它，请导航到 Azure 门户并搜索 Windows 10 企业版虚拟桌面版本。 对于与 Microsoft 365 企业应用版集成的映像，请转到 Azure 门户并搜索“Microsoft Windows 10 + Microsoft 365 企业应用版”。

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>我应该使用哪个 Windows 10 企业版多会话映像？

Azure 库包含多个版本，包括 Windows 10 企业版多会话版本 1809 和 Windows 10 企业版多会话版本 1903。 建议使用最新版本，以提高性能和可靠性。

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>哪些 Windows 10 企业版多会话版本受支持？

Windows 10 企业版多会话版本 1809 及更高版本均受支持，它们都通过 Azure 库提供。 这些版本遵循与 Windows 10 企业版相同的支持生命周期策略，这意味着 3 月版可得到支持 18 个月，9 月版可得到支持 30 个月。

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>应该对 Windows 10 企业版多会话使用哪种配置文件管理解决方案？

如果在非永久性环境或其他需要集中存储配置文件的场景中配置 Windows 10 企业版，我们建议你使用 FSLogix 配置文件容器。 FSLogix 可确保用户配置文件对每个用户会话可用，且保持最新。 我们还建议你使用 FSLogix 配置文件容器将用户配置文件存储在具有相应权限的任何 SMB 共享中，但如有必要，也可将用户配置文件存储在 Azure 页 blob 存储中。 Windows 虚拟桌面用户可以使用 FSLogix，而无需额外付费。  FSLogix 预先安装在所有 Windows 10 企业版多会话映像上，但仍由 IT 管理员负责配置 FSLogix 配置文件容器。

要详细了解如何配置 FSLogix 配置文件容器，请参阅[配置 FSLogix 配置文件容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)。

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>访问 Windows 10 企业版多会话需要哪个许可证？

有关适用许可证的完整列表，请参阅 [Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>为何我注销后，应用消失了？

出现这种情况是因为你将 Windows 10 企业版多会话与 FSLogix 等配置文件管理解决方案一起使用。 你的管理员或配置文件解决方案配置了系统，使其在用户注销时删除用户配置文件。此配置意味着，在你注销后，系统删除用户配置文件时，还会删除会话期间安装的任何应用。 如果要保留已安装的应用，则需要让管理员为 Windows 虚拟桌面环境中的所有用户预配这些应用。

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>如何确保用户注销时应用不会消失？

默认情况下，大多数虚拟化环境都配置为阻止用户将其他应用安装到其配置文件中。 如果要确保在用户注销 Windows 虚拟桌面时应用不会消失，则必须为环境中的所有用户配置文件预配该应用。 如需详细了解如何预配应用，请查看以下资源：

- [在 Windows 虚拟桌面中发布内置应用](publish-apps.md)
- [DISM 应用包服务命令行选项](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](/powershell/module/dism/add-appxprovisionedpackage)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>如何确保用户不会从 Microsoft Store 下载和安装应用？

可以禁用 Microsoft Store 应用，确保用户只下载你已为其预配的应用，而不会下载除此之外的其他应用。

禁用 Store 应用：

1. 创建新的组策略。
2. 选择“计算机配置” > “管理模板” > “Windows 组件”  。
3. 选择“应用商店”。
4. 选择“Store 应用程序”。
5. 选择“禁用”，然后选择“确定” 。
6. 选择“应用”。

## <a name="next-steps"></a>后续步骤

详细了解 Windows 虚拟桌面和 Windows 10 企业版多会话：

- 阅读 [Windows 虚拟桌面文档](overview.md)
- 访问 [Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- 使用 [Windows 虚拟桌面教程](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)设置 Windows 虚拟桌面部署
