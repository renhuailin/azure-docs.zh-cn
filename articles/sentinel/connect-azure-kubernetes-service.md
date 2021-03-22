---
title: 将 Azure Kubernetes 服务 (AKS) 诊断日志连接到 Azure Sentinel
description: 了解如何使用 Azure Policy 将 Azure Kubernetes 服务诊断日志连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: c3a4593aa92acededf9784974b2a1e2dd3cfb319
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507171"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>连接 Azure Kubernetes 服务诊断日志

Azure Kubernetes 服务 (AKS) 是一项完全托管的开放源代码容器业务流程服务，可用于在群集环境中部署、缩放和管理 Docker 容器和基于容器的应用程序。

使用此连接器，你可以将 Azure Kubernetes 服务 (AKS) 诊断日志流式传输到 Azure Sentinel 中，便于你持续监视所有实例中的活动。 

详细了解如何[监视 Azure Kubernetes 服务](../azure-monitor/containers/container-insights-overview.md)，并了解 [AKS 诊断遥测](../aks/view-control-plane-logs.md)的相关信息。

## <a name="prerequisites"></a>先决条件

若要将 AKS 日志引入 Azure Sentinel：

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 要使用 Azure Policy 将日志流式处理策略应用到 AKS 资源，你必须已分配有对策略分配作用域的所有者角色。

## <a name="connect-to-azure-kubernetes-service"></a>连接到 Azure Kubernetes 服务

此连接器使用 Azure Policy 将单个 Azure Kubernetes 服务日志流式处理配置应用于定义为作用域的实例集合。 你可以在连接器页面左侧的“数据类型”下看到从 Azure Kubernetes 服务中引入的日志类型。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Azure Kubernetes 服务 (AKS)”，然后在预览窗格中选择“打开连接器页面”。

1. 在连接器页面的“配置”部分，展开“启用 Azure Kubernetes 服务 (AKS) 上的诊断日志”。

1. 选择“启动 Azure Policy 分配向导”按钮。

    随即打开策略分配向导，准备创建一个名为“部署 - 将 Azure Kubernetes 服务的诊断设置配置为 Log Analytics 工作区”的新策略。

    1. 在“基本信息”选项卡中，单击“作用域”下带三个点的按钮，选择你的订阅（或资源组）。 还可以添加说明。

    1. 在“参数”选项卡中，从“Log Analytics 工作区”下拉列表中选择 Azure Sentinel 工作区。 其余的下拉字段表示可用的诊断日志类型。 将要引入的所有日志类型标记为“True”。

    1. 若要对现有资源应用策略，请选择“修正”选项卡，然后勾选“创建修正任务”复选框。

    1. 在“查看 + 创建”选项卡中，单击“创建”   。 你的策略现已分配给所选的作用域。

> [!NOTE]
>
> 对于这个特定的数据连接器，只有当数据在过去两周内的某个时间点被引入的情况下，连接状态指示器（数据连接器库中的色条和数据类型名称旁边的连接图标）才会显示为“已连接”（绿色）。 一旦两周过去但没有数据引入，连接器将显示为已断开。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Azure Kubernetes 服务连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
