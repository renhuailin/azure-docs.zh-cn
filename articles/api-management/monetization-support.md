---
title: Azure API 管理对盈利的支持
description: 了解 Azure API 管理如何支持 API 产品的盈利策略。
author: v-hhunter
ms.author: v-hhunter
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 88323145b9b6bb66babe77b19d6b834afc2831ab
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868455"
---
# <a name="how-api-management-supports-monetization"></a>API Management 如何支持变现

借助 [Azure API 管理](./api-management-key-concepts.md)服务平台，你可以：
* 发布 API，供使用者订阅。
* 降低实现风险。 
* 加速项目的时间刻度。
* 自信地缩放 API。

在本文档中，我们将重点介绍 API 管理功能，这些功能可以实现盈利策略，如提供顺畅的体验完成以下操作：
* 发现公共 API。
* 输入付款详细信息。
* 激活订阅。
* 使用 API。
* 监视使用情况。
* 自动为 API 使用量付费。

下图介绍了这些关键 API 管理功能：

:::image type="content" source="media/monetization-support/architecture-overview.png" alt-text="密钥 API 管理盈利功能示意图":::

## <a name="api-discovery"></a>API 发现

使用 API 管理的内置开发人员门户启动 API 和载入 API 使用者。 强调开发人员门户的优质开发内容，使 API 使用者能够无缝地浏览和使用 API。 测试为可访问性、完整性和可用性提供的内容和信息。

有关如何添加内容和控制开发人员门户品牌的详细信息，请参阅[开发人员门户概述](./api-management-howto-developer-portal.md)。

## <a name="api-packaging"></a>API 打包

API 管理用于管理如何打包 API 并使用“产品”和“策略”的概念展示 API。

### <a name="products"></a>产品

[通过产品](./api-management-howto-add-products.md)发布 API。 产品允许定义：
* 订阅服务器可以访问的 API。
* 特定限制[策略](./api-management-howto-policies.md)，如将特定订阅限制为每月调用配额。

当 API 使用者订阅某个产品时，他们将收到一个 API 密钥，可使用这些密钥发出调用。 最初，订阅设置为 `submitted` 状态。 激活订阅以允许订阅服务器使用 API。

将 API 管理产品配置为打包基础 API，以镜像收入模型，其中包括：
* 收入模型中各层之间的一对一关系。
* 对应的 API 管理产品。

示例项目使用 API 管理产品作为整理盈利策略的顶级方法。 API 管理产品会镜像收入模型层，并为每个层的特定定价模型编制索引。 此设置提供了一种配置驱动的灵活方法来准备盈利策略。

### <a name="policies"></a>策略

应用 API 管理策略来控制每种产品的服务质量。 示例项目使用两个特定的策略功能来控制服务质量，并与收入模型相符合：

| 策略功能 | 说明 |
| ----- | ----- |
| **配额** | 定义在指定的时间段内用户可对 API 进行的调用总数。 例如，“每月 100 次调用”。 用户达到配额后，对 API 的调用将失败，并且调用方会收到 `403 Forbidden` 响应状态代码。 |
| **速率限制** | 定义在滑动时间范围内可对 API 进行的调用数。 例如，“每分钟 200 次调用”。 旨在防止在使用所选产品的付费服务质量时出现 API 使用高峰。 超过调用速率时，调用方会收到 `429 Too Many Requests` 响应状态代码。 |

有关策略的详细信息，请参阅 [Azure API 管理中的策略](./api-management-howto-policies.md)文档。

## <a name="api-consumption"></a>API 使用情况

通过使用 API 订阅的产品向 API 授予 API 使用者访问权限。

1. 当注册特定 API 管理产品时，API 使用者会建立 API 订阅。 
1. 使用 API 管理委派将订阅过程与付款提供程序集成。 
1. 成功提供付款详细信息后，用户便可以使用针对订阅生成的唯一安全密钥访问 API。

有关订阅的详细信息，请参阅 [Azure API 管理中的订阅](./api-management-subscriptions.md)文档。

## <a name="api-usage-monitoring"></a>API 使用情况监视

使用 API 管理的内置分析获取有关 API 使用情况和性能的见解。 这些分析通过以下指标提供报告：
* API
* 地理位置
* API 操作
* 产品
* 请求
* 订阅
* 时间 
* 用户

定期查看分析报告，以了解 API 使用者如何采用你的盈利策略。

有关详细信息，请参阅[在 Azure API 管理中获取 API 分析](./howto-use-analytics.md)。

## <a name="security"></a>安全性

使用 API 管理的产品、API 策略和订阅控制每个用户对每个产品的访问级别。 如果用户已成功向付款提供程序进行身份验证，则即使特定的 API 产品免费，你也可以通过授予订阅级 API 访问权限来防止不当使用和滥用。

## <a name="integration"></a>集成

通过 API 管理与所选付款提供程序之间的前端和后端集成，打造无缝盈利体验。  使用 API 管理委派进行前端集成，使用 REST API 进行后端集成。

### <a name="delegation"></a>委托

在示例项目中，你可以使用 [API 管理委派](./api-management-howto-setup-delegation.md)与第三方付款提供程序进行自定义集成。 此演示使用委派实现注册/登录和产品订阅体验。

#### <a name="sign-upsign-in-workflow"></a>注册/登录工作流

1. 开发人员单击 API 管理开发人员门户中的登录或注册链接。
1. 浏览器重定向到委派终结点（配置为自定义计费门户应用中的某个页面）。
1. 自定义计费门户应用显示登录/注册 UI。
1. 成功登录/注册后，用户会进行身份验证，并重定向回“启动 API 管理”开发人员门户页面。

#### <a name="product-subscription-workflow"></a>产品订阅工作流

1. 开发人员在 API 管理开发人员门户中选择一个产品，并单击“订阅”按钮
1. 浏览器重定向到委派终结点（配置为自定义计费门户应用中的某个页面）。
1. 自定义计费门户应用：
    * 显示基于付款提供程序配置的 UI（Stripe 或 Adyen）。
    * 使用户完成相关的结帐过程。
1. 用户被重定向回“启动 API 管理”产品页面。 
    * 该产品将处于活动状态，API 密钥将可用。

### <a name="rest-api"></a>REST API

使用用于 API 管理的 REST API 自动执行盈利策略的操作。

示例项目使用 API 以编程方式执行以下操作：

- 检索 API 管理产品和策略，以在付款提供程序（如 Stripe）中支持类似概念的同步配置。
- 定期轮询 API 管理以检索每个订阅的 API 使用情况指标，并推动计费过程。

有关详细信息，请参阅 [REST API Azure API 管理](https://docs.microsoft.com/rest/api/apimanagement/#rest-operation-groups)概述。

## <a name="devops"></a>DevOps

版本控制和使用 Azure 资源管理器自动部署对 API 管理的更改，包括配置实现盈利策略的功能，例如：
* 产品
* 策略
* 开发人员门户

在示例项目中，Azure 资源管理器脚本通过 JSON 文件进行扩充，该文件定义每个 API 管理产品的定价模型。 通过这种扩充方式，你可以在 API 管理和所选付款提供程序之间同步配置。 整个解决方案在单个源代码管理存储库下进行管理，以执行以下操作：
* 将与正在进行的盈利策略演变相关的所有更改协调为单个版本。
* 按照监管和审核要求执行更改。

## <a name="initialization-and-deployment"></a>初始化和部署

可以通过以下方式部署 API 管理：
* Azure 门户 UI，或
* 使用 [Azure 资源管理器模板](https://azure.microsoft.com/services/arm-templates)的“基础结构即代码”方法。 

## <a name="next-steps"></a>后续步骤

* [了解有关 API 管理盈利策略的详细信息](monetization-overview.md)。
* 通过关联的 [Git 存储库](https://github.com/microsoft/azure-api-management-monetization)部署演示 Adyen 或 Stripe 集成。
