---
title: 将 emptyDir 卷装载到容器组
description: 了解如何在 Azure 容器实例中装载 emptyDir 卷以在容器组中的容器之间共享数据
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 328dbec7b7689eaecc89f06957a2ad84dbcdc9dd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565240"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>在 Azure 容器实例中装载 emptyDir 卷

了解如何在 Azure 容器实例中装载 *emptyDir* 卷以在容器组中的容器之间共享数据。 使用 emptyDir 卷作为容器化工作负荷的临时缓存。

> [!NOTE]
> 当前只有 Linux 容器能装载 *emptyDir* 卷。 虽然我们正致力于为 Windows 容器提供全部功能，但你可在[概述](container-instances-overview.md#linux-and-windows-containers)中了解当前的平台差异。

## <a name="emptydir-volume"></a>emptyDir 卷

*emptyDir* 卷提供了容器组中的每个容器都可访问的可写目录。 该组中的容器可以读取和写入此卷中的相同文件，并且可以在每个容器中使用相同或不同路径装载此卷。

一些示例用于 *emptyDir* 卷：

* 暂存空间
* 长时间运行任务期间的检查点
* 存储由挎斗容器检索的数据以及由应用程序容器提供的数据

*emptyDir* 卷中的数据将一直保留到容器崩溃。 但是，并不保证重新启动的容器能够持久保留 *emptyDir* 卷中的数据。 如果停止容器组，则不会持久保留 emptyDir 卷。

Linux emptyDir 卷的最大大小为 50 GB。

## <a name="mount-an-emptydir-volume"></a>装载 emptyDir 卷

若要在容器实例中装载 emptyDir 卷，可以使用 [Azure 资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)、[YAML 文件](container-instances-reference-yaml.md)或其他编程方法来部署容器组。

首先，在该文件的容器组 `properties` 节中填充 `volumes` 数组。 接下来，针对容器组中希望装载 *emptyDir* 卷的每个容器，在容器定义的 `properties` 节中填充 `volumeMounts` 数组。

例如，以下资源管理器模板创建了一个包含两个容器的容器组，每个容器均装载了 *emptyDir* 卷：

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/resourcemanager-templates/container-instances/aci-deploy-volume-emptydir.json)]

若要查看容器组部署的示例，请参阅[使用资源管理器模板部署多容器组](container-instances-multi-container-group.md)和[使用 YAML 文件部署多容器组](container-instances-multi-container-yaml.md)。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 Azure 文件共享](container-instances-volume-azure-files.md)
* [在 Azure 容器实例中装载 gitRepo 卷](container-instances-volume-gitrepo.md)
* [在 Azure 容器实例中装载机密卷](container-instances-volume-secret.md)
