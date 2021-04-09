---
title: 使用 Azure PowerShell 在 Azure Stack Edge Mini R 设备上配置 VPN
description: 介绍如何使用 Azure PowerShell 脚本在 Azure Stack Edge Mini R 设备上配置 VPN，以创建 Azure 资源。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 9fa4c678a04342b47601f81ede7c49ab841f42ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630956"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>使用 Azure PowerShell 在 Azure Stack Edge Mini R 设备上配置 VPN

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN 选项为从 Azure Stack Edge Mini R 或 Azure Stack Edge Pro R 设备到 Azure 的基于 TLS 的数据活动提供了另一层的加密。 可以通过 Azure 门户或 Azure PowerShell 在 Azure Stack Edge Mini R 设备上配置 VPN。 

本文介绍使用 Azure PowerShell 脚本在 Azure Stack Edge Mini R 设备上配置点到站点 (P2S) VPN 以在云中创建配置所需的步骤。 Azure Stack Edge 设备上的配置是通过本地 UI 完成的。

## <a name="about-vpn-setup"></a>关于 VPN 设置

P2S VPN 网关连接用于创建从单个客户端计算机或 Azure Stack Edge Mini R 设备到虚拟网络的安全连接。 通过客户端计算机或设备启动 P2S 连接。 在这种情况下，P2S 连接使用 IKEv2 VPN，这是一种基于标准的 IPsec VPN 解决方案。

典型的工作流包括以下步骤：

1. 配置先决条件。
2. 在 Azure 上设置必要的资源。
    1. 创建并配置虚拟网络和所需子网。 
    2. 创建并配置 Azure VPN 网关（虚拟网络网关）。
    3. 设置 Azure 防火墙并添加网络和应用规则。
    4. 创建 Azure 路由表并添加路由。
    5. 在 VPN 网关中启用点到站点。
        1. 添加客户端地址池。
        2. 配置隧道类型。
        3. 配置身份验证类型。
        4. 创建证书。
        5. 上传证书。
    6. 下载电话簿。
3. 在设备的本地 Web UI 中设置 VPN。 
    1. 提供电话簿。
    2. 提供服务标记 (json) 文件。


以下部分提供了有关详细步骤。

## <a name="configure-prerequisites"></a>配置先决条件

- 你应该有权访问按照[安装 Azure Stack Edge Mini R 设备](azure-stack-edge-mini-r-deploy-install.md)中的说明安装的 Azure Stack Edge Mini R 设备。 此设备将建立与 Azure 的 P2S 连接。 

- 你应该有权访问在 Azure 中为 Azure Stack Edge 服务启用的有效 Azure 订阅。 使用此订阅在 Azure 中创建相应的资源，以管理 Azure Stack Edge Mini R 设备。  

- 你有权访问用于访问 Azure Stack Edge Mini R 设备的 Windows 客户端。 你将使用此客户端以编程方式在云中创建配置。

    1. 要在 Windows 客户端上安装所需版本的 PowerShell，请运行以下命令：

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. 若要连接到 Azure 帐户和订阅，请运行以下命令：

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        提供 Azure Stack Edge Mini R 设备正在使用的 Azure 订阅名称以配置 VPN。

    3. [下载在云中创建配置所需的脚本](https://aka.ms/ase-vpn-deployment)。 脚本将执行以下操作：
        
        - 创建 Azure 虚拟网络和以下子网：GatewaySubnet 和 AzureFirewallSubnet 。
        - 创建并配置 Azure VPN 网关。
        - 创建并配置 Azure 本地网络网关。
        - 在 Azure VPN 网关与本地网络网关之间创建并配置 Azure VPN 连接。
        - 创建 Azure 防火墙并添加网络规则和应用规则。
        - 创建 Azure 路由表并向其添加路由。

    4. 在要创建 Azure 资源的 Azure 门户中创建资源组。 在 Azure 门户中，前往服务列表，选择“资源组”，再选择“+ 添加”。 提供资源组的订阅信息和名称，然后选择“创建”。 如果转到此资源组，此时该资源组下不应有任何资源。

        ![Azure 资源组](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. 你需要为 Azure Stack Edge Mini R 设备提供一个 `.cer` 格式的 Base 64 编码的证书。 此证书应使用私钥作为 `pfx` 上传到 Azure Stack Edge 设备。 此证书还需要安装在尝试建立 P2S 连接的客户端上的存储的受信任根目录中。

## <a name="use-the-script"></a>使用脚本

首先修改 `parameters-p2s.json` 文件以输入参数。 接下来，使用已修改的 json 文件运行脚本。

以下部分介绍了这些步骤。

### <a name="download-service-tags-file"></a>下载服务标记文件

在下载脚本的文件夹中，可能已有一个 `ServiceTags.json` 文件。 如果没有，可以下载服务标记文件。

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>修改参数文件

第一步是修改 `parameters-p2s.json` 文件，并保存所做的更改。 

对于创建的 Azure 资源，需要提供以下名称：

|参数名称  |说明  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure 虚拟网络名称        |
|azureFirewalls_firewall_name     | Azure 防火墙名称        |
|routeTables_routetable_name     | Azure 路由表名称        |
|publicIPAddresses_VNGW_public_ip_name     | 虚拟网络网关的公共 IP 地址名称       |
|virtualNetworkGateways_VNGW_name    | Azure VPN 网关（虚拟网络网关）名称        |
|publicIPAddresses_firewall_public_ip_name     | Azure 防火墙的公共 IP 地址名称         |
|location     |这是要在其中创建虚拟网络的区域。 选择与设备关联的区域相同的区域。         |
|RouteTables_routetable_onprem_name| 这是帮助防火墙将数据包路由回 Azure Stack Edge 设备的附加路由表的名称。 此脚本将创建两个附加路由，并将默认子网和防火墙子网与此路由表相关联。|

为创建的 Azure 资源提供以下 IP 地址和地址空间，其中包括虚拟网络和关联的子网（默认子网、防火墙子网、网关子网）  。

|参数名称  |说明  |
|---------|---------|
|VnetIPv4AddressSpace    | 这是与虚拟网络关联的地址空间。 提供 Vnet IP 范围作为专用 IP 范围 (https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) 。     |
|DefaultSubnetIPv4AddressSpace    |这是与虚拟网络的 `Default` 子网相关联的地址空间。         |
|FirewallSubnetIPv4AddressSpace    |这是与虚拟网络的 `Firewall` 子网相关联的地址空间。          |
|GatewaySubnetIPv4AddressSpace    |这是与虚拟网络的 `GatewaySubnet` 相关联的地址空间。          |
|GatewaySubnetIPv4bgpPeeringAddress    | 这是为 BGP 通信保留的 IP 地址，它基于与虚拟网络的 `GatewaySubnet` 关联的地址空间。          |
|ClientAddressPool    | 此 IP 地址用于 Azure 门户的 P2S 配置中的地址池。         |
|PublicCertData     | VPN 网关使用公共证书数据对与其建立连接的 P2S 客户端进行身份验证。 若要获取证书数据，请安装根证书。 确保证书使用 Base 64 进行编码，且扩展名为 .cer。 打开此证书，并复制证书中连续一行中的 ==BEGIN CERTIFICATE== 和 ==END CERTIFICATE== 之间的文本。     |



### <a name="run-the-script"></a>运行脚本

请按照以下步骤使用修改后的 `parameters-p2s.json` 并运行脚本以创建 Azure 资源。

1. 运行 PowerShell。 切换到脚本所在的目录。

3. 运行该脚本。

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > 在此版本中，该脚本仅适用于美国东部位置。

    运行脚本时，需要输入以下信息：

    
    |参数  |说明  |
    |---------|---------|
    |位置     |这是必须在其中创建 Azure 资源的区域。         |
    |AzureAppRuleFilePath     | 这是 `appRule.json` 的文件路径。       |
    |AzureIPRangesFilePath     |这是在前面步骤中下载的服务标记 json 文件。         |
    |ResourceGroupName     | 这是在其下创建所有 Azure 资源的资源组的名称。        |
    |AzureDeploymentName    |这是 Azure 部署的名称。         |
    |NetworkRuleCollectionName            | 这是创建并添加到 Azure 防火墙的所有网络规则集合的名称。             |
    |优先级            | 这是分配给所有创建的网络和应用程序规则的优先级。              |
    |AppRuleCollectionName            |这是创建并添加到 Azure 防火墙的所有应用程序规则集合的名称。                |


    下面显示了示例输出。
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - 脚本运行大约需要 90 分钟。 确保在脚本开始之前登录到你的网络。
    > - 如果由于任何原因导致脚本会话失败，请确保删除资源组以删除在其下创建的所有资源。
  
    
    脚本完成后，将在脚本所在的同一文件夹中生成部署日志。


## <a name="verify-the-azure-resources"></a>验证 Azure 资源

成功运行脚本后，验证是否在 Azure 中创建了所有资源。 转到所创建的资源组。 应会看到以下资源：

![Azure 资源](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>下载 VPN 配置文件的电话簿

在此步骤中，你将下载设备的 VPN 配置文件。

1. 在 Azure 门户中，请前往资源组，然后选择在前面的步骤中创建的虚拟网络网关。

    ![Azure 虚拟网络网关](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. 转到“设置”>“点到站点配置”。 选择“下载 VPN 客户端”。

    ![启用 P2S 配置 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. 保存压缩的配置文件并在 Windows 客户端上提取该文件。

    ![启用 P2S 配置 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. 转到 WindowsAmd64 文件夹，然后提取 `.exe`：VpnClientSetupAmd64.exe。

    ![启用 P2S 配置 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. 创建一个临时路径。 例如：

    `C:\NewTemp\vnet\tmp`

4. 运行 PowerShell 并转到 `.exe` 所在的目录。 若要执行 `.exe`，请键入：

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. 临时路径将包含新的文件。 下面是示例输出：

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. .pbk 文件是 VPN 配置文件的电话簿。 你将在本地 UI 中使用它。


## <a name="vpn-configuration-on-the-device"></a>在设备上进行 VPN 配置

在 Azure Stack Edge 设备的本地 UI 上执行以下步骤。

1. 在本地 UI 中，转到 VPN 页面。 在“VPN 状态”下，选择“配置”。

    ![配置 VPN 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. 在“配置 VPN”边栏选项卡中：
    
    1. 在“上传电话簿文件”中，指向在之前步骤中创建的 .pbk 文件。
    2. 在“上传公共 IP 列表配置文件”中，提供 Azure 数据中心 IP 范围 JSON 文件作为输入。 你已在前面的步骤中下载了此文件，网址为 [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519)。
    3. 选择“eastus”作为区域，然后选择“应用” 。

    ![配置 VPN 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. 在“仅使用 VPN 访问的 IP 地址范围”部分，输入已为 Azure 虚拟网络选择的 Vnet IPv4 范围。

    ![配置 VPN 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>验证客户端连接

1. 在 Azure 门户中，转到 VPN 网关。
2. 转到“设置”>“点到站点配置”。 在“已分配的 IP 地址”下，应显示 Azure Stack Edge 设备的 IP 地址。

## <a name="validate-data-transfer-through-vpn"></a>验证通过 VPN 传输数据

要确认 VPN 能够正常工作，请将数据复制到 SMB 共享。 请按照在 Azure Stack Edge 设备上[添加共享](azure-stack-edge-gpu-manage-shares.md#add-a-share)中的步骤操作。 

1. 将一个文件（例如 \data\pictures\waterfall.jpg）复制到在客户端系统上安装的 SMB 共享中。 
2. 若要在复制数据时验证数据是否通过 VPN 传输，请执行以下操作：

    1. 在 Azure 门户中，转到 VPN 网关。 

    2. 转到“监视”>“指标”。

    3. 在右侧窗格中，选择“作用域”作为 VPN 网关，选择“指标”作为网关 P2S 带宽，并将“聚合”设置为平均。  

    4. 在复制数据时，带宽利用率会增加，数据复制完成后，带宽利用率会下降。

        ![Azure VPN 指标](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. 验证此文件是否显示在云中的存储帐户中。
 
## <a name="debug-issues"></a>调试问题

要调试任何问题，请使用以下命令：

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

示例输出如下所示：


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>后续步骤

[通过 Azure Stack Edge 设备上的本地 UI 配置 VPN](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)。