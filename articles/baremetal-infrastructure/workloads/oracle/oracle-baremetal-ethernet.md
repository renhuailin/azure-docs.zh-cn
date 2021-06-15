---
title: 适用于 Oracle 的 BareMetal 的以太网配置
description: 了解适用于 Oracle 工作负载的 BareMetal 实例上的以太网接口配置。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: b3183e660b8775e8f3c9137f27220e9db97deec6
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578595"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>适用于 Oracle 的 BareMetal 的以太网配置

在本文中，我们将了解适用于 Oracle 工作负载的 BareMetal 实例上的以太网接口配置。

适用于 Oracle 的每个已预配 BareMetal 实例都预配置了以太网接口集。 以太网接口分为四种类型：

- 用于客户端访问或通过客户端访问进行使用。
- 用于节点到节点通信。 所有服务器上都配置了此接口（不论所请求的拓扑为何）。 它仅用于横向扩展方案。
- 用于节点到存储连接。
- 用于灾难恢复 (DR) 设置和连接到 Global Reach 以实现跨区域连接。

## <a name="architecture"></a>体系结构

下图说明了预配置了以太网接口的 BareMetal 基础结构的体系结构。 

[![显示适用于 Oracle 工作负载的预配置以太网接口的体系结构的图。](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

默认配置附带了一个客户端 IP 接口 (eth1)，该接口从 Azure 虚拟网络 (VNET) 进行连接，可用于使用安全外壳 (SSH) 来访问 BareMetal 实例。

> [!NOTE]
> 对于来自不同 Azure VNET 的其他客户端接口 (eth10)，请联系 Microsoft CSA 以提交服务请求。 例如，如果要开发/测试以及生产/DR 环境。

| **NIC 逻辑接口** | **带 RHEL 操作系统的名称** | **用例** |
| --- | --- | --- |
| A | net1.tenant | 客户端到 BareMetal 实例 |
| C | net2.tenant | 节点到存储；支持协调和访问存储控制器以管理存储环境。 |
| B | net3.tenant | 节点到节点（专用互连） |
| C | net4.tenant | 保留/iSCSI |
| C | net5.tenant | 保留/日志备份 |
| C | net6.tenant | Node-to-storage_Data Backup（RMAN、快照） |
| C | net7.tenant | Node-to-storage_dNFS-Pri；提供与 NetApp 存储阵列的连接。 |
| C | net8.tenant | Node-to-storage_dNFS-Sec；提供与 NetApp 存储阵列的连接。 |
| D | net9.tenant | Global Reach 设置的 DR 连接，用于访问另一个区域中的 BMI。 |
| A | net10.tenant\* | \*客户端到 BareMetal 实例
 |

如有必要，可以自行定义更多网络接口控制器 (NIC) 卡。 但是，无法更改现有 NIC 的配置。

## <a name="usage-rules"></a>用法规则

对于 BareMetal 实例，默认值在四个逻辑 NIC 上具有九个分配的 IP 地址。 下列用法规则适用：

- 以太网“A”应具有一个位于提交给 Microsoft 的服务器 IP 池地址范围以外的已分配 IP 地址。 此 IP 地址不应在操作系统的 etc/hosts 目录中进行维护。
- 以太网“B”应专门在 etc/hosts 目录中进行维护，以便在各个实例之间进行通信。 在横向扩展 Oracle Real Application Clusters (RAC) 配置中将这些 IP 地址作为用于节点间配置的 IP 地址进行维护。
- 以太网“C”应具有一个用于与 NFS 存储通信的已分配 IP 地址。 此类型的地址不应在 etc/hosts 目录中进行维护。
- 以太网“D”应专门用于 Global Reach 设置以便访问 DR 区域中的 BareMetal 实例。

## <a name="next-steps"></a>后续步骤

详细了解适用于 Oracle 的 BareMetal 基础结构体系结构。

> [!div class="nextstepaction"]
> [适用于 Oracle 的 BareMetal 基础结构的体系结构](oracle-baremetal-architecture.md)
