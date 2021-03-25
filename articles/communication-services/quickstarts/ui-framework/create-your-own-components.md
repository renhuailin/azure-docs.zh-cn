---
title: 创建自己的 UI 框架组件
titleSuffix: An Azure Communication Services quickstart
description: 本快速入门介绍如何生成与 UI 框架兼容的自定义组件
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 093fcfd95d291d959ed49cc39a227a99f14a0383
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488238"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>快速入门：创建自己的 UI 框架组件

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

通过使用 UI 框架将通信体验快速集成到你的应用程序中，开始使用 Azure 通信服务。

在本快速入门中，你将了解如何使用由 UI Framework 提供的预定义状态接口创建你自己的组件。 此方法非常适合那些需要更多自定义项并希望使用自己的设计资产来实现体验的开发人员。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) Active LTS 和 Maintenance LTS 版本（建议使用 Node 12）。
- 活动的通信服务资源。 [创建通信服务资源](./../create-communication-resource.md)。
- 用于实例化呼叫客户端的用户访问令牌。 了解如何[创建和管理用户访问令牌](./../access-tokens.md)。

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

//For Private Preview install tarball

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
| 提供程序| Fluent UI 提供程序，开发人员可用它来修改基本的 Fluent UI 组件|
| CallingProvider| 用于将通话实例化的通话提供程序。 必须具有它才能添加基本组件|
| ChatProvider | 用于将聊天线程实例化的聊天提供程序。 必须具有它才能添加基本组件|
| connectFuncsToContext | 使用映射器将 UI 框架组件与基础提供程序连接的方法 |
| MapToChatMessageProps | 向组件提供聊天消息属性的聊天消息数据层映射器 |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>使用 Azure 通信服务凭据将聊天提供程序初始化

在本快速入门中，我们将使用聊天作为示例，有关通话的详细信息，请参阅[基本组件快速入门](./get-started-with-components.md)和[复合组件快速入门](./get-started-with-composites.md)。

转到 `my-app` 中的 `src` 文件夹，然后查找 `app.js` 文件。 我们将在这里删除以下代码，将聊天提供程序初始化。 此提供程序负责维护通话和聊天体验的上下文。 若要初始化组件，需要访问从 Azure 通信服务检索到的访问令牌。 若要详细了解如何获取访问令牌，请参阅[创建和管理访问令牌](./../access-tokens.md)。

UI 框架组件对服务的其余部分遵循相同的通用体系结构。 组件不会生成访问令牌、组 ID 和线程 ID。 这些元素来源于服务，后者通过适当的步骤生成这些 ID 并将它们传递到客户端应用程序。 有关详细信息，请参阅[客户端服务器体系结构](./../../concepts/client-and-server-architecture.md)。

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

进行初始化后，可借助此提供程序，使用 UI 框架组件和任何其他布局逻辑构建你自己的布局。 此提供程序负责对所有基础逻辑进行初始化，并将不同部件适当地连接在一起。 接下来，我们将使用 UI 框架映射器创建自定义组件以连接到聊天提供程序。


## <a name="create-a-custom-component-using-mappers"></a>使用映射器创建自定义组件

首先，我们将创建一个名为 `SimpleChatThread.js` 的新文件，用于创建组件。 首先，我们将导入所需的 UI 框架组件。 在这里，我们将使用现成的 HTML 和 React 为简单的聊天线程创建完全自定义的组件。 借助 `connectFuncsToContext` 方法，我们将使用 `MapToChatMessageProps` 映射器将属性映射到 `SimpleChatThread` 自定义组件。 通过这些属性，我们可以访问要发送和接收的聊天消息，并将其填充到简单的线程上。

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>向应用程序添加自定义组件

现在我们已经准备好了自定义组件，我们将导入它并将其添加到我们的布局中。

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>运行快速入门

若要运行上述代码，请使用以下命令：

```console

npm run start   

```

若要全面测试功能，你将需要第二个使用聊天功能的客户端来发送将由简单聊天线程接收的消息。 若要了解可能的选项，请查看我们的[通话主图示例](./../../samples/calling-hero-sample.md)和[聊天主图示例](./../../samples/chat-hero-sample.md)。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [试用 UI 框架复合组件](./get-started-with-composites.md)

有关详细信息，请参阅以下资源：
- [UI 框架概述](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI 框架功能](./../../concepts/ui-framework/ui-sdk-features.md)
- [UI 框架基本组件快速入门](./get-started-with-components.md)
