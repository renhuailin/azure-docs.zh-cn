---
title: 在 Azure Stack Edge Pro GPU 上使用证书 | Microsoft Docs
description: 描述如何在 Azure Stack Edge Pro GPU 设备上使用证书，包括使用原因、证书类型以及在设备上上传证书的方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: 15cfd2b7188f84e14aa12824f8d5fab06d226330
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363920"
---
# <a name="upload-import-and-export-certificates-on-azure-stack-edge-pro-gpu"></a>在 Azure Stack Edge Pro GPU 上上传、导入、导出证书

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

若要确保 Azure Stack Edge 设备与连接到它的客户端之间的安全可信通信，可以使用自签名证书或自带证书。 本文介绍如何管理这些证书，包括如何上传、导入、导出这些证书或查看其到期日期。

要详细了解如何创建这些证书，请参阅[使用 Azure PowerShell 创建证书](azure-stack-edge-gpu-create-certificates-powershell.md)。


## <a name="upload-certificates-on-your-device"></a>在设备上上传证书

如果自带证书，则为设备创建的证书默认位于客户端的“个人存储”中。 这些证书需要在你的客户端上导出为合适的格式文件，然后才能上传到设备。


### <a name="prerequisites"></a>先决条件

将根证书和终结点证书上传到设备之前，请确保以适当的格式导出证书。

- 根证书必须导出为包含 `.cer` 扩展名的 DER 格式。 有关详细步骤，请参阅将[将证书导出为 DER 格式](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format)。
- 终结点证书必须导出为包含私钥的 .pfx 文件。 有关详细步骤，请参阅将[将证书导出为包含私钥的 .pfx 文件](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key)。 

### <a name="upload-certificates"></a>上传证书 

要在设备上上传根证书和终结点证书，在本地 Web UI 中使用“证书”页上的“+ 添加证书”选项。 执行以下步骤：

1. 首先上传根证书。 在本地 Web UI 中，转到“证书”。
1. 选择“+ 添加证书”。

    ![添加签名链证书 1](media/azure-stack-edge-gpu-manage-certificates/add-cert-1.png)

1. 保存证书。

#### <a name="upload-endpoint-certificate"></a>上传终结点证书

1. 接下来上传终结点证书。 

    ![添加签名链证书 2](media/azure-stack-edge-gpu-manage-certificates/add-cert-2.png)

    选择 .pfx 格式的证书文件，并输入在导出证书时提供的密码。 Azure 资源管理器证书可能需要几分钟才会应用。

    如果未首先更新签名链，并且尝试上传终结点证书，则会出现错误。

    ![应用证书错误](media/azure-stack-edge-gpu-manage-certificates/apply-cert-error-1.png)

    返回并上传签名链证书，然后上传和应用终结点证书。

> [!IMPORTANT]
> 如果更改了设备名称或 DNS 域，则必须创建新证书。 然后，应将客户端证书和设备证书更新为新的设备名称和 DNS 域。 

#### <a name="upload-kubernetes-certificates"></a>上传 Kubernetes 证书

Kubernetes 证书可用于 Edge 容器注册表或 Kubernetes 仪表板。 在每种情况下，都必须上传证书和密钥文件。 请按照以下步骤创建和上传 Kubernetes 证书：


1. 你将使用 `openssl` 创建 Kubernetes 仪表板证书或 Edge 容器注册表。 请确保在用于创建证书的系统上安装 OpenSSL。 在 Windows 系统中，可以使用 Chocolatey 安装 `openssl`。 安装 Chocolatey 后，打开 PowerShell 并键入以下命令：
    
    ```powershell
    choco install openssl
    ```
1. 使用 `openssl` 创建这些证书。 将创建 `cert.pem` 证书文件和 `key.pem` 密钥文件。  

    - 对于 Edge 容器注册表，请使用以下命令：
    
        ```powershell
        openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=<ecr.endpoint-suffix>"
        ``` 
        下面是示例输出： 

        ```powershell
        PS C:\WINDOWS\system32> openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=ecr.dbe-1d6phq2.microsoftdatabox.com"
        Generating a RSA private key
        .....................++++....++++
        writing new private key to 'key.pem'
        -----
        PS C:\WINDOWS\system32>
        ```    
    - 对于 Kubernetes 仪表板证书，请使用以下命令：  
     
        ```powershell
        openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=<<kubernetes-dashboard.endpoint-suffix> OR <endpoint-suffix>>"
        ```
        下面是示例输出： 

        ```powershell
        PS C:\WINDOWS\system32> openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=kubernetes-dashboard.dbe-1d8phq2.microsoftdatabox.com"
        Generating a RSA private key
        .....................++++....++++
        writing new private key to 'key.pem'
        -----
        PS C:\WINDOWS\system32>
        ```          
1. 上传 Kubernetes 证书和之前生成的相应密钥文件。
    
    - 对于 Edge 容器注册表
    
        ![添加 Edge 容器注册表证书和密钥文件的屏幕截图](media/azure-stack-edge-gpu-manage-certificates/add-cert-3.png)      

    - 对于 Kubernetes 仪表板     

        ![添加 Kubernetes 仪表板证书和密钥文件的屏幕截图](media/azure-stack-edge-gpu-manage-certificates/add-cert-4.png) 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>在访问设备的客户端上导入证书

可使用设备生成的证书，或自带证书。 使用设备生成的证书时，必须先在客户端上下载证书，然后才能将其导入到相应的证书存储中。 请参阅[将证书下载到访问设备的客户端](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)。

在这两种情况下，你创建并上传到设备上的证书必须导入到 Windows 客户端上（访问设备）相应的证书存储中。 

- 现在应在客户端系统上的“受信任的根证书颁发机构”中导入作为 DER 导出的根证书。 有关详细步骤，请参阅[将证书导入到受信任的根证书颁发机构存储](#import-certificates-as-der-format)。

- 导出为 `.pfx` 的终结点证书必须导出为包含 `.cer` 扩展名的 DER。 然后，将 `.cer` 导入到系统上的“个人证书存储”中。 有关详细步骤，请参阅[将证书导入到个人证书存储](#import-certificates-as-der-format)。

### <a name="import-certificates-as-der-format"></a>以 DER 格式导入证书 

若要在 Windows 客户端上导入证书，请执行以下步骤：

1. 右键单击文件并选择“安装证书”。 该操作会启动证书导入向导。

    ![导入证书 1](media/azure-stack-edge-gpu-manage-certificates/import-cert-1.png)

2. 对于“存储位置”，选择“本地计算机”，然后选择“下一步”。

    ![导入证书 2](media/azure-stack-edge-gpu-manage-certificates/import-cert-2.png)

3. 选择“将所有证书放入以下存储区”，然后选择“浏览”。 

    - 若要导入到个人存储，请导航到远程主机的“个人存储”，然后选择“下一步”。

        ![导入证书 4](media/azure-stack-edge-gpu-manage-certificates/import-cert-4.png)


    - 若要导入到受信任的存储区，请导航到“受信任的根证书颁发机构”，然后选择“下一步”。

        ![导入证书 3](media/azure-stack-edge-gpu-manage-certificates/import-cert-3.png)

 
4. 选择“完成”。 将显示一条提示已成功导入的消息。


## <a name="view-certificate-expiry"></a>查看证书有效期

如果自带证书，证书将在 1 年或 6 个月后过期。 若要查看证书的到期日期，请转到设备本地 Web UI 的“证书”页。 如果选择特定证书，则可以查看证书的到期日期。


## <a name="next-steps"></a>后续步骤

了解如何[排查证书问题](azure-stack-edge-gpu-certificate-troubleshooting.md)
