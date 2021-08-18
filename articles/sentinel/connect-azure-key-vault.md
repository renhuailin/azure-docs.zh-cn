---
title: 将 Azure 密钥保管库诊断日志连接到 Azure Sentinel
description: 了解如何使用 Azure Policy 将 Azure 密钥保管库诊断日志连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/22/2021
ms.author: yelevin
ms.openlocfilehash: ba268f75b770dfd3d19e7bc4750f2c466245279d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777996"
---
# <a name="connect-azure-key-vault-diagnostics-logs"></a>连接 Azure 密钥保管库诊断日志

Azure Key Vault 是一个用于安全地存储和访问机密的云服务。 机密是你想要严格控制对其的访问的任何内容，例如 API 密钥、密码、证书或加密密钥。

使用此连接器可将 Azure 密钥保管库诊断日志流式传输到 Azure Sentinel 中，以便持续监视所有实例中的活动。

了解有关[监视 Azure 密钥保管库](../azure-monitor/insights/key-vault-insights-overview.md)以及 [Azure 密钥保管库诊断遥测](../key-vault/general/logging.md)的详细信息。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

若将 Azure 密钥保管库日志引入 Azure Sentinel：

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 若要使用 Azure Policy 将日志流式处理策略应用到 Azure 密钥保管库资源，则你必须拥有策略分配范围的“所有者”角色。

## <a name="connect-to-azure-key-vault"></a>连接到 Azure 密钥保管库

此连接器使用 Azure Policy 将单个 Azure 密钥保管库日志流式处理配置应用到定义为某个范围的实例集合。 可以在连接器页面左侧的“数据类型”下看到从 Azure 密钥保管库引入的日志类型。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Azure 密钥保管库”，然后在预览窗格中选择“打开连接器页面”。 

1. 在连接器页面的“配置”部分，展开“大规模流式传输 Azure 密钥保管库的诊断日志” 。

1. 选择“启动 Azure Policy 分配向导”按钮。

    策略分配向导随即打开，可在其中创建名为“部署 - 为 Azure 密钥保管库配置诊断设置以将日志传送到 Log Analytics 工作区”的新策略。

    1. 在“基本信息”选项卡中，单击“作用域”下带三个点的按钮，选择你的订阅（或资源组）。 还可以添加说明。

    1. 在“参数”选项卡中，将“效果”和“设置名称”字段保留原样  。 从“Log Analytics 工作区”下拉列表中选择你的 Azure Sentinel 工作区。 其余的下拉字段表示可用的诊断日志类型。 将要引入的所有日志类型标记为“True”。

    1. 此策略将应用于将来添加的资源。 若也要对现有资源应用该策略，请选择“修正”选项卡，然后勾选“创建修正任务”复选框 。

    1. 在“查看 + 创建”选项卡中，单击“创建”   。 你的策略现已分配给所选的作用域。

> [!NOTE]
>
> 对于此特定数据连接器，仅当在过去 14 天内的某个时间点引入数据时，连接状态指示器（数据连接器库中的色带以及数据类型名称旁边的连接图标）才显示为“已连接”（绿色）。 一旦 14 天过去但未引入数据，连接器将显示为已断开连接。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何使用 Azure Policy 将 Azure 密钥保管库连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
