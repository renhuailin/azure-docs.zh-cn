---
title: 使用 RDP 连接到 Windows VM
titleSuffix: Azure Bastion
description: 了解如何使用 Azure Bastion 通过 RDP 连接到 Windows VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 1a1423a0587a64452671e74a210baa9d2ad16ea8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699046"
---
# <a name="create-an-rdp-connection-to-a-windows-vm-using-azure-bastion"></a>使用 Azure Bastion 创建与 Windows VM 的 RDP 连接

本文演示如何直接通过 Azure 门户安全无缝地与 Azure 虚拟网络中的 Windows VM 建立 RDP 连接。 使用 Azure Bastion 时，VM 不需要客户端、代理或其他软件。 也可使用 SSH 连接到 Windows VM。 有关信息，请参阅[创建与 Windows VM 的 SSH 连接](bastion-connect-vm-ssh-windows.md)。

Azure Bastion 为预配它的虚拟网络中的所有 VM 提供安全的连接。 使用 Azure Bastion 可防止虚拟机向外部公开 RDP/SSH 端口，同时仍然使用 RDP/SSH 提供安全访问。 有关详细信息，请参阅[什么是 Azure Bastion？](bastion-overview.md)。

## <a name="prerequisites"></a>先决条件

在开始之前，请验证是否满足以下条件：

* 一个已安装 Bastion 主机的 VNet。

  * 请确保已为 VM 所在的虚拟网络设置 Azure Bastion 主机。 在虚拟网络中预配和部署 Bastion 服务后，便可以使用它连接到此虚拟网络中的任何 VM。 
  * 若要设置 Azure Bastion 主机，请参阅[创建堡垒主机](tutorial-create-host-portal.md#createhost)。 如果计划配置自定义端口值，请确保在配置 Bastion 时选择标准 SKU。

* 虚拟网络中的 Windows 虚拟机。

### <a name="required-roles"></a>必需的角色

* 虚拟机上的读者角色。
* NIC 上的读者角色（使用虚拟机的专用 IP）。
* Azure Bastion 资源上的读者角色。

### <a name="ports"></a>端口

若要连接到 Windows VM，必须在 Windows VM 上打开以下端口：

*   入站端口：RDP (3389) 或
*   入站端口：自定义值（然后，你需要在通过 Azure Bastion 连接到 VM 时指定此自定义端口）

> [!NOTE]
> 如果要指定自定义端口值，则必须使用标准 SKU 对 Azure Bastion 进行配置。 基本 SKU 不允许指定自定义端口。 标准 SKU 目前为预览状态。
>

## <a name="connect"></a><a name="rdp"></a>连接

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。
