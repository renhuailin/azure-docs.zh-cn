---
title: Azure 通信服务 UI 框架 SDK 复合组件入门
titleSuffix: An Azure Communication Services quickstart
description: 本快速入门将介绍如何开始使用 UI 框架复合组件
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 17a8369463a27acffc8bdc52c48e4ae0624f41cd
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539667"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>快速入门：开始使用 UI 框架复合组件

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

通过使用 UI 框架将通信体验快速集成到你的应用程序中，开始使用 Azure 通信服务。 在本快速入门中，你将学习如何将 UI 框架复合组件集成到应用程序中来构建通信体验。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) Active LTS 和 Maintenance LTS 版本（建议使用 Node 12）。
- 活动的通信服务资源。 [创建通信服务资源](./../create-communication-resource.md)。
- 用于实例化呼叫复合组件的用户访问令牌。 了解如何[创建和管理用户访问令牌](./../access-tokens.md)。

## <a name="setting-up"></a>设置

UI 框架要求设置 React 环境。 我们接下来将进行设置。 如果你已有 React 应用，则可跳过此部分。

### <a name="set-up-react-app"></a>设置 React 应用

我们将对本快速入门使用“create-react-app”模板。 有关详细信息，请参阅：[React 入门](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

此过程结束时，你在 `my-app` 文件夹中应该会有一个完整的应用程序。 在本快速入门中，我们将修改 `src` 文件夹中的文件。

### <a name="install-the-package"></a>安装包

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务呼叫客户端库。 将提供的 tarball（个人预览版）移动到 my-app 目录。

```console

//Private Preview install tarball

npm install --save ./{path for tarball}

```

`--save` 选项在 package.json 文件中列出作为依赖项的库。

### <a name="run-create-react-app"></a>运行“创建 React 应用”

运行以下内容来测试“创建 React 应用”安装项：

```console

npm run start 

```

## <a name="object-model"></a>对象模型

以下类和接口会处理 Azure 通信服务 UI 客户端库的某些主要功能：

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| GroupCall | 用于呈现参与者库和控件的群组通话体验的复合组件。 |
| GroupChat | 用于呈现聊天线程和输入的群组聊天体验的复合组件 |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>初始化群组通话和群组聊天复合组件

转到 `my-app` 中的 `src` 文件夹，然后查找 `app.js` 文件。 在这里，我们将删除以下代码来初始化用于群组聊天和通话的复合组件。 可根据正在构建的通信体验的类型选择要使用哪一个。 如果需要，可同时使用这两者。 若要初始化组件，需要访问从 Azure 通信服务检索到的访问令牌。 若要详细了解如何获取访问令牌，请参阅[创建和管理用户访问令牌](./../access-tokens.md)。

> [!NOTE]
> 组件不会生成访问令牌、组 ID 和线程 ID。 这些元素来源于服务，后者通过适当的步骤生成这些 ID 并将它们传递到客户端应用程序。 有关详细信息，请参阅：[客户端服务器体系结构](./../../concepts/client-and-server-architecture.md)。
> 
> 例如：群组聊天复合组件要求用于进行初始化的 `token` 相关联的 `userId` 已加入提供的 `threadId`。 如果令牌未加入线程 ID，那么群组聊天复合组件将失败。 有关聊天的详细信息，请参阅：[聊天入门](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

```

## <a name="run-quickstart"></a>运行快速入门

若要运行上述代码，请使用以下命令：

```console

npm run start 

```

若要全面测试功能，还需要一个具有通话和聊天功能的客户端以加入通话和聊天线程。 若要了解可能的选项，请查看我们的[通话主图示例](./../../samples/calling-hero-sample.md)和[聊天主图示例](./../../samples/chat-hero-sample.md)。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [试用 UI Framework 基本组件](./get-started-with-components.md)

有关详细信息，请参阅以下资源：
- [UI 框架概述](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI 框架功能](./../../concepts/ui-framework/ui-sdk-features.md)
