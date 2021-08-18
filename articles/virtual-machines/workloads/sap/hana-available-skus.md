---
title: Azure SAP HANA（大型实例）的 SKU | Microsoft Docs
description: 了解 Azure SAP HANA（大型实例）的可用 SKU。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI、HANA、SKU、S896、S224、S448、S672、Optane、SAP
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, references_regions
ms.openlocfilehash: 48fd56e204a29f61ab165a6aa4d349c5095ad560
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113218360"
---
# <a name="available-skus-for-hana-large-instances"></a>适用于 HANA 大型实例的 SKU

## <a name="baremetal-infrastructure-availability-by-region"></a>BareMetal 基础结构可用性（按区域）

基于修订版 4.2* 的 BareMetal 基础结构（经认证用于 SAP HANA 工作负载）服务在以下区域中提供：
- 西欧
- 北欧
- 德国中西部（具有区域支持）
- 美国东部（具有区域支持）
- 美国东部 2
- 美国中南部
- 美国西部 2（具有区域支持）

基于修订版 3* 的 BareMetal 基础结构（经认证用于 SAP HANA 工作负载）服务在以下区域的可用性受限：
- 美国西部
- 美国东部 
- 澳大利亚东部 
- 澳大利亚东南部
- Japan East

## <a name="list-of-available-azure-large-instances"></a>可用的 Azure 大型实例列表

下面是可用的 Azure 大型实例（也称为 BareMetal 基础结构实例）列表。

> [!IMPORTANT]
> 请注意，第一栏表示列表中每个大型实例类型的 HANA 证书状态。 列应与以字母 S 开头的 Azure SKU 的 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)关联。


| SAP HANA 认证 | 建模 | 内存总量 | 内存 DRAM | 内存 Optane | 存储 | 可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | Azure 上的 SAP HANA S96<br /> — 2 x Intel® Xeon® 处理器 E7-8890 v4 <br /> 48 CPU 内核和 96 CPU 线程 |  768 GB | 768 GB | --- | 3.0 TB | 可用 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | Azure 上的 SAP HANA S224<br /> – 4 x Intel® Xeon® Platinum 8276 处理器 <br /> 112 CPU 内核和 224 CPU 线程 |  3.0 TB | 3.0 TB | --- | 6.3 TB | 可用 |
| YES <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | Azure S224m 上的 SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 处理器 <br /> 112 CPU 内核和 224 CPU 线程 |  6.0 TB | 6.0 TB | --- | 10.5 TB | 可用 |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2522)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | Azure 上的 SAP HANA S224om<br /> – 4 x Intel® Xeon® Platinum 8276 处理器 <br /> 112 CPU 内核和 224 CPU 线程 | 6.0 TB |  3.0 TB |  3.0 TB | 10.5 TB | 可用 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2528)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2527) | Azure 上的 SAP HANA S224oo<br /> – 4 x Intel® Xeon® Platinum 8276 处理器 <br /> 112 CPU 内核和 224 CPU 线程 | 4.5 TB |  1.5 TB |  3.0 TB | 8.4 TB | 可用 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2526)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2525) | Azure 上的 SAP HANA S224ooo<br /> – 4 x Intel® Xeon® Platinum 8276 处理器 <br /> 112 CPU 内核和 224 CPU 线程 | 7.5 TB |  1.5 TB |  6.0 TB | 12.7 TB | 可用 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2524)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2523) | Azure 上的 SAP HANA S224oom<br /> – 4 x Intel® Xeon® Platinum 8276 处理器 <br /> 112 CPU 内核和 224 CPU 线程 | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB | 可用 |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | Azure 上的 SAP HANA S384<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  4.0 TB | 4.0 TB | --- | 16 TB | 可用 |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | Azure 上的 SAP HANA S384m<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  6.0 TB | 6.0 TB | --- | 18 TB |  可用  |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | Azure 上的 SAP HANA S384xm<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  8.0 TB | 8.0 TB | --- | 22 TB | 可用 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | Azure 上的 SAP HANA S448<br /> – 8 x Intel® Xeon® Platinum 8276 处理器 <br /> 224 CPU 内核和 448 CPU 线程 | 6.0 TB |  6.0 TB |  --- | 10.5 TB | 可用（仅适用于修订版 4.2） |
| 是 <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | Azure 上的 SAP HANA S448m<br /> – 8 x Intel® Xeon® Platinum 8276 处理器 <br /> 224 CPU 内核和 448 CPU 线程 | 12.0 TB |  12.0 TB |  --- | 18.9 TB | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S448oo<br /> – 8 x Intel® Xeon® Platinum 8276 处理器 <br /> 224 CPU 内核和 448 CPU 线程 | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB  | 可用（仅适用于修订版 4.2） |
|  是 <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2531)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2534)  | Azure 上的 SAP HANA S448om<br /> – 8 x Intel® Xeon® Platinum 8276 处理器 <br /> 224 CPU 内核和 448 CPU 线程 | 12.0 TB |  6.0 TB |  6.0 TB | 18.9 TB  | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S448ooo<br /> – 8 x Intel® Xeon® Platinum 8276 处理器 <br /> 224 CPU 内核和 448 CPU 线程 | 15.0 TB |  3.0 TB |  12.0 TB | 23.2 TB  | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S448oom<br /> – 8 x Intel® Xeon® Platinum 8276 处理器 <br /> 224 CPU 内核和 448 CPU 线程 | 18.0 TB |  6.0 TB |  12.0 TB | 27.4 TB  | 可用（仅适用于修订版 4.2） |
| 是 <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | Azure 上的 SAP HANA S576m<br /> – 12 x Intel® Xeon® 处理器 E7-8890 v4<br /> 288 CPU 内核和 576 CPU 线程 |  12.0 TB | 12.0 TB | --- | 28 TB | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S576xm<br /> – 12 x Intel® Xeon® 处理器 E7-8890 v4<br /> 288 CPU 内核和 576 CPU 线程 |  18.0 TB | 18.0 | --- |  41 TB | 可用 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | Azure 上的 SAP HANA S672<br /> – 12 x Intel® Xeon® Platinum 8276 处理器 <br /> 336 CPU 内核和 672 CPU 线程 | 9.0 TB |  9.0 TB |  --- | 14.7 TB | 可用（仅适用于修订版 4.2） |
| 是 <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2530)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | Azure 上的 SAP HANA S672m<br /> – 12 x Intel® Xeon® Platinum 8276 处理器 <br /> 336 CPU 内核和 672 CPU 线程 | 18.0 TB |  18.0 TB |  --- | 27.4 TB | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S672oo<br /> – 12 x Intel® Xeon® Platinum 8276 处理器 <br /> 336 CPU 内核和 672 CPU 线程 | 13.5 TB |  4.5 TB |  9.0 TB | 21.1 TB  | 可用（仅适用于修订版 4.2） |
| 是 <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2529)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2533) | Azure 上的 SAP HANA S672om<br /> – 12 x Intel® Xeon® Platinum 8276 处理器 <br /> 336 CPU 内核和 672 CPU 线程 | 18.0 TB |  9.0 TB |  9.0 TB | 27.4 TB  | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S672ooo<br /> – 12 x Intel® Xeon® Platinum 8276 处理器 <br /> 336 CPU 内核和 672 CPU 线程 | 22.5 TB |  4.5 TB |  18.0 TB | 33.7 TB  | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S672oom<br /> – 12 x Intel® Xeon® Platinum 8276 处理器 <br /> 336 CPU 内核和 672 CPU 线程 | 27.0 TB |  9.0 TB |  18.0 TB | 40.0 TB  | 可用（仅适用于修订版 4.2） |
| 是 <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | Azure 上的 SAP HANA S768m<br /> – 16 x Intel® Xeon® 处理器 E7-8890 v4<br /> 384 CPU 内核和 768 CPU 线程 |  16.0 TB | 16.0 TB | -- | 36 TB | 可用 |
| 是 | Azure 上的 SAP HANA S768xm<br /> – 16 x Intel® Xeon® 处理器 E7-8890 v4<br /> 384 CPU 内核和 768 CPU 线程 |  24.0 TB | 24.0 TB | --- | 56 TB | 可用 |
|  YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | Azure 上的 SAP HANA S896<br /> – 16 x Intel® Xeon® Platinum 8276 处理器 <br /> 448 CPU 内核和 896 CPU 线程 | 12.0 TB |  12.0 TB |  --- | 18.9 TB | 可用（仅适用于修订版 4.2） |
| 是 <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | Azure 上的 SAP HANA S896m<br /> – 16 x Intel® Xeon® Platinum 8276 处理器 <br /> 448 CPU 内核和 896 CPU 线程 | 24.0 TB | 24.0 TB | -- | 35.8 TB | 可用 |
| 是 | Azure 上的 SAP HANA S896oo<br /> – 16 x Intel® Xeon® Platinum 8276 处理器 <br /> 448 CPU 内核和 896 CPU 线程 | 18.0 TB |  6.0 TB |  12.0 TB | 27.4 TB  | 可用（仅适用于修订版 4.2） |
| 是  <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2529)、[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2532) | Azure 上的 SAP HANA S896om<br /> – 16 x Intel® Xeon® Platinum 8276 处理器 <br /> 448 CPU 内核和 896 CPU 线程 | 24.0 TB |  12.0 TB |  12.0 TB | 35.8 TB  | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S896ooo<br /> – 16 x Intel® Xeon® Platinum 8276 处理器 <br /> 448 CPU 内核和 896 CPU 线程 | 30.0 TB |  6.0 TB |  24.0 TB | 44.3 TB  | 可用（仅适用于修订版 4.2） |
| 是 | Azure 上的 SAP HANA S896oom<br /> – 16 x Intel® Xeon® Platinum 8276 处理器 <br /> 448 CPU 内核和 896 CPU 线程 | 36.0 TB |  12.0 TB |  24.0 TB | 52.7 TB  | 可用（仅适用于修订版 4.2） |
| 是 <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | Azure 上的 SAP HANA S960m<br /> – 20 x Intel® Xeon® 处理器 E7-8890 v4<br /> 480 CPU 内核和 960 CPU 线程 |  20.0 TB | 20.0 TB | -- | 46 TB | 可用（仅适用于修订版 4.2） |


- CPU 内核数 = 服务器单元处理器之和的非超线程 CPU 内核数的总和。
- CPU 线程数 = 服务器单元处理器之和的超线程 CPU 内核数所提供的计算线程总和。 大多数单元都默认配置为使用超线程技术。
- 根据供应商的建议，S768m、S768xm 和 S960m 未配置为使用超线程来运行 SAP HANA。


> [!IMPORTANT]
> 尽管仍支持以下 SKU，但无法再购买：S72、S72m、S144、S144m、S192 和 S192m。

选择的具体配置取决于工作负荷、CPU 资源和所需的内存。 OLTP 工作负荷可以利用针对 OLAP 工作负荷进行了优化的 SKU。 

两种不同类的硬件将 SKU 分为：

- S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m、S224oo、S224om、S224ooo、S224oom 称为 SKU 的“类型 I 类”。
- 所有其他 SKU 称为 SKU 的“类型 II 类”。
- 如果你对尚未在 SAP 硬件目录中列出的 SKU 感兴趣，请联系你的 Microsoft 帐户团队获取详细信息。 

## <a name="tenant-considerations"></a>租户注意事项

不会将整个 HANA 大型实例模具以独占方式分配给单个客户使用。 这也适用于通过 Azure 中部署的网络结构连接的计算和存储资源的机架。 HANA 大型实例基础结构（例如 Azure）部署在以下三个级别中相互隔离的不同客户“租户”：

- **网络**：在 HANA 大型实例模具中通过虚拟网络实现隔离。
- **存储**：通过分配了存储卷的存储虚拟机实现隔离，并在租户之间隔离存储卷。
- **计算**：专用于单个租户的服务器单元分配。 不对服务器单元进行硬分区或软分区。 租户之间不共享单个服务器或主机。 

不同租户之间的 HANA 大型实例单元部署相互不可见。 在 HANA 大型实例模具级别上，不同租户中部署的 HANA 大型实例单元也无法直接相互通信。 在 HANA 大型实例模具级别上，只有一个租户中的 HANA 大型实例单元才能相互通信。

大型实例模具中部署的每个租户分配给一个 Azure 订阅以进行计费。 但在网络级别，则可从其他 Azure 订阅的虚拟网络进行访问，只要是属于同一 Azure 合约即可。 如果在部署时使用了同一 Azure 区域的另一 Azure 订阅，则也可选择要求隔离的 HANA 大型实例租户。

## <a name="sap-hana-on-hana-large-instances-vs-on-vms"></a>HANA 大型实例上的 SAP HANA 与 VM 上的 SAP HANA

在 HANA 大型实例上运行 SAP HANA 与在 Azure 中部署的 VM 上运行 SAP HANA 之间有重大差别：

- Azure 上的 SAP HANA（大型实例）没有虚拟化层。 可以利用底层裸机硬件的性能。
- 与 Azure 不同，Azure 上的 SAP HANA（大型实例）服务器专用于特定客户。 服务器单元或主机不可能进行硬分区或软分区。 因此，HANA 大型实例单元作为一个整体分配给租户，并在这种情况下分配。 重新启动或关闭服务器不会自动导致操作系统和 SAP HANA 被部署在另一台服务器上。 （对于 I 类 SKU，唯一的例外是当服务器可能遇到了问题并且需要在另一台服务器上重新部署时。）
- 在 Azure 中，主机处理器类型是根据最佳性价比选择的，与之不同，为 Azure 上的 SAP HANA（大型实例）选择的处理器类型是 Intel E7v3 和 E7v4 处理器系列中性能最高的类型。

## <a name="next-steps"></a>后续步骤
了解如何调整 HANA 大型实例大小。

> [!div class="nextstepaction"]
> [HLI 调整大小](hana-sizing.md)
