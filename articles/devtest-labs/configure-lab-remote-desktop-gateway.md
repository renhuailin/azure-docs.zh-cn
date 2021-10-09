---
title: 将实验室配置为使用远程桌面网关
description: 了解如何在 Azure 开发测试实验室中配置一个使用远程桌面网关的实验室，以确保无需公开 RDP 端口即可安全访问实验室 VM。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: fcafc9f035d57e7685ffc3646cecaf27b60684f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652408"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>将 Azure 开发测试实验室中的实验室配置为使用远程桌面网关
在 Azure 开发测试实验室中，可为实验室配置远程桌面网关，以确保无需公开 RDP 端口即可安全访问实验室虚拟机 (VM)。 实验室提供了一个中心位置，以便实验室用户查看和连接他们有权访问的所有虚拟机。 “虚拟机”页上的“连接”按钮用于创建计算机特定的 RDP 文件，打开该文件可以连接到该计算机 。 可以通过将实验室连接到远程桌面网关来进一步自定义和保护 RDP 连接。 

此方法更安全，因为实验室用户将直接向网关计算机进行身份验证，或者可以使用已加入域的网关计算机上的公司凭据连接到其计算机。 实验室还支持使用令牌向网关计算机进行身份验证，这样，无需向 Internet 公开 RDP 端口，用户即可连接到其实验室虚拟机。 本文将通过一个示例逐步介绍如何设置使用令牌身份验证连接到实验室计算机的实验室。

## <a name="architecture-of-the-solution"></a>解决方案的体系结构

![解决方案的体系结构](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. 选择“连接”按钮时，会调用“[获取 RDP 文件内容](/rest/api/dtl/virtualmachines/getrdpfilecontents)”操作。 
1. “获取 RDP 文件内容”操作调用 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 来请求身份验证令牌。
    1. `{gateway-hostname}` 是在 Azure 门户中实验室的“实验室设置”页上指定的网关主机名。 
    1. `{lab-machine-name}` 是你尝试连接的计算机的名称。
    1. `{port-number}` 是需要在其上建立连接的端口。 通常，此端口为 3389。 如果实验室 VM 正在使用开发测试实验室中的[共享 IP](devtest-lab-shared-ip.md) 功能，则端口将不是 3389。
1. 远程桌面网关将来自 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 的调用提交给某个 Azure 函数，以生成身份验证令牌。 开发测试实验室服务自动在请求头中包含函数密钥。 函数密钥将保存到实验室的密钥保管库中。 该机密的名称将在实验室的“实验室设置”页上显示为“网关令牌机密” 。
1. 该 Azure 函数应会返回用于对网关计算机进行基于证书的令牌身份验证的令牌。  
1. 然后，“获取 RDP 文件内容”操作返回完整的 RDP 文件，其中包含身份验证信息。
1. 请使用你偏好的 RDP 连接程序打开该 RDP 文件。 请记住，并非所有 RDP 连接程序都支持令牌身份验证。 身份验证令牌具有一个由函数应用设置的到期日期。 请在令牌到期之前与实验室 VM 建立连接。
1. 远程桌面网关计算机对 RDP 文件中的令牌进行身份验证后，该连接将转发到实验室计算机。

### <a name="solution-requirements"></a>解决方案要求
若要使用开发测试实验室令牌身份验证功能，网关计算机、域名服务 (DNS) 和函数的配置需要满足几项要求。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>远程桌面网关计算机的要求
- 必须在网关计算机上安装 TLS/SSL 证书以处理 HTTPS 流量。 如果只有一台计算机，则该证书必须与网关场负载均衡器的完全限定域名 (FQDN) 或者该计算机本身的 FQDN 相匹配。 不能使用通配符 TLS/SSL 证书。  
- 已在网关计算机上安装一个签名证书。 使用 [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) 脚本创建签名证书。
- 安装支持远程桌面网关令牌身份验证的[可插式身份验证](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)模块。 此类模块的一个示例是 [System Center Virtual Machine Manager (VMM) 映像](/system-center/vmm/install-console?view=sc-vmm-1807&preserve-view=true)随附的 `RDGatewayFedAuth.msi`。 有关 System Center 的详细信息，请参阅 [System Center 文档](/system-center/)和[定价详细信息](https://www.microsoft.com/cloud-platform/system-center-pricing)。  
- 网关服务器可以处理对 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 发出的请求。

    如果只有一台计算机，则 gateway-hostname 是网关场负载均衡器的 FQDN，或该计算机本身的 FQDN。 `{lab-machine-name}` 是你尝试连接的实验室计算机的名称，`{port-number}` 是要在其上建立连接的端口。  此端口默认为 3389。  但是，如果虚拟机正在使用开发测试实验室中的[共享 IP](devtest-lab-shared-ip.md) 功能，则端口将不是 3389。
- 可以使用 Internet Information Server (IIS) 的[应用程序路由请求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)模块将 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 请求重定向到某个 Azure 函数，该函数将处理此请求，以获取用于身份验证的令牌。


## <a name="requirements-for-azure-function"></a>Azure 函数的要求
Azure 函数处理 `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` 格式的请求，并基于网关计算机上安装的相同签名证书返回身份验证令牌。 `{function-app-uri}` 是用于访问函数的 URI。 函数密钥将自动在请求头中传递。 有关示例函数，请参阅 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)。 


## <a name="requirements-for-network"></a>网络要求

- 与网关计算机上安装的 TLS/SSL 证书关联的 FQDN 的 DNS 必须将流量定向到网关计算机，或网关计算机场的负载均衡器。
- 如果实验室计算机使用专用 IP，则必须存在一条从网关计算机到实验室计算机的网络路径，可以通过共享同一个虚拟网络或使用对等互连的虚拟网络来建立该路径。

## <a name="configure-the-lab-to-use-token-authentication"></a>将实验室配置为使用令牌身份验证 
本部分介绍如何将实验室配置为使用支持令牌身份验证的远程桌面网关计算机。 本部分不介绍如何设置远程桌面网关场本身。 有关此操作的信息，请参阅本文最后的[创建远程桌面网关的示例](#sample-to-create-a-remote-desktop-gateway)部分。 

在更新实验室设置之前，请在实验室的密钥保管库中存储成功执行函数（以返回身份验证令牌）所需的密钥。 可以在 Azure 门户中函数的“管理”页上获取函数密钥值。 有关如何在密钥保管库中保存机密的详细信息，请参阅[将机密添加到密钥保管库](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)。 保存机密的名称供稍后使用。

若要查找实验室密钥保管库的 ID，请运行以下 Azure CLI 命令： 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

使用以下步骤将实验室配置为使用令牌身份验证：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 在实验室列表中选择你的实验室。
1. 在实验室的页上，选择“配置和策略”。
1. 在左侧菜单中的“设置”部分，选择“实验室设置” 。
1. 在“远程桌面”部分的“网关主机名”字段中，输入远程桌面服务网关计算机或场的完全限定域名 (FQDN) 或 IP 地址 。 此值必须与网关计算机上使用的 TLS/SSL 证书的 FQDN 相匹配。

    ![实验室设置中的“远程桌面”选项](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 在“远程桌面”部分，对于“网关令牌机密”，请输入先前创建的机密的名称 。 此值不是函数密钥本身，而是保存了函数密钥的实验室密钥保管库中的机密名称。

    ![实验室设置中的“网关令牌机密”](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. 保存更改。

    > [!NOTE] 
    > 单击“保存”即表示你同意[远程桌面网关的许可条款](https://www.microsoft.com/licensing/product-licensing/products)。 有关远程网关的详细信息，请参阅[欢迎使用远程桌面服务](/windows-server/remote/remote-desktop-services/Welcome-to-rds)和[部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。


如果你偏向于通过自动化配置实验室，请参阅 [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1)，其中提供了一个用于设置“网关主机名”和“网关令牌机密”的示例 PowerShell 脚本 。 [Azure 开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab)还提供了一个 Azure 资源管理器模板，用于创建或更新具有“网关主机名”和“网关令牌机密”设置的实验室 。

## <a name="configure-network-security-group"></a>配置网络安全组
若要进一步保护实验室，可将网络安全组 (NSG) 添加到实验室虚拟机使用的虚拟网络。 有关如何设置 NSG 的说明，请参阅[创建、更改或删除网络安全组](../virtual-network/manage-network-security-group.md)。

以下示例 NSG 只允许最先通过网关的流量进入实验室计算机。 此规则中的源是单个网关计算机的 IP 地址，或者网关计算机前面的负载均衡器的 IP 地址。

![网络安全组 - 规则](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>创建远程桌面网关的示例

> [!NOTE] 
> 使用示例模板即表示你同意[远程桌面网关的许可条款](https://www.microsoft.com/licensing/product-licensing/products)。 有关远程网关的详细信息，请参阅[欢迎使用远程桌面服务](/windows-server/remote/remote-desktop-services/Welcome-to-rds)和[部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

[Azure 开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab)提供了几个示例，用于帮助设置在开发测试实验室中使用令牌身份验证和远程桌面网关所需的资源。 这些示例包括用于网关计算机、实验室设置和函数应用的 Azure 资源管理器模板。

请遵循以下步骤设置远程桌面网关场的示例解决方案。

1. 创建签名证书。  运行 [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)。 保存创建的证书的指纹、密码和 Base64 编码。
2. 获取 TLS/SSL 证书。 与 TLS/SSL 证书关联的 FQDN 必须是你控制的域的 FQDN。 保存此证书的指纹、密码和 Base64 编码。 若要使用 PowerShell 获取指纹，请使用以下命令。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import('path-to-certificate');
    $hash = $cer.GetCertHashString()
    ```

    若要使用 PowerShell 获取 Base64 编码，请使用以下命令。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes('path-to-certificate'))
    ```
3. 从 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) 下载文件。

    该模板需要访问位于同一基 URI 处的其他几个资源管理器模板和相关资源。 将从 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) 下载的所有文件以及 RDGatewayFedAuth.msi 复制到存储帐户中的 Blob 容器。  
4. 部署从 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) 下载的 azuredeploy.json。 此模板采用以下参数：
    - adminUsername – 必需。  网关计算机的管理员用户名。
    - adminPassword – 必需。 网关计算机的管理员帐户的密码。
    - instanceCount – 要创建的网关计算机数。  
    - alwaysOn – 指示是否将创建的 Azure Functions 应用保持热状态。 保留 Azure Functions 应用可以避免当用户首次尝试连接到其实验室 VM 时出现延迟，但确实会产生费用。  
    - tokenLifetime – 创建的令牌的有效期。 格式为 HH:MM:SS。
    - sslCertificate – 网关计算机的 TLS/SSL 证书的 Base64 编码。
    - sslCertificatePassword – 网关计算机的 TLS/SSL 证书的密码。
    - sslCertificateThumbprint - 用于在 TLS/SSL 证书的本地证书存储中提供标识的证书指纹。
    - signCertificate – 网关计算机的签名证书的 Base64 编码。
    - signCertificatePassword – 网关计算机的签名证书的密码。
    - signCertificateThumbprint – 用于在签名证书的本地证书存储中提供标识的证书指纹。
    - _artifactsLocation – 可在其中找到所有支持资源的 URI 位置。 此值必须是完全限定的 UIR，而不是相对路径。
    - _artifactsLocationSasToken – 用于访问支持资源的共享访问签名 (SAS) 令牌（如果位置是 Azure 存储帐户）。

    可以在 Azure CLI 中使用以下命令部署模板：

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    下面是参数说明：

    - 可以运行 `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` 获取 {storage-account-endpoint}。  {storage-acct-name} 是保存上传文件的存储帐户的名称。  
    - {container-name} 是 {storage-acct-name} 中用于保存上传文件的容器的名称。  
    - 可以运行 `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` 获取 {sas-token}。 
        - {storage-acct-name} 是保存上传文件的存储帐户的名称。  
        - {container-name} 是 {storage-acct-name} 中用于保存上传文件的容器的名称。  
        - {utc-expiration-date} 是 SAS 令牌的到期日期 (UTC)，该日期过后，不再可以使用该 SAS 令牌访问存储帐户。

    请记下模板部署输出中的 gatewayFQDN 和 gatewayIP 值。 此外，需要保存新建函数的函数密钥值，该值可以在“[函数应用设置](../azure-functions/functions-how-to-use-azure-function-app-settings.md)”选项卡中找到。
5. 配置 DNS，以便 TLS/SSL 证书的 FQDN 定向到在上一步骤中记下的 gatewayIP 的 IP 地址。

    创建远程桌面网关场并做出相应的 DNS 更新后，该场便可供开发测试实验室中的实验室使用了。 “网关主机名”和“网关令牌机密”设置必须配置为使用部署的网关计算机 。 

    > [!NOTE]
    > 如果实验室计算机使用专用 IP，则必须存在一条从网关计算机到实验室计算机的网络路径，可以通过共享同一个虚拟网络或使用对等互连的虚拟网络来建立该路径。

    配置网关和实验室后，在实验室用户单击“连接”时创建的连接文件将自动包含使用令牌身份验证进行连接所需的信息。     

## <a name="next-steps"></a>后续步骤
请参阅以下文章来详细了解远程桌面服务：[远程桌面服务文档](/windows-server/remote/remote-desktop-services/Welcome-to-rds)