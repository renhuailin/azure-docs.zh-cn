---
title: 使用 RDP 连接到 Linux VM
titleSuffix: Azure Bastion
description: 了解如何使用 Azure Bastion 通过 RDP 连接到 Linux VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 9fbe16756bc178ed012af7f96e937614b0b3a855
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699330"
---
# <a name="create-an-rdp-connection-to-a-linux-vm-using-azure-bastion-preview"></a>使用 Azure Bastion（预览版）创建与 Linux VM 的 RDP 连接

本文演示如何直接通过 Azure 门户安全无缝地与 Azure 虚拟网络中的 Linux VM 建立 RDP 连接。 使用 Azure Bastion 时，VM 不需要客户端、代理或其他软件。 也可使用 SSH 连接到 Linux VM。 有关信息，请参阅[创建与 Linux VM 的 SSH 连接](bastion-connect-vm-ssh-linux.md)。

Azure Bastion 为预配它的虚拟网络中的所有 VM 提供安全的连接。 使用 Azure Bastion 可防止虚拟机向外部公开 RDP/SSH 端口，同时仍然使用 RDP/SSH 提供安全访问。 有关详细信息，请参阅[什么是 Azure Bastion？](bastion-overview.md)。

> [!NOTE]
> 使用 RDP 连接到 Linux 虚拟机需要 Azure Bastion 标准 SKU（当前为预览版）。
>

使用 Azure Bastion 通过 RDP 连接到 Linux 虚拟机时，必须使用用户名/密码进行身份验证。

## <a name="prerequisites"></a>先决条件

在开始之前，请验证是否满足以下条件：

请确保已为 VM 所在的虚拟网络设置 Azure Bastion 主机。 有关详细信息，请参阅[创建 Azure Bastion 主机](tutorial-create-host-portal.md)。 在虚拟网络中预配和部署 Bastion 服务后，便可以使用它连接到此虚拟网络中的任何 VM。

若要通过 RDP 连接到 Linux 虚拟机，还必须确保在 Linux 虚拟机上安装并配置 xrdp。 若要了解如何这样做，请参阅[将 xrdp 与 Linux 配合使用](../virtual-machines/linux/use-remote-desktop.md)。

### <a name="required-roles"></a>必需的角色

需要使用以下角色进行连接：
* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

### <a name="ports"></a>端口

若要通过 RDP 连接到 Linux VM，必须在 VM 上打开以下端口：
*   入站端口：RDP (3389) 或
*   入站端口：自定义值（然后，你需要在通过 Azure Bastion 连接到 VM 时指定此自定义端口）

### <a name="supported-configurations"></a>支持的配置

目前，Azure Bastion 仅支持使用 xrdp 通过 RDP 连接到 Linux VM。

## <a name="connect"></a><a name="rdp"></a>连接

[!INCLUDE [Connect to a Linux VM using RDP](../../includes/bastion-vm-rdp-linux.md)]
 
## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。
