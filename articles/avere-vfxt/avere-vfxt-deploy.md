---
title: 部署 Avere vFXT for Azure
description: 了解如何使用 Azure 市场中提供的部署向导，通过 Avere vFXT for Azure 部署群集。
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 330fa1368c078f1e44dd436cd3bea6407c6f4cc6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124794645"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 群集

此过程详述如何使用 Azure 市场提供的部署向导。 此向导自动使用 Azure 资源管理器模板来部署群集。 在表单中输入参数并单击“创建”后，Azure 会自动完成以下任务：

* 创建群集控制器，该控制器是基本的 VM，其中包含部署和管理群集所需的软件。
* 设置资源组和虚拟网络基础结构，包括创建新元素。
* 创建群集节点 VM 并将其配置为 Avere 群集。
* 根据请求创建新的 Azure Blob 容器并将其配置为群集核心文件管理器。

按照本文档中的说明操作后，将获得虚拟网络、子网、群集控制器和 vFXT 群集，如下图所示。 此图显示了可选的 Azure Blob 核心文件管理器，其中包括新的 Blob 存储容器（在新的存储帐户中，未显示）、子网中的 Microsoft 存储的服务终结点。

![示意图中显示了包含 Avere 群集组件的三个同心矩形。 外层矩形的标签为“资源组”，包含标签为“Blob 存储(可选)”的一个六边形。 其中的下一个矩形的标签为“虚拟网络: 10.0.0.0/16”，未包含任何唯一组件。 最内层矩形的标签为“子网: 10.0.0.0/24”，包含标签为“群集控制器”的一个 VM、标签为“vFXT 节点(vFXT 群集)”的一个三 VM 堆栈，和标签为“服务终结点”的一个六边形。 有一个箭头将服务终结点（位于子网内）和 Blob 存储（位于子网和 vNet 外，在资源组中）连接起来。 此箭头穿越了子网和虚拟网络边界。](media/avere-vfxt-deployment.png)

在使用创建模板之前，请确保已具备以下必备项：  

* [新订阅](avere-vfxt-prereqs.md#create-a-new-subscription)
* [订阅所有者权限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [存储服务终结点（如果需要）](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed)- 对于使用现有虚拟网络并创建 blob 存储的部署，这是必填项

有关群集部署的步骤和计划的详细信息，请阅读[计划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)和[部署概述](avere-vfxt-deploy-overview.md)。

## <a name="create-the-avere-vfxt-for-azure"></a>创建 Avere vFXT for Azure

在 Azure 门户中访问创建模板，方法是：先搜索 Avere，然后选择“Avere vFXT for Azure ARM 模板”。

![显示 Azure 门户的浏览器窗口（带有面包屑导航“新建>市场>所有内容”）。 在“所有内容”页面上，搜索字段具有字词“avere”，并且第二个结果“Avere vFXT for Azure ARM 模板”以红色边框突出显示。](media/avere-vfxt-template-choose.png)

阅读“Avere vFXT for Azure ARM 模板”页面上的详细信息后，单击其“创建”按钮以开始操作。

![Azure 市场，显示部署模板的第一页](media/avere-vfxt-deploy-first.png)

模板包含四个步骤/页面 - 两个信息收集页面，一个验证步骤和一个确认步骤。

* 第一页收集群集控制器 VM 的设置。
* 第二页收集参数，这些参数用于创建群集以及诸如子网和存储之类的其他资源。
* 第三页汇总你的选择并验证配置。
* 第四页说明软件条款和条件，让你能够启动群集创建过程。

## <a name="page-one-parameters---cluster-controller-information"></a>第一页的参数 - 群集控制器信息

部署模板的第一页侧重于群集控制器。

![部署模板的第一页](media/avere-vfxt-deploy-1.png)

填充以下信息：

* **群集控制器名称** - 设置群集控制器 VM 的名称。

* 控制器用户名 - 设置群集控制器 VM 的根用户名。

* **身份验证类型** - 选择连接到控制器所需的密码或 SSH 公钥身份验证。 建议使用 SSH 公钥方法；如需帮助，请阅读[如何创建和使用 SSH 密钥](../virtual-machines/linux/ssh-from-windows.md)。

* **密码** 或 **SSH 公钥** - 必须在后续字段中提供 RSA 公钥或密码，具体取决于所选身份验证类型。 此凭据与以前提供的用户名配合使用。

* **订阅** - 选择 Avere vFXT 的订阅。

* **资源组** - 选择 Avere vFXT 群集的资源组，或者单击“新建”，然后输入新的资源组名称。

* **位置** - 选择群集和资源的 Azure 位置。

完成后，单击“确定”。

> [!NOTE]
> 如果希望群集控制器有一个面向公众的 IP 地址，请为群集创建一个新的虚拟网络，而不是选择一个现有的网络。 此设置在第二页上。

## <a name="page-two-parameters---vfxt-cluster-information"></a>第二页的参数 - vFXT 群集信息

可以使用部署模板的第二页来设置群集大小、节点类型、缓存大小、存储参数等设置。

![部署模板的第二页](media/avere-vfxt-deploy-2.png)

* Avere vFXT 群集节点计数 - 选择群集中的节点数。 至少 3 个节点，至多 20 个节点。

* **群集管理密码** - 创建用于群集管理的密码。 可以将此密码与用户名 ```admin``` 配合使用来登录到群集控制面板，你可以在控制面板中监视群集并配置群集设置。

* **Avere vFXT 群集名称** - 为群集提供唯一名称。

* 大小 - 此部分显示将用于群集节点的 VM 类型。 尽管只有一个建议选项，但“更改大小”链接会打开一个表，其中包含有关此实例类型的详细信息和一个指向价格计算器的链接。

* 每个节点的缓存大小 - 群集缓存分布在多个群集节点上，因此 Avere vFXT 群集上的总缓存大小为此大小乘以节点数。

  建议的配置：对于 Standard_E32s_v3 节点，请为每个节点使用 4 TB。

* 虚拟网络 - 定义一个新的虚拟网络来容纳群集，或选择满足[规划 Avere vFXT 系统](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)中所述先决条件的现有网络。

  > [!NOTE]
  > 如果你创建新的虚拟网络，则群集控制器会有一个公共 IP 地址，供你访问新的专用网络。 如果你选择现有虚拟网络，则配置的群集控制器没有公共 IP 地址。
  >
  > 如果群集控制器上有公开显示的 IP 地址，则虽然方便用户对 vFXT 群集的访问，但会有一些安全风险。
  >* 用户可以将群集控制器上的公共 IP 地址用作跳转主机，从专用子网外部连接到 Avere vFXT 群集。
  >* 如果你在控制器上没有公共 IP 地址，则你需要使用其他跳转主机、VPN 连接或 ExpressRoute 来访问群集。 例如，使用已配置了 VPN 连接的现有虚拟网络。
  >* 如果创建具有公共 IP 地址的控制器，则应使用网络安全组保护控制器 VM。 默认情况下，Avere vFXT for Azure 部署会创建一个网络安全组，仅限对具有公共 IP 地址的控制器的端口 22 进行入站访问。 可以通过锁定对 IP 源地址范围的访问来进一步保护系统，换句话说，只允许从你打算将其用于群集访问的计算机进行连接。

  新的虚拟网络还配置了 Azure Blob 存储的存储服务终结点，并锁定了网络访问控制，仅允许来自群集子网的 IP。

* 子网 - 选择一个子网或新建一个。

* 创建并使用 Blob 存储 - 选择“true”，创建新的 Azure Blob 容器并将其配置为新 Avere vFXT 群集的后端存储。 此选项还会在群集的资源组中创建一个新的存储帐户，并在群集子网中创建一个 Microsoft 存储服务终结点。
  
  如果提供现有虚拟网络，则在创建群集之前，它必须具有存储服务终结点。 （有关详细信息，请阅读[规划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)。）

  如果不希望创建新容器，请将此字段设置为 **false**。 在这种情况下，必须在创建群集后附加并配置存储。 有关说明，请参阅[配置存储](avere-vfxt-add-storage.md)。

* (新建)存储帐户 - 如果创建新的 Azure Blob 容器，请为新的存储帐户输入一个名称。

## <a name="validation-and-purchase"></a>验证和购买

第三页汇总配置并验证参数。 验证成功后，请检查摘要并单击“确定”按钮。

> [!TIP]
> 可以通过单击“确定”按钮旁的“下载模板和参数”链接来保存此群集的创建设置。 如果以后需要创建类似的群集（例如在灾难恢复场景中创建替代群集），则此信息可能很有帮助。 （请阅读[灾难恢复指南](disaster-recovery.md)来了解详细信息。）

![部署模板的第三页 - 验证](media/avere-vfxt-deploy-3.png)

第四页提供了使用条款以及指向隐私和定价信息的链接。

输入任何缺失的联系人信息，然后单击“创建”按钮以接受条款并创建 Avere vFXT for Azure 群集。

![部署模板的第四页 - 条款和条件、“创建”按钮](media/avere-vfxt-deploy-4.png)

群集部署需要 15-20 分钟。

## <a name="gather-template-output"></a>收集模板输出

Avere vFXT 模板在创建完群集以后会输出有关新群集的重要信息。

> [!TIP]
> 请务必复制模板输出中的管理 IP 地址。 需要该地址来管理群集。

若要查找该信息，请执行以下操作：

1. 转到群集控制器的资源组。

1. 在左侧单击“部署”，然后单击“microsoft-avere.vfxt-template”。

   ![资源组门户页面，其左侧选中“部署”，“部署名称”下的表中显示 microsoft-avere.vfxt-template](media/avere-vfxt-outputs-deployments.png)

1. 在左侧，单击“输出”。 复制每个字段中的值。

   ![输出页面，在标签右侧的字段中显示 SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK_RESOURCE_GROUP、NETWORK、SUBNET、SUBNET_ID、VSERVER_IPs 和 MGMT_IP 的值](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>后续步骤

现在，群集正在运行，并且你已知道其管理 IP 地址，可以[连接到群集配置工具](avere-vfxt-cluster-gui.md)了。

使用配置界面来自定义群集，包括以下设置任务：

* [启用支持](avere-vfxt-enable-support.md)
* [添加存储](avere-vfxt-add-storage.md)（如果需要）