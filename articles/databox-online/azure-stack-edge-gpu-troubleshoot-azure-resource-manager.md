---
title: 排查 Azure Stack Edge Pro GPU 上的 Azure 资源管理器配置问题 | Microsoft Docs
description: 介绍如何排查 Azure Stack Edge 设备上的 Azure 资源管理器配置问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: d8226d780c3caabb94f0780f3a4296765207f1b1
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987792"
---
# <a name="troubleshoot-azure-resource-manager-issues-on-an-azure-stack-edge-device"></a>排查 Azure Stack Edge 设备上的 Azure 资源管理器问题 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何排查可能会对 Azure Stack Edge 设备上的资源管理造成干扰的 Azure 资源管理器问题。 Azure 资源管理器提供一个管理层用于在 Azure 帐户中创建、更新和删除资源。
 
## <a name="azure-resource-manager-configuration-errors"></a>Azure 资源管理器配置错误

以下错误可能指示 Azure 资源管理器配置有问题。 

| **问题 / 错误** |  **分辨率** | 
|------------|-----------------|
|常规问题|<li>[验证是否已正确配置设备](#verify-the-device-is-configured-properly)。<li> [验证是否已正确配置客户端](#verify-the-client-is-configured-properly)。|
|Add-AzureRmEnvironment：发送请求时出错。<br>位置：line:1 char:1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|设备不可访问或配置不正确。 验证是否正确配置了设备和客户端。 有关指南，请参阅此表中的“常规问题”行。|
|服务返回了错误。 查看 InnerException 了解详情：基础连接已关闭：无法为 SSL/TLS 安全通道建立信任关系。 |  在设备上创建和安装证书时出错。 有关详细信息，请参阅[创建和安装证书](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)。 |
|操作返回了无效状态代码“ServiceUnavailable” <br> 响应状态代码不指明成功：503（服务不可用）。 | 此错误可能是由以下任何一种状况导致的：<li>ArmStsPool 处于停止状态。</li><li>Azure 资源管理器已关闭，或者安全令牌服务的网站已关闭。</li><li>Azure 资源管理器群集资源已关闭。</li><br>重启设备可能会解决问题。 若要进一步调试，请[收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)。|
|AADSTS50126：用户名或密码无效。<br>跟踪 ID：29317da9-52fc-4ba0-9778-446ae5625e5a<br>相关 ID：1b9752c4-8cbf-4304-a714-8a16527410f4<br>时间戳：2019-11-15 09:21:57Z：远程服务器返回了错误：(400) 错误的请求。<br>位置：line:1 char:1 |此错误可能是由以下任何一种状况导致的：<li>如果用户名和密码无效，请确保[在 Azure 门户中重置 Azure 存储管理器密码](./azure-stack-edge-gpu-set-azure-resource-manager-password.md)，然后使用正确的密码。<li>如果租户 ID 无效，请确保将租户 ID 设置为 `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|connect-AzureRmAccount：AADSTS90056：资源已禁用或不存在。 请检查应用代码，确保为尝试访问的资源指定了确切的资源 URL。<br>跟踪 ID：e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>相关 ID：75c8ef5a-830e-48b5-b039-595a96488ff9 时间戳：2019-11-18 07:00:51Z：远程服务器返回了错误：(400) 错误 |`Add-AzureRmEnvironment` 命令中使用的 Azure 资源管理器终结点不正确。<br>若要查找 Azure 资源管理器终结点，请在设备本地 Web UI 的“设备”页上查看“设备终结点” 。<br>有关 PowerShell 说明，请参阅[设置 Azure 资源管理器环境](azure-stack-edge-gpu-connect-resource-manager.md#step-7-set-azure-resource-manager-environment)。 |
|无法从云中获取终结点。<br>确保已建立网络连接。 错误详细信息：HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005)：超过了最大重试次数，URL：/metadata/endpoints?api-version=2015-01-01 (Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |此错误主要出现在 Mac 或 Linux 环境中。 出错的原因是未将 PEM 格式的证书添加到 Python 证书存储。 |


## <a name="troubleshoot-general-issues-with-azure-resource-manager"></a>排查 Azure 资源管理器的一般性问题

对于一般性的 Azure 资源管理器问题，请确保已正确配置设备和客户端。 有关端到端过程，请参阅[连接到 Azure Stack Edge Pro GPU 设备上的 Azure 资源管理器](azure-stack-edge-gpu-connect-resource-manager.md)。


### <a name="verify-the-device-is-configured-properly"></a>验证是否已正确配置设备

1. 从本地 UI 验证是否已正确配置设备网络。

2. [验证是否已更新所有终结点的证书](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)。

3. 从本地 UI 的“设备”页获取 Azure 资源管理器管理和登录终结点。

4. 验证是否在 Azure 中激活并注册了设备。


### <a name="verify-the-client-is-configured-properly"></a>验证是否已正确配置客户端

1. [验证是否安装了正确的 PowerShell 版本](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client)。

2. [验证是否安装了正确的 PowerShell 模块](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)。

3. 验证是否可访问 Azure 资源管理器和登录终结点。 可以尝试对终结点执行 ping 操作。 例如：

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   如果无法访问这些终结点，请[添加 DNS/主机文件条目](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)。
   
4. [验证是否已安装客户端证书](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)。

5. 如果使用的是 PowerShell，请运行以下 PowerShell 命令启用调试首选项，以查看详细消息： 

    `$debugpreference = "continue"`


## <a name="next-steps"></a>后续步骤

- [排查设备激活问题](azure-stack-edge-gpu-troubleshoot-activation.md)。
- [排查设备问题](azure-stack-edge-gpu-troubleshoot.md)。