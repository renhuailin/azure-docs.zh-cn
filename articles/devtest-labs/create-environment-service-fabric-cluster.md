---
title: 创建 Service Fabric 群集环境
description: 了解如何创建具有自包含 Service Fabric 群集的环境，以及如何使用计划启动和停止群集。
author: EMaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 42a8c3b11f2da5910ff3aed1a1eface827100289
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593831"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建具有自包含 Service Fabric 群集的环境
本文提供有关如何在 Azure 开发测试实验室中创建具有自包含 Service Fabric 群集的环境的信息。 

## <a name="overview"></a>概述
开发测试实验室可以根据 Azure 资源管理器模板的定义创建自包含测试环境。 这些环境包含 IaaS 资源，例如虚拟机和 PaaS 资源（如 Service Fabric）。 开发测试实验室通过提供用于控制虚拟机的命令，让你能够管理环境中的虚拟机。 这些命令使你能够按计划启动或停止虚拟机。 同样，开发测试实验室还可以帮助你管理环境中的 Service Fabric 群集。 你可以手动或通过计划启动或停止环境中的 Service Fabric 群集。

## <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集
Service Fabric 群集是使用开发测试实验室中的环境创建的。 每个环境均通过 Git 存储库中的 Azure 资源管理器模板定义。 用于开发测试实验室的[公共 Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)包含用于在 [ServiceFabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 文件夹中创建 Service Fabric 群集的资源管理器模板。 

1. 首先，使用以下文章中的说明在 Azure 开发测试实验室中创建实验室：[创建实验室](devtest-lab-create-lab.md)。 请注意，默认情况下“公共环境”选项处于“启用”状态 。 
2. 通过执行以下步骤，确认是否已为你的订阅注册 Service Fabric 提供程序：
    1. 在左侧导航菜单中选择“订阅”，然后选择你的订阅 
    2. 在“订阅”页左侧菜单的“设置”部分中，选择“资源提供程序”  。 
    3. 如果未注册 Microsoft.ServiecFabric，请选择“注册” 。 
3. 在实验室的“开发测试实验室”页上，选择工具栏中的“+ 添加”。 
    
    ![工具栏上的“添加”按钮](./media/create-environment-service-fabric-cluster/add-button.png)
3. 在“选择基础映像”页上，选择列表中的“Service Fabric 实验室群集” 。 

    ![选择列表中的“Service Fabric 实验室群集”](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 在“配置设置”页上，按照以下步骤操作： 
    1. 指定群集环境的名称 。 这是要在其中创建 Service Fabric 群集的 Azure 中资源组的名称。 
    2. 选择群集虚拟机的操作系统 (OS)。 默认值为 Windows。
    3. 为群集管理员指定名称。 
    4. 为管理员指定密码。 
    5. 对于证书，请以 Base64 编码的字符串输入证书信息。 若要创建证书，请按照以下步骤操作：
        1. 从 [Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)下载 **Create-ClusterCertificate.ps1** 文件。 或者，将存储库克隆到计算机上。 
        2. 启动 **PowerShell**。 
        3. 使用命令 `.\Create-ClusterCertificate.ps1` 运行 ps1 文件。 你会看到记事本中打开的文本文件，其中包含需要在此页上填写的证书相关字段信息。 . 
    6. 输入证书的密码。
    7. 指定证书的指纹。
    8. 在“配置设置”页上，选择“添加” 。 

        ![配置群集设置](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 创建群集后，会看到一个资源组，该资源组具有上一步骤中提供的环境名称。 展开它后，会看到其中的 Service Fabric 群集。 如果资源组的状态停留在“正在创建”，请在工具栏上选择“刷新” 。 Service Fabric 群集环境会在 Linux 或 Windows 上创建一个 5 节点的 1 节点类型群集。

    在以下示例中，mysfabricclusterrg 是专门为 Service Fabric 群集创建的资源组的名称。 需要特别注意的是，实验室环境自包含在创建它们的资源组中。 这意味着定义环境的模板只能访问新创建的资源组或[配置为由实验室使用的虚拟网络](devtest-lab-configure-vnet.md)中的资源。 上面的示例创建了同一资源组中所需的所有资源。

    ![已创建群集](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>启动或停止群集
可以从开发测试实验室页面本身或开发测试实验室提供的 Service Fabric 群集页启动或停止群集。 

### <a name="from-the-devtest-lab-page"></a>从开发测试实验室页
你可以在实验室的开发测试实验室页上启动或停止群集。 

1. 为 Service Fabric 群集选择三个点 (...)，如下图所示： 

    ![群集的启动和停止命令](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 在上下文菜单中，可以看到用于启动和停止群集的两个命令 。 启动命令会启动群集中的所有节点。 停止命令会停止群集中的所有节点。 群集停止后，Service Fabric 群集本身仍处于“就绪”状态，但在对实验室中的群集重新颁发“启动”命令之前，无法使用任何节点。

    在测试环境中使用 Service Fabric 群集时，需要注意几个事项。 在重新启动节点后，Service Fabric 群集可能需要一段时间才能完全解除冻结。 为了保留从关机到启动的数据，必须将数据保存在附加到虚拟机的托管磁盘上。 使用附加的托管磁盘会影响性能，因此建议仅用于测试环境。 如果没有备份用于数据存储的磁盘，则对群集发出停止命令后，数据将丢失。

### <a name="from-the-service-fabric-cluster-page"></a>从 Service Fabric 群集页 
还可以通过另一种方式启动或停止群集。 

1. 在开发测试实验室页上的树状视图中，选择 Service Fabric 群集。 

    ![选择群集](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 在群集的“Service Fabric 群集”页上，可以在工具栏上看到用于启动或停止群集的命令。 

    ![Service Fabric 群集页中的启动或停止命令](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>配置自动启动和自动关闭计划
Service Fabric 群集也可以按计划启动或停止。 此体验类似于实验室中虚拟机的体验。 为了节省资金，默认情况下，实验室中创建的每个群集都会按实验室[关闭策略](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)定义的时间自动关闭。 可以通过指定是否应关闭群集或指定群集关闭的时间来进行覆盖。 

![用于自动启动和自动关闭的现有计划](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>选择启用自动启动计划
若要选择启用启动计划，请按照以下步骤操作：

1. 在左侧菜单中选择“自动启动”
2. 对于“允许将此 Service Fabric 计划为自动启动”，请选择“开启” 。 仅当实验室所有者允许用户自动启动其虚拟机或 Service Fabric 群集时，才会启用此页。
3. 在工具栏上选择“保存”。 

    ![自动启动页面](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>配置自动关闭设置 
若要更改关闭的设置，请按照以下步骤操作：

1. 在左侧菜单上，选择“自动关闭”。 
2. 在此页上，可以通过对“启用”选择“关闭”来退出自动关闭 。 
3. 如果已对“启用”选择了“开启”，请按照以下步骤操作 ：
    1. 指定关闭的时间。
    2. 指定时间的时区。 
    3. 指定是否希望开发测试实验室在自动关闭前发送通知。 
    4. 如果为“通知”选项选择了“是”，请指定用于发送通知的 Webhook URL 和/或 电子邮件地址  。 
    5. 在工具栏上选择“保存”。

        ![自动关闭页面](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>查看 Service Fabric 群集中的节点
你在之前的步骤中看到的 Service Fabric 群集页特定于开发测试实验室页。 它不会显示群集中的节点。 若要查看有关群集的详细信息，请按照以下步骤操作：

1. 在实验室的“开发测试实验室”页上，在“我的虚拟机”部分中的树视图中选择资源组  。

    ![选择资源组](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 在“资源组”页上，可以看到资源组中所有资源的列表。 从列表中选择“Service Fabric 群集”。 

    ![在列表中选择群集](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 你会看到群集的“Service Fabric 群集”页。 这是 Service Fabric 提供的页面。 你将看到有关群集的所有信息，例如节点、节点类型等。

    ![Service Fabric 群集主页](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>后续步骤
有关环境的详细信息，请参阅以下文章： 

- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
