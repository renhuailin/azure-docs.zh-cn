---
title: Confluent 上的 Apache Kafka 云概述-Azure 合作伙伴解决方案
description: 了解如何在 Azure Marketplace 中的 Confluent Cloud 上使用 Apache Kafka。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253354"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Confluent 云的 Apache Kafka 是什么？

Confluent Cloud Apache Kafka 是一种提供作为服务 Apache Kafka 的 Azure Marketplace 产品。 它是完全托管的，因此你可以专注于构建应用程序，而不是管理群集。

为了降低跨平台管理的负担，Microsoft 与 Confluent Cloud 进行了合作，以构建从 Azure 到 Confluent 云的集成设置层。 它提供在 Azure 上使用 Confluent 云的合并体验。 可以轻松地将 Confluent Cloud 与 Azure 应用程序集成。

以前，你必须购买 Marketplace 中的 Confluent 云产品并在 Confluent 云中单独设置帐户。 若要管理配置和资源，必须在 Azure 门户和 Confluent Cloud 之间导航。

现在，通过名为 **Confluent** 的资源提供程序预配 Confluent 云资源。 可以通过 [Azure 门户](https://portal.azure.com/)、 [Azure CLI](/cli/azure/)或 [Azure Sdk](/azure/#languages-and-tools)来创建和管理 Confluent 云组织资源。 Confluent Cloud 拥有和运行 (SaaS) 应用程序的软件即服务，包括环境、群集、主题、API 密钥和托管连接器。

## <a name="capabilities"></a>功能

Confluent 云和 Azure 之间的深度集成可实现以下功能：

- 使用完全托管的基础结构，从 Azure 门户预配新的 Confluent 云组织资源。
- 通过 Azure Active Directory (Azure AD) ，简化从 Azure 到 Confluent Cloud (SSO) 的单一登录。 不需要从 Confluent 云门户进行单独的身份验证。
- 通过 Azure 订阅开票获取 Confluent 云消耗的统一计费。
- 从 Azure 门户管理 Confluent 云资源，并将其与其他 Azure 资源一起在 " **所有资源** " 页中跟踪。

## <a name="confluent-organization"></a>Confluent 组织

Confluent 组织是提供 Azure 与 Confluent 云资源之间的映射的资源。 这是其他 Confluent 云资源的父资源。

每个 Azure 订阅可以包含多个 Confluent 计划。 每个 Confluent 计划都映射到 Confluent 门户中的用户帐户和组织。 在每个 Confluent 组织内，可以创建多个环境、群集、主题和连接器。

当你在 Azure 中预配 Confluent 云资源时，将获取 Confluent 组织 ID、默认环境和用户帐户。 有关详细信息，请参阅 [快速入门： Azure 上的 Confluent Cloud 入门](create.md)。

对于计费，在 Marketplace 中购买的每个 Confluent 云产品/服务将映射到唯一的 Confluent 组织。

## <a name="single-sign-on"></a>单一登录

登录到 Azure 门户时，还可以使用凭据登录 Confluent Cloud SaaS 门户。 经验使用 [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) 和 [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) ，为你提供一种安全且方便的方式登录。

有关详细信息，请参阅 [单一登录](manage.md#single-sign-on)。

## <a name="billing"></a>计费

提供了两种计费选项：即用即付每月计划和承诺计划。

- 使用即用即 **付每月计划**，可在 Azure 月度帐单上收到 Confluent 的云消耗费用。
- 使用 **承诺计划**，你可以注册最小支出，并获得 Confluent 云的已提交使用情况的折扣。

您决定在创建服务时要使用的计费选项。

## <a name="confluent-links"></a>Confluent 链接

有关使用适用于 Confluent 云的 Apache Kafka 的其他帮助，请参阅 [Confluent 站点](https://docs.confluent.io/home/overview.html)的以下链接。

若要了解有关计费选项的信息，请参阅：

* [Azure Marketplace，即用即付](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [具有承诺的 Azure Marketplace](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

若要了解有关管理解决方案的信息，请参阅：

* [在 Confluent 云中创建群集](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Confluent 云环境](https://docs.confluent.io/current/cloud/using/environments.html)
* [Confluent 云基础知识](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

有关支持和条款，请参阅：

* [Confluent 支持](https://support.confluent.io)
* [服务条款](https://www.confluent.io/confluent-cloud-tos)。

## <a name="next-steps"></a>后续步骤

若要为 Confluent Cloud 创建 Apache Kafka 实例，请参阅 [快速入门： Azure 上的 Confluent Cloud](create.md)入门。
