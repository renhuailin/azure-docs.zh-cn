---
title: 为迁移准备 Windows Server 2003 服务器 Azure Migrate
description: 了解如何为迁移准备 Windows Server 2003 服务器，并 Azure Migrate。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753735"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>为迁移准备 Windows Server 2003 计算机

本文介绍如何准备运行 Windows Server 2003 的计算机迁移到 Azure。 


> [!NOTE]
> [Windows Server 2003 扩展支持](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) 于2015年7月14日结束。  Azure 支持团队将继续帮助排查在 Azure 上运行 Windows Server 2003 时遇到的问题。 但是，这种支持仅限于不需要操作系统级故障排除或修补程序的问题。 建议将应用程序迁移到运行较新版本 Windows Server 的 Azure 实例，以确保有效地利用 Azure 云的灵活性和可靠性。 但是，如果你仍选择将 Windows Server 2003 迁移到 Azure，则可以使用 Azure Migrate： Server 迁移工具（如果你的 Windows Server 是在 VMware 或 Hyper-v 上运行的虚拟机）。


- 你可以使用无代理迁移将 [Hyper-v vm](tutorial-migrate-hyper-v.md) 和 [VMware Vm](tutorial-migrate-vmware.md) 迁移到 Azure。
- 若要在迁移后连接到 Azure Vm，Hyper-v Integration Services 必须安装在 Azure VM 上。 默认情况下，Windows Server 2003 计算机未安装此安装程序。
- 没有直接下载链接可用于安装 Hyper-v Integration Services，因此，你需要执行以下操作：
    - 对于未安装它的 Hyper-v Vm，请在运行带有 Hyper-v 角色的 Windows Server 2012 R2/Windows Server 2012 的计算机上提取 Integration Services 的安装文件，然后将该安装程序复制到 Windows Server 2003 计算机。 安装文件在运行 Windows Server 2016 的计算机上不可用。
    - 对于 VMware Vm，请创建一个启动任务，该任务将在迁移后启动 Azure VM 时安装 Integration Services。


## <a name="install-on-hyper-v-vms"></a>在 Hyper-v Vm 上安装

迁移之前，请检查是否已安装 Hyper-v Integration Services，并根据需要进行安装。

1. 按照 [这些说明](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) 检查是否已安装。
2. 如果未安装，请使用 Hyper-v 角色登录到运行 Windows Server 2012 R2/Windows Server 2012 的计算机。
3. 导航到 **C:\Windows\System32\vmguest.iso** 上的安装文件，并装载该文件。
2. 将安装文件夹复制到 Windows Server 2003 计算机上，然后安装 Integration Services。
4. 安装后，可以在 Integration Services 中保留默认设置。 

## <a name="install-on-vmware-vms"></a>在 VMware Vm 上安装

1. 使用 Hyper-v 角色登录到运行 Windows Server 2012 R2/Windows Server 2012 的计算机。
2. 导航到 **C:\Windows\System32\vmguest.iso** 上的安装文件，并装载该文件。
3. 将安装文件夹复制到 VMware VM。
4. 从 VM 上的命令行运行 ```gpedit.msc``` 。
5. 打开 **计算机配置**  >  **Windows 设置**  >  **脚本 (启动/关闭)**。
6. 在 "**启动**"  >  "**添加**  >  **脚本名称**" 中，键入 setup.exe 地址。
7. 迁移到 Azure 后，该脚本将在 Azure VM 首次启动时运行。
8. 手动重新启动 Azure VM。 启动诊断弹出窗口指示需要重新启动。
9. 脚本运行后，Hyper-v Integration Services 安装在 Azure VM 上，你可以从启动中删除该脚本。
10. 安装后，可以在 Integration Services 中保留默认设置。 

## <a name="next-steps"></a>后续步骤

- 查看 [VMware](migrate-support-matrix-vmware-migration.md) 和 [hyper-v](migrate-support-matrix-hyper-v-migration.md) vm 的迁移要求。
- 迁移 [VMware](server-migrate-overview.md) 和 [hyper-v](tutorial-migrate-hyper-v.md) vm。
