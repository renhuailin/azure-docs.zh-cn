---
title: 使用 Azure AD B2C 的外部进程的弹性接口 |Microsoft Docs
description: 用外部进程构建弹性接口的方法
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
ms.openlocfilehash: c96856c988cae891e64ddf460d61851102e4666c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919324"
---
# <a name="resilient-interfaces-with-external-processes"></a>具有外部进程的弹性接口

本文介绍如何在用户旅程中规划和实现 RESTFul Api，并使应用程序更具弹性的 API 故障。

![Image 显示具有外部进程组件的接口](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>确保正确放置 Api

标识体验框架 (IEF) 策略允许使用 [RESTFUL API 技术配置文件](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile)调用外部系统。 外部系统不受 IEF 运行时环境控制，并且是潜在的故障点。

### <a name="how-to-manage-external-systems-using-apis"></a>如何使用 Api 管理外部系统

- 调用接口访问某些数据时，请检查数据是否将用于驱动身份验证决定。 评估信息是否对应用程序的核心功能至关重要。 例如，电子商务与辅助功能，例如管理。 如果身份验证不需要此信息，并且仅在辅助方案中需要此信息，请考虑将调用移动到应用程序逻辑。

- 如果身份验证所需的数据相对静态且小，且没有其他业务原因要从目录中外部化，请考虑将其放在目录中。

- 尽可能从预验证路径中删除 API 调用。 如果无法做到这一点，则必须对拒绝服务 (DoS) 和分布式拒绝服务 (DDoS) 攻击，并将其置于 Api 前面。 攻击者可以加载登录页面，尝试利用 DoS 攻击使 API 淹没，并使应用程序瘫痪。 例如，在登录时使用 CAPTCHA，"注册流" 可以提供帮助。

- 在使用标识提供者登录或创建用户之前，尽可能使用 [内置注册用户流的 API 连接器](https://docs.microsoft.com/azure/active-directory-b2c/api-connectors-overview) 以与 web api 集成。 由于用户流已经过广泛测试，因此您可能无需执行用户流级别的功能、性能或缩放测试。 你仍需要测试应用程序的功能、性能和缩放性。

- Azure AD RESTFul API [技术配置文件](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile) 不提供任何缓存行为。 相反，RESTFul API 配置文件实现了重试逻辑和策略中内置的超时。

- 对于需要写入数据的 Api，将任务排队，使后台辅助角色执行此类任务。 可以使用 [Azure 队列](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction) 等服务。 这会使 API 返回高效地提高策略执行性能。  

## <a name="api-error-handling"></a>API 错误处理

当 Api 位于 Azure AD B2C 系统外时，需要在技术配置文件中进行正确的错误处理。 请确保最终用户得到适当的通知，并且应用程序可以正常地处理故障。

### <a name="how-to-gracefully-handle-api-errors"></a>如何适当地处理 API 错误

- API 可能会因各种原因而失败，使应用程序能够应对此类故障。 如果 API 无法完成请求，则[返回 HTTP 4xx 错误消息](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#returning-validation-error-message)。 在 Azure AD B2C 策略中，尝试适当地处理 API 不可用的情况，可能会呈现更精简的体验。

- [妥善处理暂时性错误](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#error-handling)。 RESTFul API 配置文件允许您为各种 [断路](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)程序配置错误消息。

- 主动监视和使用持续集成/持续交付 (CICD) ，旋转 API 访问凭据，如 [技术配置文件引擎](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile)使用的密码和证书。

## <a name="api-management---best-practices"></a>API 管理-最佳实践

部署 REST Api 并配置 RESTful 技术配置文件时，遵循建议的最佳实践可帮助你避免发生常见错误和问题。

### <a name="how-to-manage-apis"></a>如何管理 Api

- API 管理 (APIM) 发布、管理和分析 Api。 APIM 还处理身份验证，以提供对后端服务和微服务的安全访问。 使用 API 网关横向扩展 API 部署、缓存和负载均衡。

- 建议在用户旅程开始时获取正确的令牌，而不是为每个 API 调用多次，并 [保护 AZURE APIM API](https://docs.microsoft.com/azure/active-directory-b2c/secure-api-management?tabs=app-reg-ga)。

## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原资源](resilience-b2c.md)
  - [复原最终用户体验](resilient-end-user-experience.md)
  - [通过开发人员的最佳做法实现复原](resilience-b2c-developer-best-practices.md)
  - [通过监视和分析进行恢复](resilience-with-monitoring-alerting.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在应用程序中提高身份验证和授权的复原能力](resilience-app-development-overview.md)
