---
title: 将 Micro Focus Enterprise Server 5.0 部署到 AKS | Microsoft Docs
description: 使用 Azure 虚拟机 (VM) 上的 Micro Focus 开发和测试环境重新托管 IBM z/OS 大型机工作负载。
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 58ba530e434a9dc141ba8cb98120f6325eb06f7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950467"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>将 Micro Focus Enterprise Server 5.0 部署到 AKS

在另一篇[文章](./run-enterprise-server-container.md)中，我已概述了在 Docker 容器中运行 Micro Focus Enterprise Server 5.0 的步骤。 作为那篇文章的延续，接下来我将介绍如何更进一步，将你创建的那个 Docker 映像部署到 Azure Kubernetes 服务 (AKS)。

Azure Kubernetes 服务是基于 Kubernetes 的托管业务流程服务。 使用该服务可以在容器主机群集中部署、缩放和管理 Docker 容器（以及其他基于容器的应用程序）。

此过程由三个步骤组成。 你需要：

1.  创建一个用于存储 Docker 映像的 Azure 容器注册表。

2.  创建一个用于运行 Docker 映像的 Azure Kubernetes 群集。

3.  运行该应用程序。

这样，你便可以真正利用云平台，在 Azure 中横向扩展（和纵向缩减）大型机现代化工作负载。

准备好了吗？ 让我们开始吧！

## <a name="create-the-azure-container-registry"></a>创建 Azure 容器注册表

在 Azure 门户的左上角选择“创建资源”。 在市场仪表板中，依次选择“容器”、“容器注册表”。  随后你将转到“创建容器注册表”窗格，需在其中填写“注册表名称”、“Azure 订阅”、“资源组”和“位置”。     “注册表名称”需要解析，因此该名称必须唯一。 选择在前一篇博客文章中使用的“资源组”，以及同一个对应的“位置”。  对“管理员用户”选择“启用”，对“SKU”选择“基本”。    填写所有内容后，选择“创建”。

![“创建容器注册表”界面](media/deploy-image-1.png)

部署注册表后，选择“转到资源”。 随后你将转到 Azure 容器注册表的主边栏选项卡。 此处有一项很有用的功能，即“快速入门”菜单选项。 选择该选项可以看到有关向注册表推送映像以及从注册表拉取映像所需执行的操作的说明。 让我们体验这些操作吧：

1.  **安装 Docker** – 无需担心此操作，因为它已经完成。

2.  **运行“hello – world”基础映像** – 同样，该操作也是无需你执行的，不过可以尽情试试此操作。

3.  **登录到容器注册表** – 需要从虚拟机 (VM) 执行此操作。 将命令复制到剪贴板或记事本。

    对于我创建的注册表，命令为：**docker login acrmf50.azurecr.io**

4.  **推送到注册表** – 登录到 VM 后，也需要对 Micro Focus 映像执行此操作。

5.  **从注册表拉取** – 与本演练不相关，但如果你需要运行另一个 Docker 映像，则最好是了解此操作。

在离开门户之前，需要获取注册表的凭据，以便能够登录。 退出“快速入门”边栏选项卡，然后从注册表菜单中选择“访问密钥”。  将“用户名”和一个“密码”（有两个）复制到剪贴板或记事本。  稍后需要使用此用户名和密码登录。

了解需要执行哪些操作后，登录到 VM。

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>通过 RDP 连接到用于创建 Docker 映像的虚拟机

由于已在 Windows 2016 服务器上创建了 Docker 映像，因此需要登录到该 VM。 在此 VM 中，可将映像推送到刚刚创建的 Azure 容器注册表。 在 Azure 门户中导航到该 VM，然后依次选择“概述”、“连接”。  这会通过远程桌面协议 (RDP) 将你连接到该 VM。 需要使用创建 VM 时所用的凭据。

## <a name="log-in-and-push-the-image-to-the-registry"></a>登录并将映像推送到注册表

登录后，打开命令提示符并启动以下 Docker 命令：

-   **docker images** – 此命令显示 VM 上当前已安装的所有映像的列表。 请记下 **microfocus/es-acctdemo**，因为这是我们所要操作的映像。

-   **docker login acrmf50.azurecr.io** – 此处的正确格式是 *docker login \<registry name\>* 。 请替换为在你创建注册表时使用的任何名称。

    -   需要使用从 Azure 门户复制的“用户名”和“密码”。  显示的内容应与下图类似。

    ![“管理员命令提示符”的屏幕截图](media/deploy-image-2.png)

-   **docker tag microfocus/es-acctdemo acrmf50.azurecr.io/es-acctdemo** – 此命令使用存储库的名称来标记相应的映像。 **注意**：如果名称 \<microfocus/es-acctdemo\> 不起作用，请尝试使用完整映像 ID。 执行该命令后，键入 **docker images –no-trunc**。 应会看到下图所示的内容。 可以发现，已正确标记该映像。

    ![“选择管理员命令提示符”屏幕](media/deploy-image-3.png)

-   **docker push acrmf50.azurecr.io/es-acctdemo** – 此命令启动实际推送到存储库的操作。 由于大小为 15 GB，因此需要几分钟时间才能完成运行。 如果一切正常，应会看到下图所示的内容。

    ![“管理员命令提示符”屏幕](media/deploy-image-4.png)

现在请返回到 Azure 门户，具体而言，是返回到“存储库”。 在“存储库”的菜单中选择“存储库”，然后应会看到“es-acctdemo”已列出。   现在创建 AKS 群集。

## <a name="create-the-azure-kubernetes-aks-cluster"></a>创建 Azure Kubernetes (AKS) 群集

在 Azure 门户中选择“创建资源”，然后在“市场”菜单中选择“容器/Kubernetes 服务”。   接下来，需要填写“创建 Kubernetes 群集”边栏选项卡。 请确保将群集保留在过去一直使用的同一区域和资源组中。 对于余下的字段，可以接受默认值，但“节点计数”除外，其值只需为 1。 完成操作后，选择“查看 + 创建”。

![“创建 Kubernetes 群集”屏幕](media/deploy-image-5.png)

完成后，部署会将“Kubernetes 服务”项目放到你在边栏选项卡上选择的“资源组”中。  但是，在部署过程中，将为实际群集创建其自己的资源组。 如果在左侧菜单中选择“资源组”，可以根据命名约定查找。 下面是我的映像 – 列表中的最后一个。

![“资源组”的屏幕截图](media/deploy-image-6.png)

**运行映像**

现在，让我们拉取映像并在 AKS 中运行该映像。 在 Azure 门户中执行此操作的最简单方法是使用 Cloud Shell。 在 Azure 门户的右上角可以找到相应的图标。 请注意，对于本演练，我使用的是 Bash Shell。

![“Cloud Shell”图标的屏幕截图](media/deploy-image-7.png)

加载 shell 后，键入以下命令：

**kubectl run es-acctdemo --image acrmf50.azurecr.io/es-acctdemo --port=9040**

此命令从 **acrmf50.azurecr.io** 存储库中提取映像，并将其载入 AKS。 然后，它会运行该映像，并且端口 9040 处于打开状态。 你可能还记得，此端口就是你为 Docker 映像打开的端口。 访问 Enterprise Server 需要此端口。

Kubernetes 应会通过一条指出已创建部署的消息做出响应。

![部署消息的屏幕截图](media/deploy-image-8.jpg)

若要确定容器是否真正运行，请键入：**kubectl get pods**。

应会看到 pod es-acctdemo 正在运行，如下图所示。

![正在运行的 pod es-acctdemo 的屏幕截图](media/deploy-image-9.png)

祝贺你！ 现在，Enterprise Server 正在 Azure Kubernetes 服务中运行。 在下一篇文章中，我将介绍如何访问 Enterprise Server 管理控制台，以及如何利用 Kubernetes 来横向扩展部署。
