---
title: 部署 Azure IoT Edge 工作负载（预览版）
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 部署 Azure IoT Edge 工作负载
keywords: Kubernetes, Arc, Azure, K8s, 容器
ms.openlocfilehash: c352455b69360df0b26f5aac38fc40ccb30fb9de
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650407"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>部署 Azure IoT Edge 工作负载（预览版）

## <a name="overview"></a>概述

Azure Arc 和 Azure IoT Edge 轻松地补充各自的功能。 

Azure Arc 为群集操作员提供配置群集的基础组件的机制，并应用和强制实施群集策略。 

Azure IoT Edge 允许应用程序操作员通过方便的云引入和双向通信基元以大规模方式远程部署和管理工作负荷。 

下图演示了 Azure Arc 和 Azure IoT Edge 的关系：

![IoT Arc 配置](./media/edge-arc.png)

## <a name="pre-requisites"></a>先决条件

* [注册 IoT Edge 设备](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device)并[部署模拟温度传感器模块](../../iot-edge/quickstart-linux.md#deploy-a-module)。 请注意以下 *yaml* 的设备连接字符串。

* 使用 [Kubernetes 的 IoT Edge 支持 ](https://aka.ms/edgek8sdoc)通过 Azure Arc 的 Flux 运算符来部署它。

* 下载 IoT Edge Helm 的 [*yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) 文件，并将 `deviceConnectionString` 文件末尾的占位符替换为前面记下的连接字符串。 根据需要设置任何其他支持的图表安装选项。 为 IoT Edge 工作负荷创建一个命名空间，并在其中生成机密：

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  还可以使用 [群集配置示例](./tutorial-use-gitops-connected-cluster.md)远程设置。

## <a name="connect-a-cluster"></a>连接群集

使用 `az` Azure CLI `connectedk8s` 扩展将 Kubernetes 群集连接到 Azure Arc：

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>创建 IoT Edge 的配置

[示例 Git](https://github.com/veyalla/edgearc)存储库指向 IoT Edge Helm 图表，并引用必备组件部分中创建的机密。

使用 `az` Azure CLI `k8s-configuration` 扩展创建将连接的群集链接到 Git 存储库的配置：

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

几分钟后，会看到部署到群集命名空间中的 IoT Edge 工作负荷模块 `iotedge` 。 

查看 `SimulatedTemperatureSensor` 该命名空间中的 pod 日志，查看正在生成的示例值。 也可使用 [Visual Studio Code 的 Azure IoT Toolkit 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看到达 IoT 中心的消息。

## <a name="cleanup"></a>清理

使用以下内容删除配置：

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>后续步骤

[使用 Azure Policy 控制群集配置](./use-azure-policy.md)
