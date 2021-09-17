---
title: 如何借助 Azure 安全中心防止配置错误
description: 了解如何使用安全中心“建议详细信息”页面上的“强制”和“拒绝”选项
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/17/2021
ms.author: memildin
ms.openlocfilehash: 4316a53675a8a7e51359b041143e8625e1db4f1f
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606137"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>使用“强制执行/拒绝”建议防止错误配置

安全性配置错误是造成安全事件的主要原因。 安全中心可以帮助阻止新资源在特定建议方面的错误配置。 

此功能可帮助保护工作负载的安全和稳定安全分数。

根据特定建议，有两种方式可以强制实施安全配置：

- 利用 Azure Policy 的“拒绝”效果，可以阻止创建不正常的资源
- 通过“强制执行”选项，可以利用 Azure Policy 的“DeployIfNotExist”效果，在创建时自动修正不符合资源 

该选项位于所选安全建议的资源详细信息页面的顶部（请参阅[带有“拒绝”/“强制执行”选项的建议](#recommendations-with-denyenforce-options)）。

## <a name="prevent-resource-creation"></a>阻止资源创建

1. 打开新资源需要满足的建议，然后选择页面顶部的“拒绝”按钮。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="突出显示“拒绝”按钮的建议页面。":::

    此时将打开“配置”窗格，其中列出了作用域选项。 

1. 通过选择相关的订阅或管理组来设置作用域。

    > [!TIP]
    > 可以使用行末尾的省略号来更改单个订阅，也可以使用复选框选择多个订阅或组，然后选择“更改为拒绝”。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="设置 Azure Policy 拒绝的作用域。":::


## <a name="enforce-a-secure-configuration"></a>强制实施安全配置

1. 打开建议，如果新资源不满足此建议，需要为其部署模板部署，然后选择页面顶部的“强制执行”按钮。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="突出显示了“强制执行”按钮的建议页面。":::

    此时将打开“配置”窗格，其中包含所有策略配置选项。 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="“强制执行”配置选项。":::

1. 设置作用域、分配名称和其他相关选项。

1. 选择“查看 + 创建”。

## <a name="recommendations-with-denyenforce-options"></a>使用拒绝/强制执行选项的建议

这些建议可与“拒绝”选项一起使用：

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

这些建议可与“强制执行”选项一起使用：

- 应启用 SQL 服务器上的审核
- 已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展
- 应为虚拟机启用 Azure 备份
- 应启用适用于应用服务的 Azure Defender
- 应启用适用于容器注册表的 Azure Defender
- 应启用 Azure Defender for DNS
- 应启用 Azure Defender for Key Vault
- 应启用 Azure Defender for Kubernetes
- 应启用 Azure Defender for Resource Manager
- 应启用适用于服务器的 Azure Defender
- 应启用适用于 Azure SQL 数据库服务器的 Azure Defender
- 应启用适用于计算机上的 SQL 服务器的 Azure Defender
- 应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL
- 应启用适用于存储的 Azure Defender
- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项
- 应该在 Azure 流分析中启用诊断日志
- 应启用 Batch 帐户中的诊断日志
- 应启用 Data Lake Analytics 中的诊断日志
- 应启用事件中心内的诊断日志
- 应启用 Key Vault 中的诊断日志
- 应启用逻辑应用的诊断日志
- 应启用搜索服务的诊断日志
- 应启用服务总线中的诊断日志