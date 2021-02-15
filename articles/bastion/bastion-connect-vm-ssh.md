---
title: 使用 Azure Bastion 连接到 Linux VM
description: 本文介绍如何使用 Azure Bastion 连接到 Linux 虚拟机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 5d61c2a1a0f5d7b26809621af6dfa88cf5080320
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518175"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>使用 Azure Bastion 通过 SSH 连接到 Linux 虚拟机

本文介绍如何在 Azure 虚拟网络中通过 SSH 安全无缝地连接到 Linux VM。 可直接从 Azure 门户连接到 VM。 使用 Azure Bastion 时，VM 不需要客户端、代理或其他软件。 有关 Azure Bastion 的详细信息，请参阅[概述](bastion-overview.md)。

可以使用 Azure Bastion 通过 SSH 连接到 Linux 虚拟机。 可以同时使用用户名/密码和 SSH 密钥进行身份验证。 可通过以下方法之一使用 SSH 密钥连接到 VM：

* 手动输入的私钥
* 包含私钥信息的文件

SSH 私钥必须采用以 `"-----BEGIN RSA PRIVATE KEY-----"` 开头并以 `"-----END RSA PRIVATE KEY-----"` 结尾的格式。

## <a name="prerequisites"></a>必备条件

请确保已为 VM 所在的虚拟网络设置 Azure Bastion 主机。 有关详细信息，请参阅[创建 Azure Bastion 主机](./tutorial-create-host-portal.md)。 在虚拟网络中预配和部署 Bastion 服务后，便可以使用它连接到此虚拟网络中的任何 VM。 

使用 Bastion 进行连接时，它假设你使用 RDP 连接到 Windows VM，使用 SSH 连接到 Linux VM。 若要了解如何连接到 Windows VM，请参阅[连接到 VM - Windows](bastion-connect-vm-rdp.md)。

### <a name="required-roles"></a>必需的角色

需要使用以下角色进行连接：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

### <a name="ports"></a>端口

若要通过 SSH 连接到 Linux VM，必须在 VM 上打开以下端口：

* 入站端口：SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>连接：使用用户名和密码

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="屏幕截图显示了在选择了 &quot;连接&quot; Azure 门户中虚拟机的概述":::
1. 选择 "堡垒" 后，会出现一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了 Bastion，则默认情况下“Bastion”选项卡处于活动状态。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](./tutorial-create-host-portal.md)。

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="屏幕截图显示选定了堡垒的 &quot;连接到虚拟机&quot; 对话框":::
1. 输入用于通过 SSH 连接到虚拟机的用户名和密码。
1. 输入密钥后，选择 " **连接** " 按钮。

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>连接：手动输入私钥

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="屏幕截图显示了在选择了 &quot;连接&quot; Azure 门户中虚拟机的概述":::
1. 选择 "堡垒" 后，会出现一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了 Bastion，则默认情况下“Bastion”选项卡处于活动状态。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](./tutorial-create-host-portal.md)。

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="选中 &quot;连接到虚拟机&quot; 对话框。":::
1. 输入用户名，并选择“SSH 私钥”。
1. 将私钥输入到文本区域“SSH 私钥”中（或直接进行粘贴）。
1. 输入密钥后，选择 " **连接** " 按钮。

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>连接：使用私钥文件

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="所选连接":::
1. 选择 "堡垒" 后，会出现一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了 Bastion，则默认情况下“Bastion”选项卡处于活动状态。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](./tutorial-create-host-portal.md)。

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="已选择堡垒。":::
1. 输入用户名，并选择“本地文件中的 SSH 私钥”。
1. 选择 " **浏览** " 按钮 (本地文件) 中的文件夹图标。
1. 浏览文件，然后选择 " **打开**"。
1. 选择 " **连接** " 以连接到 VM。 单击“连接”后，可连接到此虚拟机的 SSH 将直接在 Azure 门户中打开。 此连接通过 HTML5 在虚拟机的专用 IP 上使用 Bastion 服务的端口 443 进行。

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>连接：使用存储在 Azure Key Vault 中的私钥

>[!NOTE]
>此功能的门户更新当前正在向区域推出。
>

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。
1. 选择 "堡垒" 后，会出现一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了 Bastion，则默认情况下“Bastion”选项卡处于活动状态。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](bastion-create-host-portal.md)。

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="“Bastion”选项卡":::
1. 输入用户名并 **从 Azure Key Vault 中选择 "SSH 私钥**"。
1. 选择 **Azure Key Vault** 下拉列表，并选择存储 SSH 私钥的资源。 如果未设置 Azure Key Vault 资源，请参阅 [创建密钥保管库](../key-vault/general/quick-create-portal.md) 并将 SSH 私钥存储为新 Key Vault 机密的值。

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure 密钥保管库":::

请确保你具有 **列表** 并 **可访问 Key Vault** 资源中存储的机密。 若要为 Key Vault 资源分配和修改访问策略，请参阅 [分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)。

1. 选择 " **Azure Key Vault 密钥** " 下拉列表，并选择包含 SSH 私钥值的 Key Vault 机密。
1. 选择 " **连接** " 以连接到 VM。 单击“连接”后，可连接到此虚拟机的 SSH 将直接在 Azure 门户中打开。 此连接通过 HTML5 在虚拟机的专用 IP 上使用 Bastion 服务的端口 443 进行。

## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)
