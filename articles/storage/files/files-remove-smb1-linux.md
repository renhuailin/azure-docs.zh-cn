---
title: 通过在 Linux 上删除 SMB 1 来保护 Azure 和本地环境 | Microsoft Docs
description: Azure 文件存储支持 SMB 3.x 和 SMB 2.1，而不是不安全的旧版 SMB（例如 SMB 1）。 在连接到 Azure 文件共享之前，可能需要禁用旧版 SMB（例如 SMB 1）。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8f0e75ebf45839eb75f22a8fb46e76bdebec4688
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124670"
---
# <a name="remove-smb-1-on-linux"></a>删除 Linux 上的 SMB 1
许多组织和 Internet 服务提供商 (ISP) 会阻止 SMB 用来通信的端口 445。 这种做法源自于有关传统版和已弃用版 SMB 协议的安全指导原则。 SMB 3.x 是在 Internet 上很安全的协议，但早期版本的 SMB（尤其是 SMB 1）不安全。 SMB 1 也称“CIFS”（通用 Internet 文件系统），包含在许多 Linux 发行版中。 

SMB 1 是一个已过时的低效且不安全的协议。 好消息是 Azure 文件存储不支持 SMB 1，并且从 Linux 内核版本 4.18 开始，可在 Linux 中禁用 SMB 1。 我们始终[强烈建议](https://aka.ms/stopusingsmb1)在生产环境中使用 SMB 文件共享之前，禁用 Linux 客户端上的 SMB 1。

## <a name="linux-distribution-status"></a>Linux 分发状态
从 Linux 内核 4.18 开始，SMB 内核模块（由于历史遗留原因，称作 `cifs`）会公开一个名为 `disable_legacy_dialects` 的新模块参数（在各种外部文档中通常名为 *parm*）。 尽管 Linux 内核 4.18 中已引入此项更改，但某些供应商会将此项更改向后移植到他们支持的旧内核。 为方便起见，下表详细描述了此模块参数在常用 Linux 分发版上的可用性。

| 分发 | 可以禁用 SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | 否 |
| Ubuntu 18.04 | 是 |
| Ubuntu 19.04+ | 是 |
| Debian 8-9 | 否 |
| Debian 10+ | 是 |
| Fedora 29+ | 是 |
| CentOS 7 | 否 | 
| CentOS 8+ | 是 |
| Red Hat Enterprise Linux 6.x-7.x | 否 |
| Red Hat Enterprise Linux 8+ | 是 |
| openSUSE Leap 15.0 | 否 |
| openSUSE Leap 15.1+ | 是 |
| openSUSE Tumbleweed | 是 |
| SUSE Linux Enterprise 11.x-12.x | 否 |
| SUSE Linux Enterprise 15 | 否 |
| SUSE Linux Enterprise 15.1 | 否 |

可通过以下命令检查 Linux 发行版是否支持 `disable_legacy_dialects` 模块参数：

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

此命令应输出以下消息：

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

## <a name="remove-smb-1"></a>删除 SMB 1
在禁用 SMB 1 之前，请确认 SMB 模块当前未加载到系统中（如果已装载 SMB 共享，则此模块会自动加载到系统中）。 可使用以下命令来执行此项检查。如果未加载 SMB，则此命令不会输出任何信息：

```bash
lsmod | grep cifs
```

若要卸载该模块，请先卸载所有 SMB 共享（使用上面所述的 `umount` 命令）。 可使用以下命令识别系统上所有已装载的 SMB 共享：

```bash
mount | grep cifs
```

卸载所有 SMB 文件共享后，可以安全地卸载该模块。 为此，可以使用 `modprobe` 命令：

```bash
sudo modprobe -r cifs
```

可以使用 `modprobe` 命令手动加载已卸载 SMB 1 的模块：

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

最后，可以通过查看 `/sys/module/cifs/parameters` 中加载的参数，来检查是否已使用相应参数加载了 SMB 模块：

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

若要在 Ubuntu 和基于 Debian 的分发版上永久禁用 SMB 1，必须创建一个名为 `/etc/modprobe.d/local.conf` 且包含相应设置的新文件（如果尚未对其他模块使用自定义选项）。 为此，可以使用以下命令：

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

可以通过加载 SMB 模块来验证上述操作是否成功：

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息：

- [规划 Azure 文件部署](storage-files-planning.md)
- [将 Azure 文件存储用于 Linux](storage-how-to-use-files-linux.md)
- [疑难解答](storage-troubleshoot-linux-file-connection-problems.md)