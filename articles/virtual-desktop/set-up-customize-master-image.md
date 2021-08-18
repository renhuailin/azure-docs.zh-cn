---
title: 准备和自定义主 VHD 映像 - Azure
description: 如何准备、自定义 Azure 虚拟桌面主映像并将其上传到 Azure。
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 8e893a95337801db4ee35cfdc00e2d97f8d12db6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285855"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>准备和自定义主 VHD 映像

本文介绍如何准备用于上传到 Azure 的主虚拟硬盘 (VHD) 映像，包括如何创建虚拟机 (VM) 并在其中安装软件。 这些说明适用于可与组织的现有过程配合使用的特定于 Azure 虚拟桌面的配置。

>[!IMPORTANT]
>建议使用 Azure 映像库中的映像。 但如果确实需要使用自定义映像，请确保 VM 上尚未安装 Azure 虚拟桌面代理。 在已安装 Azure 虚拟桌面代理的情况下使用自定义映像，可能会导致映像出现问题，例如阻止注册、阻止用户会话连接。  

## <a name="create-a-vm"></a>创建 VM

Azure 映像库中提供 Windows 10 企业版多会话。 自定义此映像有两种方法。

第一种方法是在 Azure 中预配虚拟机 (VM)，即按照[从托管的映像创建 VM](../virtual-machines/windows/create-vm-generalized-managed.md) 中的说明执行操作，然后跳到[软件准备和安装](set-up-customize-master-image.md#software-preparation-and-installation)。

第二种方法是在本地创建映像，即下载映像，预配 Hyper-V VM，然后根据需要对其进行自定义。在以下部分，我们将介绍这种方法。

### <a name="local-image-creation"></a>创建本地映像

将映像下载到本地位置后，打开“Hyper-V 管理器”，使用复制的 VHD 创建 VM。 下面是简单版本的说明，有关更加详细的说明，请参阅[在 Hyper-V 中创建虚拟机](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)。

使用复制的 VHD 创建 VM：

1. 打开“新建虚拟机向导”。

2. 在“指定代数”页面上，选择“第 1 代”。

    > [!div class="mx-imgBorder"]
    > ![“指定代数”页的屏幕截图。 已选择“第 1 代”选项。](media/a41174fd41302a181e46385e1e701975.png)

3. 在“检查点类型”下，取消选中复选框，禁用检查点。

    > [!div class="mx-imgBorder"]
    > ![“检查点”页中“检查点类型”部分的屏幕截图。](media/20c6dda51d7cafef33251188ae1c0c6a.png)

此外，还可以在 PowerShell 中运行以下 cmdlet 来禁用检查点。

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>固定磁盘

如果基于现有 VHD 创建 VM，则默认创建动态磁盘。 可以通过选择“编辑磁盘...”将其更改为固定磁盘，如下图所示。 有关更加详细的说明，请参阅[准备上传到 Azure 的 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。

> [!div class="mx-imgBorder"]
> ![“编辑磁盘”选项的屏幕截图。](media/35772414b5a0f81f06f54065561d1414.png)

此外，还可以运行以下 PowerShell cmdlet，将磁盘更改为固定磁盘。

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>软件准备和安装

本部分介绍如何准备和安装 FSLogix 和 Windows Defender，以及一些用于应用和映像注册表的基本配置选项。

如果要在 VM 上安装 Microsoft 365 应用企业版和 OneDrive，请参阅[在主 VHD 映像上安装 Office](install-office-on-wvd-master-image.md)，并按照相关说明安装应用。 完成后，请返回到本文章。

如果用户需要访问某些 LOB 应用程序，建议在完成本部分说明后再安装这些应用程序。

### <a name="set-up-user-profile-container-fslogix"></a>设置用户配置文件容器 (FSLogix)

要将 FSLogix 容器作为映像的一部分包括在内，请按照[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)中的说明进行操作。 通过此[快速入门](/fslogix/configure-cloud-cache-tutorial/)可测试 FSLogix 容器的功能。

### <a name="configure-windows-defender"></a>配置 Windows Defender

如果 VM 中配置了 Windows Defender，请确保其配置为在附加期间不扫描 VHD 和 VHDX 文件的全部内容。

此项配置只是在附加期间取消扫描 VHD 和 VHDX 文件，但不会影响实时扫描。

有关如何在 Windows Server 上配置 Windows Defender 的更详细说明，请参阅[在 Windows Server 上配置 Windows Defender 防病毒排除项](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)。

要详细了解如何配置 Windows Defender 来排除对某些文件的扫描，请参阅[基于文件扩展名和文件夹位置配置和验证排除项](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)。

### <a name="disable-automatic-updates"></a>禁用自动更新

通过本地组策略禁用自动更新：

1. 打开“本地组策略编辑器”\\“管理模板”\\“Windows 组件”\\“Windows 更新”。
2. 右键单击“配置自动更新”，并将其设置为“已禁用”。

此外，还可以在命令提示符下运行以下命令来禁用自动更新。

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>指定 Windows 10 电脑的开始布局（可选）

运行以下命令以指定 Windows 10 电脑的开始布局。

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>设置时区重定向

由于主机池中的所有 VM 都是同一安全组的一部分，因此可以在组策略级别强制执行时区重定向。

重定向时区：

1. 在 Active Directory 服务器上，打开“组策略管理控制台”。
2. 展开你的域和组策略对象。
3. 右键单击为组策略设置创建的“组策略对象”，然后选择“编辑”。
4. 在“组策略管理编辑器”中，导航到“计算机配置” > “策略” > “管理模板” > “Windows 组件” > “远程桌面服务” > “远程桌面会话主机” > “设备和资源重定向”。
5. 启用“允许时区重定向”设置。

此外，还可以对主映像运行以下命令来重定向时区：

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>禁用存储感知

对于使用 Windows 10 企业版或 Windows 10 企业版多会话的 Azure 虚拟桌面会话主机，建议禁用存储感知。 用户可以在“设置”菜单的“存储”下禁用存储感知，如以下屏幕截图所示：

> [!div class="mx-imgBorder"]
> ![“设置”下“存储”菜单的屏幕截图。 “存储感知”选项已关闭。](media/storagesense.png)

此外，还可以通过运行以下命令来更改注册表的设置：

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>包括其他语言支持

本文不介绍如何配置语言和区域支持。 有关详细信息，请参阅以下文章：

- [将语言添加到 Windows 映像](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [按需功能](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [按需语言和区域功能 (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>其他应用程序和注册表配置

本部分介绍应用程序和操作系统配置。 本部分中的所有配置都通过使用命令行和 regedit 工具执行注册表项来完成。

>[!NOTE]
>用户可以在配置中通过组策略对象 (GPO) 或注册表导入来实现最佳做法。 管理员可以根据组织需求选择任一选项。

如果反馈中心要收集有关 Windows 10 企业版多会话的遥测数据，请运行以下命令：

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

运行以下命令以修复 Watson 崩溃：

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

在注册表编辑器中输入以下命令以修复 5k 分辨率支持。 必须先运行命令，然后才能启用并排堆栈。

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>准备要上传到 Azure 的映像

完成配置并安装所有应用程序后，按照[准备要上传到 Azure 的 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 中的说明来准备映像。

准备好用于上传的映像后，确保 VM 处于关闭或已解除分配状态。

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>将主映像上传到 Azure 中的存储帐户

本部分仅适用于在本地创建主映像的情况。

以下说明将介绍如何将主映像上传到 Azure 存储帐户。 如果还没有 Azure 存储帐户，请按照[此文](../storage/common/storage-account-create.md)中的说明创建一个。

1. 将 VM 映像 (VHD) 转换为固定映像（如果尚未转换）。 如果未将映像转换为固定映像，则无法成功创建映像。

2. 将 VHD 上传到存储帐户中的 blob 容器。 可以通过[存储资源管理器工具](https://azure.microsoft.com/features/storage-explorer/)快速上传。 有关存储资源管理器工具的详细信息，请参阅[此文](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)。

    > [!div class="mx-imgBorder"]
    > ![Microsoft Azure 存储资源管理器工具搜索窗口的屏幕截图。 已选中“将 .vhd 或 vhdx 文件上传为页 blob（建议）”复选框。](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 接下来，在浏览器中转到 Azure 门户，搜索“映像”。 搜索应会引导你转到“创建映像”页，如以下屏幕截图所示：

    > [!div class="mx-imgBorder"]
    > ![Azure 门户中“创建映像”页的屏幕截图，已为映像填充示例值。](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 创建映像后，应会看到一条通知，如下屏幕截图所示：

    > [!div class="mx-imgBorder"]
    > ![“已成功创建映像”通知的屏幕截图。](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>后续步骤

现在，你已拥有映像，则可以创建或更新主机池。 有关如何创建和更新主机池的详细信息，请参阅以下文章：

- [使用 Azure 资源管理器模板创建主机池](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [教程：通过 Azure 市场创建主机池](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 创建主机池](create-host-pools-powershell.md)
- [使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)
- [配置 Azure 虚拟桌面负载均衡方法](configure-host-pool-load-balancing.md)

如果在准备或自定义 VHD 映像后遇到连接问题，请查看[故障排除指南](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved)获取帮助。