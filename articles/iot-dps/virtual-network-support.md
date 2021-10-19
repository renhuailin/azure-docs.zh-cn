---
title: 对虚拟网络的 Azure IoT 设备预配服务 (DPS) 支持
description: 如何将虚拟网络连接模式与 Azure IoT 设备预配服务 (DPS) 一起使用
services: iot-dps
author: anastasia-ms
ms.service: iot-dps
manager: lizross
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: v-stharr
ms.openlocfilehash: 8d90a033f5af5afb55be9585756a7235dc6d89d7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659310"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>对虚拟网络的 Azure IoT 中心设备预配服务 (DPS) 支持

本文介绍了通过使用 DPS 的 IoT 中心进行 IoT 设备预配时，所适用的虚拟网络 (VNET) 连接模式。 此模式在客户拥有的 Azure VNET 内的设备、DPS 和 IoT 中心之间提供专用连接。 

在大多数情况下，使用 VNET 配置了 DPS 后，还将在同一 VNET 中配置 IoT 中心。 有关对 IoT 中心的 VNET 支持和配置的更多特定信息，请参阅 [IoT 中心虚拟网络支持](../iot-hub/virtual-network-support.md)。

## <a name="introduction"></a>简介

默认情况下，DPS 主机名会映射到一个公共终结点，该终结点具有可通过 Internet 以公开方式路由的 IP 地址。 此公共终结点对所有客户都可见。 IoT 设备可以尝试通过广域网络和本地网络来访问公共终结点。

出于多种原因，客户可能想要限制与 DPS 等 Azure 资源的连接。 其原因包括：

* 防止通过公共 Internet 的连接曝光。 可以通过网络级隔离为 IoT 中心和 DPS 资源引入更多安全层，从而降低曝光度

* 提供来自本地网络资产的专用连接体验，确保数据和流量直接传输到 Azure 主干网络。

* 阻止来自敏感的本地网络的渗透攻击。 

* 采用 Azure 范围内常见的[专用终结点](../private-link/private-endpoint-overview.md)连接模式。

限制连接的常见方法包括 [DPS IP 筛选器规则](./iot-dps-ip-filtering.md)和具有[专用终结点](../private-link/private-endpoint-overview.md)的虚拟网络 (VNET)。 本文的重点介绍对象是使用专用终结点的 DPS 的 VNET 方法。 

在本地网络中运行的设备可以使用[虚拟专用网络 (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 专用对等互连来连接到 Azure 中的 VNET，并通过专用终结点访问 DPS 资源。 

专用终结点是在客户拥有的 VNET 内部分配的专用 IP 地址，可通过该地址访问 Azure 资源。 当 DPS 资源有了专用终结点时，流量无需经过公共终结点，在 VNET 中运行的设备就能请求 DPS 资源进行预配。

## <a name="prerequisites"></a>先决条件

在继续操作之前，请确保满足以下先决条件：

* DPS 资源已创建并链接到 IoT 中心。 有关如何设置新 DPS 资源的指南，请参阅[使用 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)

* 已预配一个 Azure VNET，其中配置了要创建专用终结点的子网。 有关详细信息，请参阅[使用 Azure CLI 创建虚拟网络](../virtual-network/quick-create-cli.md)。

* 对于在本地网络中运行的设备，请在 Azure VNET 中设置[虚拟专用网络 (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 专用对等互连。

## <a name="private-endpoint-limitations"></a>专用终结点限制

使用专用终结点时，请注意以下对 DPS 的当前限制：

* 当 DPS 资源和链接的中心位于不同云中时，专用终结点将无法与 DPS 一起使用。 例如 [Azure 政府和全球 Azure](../azure-government/documentation-government-welcome.md)。

* 目前，当 Azure 函数锁定到 VNET 和专用终结点时，针对 DPS 的、[使用 Azure Functions 的自定义分配策略](how-to-use-custom-allocation-policies.md)将不起作用。 

* 当前的 DPS VNET 支持仅适用于进入 DPS 的数据流入。 数据流出（从 DPS 到 IoT 中心的流量）使用内部服务到服务机制，而不是专用 VNET。 当前不支持在 DPS 和 IoT 中心之间进行完全的基于 VNET 的流出锁定。

* 使用最短延迟分配策略，将设备分配到具有最低延迟的 IoT 中心。 此分配策略在虚拟网络环境中不可靠。 

>[!NOTE]
>**数据驻留注意事项：**
>
>DPS 提供全局设备终结点 (`global.azure-devices-provisioning.net`)。 但是，使用全局终结点时，数据可能会重定向到最初创建 DPS 实例的区域之外。 若要确保数据驻留在初始 DPS 区域，请使用专用终结点。

## <a name="set-up-a-private-endpoint"></a>设置专用终结点

若要设置专用终结点，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，打开 DPS 资源，然后单击“网络”选项卡。单击“Private endpoint connections（专用终结点连接）”和“+ 专用终结点”。

    ![为 DPS 添加新的专用终结点](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. 在“创建专用终结点”的“基本信息”页上，输入下表中所述的信息。

    ![创建专用终结点基本信息](./media/virtual-network-support/create-private-endpoint-basics.png)

    | 字段 | Value |
    | :---- | :-----|
    | **订阅** | 选择要包含专用终结点的所需 Azure 订阅。  |
    | **资源组** | 选择或创建一个要包含专用终结点的资源组 |
    | **名称**       | 输入专用终结点的名称 |
    | **区域**     | 选择的区域必须与包含 VNET 的区域相同，但不一定要与 DPS 资源相同。 |

    单击“下一步:资源”，以配置专用终结点将指向的资源。

3. 在“创建专用终结点”的“资源”页上，输入下表中所述的信息。

    ![创建专用终结点资源](./media/virtual-network-support/create-private-endpoint-resource.png)

    | 字段 | 值 |
    | :---- | :-----|
    | **订阅**        | 选择包含了专用终结点将指向的 DPS 资源的 Azure 订阅。  |
    | **资源类型**       | 选择 **Microsoft.Devices/ProvisioningServices**。 |
    | **资源**            | 选择专用终结点将映射到的 DPS 资源。 |
    | **目标子资源** | 选择 **iotDps**。 |

    > [!TIP]
    > 有关“按资源 ID 或别名连接到 Azure 资源”设置的信息，请参阅本文中的[请求专用终结点](#request-a-private-endpoint)部分。


    单击“下一步:配置”，为专用终结点配置 VNET。

4. 在“创建专用终结点”的“配置”页上，选择要在其中创建专用终结点的虚拟网络和子网。

    单击“下一步:标记”，为资源提供标记（可选）。

    ![配置专用终结点](./media/virtual-network-support/create-private-endpoint-configuration.png)

5. 依次单击“查看 + 创建”和“创建”，以创建专用终结点资源。

## <a name="use-private-endpoints-with-devices"></a>对设备使用专用终结点

若要将专用终结点与设备预配代码一起使用，预配代码必须使用 DPS 资源的特定 **服务终结点**，如 [Azure 门户](https://portal.azure.com)中 DPS 资源的概述页所示。 服务终结点具有以下形式。

`<Your DPS Tenant Name>.azure-devices-provisioning.net`

文档和 SDK 中演示的大多数示例代码使用全局设备终结点 (`global.azure-devices-provisioning.net`) 和 ID 范围来解析特定的 DPS 资源。 使用专用终结点连接到 DPS 资源来预配设备时，使用服务终结点来代替全局设备终结点。

例如，[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的预配设备客户端示例 ([pro_dev_client_sample](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/prov_dev_client_sample)) 旨在使用全局设备终结点作为 [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) 中的全局预配 URI (`global_prov_uri`)

:::code language="c" source="~/iot-samples-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c" range="60-64" highlight="4":::

:::code language="c" source="~/iot-samples-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c" range="138-144" highlight="3":::

若要将示例与专用终结点一起使用，上述突出显示的代码将更改为使用 DPS 资源的服务终结点。 例如，如果服务终结点为 `mydps.azure-devices-provisioning.net`，则代码将如下所示。

```C
static const char* global_prov_uri = "global.azure-devices-provisioning.net";
static const char* service_uri = "mydps.azure-devices-provisioning.net";
static const char* id_scope = "[ID Scope]";
```

```C
    PROV_DEVICE_RESULT prov_device_result = PROV_DEVICE_RESULT_ERROR;
    PROV_DEVICE_HANDLE prov_device_handle;
    if ((prov_device_handle = Prov_Device_Create(service_uri, id_scope, prov_transport)) == NULL)
    {
        (void)printf("failed calling Prov_Device_Create\r\n");
    }
```


## <a name="request-a-private-endpoint"></a>请求专用终结点

可以按资源 ID 向 DPS 资源请求专用终结点。 若要提出此请求，需要资源所有者提供资源 ID。 

1. 资源 ID 显示在 DPS 资源的属性选项卡上，如下所示。

    ![DPS“属性”选项卡](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > 请注意，资源 ID 中包含订阅 ID。 

2. 获得资源 ID 后，请按照上文 [设置专用终结点](#set-up-a-private-endpoint)中的步骤到 _创建专用终结点“资源”_ 页中的步骤 3 进行操作。 单击“按资源 ID 或别名连接到 Azure 资源”，并输入下表中的信息。 

    | 字段 | 值 |
    | :---- | :-----|
    | **资源 ID 或别名** | 输入 DPS 资源的资源 ID。 |
    | **目标子资源** | 输入 **iotDps** |
    | 请求消息 | 输入面向 DPS 资源所有者的请求消息。<br>例如， <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    单击“下一步:配置”，为专用终结点配置 VNET。

3. 在“创建专用终结点”的“配置”页上，选择要在其中创建专用终结点的虚拟网络和子网。
 
    单击“下一步:标记”，为资源提供标记（可选）。

4. 依次单击“查看 + 创建”和“创建”，以创建专用终结点请求。

5. DPS 所有者将在 DPS 网络选项卡上的“专用终结点连接”列表中看到专用终结点请求。在此页面上，所有者可以“批准”或“拒绝”专用终结点请求，如下所示。

    ![DPS 审批](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>专用终结点定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。



## <a name="next-steps"></a>后续步骤

使用以下链接详细了解 DPS 安全功能：

* [安全性](./concepts-service.md#attestation-mechanism)
* [TLS 1.2 支持](tls-support.md)