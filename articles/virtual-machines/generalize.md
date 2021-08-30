---
title: 在创建映像之前通用化 VM
description: 在创建映像之前，通用化 VM 以删除计算机特定信息。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: 7b733043a6b9a8e68aad94aaa57baf5c4d3fd2aa
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128905"
---
# <a name="remove-machine-specific-information-by-generalizing-a-vm-before-creating-an-image"></a>在创建映像之前，通过通用化 VM，删除计算机特定的信息

在[共享映像库](shared-image-galleries.md#generalized-and-specialized-images)中创建映像时，不需要通用化 VM，除非特别需要创建通用化映像。 在库外创建托管映像时需要进行通用化。

进行通用化可删除计算机特定的信息，以便可以使用该映像创建多个 VM。 虚拟机通用化后，需要让平台知道 VM 已通用化，以便能够正确设置启动顺序。 VM 通用化后，就不应重启。


## <a name="linux"></a>Linux

首先，使用 Azure VM 代理取消预配 VM 以删除计算机特定文件和数据。 在源 Linux VM 上，使用带 `-deprovision+user` 参数的 `waagent` 命令。 有关详细信息，请参阅 [Azure Linux 代理用户指南](./extensions/agent-linux.md)。 此过程不可逆。

1. 使用 SSH 客户端连接到 Linux VM。
2. 在 SSH 窗口中，输入以下命令：
   ```bash
    sudo waagent -deprovision+user
   ```
   > [!NOTE]
   > 仅在将捕获为映像的 VM 上运行此命令。 此命令无法保证映像中的所有敏感信息均已清除，或该映像适合再分发。 `+user` 参数还会删除上次预配的用户帐户。 要保留 VM 中的用户帐户凭据，请仅使用 `-deprovision`。
 
3. 按 **y** 继续。 添加 `-force` 参数即可免除此确认步骤。
4. 该命令完成后，请输入“退出”以关闭 SSH 客户端。  VM 在此时仍会运行。


然后，需要在平台上将 VM 标记为已通用化。 

```azurecli-interactive
az vm generalize \
   --resource-group myResourceGroup \
   --name myVM
```

## <a name="windows"></a>Windows 

Sysprep 将删除所有个人帐户和安全信息，并准备好要用作映像的计算机。 有关 Sysprep 的信息，请参阅 [Sysprep 概述](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

确保 Sysprep 支持计算机上运行的服务器角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)和[不支持的方案](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)。 

> [!IMPORTANT]
> 在 VM 上运行 Sysprep 后，该 VM 将被视为已通用化而无法重启。 通用化 VM 的过程是不可逆的。 如果需要保持原始 VM 正常运行，请创建 [VM 的副本](./windows/create-vm-specialized.md#option-3-copy-an-existing-azure-vm)并将其副本通用化。 
>
> Sysprep 要求对驱动器进行完全解密。 如果在 VM 上启用了加密，请在运行 Sysprep 之前将其禁用。
>
> 如果计划在首次将虚拟硬盘 (VHD) 上传到 Azure 之前运行 Sysprep，请确保先[准备好 VM](./windows/prepare-for-upload-vhd-image.md)。  
> 
> 

若要将 Windows VM 通用化，请执行以下步骤：

1. 登录到 Windows VM。
   
2. 以管理员身份打开“命令提示符”窗口。 

3. 删除 panther 目录 (C:\Windows\Panther)。 然后将目录切换到 %windir%\system32\sysprep，然后运行 `sysprep.exe`。
   
4. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，并选中“通用”复选框。
   
5. 在“关机选项”中选择“关机”。
   
6. 选择“确定”。
   
    :::image type="content" source="windows/media/upload-generalized-managed/sysprepgeneral.png" alt-text="![Start Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)":::

6. Sysprep 在完成运行后会关闭 VM。 不要重新启动 VM。

> [!TIP]
> **可选** 使用 [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) 优化映像并减少 VM 的首次启动时间。
>
> 若要优化映像，请通过在 Windows 资源管理器中双击 VHD 来装载它，然后使用 `/optimize-image` 参数运行 DISM。
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> 其中 D：是装载的 VHD 的路径。
>
> 运行 `DISM /optimize-image` 应该是你对 VHD 所做的最后一次修改。 如果在部署之前对 VHD 进行了任何更改，则必须再次运行 `DISM /optimize-image`。

Sysprep 完成运行后，将虚拟机的状态设置为“通用化”。
   
```azurepowershell-interactive
Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
```
