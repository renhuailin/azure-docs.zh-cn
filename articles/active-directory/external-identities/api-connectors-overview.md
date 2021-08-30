---
title: 关于自助注册流中的 API 连接器 - Azure AD
description: 使用 Azure Active Directory (Azure AD) API 连接器，可通过使用 Web API 来自定义并扩展自助注册用户流。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ead99d955fbd82099b4ad577e99026e8e66aea5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442407"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>使用 API 连接器来自定义并扩展自助注册 

## <a name="overview"></a>概述 
作为开发者或 IT 管理员，你可以使用 API 连接器将[自助注册用户流](self-service-sign-up-overview.md)与 Web API 集成，以自定义注册体验，并与外部系统集成。 例如，使用 API 连接器，可以：

- [与自定义审批工作流集成](self-service-sign-up-add-approvals.md)。 连接到用于管理和限制帐户创建的自定义审批系统。
- [执行身份验证](code-samples-self-service-sign-up.md#identity-verification)。 使用身份验证服务为帐户创建决策添加额外的安全级别。
- 验证用户输入数据。 针对格式错误或无效的用户数据进行验证。 例如，可以根据外部数据存储或允许值列表中的现有数据验证用户提供的数据。 如果无效，你可以要求用户提供有效数据或阻止用户继续注册流。
- 覆盖用户属性。 重新格式化或为从用户收集的属性赋值。 例如，如果用户使用全小写或全大写字母输入了名字，则你可以设置该名字的格式，只将第一个字母大写。 
- 运行自定义业务逻辑。 可以在云系统中触发下游事件，以发送推送通知、更新企业数据库、管理权限、审核数据库以及执行其他自定义操作。

API 连接器通过定义 API 调用的 HTTP 终结点 URL 和身份验证，为 Azure Active Directory 提供调用 API 终结点所需的信息。 配置 API 连接器后，可以为用户流中的特定步骤启用它。 当用户到达注册流中的那一步时，API 连接器被调用，并作为 HTTP POST 请求具体化到你的 API 中，将用户信息（“声明”）作为 JSON 正文中的键值对。 API 响应可能会影响用户流的执行。 例如，API 响应可以阻止用户进行注册，要求用户重新输入信息，或覆盖并追加用户属性。

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>可在其中启用用户流中的 API 连接器

用户流中有两个位置可用于启用 API 连接器：

- 在注册期间与标识提供者进行联合之后
- 创建用户之前

> [!IMPORTANT]
> 在这两种情况下，将在用户“注册”而不是登录过程中调用 API 连接器。

### <a name="after-federating-with-an-identity-provider-during-sign-up"></a>在注册期间与标识提供者进行联合之后

在注册过程的此步骤中，在用户使用标识提供者（例如 Google、Facebook 和 Azure AD）进行身份验证之后，会立即调用 API 连接器。 此步骤优先于特性收集页，后者是向用户显示的用于收集用户特性的表单。 如果用户正在使用本地帐户注册，则不会调用此步骤。 以下是可能在此步骤中启用的 API 连接器方案的示例：

- 使用用户提供的电子邮件或联合标识在现有系统中查找声明。 从现有系统返回这些声明，预先填充属性集合页，并使它们可在令牌中返回。
- 根据社交标识实现允许或阻止列表。

### <a name="before-creating-the-user"></a>创建用户之前

在显示属性集合页之后，将调用注册过程中的此步骤的 API 连接器（如果已包含一个连接器）。 在创建用户帐户之前始终会调用此步骤。 下面是你在注册过程中此时可能会启用的方案示例：

- 验证用户输入数据并要求用户重新提交数据。
- 阻止用户根据用户输入的数据进行注册。
- 执行身份验证。
- 在外部系统中查询有关用户的现有数据，以将其返回到应用程序令牌中或将其存储在 Azure AD 中。

## <a name="next-steps"></a>后续步骤
- 了解如何[向用户流添加 API 连接器](self-service-sign-up-add-api-connector.md)
- 了解如何[将自定义审批系统添加到自助注册](self-service-sign-up-add-approvals.md)