---
title: 配置 Azure 密钥保管库警报
description: 了解如何创建警报来监视密钥保管库的运行状况。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: d74cb93cd28f3dc02b37a6c29832d9e8412ef098
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458901"
---
# <a name="configure-azure-key-vault-alerts"></a>配置 Azure 密钥保管库警报

开始使用 Azure 密钥保管库来存储生产机密之后，必须监视密钥保管库的运行状况以确保服务正常运行。 

当你开始缩放服务时，发送到密钥保管库的请求数量将会增加。 这可能会增加请求的延迟时间。 在极端情况下，它可能会导致请求受到限制，从而影响服务的性能。 你还需要知道密钥保管库是否发送了异常数量的错误代码，以便快速处理访问策略或防火墙配置存在的任何问题。 

本文将介绍如何为指定的阈值配置警报，让你在密钥保管库运行不正常时提醒团队立即采取措施。 可以配置发送电子邮件的警报（最好是发送到团队通讯组），触发 Azure 事件网格通知，或者拨打电话号码/发短信。 

可以选择以下警报类型：

- 基于固定值的静态警报
- 动态警报，在受监视的指标在定义的时间范围内超出密钥保管库的平均限制一定次数的情况下发出通知

> [!IMPORTANT]
> 新配置的警报最长需要 10 分钟才会开始发送通知。 

本文重点介绍密钥保管库的警报。 有关将日志与指标结合以提供全局监视解决方案的密钥保管库见解的信息，请参阅[利用密钥保管库见解监视密钥保管库](../../azure-monitor/insights/key-vault-insights-overview.md#introduction-to-key-vault-insights)。

## <a name="configure-an-action-group"></a>配置操作组 

操作组是可配置的通知和属性列表。 配置警报的第一步是创建操作组并选择警报类型：

1. 登录到 Azure 门户。
2. 在搜索框中搜索“警报”。
3. 选择“管理操作”。

   > [!div class="mx-imgBorder"]
   > ![突出显示“管理操作”按钮的屏幕截图。](../media/alert-6.png)

4. 选择“+ 添加操作组”。

   > [!div class="mx-imgBorder"]
   > ![突出显示用于添加操作组的按钮的屏幕截图](../media/alert-7.png)。

5. 为操作组选择“操作类型”值。 本示例将创建电子邮件和短信警报。 选择“电子邮件/短信/推送/语音”。

   > [!div class="mx-imgBorder"]
   > ![突出显示用于添加操作组的选项的屏幕截图](../media/alert-8.png)。

6. 在对话框中，输入电子邮件和短信详细信息，然后选择“确定”。

   > [!div class="mx-imgBorder"]
   > ![显示用于添加电子邮件和短信警报的选项的屏幕截图。](../media/alert-9.png)

## <a name="configure-alert-thresholds"></a>配置警报阈值 

接下来，创建规则并配置触发警报的阈值：

1. 在 Azure 门户中选择密钥保管库资源，然后选择“监视”下的“警报” 。

   > [!div class="mx-imgBorder"]
   > ![显示“监视”部分的“警报”菜单选项的屏幕截图。](../media/alert-10.png)

2. 选择“新建警报规则”。

   > [!div class="mx-imgBorder"]
   > ![显示用于添加新警报规则的按钮的屏幕截图。](../media/alert-11.png)

3. 选择警报规则的作用域。 可以选择一个或多个保管库。 

   > [!IMPORTANT]
   > 如果选择多个保管库作为警报范围，则选定的所有保管库必须位于同一区域中。 不同区域中的保管库需要配置单独的警报规则。 

   > [!div class="mx-imgBorder"]
   > ![此屏幕截图显示了如何选择保管库。](../media/alert-12.png)

4. 选择定义警报逻辑的阈值，然后选择“添加”。 密钥保管库团队建议配置以下警报阈值： 

    + 密钥保管库可用性低于 100%（静态阈值）
    + 密钥保管库延迟大于 500 ms（静态阈值） 
    + 保管库整体饱和度大于 75%（静态阈值） 
    + 保管库整体饱和度超过平均数（动态阈值）
    + 错误代码总计高于平均值（动态阈值） 

   > [!div class="mx-imgBorder"]
   > ![此屏幕截图显示了选择警报条件的位置。](../media/alert-13.png)

### <a name="example-configure-a-static-alert-threshold-for-latency"></a>示例：为延迟配置静态警报阈值

1. 选择“整体服务 API 延迟”作为信号名称。

   > [!div class="mx-imgBorder"]
   > ![显示选择信号名称的屏幕截图。](../media/alert-14.png)

1. 使用以下配置参数：

   + 将“阈值”设置为“静态” 。 
   + 将“运算符”设置为“大于” 。
   + 将“聚合类型”设置为“平均” 。
   + 将“阈值”设置为 500 。
   + 将“聚合粒度(时间范围)”设置为“5 分钟” 。
   + 将“评估频率”设置为“每 1 分钟” 。

   > [!div class="mx-imgBorder"]
   > ![显示静态警报阈值的配置逻辑的屏幕截图。](../media/alert-15.png)

1. 选择“完成”。  

### <a name="example-configure-a-dynamic-alert-threshold-for-vault-saturation"></a>示例：为保管库饱和度配置动态警报阈值 

使用动态警报时，可以看到所选密钥保管库的历史数据。 蓝色区域表示密钥保管库的平均使用情况。 红色区域显示可能触发警报的高峰，前提是满足警报配置中的其他条件。 红点显示在聚合时间范围内满足警报条件的违规实例。 

> [!div class="mx-imgBorder"]
> ![显示保管库总体饱和度图的屏幕截图。](../media/alert-16.png)

可以设置在一定时间内发生一定次数的冲突后触发警报。 如果不想包含过去的数据，可以使用高级设置中的一个选项来排除它们。 

1. 使用以下配置参数：

   + 将“阈值”设置为“动态” 。 
   + 将“运算符”设置为“大于” 。
   + 将“聚合类型”设置为“平均” 。
   + 将“阈值敏感度”设置为“中等” 。
   + 将“聚合粒度(时间范围)”设置为“5 分钟” 。
   + 将“评估频率”设置为“每 5 分钟” 。
   + 配置高级设置（可选）。 

   > [!div class="mx-imgBorder"]
   > ![显示动态警报阈值的配置逻辑的屏幕截图。](../media/alert-17.png)

1. 选择“完成”。

1. 选择“添加”来添加已配置的操作组。

   > [!div class="mx-imgBorder"]
   > ![显示用于添加操作组的按钮的屏幕截图。](../media/alert-18.png)

1. 在警报详细信息中，启用警报并分配严重性。

   > [!div class="mx-imgBorder"]
   > ![屏幕截图，显示在何处启用警报并分配严重性。](../media/alert-19.png)

1. 创建警报。 

### <a name="example-email-alert"></a>示例电子邮件警报 

完成上述所有步骤后，当密钥保管库满足配置的警报条件时，你就会收到电子邮件警报。 电子邮件警报示例如下。 

> [!div class="mx-imgBorder"]
> ![此屏幕截图突出显示了配置电子邮件警报所需的信息。](../media/alert-20.png)

## <a name="next-steps"></a>后续步骤

使用你在本文中设置的工具积极监视密钥保管库的运行状况：

- [监视 Key Vault](monitor-key-vault.md)
- [监视 Key Vault 数据引用](monitor-key-vault-reference.md)
