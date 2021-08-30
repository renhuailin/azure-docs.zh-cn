---
title: 虚拟网络中的 Azure Spring Cloud 访问应用
description: 访问虚拟网络中 Azure Spring Cloud 中的应用。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 68cac51ba9d54abc6514cf493077740339ac56c5
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015572"
---
# <a name="access-your-application-in-a-private-network"></a>在专用网络中访问应用程序

本文介绍如何在专用网络中访问应用程序的终结点。  要获取访问权限，需要在订阅中创建一个“Azure 专用 DNS 区域”，以将专用完全限定的域名 (FQDN) 转换/解析到其 IP 地址。

在虚拟网络中部署了 Azure Spring Cloud 服务实例中的应用程序的“分配终结点”时，该终结点为专用 FQDN。 只能在专用网络中访问该域。 应用和服务使用应用程序终结点。 包括[查看应用和部署](./how-to-staging-environment.md#view-apps-and-deployments)中所述的“测试终结点”。 “日志流式处理”（如[实时流式传输 Azure Spring Cloud 应用日志](./how-to-log-streaming.md)中所述）也仅可在专用网络中进行。

## <a name="create-a-private-dns-zone"></a>创建专用 DNS 区域

以下过程为专用网络中的应用程序创建专用 DNS 区域。

1. 打开 Azure 门户。 在顶部搜索框中，搜索“专用 DNS 区域”，然后从结果中选择“专用 DNS 区域”。

2. 在“专用 DNS 区域”页面上，选择“添加” 。

3. 填写“创建专用 DNS 区域”页上的表单。 输入“private.azuremicroservices.io”作为区域的“名称” **<span></span>** 。

    >[!NOTE]
    > 如果你使用的是 Azure 中国，请将整个文档中的 `private.azuremicroservices.io` 替换为 `private.microservices.azure.cn`，[了解详细信息](/azure/china/resources-developer-guide#check-endpoints-in-azure)。

4. 选择“查看 + 创建”  。

5. 选择“创建”  。

创建区域可能需要几分钟。

## <a name="link-the-virtual-network"></a>链接虚拟网络

若要将专用 DNS 区域链接到虚拟网络，需要创建虚拟网络链接。

1. 选择上面创建的专用 DNS 区域资源：<span>private.azuremicroservices.io</span>

2. 在左窗格中，选择“虚拟网络链接”。

3. 选择“添加”。

4. 为“链接名称”输入“azure-spring-cloud-dns-link” 。

5. 对于“虚拟网络”，请选择你创建的虚拟网络，如[在 Azure 虚拟网络中部署 Azure Spring Cloud（VNet 注入）](./how-to-deploy-in-azure-virtual-network.md)中所述。

    ![添加虚拟网络链接](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. 选择“确定”。

## <a name="create-dns-record"></a>创建 DNS 记录

若要使用专用 DNS 区域来转换/解析 DNS，必须在该区域中创建“A”类型的记录。

1. 选择你创建的虚拟网络资源，如[在 Azure 虚拟网络中部署 Azure Spring Cloud（VNet 注入）](./how-to-deploy-in-azure-virtual-network.md)中所述。

2. 在“连接的设备”搜索框中输入“kubernetes-internal”。

3. 在筛选的结果中，找到连接到服务实例的服务运行时“子网”的“设备”，然后复制其“IP 地址”  。 在此示例中，IP 地址为 10.1.0.7。

    [ ![创建 DNS 记录](media/spring-cloud-access-app-vnet/create-dns-record.png) ](media/spring-cloud-access-app-vnet/create-dns-record.png)

或者，可以使用以下 az CLI 命令提取 IP：

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. 选择上面创建的专用 DNS 区域资源：private.azuremicroservices.io **<span></span>** 。

5. 选择“记录集”。 

6. 在“添加记录集”中，输入或选择以下信息：

    |设置     |值                                                                      |
    |------------|---------------------------------------------------------------------------|
    |名称        |输入“\*”                                                                 |
    |类型        |选择“A”                                                               |
    |TTL         |输入 1                                                                  |
    |TTL 单位    |选择“小时”                                                           |
    |IP 地址  |输入在步骤 3 中复制的 IP 地址。 在此示例中，输入 10.1.0.7。    |

    然后选择“确定”。

    ![添加专用 DNS 区域记录](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>为应用程序分配专用 FQDN

按照[生成和部署微服务应用程序](./how-to-deploy-in-azure-virtual-network.md)中的过程操作后，可以为应用程序分配专用 FQDN。

1. 选择在虚拟网络中部署的 Azure Spring Cloud 服务实例，并打开左侧菜单中的“应用”选项卡。

2. 选择此应用程序以显示“概述”页面。

3. 选择“分配终结点”，将专用 FQDN 分配给应用程序。 这会需要几分钟时间。

    ![分配专用终结点](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. 分配的专用 FQDN（标记为 URL）现在可用。 但只能在专用网络内访问，不能在 Internet 上访问。

## <a name="access-application-private-fqdn"></a>访问应用程序专用 FQDN

分配后，可以在专用网络中访问应用程序的专用 FQDN。 例如，可以在同一虚拟网络或对等互连虚拟网络中创建 jumpbox 计算机，并且可以在该 jumpbox 计算机上访问专用 FQDN。

![访问 vnet 中的专用终结点](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>后续步骤

- [向 Internet 公开应用程序 - 使用应用程序网关和 Azure 防火墙](./expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>请参阅

- [在 VNET 中排除 Azure Spring Cloud 的故障](./troubleshooting-vnet.md)
- [在 VNET 中运行 Azure Spring Cloud 的客户责任](./vnet-customer-responsibilities.md)
