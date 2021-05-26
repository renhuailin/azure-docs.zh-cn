---
title: 在启用了 Azure Arc 的 Kubernetes 群集上安装事件网格
description: 本文提供在启用了 Azure Arc 的 Kubernetes 群集上安装事件网格的步骤。
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 43879b5124c49ba3d0c4494e2d3a1eba95e617ff
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384906"
---
# <a name="install-event-grid-extension-on-azure-arc-enabled-kubernetes-cluster"></a>在启用了 Azure Arc 的 Kubernetes 群集上安装事件网格扩展
本文将指导你完成在[启用了 Azure Arc 的 Kubernetes](../../azure-arc/kubernetes/overview.md) 群集上安装事件网格的步骤。

为简洁起见，本文将“Kubernetes 扩展上的事件网格”称为“Kubernetes 上的事件网格”或简称为“事件网格”。

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]


## <a name="supported-kubernetes-distributions"></a>支持的 Kubernetes 分布
下面是受支持的 Kubernetes 发行版，可在其中部署和运行事件网格。

1. Azure AKS [支持的 Kubernetes 分布](../../aks/supported-kubernetes-versions.md)。
1. RedHat [OpenShift 容器平台](https://www.openshift.com/products/container-platform)。

根据[用户的反馈](https://feedback.azure.com/forums/909934-azure-event-grid)和[已启用 Azure Arc 的 Kubernetes 提供的支持](../../azure-arc/kubernetes/validation-program.md)，将载入更多的发行版。

## <a name="event-grid-extension"></a>事件网格扩展
在 Kubernetes 群集上安装事件网格服务实例的操作是创建 Azure Arc 群集扩展，这会同时部署“事件网格代理”和“事件网格操作员” 。 有关代理和操作员的功能的详细信息，请参阅 [Kubernetes 组件上的事件网格](concepts.md#event-grid-on-kubernetes-components)。 [Azure Arc 群集扩展](../../azure-arc/kubernetes/conceptual-extensions.md)功能使用 Azure 资源管理器 (ARM) 控制平面操作对部署到已启用 Azure Arc 的 Kubernetes 群集的事件网格提供生命周期管理。

> [!NOTE]
> 此服务的预览版本仅支持 Kubernetes 群集上的事件网格扩展的单个实例，因为事件网格扩展当前定义为群集范围扩展。 目前尚不支持事件网格的命名空间范围的部署（允许将多个实例部署到群集）。  有关扩展范围的详细信息，请参阅[创建扩展实例](../../azure-arc/kubernetes/extensions.md#create-extensions-instance)和搜索 ``scope``。

## <a name="prerequisites"></a>先决条件
在继续安装事件网格之前，请确保满足以下先决条件。 

1. 在其中一个[受支持的 Kubernetes 发行版](#supported-kubernetes-distributions)上运行的群集。
1. [Azure 订阅](https://azure.microsoft.com/en-us/free/)。
1. [PKI 证书](#pki-certificate-requirements)，用于与事件网格代理建立 HTTPS 连接。
1. [将群集连接到 Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。

## <a name="getting-support"></a>获取支持
如果遇到问题，请参阅[疑难解答](#troubleshooting)部分，以获取常见情况的帮助。 如果仍有问题，请[创建 Azure 支持请求](get-support.md#how-to-create-a-support-request)。

## <a name="pki-certificate-requirements"></a>PKI 证书要求
事件网格代理（服务器）为两种类型的客户端提供服务。 服务器身份验证是使用证书完成的。 客户端身份验证是使用基于客户端类型的证书或 SAS 密钥进行。

- 向事件网格代理提出控制平面请求的事件网格操作员使用证书进行身份验证。
- 向事件网格主题发布事件的事件网格发布者使用主题的 SAS 密钥进行身份验证。

为了与事件网格代理和事件网格操作员建立安全的 HTTPS 通信，我们在安装事件网格扩展期间使用 PKI 证书。 下面是这些 PKI 证书的一般要求：

1. 证书和密钥必须经过 [X.509](https://en.wikipedia.org/wiki/X.509) 证书和[隐私增强型邮件](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail) PEM 编码。
1. 要在安装过程中配置事件网格代理（服务器）证书，需要提供：
    1. CA 证书
    1. 公共证书
    1. 私钥
1. 若要配置事件网格操作员（客户端）证书，需要提供：
    1. CA 证书
    1. 公共证书
    1. 私钥

    发布客户端可以使用事件网格代理 CA 证书在将事件发布到主题时验证服务器。

    > [!IMPORTANT]
    > 虽然与客户端关联的域可能拥有由不同证书颁发机构颁发的多个公共证书，但 Kubernetes 上的事件网格只允许在安装事件网格时为客户端上传一个 CA 证书。 因此，事件网格操作员的证书应该由同一 CA 颁发（签名），以便证书链验证成功并成功建立 TLS 会话。
1. 配置服务器和客户端证书的公用名 (CN) 时，请确保它们不同于为证书颁发机构证书提供的 CN。

    > [!IMPORTANT] 
    > 对于早期的概念证明阶段，可以选择使用自签名证书，但是通常应购买并使用由证书颁发机构 (CA) 签名的恰当的 PKI 证书。

## <a name="install-event-grid-on-kubernetes-extension"></a>在 Kubernetes 扩展上安装事件网格

1. 在 Azure 门户上，（在顶部字段中）搜索 Azure Arc
1. 在“基础结构”部分的左侧菜单中选择“Kubernetes 群集” 
1. 在群集列表下，找到要安装事件网格的对象，然后选择它。 将显示群集的“概述”页。
1. 在左侧菜单的“设置”组中选择“扩展” 。
1. 选择“+ 添加”。 将显示一个页面，其中显示了可用的 Azure Arc Kubernetes 扩展。
1. 选择“事件网格”。
1. 在“具有 Azure Arc 的 Kubernetes 上的事件网格”页上选择“创建”。
1. 将显示“安装事件网格”页上的“基本信息”选项卡 。 “项目详细信息”部分显示只读订阅和资源组值，因为 Azure Arc 扩展部署在安装了它们的连接群集的同一 Azure 订阅和资源组下。
1. 在“事件网格扩展名称”字段中提供名称。 此名称应在部署到同一 Azure Arc 连接群集的其他 Azure Arc 扩展中是唯一的。
1. 对于“版本命名空间”，可能需要提供将在其中部署事件网格组件的 Kubernetes 命名空间的名称。 默认值为 eventgrid-system。 如果提供的命名空间不存在，则会为你创建它。
1. 在“事件网格代理”详细信息部分中，将显示服务类型。 事件网格代理是公开事件发送到的主题终结点的组件，它以 Kubernetes 服务类型 ClusterIP 的形式公开。 因此，分配给所有主题的 IP 使用为群集配置的专用 IP 空间。
1. 提供要用于代理的“存储类名称”，该名称受 Kubernetes 发行版支持。 例如，如果使用的是 AKS，则可以使用 `azurefile`，它使用 Azure 标准存储。 有关 AKS 支持的预定义存储类的详细信息，请参阅 [AKS 中的存储类](../../aks/concepts-storage.md#storage-classes)。 如果使用的是其他 Kubernetes 发行版，请参阅 Kubernetes 发行版文档，了解支持的预定义存储类或你可以提供自己的存储类的方式。
1. 存储大小。 默认值为 1 GiB。 确定存储大小时，请考虑使用引入速率。 通过事件网格代理中所有主题上事件的大小乘以发布速率（每秒事件数）来衡量的引入速率（以 MiB/秒为单位）是分配存储时的关键因素。 事件本质上是暂时性的，一旦事件被传送，这些事件就不会消耗任何存储空间。 虽然引入速率是存储使用的一个主要驱动因素，但不是唯一的。 保存主题和事件订阅配置的元数据也会消耗存储空间，但通常所需的存储空间要比事件网格所引入和传送的事件要少。
1. 内存限制。 默认值为 1 GiB。 
1. 内存请求。 默认值为 200 MiB。 此字段不可编辑。

    :::image type="content" source="./media/install-k8s-extension/basics-page.png" alt-text="安装事件网格扩展 -“基本信息”页":::
1. 在页面底部选择“下一步：配置”。
1. 启用 HTTP（非安全）通信。 如果希望在客户端与事件网格代理通信时使用非安全通道，请选中此框。

    > [!IMPORTANT]
    > 启用此选项将使所有与事件网格代理的通信使用 HTTP 作为传输方式。 因此，任何发布客户端和事件网格操作员都不会与事件网格代理安全地通信。 应仅在开发早期阶段使用此选项。
1. 如果未启用 HTTP 通信，请选择你采购的每个 PKI 证书文件，并满足 [PKI 证书要求](#pki-certificate-requirements)。

    :::image type="content" source="./media/install-k8s-extension/configuration-page.png" alt-text="安装事件网格扩展 -“配置”页":::
1. 选择页面底部的“下一步：监视”。
1. 通过选中此选项“启用指标”。 Kubernetes 上的事件网格使用 [Prometheus 阐释格式](https://prometheus.io/docs/instrumenting/exposition_formats/)公开主题和事件订阅的指标。

    :::image type="content" source="./media/install-k8s-extension/monitoring-page.png" alt-text="安装事件网格扩展 -“监视”页":::    
1. 选择“下一步：标记”以导航到“标记”页 。 如有必要，定义[标记](/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

    :::image type="content" source="./media/install-k8s-extension/tags-page.png" alt-text="安装事件网格扩展 -“标记”页":::
1. 在页面底部选择“查看 + 创建”。
1. 在“查看 + 创建”选项卡上，选择“创建”。 
    
    :::image type="content" source="./media/install-k8s-extension/review-create-page.png" alt-text="安装事件网格扩展 -“查看并创建”页":::   
    
    > [!IMPORTANT]
    > 事件网格的安装是异步操作，它在 Kubernetes 集群上运行的时间可能比在 Azure 门户上看到告知部署已完成的通知的时间要长。 看到“部署已完成”的通知后，至少等待 5 分钟，然后再尝试创建自定义位置（下一步）。 如果你有权访问 Kubernetes 群集，则可以在 bash 会话中执行以下命令，以验证事件网格代理和事件网格操作员 pod 是否处于“正在运行”状态（如果是，则表明安装已完成）：

    ```bash
    kubectl get pods -n \<release-namespace-name\>
    ```
    > [!IMPORTANT]
    > 在尝试部署事件网格主题之前，需要创建自定义位置。 若要创建自定义位置，可以在“部署已完成”通知显示 5 分钟后，选择底部的“上下文”页。 或者，可以使用 [Azure 门户](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ExtendedLocation%2FCustomLocations)创建自定义位置。
1. 部署成功后，你将能够在“扩展”页上看到一个条目，其中包含你向事件网格扩展提供的名称。

## <a name="troubleshooting"></a>故障排除

### <a name="azure-arc-connect-cluster-issues"></a>Azure Arc 连接群集问题

问题：在导航到 Azure Arc 并单击左侧菜单上的“Kubernetes 群集”时，展示的页面并没有显示我打算在其中安装事件网格的 Kubernetes 集群  。

解决方法：Kubernetes 群集未注册到 Azure。 按照[将现有 Kubernetes 群集连接到 Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md) 一文中的步骤进行操作。如果在此步骤中遇到问题，请向已启用 Azure Arc 的 Kubernetes 团队发出[支持请求](#getting-support)。

### <a name="event-grid-extension-issues"></a>事件网格扩展问题

问题：尝试安装“事件网格扩展”时，收到以下消息：“操作无效”- 事件网格的实例已安装在此连接的 Kubernetes 群集上 。 事件网格扩展的范围在群集级别，这意味着群集上只能安装一个实例。
    
说明：已安装事件网格。 预览版的事件网格仅支持在一个集群中部署一个事件网格扩展实例。


## <a name="next-steps"></a>后续步骤
请参阅快速入门[使用 Kubernetes 上的 Azure 事件网格将云事件路由到 Webhook](create-topic-subscription.md)。

