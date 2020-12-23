---
title: Azure Marketplace 中的 VM 常见问题
description: 在 Azure Marketplace 中创建虚拟机时遇到的常见问题。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 88ec58ca9bcfa5c64036d3b65e77f3248e26357d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124945"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Azure Marketplace 中的 VM 常见问题

这些常见问题 (常见问题) 介绍在 Azure Marketplace 中创建虚拟机 (VM) 产品/服务时可能会遇到的常见问题。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何将虚拟专用网络 (VPN) 配置为使用我的 VM？

如果使用 Azure 资源管理器部署模型，则有三个选项：

- [使用 Azure 门户创建基于路由的 VPN 网关](../vpn-gateway/tutorial-create-gateway-portal.md)
- [使用 Azure PowerShell 创建基于路由的 VPN 网关](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [使用 CLI 创建基于路由的 VPN 网关](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>有关在基于 Azure 的 VM 上运行 Microsoft 服务器软件的 Microsoft 支持策略有哪些？

有关详细信息，请访问 [Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中如何管理启动任务中的自定义脚本扩展？

有关通过 Azure PowerShell 模块、Azure 资源管理器模板和 Windows 系统上的故障排除步骤使用自定义脚本扩展的详细信息，请参阅[适用于 Windows 的自定义脚本扩展](../virtual-machines/extensions/custom-script-windows.md)。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure 市场是否支持 32 位应用程序或服务？

没有。 Azure VM 支持的操作系统和标准服务均为 64 位。 尽管大多数 64 位操作系统都支持 32 位版本的应用程序以实现向后兼容性，但不支持将 32 位应用程序用作 VM 解决方案的一部分，因此强烈建议不要使用 32 位应用程序。 请将应用程序重新创建为 64 位项目。

有关详细信息，请参阅以下文章：

- [运行 32 位应用程序](/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure 虚拟机中 32 位操作系统的支持](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>错误：VHD 已作为资源注册到映像存储库

每当我尝试从 VHD 创建映像时，都会在 Azure PowerShell 中收到错误“VHD 已作为资源注册到映像存储库”。 我之前没有创建任何映像，也没在 Azure 中找到任何带有此名称的映像。 如何解决此问题？

如果从带锁的 VHD 创建了 VM，则通常会出现此问题。 请确认没有从此 VHD 分配 VM，然后重试此操作。 如果问题仍然存在，请开具支持票证。 请参阅[合作伙伴中心支持](support.md)。

## <a name="next-steps"></a>后续步骤

- [VM 认证故障排除](azure-vm-create-certification-faq.md)