---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 07/25/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: d20d6ad62c7513ff810c1b151085019136021dc9
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675128"
---
这一类别有 12 条相关建议。

|建议 |说明 |严重性 |
|---|---|---|
|[ 默认 IP 筛选策略应为“拒绝”](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5a3d6cdd-8eb3-46d2-ba11-d24a0d47fe65) |“IP 筛选器配置”应定义允许流量的规则，并默认拒绝其他所有流量<br />（无相关策略） |中型 |
|[应启用 IoT 中心的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/77785808-ce86-4e40-b45f-19110a547397) |启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。<br />（相关策略：[应启用 IoT 中心的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)） |低 |
|[ 完全相同的身份验证凭据](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/9d07b7e6-2986-4964-a76c-b2689604e212) |IoT 中的相同身份验证凭据可供多个设备使用。 这可能表示某个非法设备模拟了合法设备。 它还公开了攻击者攻击模拟装置的风险<br />（无相关策略） |高 |
|[ IoT 设备 - 代理正在发送未充分利用的消息](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a9a59ebb-5d6f-42f5-92a1-036fd0fd1879) |当前未充分利用 IoT 代理消息大小容量，婴儿导致发送的消息数增加。 调整消息间隔以提高利用率<br />（无相关策略） |低 |
|[ IoT 设备 - 经审核的进程已停止发送事件](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d74d2738-2485-4103-9919-69c7e63776ec) |不再从此设备接收源自 AuditD 进程的安全事件<br />（无相关策略） |高 |
|[ IoT 设备 - 打开设备上的端口](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1a36f14a-8bd8-45f5-abe5-eef88d76ab5b) |在设备上发现了侦听终结点<br />（无相关策略） |中型 |
|[ IoT 设备 - 操作系统基线验证失败](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5f65e47f-7a00-4bf3-acae-90ee441ee876) |识别到安全相关系统配置问题<br />（无相关策略） |中型 |
|[ IoT 设备 - 在其中一个链中找到了宽容防火墙策略](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/beb62be3-5e78-49bd-ac5f-099250ef3c7c) |在主防火墙链（输入/输出）中找到允许的防火墙策略。 默认情况下，防火墙策略应拒绝所有流量，并定义规则以在设备之间进行必要通信<br />（无相关策略） |中型 |
|[ IoT 设备 - 在输入链中找到了宽容防火墙规则](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ba975338-f956-41e7-a9f2-7614832d382d) |在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松模式的规则<br />（无相关策略） |中型 |
|[ IoT 设备 - 在输出链中找到了宽容防火墙规则](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d5a8d84a-9ad0-42e2-80e0-d38e3d46028a) |在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松式规则<br />（无相关策略） |中型 |
|[ IoT 设备 - 需要进行 TLS 加密套件升级](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/2acc27c6-5fdb-405e-9080-cb66b850c8f5) |检测到不安全的 TLS 配置。 建议立即升级 TLS 密码套件<br />（无相关策略） |中型 |
|[ IP 筛选器规则的 IP 范围大](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d8326952-60bb-40fb-b33f-51e662708a88) |允许 IP 筛选器规则的源 IP 范围太大。 过度宽松的规则可能会将 IoT 中心暴露给恶意行动者<br />（无相关策略） |中型 |
|||
