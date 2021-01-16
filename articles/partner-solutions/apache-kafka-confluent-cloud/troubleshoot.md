---
title: Confluent 云的故障排除 Apache Kafka-Azure 合作伙伴解决方案
description: 本文提供了有关 Azure 上的 Confluent Cloud () 常见问题的疑难解答和常见问题的信息。
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: cbf166086a489165e8100dafd7c212ab6c298b41
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253353"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Confluent 云解决方案的故障排除 Apache Kafka

本文档包含有关解决使用 Confluent Cloud Apache Kafka 的解决方案的信息。

如果找不到答案或无法解决问题，请 [通过 Azure 门户创建请求](manage.md#get-support) 或联系 [Confluent 支持人员](https://support.confluent.io)。

## <a name="cant-find-offer-in-the-marketplace"></a>在 Marketplace 中找不到产品/服务

若要查找 Azure Marketplace 中的产品/服务，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”。
1. _在 Confluent Cloud 上搜索 Apache Kafka_。
1. 选择 "应用程序" 磁贴。

如果产品/服务未显示，请联系 [Confluent 支持](https://support.confluent.io)。 你的 Azure Active Directory 租户 ID 必须在允许的租户列表上。 若要了解如何查找你的租户 ID，请参阅 [如何查找你的 Azure Active Directory 租户 id](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)。

## <a name="conflict-error"></a>冲突错误

如果你以前已注册了 Confluent 云，则必须使用新的电子邮件地址来创建另一个 Confluent 云组织资源。 使用以前注册的电子邮件地址时，会收到 **冲突** 错误。 重新注册，但这一次使用新的电子邮件地址。

## <a name="deploymentfailed-error"></a>DeploymentFailed 错误

如果遇到 **DeploymentFailed** 错误，请检查 Azure 订阅的状态。 请确保它未挂起，并且没有任何计费问题。

## <a name="resource-isnt-displayed"></a>未显示资源

如果门户中未显示 " **概述** " 或 " **删除** Confluent" 云，请尝试刷新页面。 此错误可能是门户出现间歇性问题。 如果这不起作用，请联系 [Confluent 支持](https://support.confluent.io)。

如果在 "Azure **所有资源** " 列表中找不到 Confluent 云资源，请联系 [Confluent 支持](https://support.confluent.io)。

## <a name="resource-creation-takes-long-time"></a>资源创建花费了很长时间

如果部署过程需要超过三个小时才能完成，请联系支持人员。

如果部署失败并且 Confluent 云资源的状态为 `Failed` ，则删除资源。 删除后，请重试创建资源。

## <a name="offer-plan-doesnt-load"></a>产品/服务计划未加载

此错误可能是 Azure 门户的间歇性问题。 再次尝试部署该产品/服务。

## <a name="unable-to-delete"></a>无法删除

如果无法删除 Confluent 资源，请验证是否有权删除该资源。 必须允许你获取 Confluent/*/Delete 操作。 有关查看权限的信息，请参阅 [使用 Azure 门户列出 Azure 角色分配](../../role-based-access-control/role-assignments-list-portal.md)。

如果您具有正确的权限，但仍无法删除资源，请联系 [Confluent 支持](https://support.confluent.io)。 这种情况可能与 Confluent 的保留策略有关。 Confluent 支持可删除组织和电子邮件地址。

## <a name="unable-to-use-single-sign-on"></a>无法使用单一登录

如果 SSO 对于 Confluent Cloud SaaS 门户不起作用，请确认你使用的是正确的 Azure Active Directory 电子邮件。 你还必须同意允许 (SaaS) 门户的服务访问 Confluent 云软件即服务。 有关详细信息，请参阅 [单一登录指南](manage.md#single-sign-on)。

如果问题仍然存在，请联系 [Confluent 支持](https://support.confluent.io)。

## <a name="next-steps"></a>后续步骤

了解如何 [管理](manage.md) Confluent Cloud Apache Kafka 的实例。
