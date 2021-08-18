---
title: 创建托管专用终结点以连接到数据源结果
description: 本文介绍如何从 Azure Synapse 工作区创建数据源的托管专用终结点。
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 6ca00f185c3bfc0a10a949953dfa0e2b1e7a09a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737442"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>创建数据源的托管专用终结点

本文介绍如何在 Azure 中创建数据源的托管专用终结点。 有关详细信息，请参阅[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>步骤 1：在 Azurxe 门户中打开 Azure Synapse 工作区

你可以从 Azure Synapse Studio 创建数据源的托管专用终结点。 在 Azure 门户中选择“概述”选项卡，然后在“入门”部分的“打开 Synapse Studio”卡上选择“打开” 。

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>步骤 2：导航到 Synapse Studio 中的“托管虚拟网络”选项卡

在 Azure Synapse Studio 中，从左侧导航栏中选择“管理”选项卡。 选择“托管专用终结点”，然后选择“+ 新建” 。
![创建新的托管专用终结点](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>步骤 3：选择数据源类型

选择数据源类型。 在本例中，目标数据源为 ADLS Gen2 帐户。 选择“继续”。
![选择目标数据源类型](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>步骤 4：输入有关数据源的信息

在新窗口中，输入有关数据源的信息。 在此示例中，我们将创建 ADLS Gen2 帐户的托管专用终结点。 输入托管专用终结点的“名称”。 提供“Azure 订阅”和“存储帐户名称” 。 选择“创建”。
![输入目标数据源详细信息](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>步骤 5：验证托管专用终结点是否已成功创建

提交请求后，你将看到请求状态。 若要验证是否已成功创建托管专用终结点，请选中其“预配状态”。 可能需要等待 1 分钟时间，然后选择“刷新”以更新预配状态。 你可以看到已成功创建 ADLS Gen2 帐户的托管专用终结点。

你还可以看到“审批状态”为“挂起” 。 目标资源所有者可以批准或拒绝专用终结点连接请求。 如果所有者批准专用终结点连接请求，则将建立一个专用链接。 如果拒绝，则不会建立专用链接。
![托管专用终结点创建请求状态](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>后续步骤

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)