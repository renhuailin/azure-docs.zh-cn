---
title: 使用 SSH 连接到 Windows VM
titleSuffix: Azure Bastion
description: 了解如何使用 Azure Bastion 通过 SSH 连接到 Windows VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 4e08aed1b1b91f55a12f5920dfff898cd0109bae
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278588"
---
# <a name="create-an-ssh-connection-to-a-windows-vm-using-azure-bastion-preview"></a>使用 Azure Bastion 创建到 Windows VM 的 SSH 连接（预览）

本文演示如何直接通过 Azure 门户安全无缝地与 Azure 虚拟网络中的 Windows VM 建立 RDP 连接。 使用 Azure Bastion 时，VM 不需要客户端、代理或其他软件。 也可使用 RDP 连接到 Windows VM。 有关信息，请参阅[创建到 Windows VM 的 RDP 连接](bastion-connect-vm-rdp-windows.md)。

Azure Bastion 为预配它的虚拟网络中的所有 VM 提供安全的连接。 使用 Azure Bastion 可防止虚拟机向外部公开 RDP/SSH 端口，同时仍然使用 RDP/SSH 提供安全访问。 有关详细信息，请参阅[什么是 Azure Bastion？](bastion-overview.md)。

> [!NOTE]
> 如果要创建到 Windows VM 的 SSH 连接，则必须使用标准 SKU 对 Azure Bastion 进行配置。 标准 SKU 目前为预览状态。
>

使用 SSH 连接到 Windows 虚拟机时，可以使用用户名/密码和 SSH 密钥进行身份验证。 可通过以下方法之一使用 SSH 密钥连接到 VM：

* 手动输入的私钥
* 包含私钥信息的文件

SSH 私钥必须采用以 `"-----BEGIN RSA PRIVATE KEY-----"` 开头并以 `"-----END RSA PRIVATE KEY-----"` 结尾的格式。

## <a name="prerequisites"></a>先决条件

请确保已为 VM 所在的虚拟网络设置 Azure Bastion 主机。 有关详细信息，请参阅[创建 Azure Bastion 主机](tutorial-create-host-portal.md)。 在虚拟网络中预配和部署 Bastion 服务后，便可以使用它连接到此虚拟网络中的任何 VM。 

若要通过 SSH 连接到 Windows 虚拟机，还必须确保：
* Windows 虚拟机运行 Windows Server 2019 或更高版本
* 在 Windows 虚拟机上安装并运行 OpenSSH 服务器。 若要了解如何执行此操作，请参阅[安装 OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse)。
* Azure Bastion 已配置为使用标准 SKU。

### <a name="required-roles"></a>必需的角色

需要使用以下角色进行连接：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

### <a name="ports"></a>端口

若要通过 SSH 连接到 Windows VM，必须在 VM 上打开以下端口：

* 入站端口：SSH (22) 或
* 入站端口：自定义值（然后，你需要在通过 Azure Bastion 连接到 VM 时指定此自定义端口）

### <a name="supported-configurations"></a>支持的配置

目前，Azure Bastion 仅支持使用 OpenSSH 通过 SSH 连接到 Windows VM。

## <a name="connect-using-username-and-password"></a><a name="username"></a>连接：使用用户名和密码

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Azure 门户中虚拟机概述的屏幕截图，已选中“连接”。" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::

1. 选择 Bastion 后，接着选择“使用 Bastion”。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](./quickstart-host-portal.md)。
1. 在“使用 Azure Bastion 进行连接”页上，展开“连接设置”部分，然后选择“SSH”  。 如果计划使用不同于标准 SSH 端口 (22) 的入站端口，请输入“端口”。

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings.png" alt-text="显示“连接设置”的屏幕截图。" lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings.png":::

1. 输入“用户名”和“密码”，然后选择“连接”以连接到 VM。  

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication.png" alt-text="密码身份验证的屏幕截图。" lightbox="./media/bastion-connect-vm-ssh-windows/authentication.png":::

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>连接：手动输入私钥

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="屏幕截图显示了 Azure 门户中虚拟机的概览，其中已选择“连接”。" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. 选择“Bastion”后，单击“使用 Bastion”。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](./quickstart-host-portal.md)。
1. 在“使用 Azure Bastion 进行连接”页上，展开“连接设置”部分，然后选择“SSH”  。 如果计划使用不同于标准 SSH 端口 (22) 的入站端口，请输入“端口”。

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-manual.png" alt-text="“连接设置”的屏幕截图。" lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-manual.png":::

1. 输入“用户名”和“SSH 私钥”。 将私钥输入到文本区域“SSH 私钥”中（或直接进行粘贴）。

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication-manual.png" alt-text="SSH 密钥身份验证的屏幕截图。" lightbox="./media/bastion-connect-vm-ssh-windows/authentication-manual.png":::

1. 选择“连接”以连接到 VM。

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>连接：使用私钥文件

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="屏幕截图描绘了 Azure 门户中虚拟机的概述，已选中“连接”" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. 选择“Bastion”后，单击“使用 Bastion”。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](./quickstart-host-portal.md)。
1. 在“使用 Azure Bastion 进行连接”页上，展开“连接设置”部分，然后选择“SSH”  。 如果计划使用不同于标准 SSH 端口 (22) 的入站端口，请输入“端口”。

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-file.png" alt-text="描述“连接设置”的屏幕截图。" lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-file.png":::

1. 输入“用户名”和“本地文件中的 SSH 私钥”。 通过浏览方式找到该文件，然后选择“打开”。

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication-file.png" alt-text="屏幕截图描述了 SSH 密钥文件。" lightbox="./media/bastion-connect-vm-ssh-windows/authentication-file.png":::

1. 选择“连接”以连接到 VM。 单击“连接”后，可连接到此虚拟机的 SSH 将直接在 Azure 门户中打开。 此连接通过 HTML5 在虚拟机的专用 IP 上使用 Bastion 服务的端口 443 进行。

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>连接：使用存储在 Azure Key Vault 中的私钥

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="屏幕截图是 Azure 门户中的虚拟机概述，已选中“连接”。" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. 选择“Bastion”后，单击“使用 Bastion”。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](./quickstart-host-portal.md)。
1. 在“使用 Azure Bastion 进行连接”页上，展开“连接设置”部分，然后选择“SSH”  。 如果计划使用不同于标准 SSH 端口 (22) 的入站端口，请输入“端口”。

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-akv.png" alt-text="显示“连接设置”的屏幕截图。" lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-akv.png":::

1. 输入“用户名”并选择“Azure Key Vault 中的 SSH 私钥” 。

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/ssh-key-vault.png" alt-text="屏幕截图，显示了来自 Azure Key Vault 的 SSH 私钥。":::
1. 选择“Azure Key Vault”下拉列表，并选择在其中存储 SSH 私钥的资源。 

   * 如果未设置 Azure Key Vault 资源，请参阅[创建密钥保管库](../key-vault/secrets/quick-create-powershell.md)并将 SSH 私钥存储为新 Key Vault 机密的值。
   * 确保对 Key Vault 中存储的机密具有“列出”和“获取”权限 。 若要为 Key Vault 资源分配访问策略和修改该资源的访问策略，请参阅[分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)。

      >[!NOTE]
      >请通过 PowerShell 或 Azure CLI 体验将 SSH 私钥作为机密存储在 Azure Key Vault 中。 通过 Azure Key Vault 门户体验存储私钥会干扰格式设置并导致登录失败。 如果确实已使用门户体验将私钥存储为机密，并且不再有权访问原始私钥文件，请参阅[更新 SSH 密钥](../virtual-machines/extensions/vmaccess.md#update-ssh-key)以使用新的 SSH 密钥对更新对目标 VM 的访问权限。
      >

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/private-key-stored.png" alt-text="屏幕截图，显示了 Azure Key Vault。" lightbox="./media/bastion-connect-vm-ssh-windows/private-key-stored.png":::

1. 选择“Azure Key Vault 机密”下拉列表，然后选择包含 SSH 私钥值的 Key Vault 机密。
1. 选择“连接”以连接到 VM。 单击“连接”后，可连接到此虚拟机的 SSH 将直接在 Azure 门户中打开。 此连接通过 HTML5 在虚拟机的专用 IP 上使用 Bastion 服务的端口 443 进行。

## <a name="next-steps"></a>后续步骤

有关 Azure Bastion 的详细信息，请参阅 [Bastion 常见问题解答](bastion-faq.md)。