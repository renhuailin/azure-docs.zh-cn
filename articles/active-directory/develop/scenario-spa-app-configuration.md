---
title: 配置单页应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何生成单页应用程序（应用的代码配置）
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 7654178d9f3393fd7ff5a0a79da5d1f71c741bbf
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222180"
---
# <a name="single-page-application-code-configuration"></a>单页应用程序：代码配置

了解如何为单页应用程序 (SPA) 配置代码。

## <a name="microsoft-libraries-supporting-single-page-apps"></a>支持单页应用的 Microsoft 库

以下 Microsoft 库支持单页应用：

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>应用程序代码配置

在 MSAL 库中，应用程序注册信息在库初始化期间作为配置传递。

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
import * as Msal from "@azure/msal-browser"; // if using CDN, 'Msal' will be available in global scope

// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create PublicClientApplication instance
const publicClientApplication = new Msal.PublicClientApplication(config);
```

有关可配置选项的更多信息，请参阅[使用 MSAL.js 初始化应用程序](msal-js-initializing-client-applications.md)。

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript
import * as Msal from "msal"; // if using CDN, 'Msal' will be available in global scope

// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new Msal.UserAgentApplication(config);
```

有关可配置选项的更多信息，请参阅[使用 MSAL.js 初始化应用程序](msal-js-initializing-client-applications.md)。

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
import { MsalModule } from '@azure/msal-angular';
import { PublicClientApplication } from '@azure/msal-browser';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            }
        }), null, null)
    ]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})
export class AppModule { }
```

# <a name="react"></a>[React](#tab/react)

```javascript
import { PublicClientApplication } from "@azure/msal-browser";
import { MsalProvider } from "@azure/msal-react";

// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create PublicClientApplication instance
const publicClientApplication = new PublicClientApplication(config);

// Wrap your app component tree in the MsalProvider component
ReactDOM.render(
    <React.StrictMode>
        <MsalProvider instance={publicClientApplication}>
            <App />
        </ MsalProvider>
    </React.StrictMode>,
    document.getElementById('root')
);
```

---

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[登录和注销](scenario-spa-sign-in.md)。
