---
title: 排查 Azure Linux 代理问题
description: 解决 Azure Linux 代理的问题。
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878691"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>排查 Azure Linux 代理问题

使用 [Azure Linux 代理](../extensions/agent-linux.md) ，虚拟机 (vm) 可以与在其上托管了 VM) IP 地址168.63.129.16 的基础物理服务器 (的结构控制器进行通信。

>[!NOTE]
>此 IP 地址是一个虚拟公共 IP 地址，用于促进通信，不应被阻止。 有关详细信息，请参阅 [什么是 IP 地址168.63.129.16？](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

## <a name="before-you-begin"></a>开始之前

检查代理状态和版本，以确保它仍受支持。 请参阅 [Azure 中虚拟机代理的最低版本支持](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) 以检查版本支持，或参阅 [WALinuxAgent 常见问题](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) 了解查找状态和版本的步骤。

## <a name="troubleshoot-a-not-ready-status"></a>排查 "未就绪" 状态

1. 检查 Azure Linux 代理的服务状态，确保其正在运行。 服务名称可能是 **walinuxagent** 或 **waagent**。

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   如果服务正在运行，请重新启动它以解决此问题。 如果服务已停止，请启动该服务，等待几分钟，然后再次检查状态。

1. 请确保已启用自动更新。 检查 **/etc/waagent.conf** 中的自动更新设置。

   ```
   AutoUpdate.Enabled=y
   ```

   有关如何更新 Azure Linux 代理的详细信息，请参阅 [如何更新 VM 上的 Azure Linux 代理](../extensions/update-linux-agent.md)。

1. 请确保 VM 可以连接到结构控制器。 使用 "卷曲" 之类的工具测试 VM 是否可以连接到端口80、443和32526上的168.63.129.16。 如果 VM 未按预期连接，请检查通过端口 80、443 和 32526 的出站通信是否在 VM 的本地防火墙中打开。 如果此 IP 地址被阻止，VM 代理可能会显示意外的行为。

## <a name="advanced-troubleshooting"></a>高级故障排除

Azure Linux 代理故障排除事件记录在 **/var/log/waagent.log** 文件中。

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>无法连接到 WireServer IP（主机 IP）

当 VM 无法连接到主机服务器上的 WireServer IP 时， **/var/log/waagent.log** 文件中会出现以下错误。

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

要解决此问题：

* 使用 SSH 连接到 VM，然后尝试从曲线访问以下 URL： http://168.63.129.16/?comp=versions 。
* 检查可能由防火墙、代理或可能阻止访问 IP 地址168.63.129.16 的其他源引起的任何问题。
* 检查 Linux IPTables 或第三方防火墙是否阻止访问端口80、443和32526。

## <a name="next-steps"></a>后续步骤

若要进一步排查 Azure Linux 代理问题，请 [联系 Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)部门。