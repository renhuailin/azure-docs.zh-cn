---
title: 在虚拟网络中部署 Azure Spring Cloud
description: 在虚拟网络中部署 Azure Spring Cloud（VNet 注入）。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli, subject-rbac-steps
ms.openlocfilehash: 6822514e6bcbb5a232f7ee7f22ec8b0ee8a21e10
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396741"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>在虚拟网络中部署 Azure Spring Cloud

本文适用于：✔️ Java ✔️ C#

本教程介绍如何在虚拟网络中部署 Azure Spring Cloud 实例。 此部署有时称为 VNet 注入。

该部署支持：

* 从企业网络中的 Internet 隔离 Azure Spring Cloud 应用和服务运行时。
* Azure Spring Cloud 与本地数据中心内的系统或其他虚拟网络中的 Azure 服务进行交互。
* 授权客户控制 Azure Spring Cloud 的入站和出站网络通信。

以下视频介绍了如何使用托管虚拟网络保护 Spring Boot 应用程序。

<br>

> [!VIDEO https://www.youtube.com/embed/LbHD0jd8DTQ?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

> [!Note]
> 仅当创建新的 Azure Spring Cloud 服务实例时，才能选择 Azure 虚拟网络。 创建 Azure Spring Cloud 后，不能改为使用其他虚拟网络。

## <a name="prerequisites"></a>先决条件

根据说明[在 Azure 门户上注册资源提供程序](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)或运行以下 Azure CLI 命令来注册 Azure Spring Cloud 资源提供程序 Microsoft.AppPlatform 和 Microsoft.ContainerService ：

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>虚拟网络要求

将 Azure Spring Cloud 实例部署到其中的虚拟网络必须满足以下要求：

* 位置：该虚拟网络必须与 Azure Spring Cloud 实例位于同一位置。
* 订阅：该虚拟网络必须与 Azure Spring Cloud 实例位于同一订阅中。
* **子网**：该虚拟网络必须包含两个专用于 Azure Spring Cloud 实例的子网：

    * 一个用于服务运行时。
    * 一个用于 Spring Boot 微服务应用程序。
    * 这些子网和 Azure Spring Cloud 实例之间存在一对一关系。 请为部署的每个服务实例使用一个新子网。 每个子网只能包含一个服务实例。
* **地址空间**：对于服务运行时子网和 Spring Boot 微服务应用程序子网，CIDR 块均为最多 /28。
* **路由表**：默认情况下，子网不需要关联现有的路由表。 你可以[使用自己的路由表](#bring-your-own-route-table)。

以下过程描述了虚拟网络的设置，以包含 Azure Spring Cloud 的实例。

## <a name="create-a-virtual-network"></a>创建虚拟网络

如果已有用于托管 Azure Spring Cloud 实例的虚拟网络，请跳过步骤 1、2 和 3。 可以从步骤 4 开始，为虚拟网络准备子网。

1. 在 Azure 门户菜单中，选择“创建资源”。 在 Azure 市场中，选择“网络” > “虚拟网络” 。

1. 在“创建虚拟网络”对话框中，输入或选择以下信息：

    |设置          |“值”                                             |
    |-----------------|--------------------------------------------------|
    |订阅     |选择订阅。                         |
    |资源组   |选择你的资源组，或新建一个资源组。  |
    |名称             |输入 azure-spring-cloud-vnet。                 |
    |位置         |选择“美国东部”。                               |

1. 在完成时选择“下一步:IP 地址”。

1. 对于 IPv4 地址空间，输入 10.1.0.0/16。

1. 选择“添加子网”。 然后输入 service-runtime-subnet 作为“子网名称”，输入 10.1.0.0/28 作为“子网地址范围”   。 然后选择“添加”  。

1. 再次选择“添加子网”，然后输入“子网名称”和“子网地址范围”  。 例如，输入“apps-subnet”和“10.1.1.0/28” 。 然后选择“添加”  。

1. 选择“查看 + 创建”。 将其余的设置保留默认值，然后选择“创建”。

## <a name="grant-service-permission-to-the-virtual-network"></a>向虚拟网络授予服务权限
Azure Spring Cloud 要求对虚拟网络具有“所有者”权限，以便在虚拟网络中授予专用的动态服务主体，进行进一步的部署和维护。

选择之前创建的虚拟网络 azure-spring-cloud-vnet。

1. 选择“访问控制(IAM)”，然后选择“添加” > “添加角色分配”  。

    ![屏幕截图显示“访问控制”屏幕。](./media/spring-cloud-v-net-injection/access-control.png)

1. 将“所有者”角色分配给 Azure Spring Cloud 资源提供程序。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md#step-2-open-the-add-role-assignment-pane)。

    ![显示资源提供程序的所有者分配情况的屏幕截图。](./media/spring-cloud-v-net-injection/assign-owner-resource-provider.png)

    还可以通过运行以下 Azure CLI 命令来执行此步骤：

    ```azurecli
    VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
        --name ${NAME_OF_VIRTUAL_NETWORK} \
        --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
        --query "id" \
        --output tsv`

    az role assignment create \
        --role "Owner" \
        --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
        --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
    ```

## <a name="deploy-an-azure-spring-cloud-instance"></a>部署 Azure Spring Cloud 实例

在虚拟网络中部署 Azure Spring Cloud 实例：

1. 打开 [Azure 门户](https://portal.azure.com)。

1. 在顶部搜索框中，搜索“Azure Spring Cloud”。 从结果中选择“Azure Spring Cloud”。

1. 在 Azure Spring Cloud 页面上，选择“+ 添加” 。

1. 在 Azure Spring Cloud“创建”页中填写表单。

1. 选择与虚拟网络相同的资源组和区域。

1. 对于“服务详细信息”下的“名称”，请选择“azure-spring-cloud-vnet” 。

1. 选择“网络”选项卡，然后选择以下值：

    |设置                                |值                                             |
    |---------------------------------------|--------------------------------------------------|
    |在自己的虚拟网络中部署     |请选择“是”。                                   |
    |虚拟网络                        |选择“azure-spring-cloud-vnet”。               |
    |服务运行时子网                 |选择“service-runtime-subnet”。                |
    |Spring Boot 微服务应用子网   |选择“apps-subnet”。                           |

    ![屏幕截图显示了“Azure Spring Cloud 创建”页上的“网络”选项卡。](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. 选择“查看并创建”。 

1. 验证你的规范，并选择“创建”。

    ![屏幕截图显示验证规范。](./media/spring-cloud-v-net-injection/verify-specifications.png)

部署后，订阅中将创建另外两个资源组来托管 Azure Spring Cloud 实例的网络资源。 转到“主页”，然后从顶部菜单项中选择“资源组”，以查找以下新资源组 。

名为 ap-svc-rt_{service instance name}_{service instance region} 的资源组包含服务实例的服务运行时网络资源。

  ![显示服务运行时的屏幕截图。](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

名为 ap-app_{service instance name}_{service instance region} 的资源组包含服务实例的 Spring Boot 微服务应用程序网络资源。

  ![显示应用资源组的屏幕截图。](./media/spring-cloud-v-net-injection/apps-resource-group.png)

这些网络资源连接到在上图中创建的虚拟网络。

  ![屏幕截图显示包含连接的设备的虚拟网络。](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > 资源组由 Azure Spring Cloud 服务完全托管。 请不要手动删除或修改内部的任何资源。

## <a name="using-smaller-subnet-ranges"></a>使用较小的子网范围

该表显示了 Azure Spring Cloud 在使用较小的子网范围时支持的最大应用实例数。

| 应用子网 CIDR | IP 总数 | 可用的 IP | 最大应用实例                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> 具有 1 个核心的应用：96 <br/> 具有 2 个核心的应用：48<br/>  具有 3 个核心的应用：32 <br/> 具有 4 个核心的应用：24 </p> |
| /27             | 32        | 24            | <p> 具有 1 个核心的应用：228<br/> 具有 2 个核心的应用：144<br/>  具有 3 个核心的应用：96 <br/>  具有 4 个核心的应用：72</p> |
| /26             | 64        | 56            | <p> 具有 1 个核心的应用：500<br/> 具有 2 个核心的应用：336<br/>  具有 3 个核心的应用：224<br/>  具有 4 个核心的应用：168</p> |
| /25             | 128       | 120           | <p> 具有 1 个核心的应用：500<br> 具有 2 个核心的应用：500<br>  具有 3 个核心的应用：480<br>  具有 4 个核心的应用：360</p> |
| /24             | 256       | 248           | <p> 具有 1 个核心的应用：500<br/> 具有 2 个核心的应用：500<br/>  具有 3 个核心的应用：500<br/>  具有 4 个核心的应用：500</p> |

Azure 为子网预留 5 个 IP 地址，而 Azure Spring Cloud 至少需要 3 个地址。 至少需要 8 个 IP 地址，因此 /29 和/30 不适用。

对于服务运行时子网，最小大小为 /28。 此大小与应用程序实例的数量无关。

## <a name="bring-your-own-route-table"></a>使用自己的路由表

Azure Spring Cloud 支持使用现有的子网和路由表。

如果自定义子网不包含路由表，Azure Spring Cloud 将为每个子网创建路由表，并在整个实例生命周期中将规则添加到路由表。 如果自定义子网包含路由表，Azure Spring Cloud 将在实例操作期间确认现有路由表，并相应地添加/更新和/或删除操作规则。

> [!Warning]
> 可将自定义规则添加到自定义路由表中并对其进行更新。 但是，规则由 Azure Spring Cloud 添加，且不可更新或删除。 诸如 0.0.0.0/0 的规则必须始终存在于给定的路由表中，并映射到 internet 网关的目标，例如 NVA 或其他出口网关。 更新规则时请谨慎，在此情况下只会修改自定义规则。

### <a name="route-table-requirements"></a>路由表要求

与自定义 VNet 关联的路由表必须符合以下要求：

* 仅当创建新的 Azure Spring Cloud 服务实例时，才能将 Azure 路由表与 VNet 相关联。 创建 Azure Spring Cloud 后，无法改用其他路由表。
* 微服务应用程序子网和服务运行时子网必须与不同的路由表相关联，或者不与任何路由表相关联。
* 在创建实例之前必须分配权限。 确保授予对路由表的 Azure Spring Cloud 资源提供程序“所有者”权限。
* 创建群集后，无法更新关联的路由表资源。 虽然无法更新路由表资源，但可以在路由表上修改自定义规则。
* 不能重复使用具有多个实例的路由表，因为这可能导致路由规则冲突。

## <a name="next-steps"></a>后续步骤

- [在 VNet 中将应用程序部署到 Azure Spring Cloud 中](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)
- [在 VNET 中排除 Azure Spring Cloud 的故障](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [在 VNET 中运行 Azure Spring Cloud 的客户责任](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
