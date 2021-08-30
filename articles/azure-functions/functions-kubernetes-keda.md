---
title: Kubernetes 上使用 KEDA 的 Azure Functions
description: 了解如何使用 KEDA (Kubernetes-based event driven autoscaling) 在云中或本地的 Kubernetes 中运行 Azure Functions。
author: eamonoreilly
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: eamono
ms.openlocfilehash: 736945109cd18bd7c6f3a6b9a16b6549f7c65652
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741393"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Kubernetes 上使用 KEDA 的 Azure Functions

Azure Functions 运行时可在托管位置和所需方式方面提供灵活性。  [KEDA](https://keda.sh) (Kubernetes-based Event Driven Autoscaling) 与 Azure Functions 运行时和工具无缝结合，以提供 Kubernetes 中的事件驱动缩放。

## <a name="how-kubernetes-based-functions-work"></a>基于 Kubernetes 的函数的工作原理

Azure Functions 服务由两个关键组件组成：运行时和缩放控制器。  Functions 运行时会运行并执行代码。  运行时包括有关如何触发、记录和管理函数执行的逻辑。  Azure Functions 运行时可以在任意位置运行。  另一个组件是缩放控制器。  缩放控制器监视以函数为目标的事件的速率，并主动缩放运行应用的实例数。  若要了解更多信息，请参阅 [Azure Functions 的缩放和托管](functions-scale.md)。

基于 Kubernetes 的 Functions 在 [Docker 容器](functions-create-function-linux-custom-image.md)中提供 Functions 运行时，通过 KEDA 进行事件驱动缩放。  KEDA 可以横向缩减到 0 个实例（没有事件发生时），以及横向扩展到 n 个实例。 它通过为 Kubernetes 自动缩放程序（水平 Pod 自动缩放程序）公开自定义指标来实现此功能。  将 Functions 容器与 KEDA 结合使用时，可以在任何 Kubernetes 群集中复制无服务器函数功能。  对于无服务器基础结构，还可以使用 [Azure Kubernetes 服务 (AKS) 虚拟节点](../aks/virtual-nodes-cli.md)功能部署这些函数。

## <a name="managing-keda-and-functions-in-kubernetes"></a>在 Kubernetes 中管理 KEDA 和函数

若要在 Kubernetes 群集上运行函数，必须安装 KEDA 组件。 可通过下面其中一种方式安装此组件：

+ Azure Functions Core Tools：使用 [`func kubernetes install` 命令](functions-core-tools-reference.md#func-kubernetes-install)。

+ Helm：可以通过多种方式在所有 Kubernetes 群集中安装 KEDA，包括 Helm。  [KEDA 站点](https://keda.sh/docs/deploy/)上提供了部署选项。

## <a name="deploying-a-function-app-to-kubernetes"></a>将函数应用部署到 Kubernetes

可以将任何函数应用部署到运行 KEDA 的 Kubernetes 群集。  由于函数在 Docker 容器中运行，因此项目需要一个 Dockerfile。  调用 `func init` 创建项目时，可以使用 [`--docker` 选项][func init]创建 Dockerfile。 如果忘记执行此操作，则始终可以从 Functions 项目的根再次调用 `func init`，这次使用 [`--docker-only` 选项][func init]，如以下示例所示。 

```command
func init --docker-only
```

若要详细了解 Dockerfile 生成，请参阅 [`func init`][func init] 参考。 

若要生成映像并将函数部署到 Kubernetes，请运行以下命令：

```command
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

在本例中，将 `<name-of-function-deployment>` 替换为函数应用名称。

部署命令执行以下操作：

1. 先前创建的 Dockerfile 用于生成适用于函数应用的本地映像。
1. 本地映像被标记并推送到用户登录的容器注册表。
1. 创建清单并将其应用于定义 Kubernetes `Deployment` 资源、`ScaledObject` 资源和 `Secrets`（包括从 `local.settings.json` 文件导入的环境变量）的群集。

要了解详细信息，请参阅 [`func kubernetes deploy` 命令](functions-core-tools-reference.md#func-kubernetes-deploy)。

### <a name="deploying-a-function-app-from-a-private-registry"></a>从专用注册表部署函数应用

以上流程也适用于专用注册表。  如果要从专用注册表拉取容器映像，请在运行 `func kubernetes deploy` 时包含引用 Kubernetes 机密（其中保存专用注册表凭据）的 `--pull-secret` 标志。

## <a name="removing-a-function-app-from-kubernetes"></a>将函数应用从 Kubernetes 中删除

部署之后，可以通过删除创建的关联 `Deployment`、`ScaledObject` 和 `Secrets` 来删除函数。

```command
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>从 Kubernetes 卸载 KEDA

可以通过以下其中一种方法从群集中删除 KEDA：

+ Azure Functions Core Tools：使用 [`func kubernetes remove` 命令](functions-core-tools-reference.md#func-kubernetes-remove)。

+ Helm：请参阅 [KEDA 网站上](https://keda.sh/docs/deploy/)的卸载步骤。

## <a name="supported-triggers-in-keda"></a>KEDA 中支持的触发器

KEDA 支持以下 Azure 函数触发器：

* [Azure 存储队列](functions-bindings-storage-queue.md)
* [Azure 服务总线队列](functions-bindings-service-bus.md)
* [Azure 事件/IoT 中心](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ 队列](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP 触发器支持

可以使用公开 HTTP 触发器的 Azure Functions，但 KEDA 不会直接管理它们。  可以利用 KEDA prometheus 触发器 [将 HTTP Azure Functions 从 1 个扩展到 n 个实例](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [使用自定义映像创建函数](functions-create-function-linux-custom-image.md)
* [在本地对 Azure Functions 进行编码和测试](functions-develop-local.md)
* [Azure 函数消耗计划的工作原理](functions-scale.md)

[func init]: functions-core-tools-reference.md#func-init