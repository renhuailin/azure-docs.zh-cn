---
title: 从 Azure 门户部署 Azure Arc 数据控制器 | 直接连接模式
description: 说明如何在直接连接模式下从 Azure 门户部署数据控制器。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 6ac96cb0420ecdba934b364c185bedf01f9f4321
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822641"
---
#  <a name="create-azure-arc-data-controller-from-azure-portal---direct-connectivity-mode"></a>从 Azure 门户创建 Azure Arc 数据控制器 - 直接连接模式

本文介绍如何在此功能的当前预览版中，在直接连接模式下部署 Azure Arc 数据控制器。 

## <a name="complete-prerequisites"></a>满足先决条件

在开始之前，请先验证是否已满足[部署数据控制器 - 直接连接模式 - 先决条件](create-data-controller-direct-prerequisites.md)中的先决条件。

## <a name="deploy-azure-arc-data-controller"></a>部署 Azure Arc 数据控制器

你可以采用以下方法之一从 Azure 门户启动 Azure Arc 数据控制器创建流：

- 在 Azure 门户的搜索栏中搜索“Azure Arc 数据控制器”，然后选择“+ 创建”
- 在已启用 Azure Arc 的 Kubernetes 群集的“概述”页中，
  - 选择“设置”下面的“扩展”。
  - 从“扩展概述”页选择“添加”，然后选择“Azure Arc 数据控制器”
  - 从 Azure Arc 数据控制器市场库选择“创建”
  
其中任一操作都应将你转到创建流的“Azure Arc 数据控制器先决条件”页。

- 请确保选中“启用 Azure Arc 的 Kubernetes 群集(直接连接模式)”选项。 选择“下一步: 数据控制器详细信息”
- 在“数据控制器详细信息”页中：
  - 选择要将 Azure Arc 数据控制器投影到的 Azure 订阅和资源组。
  - 在“名称”中输入数据控制器的名称。
  - 选择预先创建的“自定义位置”，或选择“新建”以创建新的自定义位置。 如果选择创建新的自定义位置，请输入新自定义位置的名称，从下拉列表中选择已启用 Azure Arc 的 Kubernetes 群集，然后输入要与新自定义位置关联的命名空间，最后在“创建新的自定义位置”窗口中选择“创建”。 详细了解[自定义位置](../kubernetes/conceptual-custom-locations.md)
  - **Kubernetes 配置** - 从下拉列表中选择与 Kubernetes 分发最匹配的 Kubernetes 配置模板。 如果选择使用自己的设置或拥有要使用的自定义配置文件，请从下拉列表中选择“自定义模板”选项。 在右侧打开的边栏选项卡中，输入 Docker 凭据、存储库信息、映像标记、映像拉取策略、基础结构类型、数据存储设置、日志及其大小、服务类型以及控制器和管理代理的端口的详细信息。 提供所有必填信息后，选择“应用”。 还可以从边栏选项卡顶部选择“上传模板(JSON)”，选择上传自己的模板文件。 如果使用自定义设置并要下载这些设置的副本，请使用“下载此模板(JSON)”来完成此操作。 详细了解[自定义配置文件](create-custom-configuration-template.md)。
  - 选择适合你的环境的“服务类型”。
  - **管理员帐户** - 输入数据控制器登录名和密码的凭据
  - **服务主体** - 输入要使用的服务主体帐户的客户端 ID、租户 ID 和客户端密码信息。
  - 提供所有必填信息后，选择“下一步: 其他设置”按钮以继续操作。
- 在“其他设置”页中：
  - 如果选择自动将日志上传到 Azure Log Analytics，请输入 Log Analytics 工作区 ID 和 Log Analytics 共享访问密钥
  - 如果选择不自动将日志上传到 Azure Log Analytics，请取消选中“启用日志上传”复选框。
  - 选择“下一步: 标记”继续。
- 在“标记”页中，输入标记的名称和值，然后选择“下一步: 查看 + 创建”。
- 在“查看 + 创建”页中，查看部署摘要。 确保所有设置都正确，然后选择“创建”以开始部署 Azure Arc 数据控制器。

## <a name="monitor-the-creation-from-azure-portal"></a>从 Azure 门户监视创建过程

在上一步中选择“创建”按钮后将启动 Azure 部署概述页，其中显示 Azure Arc 数据控制器的部署进度。

## <a name="monitor-the-creation-from-your-kubernetes-cluster"></a>从 Kubernetes 群集监视创建过程

可以采用以下方式监视 Azure Arc 数据控制器的部署进度：

- 从群集运行 ```kubectl get crd ``` 检查是否创建了 CRD  
- 从群集运行 ```kubectl get ns``` 检查是否创建了命名空间
- 运行 ```az customlocation list --resource-group <resourcegroup> -o table``` 检查是否创建了自定义位置 
- 运行 ```kubectl get pods -ns <namespace>``` 检查 Pod 部署的状态

## <a name="next-steps"></a>后续步骤

[创建已启用 Azure Arc 的 SQL 托管实例](create-sql-managed-instance.md)

[创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组](create-postgresql-hyperscale-server-group.md)
