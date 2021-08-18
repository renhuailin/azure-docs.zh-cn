---
title: Apache Kafka for Confluent Cloud 故障排除 - Azure 合作伙伴解决方案
description: 本文提供有关 Azure 上 Confluent Cloud 的故障排除和常见问题解答 (FAQ) 信息。
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 784fed6efb83e02940c6b73fb4c3aab792c4cf99
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112518225"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Apache Kafka for Confluent Cloud 解决方案故障排除

本文档包含有关对使用 Apache Kafka for Confluent Cloud 的解决方案进行故障排除的信息。

如果在本文中找不到解答或无法解决问题，请[通过 Azure 门户创建请求](get-support.md)或联系 [Confluent 支持人员](https://support.confluent.io)。

## <a name="cant-find-offer-in-the-marketplace"></a>在市场中找不到产品/服务

若要在 Azure 市场中查找产品/服务，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”。
1. 搜索 Apache Kafka on Confluent Cloud。
1. 选择应用程序磁贴。

如果该产品/服务未显示，请联系 [Confluent 支持人员](https://support.confluent.io)。 你的 Azure Active Directory 租户 ID 必须已列在允许的租户列表中。 若要了解如何查找租户 ID，请参阅[如何查找 Azure Active Directory 租户 ID](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)。

## <a name="purchase-errors"></a>购买错误

* 购买失败，因为该 Azure 订阅未连接有效信用卡，或该订阅未关联付款方式。

  使用其他 Azure 订阅。 或者，为订阅添加或更新信用卡或付款方式。 有关详细信息，请参阅[更新信用卡和付款方式](../../cost-management-billing/manage/change-credit-card.md)。

* EA 订阅不允许在市场中购买。

  使用其他订阅。 或者，检查你的 EA 订阅是否启用了市场购买。 有关详细信息，请参阅[启用市场购买](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)。 如果这些方法不能解决问题，请联系 [Confluent 支持人员](https://support.confluent.io)。

## <a name="conflict-error"></a>冲突错误

如果你以前已注册了 Confluent Cloud，则必须使用新电子邮件地址创建另一个 Confluent Cloud 组织资源。 使用以前注册的电子邮件地址会出现“冲突”错误。 重新注册，但这一次请使用新电子邮件地址。

## <a name="deploymentfailed-error"></a>DeploymentFailed 错误

如果遇到 DeploymentFailed 错误，请检查 Azure 订阅的状态。 请确保该订阅未暂停，并且不存在任何计费问题。

## <a name="resource-isnt-displayed"></a>不显示资源

如果门户中不显示 Confluent Cloud 的“概述”或“删除”边栏选项卡，请尝试刷新页面 。 此错误可能是门户的间歇性问题造成的。 如果刷新不起作用，请联系 [Confluent 支持人员](https://support.confluent.io)。

如果在 Azure“所有资源”列表中找不到 Confluent Cloud 资源，请联系 [Confluent 支持人员](https://support.confluent.io)。

## <a name="resource-creation-takes-long-time"></a>创建资源花费了很长时间

如果部署过程需要三个小时以上才能完成，请联系支持人员。

如果部署失败并且 Confluent Cloud 资源的状态为 `Failed`，请删除该资源。 删除后，重试创建该资源。

## <a name="offer-plan-doesnt-load"></a>产品/服务计划不加载

此错误可能是 Azure 门户的间歇性问题造成的。 重试部署产品/服务。

## <a name="unable-to-delete"></a>无法删除

如果无法删除 Confluent 资源，请验证你是否有权删除该资源。 必须有权执行 Microsoft.Confluent/*/Delete 操作。 有关查看权限的信息，请参阅[使用 Azure 门户列出 Azure 角色分配](../../role-based-access-control/role-assignments-list-portal.md)。

如果你拥有正确的权限，但仍无法删除该资源，请联系 [Confluent 支持人员](https://support.confluent.io)。 这种状况可能与 Confluent 的保留策略有关。 Confluent 支持人员可为你删除组织和电子邮件地址。

## <a name="unable-to-use-single-sign-on"></a>无法使用单一登录

如果 SSO 在 Confluent Cloud SaaS 门户中不起作用，请验证是否使用了正确的 Azure Active Directory 电子邮件。 此外，必须同意在 Confluent Cloud 软件即服务 (SaaS) 门户中进行访问。 有关详细信息，请参阅[单一登录指南](manage.md#single-sign-on)。

如果该问题依然出现，请联系 [Confluent 支持人员](https://support.confluent.io)。

## <a name="next-steps"></a>后续步骤

了解如何[管理 Apache Kafka for Confluent Cloud 的实例](manage.md)。
