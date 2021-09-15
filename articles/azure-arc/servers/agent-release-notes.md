---
title: 已启用 Azure Arc 的服务器代理的新增功能
description: 本文提供了已启用 Azure Arc 的服务器代理的发行说明。 对于许多汇总问题，提供了指向更多详细信息的链接。
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: 54e7bc72884cc7bc9116c88e7cdb72e5af5f71cd
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770957"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>已启用 Azure Arc 的服务器代理的新增功能

已启用 Azure Arc 的服务器 Connected Machine 代理不断地获得改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复

## <a name="july-2021"></a>2021 年 7 月

版本 1.8

### <a name="new-features"></a>新增功能

- 提高了在 Red Hat 和 CentOS 系统上安装 Azure Monitor 代理扩展时的可靠性
- 添加了最大资源名称长度（54 个字符）的代理端强制执行
- 来宾配置策略改进：
  - 在 Linux 操作系统上添加了对基于 PowerShell 的来宾配置策略的支持
  - 添加了对同一服务器上同一来宾配置策略的多个分配的支持
  - 在 Windows 操作系统上将 PowerShell Core 升级到版本7.1

### <a name="fixed"></a>固定

- 如果代理无法将服务开始/停止事件写入 Windows 应用程序事件日志，代理将继续运行

## <a name="june-2021"></a>2021 年 6 月

版本 1.7

### <a name="new-features"></a>新增功能

- 提高了加入期间的可靠性：
  - 改进了 HIMDS 不可用时的重试逻辑
  - 如果无法获取 OS 信息，加入将继续，而不是中止
- 提高了在 Red Hat 和 CentOS 系统上安装 OMS 代理扩展时的可靠性

## <a name="may-2021"></a>2021 年 5 月

版本 1.6

### <a name="new-features"></a>新增功能

- 添加了对 SUSE Enterprise Linux 12 的支持
- 已将来宾配置代理更新至 1.26.12.0 版，以包括：

   - 在单独的进程中执行的策略。
   - 为扩展验证添加的 V2 签名支持。
   - 对数据日志记录进行的次要更新。

## <a name="april-2021"></a>2021 年 4 月

1.5 版

### <a name="new-features"></a>新增功能

- 添加了对 Red Hat Enterprise Linux 8 和 CentOS Linux 8 的支持。
- 用于将错误和详细输出定向到 stderr 的新 `-useStderr` 参数。
- 用于以 JSON 格式定向输出结果的新 `-json` 参数（与 -useStderr 一起使用时）。
- 收集其他实例元数据 - 制造商、模型和群集资源 ID（Azure Stack HCI 节点的）。
 
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
