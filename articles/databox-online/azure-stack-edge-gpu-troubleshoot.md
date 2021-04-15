---
title: 使用 Azure 门户排查 Azure Stack Edge Pro GPU 问题 | Microsoft Docs
description: 介绍如何排查 Azure Stack Edge Pro GPU 问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 87e75d771c2cc269eaae81c2433f445eb65a17a9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314144"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>排查 Azure Stack Edge Pro GPU 设备问题 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何排查 Azure Stack Edge Pro GPU 设备上的问题。 


## <a name="run-diagnostics"></a>运行诊断

若要诊断和排查任何设备错误，可以运行诊断测试。 在设备的本地 Web UI 中执行以下步骤，以运行诊断测试。

1. 在本地 Web UI 中，转到“故障排除”>“诊断测试”。 选择要运行的测试，然后选择“运行测试”。 此测试会诊断网络、设备、Web 代理、时间或云设置可能存在的任何问题。 你将收到设备正在运行测试的通知。

    ![选择测试 ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. 测试完成后，会显示结果。 

    ![查看测试结果](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    如果未通过某项测试，会显示建议的措施的 URL。 选择 URL 查看建议的操作。
 
    ![查看未通过测试的警告](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>收集支持包

日志包由所有可帮助 Microsoft 支持部门排查任何设备问题的相关日志组成。 可以通过本地 Web UI 生成日志包。

执行以下步骤收集支持包。 

1. 在本地 Web UI 中，转到“故障排除”>“支持”。 选择“创建支持包”。 系统随即开始收集支持包。 收集包可能需要几分钟时间。

    ![选择“添加用户”](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. 创建支持包后，选择“下载支持包”。 压缩包将下载到所选的路径。 可以解压缩该包并查看系统日志文件。

    ![选择“添加用户 2”](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>收集高级安全日志

高级安全日志可以是 Azure Stack Edge Pro 设备的软件或硬件入侵日志。

### <a name="software-intrusion-logs"></a>软件入侵日志

会针对入站和出站流量收集软件入侵或默认防火墙日志。 

- 当设备在工厂进行映像时，会启用默认防火墙日志记录。 当你通过本地 UI 或通过设备的 Windows PowerShell 界面创建支持包时，这些日志默认会捆绑在支持包中。

- 如果支持包中仅需要防火墙日志来查看设备中的任何软件 (NW) 入侵，请在创建支持包时使用 `-Include FirewallLog` 选项。 

- 如果未提供任何特定的包含选项，防火墙日志将默认会包含在支持包中。

- 在支持包中，防火墙日志是 `pfirewall.log`，位于根文件夹中。 下面是 Azure Stack Edge Pro 设备的软件入侵日志的一个示例。 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>硬件入侵日志

为了检测设备的任何硬件入侵，目前所有的机箱事件（如打开或关闭机箱）都会被记录下来。 

- 设备的系统事件日志是使用 `racadm` cmdlet 读取的。 然后，这些事件会经过筛选，将与机箱相关的事件筛选到 `HWIntrusion.txt` 文件中。

- 若要仅获取支持包中的硬件入侵日志，请在创建支持包时使用 `-Include HWSelLog` 选项。 

- 如果未提供任何特定的包含选项，硬件入侵日志将默认会包含在支持包中。

- 在支持包中，硬件入侵日志为 `HWIntrusion.txt`，位于根文件夹中。 下面是 Azure Stack Edge Pro 设备的硬件入侵日志的一个示例。 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>使用日志进行故障排除

相关错误文件中包含上传和刷新过程中遇到的任何错误。

1. 若要查看错误文件，请转到共享，然后选择该共享以查看内容。 


2. 选择“Microsoft Data Box Edge 文件夹”。 此文件夹包含两个子文件夹：

    - Upload 文件夹：包含上传错误的日志文件。
    - Refresh 文件夹：包含刷新期间发生的错误。

    下面是 refresh 文件夹中的日志文件示例。

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. 如果此文件中包含错误（在示例中已突出显示），请记下错误代码（在本例中为 16001）。 根据以下错误参考查找此错误代码的说明。

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>使用错误列表排查故障

错误列表根据标识的方案进行编译，可用于自行诊断和排查故障。 

## <a name="azure-resource-manager"></a>Azure 资源管理器

下面是在配置 Azure 资源管理器以访问设备时可能出现的错误。 

| **问题 / 错误** |  **分辨率** | 
|------------|-----------------|
|常规问题|<li>[验证 Edge 设备是否已正确配置](#verify-the-device-is-configured-properly)。<li> [验证客户端是否已正确配置](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment：发送请求时出错。<br>位置：line:1 char:1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|此错误表示无法访问 Azure Stack Edge Pro 设备或配置不正确。 验证是否正确配置了 Edge 设备和客户端。 有关指南，请参阅此表中的“常规问题”行。|
|服务返回了错误。 查看 InnerException 了解详情：基础连接已关闭：无法为 SSL/TLS 安全通道建立信任关系。 |   出现此错误的原因可能是一个或多个自带证书步骤未正确执行。 可以在[此处](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)找到指南。 |
|操作返回了无效状态代码“ServiceUnavailable” <br> 响应状态代码不指明成功：503（服务不可用）。 | 此错误可能是由以下任何一种状况导致的。<li>ArmStsPool 处于停止状态。</li><li>Azure 资源管理器/安全令牌服务网站均已关闭。</li><li>Azure 资源管理器群集资源已关闭。</li><br>注意：重新启动设备可能会解决这个问题，但你应收集支持包，以便进一步调试。|
|AADSTS50126：用户名或密码无效。<br>跟踪 ID：29317da9-52fc-4ba0-9778-446ae5625e5a<br>相关 ID：1b9752c4-8cbf-4304-a714-8a16527410f4<br>时间戳：2019-11-15 09:21:57Z：远程服务器返回了错误：(400) 错误的请求。<br>位置：line:1 char:1 |此错误可能是由以下任何一种状况导致的。<li>对于无效的用户名和密码，请按照[此处](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password)的步骤操作，然后使用正确的密码来验证客户是否从 Azure 门户更改了密码。<li>对于无效的租户 ID，租户 ID 是固定 GUID，应设置为 `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|connect-AzureRmAccount：AADSTS90056：资源已禁用或不存在。 请检查应用代码，确保为尝试访问的资源指定了确切的资源 URL。<br>跟踪 ID：e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>相关 ID：75c8ef5a-830e-48b5-b039-595a96488ff9 时间戳：2019-11-18 07:00:51Z：远程服务器返回了错误：(400) 错误 |`Add-AzureRmEnvironment` 命令中使用的资源终结点不正确。|
|无法从云中获取终结点。<br>请确保已连接网络。 错误详细信息：HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005)：超过了最大重试次数，URL：/metadata/endpoints?api-version=2015-01-01 (Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |此错误主要出现在 Mac/Linux 环境中，原因如下：<li>未将 PEM 格式证书添加到 Python 证书存储中。</li> |

### <a name="verify-the-device-is-configured-properly"></a>验证是否已正确配置设备

1. 从本地 UI 验证是否已正确配置设备网络。

2. 验证是否已为所有终结点更新证书，如[此处](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)所述。

3. 从本地 UI 的“设备”页获取 Azure 资源管理器管理和登录终结点。

4. 验证是否在 Azure 中激活并注册了设备。


### <a name="verify-the-client-is-configured-properly"></a>验证是否已正确配置客户端

1. 验证是否安装了正确的 PowerShell 版本，如[此处](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client)所述。

2. 验证是否安装了正确的 PowerShell 模块，如[此处](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)所述。

3. 验证是否可访问 Azure 资源管理器和登录终结点。 可以尝试对终结点执行 ping 操作。 例如：

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   如果均无法访问，请按[此处](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)所述添加 DNS / 主机文件条目。
   
4. 验证是否已按[此处](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)所述安装客户端证书。

5. 如果客户使用的是 PowerShell，则应启用调试首选项以查看详细消息，方法是运行以下 PowerShell 命令。 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>设备上的 Blob 存储 

下面列出了与 Azure Stack Edge Pro / Data Box Gateway 设备上的 Blob 存储相关的错误。

| **问题 / 错误** |  **分辨率** | 
|--------------------|-----------------|
|无法检索子资源。 其中一个 HTTP 标头的值的格式不正确。| 在“编辑”菜单中，选择“目标 Azure Stack API”。  然后，重启 Azure 存储资源管理器。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|检查是否已在以下路径将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件：`C:\Windows\System32\drivers\etc\hosts` (Windows)，或 `/etc/hosts` (Linux)。|
|无法检索子资源。<br> 详细信息：自签名证书 |将设备的 SSL 证书导入 Azure 存储资源管理器： <ol><li>从 Azure 门户下载证书。 有关详细信息，请参阅[下载证书](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)。</li><li>在“编辑”菜单中，选择“SSL 证书”，然后选择“导入证书”。</li></ol>|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达一分钟：<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|检查是否已在 `C:\Windows\System32\drivers\etc\hosts` 将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达一分钟：<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |将设备的 SSL 证书导入系统的证书存储中。 有关详细信息，请参阅[下载证书](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟：<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |检查是否已在 `/etc/hosts` 将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟：<br>`Error parsing source location… The SSL connection could not be established`. |将设备的 SSL 证书导入系统的证书存储中。 有关详细信息，请参阅[下载证书](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟：<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|检查是否已在 `/etc/hosts` 将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟：`Error parsing source location… The SSL connection could not be established`。|将设备的 SSL 证书导入系统的证书存储中。 有关详细信息，请参阅[下载证书](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)。|
|其中一个 HTTP 标头的值的格式不正确。|Data Box 不支持用于 Python 的 Microsoft Azure 存储库的已安装版本。 请查看 Azure Data Box Blob 存储要求，了解支持的版本。|
|… [SSL:CERTIFICATE_VERIFY_FAILED] …| 在运行 Python 之前，请将 REQUESTS_CA_BUNDLE 环境变量设置为 Base64 编码的 SSL 证书文件的路径（请参阅如何[下载证书](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)）。 例如：<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>也可将证书添加到系统的证书存储，然后将此环境变量设置为该存储的路径。 例如，在 Ubuntu 上为：<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|连接超时。|登录到 Azure Stack Edge Pro，然后检查它是否已解锁。 设备在重启后会保持锁定状态，直到有人登录为止。|

## <a name="troubleshoot-iot-edge-errors"></a>排查 IoT Edge 错误

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>后续步骤

- 详细了解如何[排查设备激活问题](azure-stack-edge-gpu-troubleshoot-activation.md)。
