---
title: Azure Kubernetes 服务 (AKS) 诊断概述
description: 了解 Azure Kubernetes 服务中的自我诊断群集。
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011552"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure Kubernetes 服务诊断（预览版）概述

排查 Azure Kubernetes 服务 (AKS) 群集问题在维护群集方面发挥着重要作用，特别是在群集运行任务关键工作负荷的情况下。 AKS 诊断是一种智能的自我诊断体验，具体如下：
* 帮助确定和解决群集中的问题。 
* 云原生。
* 不需要额外的配置或收费。

此功能目前以公共预览版提供。 

## <a name="open-aks-diagnostics"></a>打开 AKS 诊断

访问 AKS 诊断：

1. 在 [Azure 门户](https://portal.azure.com)中导航到 Kubernetes 群集。
1. 在左侧导航栏中单击“诊断并解决问题”，这将打开“AKS 诊断”。
1. 选择最能描述群集问题的类别，例如 _群集节点问题_，方法如下：
    * 使用主页磁贴中的关键字。
    * 在搜索栏中键入最能描述你的问题的关键字。

![主页](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>查看诊断报告

单击某个类别后，可以查看特定于群集的诊断报告。 诊断报告通过状态图标智能地标注群集中的任何问题。 可以通过单击“更多信息”来深入了解每个主题，以查看以下方面的详细描述：
* 问题
* 建议的操作
* 有用文档的链接
* 相关指标
* 记录数据 

诊断报告在运行各种检查后基于群集的当前状态生成。 这些诊断报告有助于查明群集的问题并了解后续步骤来解决此问题。

![诊断报告](./media/concepts-diagnostics/diagnostic-report.png)

![展开的诊断报告](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>群集见解

“群集见解”中提供以下诊断检查。

### <a name="cluster-node-issues"></a>群集节点问题

群集节点问题检查与节点相关的问题，这些问题会导致群集行为异常。

- 节点就绪问题
- 节点故障
- 资源不足
- 节点缺少 IP 配置
- 节点 CNI 故障
- 找不到节点
- 节点关机
- 节点身份验证失败
- 节点 kube-proxy 过时

### <a name="create-read-update--delete-crud-operations"></a>创建、读取、更新和删除 (CRUD) 操作

CRUD 操作检查导致群集中出现问题的任何 CRUD 操作。

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

标识和安全管理检测阻止与群集通信的身份验证和授权错误。

- 节点授权失败
- 401 错误
- 403 错误

## <a name="next-steps"></a>后续步骤

* 收集日志，以帮助你使用 [AKS Periscope](https://aka.ms/aksperiscope) 进一步排查群集问题。

* 阅读 AKS Day-2 操作指南的[会审实践部分](/azure/architecture/operator-guides/aks/aks-triage-practices)。

* 通过在标题中添加“[Diag]”，在 [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) 上发布你的问题或反馈。