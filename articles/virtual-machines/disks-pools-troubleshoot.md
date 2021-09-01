---
title: Azure 磁盘池（预览）故障排除概述
description: 解决 Azure 磁盘池问题。 了解常见故障代码及其解决方式。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f14ae211e13c299807c6f1c43e8be11752fce008
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418334"
---
# <a name="troubleshoot-azure-disk-pools-preview"></a>排除 Azure 磁盘池（预览）故障

本文列出了与 Azure 磁盘池（预览）相关的一些常见失败代码。 文中还提供了可行的解决方法，并阐述了磁盘池的不同状态。

## <a name="disk-pool-status"></a>查看磁盘池状态

磁盘池及 iSCSI 目标均有四种状态：未知状态；运行状态；更新状态；已停止状态（已解除分配）。

未知状态表示资源处于错误或未知状态。 若要尝试恢复，请更新资源（如添加或删除磁盘/LUNS）或删除并重新部署磁盘池。

运行状态表示资源正在运行且状态正常。

更新状态表示资源正在进行更新。 这通常在部署期间或更新应用（如添加磁盘或 LUN）时发生。

已停止状态（已解除分配）表示资源已停止，且其基础资源已解除分配。 可重启资源以恢复磁盘池或 iSCSI 目标。

## <a name="common-failure-codes-when-deploying-a-disk-pool"></a>部署磁盘池时常见的故障代码
 
|代码  |说明  |
|---------|---------|
|UnexpectedError     |通常在发生后端不可恢复错误时出现。 重试部署。 如问题未解决，请联系　Azure 支持，并提供错误消息的跟踪 ID。         |
|部署失败及配置失败     |当 Azure 耗尽容量以预配指定区域中的 VM 时，会出现这种情况。 请再次重试部署。         |
|部署失败及超出配额     |用于部署磁盘池的订阅超出此区域的 VM 核心配额。 对于 Dsv3 系列，你可以[请求提高每个 Azure VM 系列的 vCPU 配额限制](../azure-portal/supportability/per-vm-quota-requests.md)。         |
|部署失败及违反政策     |订阅政策拦截了支持磁盘池所需的 Azure 资源部署。 请查看错误以获取更多详细信息。         |
|部署超时     |当磁盘池基础结构的部署无法继续进行，并且未在指定时间内完成时，会出现这种情况。 重试部署。 若问题仍然存在，请联系 Azure 支持，并提供错误消息的跟踪 ID。         |
|操作进行中     |磁盘池上正在进行的操作在运行中。 待该操作完成，再重试部署。         |

## <a name="common-failure-codes-when-enabling-iscsi-on-disk-pools"></a>在磁盘池上启用 iSCSI 时常见的失败代码

|代码  |说明  |
|---------|---------|
|目标状态应用错误     |在 iSCSI 目标配置无效且无法应用于磁盘池时出现。 重试部署。 若问题仍然存在，请联系 Azure 支持，并提供错误的跟踪 ID。         |
|目标状态应用超时错误     |在磁盘池基础结构停止响应资源提供程序时出现。 重试部署。 若问题仍然存在，请联系 Azure 支持，并提供错误的跟踪 ID。         |
|操作进行中     |磁盘池上正在进行的操作在运行中。 待该操作完成，再重试部署。         |

## <a name="next-steps"></a>后续步骤

[管理磁盘池（预览）](disks-pools-manage.md)