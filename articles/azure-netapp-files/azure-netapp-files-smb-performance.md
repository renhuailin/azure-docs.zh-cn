---
title: Azure NetApp 文件的 SMB 性能最佳做法 | Microsoft Docs
description: 帮助你了解 Azure NetApp 文件的 SMB 性能和最佳做法。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: b-juche
ms.openlocfilehash: 6c29c1804e5587a2a1a3bae3e2f566a5938350cf
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112004774"
---
# <a name="smb-performance-best-practices-for-azure-netapp-files"></a>Azure NetApp 文件的 SMB 性能最佳做法

本文将帮助你了解 Azure NetApp 文件的 SMB 性能和最佳做法。

## <a name="smb-multichannel"></a>SMB 多通道

SMB 共享中默认启用 SMB 多通道。 所有 SMB 共享预先可追溯的现有 SMB 卷已启用了这一功能，并且在创建新卷时，所有新创建的卷也将启用该功能。 

在启用该功能之前建立的任何 SMB 连接需要重置才能利用 SMB 多通道功能。 要重置，你可以先断开与 SMB 共享的连接，然后重新连接。

从 Windows 2012 开始，Windows 支持 SMB 多通道，以实现最佳性能。  有关详细信息，请参阅[部署 SMB 多通道](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11))和 [SMB 多通道基础知识](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0)。 

### <a name="benefits-of-smb-multichannel"></a>SMB 多通道的优点

使用 SMB 多通道功能，SMB3 客户端可以通过单个网卡 (NIC) 或多个 NIC 建立连接池，并使用这些连接来发送单个 SMB 会话的请求。 相反，根据设计，SMB1 和 SMB2 要求客户端建立一个连接，并通过该连接发送给定会话的所有 SMB 流量。 这一单个连接可限制单个客户端可以实现的总体协议性能。

### <a name="performance-for-smb-multichannel"></a>SMB 多通道的性能

以下测试和图形演示了单实例工作负载上 SMB 多通道的强大功能。

#### <a name="random-io"></a>随机 I/O  

如果在客户端上禁用 SMB 多通道，则使用 FIO 和 40 GiB 工作集执行纯 4 KiB 读写测试。  SMB 共享已在每个测试之间分离，SMB 客户端连接计数递增，每个 RSS 网络接口设置为 `1`、`4`、`8`、`16` 和 `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`。 这些测试表明默认设置 `4` 足以满足 I/O 密集型工作负载的需求；递增到 `8` 和 `16` 产生的影响可以忽略。 

`netstat -na | findstr 445` 命令证明，已建立了其他连接，并且从 `1` 依次递增到 `4`、`8` 和 `16`。  在每个测试过程中，SMB 已充分利用了四个 CPU 内核，这是由 perfmon `Per Processor Network Activity Cycles` 统计信息确认（不在本文讨论范围内）。

![该图表显示了 SMB 多通道的随机 I/O 比较。](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 虚拟机不会影响 SMB（和 NFS）的存储 I/O 限制。  如下图所示，对于缓存的存储 IOPS，D32ds 实例类型的速率上限为 308000，对于非缓存存储 IOPS，则为 51200。  但是，上图显示出 I/O 明显高于 SMB。

![该图表显示随机 I/O 比较测试。](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

#### <a name="sequential-io"></a>顺序 IO 

类似于前面所述的随机 I/0 测试的测试是通过 64 KiB 顺序 I/O 执行的。 尽管每个 RSS 网络接口增加的客户端连接数增加了 4，但对随机 I/0 没有明显影响，但这一点不适用于顺序 I/O。 如下图所示，每次增加都与读取吞吐量的相应增加有关。 由于 Azure 针对每个实例类型/大小实施了网络带宽限制，因此写入吞吐量仍保持平缓趋势。 

![显示吞吐量测试比较的图表。](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure 会对每个虚拟机类型/大小施加网络速率限制。 仅对出站流量施加速率限制。 虚拟机上存在的 NIC 数量与计算机的可用带宽总量无关。  例如，D32ds 实例类型施加的网络限制为 16000 Mbps (2000 MiB/秒)。  如上的顺序关系图所示，该限制会影响出站流量（写入），但不影响多多通道读取。

![显示顺序 I/O 比较测试的图表。](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="smb-signing"></a>SMB 签名

SMB 协议为文件和打印共享以及其他网络操作（例如远程 Windows 管理）提供基础。 为了防止在传输中修改 SMB 数据包的中间人攻击，SMB 协议支持对 SMB 数据包进行数字签名。 

Azure NetApp 文件支持的所有 SMB 协议版本都支持 SMB 签名。 

### <a name="performance-impact-of-smb-signing"></a>SMB 签名的性能影响  

SMB 签名对 SMB 性能有负面影响。 在性能下降的各种可能原因中，每个数据包的数字签名将耗用额外的客户端 CPU 处理能力，如下面的 perfmon 输出所示。 在这种情况下，核心 0 在负责处理 SMB，包括 SMB 签名。  与上一节中的非多通道顺序读取吞吐量数字相比，结果表明，SMB 签名导致总体吞吐量从 875MiB/s 降低到大约 250MiB/s。 

![显示 SMB 签名对性能造成的影响的图表。](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="performance-for-a-single-instance-with-a-1-tb-dataset"></a>具有 1 TB 数据集的单个实例的性能

为了让您更详细地了解读/写混合型工作负荷，以下两个图表显示了具有 1 TB 数据集且 SMB 多通道为 4 的单个超级服务级别云卷（50 TB）的性能。 使用的最佳 IODepth 为 16，而灵活的 IO (FIO) 参数用于确保充分利用网络带宽 (`numjobs=16`)。

下图显示了4k 随机 I/O 的结果，其中包含单个 VM 实例，读/写混合的时间间隔为 10%：

![显示 Windows 2019 standard _D32ds_v4 4K 随机 IO 测试的图表。](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

以下图表显示了顺序 I/O 的结果：

![显示 Windows 2019 standard _D32ds_v4 64K 顺序吞吐量的图表。](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="performance-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>使用具有 1 TB 数据集的 5 个 VM 进行横向扩展时的性能

这些测试包含 5 个 VM，使用与单个 VM 相同的测试环境，每个进程将写入到自己的文件中。

以下图表显示了顺序 I/O 的结果：

![显示 Windows 2019 standard _D32ds_v4 4K 5 实例随机 IO 测试的图表。](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

以下图表显示了顺序 I/O 的结果：

![显示 Windows 2019 standard _D32ds_v4 64K 5 实例顺序吞吐量的图表。](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-to-monitor-hyper-v-ethernet-adapters"></a>如何监视 Hyper-V 以太网适配器  

使用 FIO 进行测试时，使用的一种策略是设置 `numjobs=16`。 这样做会将每个作业分叉为 16 个特定实例，以将 Microsoft Hyper-V 的网络适配器最大化。

可以在Windows 性能监视器中检查每个适配器的活动，方法是选择“性能监视器”>“添加计数器”>“网络接口”>“ Microsoft Hyper-V 网络适配器”。

![显示性能监视器添加计数器接口的屏幕截图。](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

在卷中运行数据流量后，可以在 Windows 性能监视器中监视适配器。 如果没有使用全部 16 个虚拟适配器，则可能无法最大程度地利用网络带宽容量。

![显示性能监视器输出的屏幕截图。](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="smb-encryption"></a>SMB 加密

本部分帮助你理解 SMB 加密（SMB 3.0 和 SMB 3.1.1） 

[SMB 加密](/windows-server/storage/file-server/smb-security)提供 SMB 数据的端对端加密，并防止数据在不受信任网络中遭到窃听。 SMB 3.0 及更高版本支持 SMB 加密。 

向存储发送请求时，客户端会对该请求进行加密，随后存储会进行解密。 同样，响应由服务器加密并由客户端解密。

Windows 10、Windows 2012 及更高版本支持 SMB 加密。

### <a name="smb-encryption-and-azure-netapp-files"></a>SMB 加密和 Azure NetApp 文件

SMB 加密在 Azure NetApp 文件的共享级别启用。 SMB 3.0 采用 AES-CCM 算法，而 SMB 3.1.1 采用 AES-GCM 算法。

SMB 加密不是必需的。 因此，只有当用户要求 Azure NetApp 文件对特定共享启用加密时，才会启用它。 Azure NetApp 文件共享从不在 Internet 中公开。 它们只能从特定 VNet 中通过 VPN 或快速路由进行访问，因此，Azure NetApp 文件共享本质上是安全的。 启用 SMB 加密的选择完全由用户决定。 在启用此功能之前，请注意预期的性能损失。

### <a name="impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>SMB 加密对客户端工作负载的影响

尽管 SMB 加密会对客户端（加密和解密消息的 CPU 开销）和存储（降低吞吐量）产生影响，但下表只重点介绍了存储影响。 在将工作负载部署到生产环境之前，应测试加密性能对应用程序的影响。

|     I/O 配置文件       |     影响        |
|-  |-  |
|     读取和写入工作负载      |     10% 到 15%        |
|     元数据密集型        |     5%    |

## <a name="accelerated-networking"></a>加速网络 

为了获得最佳性能，建议尽可能在虚拟机上配置[加速网络](../virtual-network/create-vm-accelerated-networking-powershell.md)。 请谨记下列注意事项：  

* 默认情况下，Azure 门户将为支持此功能的虚拟机启用加速网络。  但其他部署方法（如 Ansible 和类似的配置工具）可能不会执行这一操作。  未启用加速网络可能会降低计算机的性能。  
* 如果虚拟机的网络接口由于不支持某一实例类型或大小而未启用加速网络，则对于更大的实例类型，仍会禁用加速网络。 在这些情况下，你将需要手动干预。

## <a name="rss"></a>RSS 

Azure NetApp 文件支持接收方缩放 (RSS)。

启用 SMB 多通道后，SMB3 客户端通过支持单一 RSS 功能的网卡 (NIC) 来建立与 Azure NetApp 文件 SMB 服务器的多个 TCP 连接。 

要查看你的 Azure 虚拟机 NIC 是否支持 RSS，请按如下所示运行 `Get-SmbClientNetworkInterface` 命令并检查字段 `RSS Capable`： 

![显示 Azure 虚拟机 RSS 输出的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)


## <a name="multiple-nics-on-smb-clients"></a>SMB 客户端上的多个 NIC

不应在客户端上为 SMB 配置多个 NIC。 SMB 客户端将匹配 SMB 服务器返回的 NIC 计数。  每个存储卷只能从一个且仅一个存储终结点进行访问。  这意味着，对于任何给定的 SMB 关系，将仅使用一个 NIC。  

如下面的 `Get-SmbClientNetworkInterace` 输出所示，虚拟机具有2个网络接口：15 和12。  如下面的命令 `Get-SmbMultichannelConnection` 中所示，尽管有两个支持 RSS 的 NIC，但与 SMB 共享的连接中仅使用了接口 12；未使用接口 15。

![该屏幕截图显示了支持 RSS 的 NIC 的输出。](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="next-steps"></a>后续步骤  

- [有关 Azure NetApp 文件的常见问题解答](azure-netapp-files-faqs.md)
- 有关如何将 SMB 文件共享与 Azure NetApp 文件配合使用，请参阅 [Azure NetApp 文件：适用于 SMB 工作负载的托管企业文件共享](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1)。
