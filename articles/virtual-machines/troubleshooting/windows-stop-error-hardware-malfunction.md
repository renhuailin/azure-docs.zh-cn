---
title: Windows 停止错误 - 硬件故障
description: 本文提供了解决以下问题的步骤：Windows Server 2008 虚拟机崩溃，并出现一条错误消息，指出存在硬件故障。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 89faa5b29e0a972f31ad51a7354635a53176541a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661351"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows 停止错误 - 硬件故障

本文提供了解决以下问题的步骤：Windows Server 2008 虚拟机崩溃，并出现一条错误消息，指出存在硬件故障。

## <a name="symptoms"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，你会看到屏幕截图为蓝屏且显示以下消息：

**\*\*\*** 硬件故障

请致电供应商寻求支持

\*\*\* 系统已停止 **\*\*\***

#### <a name="blue-screen"></a>蓝屏

![屏幕截图显示蓝屏硬件故障性崩溃。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>串行控制台

![如果启用了串行控制台功能，屏幕截图将显示串行控制台功能上的 "硬件故障" 消息。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>原因

当来宾 OS 未正确设置且发送了不可屏蔽中断 (NMI) 时，会显示此屏幕。 错误消息指示内核模式程序生成了异常，处理程序未捕获此异常。 可以通过收集内存转储来确定生成了什么异常。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述 

> [!TIP]
> 如果你有 VM 的最新备份，则可以尝试 [从备份还原 vm](../../backup/backup-azure-arm-restore-vms.md) ，以解决启动问题。

1. 设置不可屏蔽中断 (NMI) 注册表项 
2. 创建并访问修复 VM 
3. 启用串行控制台和内存转储收集 
4. 重建 VM 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>设置不可屏蔽中断 (NMI) 注册表项

1. 使用 Azure 门户重启 VM，以便来宾 OS 正常启动。 
2. 恢复对 VM 的某些访问后，打开一个提升的命令提示符（以管理员身份运行）。 
3. 使用以下命令设置 NMI 注册表项：

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [查看有关 REG ADD 命令的详细信息](/windows-server/administration/windows-commands/reg-add)
4. （可选）设置内存转储收集：

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. （可选）设置串行控制台访问：

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [查看有关 BCDEDIT 命令的详细信息](/windows-server/administration/windows-commands/bcdedit)
6. 使用以下命令重启 VM：

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [查看有关 SHUTDOWN 命令的详细信息](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> 此问题现在应当已修复！

> [!NOTE]
> 重启后，测试你的 VM，确保其正常运行。 如果仍遇到问题，可以继续阅读下一部分以获得进一步的说明。

> [!TIP]
> 建议在上面的部分中设置非屏蔽中断 (NMI) 注册表项，但如果 VM 不正常启动，则可能不会发生对来宾 OS 注册表的预期更改。 如果是这种情况，则可以按照下面的说明操作，手动添加注册表设置。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步骤 1-3 来准备一个修复 VM。
2. 使用远程桌面连接来连接到修复 VM。

### <a name="enable-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储收集

重新生成 VM 之前，建议启用内存转储收集和串行控制台。 为此，请运行以下脚本： 

1. 打开提升的命令提示符会话（以管理员身份运行）。 
2. 列出 BCD 存储数据，并确定要在下一步中使用的引导加载程序标识符。 
    1. 对于第 1 代 VM，请输入以下命令，并记下列出的标识符： 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        在该命令中，将 `<BOOT PARTITON>` 替换为附加磁盘中包含引导文件夹的分区驱动器号。 

        ![屏幕截图显示了在第 1 代 VM 中列出 BCD 存储时的输出，在 Windows 引导加载程序下方列出了标识符编号。](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. 对于第 2 代 VM，请输入以下命令，并记下列出的标识符：
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * 在该命令中，将 `<LETTER OF THE EFI SYSTEM PARTITION>` 替换为 EFI 系统分区的驱动器号。
        * 这可能有助于启动“磁盘管理”控制台以识别标记为 EFI 系统分区的相应系统分区。
        * 标识符可以是唯一的 GUID，也可以是默认的 bootmgr。
3. 运行以下命令以启用串行控制台：

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * 在该命令中，将 `<VOLUME LETTER WHERE THE BCD FOLDER IS>` 替换为 BCD 文件夹的驱动器号。
    * 在该命令中，将 `<BOOT LOADER IDENTIFIER>` 替换为在上一步骤中找到的标识符。
4. 验证 OS 磁盘上的可用空间是否大于 VM 上的内存大小 (RAM)。 
    1. 如果 OS 磁盘上没有足够的空间，则应更改将在其中创建内存转储文件的位置。 不要在 OS 磁盘上创建文件，可以将其指向附加到 VM 且具有足够可用空间的任何其他数据磁盘。 若要更改位置，请将下面列出的命令中的 %SystemRoot% 替换为数据磁盘的驱动器号（例如 F:）。 
    2. 输入以下命令（建议的转储配置）：

    **从损坏的 OS 磁盘加载注册表配置单元：**

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    **在 ControlSet001 上启用：**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **在 ControlSet002 上启用：**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **卸载损坏的 OS 磁盘：**

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```
### <a name="rebuild-the-virtual-machine"></a>重新生成虚拟机

* 使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新生成 VM。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [排查 Azure 虚拟机启动错误](./boot-error-troubleshoot.md)