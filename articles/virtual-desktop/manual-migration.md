---
title: 从 Windows 虚拟桌面（经典版）手动进行迁移 - Azure
description: 如何从 Windows 虚拟桌面（经典版）手动迁移到 Windows 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 350b7306b6d39b42fbfebcc6566762df305b11b4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445969"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>从 Windows 虚拟桌面（经典版）手动进行迁移

Windows 虚拟桌面（经典版）使用 PowerShell cmdlet、REST API 和服务对象创建其服务环境。 Windows 虚拟桌面服务环境中的“对象”是 Windows 虚拟桌面创建的一项内容。 服务对象包括租户、主机池、应用程序组和会话主机。

但是，Windows 虚拟桌面（经典版）未与 Azure 集成。 如果没有 Azure 集成，则 Azure 门户不会自动管理你创建的任何对象，因为这些对象未连接到你的 Azure 订阅。

Windows 虚拟桌面最近的重大更新标志着该服务向完全 Azure 集成的转变。 在 Windows 虚拟桌面中创建的对象将由 Azure 门户自动管理。

在本文中，我们将介绍为什么应考虑迁移到最新版本的 Windows 虚拟桌面。 接下来，我们将告诉你如何从 Windows 虚拟桌面（经典版）手动迁移到最新的 Windows 虚拟桌面更新。

## <a name="why-migrate"></a>为何要迁移？

重大更新可能很不方便，尤其是那些你必须手动执行的更新。 但是，下面一些原因会导致你无法自动进行迁移：

- 使用经典版本创建的现有服务对象在 Azure 中没有对应的表示形式。 它们的作用域并未超出 Windows 虚拟桌面服务。
- 在最新的更新中，该服务的应用程序 ID 已更改，这样是为了删除对应用的同意，就像它对 Windows 虚拟桌面（经典版）所做的那样。 你将无法通过 Windows 虚拟桌面创建新的 Azure 对象，除非这些对象通过新的应用程序 ID 进行身份验证。

尽管很麻烦，但从经典版本进行迁移仍然很重要。 下面是迁移后可以执行的操作：

- 通过 Azure 门户管理 Windows 虚拟桌面。
- 将 Azure Active Directory (AD) 用户组分配给应用程序组。
- 使用改进后的 Log Analytics 功能对你的部署进行故障排除。
- 使用 Azure 原生的基于角色的访问控制 (Azure RBAC) 对管理性访问进行管理。

## <a name="when-should-i-migrate"></a>应该何时进行迁移？

在询问自己是否应该迁移时，还应该考虑部署的当前的和未来的情况。

在下面一些场景中，我们尤其建议你手动进行迁移：

- 你设置了包含少量用户的测试主机池。
- 你设置了包含少量用户的生产主机池，但计划最终增加到数百个用户。
- 你有一个可以轻松复制的简单设置。 例如，如果你的 VM 使用库映像。

> [!IMPORTANT]
> 如果你使用的是需要很长时间才能稳定的高级配置，或者有大量用户，则我们不建议手动迁移。

## <a name="prepare-for-migration"></a>准备迁移

在开始之前，你需要确保你的环境已准备好进行迁移。

下面是启动迁移过程所需满足的条件：

- 一个 Azure 订阅，你将在其中创建新的 Azure 服务对象。
- 确保已将你分配到下列角色：
    
    - 参与者
    - 用户访问管理员
    
    “参与者”角色允许你在订阅上创建 Azure 对象，“用户访问管理员”角色允许你将用户分配到应用程序组。

## <a name="how-to-migrate-manually"></a>如何手动进行迁移

现在，你已做好迁移过程的准备，可以实际进行迁移了。

若要从 Windows 虚拟桌面（经典版）手动迁移到 Windows 虚拟桌面，请执行以下操作：

1. 按照[使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)中的说明使用 Azure 门户创建所有高级别对象。
2. 如果要将已在使用的虚拟机迁移过来，请按照[将虚拟机注册到 Windows 虚拟桌面主机池](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)中的说明手动将它们注册到你在步骤 1 中创建的新主机池。
3. 创建新的 RemoteApp 应用组。
4. 将用户或用户组发布到新的桌面和 RemoteApp 应用组。
5. 按照[设置多重身份验证](set-up-mfa.md)中的说明更新你的条件访问策略，以允许新对象。

为了防止停机，你应该先将现有会话主机以小组形式一次性注册到 Azure 资源管理器集成的主机池中。 然后，慢慢将用户迁移到新的 Azure 资源管理器集成应用组。

## <a name="next-steps"></a>后续步骤

在迁移后，请通过查看[我们的教程](create-host-pools-azure-marketplace.md)来了解 Windows 虚拟桌面的工作方式。 从[扩展现有主机池](expand-existing-host-pool.md)和[自定义 RDP 属性](customize-rdp-properties.md)了解高级管理功能。

若要详细了解服务对象，请参阅 [Windows 虚拟桌面环境](environment-setup.md)。
