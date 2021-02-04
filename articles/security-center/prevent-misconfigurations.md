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
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558191"
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

- 应限制对具有防火墙和虚拟网络配置的存储帐户的访问
- Azure Cache for Redis 应驻留在虚拟网络中
- Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据
- Azure 机器学习工作区应使用客户托管密钥 (CMK) 进行加密
- Azure Spring Cloud 应使用网络注入
- 认知服务帐户应启用使用客户管理的密钥 (CMK) 进行数据加密
- 应强制执行容器 CPU 和内存限制
- 应只从受信任的注册表中部署容器映像
- 容器注册表应使用客户托管密钥 (CMK) 进行加密
- 应避免使用特权提升的容器
- 应避免使用共享敏感主机命名空间的容器
- 容器应只侦听允许的端口
- 应强制对容器使用不可变（只读）根文件系统
- Key Vault 密钥应具有到期日期
- Key Vault 机密应具有到期日期
- 密钥保管库应启用清除保护
- 密钥保管库应启用软删除
- 应强制对容器使用最低权限 Linux 功能
- 应该启用只能通过安全方式连接到 Redis 缓存
- 应限制替代或禁用容器 AppArmor 配置文件
- 应避免特权容器
- 应避免以根用户身份运行容器
- 应该启用安全传输到存储帐户
- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign
- Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证
- 服务应只侦听允许的端口
- 应将存储帐户迁移到新 Azure 资源管理器资源
- 存储帐户应使用虚拟网络规则来限制网络访问
- 应限制对主机网络和端口的使用
- 应限制为只有已知列表才能使用 Pod HostPath 卷装载，以限制来自遭入侵容器的节点访问
- 存储在 Azure Key Vault 中的证书的有效期不得超过 12 个月
- 应将虚拟机迁移到新的 Azure 资源管理器资源
- 应为应用程序网关启用 Web 应用程序防火墙 (WAF)
- 应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)

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