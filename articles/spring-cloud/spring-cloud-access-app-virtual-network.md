---
title: 虚拟网络中的 Azure 春季云访问应用
description: 在虚拟网络中的 Azure 春季云中访问应用。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: b6e523fc4a3dcd0eb8c8ed7a44b3d7d9fcdd7d34
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98887156"
---
# <a name="access-your-application-in-a-private-network"></a>在专用网络中访问应用程序

本文档介绍如何在专用网络中访问应用程序的终结点。  若要获取访问权限，需要在订阅中创建一个 **Azure 专用 DNS 区域** ，以将专用完全限定的域名转换/解析 (FQDN) 到其 IP 地址。

在虚拟网络中部署了 Azure 春季云服务实例中的应用程序的 **分配终结点** 时，终结点是专用 FQDN。 只能在专用网络中访问域。 应用和服务使用应用程序终结点。 它们包括 [验证过渡部署](spring-cloud-howto-staging-environment.md#verify-the-staging-deployment)中所述的 **测试终结点**。 **日志流式处理**（ [实时传输 Azure 春季云应用日志中](spring-cloud-howto-log-streaming.md)所述）也仅适用于专用网络中。

## <a name="create-a-private-dns-zone"></a>创建专用 DNS 区域

以下过程将为专用网络中的应用程序创建专用 DNS 区域。

1. 打开 Azure 门户。 在顶部搜索框中，搜索 " **专用 DNS 区域**"，然后从结果中选择 " **专用 DNS 区域** "。

2. 在 " **专用 DNS 区域** " 页上，选择 " **+ 添加**"。

3. 在 " **创建专用 DNS 区域** " 页上填写表单。 输入 **<span>private.azuremicroservices.io</span>** 作为区域的 **名称** 。

4. 选择“查看 + 创建”  。

5. 选择“创建”  。

创建区域可能需要几分钟。

## <a name="link-the-virtual-network"></a>链接虚拟网络

若要将专用 DNS 区域链接到虚拟网络，需要创建虚拟网络链接。

1. 选择上面创建的专用 DNS 区域资源： **<span>private.azuremicroservices.io</span>** 

2. 在左窗格中，选择“虚拟网络链接”。

3. 选择“添加”。

4. 为 **链接名称** 输入 " **azure-云-dns" 链接**。

5. 对于 " **虚拟网络**"，请选择你创建的虚拟网络，如在 [azure 虚拟网络中部署 azure 春季云中 (VNet 注入)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)中所述。

    ![添加虚拟网络链接](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. 单击“确定”。

## <a name="create-dns-record"></a>创建 DNS 记录

若要使用专用 DNS 区域来转换/解析 DNS，必须在该区域中创建 "A" 类型的记录。

1. 根据在 [azure 虚拟网络中部署 Azure 春季云中 (VNet 注入) ](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)中所述，选择已创建的虚拟网络资源。

2. 在 " **连接的设备** " 搜索框中输入 " *kubernetes-内部*"。

3. 在筛选的结果中，找到连接到服务实例的服务运行时 **子网** 的 **设备**，然后复制其 **IP 地址**。 在此示例中，IP 地址为 *10.1.0.7*。

    [![创建 DNS 记录 ](media/spring-cloud-access-app-vnet/create-dns-record.png)](media/spring-cloud-access-app-vnet/create-dns-record.png)

或者，可以使用以下 az CLI 命令提取 IP：

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. 选择上面创建的专用 DNS 区域资源： **<span>private.azuremicroservices.io</span>**。

5. 选择“+记录集”。

6. 在 " **添加记录集**" 中，输入或选择以下信息：

    |设置     |值                                                                      |
    |------------|---------------------------------------------------------------------------|
    |名称        |输入“\*”                                                                 |
    |类型        |选择 **一个**                                                               |
    |TTL         |输入 *1*                                                                  |
    |TTL 单位    |选择 **小时数**                                                           |
    |IP 地址  |输入在步骤3中复制的 IP 地址。 在示例中，输入 *10.1.0.7*。    |

    然后选择“确定”。

    ![添加专用 DNS 区域记录](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>为应用程序分配专用 FQDN

按照 [生成和部署微服务应用程序](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)中的过程操作后，你可以为应用程序分配专用 FQDN。

1. 选择虚拟网络中部署的 Azure 春季云服务实例，并打开左侧菜单中的 " **应用** " 选项卡。

2. 选择应用程序以显示 " **概述** " 页。

3. 选择 " **分配终结点** " 将专用 FQDN 分配给应用程序。 这会需要几分钟时间。

    ![分配专用终结点](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. 已分配的私人 FQDN (标记 **URL**) 现在可用。 只能在专用网络内访问，但不能在 Internet 上访问。

## <a name="access-application-private-fqdn"></a>访问应用程序专用 FQDN

分配后，你可以在专用网络中访问应用程序的私有 FQDN。 例如，你可以在同一虚拟网络中创建 jumpbox 计算机，也可以在对等互连虚拟网络中创建，并且可以访问该 jumpbox 计算机上的专用 FQDN。

![访问 vnet 中的专用终结点](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>后续步骤

- [使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>请参阅

- [在 VNET 中排除 Azure Spring Cloud 的故障](spring-cloud-troubleshooting-vnet.md)
- [在 VNET 中运行 Azure Spring Cloud 的客户责任](spring-cloud-vnet-customer-responsibilities.md)