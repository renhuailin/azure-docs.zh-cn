---
title: 使用 Azure 防火墙工作簿监视日志
description: Azure 防火墙工作簿为 Azure 防火墙数据分析和在 Azure 门户中创建丰富的视觉对象报表提供了灵活的画布。
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831835"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>使用 Azure 防火墙工作簿监视日志

Azure 防火墙工作簿为 Azure 防火墙数据分析提供了一个灵活的画布。 该画布可用于在 Azure 门户中创建丰富的视觉对象报表。 你可以利用跨 Azure 部署的多个防火墙，并将其组合成统一的交互式体验。

可以深入了解 Azure 防火墙事件，了解应用程序和网络规则，并查看跨 URL、端口和地址的防火墙活动统计信息。 借助 Azure 防火墙工作簿，可以筛选防火墙和资源组，并且可以按类别进行动态地筛选，以便在调查日志中的问题时，数据集易于读取。 

## <a name="prerequisites"></a>先决条件

开始之前，应该通过 Azure 门户[启用诊断日志记录](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal)。 另请参阅 [Azure 防火墙日志和指标](logs-and-metrics.md)，以获取关于可用于 Azure 防火墙的诊断日志和指标的概述。

## <a name="get-started"></a>入门

若要部署工作簿，请转到[适用于 Azure 防火墙的 Azure Monitor 工作簿](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook)，然后按照页面上的说明进行操作。 Azure 防火墙工作簿旨在跨多租户、多订阅工作，并且可对多个防火墙进行筛选。

## <a name="overview-page"></a>概述页

概述页面提供了跨工作区、时间和防火墙进行筛选的方法。 它按时间跨防火墙和日志类型显示事件 (应用程序、网络、威胁 intel、DNS proxy) 。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Azure 防火墙工作簿概述":::

## <a name="application-rule-log-statistics"></a>应用程序规则日志统计信息

此页面显示一段时间内 IP 地址的唯一源、应用程序规则计数的使用情况、一段时间内已拒绝/已允许的 FQDN，以及经过筛选的数据。 可以根据 IP 地址筛选数据。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Azure 防火墙工作簿应用程序规则日志":::

## <a name="network-rule-log-statistics"></a>网络规则日志统计信息

此页提供按规则操作-允许/拒绝、目标端口按 IP 和 DNAT 进行查看的视图。 你还可以按操作、端口和目标类型进行筛选。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Azure 防火墙工作簿网络规则日志":::

还可以根据时间范围筛选日志：

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Azure 防火墙工作簿网络规则日志时间范围":::

## <a name="investigations"></a>调查

可以查看日志，并根据源 IP 地址详细了解资源。 可以获取虚拟机名称和网络接口名称等信息。 可以轻松地从日志中对资源进行筛选。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure 防火墙工作簿调查":::

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 防火墙诊断信息](firewall-diagnostics.md)
