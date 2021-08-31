---
title: 将自承载网关部署到 Azure Kubernetes 服务 | Microsoft Docs
description: 了解如何将 Azure API 管理的自承载网关组件部署到 Azure Kubernetes 服务
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 06/11/2021
ms.author: apimpm
ms.openlocfilehash: c43f31be807d6a649cdd750ee15841a0ecbd7631
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300752"
---
# <a name="deploy-to-azure-kubernetes-service"></a>部署到 Azure Kubernetes 服务

本文提供将 Azure API 管理的自承载网关组件部署到 [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)的步骤。 有关将自承载网关部署到 Kubernetes 群集的详细说明，请参阅[操作指南](how-to-deploy-self-hosted-gateway-kubernetes.md)一文。

> [!NOTE]
> 你还可将自承载网关部署到[已启用 Azure Arc 的 Kubernetes 群集](how-to-deploy-self-hosted-gateway-azure-arc.md)作为[群集扩展](../azure-arc/kubernetes/extensions.md)。

## <a name="prerequisites"></a>先决条件

- [创建一个 Azure API 管理实例](get-started-create-service-instance.md)
- [创建 Azure Kubernetes 群集](../aks/kubernetes-walkthrough-portal.md)
- [在 API 管理实例中预配网关资源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>将自承载网关部署到 AKS

1. 选择“部署和基础结构”下的“网关” 。
2. 选择要部署的自承载网关资源。
3. 选择“部署”。
4. “令牌”文本框中的新令牌是使用默认“Expiry”和“Secret Key”值自动生成的。 根据需要调整其中的一个或两个值，然后选择“生成”以创建新令牌。
5. 确保在“部署脚本”下选择“Kubernetes”。 
6. 选择“部署”旁边的“<网关名称>.yml”文件链接以下载该文件。 
7. 根据需要调整 .yml 文件中的 `config.service.endpoint`、端口映射和容器名称。
8. 根据你的方案，你可能需要更改[服务类型](../aks/concepts-network.md#services)。 
    * 默认值为 `LoadBalancer`，它是外部负载均衡器。 
    * 可以使用[内部负载均衡器](../aks/internal-lb.md)将对自承载网关的访问限制为仅限内部用户。 
    * 以下示例使用 `NodePort`。
1. 选择“部署”文本框右侧的“复制”图标，将 `kubectl` 命令保存到剪贴板。 
1. 将该命令粘贴到终端（或命令）窗口。 该命令要求下载的环境文件位于当前目录中。
    ```console
        kubectl apply -f <gateway-name>.yaml
    ```
1. 执行命令。 此命令指示 AKS 群集：
    * 使用从 Microsoft Container Registry 下载的自承载网关映像运行容器。 
    * 配置容器以公开 HTTP (8080) 和 HTTPS (443) 端口。
1. 运行以下命令来检查网关 pod 是否正在运行。 Pod 名称将有所不同。
    ```console
    kubectl get pods
    NAME                                   READY     STATUS    RESTARTS   AGE
    contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
    ```
1. 运行以下命令来检查网关服务是否正在运行。 你的服务名称和 IP 地址与此不同。
    ```console
    kubectl get services
    NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
    ```
1. 返回 Azure 门户，确认刚刚部署的网关节点是否报告正常状态。

> [!TIP]
> 使用 <code>kubectl logs <gateway-pod-name></code> 命令查看自承载网关日志的快照。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)。
* 了解[如何将 API 管理自承载网关部署到已启用 Azure Arc 的 Kubernetes 群集](how-to-deploy-self-hosted-gateway-azure-arc.md)。
* 了解有关 [Azure Kubernetes 服务](../aks/intro-kubernetes.md)的详细信息。
* 了解[如何在云中配置和保留日志](how-to-configure-cloud-metrics-logs.md)。
* 了解[如何在本地配置和保留日志](how-to-configure-local-metrics-logs.md)。
