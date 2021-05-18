---
title: 配置警报 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 本文介绍如何配置和访问 Azure Database for PostgreSQL - 超大规模 (Citus) 的指标警报
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: f5557140d77865a6d4c44316cecd512f877736e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577078"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>使用 Azure 门户设置 Azure Database for PostgreSQL - 超大规模 (Citus) 的指标警告

本文介绍如何使用 Azure 门户设置 Azure Database for PostgreSQL 警报。 可根据[监视指标](concepts-hyperscale-monitoring.md)接收 Azure 服务的警报。

我们将设置一个将在指定指标的值超出阈值时触发的警报。 首次满足条件时，将触发警报，之后也将继续触发。

可配置警报，使警报触发时执行以下操作：
* 向服务管理员和共同管理员发送电子邮件通知。
* 将电子邮件发送到指定的其他电子邮件地址。
* 调用 Webhook。

可使用以下项配置并获取预警规则相关信息：
* [Azure 门户](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure 监视器 REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>通过 Azure 门户针对指标创建警报规则
1. 在 [Azure 门户](https://portal.azure.com/)中，选择要监视的 Azure Database for PostgreSQL 服务器。

2. 在边栏的“监视”部分，选择“警报”，如下所示   ：

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="选择警报规则":::

3. 选择“新建警报规则”（+ 图标）。

4. 随即打开“创建规则”页面，如下所示  。 填写所需信息：

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="添加指标警报窗体":::

5. 在“条件”部分中，选择“添加” 。

6. 从要发出警报的信号列表中选择一个指标。 在此示例中，选择“存储百分比”。
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="屏幕截图显示了你可在其中查看多个信号的“配置信号逻辑”页。":::

7. 配置警报逻辑：

    * 运算符（例如 “大于”）
    * 阈值（例如 85%）
    * 聚合粒度是触发警报前必须满足指标规则的“时间段”（例如 “最近 30 分钟”）
    * 计算频率（例如 “1 分钟”）
   
   完成后选择“完成”  。

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="屏幕截图显示了你可在其中配置警报逻辑的窗格。":::

8. 在“操作组”部分中，选择“新建”创建新组以接收有关警报的通知   。

9. 使用名称、短名称、订阅和资源组填写“添加操作组”表单。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="屏幕截图显示了你可在其中输入所述值的“添加操作组”窗体。":::

10. 配置“电子邮件/短信/推送/语音”操作类型。
    
    选择“电子邮件 Azure 资源管理器角色”，将通知发送到订阅所有者、参与者和读者。
   
    完成后选择“确定”  。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="屏幕截图显示了“电子邮件/短信/推送/语音”窗格。":::

11. 指定预警规则名称、说明和严重性。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="屏幕截图显示了“警报详细信息”窗格。"::: 

12. 选择“创建警报规则”可以创建警报  。

    几分钟后，警报将处于活动状态，并按前面所述进行触发。

### <a name="managing-alerts"></a>管理警报

创建警报后，可选择它并执行以下操作：

* 查看图，了解与此警报相关的指标阈值和前一天实际值。
* 编辑或删除预警规则   。
* 如果要暂时停止或恢复接收通知，可禁用或启用警报   。

## <a name="suggested-alerts"></a>建议的警报

### <a name="disk-space"></a>硬盘空间

监视和警报对于每个生产超大规模 (Citus) 服务器组都至关重要。 底层 PostgreSQL 数据库需要磁盘具有可用空间才能正常运行。 如果磁盘已满，数据库服务器节点将处于脱机状态并拒绝启动，直到磁盘有可用空间为止。 此时，需要发起 Microsoft 支持请求来处理这种情况。

建议对每个服务器组中的每个节点设置磁盘空间警报，即使对于非生产使用也是如此。 磁盘空间使用情况警报提供进行干预并使节点保持正常运行所需的高级警告。 为获得最佳结果，可尝试设置一系列在使用率达到 75%、85% 和 95% 时发出的警报。 选择的百分比取决于数据引入速度，因为数据引入速度快，磁盘也会更快地被填满。

磁盘快要达到其空间限制时，可尝试以下方法获取更多可用空间：

* 查看数据保留策略。 如果可行，请将较旧的数据移到冷存储。
* 考虑向服务器组[添加节点](howto-hyperscale-scale-grow.md#add-worker-nodes)并重新平衡分片。 重新平衡可让数据在多台计算机中分配。
* 考虑[增加](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes)工作器节点的容量。 每个工作器最多可有 2 TiB 的存储空间。 但应该先尝试添加节点，再调整节点大小，因为添加节点过程可更快完成。

### <a name="cpu-usage"></a>CPU 使用率

监视 CPU 使用率有助于建立性能基线。 例如，你可能会注意到，CPU 使用率通常约为 40-60%。 如果 CPU 使用率突然开始停留在 95%，则可以识别出异常情况。 CPU 使用率可反映自然增长，但也可能会反映出意外的查询。 创建 CPU 警报时，请设置较长的聚合粒度来捕获长期的增长情况，并忽略短暂的峰值。

## <a name="next-steps"></a>后续步骤
* 了解[在警报中配置 Webhook](../azure-monitor/alerts/alerts-webhooks.md)的详细信息。
* 获取[指标集合概述](../azure-monitor/data-platform.md)以确保服务可用且响应迅速。
