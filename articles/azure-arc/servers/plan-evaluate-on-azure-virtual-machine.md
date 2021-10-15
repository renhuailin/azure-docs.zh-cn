---
title: 如何使用 Azure VM 评估已启用 Azure Arc 的服务器
description: 了解如何使用 Azure 虚拟机评估已启用 Azure Arc 的服务器。
ms.date: 10/01/2021
ms.topic: conceptual
ms.openlocfilehash: 54656e0701857fd3badbcec619b2185917935857
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389810"
---
# <a name="evaluate-azure-arc-enabled-servers-on-an-azure-virtual-machine"></a>在 Azure 虚拟机上评估已启用 Azure Arc 的服务器

已启用 Azure Arc 的服务器旨在帮助将本地运行或在其他云中运行的服务器连接到 Azure。 通常不会在 Azure 虚拟机上使用已启用 Azure Arc 的服务器，因为这些 VM 可以本机使用所有相同的功能，包括 Azure 资源管理器中 VM 的表示形式、VM 扩展、托管标识和 Azure Policy。 如果尝试在 Azure VM 上安装已启用 Azure Arc 的服务器，则会收到一条错误消息，指出该服务器不受支持并且代理安装将取消。

对于生产方案，虽然不能在 Azure VM 上安装已启用 Azure Arc 的服务器，但可以将已启用 Azure Arc 的服务器配置为在 Azure VM 上运行，以便仅用于评估和测试目的。 本文将帮助你设置 Azure VM，然后即可在上面启用已启用 Azure Arc 的服务器。

> [!NOTE]
> 本文中的步骤适用于托管在 Azure 云中的虚拟机。 Azure Stack Hub 或 Azure Stack Edge 上运行的虚拟机不支持已启用 Azure Arc 的服务器。

## <a name="prerequisites"></a>先决条件

* 已为帐户分配[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色。
* Azure 虚拟机正在运行[已启用 Azure Arc 的服务器支持的操作系统](agent-overview.md#supported-operating-systems)。 如果没有 Azure VM，可以部署[简单的 Windows VM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json) 或[简单的 Ubuntu Linux 18.04 LTS VM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json)。
* Azure VM 可以出站通信，以便从 [Microsoft 下载中心](https://aka.ms/AzureConnectedMachineAgent)下载适用于 Windows 的 Azure Connected Machine 代理包，以及从 Microsoft [包存储库](https://packages.microsoft.com/)下载适用于 Linux 的 Azure Connected Machine 代理包。 如果根据 IT 安全策略限制到 Internet 的出站连接，则需要手动下载代理包，并将其复制到 Azure VM 上的文件夹。
* 具有提升权限的帐户（即管理员或 root 帐户），以及对 VM 的 RDP 或 SSH 访问权限。
* 要使用已启用 Azure Arc 的服务器注册和管理 Azure VM，你需要是资源组中的 [Azure Connected Machine 资源管理员](../../role-based-access-control/built-in-roles.md#azure-connected-machine-resource-administrator)或具有[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色。

## <a name="plan"></a>计划

若要开始将 Azure VM 作为已启用 Azure Arc 的服务器进行管理，需要先对 Azure VM 进行以下更改，然后才能安装和配置已启用 Azure Arc 的服务器。

1. 删除部署到 Azure VM 的任何 VM 扩展，如 Log Analytics 代理。 虽然已启用 Azure Arc 的服务器与 Azure VM 支持许多相同的扩展，但已启用 Azure Arc 的服务器代理无法管理已部署到 VM 的 VM 扩展。

2. 禁用 Azure Windows 或 Linux 来宾代理。 Azure VM 来宾代理与已启用 Azure Arc 的服务器 Connected Machine 代理具有相似用途。 为避免这两者之间的冲突，需要禁用 Azure VM 代理。 禁用后，将无法使用 VM 扩展或某些 Azure 服务。

3. 创建安全规则以拒绝访问 Azure 实例元数据服务 (IMDS)。 IMDS 是一个 REST API，应用程序可以调用它来获取 Azure 中 VM 的表示形式的相关信息，包括其资源 ID 和位置。 IMDS 还提供对分配到计算机的任何管理标识的访问权限。 已启用 Azure Arc 的服务器提供其自己的 IMDS 实现，并返回 VM 的 Azure Arc 表示形式的相关信息。 若要避免这两个 IMDS 终结点都可用且应用必须在两者之间进行选择的情况，可阻止访问 Azure VM IMDS，仅使已启用 Azure Arc 的服务器 IMDS 实现可用。

执行这些更改后，Azure VM 的行为将类似于 Azure 之外的任何计算机或服务器，并且可以开始安装和评估已启用 Azure Arc 的服务器。

当在 VM 上配置已启用 Azure Arc 的服务器时，Azure 中会出现它的两个表示形式。 一个是 Azure VM 资源，具有 `Microsoft.Compute/virtualMachines` 资源类型，另一个是 Azure Arc 资源，具有 `Microsoft.HybridCompute/machines` 资源类型。 由于阻止从共享物理主机服务器管理来宾操作系统，理解这两个资源的最佳方式是，Azure VM 资源是 VM 的虚拟硬件，使你可以控制电源状态并查看有关其 SKU、网络和存储配置的信息。 Azure Arc 资源管理该 VM 中的来宾操作系统，并可用于安装扩展、查看 Azure Policy 的符合性数据，以及完成已启用 Azure Arc 的服务器支持的其他任务。

## <a name="reconfigure-azure-vm"></a>重新配置 Azure VM

1. 删除 Azure VM 上的任何 VM 扩展。

   在 Azure 门户中，导航到 Azure VM 资源，然后从左侧窗格中选择“扩展”。 如果 VM 上安装了任何扩展，请单独选择每个扩展，然后选择“卸载”。 等待所有扩展完成卸载，然后再继续执行第 2 步。

2. 禁用 Azure VM 来宾代理。

   若要禁用 Azure VM 来宾代理，需使用远程桌面连接 (Windows) 或 SSH (Linux) 连接到 VM。 连接后，运行以下命令以禁用来宾代理。

   对于 Windows，请运行以下 PowerShell 命令：

   ```powershell
   Set-Service WindowsAzureGuestAgent -StartupType Disabled -Verbose
   Stop-Service WindowsAzureGuestAgent -Force -Verbose
   ```

   对于 Linux，请运行以下命令：

   ```bash
   current_hostname=$(hostname)
   sudo service walinuxagent stop
   sudo waagent -deprovision -force
   sudo rm -rf /var/lib/waagent
   sudo hostnamectl set-hostname $current_hostname
   ```

3. 阻止访问 Azure IMDS 终结点。

   在仍连接到服务器时，运行以下命令来阻止访问 Azure IMDS 终结点。 对于 Windows，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule -Name BlockAzureIMDS -DisplayName "Block access to Azure IMDS" -Enabled True -Profile Any -Direction Outbound -Action Block -RemoteAddress 169.254.169.254
   ```

   对于 Linux，请参阅分发版文档，了解阻止通过 TCP 端口 80 对 `169.254.169.254/32` 进行出站访问的最佳方法。 通常将使用内置防火墙阻止出站访问，但也可使用 iptable 或 nftables 来暂时阻止出站访问 。

   如果 Azure VM 正在运行 Ubuntu，请执行以下步骤来配置其简单的防火墙 (UFW)：

   ```bash
   sudo ufw --force enable
   sudo ufw deny out from any to 169.254.169.254
   sudo ufw default allow incoming
   ```

   如果 Azure VM 运行 CentOS、Red Hat 或 SUSE Linux Enterprise Server (SLES)，请执行以下步骤来配置防火墙：

   ```bash
   firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 1 -p tcp -d 169.254.169.254 -j DROP
   firewall-cmd --reload
   ```

   对于其他分发版，请参阅防火墙文档或使用以下命令配置通用 iptables 规则：

   ```bash
   iptables -A OUTPUT -d 169.254.169.254 -j DROP
   ```

   > [!NOTE]
   > 每次重启后都需要设置 iptables 配置，除非使用永久性 iptables 解决方案。


4. 安装和配置已启用 Azure Arc 的服务器代理。

   VM 现在已可开始评估已启用 Azure Arc 的服务器。 若要安装和配置已启用 Azure Arc 的服务器代理，请参阅[使用 Azure 门户连接混合计算机](onboard-portal.md)，然后按照相应步骤生成安装脚本并使用脚本方法进行安装。

   > [!NOTE]
   > 如果从 Azure VM 到 Internet 的出站连接受到限制，则需要手动下载代理包。 将代理包复制到 Azure VM，并修改已启用 Azure Arc 的服务器安装脚本以引用源文件夹。

如果缺少其中一个步骤，安装脚本会检测到它正在 Azure VM 上运行，然后终止并提示错误。 请验证是否已完成步骤 1-3，然后重新运行该脚本。

## <a name="verify-the-connection-with-azure-arc"></a>验证是否与 Azure Arc 连接

安装并配置要注册到已启用 Azure Arc 的服务器的代理后，转到 Azure 门户以验证是否已成功连接服务器。 在 [Azure 门户](https://portal.azure.com)中查看你的计算机。

![服务器连接成功](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

* 了解[如何在已启用 Azure Arc 的服务器中计划并启用大量计算机](plan-at-scale-deployment.md)，以简化 Azure 中不可或缺的安全管理和监视功能的配置。

* 了解可用于借助其他 Azure 服务简化部署的[受支持 Azure VM 扩展](manage-vm-extensions.md)，这些服务包括自动化、KeyVault 以及适用于 Windows 或 Linux 计算机的其他服务。

* 完成测试后，请参阅[删除已启用 Azure Arc 的服务器代理](manage-agent.md#remove-the-agent)。
