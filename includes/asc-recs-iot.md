---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/22/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 388798b9f1ada6921fb79363678ecd17a3041227
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801449"
---
这一类别有 12 条相关建议。

|建议 |说明 |严重性 |
|---|---|---|
| 默认 IP 筛选策略应为“拒绝” |“IP 筛选器配置”应定义允许流量的规则，并默认拒绝其他所有流量<br />（无相关策略） |中型 |
|应启用 IoT 中心的诊断日志 |启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。<br />（相关策略：[应启用 IoT 中心的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)） |低 |
| 完全相同的身份验证凭据 |IoT 中的相同身份验证凭据可供多个设备使用。 这可能表示某个非法设备模拟了合法设备。 它还公开了攻击者攻击模拟装置的风险<br />（无相关策略） |高 |
| IoT 设备 - 代理正在发送未充分利用的消息 |当前未充分利用 IoT 代理消息大小容量，婴儿导致发送的消息数增加。 调整消息间隔以提高利用率<br />（无相关策略） |低 |
| IoT 设备 - 经审核的进程已停止发送事件 |不再从此设备接收源自 AuditD 进程的安全事件<br />（无相关策略） |高 |
| IoT 设备 - 打开设备上的端口 |在设备上发现了侦听终结点<br />（无相关策略） |中型 |
| IoT 设备 - 操作系统基线验证失败 |识别到安全相关系统配置问题<br />（无相关策略） |中型 |
| IoT 设备 - 在其中一个链中找到了宽容防火墙策略 |在主防火墙链（输入/输出）中找到允许的防火墙策略。 默认情况下，防火墙策略应拒绝所有流量，并定义规则以在设备之间进行必要通信<br />（无相关策略） |中型 |
| IoT 设备 - 在输入链中找到了宽容防火墙规则 |在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松模式的规则<br />（无相关策略） |中型 |
| IoT 设备 - 在输出链中找到了宽容防火墙规则 |在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松式规则<br />（无相关策略） |中型 |
| IoT 设备 - 需要进行 TLS 加密套件升级 |检测到不安全的 TLS 配置。 建议立即升级 TLS 密码套件<br />（无相关策略） |中型 |
| IP 筛选器规则的 IP 范围大 |允许 IP 筛选器规则的源 IP 范围太大。 过度宽松的规则可能会将 IoT 中心暴露给恶意行动者<br />（无相关策略） |中型 |
|||
