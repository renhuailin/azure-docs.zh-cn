---
title: 排查 Azure Linux 来宾代理问题
description: 排查 Azure Linux 来宾代理不起作用问题
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
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549872"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>排查 Azure Linux 来宾代理问题

[Azure Linux 来宾代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 是 (VM) 代理的虚拟机。 它使虚拟机能够与 IP 地址 168.63.129.16 上的结构控制器（VM 所在的基础物理服务器）通信。 此 IP 地址是一个虚拟公共 IP 地址，用于促进通信。 有关详细信息，请参阅[什么是 IP 地址 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

## <a name="checking-agent-status-and-version"></a>检查代理状态和版本

请参见https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>对处于“未就绪”状态的 VM 代理进行故障排除

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>步骤1检查 Azure Linux 来宾代理服务是否正在运行

根据发行版，服务名称可以是 walinuxagent 或 waagent：

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


如果你可以看到服务并且它们正在运行，请重新启动服务，以查看是否已解决问题。 如果服务已停止，请启动它们并等待几分钟。 然后检查“代理状态”是否报告为“就绪” 。 若要进一步排查这些问题，请联系 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="step-2-check-whether-auto-update-is-enabled"></a>步骤2检查是否已启用自动更新

检查/etc/waagent.conf 中的此设置：

```
AutoUpdate.Enabled=y
```

有关如何更新 Azure Linux 代理的详细信息，请参阅 https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>步骤 3 检查 VM 是否可以连接到结构控制器

使用 "卷曲" 之类的工具测试 VM 是否可以连接到端口80、32526和443上的168.63.129.16。 如果 VM 未按预期方式连接，请检查是否已在 VM 上的本地防火墙中打开通过端口80、443和32526的出站通信。 如果阻止此地址，VM 代理可能在各种情况下出现意外行为。

## <a name="advanced-troubleshooting"></a>高级故障排除

Azure Linux 来宾代理故障排除事件记录在以下日志文件中：

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>无法连接到 WireServer IP（主机 IP） 

你会在/var/log/waagent.log 中注意到以下错误条目：

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**分析**

VM 无法连接到主机服务器上的 WireServer IP。

**解决方案**

1. 由于 WireServer IP 不可访问，因此请使用 SSH 连接到 VM，然后尝试从曲线访问以下 URL： http://168.63.129.16/?comp=versions 
1. 检查防火墙、代理或其他可能阻止访问 IP 地址 168.63.129.16 的源可能导致的任何问题。
1. 检查 Linux IPTables 或第三方防火墙是否阻止访问端口80、443和32526。 有关此地址不应被阻止的原因的详细信息，请参阅[什么是 IP 地址 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。


## <a name="next-steps"></a>后续步骤

若要进一步排查 "Windows Azure 来宾代理无法工作" 问题，请 [联系 Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)部门。
