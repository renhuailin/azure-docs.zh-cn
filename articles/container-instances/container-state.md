---
title: Azure 容器实例状态
description: 了解 Azure 容器实例预配操作、容器和容器组的状态。
ms.author: nickoman
author: nickomang
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 0144b6ebd82c6df4c9ec501897a4315b4554103d
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014641"
---
# <a name="azure-container-instances-states"></a>Azure 容器实例状态

Azure 容器实例显示多个独立的状态值。 本文以目录的形式列出了这些值，并说明了其所在位置和含义。

## <a name="where-to-find-state-values"></a>在何处查找状态值

在 Azure 门户中，状态会显示在不同的位置。 所有状态值都可通过资源的 JSON 定义来访问。 此值可在“概述”边栏选项卡中的“概要”下找到，如下所示。

:::image type="content" source="./media/container-state/provisioning-state.png" alt-text="显示了 Azure 门户中的“概述”边栏选项卡。链接“JSON 视图”已突出显示。":::

状态还会显示在 Azure 门户中的其他位置。 下表汇总了状态值所在的位置：

|名称|JSON 路径|Azure 门户位置|
|-|-|-|
|容器组状态|`properties.instanceView.state`|在“概述”边栏选项卡中的“概要”下|
|当前容器状态|`properties.containers/initContainers[x].instanceView.currentState.state`|在“容器”边栏选项卡的表“状态”列下|
|先前容器状态|`properties.containers/initContainers[x].instanceView.previousState.state`|通过“概述”边栏选项卡中“概要”下的“JSON 视图”提供|
|正在设置状态|`properties.provisioningState`|通过“概述”边栏选项卡中“概要”下的“JSON 视图”提供；通过 HTTP 响应正文提供|

## <a name="container-groups"></a>容器组

此值是在后端上部署的容器组的状态。

:::image type="content" source="./media/container-state/container-group-state.png" alt-text="在 Web 浏览器中显示了 Azure 门户中的资源概述边栏选项卡。文本“状态: 正在运行”已突出显示。":::

- 正在运行：容器组正在运行，在发生用户操作或由重启策略导致的停止之前，它将持续保持运行。

- 已停止：容器组已停止，在没有用户操作的情况下，它不会按计划运行。

- 挂起：容器组正在等待初始化（完成运行 init 容器，并装载 Azure 文件卷（如果适用））。 除非发生用户操作（停止/删除），否则容器将继续尝试进入“正在运行”状态。

- 成功：容器组已成功完成运行。 仅适用于“从不”和“失败时”重启策略 。

- 失败：容器组无法完成运行。 仅适用于“从不”重启策略。 此状态指示基础结构故障（例如，错误的 Azure 文件共享凭据）或用户应用程序故障（例如，应用程序引用了不存在的环境变量）。

下表基于指定的重启策略显示了哪些状态适用于容器组：

|值|从不|失败时|始终|
|--|--|--|--|
|正在运行|是|是|是|
|已停止|是|是|是|
|挂起的|是|是|是|
|已成功|是|是|否|
|已失败|是|否|否|

## <a name="containers"></a>容器

容器有两个状态值 - 当前状态和先前状态。 在 Azure 门户中仅显示当前状态，如下所示。 不管容器组的重启策略如何，所有状态值都适用于任一给定容器。

> [!NOTE]
> `currentState` 和 `previousState` 的 JSON 值包含附加信息（例如退出代码或原因），这些信息不会显示在 Azure 门户中的其他位置。

:::image type="content" source="./media/container-state/container-state.png" alt-text="显示了 Azure 门户中的“容器”边栏选项卡。其中显示了一个表格，“状态”列下的“正在运行”已突出显示。":::

- 正在运行：容器正在运行。

- 正在等待：容器正在等待运行。 此状态指示 init 容器仍在运行，或者由于出现崩溃循环，容器正在回退。

- 已终止：容器已终止，并返回了退出代码值。

## <a name="provisioning"></a>设置

此值是上次对容器组执行的操作的状态。 通常，此操作是 PUT（创建），但也可能是 POST（启动/重启/停止）或 DELETE（删除）。

> [!IMPORTANT]
> 此外，用户不应创建依赖于非终端预配状态的依赖项。 依赖于“成功”和“失败”状态的依赖项是可接受的 。

除 JSON 视图以外，还可以在 [HTTP 调用的响应正文](/rest/api/container-instances/containergroups/createorupdate#response)中找到预配状态。

### <a name="create-start-and-restart-operations"></a>创建、启动和重启操作

> [!IMPORTANT]
> PUT（创建）操作是异步的。 PUT 响应正文中的返回值并非最终状态。 监视部署状态的建议方式是对容器组的 resourceId 或 AsyncOperation（在 PUT 响应头中返回）发出后续的 GET 调用。

这些状态适用于 PUT（创建）和 POST（启动/重启）事件。

- 挂起：容器组正在等待进行基础结构设置，例如，分配节点、预配虚拟网络，或者在拉取用户映像之前需要执行的任何其他操作。

- 正在创建：基础结构设置已完成。 容器组现在正在启动并接收所需的资源（装载 Azure 文件卷、获取入口 IP 地址等）。

- 成功：容器组已成功使其容器进入“正在运行”状态，并已接收所需的所有资源。

- 不正常：容器组不正常。 如果出现意外状态（例如节点关闭），将自动触发一个作业，以通过移动容器组来修复该容器组。

- 正在修复：正在移动容器组以修复不正常状态。

- 失败：容器组无法达到“成功”预配状态 。 可能会导致失败的原因有很多（无法访问网络配置文件、指定区域中的容量不足、耗尽了用户配额、30 分钟后超时，等等）。 在 JSON 视图中的 `events` 下可以找到有关失败的详细信息。
    > [!NOTE]
    > 失败状态并不意味着资源已被删除或停止尝试进入成功状态。 容器组状态将指示该组的当前状态。 如果你想要确保容器组在进入“失败”预配状态后不运行，必须将它停止或删除。

### <a name="stop-and-delete-operations"></a>停止和删除操作

这些值适用于 POST（停止）和 DELETE（删除）事件。

- 成功：用于停止或删除容器组的操作已成功完成。

- 失败：容器组无法达到“成功”预配状态，这意味着停止/删除事件未完成 。 在 JSON 视图中的 `events` 下可以找到有关失败的详细信息。