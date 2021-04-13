---
title: Unity 游戏对象和组件
description: 介绍用于处理远程渲染实体和组件的特定于 Unity 的方法。
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594140"
---
# <a name="interact-with-unity-game-objects-and-components"></a>与 Unity 游戏对象和组件交互

Azure 远程渲染 (ARR) 针对处理大量对象的情况进行了优化（请参阅[限制](../../reference/limits.md)）。 虽然可以在主机上管理大型而复杂的层次结构，但在硬件处理能力不足的设备上，在 Unity 中全部复制这些层次结构是不可行的。

因此，在主机上加载模型时，Azure 远程渲染会对客户端设备上有关模型结构的信息进行镜像（这样会产生网络流量），但不会在 Unity 中复制对象和组件。 相反，应该由你来手动请求所需的 Unity 游戏对象和组件，这样你就可以将开销限制在实际需要的程度。 通过这种方式，你可以更好地控制客户端性能。

因此，Azure 远程渲染的 Unity 集成提供了根据需要复制远程渲染结构的附加功能。

## <a name="load-a-model-in-unity"></a>在 Unity 中加载模型

在加载模型时，你会获得对所加载模型的根对象的引用。 此引用不是 Unity 游戏对象，但你可以使用扩展方法 `Entity.GetOrCreateGameObject()` 将它转换为一个游戏对象。 该函数需要一个 `UnityCreationMode` 类型的参数。 如果传递 `CreateUnityComponents`，将会为主机上存在的所有远程渲染组件使用代理组件来另外填充新创建的 Unity 游戏对象。 不过，还是建议首选使用 `DoNotCreateUnityComponents`，以将开销保持在最低程度。

### <a name="load-model-with-unity-coroutines"></a>使用 Unity 协同例程来加载模型

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>使用 await 模式来加载模型

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

上面的代码示例通过 SAS 使用了模型加载路径，因为内置模型已加载。 通过 blob 容器（使用 `LoadModelAsync` 和 `LoadModelOptions`）对模型寻址以完全类似的方式工作。

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

创建 Unity 游戏对象会将 `RemoteEntitySyncObject` 组件隐式添加到该游戏对象。 此组件用于将实体转换同步到服务器。 在默认情况下，`RemoteEntitySyncObject` 会要求用户显式调用 `SyncToRemote()`，以将本地 Unity 状态同步到服务器。 启用 `SyncEveryFrame` 会自动同步该对象。

具有 `RemoteEntitySyncObject` 的对象可以通过 :::no-loc text="Show children"::: 按钮在 Unity 编辑器中实例化并显示其远程子对象。

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>包装器组件

附加到远程渲染实体的[组件](../../concepts/components.md)通过代理 `MonoBehavior` 向 Unity 公开。 这些代理表示 Unity 中的远程组件，会将所有修改转发到主机。

若要创建代理远程渲染组件，请使用扩展方法 `GetOrCreateArrComponent`：

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>耦合生存期

远程[实体](../../concepts/entities.md)和 Unity 游戏对象在通过 `RemoteEntitySyncObject` 进行绑定时，它们的生存期会耦合。 如果使用此类游戏对象调用 `UnityEngine.Object.Destroy(...)`，则也会删除远程实体。

若要销毁 Unity 游戏对象，而不影响远程实体，则需要先在 `RemoteEntitySyncObject` 上调用 `Unbind()`。

这种情况同样适用于所有代理组件。 若要只销毁客户端表示形式，则需要先在代理组件上调用 `Unbind()`：

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>后续步骤

* [为 Unity 设置远程渲染](unity-setup.md)
* [教程：在 Unity 中操作远程实体](../../tutorials/unity/manipulate-models/manipulate-models.md)
