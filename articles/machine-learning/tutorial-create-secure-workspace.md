---
title: 创建安全的工作区
titleSuffix: Azure Machine Learning
description: 在安全的虚拟网络中创建 Azure 机器学习工作区和所需的 Azure 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: jhirono
ms.author: larryfr
author: blackmist
ms.date: 09/15/2021
ms.topic: how-to
ms.custom: subject-rbac-steps
ms.openlocfilehash: ad81535a8287a4b89f978c4c2523b664375d23d6
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428723"
---
# <a name="how-to-create-a-secure-workspace"></a>创建安全工作区的方法

本文介绍如何创建并连接到安全的 Azure 机器学习工作区。 安全的工作区使用 Azure 虚拟网络围绕由 Azure 机器学习所使用的资源创建安全边界。 

在本教程中，你将完成以下任务：

> [!div class="checklist"]
> * 创建 Azure 虚拟网络 (VNet) 以确保虚拟网络中服务之间的通信安全。
> * 在 VNet 背后创建 Azure 存储帐户（Blob 和文件）。 此服务用作工作区的默认存储。
> * 在 VNet 背后创建 Azure Key Vault。 此服务用于存储工作区使用的机密。 例如，访问存储帐户所需的安全信息。
> * 创建 Azure 容器注册表 (ACR)。 此服务用作 Docker 映像的存储库。 Docker 映像提供在训练机器学习模型或将已训练的模型部署为终结点时所需的计算环境。
> * 创建 Azure 机器学习工作区。
> * 创建跳转盒。 跳转盒是 VNet 背后的 Azure 虚拟机。 由于 VNet 限制从公共网络访问，因此跳转盒用作连接到 VNet 背后的资源的一种方式。
> * 将 Azure 机器学习工作室配置为在 VNet 背后工作。 工作室提供适用于 Azure 机器学习的 Web 接口。
> * 创建 Azure 机器学习计算群集。 在云中训练机器学习模型时会用到计算群集。 在 Azure 容器注册表位于 VNet 背后的配置中，它还用于生成 Docker 映像。
> * 连接到跳转盒并使用 Azure 机器学习工作室。

如果环境满足先决条件，并且你对使用 ARM 模板很熟悉，则还可选择“部署到 Azure”按钮来完成本教程中的前五个步骤。 可以继续从[连接到工作区](#connect-to-the-workspace)中进行读取。

[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.machinelearningservices%2Fmachine-learning-workspace-vnet%2Fazuredeploy.json)
[![部署到 Azure US Gov](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazuregov.svg?sanitize=true)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.machinelearningservices%2Fmachine-learning-workspace-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

* 熟悉 Azure 虚拟网络和 IP 网络
* 虽然本文中的大部分步骤使用的是 Azure 门户或 Azure 机器学习工作室，但某些步骤会使用适用于机器学习的 Azure CLI 扩展。

## <a name="create-a-virtual-network"></a>创建虚拟网络

若要创建虚拟网络，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择左上角的门户菜单。 从菜单中选择“+ 创建资源”，然后在搜索字段中输入“虚拟网络” 。 选择“虚拟网络”条目，然后选择“创建” 。


    :::image type="content" source="./media/tutorial-create-secure-workspace/create-resource-search-vnet.png" alt-text="创建资源 的 UI 搜索":::

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-resource-vnet.png" alt-text="虚拟网络创建":::

1. 从“基本信息”选项卡中，选择要用于此资源的 Azure 订阅，然后选择或新建资源组  。 在“实例详细信息”下，为虚拟网络输入一个易记名称，并选择要在其中创建该网络的区域  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-basics.png" alt-text="基本虚拟网络配置的屏幕截图":::

1. 选择“IP 地址”选项卡。默认设置应类似于下图：

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-ip-address-default.png" alt-text="默认 IP 地址屏幕截图":::

    使用以下步骤配置 IP 地址并配置子网以对资源进行训练和评分：

    > [!TIP]
    > 虽然可以针对所有 Azure ML 资源使用一个子网，但本文中的步骤演示了如何创建两个子网来分隔资源的训练和评分。
    >
    > 工作区和其他依赖项服务将分配到训练子网中。 资源也可以在其他子网中使用它们，例如评分子网。

    1. 查看默认的“IPv4 地址空间”值。 在屏幕截图中，此值为 172.17.0.0/16。 此默认值在你设置时可能会有所不同。 你可以使用不同的值，但本教程中的剩余步骤都基于值 172.17.0.0/16 进行。
    1. 选择“默认”子网，然后选择“删除子网” 。
    
        :::image type="content" source="./media/tutorial-create-secure-workspace/delete-default-subnet.png" alt-text="删除默认子网的屏幕截图":::

    1. 若要创建子网以包含用于训练的工作区、依赖项服务和资源，请选择“+ 添加子网”，并为子网使用以下值：
        * __子网名称__：训练
        * __子网地址范围__：172.17.0.0/24

        :::image type="content" source="./media/tutorial-create-secure-workspace/vnet-add-training-subnet.png" alt-text="训练子网的屏幕截图":::

        > [!TIP]
        > 如果计划使用服务终结点将 Azure 存储帐户、Azure Key Vault 和 Azure 容器注册表添加到 VNet，则在“服务”下选择以下选项：
        > * __Microsoft.Storage__
        > * __Microsoft.KeyVault__
        > * __Microsoft.ContainerRegistry__
        >
        > 如果你计划使用专用终结点将这些服务添加到 VNet，则无需选择这些项。 本文中的步骤将专用终结点用于这些服务，因此在执行这些步骤时无需选择它们。

    1. 若要为用于对模型进行评分的计算资源创建子网，请再次选择“+ 添加子网”，并使用以下值：
        * __子网名称__：评分
        * __子网地址范围__：172.17.1.0/24

        :::image type="content" source="./media/tutorial-create-secure-workspace/vnet-add-scoring-subnet.png" alt-text="评分子网的屏幕截图":::

        > [!TIP]
        > 如果计划使用服务终结点将 Azure 存储帐户、Azure Key Vault 和 Azure 容器注册表添加到 VNet，则在“服务”下选择以下选项：
        > * __Microsoft.Storage__
        > * __Microsoft.KeyVault__
        > * __Microsoft.ContainerRegistry__
        >
        > 如果你计划使用专用终结点将这些服务添加到 VNet，则无需选择这些项。 本文中的步骤将专用终结点用于这些服务，因此在执行这些步骤时无需选择它们。

1. 选择“安全”。 对于“BastionHost”，选择“启用” 。 [Azure Bastion](../bastion/bastion-overview.md) 可提供一种安全的方式访问 VM 跳转盒，在后续的步骤中，你将在 VNet 中创建此跳转盒。 对于剩余字段，请使用以下值：

    * __Bastion 名称__：此 Bastion 实例的唯一名称
    * __AzureBastionSubnetAddress space__：172.17.2.0/27
    * __公共 IP 地址__：创建新的公共 IP 地址。

    将其他字段保留默认值。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-bastion.png" alt-text="Bastion 配置的屏幕截图":::

1. 选择“查看 + 创建”。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-ip-address-final.png" alt-text="显示“查看 + 创建”按钮的屏幕截图":::

1. 确认信息无误，然后选择“创建”。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-review.png" alt-text="“查看”页的屏幕截图":::

## <a name="create-a-storage-account"></a>创建存储帐户

1. 在 [Azure 门户](https://portal.azure.com)中，选择左上角的门户菜单。 从菜单中选择“+ 创建资源”，然后输入“存储帐户” 。 选择“存储帐户”条目，然后选择“创建” 。
1. 在“基本信息”选项卡中，选择之前用于虚拟网络的“订阅”、“资源组”和“区域”   。 输入唯一的“存储帐户名称”，并将“冗余”设置为“本地冗余存储(LRS)”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-storage.png" alt-text="存储帐户基本配置的屏幕截图":::

1. 在“网络”选项卡中，选择“专用终结点”，然后选择“+ 添加专用终结点”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-enable-private-endpoint.png" alt-text="添加 Blob 专用网络的 UI":::

1. 在“创建专用终结点”窗体中，使用以下值：
    * __订阅__：包含之前已创建资源的同一 Azure 订阅。
    * __资源组__：包含之前已创建资源的同一 Azure 资源组。
    * __位置__：包含之前已创建资源的同一 Azure 区域。
    * __名称__：此专用终结点的唯一名称。
    * __目标子资源__：blob
    * __虚拟网络__：前面已创建的虚拟网络。
    * __子网__：训练 (172.17.0.0/24)
    * __专用 DNS 集成__：是
    * __专用 DNS 区域__：privatelink.blob.core.windows.net

    选择“确定”以创建专用终结点。

1. 选择“查看 + 创建”。 确认信息无误，然后选择“创建”。

1. 创建存储帐户后，选择“转到资源”：

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-go-to-resource.png" alt-text="转到新存储资源":::

1. 在左侧导航栏中，选择“网络”，然后选择“专用终结点连接”选项卡 >“+ 专用终结点”  ：

    > [!NOTE]
    > 在前面的步骤中为 Blob 存储创建一个专用终结点时，还必须为文件存储创建一个专用终结点。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-networking.png" alt-text="存储帐户网络的 UI":::

1. 在“创建专用终结点”窗体中，使用为之前的资源使用的同一“订阅”、“资源组”和“区域”   。 输入唯一名称。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint.png" alt-text="添加文件专用终结点的 UI":::

1. 选择“下一步: 资源”，然后将“目标子资源”设置为“文件”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint-resource.png" alt-text="添加“文件”子资源":::

1. 选择“下一步: 配置”，然后使用以下值：
    * __虚拟网络__：前面创建的虚拟网络
    * __子网__：训练
    * __与专用 DNS 区域集成__：是
    * __专用 DNS 区域__：privatelink.file.core.windows.net

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint-config.png" alt-text="配置文件专用终结点的 UI":::

1. 选择“查看 + 创建”  。 确认信息无误，然后选择“创建”。

> [!TIP]
> 如果计划在管道中使用 [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md)，还必须配置专用终结点目标队列和表子资源 。 ParallelRunStep 在幕后使用队列和表进行任务调度和分派。

## <a name="create-a-key-vault"></a>创建 key vault

1. 在 [Azure 门户](https://portal.azure.com)中，选择左上角的门户菜单。 从菜单中选择“+ 创建资源”，然后输入“Key Vault” 。 选择“Key Vault”条目，然后选择“创建” 。
1. 在“基本信息”选项卡中，选择之前用于虚拟网络的“订阅”、“资源组”和“区域”   。 输入唯一的密钥保管库名称。 将其他字段保留默认值。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-key-vault.png" alt-text="创建新的密钥保管库":::

1. 在“网络”选项卡中，选择“专用终结点”，然后选择“+ 添加”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/key-vault-networking.png" alt-text="密钥保管库网络":::

1. 在“创建专用终结点”窗体中，使用以下值：
    * __订阅__：包含之前已创建资源的同一 Azure 订阅。
    * __资源组__：包含之前已创建资源的同一 Azure 资源组。
    * __位置__：包含之前已创建资源的同一 Azure 区域。
    * __名称__：此专用终结点的唯一名称。
    * __目标子资源__：保管库
    * __虚拟网络__：前面已创建的虚拟网络。
    * __子网__：训练 (172.17.0.0/24)
    * __专用 DNS 集成__：是
    * __专用 DNS 区域__：privatelink.vaultcore.azure.net

    选择“确定”以创建专用终结点。

    :::image type="content" source="./media/tutorial-create-secure-workspace/key-vault-private-endpoint.png" alt-text="配置密钥保管库专用终结点":::

1. 选择“查看 + 创建”。 确认信息无误，然后选择“创建”。

## <a name="create-a-container-registry"></a>创建容器注册表

1. 在 [Azure 门户](https://portal.azure.com)中，选择左上角的门户菜单。 从菜单中选择“+ 创建资源”，然后输入“容器注册表” 。 选择“容器注册表”条目，然后选择“创建” 。
1. 在“基本信息”选项卡中，选择之前用于虚拟网络的“订阅”、“资源组”和“位置”   。 输入唯一的“注册表名称”，并将“SKU”设置为“高级”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-container-registry.png" alt-text="创建容器注册表":::

1. 在“网络”选项卡中，选择“专用终结点”，然后选择“+ 添加”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-networking.png" alt-text="容器注册表网络":::

1. 在“创建专用终结点”窗体中，使用以下值：
    * __订阅__：包含之前已创建资源的同一 Azure 订阅。
    * __资源组__：包含之前已创建资源的同一 Azure 资源组。
    * __位置__：包含之前已创建资源的同一 Azure 区域。
    * __名称__：此专用终结点的唯一名称。
    * __目标子资源__：注册表
    * __虚拟网络__：前面已创建的虚拟网络。
    * __子网__：训练 (172.17.0.0/24)
    * __专用 DNS 集成__：是
    * __专用 DNS 区域__：privatelink.azurecr.io

    选择“确定”以创建专用终结点。

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-private-endpoint.png" alt-text="配置容器注册表专用终结点":::

1. 选择“查看 + 创建”。 确认信息无误，然后选择“创建”。
1. 创建容器注册表后，选择“转到资源”。

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-go-to-resource.png" alt-text="选择“转到资源”":::

1. 在页面左侧，选择“访问密钥”，然后启用“管理员用户” 。 如果要将虚拟网络中的 Azure 容器注册表与 Azure 机器学习结合使用，此设置则是必需的。

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-admin-user.png" alt-text="管理员用户切换开关的屏幕截图":::

## <a name="create-a-workspace"></a>创建工作区

1. 在 [Azure 门户](https://portal.azure.com)中，选择左上角的门户菜单。 从菜单中选择“+ 创建资源”，然后输入“机器学习” 。 选择“机器学习”条目，然后选择“创建” 。

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-create.png" alt-text="{alt-text}":::

1. 在“基本信息”选项卡中，选择之前用于虚拟网络的“订阅”、“资源组”和“区域”   。 对其他字段使用以下值：
    * __工作区名称__：你的工作区的唯一名称。
    * __存储帐户__：选择前面创建的存储帐户。
    * __密钥保管库__：选择前面创建的密钥保管库。
    * __应用程序见解__：使用默认值。
    * __容器注册表__：使用前面创建的容器注册表。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-machine-learning-workspace.png" alt-text="基本的工作区配置":::

1. 在“网络”选项卡中，选择“专用终结点”，然后选择“+ 添加”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-workspace-networking.png" alt-text="工作区网络":::

1. 在“创建专用终结点”窗体中，使用以下值： 
    * __订阅__：包含之前已创建资源的同一 Azure 订阅。
    * __资源组__：包含之前已创建资源的同一 Azure 资源组。
    * __位置__：包含之前已创建资源的同一 Azure 区域。
    * __名称__：此专用终结点的唯一名称。
    * __目标子资源__：amlworkspace
    * __虚拟网络__：前面已创建的虚拟网络。
    * __子网__：训练 (172.17.0.0/24)
    * __专用 DNS 集成__：是
    * __专用 DNS 区域__：将两个专用 DNS 区域保留为默认值 privatelink.api.azureml.ms 和 privatelink.notebooks.azure.net 。

    选择“确定”以创建专用终结点。

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-workspace-private-endpoint.png" alt-text="工作区专用网络配置的屏幕截图":::

1. 选择“查看 + 创建”。 确认信息无误，然后选择“创建”。
1. 创建工作区后，选择“转到资源”。
1. 在左侧的“设置”部分中，选择“专用终结点连接”，然后选择“专用终结点”列中的链接  ：

    :::image type="content" source="./media/tutorial-create-secure-workspace/workspace-private-endpoint-connections.png" alt-text="工作区专用终结点连接的屏幕截图":::

1. 在专用终结点信息出现后，从页面左侧选择“DNS 配置”。 保存此页上的 IP 地址和完全限定的域名 (FQDN) 信息，因为稍后将用到这些信息。

    :::image type="content" source="./media/tutorial-create-secure-workspace/workspace-private-endpoint-dns.png" alt-text="IP 和 FQDN 条目的屏幕截图":::

> [!IMPORTANT]
> 在可以完全使用工作区之前，还需要执行一些配置步骤。 但是，执行这些步骤需要连接到工作区。

## <a name="enable-studio"></a>启用工作室

Azure 机器学习工作室是基于 Web 的应用程序，使你能够轻松管理工作区。 但是，需要先对它进行一些额外的配置，然后才能将它与 VNet 中受保护的资源结合使用。 使用以下步骤启用工作室：

1. 当使用具有专用终结点的 Azure 存储帐户时，将工作区的服务主体添加为存储专用终结点的“读者”。 在 Azure 门户中，选择你的存储帐户，然后选择“网络”。 接着选择“专用终结点连接”。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-private-endpoint-select.png" alt-text="存储专用终结点的屏幕截图":::

1. 对于“列出的每个专用终结点”，请使用以下步骤：

    1. 选择“专用终结点”列中的链接。
    
        :::image type="content" source="./media/tutorial-create-secure-workspace/storage-private-endpoint-selected.png" alt-text="要选择的终结点的屏幕截图":::

    1. 在左侧选择“访问控制(IAM)”。 
    1. 选择“+ 添加”，然后选择“添加角色分配(预览)” 。

        ![打开了“添加角色分配”菜单的“访问控制(IAM)”页。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

    1. 在“角色”选项卡上，选择“读者” 。

        ![选中了“角色”选项卡的“添加角色分配”页。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

    1. 在“成员”选项卡上，在“将访问权限分配到”区域中选择“用户、组或服务主体”，然后选择“+ 选择成员”   。 在“选择成员”对话框中，输入名称作为 Azure 机器学习工作区。 为工作区选择服务主体，然后使用“选择”按钮。

    1. 在“查看 + 分配”选项卡上，选择“查看 + 分配”，以分配角色 。

## <a name="connect-to-the-workspace"></a>连接到工作区

可以通过多种方式连接到安全的工作区。 本文中的步骤使用的是跳转盒，它是 VNet 中的虚拟机。 可以使用 Web 浏览器和 Azure Bastion 连接到它。 下表列出了可以连接到安全工作区的其他几种方法：

| 方法 | 说明 |
| ----- | ----- |
| [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md) | 通过专用连接将本地网络连接到 VNet。 通过公共 Internet 建立连接。 |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | 通过专用连接将本地网络连接到云。 使用连接提供程序建立连接。 |

> [!IMPORTANT]
> 使用“VPN 网关”或“ExpressRoute”时，需要规划本地资源与 VNet 中资源之间名称解析的工作方式 。 有关详细信息，请参阅[使用自定义 DNS 服务器](how-to-custom-dns.md)。

### <a name="create-a-jump-box-vm"></a>创建跳转盒 (VM)

使用以下步骤创建 Data Science Virtual Machine来用作跳转盒：

1. 在 [Azure 门户](https://portal.azure.com)中，选择左上角的门户菜单。 从菜单中选择“+ 创建资源”，然后输入“Data Science Virtual Machine” 。 选择“Data Science Virtual Machine - Windows”条目，然后选择“创建” 。
1. 在“基本信息”选项卡中，选择之前用于虚拟网络的“订阅”、“资源组”和“区域”   。 提供唯一的“虚拟机名称”、“用户名”和“密码”  。 将其他字段保留为默认值。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-virtual-machine-basic.png" alt-text="VM 基本配置的屏幕截图":::

1. 选择“网络”，然后选择前面创建的“虚拟网络” 。 使用以下信息来设置剩余字段：

    * 选择“训练”子网。
    * 将“公共 IP”设置为“无” 。
    * 将其他字段保留默认值。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-virtual-machine-network.png" alt-text="VM 网络配置的屏幕截图":::

1. 选择“查看 + 创建”。 确认信息无误，然后选择“创建”。


### <a name="connect-to-the-jump-box"></a>连接到跳转盒

1. 创建虚拟机后，选择“转到资源”。
1. 在页面顶部，选择“连接”，然后选择“Bastion” 。

    :::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-connect.png" alt-text="连接/Bastion UI 的屏幕截图":::

1. 选择“使用 Bastion”，并提供虚拟机的身份验证信息，随后将在浏览器中建立一个连接。

    :::image type="content" source="./media/tutorial-create-secure-workspace/use-bastion.png" alt-text="使用 Bastion 对话框的屏幕截图":::

## <a name="create-a-compute-cluster-and-compute-instance"></a>创建计算群集和计算实例

计算群集由你的训练作业使用。 计算实例可在附加到工作区的共享计算资源上提供 Jupyter Notebook 体验。

1. 从 Azure Bastion 连接到跳转盒，在远程桌面上打开 Microsoft Edge 浏览器。
1. 在远程浏览器会话中，转到 https://ml.azure.com。 出现提示时，使用 Azure AD 帐户进行身份验证。
1. 在“欢迎使用工作室!” 屏幕中，选择之前创建的“机器学习工作区”，然后选择“开始使用” 。

    > [!TIP]
    > 如果你的 Azure AD 帐户有权访问多个订阅或目录，请使用“目录和订阅”下拉列表以选择包含该工作区的订阅或目录。

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-select-workspace.png" alt-text="选择工作区对话框的屏幕截图":::

1. 在工作室中，依次选择“计算”、“计算群集”和“+ 新建”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-cluster.png" alt-text="新计算群集工作流的屏幕截图":::

1. 在“虚拟机”对话框中，选择“下一步”以接受默认的虚拟机配置 。

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-vm.png" alt-text="计算群集 VM 设置的屏幕截图":::
    
1. 从“配置设置”对话框中，输入“cpu-cluster”作为计算名称  。 将“子网”设置为“训练”，然后选择“创建”以创建群集  。

    > [!TIP]
    > 计算群集会根据需要动态缩放群集中的节点。 建议在未使用群集时将节点的最小数量保持为 0 以降低成本。

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-settings.png" alt-text="新计算群集设置的屏幕截图":::

1. 在工作室中，依次选择“计算”、“计算实例”和“+ 新建”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance.png" alt-text="新计算实例工作流的屏幕截图":::

1. 在“虚拟机”对话框中，输入唯一“计算机名称”，然后选择“下一步:高级设置”  。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance-vm.png" alt-text="计算实例 VM 设置的屏幕截图":::

1. 在“高级设置”对话框中，将“子网”设置为“训练”，然后选择“创建”   。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance-settings.png" alt-text="计算实例设置的屏幕截图":::

> [!TIP]
> 在创建计算群集或计算实例时，Azure 机器学习动态添加网络安全组 (NSG)。 此 NSG 包含以下特定于计算群集和计算实例的规则：
> 
> * 允许 `BatchNodeManagement` 服务标记中端口 29876-29877 上的入站 TCP 流量。
> * 允许 `AzureMachineLearning` 服务标记中端口 44224 上的入站 TCP 流量。
>
> 下面的屏幕截图展示了这些规则的示例：
>
> :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="NSG 的屏幕截图":::

有关计算群集以及创建计算群集的详细信息，包括如何使用 Python 和 CLI 进行创建，请参阅以下文章：

* [创建计算群集](how-to-create-attach-compute-cluster.md)
* [创建计算实例](how-to-create-manage-compute-instance.md)

## <a name="configure-image-builds"></a>配置映像生成

当 Azure 容器注册表位于虚拟网络背后时，Azure 机器学习无法使用它来直接生成 Docker 映像（用于训练和部署）。 相反，可以将工作区配置为使用前面创建的计算群集。 使用以下步骤来创建计算群集，并将工作区配置为使用它来生成映像：

1. 导航到 [https://shell.azure.com/](https://shell.azure.com/) 以打开 Azure Cloud Shell。
1. 从 Cloud Shell 中，使用以下命令安装适用于 Azure 机器学习的 1.0 CLI：

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. 将工作区更新为使用计算群集来生成 Docker 映像。 将 `docs-ml-rg` 替换为你的资源组。 将 `docs-ml-ws` 替换为你的工作区。 将 `cpu-cluster` 替换为要使用的计算群集：

    ```azurecli-interactive
    az ml workspace update -g docs-ml-rg -w docs-ml-ws --image-build-compute cpu-cluster
    ```

    > [!NOTE]
    > 可以使用同一计算群集为工作区训练模型和生成 Docker 映像。

## <a name="use-the-workspace"></a>使用工作区

此时，可以使用工作室以交互方式在计算实例上使用笔记本，并在计算群集上运行训练作业。 有关使用计算实例和计算群集的教程，请参阅[运行 Python 脚本](tutorial-1st-experiment-hello-world.md)。

## <a name="stop-compute-instance-and-jump-box"></a>停止计算实例和跳转盒

> [!WARNING]
> 当处于运行状态时（启动后），计算实例和跳转盒将继续收取你的订阅费用。 若要避免额外成本，请在它们未使用时停止运行它们。

计算群集在创建时可在最小和最大节点计数集之间动态缩放。 如果接受默认值，最小值则为 0，这可有效关闭未处于使用状态的群集。
### <a name="stop-the-compute-instance"></a>停止计算实例

在工作室中，选择“计算”、“计算群集”，然后选择计算实例 。 最后，从页面顶部选择“停止”。

:::image type="content" source="./media/tutorial-create-secure-workspace/compute-instance-stop.png" alt-text="计算实例的“停止”按钮的屏幕截图":::
### <a name="stop-the-jump-box"></a>停止跳转盒

创建跳转盒后，在 Azure 门户中选择虚拟机，然后使用“停止”按钮。 当准备好再次使用它时，使用“启动”按钮来启动它。

:::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-stop.png" alt-text="VM 的“停止”按钮的屏幕截图":::

还可以将跳转盒配置为在特定时间自动关闭。 若要执行此操作，请依次选择“自动关闭”、“启用”，设置一个时间，然后选择“保存”  。

:::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-auto-shutdown.png" alt-text="自动关闭选项的屏幕截图":::

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用安全的工作区和其他资源，请跳过本部分。

若要删除本教程中创建的所有资源，请执行以下步骤：

1. 在 Azure 门户中，选择最左侧的“资源组”。
1. 从列表中选择你在本教程中创建的资源组。
1. 选择“删除资源组”。

    :::image type="content" source="./media/tutorial-create-secure-workspace/delete-resources.png" alt-text="“删除资源组”按钮的屏幕截图":::

1. 输入资源组名称，然后选择“删除”。
## <a name="next-steps"></a>后续步骤

现在，你已经创建了一个安全的工作区并可以访问工作室了，接下来将了解如何使用 Azure 机器学习[运行 Python 脚本](tutorial-1st-experiment-hello-world.md)。
