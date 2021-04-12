---
title: 对象和资源生存期
description: 介绍不同类型的生存期管理
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593885"
---
# <a name="object-and-resource-lifetime"></a>对象和资源生存期

Azure 远程渲染区分两种类型：对象和资源。

## <a name="object-lifetime"></a>对象生存期

对象可以由用户自行创建、修改和销毁。 对象可以自由复制，每个实例可以随时间而改变。 因此，[实体](entities.md)和[组件](components.md)是对象。

对象的生存期完全受用户控制。 但是它与客户端表示形式的生存期无关。 诸如 `Entity` 和 `Component` 的类包含 `Destroy` 函数，必须调用该函数才能释放远程渲染主机上的对象。 此外，`Entity.Destroy()` 将销毁实体、其子级以及该层次结构中的所有组件。

## <a name="resource-lifetime"></a>资源生存期

资源的生存期完全由远程渲染主机管理。 资源在内部进行引用计数。 如果不再引用它们，则会将其释放。

大多数资源只能间接创建，通常方法是从文件加载它们。 多次加载同一文件时，Azure 远程渲染将返回相同的引用，而不会再次加载该数据。

很多资源是不可变的，例如[网格](meshes.md)和[纹理](textures.md)。 但某些资源是可变的，例如[材料](materials.md)。 资源通常是共享的，因此修改资源可能会影响多个对象。 例如，更改材料的颜色将更改引用该材料的网络，进而更改使用该网格的所有对象的颜色。

### <a name="built-in-resources"></a>内置资源

Azure 远程渲染包含一些内置资源，可以在调用 `RenderingSession.Connection.LoadXYZAsync()` 期间通过在其各自标识符前追加 `builtin://` 来加载这些资源。 文档中列出了各个函数的可用内置资源。 例如，[天空一章](../overview/features/sky.md)列出了内置的天空纹理。

## <a name="general-lifetime"></a>常规生存期

所有对象和资源的生存期受连接状态影响。 断开连接后，将丢弃所有内容。 当重新连接到相同会话时，场景图将为空，且已清除所有资源。

在实践中，断开连接后，将相同资源加载到会话中的速度通常比第一次快。 这是因为，首次必须从 Azure 存储下载大部分资源，而第二次不必再下载它们，因此节省了大量时间。

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [组件](components.md)
* [模型](models.md)
