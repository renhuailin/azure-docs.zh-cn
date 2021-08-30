---
title: 对适用于 Azure Stack Edge Pro GPU 的 Blob 存储进行故障排除 | Microsoft Docs
description: 介绍如何排查适用于 Azure Stack Edge 设备的 Blob 存储的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 2a77d4992d5ee2e505f9e3f29112d45c3776d8d2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438755"
---
# <a name="troubleshoot-blob-storage-issues-for-an-azure-stack-edge-device"></a>排查适用于 Azure Stack Edge 设备的 Blob 存储的问题 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

此文章介绍如何排查适用于 Azure Stack Edge 设备的 Blob 存储的问题。 

## <a name="errors-for-blob-storage-on-device"></a>设备上 Blob 存储的错误 

下面列出了与适用于 Azure Stack Edge 设备的 Blob 存储相关的错误。

| **问题 / 错误** |  **分辨率** | 
|--------------------|-----------------|
|无法检索子资源。 其中一个 HTTP 标头的值的格式不正确。| 在“编辑”菜单中，选择“目标 Azure Stack API”。  然后，重启 Azure 存储资源管理器。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|检查是否已在以下路径将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件：`C:\Windows\System32\drivers\etc\hosts` (Windows)，或 `/etc/hosts` (Linux)。|
|无法检索子资源。<br> 详细信息：自签名证书 |将设备的 SSL 证书导入 Azure 存储资源管理器： <ol><li>[生成并下载证书](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)。</li><li>在“编辑”菜单中，选择“SSL 证书”，然后选择“导入证书”。</li></ol>|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达一分钟：<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|检查是否已在 `C:\Windows\System32\drivers\etc\hosts` 将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达一分钟：<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |将设备的 SSL 证书导入 Azure 存储资源管理器： <ol><li>[生成并下载证书](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)。</li><li>在“编辑”菜单中，选择“SSL 证书”，然后选择“导入证书”。</li></ol>|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟：<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |检查是否已在 `/etc/hosts` 将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟：<br>`Error parsing source location… The SSL connection could not be established`. |将设备的 SSL 证书导入 Azure 存储资源管理器： <ol><li>[生成并下载证书](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)。</li><li>在“编辑”菜单中，选择“SSL 证书”，然后选择“导入证书”。</li></ol>|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟：<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|检查是否已在 `/etc/hosts` 将终结点名称 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 添加到 hosts 文件。|
|AzCopy 命令在显示以下错误之前，似乎停止了响应，时间长达 20 分钟：`Error parsing source location… The SSL connection could not be established`。|将设备的 SSL 证书导入系统的证书存储中。 有关详细信息，请参阅[下载证书](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)。|
|其中一个 HTTP 标头的值的格式不正确。|Azure Stack Edge 不支持用于 Python 的 Microsoft Azure 存储库的已安装版本。 有关受支持的库版本，请参阅[受支持的 Azure 客户端库](azure-stack-edge-gpu-system-requirements-rest.md#supported-azure-client-libraries)。|
|… [SSL:CERTIFICATE_VERIFY_FAILED] …| 在运行 Python 之前，请将 REQUESTS_CA_BUNDLE 环境变量设置为 Base64 编码的 SSL 证书文件的路径（请参阅如何[下载证书](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)）。 例如，运行：<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>也可将证书添加到系统的证书存储，然后将此环境变量设置为该存储的路径。 例如，在 Ubuntu 上运行：<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`|
|连接超时。|登录设备，然后检查设备是否已解锁。 设备在重启后会保持锁定状态，直到有人登录为止。|
|无法创建或更新 storageaccount。 确保存储帐户的访问密钥有效。 如果需要，请更新设备上的密钥。|同步存储帐户密钥。 安装[此处](azure-stack-edge-gpu-manage-storage-accounts.md#sync-storage-keys)概述的步骤执行操作。|

## <a name="next-steps"></a>后续步骤

- [排查设备上传和刷新错误](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-device-upload-and-refresh-errors)。
