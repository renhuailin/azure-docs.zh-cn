---
title: Azure Kubernetes 服务 (AKS) 诊断概述
description: 了解 Azure Kubernetes 服务中的自我诊断群集。
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: e8921152177d3e4534ca9fb48cf209aed6e1b27c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96183356"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure Kubernetes 服务诊断（预览版）概述

排查 Azure Kubernetes 服务 (AKS) 群集问题是维护群集的重要部分，特别是在群集运行任务关键工作负荷的情况下。 AKS 诊断是一种智能自我诊断体验，可帮助你识别和解决群集中的问题。 AKS 诊断是云原生诊断，无需额外配置或计费成本即可使用。

此功能目前以公共预览版提供。

## <a name="open-aks-diagnostics"></a>打开 AKS 诊断

访问 AKS 诊断：

- 在 [Azure 门户](https://portal.azure.com)中导航到 Kubernetes 群集。
- 在左侧导航栏中单击“诊断并解决问题”，这将打开“AKS 诊断”。
- 通过使用主页磁贴中的关键字，选择最能描述群集问题的类别，或在搜索栏中键入最能描述你的问题的关键字，例如“群集节点问题”。

![主页](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>查看诊断报告

单击某个类别后，可以查看特定于群集的诊断报告。 如果通过状态图标表示群集中存在任何问题，将智能地调用诊断报告。 单击“详细信息”可查看问题的详细说明、建议的操作、指向有用文档的链接、相关指标和记录数据，从而向下钻取每个主题。 运行各种检查之后，将基于群集的当前状态智能地生成诊断报告。 诊断报告是用于查明群集问题并找到解决问题的后续步骤的有用工具。

![诊断报告](./media/concepts-diagnostics/diagnostic-report.png)

![展开的诊断报告](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>群集见解

“群集见解”中提供以下诊断检查。

### <a name="cluster-node-issues"></a>群集节点问题

群集节点问题检查与节点相关的问题，这些问题可能会导致群集意外运行。

- 节点就绪问题
- 节点故障
- 资源不足
- 节点缺少 IP 配置
- 节点 CNI 故障
- 找不到节点
- 节点关机
- 节点身份验证失败
- 节点 kube-proxy 过时

### <a name="create-read-update--delete-operations"></a>创建、读取、更新和删除操作

CRUD 操作检查可能导致群集中出现问题的任何 CRUD 操作。

- 使用中的子网删除操作错误
- 网络安全组删除操作错误
- 使用中的路由表删除操作错误
- 引用的资源预配错误
- 公共 IP 地址删除操作错误
- 由于部署配额导致的部署失败
- 由于组织策略导致的操作错误
- 缺少订阅注册
- VM 扩展预配错误
- 子网容量
- 超过配额错误

### <a name="identity-and-security-management"></a>标识和安全管理

标识和安全管理检测可阻止与群集通信的身份验证和授权错误。

- 节点授权失败
- 401 错误
- 403 错误

## <a name="next-steps"></a>后续步骤

收集日志，以帮助你使用 [AKS Periscope](https://aka.ms/aksperiscope) 进一步排查群集问题。

阅读 AKS Day-2 操作指南的[会审实践部分](/azure/architecture/operator-guides/aks/aks-triage-practices)。

通过在标题中添加“[Diag]”，在 [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) 上发布你的问题或反馈。