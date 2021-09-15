---
title: 教程：使用 Azure 虚拟 WAN 创建站点到站点连接
description: 了解如何使用 Azure 虚拟 WAN 来与 Azure 建立站点到站点 VPN 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/18/2021
ms.author: cherylmc
ms.openlocfilehash: dbc48719f2897c22717319e1e07b5b3b3146fe84
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638279"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建站点到站点连接

本教程介绍如何使用虚拟 WAN 通过 IPsec/IKE（IKEv1 和 IKEv2）VPN 连接来与 Azure 中的资源建立连接。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建虚拟 WAN
> * 创建中心
> * 创建站点到站点 VPN 网关
> * 创建站点
> * 将站点连接到中心
> * 将 VPN 站点连接到中心
> * 将 VNet 连接到中心
> * 下载配置文件
> * 查看或编辑 VPN 网关

> [!NOTE]
> 如果你有多个站点，则通常会使用[虚拟 WAN 合作伙伴](https://aka.ms/virtualwan)来创建此配置。 但是，如果你熟悉网络技术并能够熟练配置自己的 VPN 设备，则可以自行创建此配置。
>

:::image type="content" source="./media/virtual-wan-about/virtualwan.png" alt-text="屏幕截图显示虚拟 WAN 的网络关系图。":::

## <a name="prerequisites"></a>先决条件

在开始配置之前，请验证你是否符合以下条件：

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>创建虚拟 WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-hub"></a><a name="hub"></a>创建中心

中心是一种虚拟网络，可包含适用于站点到站点、ExpressRoute 或点到站点功能的网关。 在本教程中，首先填写虚拟中心的“基本信息”选项卡，然后在下一个部分中继续填写站点到站点选项卡。 请注意，可以创建空中心（不包含任何网关的中心），然后在将来添加网关（S2S、P2S、ExpressRoute 等）。 创建中心后，即使你没有在中心内附加任何站点或创建任何网关，也会对该中心收取费用。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-site-to-site-vpn-gateway"></a><a name="gateway"></a>创建站点到站点 VPN 网关

在本部分中，你会配置站点到站点连接设置，然后继续创建中心和 S2S VPN 网关。 创建中心和网关可能需要花费大约 30 分钟。

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]

## <a name="create-site"></a><a name="site"></a>创建站点

在本部分，你将创建站点。 站点对应于你的物理位置。 请根据需要创建任意数量的站点。 例如，如果你在纽约、伦敦和洛杉矶各有一个分支机构，请创建三个独立的站点。 这些站点包含本地 VPN 设备终结点。 在虚拟 WAN 中，每个虚拟中心最多可创建 1000 个站点。 如果有多个中心，则可以为每个中心创建 1000 个站点。 如果你有虚拟 WAN 合作伙伴 CPE 设备，请咨询这些合作伙伴以了解他们对 Azure 进行的自动化。 通常，自动化意味着只需执行简单的单击操作即可将大规模分支信息导出到 Azure 中，并设置从 CPE 到 Azure 虚拟 WAN VPN 网关的连接。 有关详细信息，请参阅[从 Azure 到 CPE 合作伙伴的自动化指南](virtual-wan-configure-automation-providers.md)。

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-vpn-site-to-hub"></a><a name="connectsites"></a>将 VPN 站点连接到中心

在本部分中，你会将 VPN 站点连接到中心。

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="vnet"></a>将 VNet 连接到中心

在本部分中，你会创建中心与 VNet 之间的连接。

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>下载 VPN 配置

使用 VPN 设备配置文件来配置本地 VPN 设备。 下面列出了基本步骤。 有关配置文件包含的内容以及如何配置 VPN 设备的信息有 

1. 导航到“虚拟中心”->“VPN (站点到站点)”页面。

1. 在“VPN (站点到站点)”页面顶部，单击“下载 VPN 配置” 。你会看到一系列消息，因为 Azure 会在资源组“microsoft-network-[location]”中创建一个存储帐户，其中，location 是 WAN 的位置。

1. 完成创建文件后，请单击相应的链接下载该文件。 若要了解该文件的内容，请参阅本部分中的[关于 VPN 设备服务配置文件](#config-file)。

1. 将配置应用到本地 VPN 设备。 有关详细信息，请参阅本部分中的 [VPN 设备配置](#vpn-device)。

1. 将配置应用于 VPN 设备之后，无需保留 Azure 创建的存储帐户。 可以删除该帐户。

### <a name="about-the-vpn-device-configuration-file"></a><a name="config-file"></a>关于 VPN 设备配置文件

设备配置文件包含配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSiteConfiguration** - 此部分表示当站点连接到虚拟 WAN 时设置的设备详细信息。 它包含分支设备的名称和公共 IP 地址。
* **vpnSiteConnections** - 此部分提供以下设置的信息：

    * 虚拟中心 VNet 的地址空间  。<br>示例：
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 已连接到中心的 VNet 的地址空间  。<br>示例：

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 虚拟中心 vpngateway 的 IP 地址。 由于 vpngateway 的每个连接由采用主动 - 主动配置的 2 个隧道构成，因此，此文件中列出了这两个 IP 地址。 在此示例中，可以看到为每个站点指定了“Instance0”和“Instance1”。<br>示例：

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * Vpngateway 连接配置详细信息，例如 BGP、预共享密钥等。PSK 是自动生成的预共享密钥。 始终可以在“概述”页中为自定义 PSK 编辑连接。
  
### <a name="example-device-configuration-file"></a>示例设备配置文件

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a><a name="vpn-device"></a>配置 VPN 设备

>[!NOTE]
> 如果正在使用虚拟 WAN 合作伙伴解决方案，则会自动进行 VPN 设备配置。 设备控制器将从 Azure 获取配置文件并应用于设备以设置与 Azure 的连接。 这意味着，无需知道如何手动配置 VPN 设备。
>

如需有关如何配置设备的说明，可以使用 [VPN 设备配置脚本页](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)中的说明，并注意以下事项：

* VPN 设备页上的说明不是针对虚拟 WAN 编写的，但你可以使用配置文件中的虚拟 WAN 值来手动配置 VPN 设备。
 
* 适用于 VPN 网关的可下载设备配置脚本并不适用于虚拟 WAN，因为配置不同。

* 新的虚拟 WAN 可以同时支持 IKEv1 和 IKEv2。

* 虚拟 WAN 可以使用基于策略和基于路由的 VPN 设备和设备说明。

## <a name="view-or-edit-gateway-settings"></a><a name="gateway-config"></a>查看或编辑网关设置

你可以随时通过导航到“虚拟中心”->“VPN (站点到站点)”并选择“查看/配置”来查看和编辑 VPN 网关设置 。

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="显示“VPN (站点到站点)”页的屏幕截图，其中箭头指向“查看/配置”操作。" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

在“编辑 VPN 网关”页上，可以看到以下设置：

* 公共 IP 地址：由 Azure 分配。
* 专用 IP 地址：由 Azure 分配。
* 默认 BGP IP 地址：由 Azure 分配。
* 自定义 BGP IP 地址：此字段保留给 APIPA（自动专用 IP 寻址）。 Azure 支持介于 169.254.21.* 与 169.254.22.* 范围之间的 BGP IP。 Azure 接受这些范围内的 BGP 连接，但会使用默认的 BGP IP 拨号连接。

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="屏幕截图显示“编辑 VPN 网关”页，并突出显示“编辑”按钮。" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>清理资源

当不再需要所创建的资源时，请将其删除。 由于存在依赖关系，必须按特定顺序删除某些虚拟 WAN 资源。 大约需要 30 分钟才能完成删除。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>后续步骤

接下来，若要详细了解虚拟 WAN，请参阅：

> [!div class="nextstepaction"]
> * [虚拟 WAN 常见问题解答](virtual-wan-faq.md)
