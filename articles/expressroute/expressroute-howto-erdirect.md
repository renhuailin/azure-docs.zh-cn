---
title: Azure ExpressRoute：配置 ExpressRoute Direct
description: 了解如何使用 Azure PowerShell 将 Azure ExpressRoute Direct 配置为直接连接到 Microsoft 全球网络。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 2c49fe65cf97207a495f6c0cc78585489a0db6ff
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228242"
---
# <a name="how-to-configure-expressroute-direct"></a>如何配置 ExpressRoute Direct

使用 ExpressRoute Direct，可以直接连接到 Microsoft 战略性分布在全球的对等互连位置的的全球网络。 有关详细信息，请参阅[关于 ExpressRoute Direct](expressroute-erdirect-about.md)。

## <a name="before-you-begin"></a>在开始之前

需要先注册订阅，然后才能使用 ExpressRoute Direct。 需要先注册订阅，然后才能使用 ExpressRoute Direct。 若要注册，请通过 Azure PowerShell 执行以下操作：
1.  登录到 Azure 并选择想要注册的订阅。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 使用以下命令注册公共预览版订阅：
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

注册后，验证 Microsoft.Network 资源提供程序是否已在你的订阅中注册。 通过注册资源提供程序来配置订阅，以供资源提供程序使用。

## <a name="create-the-resource"></a><a name="resources"></a>创建资源

1. 登录到 Azure 并选择订阅。 ExpressRoute Direct 资源和 ExpressRoute 线路必须位于同一订阅中。

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. 将你的订阅重新注册到 Microsoft.Network 以访问 expressrouteportslocation 和 expressrouteport API。

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. 列出支持 ExpressRoute Direct 的所有位置。
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **示例输出**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
4. 确定上面列出的某个位置是否有可用的带宽

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **示例输出**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
5. 根据上面选择的位置创建 ExpressRoute Direct 资源

   ExpressRoute Direct 同时支持 QinQ 和 Dot1Q 封装。 如果选择了 QinQ，则会动态为每个 ExpressRoute 线路分配一个 S-Tag，并且每个线路在整个 ExpressRoute Direct 资源中将是唯一的。 线路上的每个 C-Tag 在该线路上必须是唯一的，但在整个 ExpressRoute Direct 中不必唯一。  

   如果选择了 Dot1Q 封装，则必须在整个 ExpressRoute Direct 资源中管理 C-Tag (VLAN) 的唯一性。  

   > [!IMPORTANT]
   > ExpressRoute Direct 只能采用一种封装类型。 无法在创建 ExpressRoute Direct 后更改封装。
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > 封装属性还可以设置为 Dot1Q。 
   >

   **示例输出：**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>生成授权信 (LOA)

引用最近创建的 ExpressRoute Direct 资源，输入客户名称以将 LOA 写入其中，并（可选）定义用于存储文档的文件位置。 如果未引用文件路径，则文档将下载到当前目录。

  ```powershell 
   New-AzExpressRoutePortLOA -ExpressRoutePort $ERDirect -CustomerName TestCustomerName -Destination "C:\Users\SampleUser\Downloads" 
   ```
 **示例输出**

   ```powershell
   Written Letter of Authorization To: C:\Users\SampleUser\Downloads\LOA.pdf
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>更改链路的管理状态
   
应当使用此过程执行第 1 层测试，以确保每个交叉连接都已针对主端口和辅助端口正确设置到每台路由器中。
1. 获取 ExpressRoute Direct 详细信息。

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. 将链路设置为“已启用”。 重复此步骤以将每个链路设置为“已启用”。

   Links[0] 是主端口，Links[1] 是辅助端口。

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **示例输出：**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   使用相同的过程和 `AdminState = "Disabled"` 设置可关闭端口。

## <a name="create-a-circuit"></a><a name="circuit"></a>创建线路

默认情况下，可以在 ExpressRoute Direct 资源所在的订阅中创建 10 条线路。 此限制可以通过支持来提高。 你负责跟踪预配的和已利用的带宽。 预配的带宽是 ExpressRoute Direct 资源上所有线路的带宽总和，已利用的带宽是基础物理接口的物理利用率。

有额外的线路带宽可以在 ExpressRoute Direct 上使用，仅用于支持上面概述的场景。 这些带宽为 40 Gbps 和 100 Gbps。

SkuTier 可以为“本地”、“标准”或“高级”。

SkuFamily 只能为 MeteredData。 ExpressRoute Direct 不支持“无限制”。

在 ExpressRoute Direct 资源上创建一个线路。

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  其他带宽包括：5.0、10.0 和 40.0

  **示例输出：**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```
## <a name="delete-the-resource"></a>删除资源
在删除 ExpressRoute Direct 资源之前，首先需要删除在 ExpressRoute Direct 端口对上创建的所有 ExpressRoute 线路。
可以通过运行以下命令来删除 ExpressRoute Direct 资源：
 ```powershell
   Remove-azexpressrouteport -Name $Name -Resourcegroupname -$ResourceGroupName
   ```
## <a name="next-steps"></a>后续步骤

有关 ExpressRoute Direct 的详细信息，请参阅[概述](expressroute-erdirect-about.md)。
