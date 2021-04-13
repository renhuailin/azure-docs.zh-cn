---
title: 网格
description: Azure 远程渲染范围内网格的定义
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594512"
---
# <a name="meshes"></a>网格

## <a name="mesh-resource"></a>网格资源

网格是一种不可变的[共享资源](../concepts/lifetime.md)，只能通过[模型转换](../how-tos/conversion/model-conversion.md)来创建。 网格包含一个或多个 *子网格*，以及一种 [raycast 查询](../overview/features/spatial-queries.md)的物理表示形式。 每个子网格都引用一种默认情况下应会渲染的[材料](materials.md)。 若要在三维空间中放置网格，可将 [MeshComponent](#meshcomponent) 添加到[实体](entities.md)。

### <a name="mesh-resource-properties"></a>网格资源属性

`Mesh` 类属性为：

* **材料：** 材料阵列。 每种材料由不同的子网格使用。 阵列中的多个条目可引用相同的[材料](materials.md)。 在运行时无法修改该数据。

* **边界：** 网格顶点的局部空间轴对齐边界框 (AABB)。

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent` 类用于放置网格资源的实例。 每个 MeshComponent 引用单一网格。 它可能替代用于渲染每个子网格的材料。

### <a name="meshcomponent-properties"></a>MeshComponent 属性

* **网格：** 此组件使用的网格资源。

* **材料：** 网格组件自身指定的材料阵列。 该阵列的长度始终与网格资源上的“材料”阵列相同。 在此阵列中，不应被网格默认值覆盖的材料应设置为 *null*。

* **UsedMaterials：** 每个子网格实际使用的材料阵列。 对于非 null 值，将与“材料”阵列中的数据完全相同。 否则，它将包含来自网格实例中“材料”阵列的值。

### <a name="sharing-of-meshes"></a>网格共享

网格组件的多个实例之间可以共享 `Mesh` 资源。 而且，分配给网格组件的 `Mesh` 资源可以随时以编程方式进行更改。 下列代码演示了如何克隆网格：

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API 文档

* [C# Mesh 类](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent 类](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C++ Mesh 类](/cpp/api/remote-rendering/mesh)
* [C++ MeshComponent 类](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>后续步骤

* [材料](materials.md)