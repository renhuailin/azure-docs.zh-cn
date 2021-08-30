---
title: 使用 Azure AD B2C 与外部进程建立可复原接口 | Microsoft Docs
description: 与外部进程建立可复原接口的方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d3c159c615d928a8d56d3913c8e1cab846c7580
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462086"
---
# <a name="resilient-interfaces-with-external-processes"></a>与外部进程建立可复原接口

本文介绍如何在用户旅程中规划和实现 RESTFul API，以及如何使应用程序更能应对 API 故障。

![图像显示了与外部进程组件的接口](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>确保正确放置 API

Identity Experience Framework (IEF) 策略允许你使用 [RESTful API 技术配置文件](../../active-directory-b2c/restful-technical-profile.md)来调用外部系统。 外部系统不受 IEF 运行时环境的控制，并且是潜在的故障点。

### <a name="how-to-manage-external-systems-using-apis"></a>如何使用 API ​​管理外部系统

- 调用接口访问某些数据时，请检查这些数据是否将驱动身份验证决策。 评估这些信息是否对应用程序的核心功能至关重要。 例如，电子商务与辅助功能（比如管理）。 如果进行身份验证时不需要这些信息，而仅在辅助方案中需要，则考虑将调用移至应用程序逻辑。

- 如果身份验证所需的数据相对静态且较小，并且没有其他业务原因要从目录外部化，则考虑将其放在目录中。

- 尽可能从预验证路径中删除 API 调用。 如果不能，则必须在 API 前面设置严格的保护措施，以防拒绝服务 (DoS) 分布式拒绝服务 (DDoS) 攻击。 攻击者可以加载登录页面，试图用 DoS 攻击淹没 API，并使应用程序瘫痪。 例如，通过在登录中使用 CAPTCHA，注册流可以提供帮助。

- 在注册期间与标识提供者进行联合之后或创建用户之前，尽可能使用[内置注册用户流的 API 连接器](../../active-directory-b2c/api-connectors-overview.md)以与 Web API 集成。 由于用户流已经过广泛测试，因此可能无需执行用户流级别的功能、性能或缩放测试。 你仍需测试应用程序的功能、性能和缩放。

- Azure AD RESTFul API [技术配置文件](../../active-directory-b2c/restful-technical-profile.md)不提供任何缓存行为。 相反，RESTFul API 配置文件实现了策略中内置的重试逻辑和超时。

- 对于需要写入数据的 API，可将任务排入队列，让后台辅助角色执行此类任务。 可以使用诸如 [Azure 队列](../../storage/queues/storage-queues-introduction.md)之类的服务。 这将使 API 高效返回，从而提高策略执行性能。  

## <a name="api-error-handling"></a>API 错误处理

由于这些 API 位于 Azure AD B2C 系统外部，因此需要在技术配置文件中进行正确的错误处理。 确保最终用户得到适当的通知，应用程序可以妥善处理故障。

### <a name="how-to-gracefully-handle-api-errors"></a>如何妥善处理 API 错误

- API 可能会因各种原因而失败，让你的应用程序能够应对此类故障。 如果 API 无法完成请求，则[返回 HTTP 4XX 错误消息](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message)。 在 Azure AD B2C 策略中，尝试妥善处理 API 不可用的问题，可能会呈现更精简的体验。

- [妥善处理暂时性错误](../../active-directory-b2c/restful-technical-profile.md#error-handling)。 RESTFul API 配置文件允许为各种[断路器](/azure/architecture/patterns/circuit-breaker)配置错误消息。

- 主动监视并使用持续集成/持续交付 (CICD)，轮换[技术配置文件引擎](../../active-directory-b2c/restful-technical-profile.md)使用的 API 访问凭据，例如密码和证书。

## <a name="api-management---best-practices"></a>API 管理 - 最佳做法

部署 REST API 和配置 RESTful 技术配置文件时，遵循建议的最佳做法可帮助你避免犯一些常见错误和忽略某些事情。

### <a name="how-to-manage-apis"></a>如何管理 API

- API 管理 (APIM) 可发布、管理和分析 API。 APIM 还可处理身份验证，以提供对后端服务和微服务的安全访问。 使用 API 网关可横向扩展 API 部署、缓存和负载均衡。

- 建议在用户旅程开始时获取正确的令牌（而不是为每个 API 调用多次），并[保护 Azure APIM API](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga)。

## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原能力资源](resilience-b2c.md)
  - [可复原的最终用户体验](resilient-end-user-experience.md)
  - [通过开发人员最佳做法实现复原能力](resilience-b2c-developer-best-practices.md)
  - [通过监视和分析实现复原能力](resilience-with-monitoring-alerting.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [提高应用程序中身份验证和授权的复原能力](resilience-app-development-overview.md)