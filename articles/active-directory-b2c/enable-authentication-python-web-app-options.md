---
title: 使用 Azure Active Directory B2C 启用 Python Web 应用程序选项
description: 本文介绍如何启用 Python Web 应用程序选项。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 97f1cb1f13ab990a6590576ae93ba86a4c935300
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220365"
---
# <a name="enable-authentication-options-in-a-python-web-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在 Python Web 应用中启用验证选项 

本文介绍如何启用、自定义和增强 Python Web 应用程序的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验。 

在开始之前，请务必熟悉如何[使用 Azure AD B2C 在示例 Python Web 应用中配置身份验证](configure-authentication-sample-python-web-app.md)。

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

若要在身份验证 URL 中使用自定义域和租户 ID，请执行以下操作： 

1. 请遵循[启用自定义域](custom-domain.md)中的指导。
1. 在“app_config.py”字段中，将 `authority_template` 类成员更新为你的自定义域。

以下 Python 代码显示了更改前的应用设置：

```python
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"
```

以下 Python 代码显示了更改后的应用设置：

```python
authority_template = "https://custom.domain.com/00000000-0000-0000-0000-000000000000/{user_flow}" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 如果使用的是自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 
1. 找到 `initiate_auth_code_flow` 方法，然后添加 `login_hint` 参数及相应的标识提供者域名（例如 facebook.com）。

```python
def _build_auth_code_flow(authority=None, scopes=None):
    return _build_msal_app(authority=authority).initiate_auth_code_flow(
        scopes or [],
        redirect_uri=url_for("authorized", _external=True),
        login_hint="bob@contoso.com")
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 检查外部标识提供者的域名。 有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。 
1. 找到 `initiate_auth_code_flow` 方法，然后添加 `domain_hint` 参数及相应的登录提示。

    ```python
    def _build_auth_code_flow(authority=None, scopes=None):
        return _build_msal_app(authority=authority).initiate_auth_code_flow(
            scopes or [],
            redirect_uri=url_for("authorized", _external=True),
            domain_hint="facebook.com")
    ```


## <a name="next-steps"></a>后续步骤

- 若要了解更多信息，请参阅[适用于 Python 的 MSAL 配置选项](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki)。
