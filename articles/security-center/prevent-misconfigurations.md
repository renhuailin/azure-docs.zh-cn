---
title: 如何防止 Azure 安全中心配置错误
description: 了解如何使用安全中心的 "强制" 和 "拒绝" 选项来了解建议详细信息页
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 2607ff3a6c4bb7bf301818925990521572777ab9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379177"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>使用“强制执行/拒绝”建议防止错误配置

安全性配置错误是造成安全事件的主要原因。 安全中心现在能够帮助阻止新资源在特定建议方面的错误配置。 

此功能可帮助保护工作负载的安全和稳定安全分数。

根据特定建议，有两种方式可以强制实施安全配置：

- 利用 Azure Policy 的“拒绝”效果，可以阻止创建不正常的资源
- 通过“强制执行”选项，可以利用 Azure Policy 的“不存在时部署”效果，在创建时自动修正不合规的资源 

此信息可在 "资源详细信息" 页面顶部找到所选的安全建议 (参阅) 的 [拒绝/强制选项建议](#recommendations-with-denyenforce-options) 。

## <a name="prevent-resource-creation"></a>阻止资源创建

1. 打开新资源必须满足的建议，然后选择页面顶部的 " **拒绝** " 按钮。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="突出显示 &quot;拒绝&quot; 按钮的建议页面":::

    此时将打开 "配置" 窗格，其中列出了作用域选项。 

1. 通过选择相关的订阅或管理组来设置作用域。

    > [!TIP]
    > 您可以使用该行末尾的三个点来更改单个订阅，或使用复选框来选择多个订阅或组，然后选择 " **更改为拒绝**"。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="设置 Azure 策略拒绝的作用域":::


## <a name="enforce-a-secure-configuration"></a>强制实施安全配置

1. 如果新资源不满足此建议，请打开要为其部署模板部署的建议，然后选择页面顶部的 " **强制执行** " 按钮。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="突出显示了 &quot;强制&quot; 按钮的建议页":::

    此时将打开 "配置" 窗格，其中包含所有策略配置选项。 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="强制配置选项":::

1. 设置 "作用域"、"分配名称" 和其他相关选项。

1. 选择“查看 + 创建”。

## <a name="recommendations-with-denyenforce-options"></a>带有 deny/强制选项的建议

这些建议可与 **deny** 选项一起使用：

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

这些建议可与 " **强制** " 选项一起使用：

- 应启用 SQL 服务器上的审核
- 应为虚拟机启用 Azure 备份
- 应对 SQL 服务器启用 Azure Defender for SQL
- 应该在 Azure 流分析中启用诊断日志
- 应启用 Batch 帐户中的诊断日志
- 应启用 Data Lake Analytics 中的诊断日志
- 应启用事件中心内的诊断日志
- 应启用 Key Vault 中的诊断日志
- 应启用逻辑应用的诊断日志
- 应启用搜索服务的诊断日志
- 应启用服务总线中的诊断日志
