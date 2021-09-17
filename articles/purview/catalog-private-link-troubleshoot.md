---
title: 对 Purview 帐户的专用终结点配置进行故障排除
description: 本文介绍如何排除与专用终结点配置相关的 Purview 帐户问题
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/02/2021
ms.openlocfilehash: 89627b97a5c2e8ae068db18583fab38aeb6fe5f6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434549"
---
# <a name="troubleshooting-private-endpoint-configuration-for-purview-accounts"></a>对 Purview 帐户的专用终结点配置进行故障排除

本指南总结与使用 Azure Purview 专用终结点相关的已知限制，并列出排除某些最常见相关问题的步骤和解决方案。 


## <a name="known-limitations"></a>已知的限制

- 我们当前不支持用于 AWS 源的引入专用终结点。
- 不支持使用自承载集成运行时扫描 Azure 多源。
- 不支持使用 Azure 集成运行时扫描专用终结点后面的数据源。
- 引入专用终结点可使用 Azure 门户，通过前面步骤中介绍的 Azure Purview 门户体验进行创建。 无法从专用链接中心创建。
- 当 Azure 专用 DNS 区域与专用终结点在不同订阅中时，不支持通过 Azure Purview 门户体验在现有 Azure DNS 区域内创建引入专用终结点的 DNS A 记录。 可以在其他订阅中的目标 DNS 区域中手动添加 A 记录。 
- 自承载集成运行时计算机必须部署在其中部署 Azure Purview 引入专用终结点的 VNet 中。
- 目前不支持扫描其专用终结点配置有阻止公共访问权限的 Power BI 租户。
- 有关专用链接服务的相关限制，请参阅 [Azure 专用链接限制](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)。

## <a name="recommended-troubleshooting-steps"></a>建议的故障排除步骤  

1. 在部署 Purview 帐户的专用终结点后，查看 Azure 环境以确保成功部署专用终结点资源。 根据自己的方案，必须在 Azure 订阅中部署以下一个或多个 Azure 专用终结点：

    |专用终结点  |专用终结点的目标分配对象 | 示例|
    |---------|---------|---------|
    |帐户  |Azure Purview 帐户         |mypurview-private-account  |
    |门户     |Azure Purview 帐户         |mypurview-private-portal  |
    |引流     |托管存储帐户 (Blob)         |mypurview-ingestion-blob  |
    |引流     |托管存储帐户（队列）         |mypurview-ingestion-queue  |
    |引流     |托管事件中心命名空间         |mypurview-ingestion-namespace  |

2. 如果已部署门户专用终结点，确保同时部署帐户专用终结点。

3. 如果已部署门户专用终结点，并在 Azure Purview 帐户中将“公用网络访问”设置为“拒绝”，则确保从内部网络启动 Azure Purview Studio。 
  <br>
    - 若要验证名称解析是否正确，可使用 NSlookup.exe 命令行工具查询 `web.purview.azure.com`。 结果必须返回一个属于门户专用终结点的专用 IP 地址。 
    - 若要验证网络连接，可以使用任何网络测试工具测试端口 443 的 `web.purview.azure.com` 终结点出站连接。 连接必须成功。    

3. 如果使用 Azure 专用 DNS 区域，则确保已部署所需 Azure DNS 区域，并且每个专用终结点都有 DNS (A) 记录。

4. 测试从管理计算机到 Purview 终结点和 Purview Web URL 的网络连接和名称解析。 如果已部署帐户和门户专用终结点，则必须通过专用 IP 地址解析终结点。


    ```powershell
    Test-NetConnection -ComputerName web.purview.azure.com -Port 443
    ```

    通过专用 IP 地址成功建立出站连接的示例：

    ```
    ComputerName     : web.purview.azure.com
    RemoteAddress    : 10.9.1.7
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName purview-test01.purview.azure.com -Port 443
    ```

    通过专用 IP 地址成功建立出站连接的示例：

    ```
    ComputerName     : purview-test01.purview.azure.com
    RemoteAddress    : 10.9.1.8
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```
    
5. 如果已在 2021 年 8 月 18 日之后创建 Azure Purview 帐户，则确保从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并安装最新版本的自承载集成运行时。
   
6. 测试从自承载集成运行时虚拟机到 Purview 终结点的网络连接和名称解析。

7. 通过端口 443 和专用 IP 地址，测试从自承载集成运行时到 Azure Purview 受管理资源（如 blob 队列和事件中心）的网络连接和名称解析。 （将托管存储帐户和事件中心命名空间替换为分配给 Azure Purview 帐户的相应受管理资源名称）。

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.blob.core.windows.net -Port 443
    ```
    通过专用 IP 地址与托管 blob 存储成功建立出站连接的示例：

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.6
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.queue.core.windows.net -Port 443
    ```
    通过专用 IP 地址与托管队列存储成功建立出站连接的示例：

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.5
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `Atlas-1225cae9-d651-4039-86a0-b43231a17a4b`.servicebus.windows.net -Port 443
    ```
    通过专用 IP 地址与事件中心命名空间成功建立出站连接的示例：

    ```
    ComputerName     : Atlas-1225cae9-d651-4039-86a0-b43231a17a4b.servicebus.windows.net
    RemoteAddress    : 10.15.1.4
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

8. 测试从数据源所在网络到 Purview 终结点和受管理资源终结点的网络连接和名称解析。

9.  如果数据源位于本地网络中，则查看 DNS 转发器配置。 测试从数据源所在的网络到自承载集成运行时、Azure Purview 终结点和受管理资源的名称解析。 应通过每个终结点的 DNS 查询获取有效的专用 IP 地址。
    
    有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)中的[不带自定义 DNS 服务器的虚拟网络工作负载](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)和[使用 DNS 转发器的本地工作负载](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)方案。

10. 如果已在本地网络中部署管理计算机和自承载集成运行时虚拟机，并已在自己的环境中设置 DNS 转发器，则在自己的环境中验证 DNS 和网络设置。 

11. 如果使用引入专用终结点，则确保自承载集成运行时已在 Purview 帐户中成功注册，并同时在自承载集成运行时虚拟机和 Azure Purview Studio 中显示为正在运行。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="issue"></a>问题 
运行扫描时，可能会收到以下错误消息：

  ```Internal system error. Please contact support with correlationId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx System Error, contact support.```

### <a name="cause"></a>原因 
这可能指示运行自承载集成运行时的虚拟机与 Azure Purview 受管理资源存储帐户或事件中心之间存在连接或名称解析相关问题。

### <a name="resolution"></a>解决方法 
验证运行自承载集成运行时的虚拟机的名称解析。


### <a name="issue"></a>问题 
运行新扫描时，可能会收到以下错误消息：

  ```message: Unable to setup config overrides for this scan. Exception:'Type=Microsoft.WindowsAzure.Storage.StorageException,Message=The remote server returned an error: (404) Not Found.,Source=Microsoft.WindowsAzure.Storage,StackTrace= at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndExecuteAsync[T](IAsyncResult result)```

### <a name="cause"></a>原因 
这可能指示运行的自承载集成运行时的版本较旧。 如果已在 2021 年 8 月 18 日之后创建 Azure Purview 帐户，则需要使用自承载集成运行时 5.9.7885.3 版。

### <a name="resolution"></a>解决方法 
将自承载集成运行时升级到 5.9.7885.3 版。


### <a name="issue"></a>问题 
带有专用终结点的 Azure Purview 帐户部署在部署期间因 Azure Policy 验证错误失败。

### <a name="cause"></a>原因
此错误表明 Azure 订阅中存在的现有 Azure Policy 分配可能会阻止部署任何所需 Azure 资源。 

### <a name="resolution"></a>解决方法 
查看现有 Azure Policy 分配，确保在 Azure 订阅中允许部署以下 Azure 资源。 
   
> [!NOTE]
> 根据自己的方案，可能需要部署以下一个或多个 Azure 资源类型： 
>  -   Azure Purview (Microsoft.Purview/Accounts)
>  -   专用终结点 (Microsoft.Network/privateEndpoints)
>  -   专用 DNS 区域 (Microsoft.Network/privateDnsZones)
>  -   事件中心命名空间 (Microsoft.EventHub/namespaces)
>  -   存储帐户 (Microsoft.Storage/storageAccounts)


### <a name="issue"></a>问题
无权访问此 Purview 帐户。 此 Purview 帐户位于专用终结点后面。 请从为 Purview 帐户的专用终结点配置的虚拟网络 (VNet) 中的客户端访问该帐户。

### <a name="cause"></a>原因 
用户尝试从公共终结点或使用“公用网络访问”设置为“拒绝”的 Azure Purview 公共终结点连接到 Azure Purview。

### <a name="resolution"></a>解决方法
在这种情况下，若要打开 Azure Purview Studio，请使用在 Azure Purview 门户专用终结点所在的虚拟网络中部署的计算机，或者使用已连接到允许混合连接的企业网络的虚拟机。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出或者无法解决，请访问以下任一通道以获取支持：

- 通过 [Microsoft Q&A](/answers/topics/azure-purview.html) 获得专家提供的答案。
- 联系 [@AzureSupport](https://twitter.com/azuresupport)。 Twitter 上的 Microsoft Azure 官方资源可以将 Azure 社区与适当的解答、支持和专家关联，以改进客户体验。
- 如果仍需要帮助，请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)，并选择“提交支持请求”。
