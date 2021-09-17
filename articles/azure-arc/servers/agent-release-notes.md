---
title: 已启用 Azure Arc 的服务器代理的新增功能
description: 本文提供了已启用 Azure Arc 的服务器代理的发行说明。 对于许多汇总问题，提供了指向更多详细信息的链接。
ms.topic: overview
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: d4008a41629ac2e71e1abdb91e30f2d6b9350538
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431597"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>已启用 Azure Arc 的服务器代理的新增功能

已启用 Azure Arc 的服务器 Connected Machine 代理不断地获得改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复

本页面每月更新，请不时回来查看。 若要查找 6 个月之前的项目，可以在[已启用 Azure Arc 的服务器代理的新增功能汇总](agent-release-notes-archive.md)中找到它们。

## <a name="august-2021"></a>2021 年 8 月

版本 1.10

### <a name="fixed"></a>固定

- 来宾配置策略代理现在可以配置和修正系统设置。 现有策略分配仍为“仅审核”。 详细了解 Azure Policy [来宾配置修正选项](../../governance/policy/concepts/guest-configuration-policy-effects.md)。
- 来宾配置策略代理现在每 48 小时重启一次，而不是每 6 小时一次。

## <a name="july-2021"></a>2021 年 7 月

版本 1.9

## <a name="new-features"></a>新增功能

添加了对印度语的支持

### <a name="fixed"></a>固定

修复了在美国西部 3 区域阻止扩展管理的 bug

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
 
## <a name="next-steps"></a>后续步骤

- 在多台混合计算机中评估或启用已启用了 Arc 的服务器之前，请先查看[连接的计算机代理概述](agent-overview.md)，以了解要求、有关代理的技术详细信息以及部署方法。

- 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。
