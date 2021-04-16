---
title: Azure 通信服务 UI 框架基本组件入门
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入门中，你将学习如何开始使用 UI 框架基本组件
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 336059007cfca40a74ad5a4395c6f9a59215bb58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825775"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>快速入门：开始使用 UI 框架基本组件

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

使用 UI 框架将通信体验快速集成到你的应用程序中，实现 Azure 通信服务入门。 在本快速入门中，你将学习如何将 UI 框架基本组件集成到应用程序中来构建通信体验。 

UI 框架组件分为两种风格：“基本”和“复合”。

- 基本组件表示分散通信功能，它们是可用于构建复杂通信体验的基本构建基块。 
- 复合组件是适合常见通信方案的一站式体验，它们是以基本组件为构建基块进行构建的，而且进行了打包，便于轻松集成到应用程序中 。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) Active LTS 和 Maintenance LTS 版本（建议使用 Node 12）。
- 活动的通信服务资源。 [创建通信服务资源](./../create-communication-resource.md)。
- 用于实例化呼叫客户端的用户访问令牌。 了解如何[创建和管理用户访问令牌](./../access-tokens.md)。

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

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务呼叫 SDK。 将提供的 tarball（个人预览版）移动到 my-app 目录。

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

以下类和接口处理 Azure 通信服务 UI SDK 的某些主要功能：

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| 提供程序| Fluent UI 提供程序，开发人员可用它来修改基本的 Fluent UI 组件|
| CallingProvider| 用于将通话实例化的通话提供程序。 必须具有它才能添加其他组件|
| ChatProvider | 用于将聊天线程实例化的聊天提供程序。 必须具有它才能添加其他组件|
| MediaGallery | 显示通话参与者及其远程视频流的基本组件 |
| MediaControls | 用于控制通话（包括静音、视频、共享屏幕）的基本组件 |
| ChatThread | 使用键入指示符和阅读回执等内容呈现聊天线程的基本组件 |
| SendBox | 用户可用于输入将发送至已加入的线程的消息的基本组件|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>使用 Azure 通信服务凭据将通话和聊天提供程序初始化

转到 `my-app` 中的 `src` 文件夹，然后查找 `app.js` 文件。 我们将在这里删除以下代码，将通话和聊天提供程序初始化。 这些提供程序负责维护通话和聊天体验的上下文。 可根据正在构建的通信体验的类型选择要使用哪一个。 如果需要，可同时使用这两者。 若要初始化组件，需要访问从 Azure 通信服务检索到的访问令牌。 若要详细了解如何获取访问令牌，请查看[创建和管理访问令牌](./../access-tokens.md)。

> [!NOTE]
> 组件不会生成访问令牌、组 ID 和线程 ID。 这些元素来源于服务，后者通过适当的步骤生成这些 ID 并将它们传递到客户端应用程序。 有关详细信息，请参阅：[客户端服务器体系结构](./../../concepts/client-and-server-architecture.md)。
> 
> 例如：聊天提供程序要求与正用于对它进行初始化的 `token` 相关联的 `userId` 已加入提供的 `threadId`。 如果令牌未加入线程 ID，那么聊天提供程序将失败。 有关聊天的详细信息，请查看：[聊天入门](./../chat/get-started.md)

我们将使用 Fluent UI 主题来增强应用程序的外观：

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

进行初始化后，可借助此提供程序，使用 UI 框架基本组件和任何其他布局逻辑构建你自己的布局。 此提供程序负责对所有基础逻辑进行初始化，并将不同部件适当地连接在一起。 接下来，我们将使用 UI 框架提供的各种基本组件来构建通信体验。 你可自定义这些组件的布局，并添加想要随之一同呈现的其他任何自定义组件。 

## <a name="build-ui-framework-calling-component-experiences"></a>构建 UI 框架通话组件体验

对于通话，我们将使用 `MediaGallery` 和 `MediaControls` 组件。 若要详细了解这些组件，请查看 [UI 框架功能](./../../concepts/ui-framework/ui-sdk-features.md)。 若要开始，请在 `src` 文件夹中，创建一个名为 `CallingComponents.js` 的新文件。 我们将在这里初始化一个函数组件，该组件将保存随后要在 `app.js` 中导入的基本组件。 你可围绕组件添加其他布局和样式。 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

在此文件的底部，我们从 UI 框架使用 `connectFuncsToContext` 方法导入了通话组件，以便使用映射函数 `MapToCallingSetupProps` 将通话 UI 组件连接到基础状态。 此方法会生成其属性已填充内容的组件，我们随后使用这些组件来检查状态和加入通话。 使用 `isCallInitialized` 属性检查 `CallAgent` 是否准备就绪，然后使用 `joinCall` 加入通话。 对于开发人员想要控制数据如何推送到组件的情况，UI 框架支持使用自定义映射函数。

## <a name="build-ui-framework-chat-component-experiences"></a>构建 UI 框架聊天组件体验

对于聊天，我们将使用 `ChatThread` 和 `SendBox` 组件。 若要详细了解这些组件，请查看 [UI 框架功能](./../../concepts/ui-framework/ui-sdk-features.md)。 若要开始，请在 `src` 文件夹中，创建一个名为 `ChatComponents.js` 的新文件。 我们将在这里初始化一个函数组件，该组件将保存随后要在 `app.js` 中导入的基本组件。

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>向主应用程序添加通话和聊天组件

回到 `app.js` 文件中，我们现将组件添加到 `CallingProvider` 和 `ChatProvider` 中，如下所示。

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

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
> [试用 UI 框架复合组件](./get-started-with-composites.md)

有关详细信息，请参阅以下资源：
- [UI 框架概述](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI 框架功能](./../../concepts/ui-framework/ui-sdk-features.md)
