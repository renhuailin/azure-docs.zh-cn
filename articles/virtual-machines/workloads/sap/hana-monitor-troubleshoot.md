---
title: 从 HANA 端针对 Azure SAP HANA（大型实例）进行监视和故障排除 | Microsoft Docs
description: 了解如何使用 SAP HANA 提供的资源对 Azure 上的 SAP HANA（大型实例）进行监视和故障排除。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/18/2021
ms.author: madhukan
ms.custom:
- H1Hack27Feb2017
- contperf-fy21q4
ms.openlocfilehash: 691f9390d291d63b1263ecbcd77d1aa7bed48167
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580955"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA 端的监视和故障排除

本文将介绍如何使用 SAP HANA 提供的资源对 Azure 上的 SAP HANA（大型实例）进行监视和故障排除。 

为了分析与 Azure 上的 SAP HANA（大型实例）相关的问题，需要缩小问题的根本原因的范围。 SAP 已发布大量文档来帮助你。 在以下 SAP 说明中可以找到与 SAP HANA 性能相关的常见问题解答：

- [SAP Note #2222200 - FAQ: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)（SAP 说明 #2222200 - 常见问题：SAP HANA 网络）
- [SAP Note #2100040 - FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)（SAP 说明 #2100040 - 常见问题：SAP HANA CPU）
- [SAP Note #199997 - FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/2177064)（SAP 说明 #199997 - 常见问题：SAP HANA 内存）
- [SAP Note #200000 - FAQ: SAP HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000)（SAP 说明 #200000 - 常见问题：SAP HANA 性能优化）
- [SAP Note #199930 - FAQ: SAP HANA Analysis](https://launchpad.support.sap.com/#/notes/1999930)（SAP 说明 #199930 - 常见问题：SAP HANA 分析）
- [SAP Note #2177064 - FAQ: SAP HANA Service Restart and Crashes](https://launchpad.support.sap.com/#/notes/2177064)（SAP 说明 #2177064 - 常见问题：SAP HANA 服务重新启动和崩溃）

## <a name="sap-hana-alerts"></a>SAP HANA 警报

首先，检查当前的 SAP HANA 警报日志。 在 SAP HANA Studio 中，转到“Administration Console: Alerts: Show: all alerts”（管理控制台: 警报: 显示: 所有警报）。 此选项卡会显示超出设置的最小和最大阈值的值（可用物理内存、CPU 使用情况等）的所有相关 SAP HANA 警报。 默认情况下，检查结果每隔 15 分钟自动刷新一次。

![在 SAP HANA Studio 中，转到“Administration Console: Alerts: Show: all alerts”（管理控制台: 警报: 显示: 所有警报）](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

对于由于阈值设置不正确而触发的警报，请重置为默认值，或指定更合理的阈值。

![重置为默认值，或指定更合理的阈值](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

以下警报可能表明 CPU 资源出现问题：

- 主机 CPU 使用率（警报 5）
- 最近的保存点操作（警报 28）
- 保存点持续时间（警报 54）

可以通过以下内容来判断 SAP HANA 数据库的 CPU 消耗量偏高：

- 系统针对当前或过去的 CPU 使用率引发了警报 5（主机 CPU 使用率）
- 概述屏幕上显示的 CPU 使用率

![概述屏幕上显示的 CPU 使用率](./media/troubleshooting-monitoring/image3-cpu-usage.png)

“负载”图可能会显示 CPU 消耗量偏高或者在过去偏高：

![“负载”图可能会显示 CPU 消耗量偏高或者在过去偏高](./media/troubleshooting-monitoring/image4-load-graph.png)

由于 CPU 使用率过高而触发的警报可能由以下多种原因引起：
- 执行某些事务
- 数据加载
- 未响应的作业
- 长时间运行的 SQL 语句
- 较差的查询性能（例如，在 HANA 多维数据集上使用 BW）

有关详细的 CPU 使用情况故障排除步骤，请参阅 [SAP HANA 故障排除：CPU 相关原因和解决方案](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/4fbc915462db406aa2fe92b708b95189.html?q=%20SAP%20HANA%20Troubleshooting:%20CPU%20Related%20Causes%20and%20Solutions)。

## <a name="operating-system-os"></a>操作系统 (OS)

对于 Linux 上 SAP HANA 的一项重要检查是确保禁用“透明大页”。 有关详细信息，请参阅 [SAP 说明 #2131662 - SAP HANA 服务器上的透明大页 (THP)](https://launchpad.support.sap.com/#/notes/2131662)。

可通过以下 Linux 命令检查是否已启用透明大页：cat /sys/kernel/mm/transparent\_hugepage/enabled
- 如果 always 括在方括号中，则表示已启用透明大页：[always] madvise never
- 如果 never 括在方括号中，则表示已禁用透明大页：always madvise [never]

以下 Linux 命令应该不会返回任何结果：**rpm qa | grep ulimit**。 如果显示 _ulimit_ 已安装，请立即卸载它。

## <a name="memory"></a>内存

你可能会注意到分配给 SAP HANA 数据库的内存量超过了预期。 以下警报指示存在内存用量偏高的问题：

- 主机物理内存用量（警报 1）
- 名称服务器的内存用量（警报 12）
- 列存储表的总内存用量（警报 40）
- 服务的内存用量（警报 43）
- 列存储表主存储的内存用量（警报 45）
- 运行时转储文件（警报 46）

有关详细的内存故障排除步骤，请参阅 [SAP HANA 故障排除：内存问题的根本原因](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/3a2ea5c4593b4b8d823b5b48152bd1d4.html)。

## <a name="network"></a>网络

请参阅 [SAP 说明 #2081065 - SAP HANA 网络故障排除](https://launchpad.support.sap.com/#/notes/2081065)并执行此 SAP 说明中的网络故障排除步骤。

1. 分析服务器与客户端之间的往返时间。
    - 运行 SQL 脚本 [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)_。_
  
2. 分析节点间通信。
    - 运行 SQL 脚本 [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_。_

3. 运行 Linux 命令 ifconfig（输出会显示是否发生了任何丢包情况）。
4. 运行 Linux 命令 **tcpdump**。

此外，请使用开源 [IPERF](https://iperf.fr/) 工具（或类似的工具）来测量实际的应用程序网络性能。

有关详细的网络故障排除步骤，请参阅 [SAP HANA 故障排除：网络性能和连接问题](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/a3ccdff1aedc4720acb24ed8826938b6.html?q=Networking%20Performance%20and%20Connectivity%20Problems)。

## <a name="storage"></a>存储

假设 I/O 性能存在问题。 最终用户可能会发现应用程序或整个系统运行缓慢、无响应，甚至停止响应。 在 SAP HANA Studio 中的“卷”选项卡上，可以查看附加的卷，以及每个服务使用了哪些卷。

![在 SAP HANA Studio 中的“Volumes”（卷）选项卡上，可以查看附加的卷，以及每个服务使用了哪些卷](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

在屏幕下半部分（在“卷”选项卡上），可以查看卷的详细信息，例如文件和 I/O 统计信息。

![在屏幕下半部分，可以查看卷的详细信息，例如文件和 I/O 统计信息](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

有关 I/O 故障排除步骤，请参阅 [SAP HANA 故障排除：I/O 相关的根本原因和解决方案](https://help.sap.com/viewer/4e9b18c116aa42fc84c7dbfd02111aba/2.0.05/en-US/dc6ff98fa36541e997e4c719a632cbd8.html?q=I%2FO%20Related%20Root%20Causes%20and%20Solutions)。 有关与磁盘相关的故障排除步骤，请参阅 [SAP HANA 故障排除：磁盘相关的根本原因和解决方案](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/474cb08a715c42fe9f7cc5efdc599959.html?q=Disk%20Related%20Root%20Causes%20and%20Solutions)。

## <a name="diagnostic-tools"></a>诊断工具

HANA\_Configuration\_Minichecks 执行 SAP HANA 运行状况检查。 此工具将返回潜在的严重技术问题，这些问题应该已在 SAP HANA Studio 中以警报的形式指出。

1. 请参阅 [SAP Note #1969700 - SQL statement collection for SAP HANA](https://launchpad.support.sap.com/#/notes/1969700)（SAP 说明 #1969700 - SAP HANA 的 SQL 语句集合），并下载该说明随附的 SQL Statements.zip 文件。 将此 .zip 文件存储在本地硬盘上。

2. 在 SAP HANA Studio 中的“System Information”（系统信息）选项卡上，右键单击“Name”（名称）并选择“Import SQL Statements”（导入 SQL 语句）。  

    ![在 SAP HANA Studio 中的“System Information”（系统信息）选项卡上，右键单击“Name”（名称）并选择“Import SQL Statements”（导入 SQL 语句）](./media/troubleshooting-monitoring/image7-import-statements-a.png)

3. 选择存储在本地的 SQL Statements.zip 文件；将导入包含相应 SQL 语句的文件夹。 此时，可以使用这些 SQL 语句运行多种不同的诊断检查。

    例如，要测试 SAP HANA 系统复制带宽要求，请在 SQL 控制台中右键单击“Replication: Bandwidth”（复制: 带宽）下面的“Bandwidth”（带宽）语句，并选择“Open”（打开）。

    整个 SQL 语句会打开，允许用户更改然后执行输入参数（modification 节）。

    ![整个 SQL 语句会打开，允许用户更改然后执行输入参数（modification 节）](./media/troubleshooting-monitoring/image8-import-statements-b.png)

4. 另一种做法是右键单击“复制: 概述”下面的语句。 从上下文菜单中选择“Execute”（执行）：

    ![另一种做法是右键单击“复制: 概述”下面的语句。 从上下文菜单中选择“Execute”（执行）](./media/troubleshooting-monitoring/image9-import-statements-c.png)

    你将查看有助于进行故障排除的信息：

    ![你将查看有助于进行故障排除的信息](./media/troubleshooting-monitoring/image10-import-statements-d.png)

5. 在 HANA\_Configuration\_Minichecks 中执行与上述相同的操作，检查 _C_（严重）列中出现的所有 _X_ 标记。

    示例输出：

    用于常规 SAP HANA 检查的 **HANA\_Configuration\_MiniChecks\_Rev102.01+1**。

    ![用于常规 SAP HANA 检查的 HANA\_Configuration\_MiniChecks\_Rev102.01+1](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

    用于概述哪些 SAP HANA 服务当前正在运行的 HANA\_Services\_Overview。

    ![用于概述哪些 SAP HANA 服务当前正在运行的 HANA\_Services\_Overview](./media/troubleshooting-monitoring/image12-services-overview.png)

    用于显示 SAP HANA 服务信息（CPU、内存等）的 **HANA\_Services\_Statistics**。

    ![用于显示 SAP HANA 服务信息的 HANA\_Services\_Statistics](./media/troubleshooting-monitoring/image13-services-statistics.png)

    用于显示有关 SAP HANA 实例的常规信息的 **HANA\_Configuration\_Overview\_Rev110+** 。

    ![用于显示有关 SAP HANA 实例的常规信息的 HANA\_Configuration\_Overview\_Rev110+](./media/troubleshooting-monitoring/image14-configuration-overview.png)

    用于检查 SAP HANA 参数的 **HANA\_Configuration\_Parameters\_Rev70+** 。

    ![用于检查 SAP HANA 参数的 HANA\_Configuration\_Parameters\_Rev70+](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

## <a name="next-steps"></a>后续步骤

了解如何使用 STONITH 设备在 SUSE 操作系统上设置高可用性。

> [!div class="nextstepaction"]
> [使用 STONITH 在 SUSE 中进行高可用性设置](ha-setup-with-stonith.md)
