---
title: 已启用 Azure Arc 的服务器代理的新增功能汇总
description: 已启用 Azure Arc 的服务器代理的“概述”部分中有新增功能发行说明，其中列出了 6 个月的新动向。 6 个月过后，这些项目将从主文章中删除并放入此文章中。
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 45f7ed97cf9e0fbb389ccf893f2674e2601ee7f9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441530"
---
# <a name="archive-for-whats-new-with-azure-arc-enabled-servers-agent"></a>已启用 Azure Arc 的服务器代理的新增功能汇总

[已启用 Azure Arc 的服务器代理的新增功能](agent-release-notes.md)这篇主要文章包含过去 6 个月的更新，而本文包含所有更早的信息。

已启用 Azure Arc 的服务器 Connected Machine 代理不断地获得改进。 本文提供以下事项的信息：

- 以前的版本
- 已知问题
- Bug 修复

## <a name="march-2021"></a>2021 年 3 月

版本 1.4

### <a name="new-features"></a>新增功能

- 添加了对专用终结点的支持（目前为受限预览版）。
- 展开了 azcmagent 的退出代码列表。
- 现在，可以使用 `--config` 参数从文件中读取代理配置参数。
- 收集新的实例元数据以确定服务器上是否安装了 Microsoft SQL Server

### <a name="fixed"></a>固定

网络终结点检查现在速度更快。

## <a name="december-2020"></a>2020 年 12 月

版本：1.3

### <a name="new-features"></a>新增功能

添加了对 Windows Server 2008 R2 SP1 的支持。

### <a name="fixed"></a>固定

解决了在 Linux 上无法成功安装自定义脚本扩展的问题。

## <a name="november-2020"></a>2020 年 11 月

版本：1.2

### <a name="fixed"></a>固定

解决了在基于 RPM 的分发上升级后，代理配置可能丢失的问题。

## <a name="october-2020"></a>2020 年 10 月

版本：1.1

### <a name="fixed"></a>固定

- 修复了用于处理备用 GC 守护程序单元文件位置的代理脚本。
- GuestConfig 代理可靠性更改。
- US Gov 弗吉尼亚州区域的 GuestConfig 代理支持。
- 如果出现故障，GuestConfig 代理扩展报告的消息将更详细。

## <a name="september-2020"></a>2020 年 9 月

版本：1.0（正式发布）

### <a name="plan-for-change"></a>更改计划

- 在将来的服务更新中将取消对预览版代理（低于 1.0 的所有版本）的支持。
- 已取消对回退终结点 `.azure-automation.net` 的支持。 如果有代理，则需要允许使用终结点 `*.his.arc.azure.com`。
- 如果在 Azure 中托管的虚拟机上安装了 Connected Machine 代理，则无法从已启用 Arc 的服务器资源安装和修改 VM 扩展。 这是为了避免从虚拟机的 Microsoft.Compute 和 Microsoft.HybridCompute 资源进行冲突的扩展操作 。 请使用计算机的 Microsoft.Compute 资源进行所有扩展操作。
- 来宾配置进程的名称已更改，在 Linux 上从 gcd 更改为 gcad，在 Windows 上从 gcservice 更改为 gcarcservice   。

### <a name="new-features"></a>新增功能

- 添加了 `azcmagent logs` 选项以收集支持信息。
- 添加了 `azcmagent license` 选项以显示 EULA。
- 添加了 `azcmagent show --json` 选项，以可轻松解析的格式输出代理状态。
- 在 `azcmagent show` 输出中添加了标志，以指示服务器是否在 Azure 中托管的虚拟机上。
- 添加了 `azcmagent disconnect --force-local-only` 选项以允许在无法访问 Azure 服务时重置本地代理状态。
- 添加了 `azcmagent connect --cloud` 选项以支持其他云。 在此版本中，在代理发布时，服务仅支持 Azure。
- 代理已本地化为 Azure 支持的语言。

### <a name="fixed"></a>固定

- 对连接性检查的改进。
- 更正了在 Linux 上升级代理时代理服务器设置丢失的问题。
- 解决了尝试在运行 Windows Server 2012 R2 的服务器上安装代理时的问题。
- 对扩展安装可靠性的改进

## <a name="august-2020"></a>2020 年 8 月

版本：0.11

- 此版本以前发布了对 Ubuntu 20.04 的支持。 由于某些 Azure VM 扩展不支持 Ubuntu 20.04，因此将取消对此版本 Ubuntu 的支持。

- 对扩展部署可靠性的改进。

### <a name="known-issues"></a>已知问题

如果使用的是较旧版本的 Linux 代理并且其配置为使用代理服务器，则需要在升级后重新配置代理服务器设置。 要执行此操作，请运行 `sudo azcmagent_proxy add http://proxyserver.local:83`。

## <a name="next-steps"></a>后续步骤

- 在多台混合计算机中评估或启用已启用了 Arc 的服务器之前，请先查看[连接的计算机代理概述](agent-overview.md)，以了解要求、有关代理的技术详细信息以及部署方法。

- 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。