---
title: 快速入门：在 Azure 中创建 Linux SQL Server VM
description: 本教程介绍如何在 Azure 门户中创建 Linux SQL Server 2017 虚拟机。
services: virtual-machines-sql
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 92969e95972586451fc2b5da20da2bf284191b3b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745724"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>在 Azure 门户中预配运行 SQL Server 的 Linux 虚拟机
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

在本快速入门教程中，你将使用 Azure 门户创建装有 SQL Server 2017 的 Linux 虚拟机。 学习以下内容： 


* [从库中创建运行 SQL Server 的 Linux VM](#create)
* [使用 ssh 连接到新的 VM](#connect)
* [更改 SA 密码](#password)
* [针对远程连接进行配置](#remote)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a> 创建安装了 SQL Server 的 Linux VM

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在左窗格中，选择“创建资源”。

1. 在“创建资源”窗格中选择“计算”。

1. 选择“特色”标题旁边的“全部查看”。 

   ![查看所有 VM 映像](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. 在搜索框中键入 SQL Server 2019，然后按 Enter 开始搜索 。

1. 选择“操作系统” > “Redhat”来限制搜索结果。 

    ![SQL Server 2019 VM 映像的搜索筛选器](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. 从搜索结果中选择 SQL Server 2019 Linux 映像。 本教程使用 RHEL74 上的 SQL Server 2019。

   > [!TIP]
   > Developer 版允许使用 Enterprise 版的功能进行测试或开发，但没有 SQL Server 许可费用。 只需支付运行 Linux VM 的费用。

1. 选择“创建”。 


### <a name="set-up-your-linux-vm"></a>设置 Linux VM

1. 在“基本信息”选项卡中，选择自己的 **订阅** 和 **资源组**。 

    ![“基本信息”窗口](./media/sql-vm-create-portal-quickstart/basics.png)

1. 在“虚拟机名称”中，输入新 Linux VM 的名称。
1. 然后键入或选择以下值：
   * **区域**：选择合适的 Azure 区域。
   * **可用性选项**：选择最适合应用和数据的可用性与冗余选项。
   * **更改大小**：选择此选项以选择计算机大小，完成后，选择“选择”。 有关 VM 计算机大小的详细信息，请参阅 [VM 大小](../../../virtual-machines/sizes.md)。

     ![选择 VM 大小](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > 对于开发和功能测试，请使用 **DS2** 或更大的 VM 大小。 若要进行性能测试，则至少使用“DS13”。

   * **身份验证类型**：选择“SSH 公钥”。

     > [!Note]
     > 可以选择使用“SSH 公钥”或“密码”进行身份验证。 SSH 更安全。 有关如何生成 SSH 密钥的说明，请参阅[在 Linux 和 Mac 上为 Azure 中的 Linux VM 创建 SSH 密钥](../../../virtual-machines/linux/mac-create-ssh-keys.md)。

   * **用户名**：输入 VM 的管理员名称。
   * **SSH 公钥**：输入 RSA 公钥。
   * **公共入站端口**：选择“允许所选的端口”，然后在“选择公共入站端口”列表中选择“SSH (22)”端口。   在本快速入门中，必须执行此步骤才能建立连接并完成 SQL Server 配置。 如果要远程连接到 SQL Server，则需要在创建虚拟机后，手动允许通过 Internet 进行连接的 Microsoft SQL Server 使用的默认端口 (1433) 的流量。

     ![入站端口](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. 在后面的其他选项卡中对设置进行任何所需的更改，或保留默认设置。
    * **磁盘**
    * **联网**
    * **Management**
    * **来宾配置**
    * **标记**

1. 选择“查看 + 创建”。
1. 在“查看 + 创建”窗格中，选择“创建”。 

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> 连接到 Linux VM

如果已使用 BASH shell，请通过 ssh 命令连接到 Azure VM。 在以下命令中，替换连接到 Linux VM 所需的 VM 用户名和 IP 地址。

```bash
ssh azureadmin@40.55.55.555
```

可以在 Azure 门户中找到 VM 的 IP 地址。

![Azure 门户中的 IP 地址](./media/sql-vm-create-portal-quickstart/vmproperties.png)

如果在 Windows 上运行且没有 BASH shell，请安装 SSH 客户端，例如 PuTTY。

1. [下载并安装 PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. 运行 PuTTY。

1. 在 PuTTY 配置屏幕上，输入 VM 的公共 IP 地址。

1. 选择“打开”，并根据提示输入用户名和密码。

若要详细了解如何连接到 Linux VM，请参阅[使用门户在 Azure 上创建 Linux VM](../../../virtual-machines/linux/quick-create-portal.md)。

> [!NOTE]
> 如果出现有关不会在注册表中缓存服务器主机密钥的 PuTTY 安全警报，请从以下选项中进行选择。 如果你信任此主机，请选择“是”将密钥添加到 PuTTy 缓存并继续进行连接。 如果你只想建立连接一次，而无需将密钥添加到缓存，请选择“否”。 如果你不信任此主机，请选择“取消”以放弃连接。

## <a name="change-the-sa-password"></a><a id="password"></a> 更改 SA 密码

新的虚拟机使用随机 SA 密码安装 SQL Server。 重置此密码，然后使用 SA 登录名连接到 SQL Server。

1. 连接到 Linux VM 以后，请打开新的命令终端。

1. 使用以下命令更改 SA 密码：

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   出现提示时，输入新的 SA 密码和密码确认。

1. 重启 SQL Server 服务。

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>将工具添加到路径（可选）

默认安装了多个 SQL Server [包](sql-server-on-linux-vm-what-is-iaas-overview.md#packages)，包括 SQL Server 命令行工具包。 工具包包含 sqlcmd 和 bcp 工具。 为了方便，可以选择将工具路径 `/opt/mssql-tools/bin/` 添加到 PATH 环境变量。

1. 运行以下命令以修改登录会话和交互式/非登录会话的路径：

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> 针对远程连接进行配置

如果需要远程连接到 Azure VM 上的 SQL Server，必须在网络安全组上配置入站规则。 该规则允许 SQL Server 所侦听的端口（默认为 1433）上的流量。 以下步骤演示如何使用此步骤所对应的 Azure 门户。

> [!TIP]
> 如果在预配过程中已在设置中选择了入站端口“MS SQL (1433)”，则已进行这些更改。 有关如何配置防火墙，可以转到下一部分。

1. 在门户中选择“虚拟机”，然后选择 SQL Server VM。
1. 在左侧窗格中的“设置”下，选择“网络”。
1. 在“网络”窗口中，选择“入站端口规则”下的“添加入站端口”。 

   ![入站端口规则](./media/sql-vm-create-portal-quickstart/networking.png)

1. 在“服务”列表中，选择“MS SQL”。

    ![MS SQL 安全组规则](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. 单击“确定”保存 VM 的规则。

### <a name="open-the-firewall-on-rhel"></a>打开 RHEL 上的防火墙

本教程介绍了如何创建 Red Hat Enterprise Linux (RHEL) VM。 若要远程连接到 RHEL VM，则还需在 Linux 防火墙上打开端口 1433。

1. [连接](#connect)到 RHEL VM。

1. 在 BASH shell 中运行以下命令：

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>后续步骤

在 Azure 中有了 SQL Server 2017 虚拟机以后，即可使用 sqlcmd 进行本地连接，以便运行 Transact-SQL 查询。

如果已将 Azure VM 配置为建立远程 SQL Server 连接，则应可建立远程连接。 若要通过示例来了解如何从 Windows 远程连接到 Linux 上的 SQL Server，请参阅[使用 Windows 上的 SSMS 连接到 Linux 上的 SQL Server](/sql/linux/sql-server-linux-develop-use-ssms)。 若要通过 Visual Studio Code 进行连接，请参阅[使用 Visual Studio Code 创建和运行 SQL Server 的 Transact-SQL 脚本](/sql/linux/sql-server-linux-develop-use-vscode)

有关 Linux 上的 SQL Server 的其他一般信息，请参阅 [Linux 上的 SQL Server 2017 概述](/sql/linux/sql-server-linux-overview)。 若要详细了解如何使用 SQL Server 2017 Linux 虚拟机，请参阅 [Azure 上的 SQL Server 2017 虚拟机概述](sql-server-on-linux-vm-what-is-iaas-overview.md)。