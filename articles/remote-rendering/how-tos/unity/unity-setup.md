---
title: 为 Unity 设置远程渲染
description: 如何在 Unity 项目中初始化 Azure 远程渲染
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594208"
---
# <a name="set-up-remote-rendering-for-unity"></a>为 Unity 设置远程渲染

为了在 Unity 中启用 Azure 远程渲染 (ARR)，我们提供了专门的方法来处理一些特定于 Unity 的事项。

## <a name="startup-and-shutdown"></a>启动和关闭

若要初始化远程渲染，请使用 `RemoteManagerUnity`。 此类调用泛型 `RenderingConnection`，但已为你实现了特定于 Unity 的细节。 例如，Unity 使用特定坐标系统。 调用 `RemoteManagerUnity.Initialize` 时，会设置适当的约定。 该调用还要求你提供用于显示远程渲染内容的 Unity 相机。

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

若要关闭远程渲染，请调用 `RemoteManagerStatic.ShutdownRemoteRendering()`。

在创建 `RenderingSession` 并将其选为主渲染会话后，必须将其注册到 `RemoteManagerUnity`：

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>完整示例代码

下面的代码演示了在 Unity 中初始化 Azure 远程渲染所需的所有步骤：

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>便捷函数

### <a name="session-state-events"></a>会话状态事件

`RemoteManagerUnity.OnSessionUpdate` 在其会话状态更改时会发出相应的事件。有关详细信息，请参阅代码文档。

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` 是一个可选组件，用于简化设置和会话管理。 它包含在应用程序退出时或在编辑器中退出播放模式时自动停止其会话的选项，以及在需要时自动续订会话租约的选项。 它缓存会话属性之类的数据（请查看其 `LastProperties` 变量），并公开有关会话状态更改和会话错误的事件。

同时不能有多个 `ARRServiceUnity` 实例。 它的目的是通过实现一些常见功能让你更快地开始。 但是，对于较大的应用程序，最好你自己做这些事情。

有关如何设置和使用 `ARRServiceUnity` 的示例，请参阅[教程：查看远程渲染的模型](../../tutorials/unity/view-remote-models/view-remote-models.md)。

## <a name="next-steps"></a>后续步骤

* [安装 Unity 的远程渲染包](install-remote-rendering-unity-package.md)
* [教程：查看远程渲染的模型](../../tutorials/unity/view-remote-models/view-remote-models.md)
