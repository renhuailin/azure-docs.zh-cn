---
title: Azure 中 Linux VM 的时间同步
description: Linux 虚拟机的时间同步。
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/30/2021
ms.author: cynthn
ms.openlocfilehash: e5ceb8e4db1d2b94d746303a2185bea2015467a0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691009"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure 中 Linux VM 的时间同步

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集 

时间同步对于安全性和事件相关性来说很重要。 有时候，它用于分布式事务实现。 多个计算机系统之间的时间准确性通过同步来实现。 同步可能受多种因素影响，包括重启以及时间源和提取时间的计算机之间的网络流量。 

Azure 受运行 Windows Server 2016 的基础设施的支持。 Windows Server 2016 已改进用于纠正时间和条件的算法，方便本地时钟与 UTC 同步。  Windows Server 2016 的准确时间功能大大改进了 VMICTimeSync 服务的控制方式，可以通过控制 VM 与主机的同步来确保时间准确。 改进包括增强 VM 启动或 VM 还原的初始时间的准确性，以及纠正中断延迟。 

> [!NOTE]
> 若要快速了解 Windows 时间服务，请参阅此[高级概述视频](https://aka.ms/WS2016TimeVideo)。
>
> 有关详细信息，请参阅 [Windows Server 2016 的准确时间](/windows-server/networking/windows-time-service/accurate-time)。 

## <a name="overview"></a>概述

计算机时钟的准确性根据计算机时钟与协调世界时 (UTC) 时间标准的接近程度来测量。 UTC 通过精确原子钟的跨国样本来定义，此类原子钟 300 年的偏差只有 1 秒。 但是，直接读取 UTC 需要专用硬件。 而时间服务器与 UTC 同步，可以从其他计算机访问，因此具备可伸缩性和可靠性。 每个计算机都有时间同步服务运行，该服务知道使用什么时间服务器，并定期检查计算机时钟是否需纠正，然后根据需要调整时间。 

Azure 主机与内部 Microsoft 时间服务器同步，这些服务器从 Microsoft 拥有的带 GPS 天线的 Stratum 1 设备获取时间。 Azure 中的虚拟机可以依赖其主机来获取准确的时间（主机时间），也可以直接从时间服务器获取时间，或者同时采用这两种方法。 

在独立硬件上，Linux OS 仅在启动时读取主机硬件时钟数据。 然后，时钟会通过 Linux 内核中的中断计时器来维护。 在此配置中，时钟会随着时间的推移而出现偏差。 在 Azure 上的较新的 Linux 发行版中，VM 可以使用 Linux Integration Services (LIS) 中随附的 VMICTimeSync 提供程序，从主机更频繁地查询时钟更新。

虚拟机与主机的交互也可能影响时钟。 在[内存保留维护](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)期间，VM 会暂停最多 30 秒的时间。 例如，在维护开始之前，VM 时钟显示上午 10:00:00，这种状态会持续 28 秒。 在 VM 恢复后，VM 上的时钟仍显示上午 10:00:00，这样就造成 28 秒的偏差。 为了纠正这种情况，VMICTimeSync 服务会监视主机上发生的情况，并更新 Linux VM 中的日历时钟以进行补偿。

如果不进行时间同步，VM 上的时钟会累积错误。 只有一个 VM 时，效果可能不明显，除非工作负荷要求极为准确的计时。 但在大多数情况下，我们有多个互连的 VM，这些 VM 使用时间来跟踪事务，因此需确保整个部署的时间一致。 当 VM 之间的时间不同时，可能会造成以下影响：

- 身份验证会失败。 安全协议（如 Kerberos）或依赖于证书的技术要求跨系统确保时间一致性。
- 如果日志（或其他数据）在时间上不一致，则很难弄清楚系统中发生了什么。 同一事件看起来就像是在不同的时间发生，难以进行关联。
- 如果时钟存在偏差，则可能造成计费不正确。


## <a name="configuration-options"></a>配置选项

时间同步要求在 Linux VM 中运行一个时间同步服务，并且有准确时间信息源可以用作同步的参照。
一般会将 ntpd 或 chronyd 用作时间同步服务，不过，也有其他开源时间同步服务可供使用。
准确时间信息源可以是 Azure 主机，也可以是通过公共 Internet 访问的外部时间服务。
VMICTimeSync 服务不会自动在 Azure 主机与 Linux VM 之间提供持续的时间同步，除非是在由于进行主机维护（如上所述）而发生暂停之后。 

过去，Linux 中的大多数 Azure 市场映像都采用以下两种配置方式之一：
- 默认不会运行时间同步服务
- ntpd 作为时间同步服务运行，并参照通过网络访问的外部 NTP 时间源进行同步。 例如，Ubuntu 18.04 LTS 市场映像使用 ntp.ubuntu.com。

若要确认 ntpd 是否正确同步，请运行 `ntpq -p` 命令。

从日历年 2021 年初开始，Linux 的最新 Azure 市场映像正在改用 chronyd 作为时间同步服务，而 chronyd 配置为参照 Azure 主机而不是外部 NTP 时间源进行同步。 Azure 主机时间通常是最佳的同步参照时间源，因为它是以非常准确、可靠的方式维护的，并且可供访问，而不会出现通过公共 Internet 访问外部 NTP 时间源时那样固有的可变网络延迟。

VMICTimeSync 是以并行方式使用的，它提供两种功能：
- 发生主机维护事件后立即更新 Linux VM 的日历时钟
- 将 IEEE 1588 精确时间协议 (PTP) 硬件时钟源实例化为一个 /dev/ptp 设备，用于提供来自 Azure 主机的准确日期时间。  可将 Chronyd 配置为参照此时间源进行同步（这是最新 Linux 映像中的默认配置）。 带有 4.11 或更高的内核版本的 Linux 发行版（或者带有 3.10.0-693 或更高的内核版本的 RHEL/CentOS 7）支持 /dev/ptp 设备。  对于不支持将 /dev/ptp 用于 Azure 主机时间的早期内核版本，只能参照外部时间源进行同步。

当然，可以更改默认配置。 可以将配置为使用 ntpd 和外部时间源的较旧映像更改为将 chronyd 和 /dev/ptp 设备用于 Azure 主机时间。  同样，可以根据应用程序或工作负载的需求，将通过 /dev/ptp 设备使用 Azure 主机时间的映像配置为使用外部 NTP 时间源。


## <a name="tools-and-resources"></a>工具和资源

可以通过一些基本的命令来检查时间同步配置。 Linux 发行版的文档更详细地说明了如何才能以最佳方式为该发行版配置时间同步。

### <a name="integration-services"></a>集成服务

查看集成服务 (hv_utils) 是否已加载。

```bash
lsmod | grep hv_utils
```
看到的内容应该如下所示：

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

### <a name="check-for-ptp-clock-source"></a>检查 PTP 时钟源

对于较新版本的 Linux，在 VMICTimeSync 提供程序中提供了对应于 Azure 主机的精确时间协议 (PTP) 时钟源。
在较旧版本的 Red Hat Enterprise Linux 或 CentOS 7.x 上，可以下载 [Linux Integration Services ](https://github.com/LIS/lis-next)，并将其用于安装更新的驱动程序。 当 PTP 时钟源可用时，Linux 设备的表示形式为 /dev/ptpx。 

查看哪些 PTP 时钟源可用。

```bash
ls /sys/class/ptp
```

在此示例中，返回的值为 *ptp0*，因此我们使用它来检查时钟名称。 若要验证设备，请检查时钟名称。

```bash
cat /sys/class/ptp/ptp0/clock_name
```

此命令应返回 `hyperv`，即 Azure 主机。

在启用了加速网络的 Linux VM 中，可能会看到列出了多个 PTP 设备，因为 Mellanox mlx5 驱动程序也会创建 /dev/ptp 设备。
由于每次启动 Linux 时初始化顺序可能不同，因此对应于 Azure 主机的 PTP 设备可能是 /dev/ptp0 或 /dev/ptp1，导致难以使用正确的时钟源配置 chronyd。 为了解决此问题，最新的 Linux 映像提供了一个 udev 规则，用于创建指向对应于 Azure 主机的任何 /dev/ptp 条目的符号链接 /dev/ptp_hyperv。 应将 Chrony 配置为使用此符号链接而不是 /dev/ptp0 或 /dev/ptp1。

### <a name="chrony"></a>chrony

在 Ubuntu 19.10 及更高版本、Red Hat Enterprise Linux 和 CentOS 8.x 上，[chrony](https://chrony.tuxfamily.org/) 配置为使用 PTP 源时钟。 旧的 Linux 发行版使用网络时间协议守护程序 (ntpd)（不支持 PTP 源），而不是使用 chrony。 若要在这些版本中启用 PTP，必须使用以下语句手动安装并配置 chrony（在 chrony.conf 中）：

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```
如上所述，如果提供了 /dev/ptp_hyperv 符号链接，请使用该符号链接而不是 /dev/ptp0，以避免与 Mellanox mlx5 驱动程序创建的 /dev/ptp 设备相混淆。

有关 Ubuntu 和 NTP 的详细信息，请参阅[时间同步](https://ubuntu.com/server/docs/network-ntp)。

有关 Red Hat 和 NTP 的详细信息，请参阅[配置 NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP)。 

有关 chrony 的详细信息，请参阅[使用 chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony)。

如果同时启用了 chrony 和 VMICTimeSync 源，则可将一个源标记为“首选”，这样就会将另一个源设置为“备用”。 由于 NTP 服务在遇到大偏差时更新时钟需要很长时间，因此可以使用 VMICTimeSync，后者在出现 VM 暂停事件时恢复时钟的速度远远快于单独使用基于 NTP 的工具的速度。

默认情况下，chronyd 会加快或减慢系统时钟以修复任何时间偏移。 如果偏移量过大，chrony 将无法修复偏移。 若要解决此问题，可以更改 /etc/chrony.conf 中的 `makestep` 参数，以便在偏移量超过指定的阈值时强制进行时间同步。

 ```bash
makestep 1.0 -1
```

在这里，如果偏移量大于 1 秒，chrony 会强制进行时间更新。 若要应用更改，请重启 chronyd 服务：

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

在 19.10 以前的 SUSE 和 Ubuntu 版本中，使用 [systemd](https://www.freedesktop.org/wiki/Software/systemd/) 配置时间同步。 有关 Ubuntu 的详细信息，请参阅[时间同步](https://help.ubuntu.com/lts/serverguide/NTP.html)。 有关 SUSE 的详细信息，请参阅 [SUSE Linux Enterprise Server 12 SP3 发行说明](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)中的第 4.5.8 节。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Windows Server 2016 的准确时间](/windows-server/networking/windows-time-service/accurate-time)。


