---
title: 使用在基于 Linux 的 Azure 虚拟机上运行的 DH2i DxEnterprise 设置 Always On 可用性组
description: 将 DH2i DxEnterprise 用作群集管理器，以在 Linux Azure 虚拟机上的 SQL Server 上通过可用性组实现高可用性
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 8791d845b822f365e47482b0215f1ecac9fa0da9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732016"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>教程 - 使用在基于 Linux 的 Azure 虚拟机上运行的 DH2i DxEnterprise 设置三节点 Always On 可用性组

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本教程介绍如何使用在基于 Linux 的 Azure 虚拟机 (VM) 上运行的 DH2i DxEnterprise 配置 SQL Server Always On 可用性组。 

有关 DxEnterprise 的详细信息，请参阅 [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/)。

> [!NOTE]
> Microsoft 支持数据移动、可用性组和 SQL Server 组件。 请联系 DH2i 以获取与 DH2i DxEnterprise 群集文档相关的支持（针对群集和仲裁管理）。
 

本教程包含以下几个步骤：

> [!div class="checklist"]
> * 在所有将构成可用性组的 Azure 虚拟机 (VM) 上安装 SQL Server。
> * 在所有 VM 上安装 DxEnterprise 并配置 DxEnterprise 群集。
> * 创建虚拟主机以提供故障转移支持和高可用性，并向可用性组添加可用性组和数据库。
> * 为可用性组侦听程序创建内部 Azure 负载均衡器（可选）。
> * 执行手动或自动故障转移。

在本教程中，我们将使用 [DxAdmin 客户端 UI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/) 设置 DxEnterprise 群集。 （可选）还可以使用 [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) 命令行接口设置群集。 在此示例中，我们使用了四台 VM。 其中三台 VM 运行的是 Ubuntu 18.04，且属于三节点群集。 第四台 VM 运行的是 Windows 10，配有用于管理和配置群集的 DxAdmin 工具。

## <a name="prerequisites"></a>先决条件

- 在 Azure 中创建四台 VM。 请遵循[快速入门：在 Azure 门户中创建 Linux 虚拟机](../../../virtual-machines/linux/quick-create-portal.md)一文，创建基于 Linux 的虚拟机。 同样，要创建基于 Windows 的虚拟机，请遵循[快速入门：在 Azure 门户中创建 Windows 虚拟机](../../../virtual-machines/windows/quick-create-portal.md)一文。
- 在将构成群集的所有基于 Linux 的 VM 上安装 .NET 3.1。 根据选择的 Linux 操作系统，按照[此处](/dotnet/core/install/linux)所述的说明进行操作。
- 需要启用了可用性组管理功能的有效 DxEnterprise 许可证。 有关详细信息，请参阅 [DxEnterprise 免费试用版](https://dh2i.com/trial/)，了解如何获取免费试用版。

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>在所有将构成可用性组的 Azure VM 上安装 SQL Server

在本教程中，我们将设置一个运行可用性组的基于 Linux 的三节点群集。 根据选择的 Linux 平台，遵循[在 Linux 上安装 SQL Server](/sql/linux/sql-server-linux-overview#install) 文档。 我们还建议你为本教程安装 [SQL Server 工具](/sql/linux/sql-server-linux-setup-tools)。
 
> [!NOTE]
> 确保所选的 Linux 操作系统是 [DH2i DxEnterprise（请参阅“最低系统要求”部分）](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf)和 [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms) 均支持的通用发行版。
>
> 在此示例中，我们使用 DH2i DxEnterprise 和 Microsoft SQL Server 均支持的 Ubuntu 18.04。

在本教程中，我们不会在 Windows VM 上安装 SQL Server，因为该节点不会成为群集的一部分，并且仅用于使用 DxAdmin 管理群集。

完成此步骤后，应在要加入可用性组的所有三个基于 Linux 的 VM 上都安装 SQL Server 和 [SQL Server 工具](/sql/linux/sql-server-linux-setup-tools)（可选）。
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>在所有 VM 上安装 DxEnterprise 并配置群集

在此步骤中，我们将在三个 Linux VM 上安装用于 Linux 的 DH2i DxEnterprise。 下表描述了每个服务器在群集中扮演的角色：

| VM 数量 | DH2i DxEnterprise 角色 | Microsoft SQL Server 可用性组副本角色 |
|--|--|--|
| 1 | 群集节点 - 基于 Linux | 主要 |
| 1 | 群集节点 - 基于 Linux | 次要 - 同步提交 |
| 1 | 群集节点 - 基于 Linux | 次要 - 同步提交 |
| 1 | DxAdmin 客户端 | NA |


要在三个基于 Linux 的节点上安装 DxEnterprise，请遵循基于所选 Linux 操作系统的 DH2i DxEnterprise 文档。 使用下面列出的方法之一安装 DxEnterprise。

- **Ubuntu**
    - [存储库安装快速入门指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [扩展快速入门指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [市场映像快速入门指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [存储库安装快速入门指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [扩展快速入门指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [市场映像快速入门指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

若要仅在 Windows VM 上安装 DxAdmin 客户端工具，请遵循 [DxAdmin 客户端 UI 快速入门指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/)。

完成此步骤后，应在 Linux VM 上创建了 DxEnterprise 群集，并在 Windows 客户端计算机上安装了 DxAdmin 客户端。 

> [!NOTE]
> 你还可以创建一个三节点群集，其中一个节点被添加为“仅配置模式”，如[此处](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes)所述，以启用自动故障转移。 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>创建虚拟主机以提供故障转移支持和高可用性

在此步骤中，我们将创建一个虚拟主机、可用性组，然后添加一个数据库，所有这些操作都使用 DxAdmin UI。   

> [!NOTE]
> 在此步骤中，将重启 SQL Server 实例以启用 Always On。 

连接到运行 DxAdmin 的 Windows 客户端计算机以连接到在上述步骤中创建的群集。 请按照[使用 DxAdmin 的 MSSQL 可用性组](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/)中所述的步骤启用 Always On 并创建虚拟主机和可用性组。 

> [!TIP]
> 在添加数据库之前，请确保已在 SQL Server 的主实例上创建并备份数据库。  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>为侦听器创建内部 Azure 负载均衡器（可选）

在此可选步骤中，你可以创建和配置 Azure 负载均衡器，其中保留可用性组侦听程序的 IP 地址。 有关 Azure 负载均衡器的详细信息，请参阅 [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)。 若要使用 DxAdmin 配置 Azure 负载均衡器和可用性组侦听程序，请遵循 DxEnterprise [Azure负载均衡器快速入门指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/)。

完成此步骤后，应创建了一个可用性组侦听程序，并将其映射到了内部 Azure 负载均衡器。

## <a name="test-manual-or-automatic-failover"></a>测试手动或自动故障转移

对于自动故障转移测试，可以继续操作并关闭主要副本（从 Azure 门户关闭虚拟机）。 这将复制主节点突然不可用的情况。 预计的行为如下：
- 群集管理器会将可用性组中的其中一个次要副本提升为主要副本。
- 发生故障的主要副本在备份后会自动加入群集。 群集管理器会将其提升为次要副本。

 
还可以按照以下所述步骤执行手动故障转移：

1. 通过 DxAdmin 连接到群集   
1. 展开可用性组的虚拟主机
1. 右键单击目标节点/次要副本，然后选择“开始在成员上托管”以启动故障转移 

有关 DxEnterprise 中更多操作的详细信息，请访问 [DxEnterprise 管理员指南](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf)和 [DxEnterprise DxCLI 指南](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Linux 上的可用性组](/sql/linux/sql-server-linux-availability-group-overview)
- [快速入门：在 Azure 门户中创建 Linux 虚拟机](../../../virtual-machines/linux/quick-create-portal.md)
- [快速入门：在 Azure 门户中创建 Windows 虚拟机](../../../virtual-machines/windows/quick-create-portal.md)
- [Linux 上的 SQL Server 2019 支持的平台](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)