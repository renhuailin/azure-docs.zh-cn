---
title: Apache Kafka on Confluent Cloud 概述 - Azure 合作伙伴解决方案
description: 了解如何在 Azure 市场中使用 Apache Kafka on Confluent Cloud。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 06/07/2021
ms.author: tomfitz
ms.openlocfilehash: 07164ece2897ab364d568d247e3d2ee8d2566291
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112523035"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Apache Kafka for Confluent Cloud 是什么？

Apache Kafka for Confluent Cloud 是一种 Azure 市场产品/服务，它提供 Apache Kafka 即服务。 它受完全托管，使你可以专注于生成应用程序，而不是管理群集。

为了减轻跨平台管理的负担，Microsoft 与 Confluent Cloud 合作，构建了一个从 Azure 到 Confluent Cloud 的集成预配层。 它为在 Azure 上使用 Confluent Cloud 提供了一个统一的体验。 你可轻松地将 Confluent Cloud 与 Azure 应用程序集成，并使用该应用对其进行管理。

以前，必须在市场中购买 Confluent Cloud 产品/服务，并单独在 Confluent Cloud 中设置帐户。 若要管理配置和资源，必须在 Azure 和 Confluent Cloud 的门户之间进行导航。

现在，你通过名为 Microsoft.Confluent 的资源提供程序预配 Confluent Cloud 资源。 通过 [Azure 门户](https://portal.azure.com/)、[Azure CLI](/cli/azure/) 或 [Azure SDK](/azure#languages-and-tools) 创建和管理 Confluent Cloud 组织资源。 Confluent Cloud 拥有并运行软件即服务 (SaaS) 应用程序，包括环境、群集、主题、API 密钥和托管连接器。

## <a name="capabilities"></a>功能

Confluent Cloud 与 Azure 之间的深度集成支持以下功能：

- 使用完全托管的基础结构从 Azure 门户预配新的 Confluent Cloud 组织资源。
- 使用 Azure Active Directory (Azure AD) 简化从 Azure 到 Confluent Cloud 的单一登录 (SSO)。 无需在 Confluent Cloud 门户单独进行身份验证。
- 通过 Azure 订阅发票获取 Confluent Cloud 使用量的统一计费。
- 在 Azure 门户中管理 Confluent Cloud 资源，并在包含其他 Azure 资源的“所有资源”页面中跟踪它们。

## <a name="confluent-organization"></a>Confluent 组织

Confluent 组织是提供 Azure 与 Confluent Cloud 资源之间的映射的资源。 它是其他 Confluent Cloud 资源的父资源。

每个 Azure 订阅可包含多个 Confluent 计划。 每个 Confluent 计划映射到 Confluent 门户中的一个用户帐户和组织。 在每个 Confluent 组织内，可创建多个环境、群集、主题和连接器。

在 Azure 中预配 Confluent Cloud 资源时，你将获取 Confluent 组织 ID、默认环境和用户帐户。 有关详细信息，请参阅[快速入门：开始在 Azure 上使用 Confluent Cloud](create.md)。

对于计费，在市场中购买的每个 Confluent Cloud 产品/服务都映射到唯一的 Confluent 组织。

## <a name="single-sign-on"></a>单一登录

在你登录到 Azure 门户时，你的凭据也将用于登录到 Confluent Cloud SaaS 门户。 该体验使用 [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) 和 [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) 为你提供安全便捷的登录方式。

有关详细信息，请参阅[单一登录](manage.md#single-sign-on)。

## <a name="billing"></a>计费

提供了两种计费选项：即用即付月计划和承诺计划。

- 使用即用即付月计划，可在 Azure 月度帐单上收到 Confluent Cloud 使用量的费用。
- 使用承诺计划，可注册一个最低支出金额，并针对承诺的 Confluent Cloud 使用量获得折扣。

在创建服务时决定要使用的计费选项。

## <a name="confluent-links"></a>Confluent 链接

有关使用 Apache Kafka for Confluent Cloud 的其他帮助，请参阅以下 [Confluent 站点](https://docs.confluent.io/home/overview.html)的链接。

若要了解计费选项，请参阅：

* [使用即用即付的 Azure 市场](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [使用承诺的 Azure 市场](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

若要了解如何管理解决方案，请参阅：

* [在 Confluent Cloud 中创建群集](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Confluent Cloud 环境](https://docs.confluent.io/current/cloud/using/environments.html)
* [Confluent Cloud 基本信息](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

有关支持和条款，请参阅：

* [Confluent 支持](https://support.confluent.io)
* [服务条款](https://www.confluent.io/confluent-cloud-tos)。

## <a name="next-steps"></a>后续步骤

若要创建一个 Apache Kafka for Confluent Cloud 实例，请参阅[快速入门：开始在 Azure 上使用 Confluent Cloud](create.md)。
