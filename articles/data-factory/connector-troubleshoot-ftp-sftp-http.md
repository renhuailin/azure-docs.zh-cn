---
title: 排查 FTP、SFTP 和 HTTP 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 FTP、SFTP 和 HTTP 连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 53c70456f754dd451d3bb3cfa3bed0c6ddac3461
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390382"
---
# <a name="troubleshoot-the-ftp-sftp-and-http-connectors-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 FTP、SFTP 和 HTTP 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供有关排查 Azure 数据工厂和 Azure Synapse 中常见的 FTP、SFTP 和 HTTP 连接器问题的建议。

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>错误代码：FtpFailedToConnectToFtpServer

- **消息**：`Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **原因**：对 FTP 服务器使用的链接服务类型可能不正确，例如使用安全 FTP (SFTP) 链接服务连接到 FTP 服务器。

- **建议**：检查目标服务器的端口。 FTP 使用端口 21。

### <a name="error-code-ftpfailedtoreadftpdata"></a>错误代码：FtpFailedToReadFtpData

- **消息**：`Failed to read data from ftp: The remote server returned an error: 227 Entering Passive Mode (*,*,*,*,*,*).`

- **原因**：对于数据工厂或 Synapse 管道支持的被动模式下的数据传输，未打开端口范围 1024 到 65535。

- **建议**：检查目标服务器的防火墙设置。 打开端口 1024-65535 或在 FTP 服务器中将端口范围指定为 SHIR/Azure IR IP 地址。

## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>错误代码：SftpOperationFail

- **消息**：`Failed to '%operation;'. Check detailed error from SFTP.`

- **原因**：SFTP 操作出现问题。

- **建议**：检查来自 SFTP 的错误详细信息。


### <a name="error-code-sftprenameoperationfail"></a>错误代码：SftpRenameOperationFail

- **消息**：`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **原因**：SFTP 服务器不支持重命名临时文件。

- **建议**：在复制接收器中将“useTempFileRename”设置为 false，以禁止上传到临时文件。


### <a name="error-code-sftpinvalidsftpcredential"></a>错误代码：SftpInvalidSftpCredential

- **消息**：`Invalid SFTP credential provided for '%type;' authentication type.`

- **原因**：私钥内容是从 Azure 密钥保管库或 SDK 提取的，但编码有误。

- **建议**：  

    如果私钥内容来自密钥保管库，则在将原始密钥文件直接上传到 SFTP 链接服务时，该文件有效。

    有关详细信息，请参阅[使用数据工厂或 Synapse 管道从/向 SFTP 服务器复制数据](./connector-sftp.md#use-ssh-public-key-authentication)。 私钥内容是 base64 编码的 SSH 私钥内容。

    请使用 base64 编码对整个原始私钥文件进行编码，并将已编码的字符串存储在密钥保管库中。 如果选择从文件上传，则可在 SFTP 链接服务上使用原始私钥文件。

    下面是可用于生成字符串的一些示例：

    - 使用 C# 代码：

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - 使用 Python 代码：

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - 使用第三方 base64 转换工具。 建议使用[编码为 Base64 格式](https://www.base64encode.org/)工具。

- **原因**：选择的密钥内容格式有误。

- **建议**：  

    当前不支持使用 PKCS#8 格式的 SSH 私钥（开头为“-----BEGIN ENCRYPTED PRIVATE KEY-----”）访问 SFTP 服务器。 

    若要将密钥转换为传统的 SSH 密钥格式（开头为“-----BEGIN RSA PRIVATE KEY-----”），请运行以下命令：

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **原因**：凭据或私钥内容无效。

- **建议**：若要查看密钥文件或密码是否正确，请使用 WinSCP 等工具仔细检查。

### <a name="sftp-copy-activity-failed"></a>SFTP 复制活动失败

- **症状**： 
  * 错误代码：UserErrorInvalidColumnMappingColumnNotFound 
  * 错误消息：`Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **原因**：源不包含名为“AccMngr”的列。

- **解决方法**：若要确定“AccMngr”列是否存在，请映射目标数据集列来仔细检查数据集配置。


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>错误代码：SftpFailedToConnectToSftpServer

- **消息**：`Failed to connect to SFTP server '%server;'.`

- **原因**：如果错误消息包含“套接字读取操作在 30,000 毫秒后超时”这一字符串，则可能是由于对 SFTP 服务器使用了错误的链接服务类型而导致的。 例如，你可能正在使用 FTP 链接服务连接到 SFTP 服务器。

- **建议**：检查目标服务器的端口。 SFTP 默认使用端口 22。

- **原因**：如果错误消息包含“服务器响应不包含 SSH 协议标识”这一字符串，则可能是由于 SFTP 服务器限制了连接而导致的。 创建了多个连接来从 SFTP 服务器并行下载，有时会遇到 SFTP 服务器限制。 通常，不同的服务器在遇到限制时会返回不同的错误。

- **建议**：  

    将 SFTP 数据集的最大并发连接数指定为 1，然后重新运行复制活动。 如果活动成功，则可确定这是限制导致的。

    若要提升低吞吐量，请与 SFTP 管理员联系以增加并发连接计数限制，或者可执行以下操作之一：

    * 如果使用自承载 IR，请将自承载 IR 计算机的 IP 添加到允许列表中。
    * 如果使用的是 Azure IR，请添加 [Azure Integration Runtime IP 地址](./azure-integration-runtime-ip-addresses.md)。 如果不想将一系列 IP 添加到 SFTP 服务器允许列表中，请改用自承载 IR。


### <a name="error-code-sftppermissiondenied"></a>错误代码：SftpPermissionDenied

- **消息**：`Permission denied to access '%path;'`

- **原因**：指定的用户在操作时没有对文件夹或文件的读取或写入权限。

- **建议**：授予用户对 SFTP 服务器上文件夹或文件的读取或写入权限。
 
### <a name="error-code-sftpauthenticationfailure"></a>错误代码：SftpAuthenticationFailure

- **消息**：`Meet authentication failure when connect to Sftp server '%server;' using '%type;' authentication type. Please make sure you are using the correct authentication type and the credential is valid. For more details, see our troubleshooting docs.`

- **原因**：指定的凭据（密码或私钥）无效。

- **建议**：检查凭据。

- **原因**：指定的身份验证类型不被允许或不足以在 SFTP 服务器中完成身份验证。

- **建议**：应用以下选项以使用正确的身份验证类型：
    - 如果服务器需要密码，请使用“基本”。
    - 如果服务器需要私钥，请使用“SSH 公钥身份验证”。
    - 如果服务器需要“密码”和“私钥”，请使用“多重身份验证”。

- **原因**： SFTP 服务器需要“键盘 - 交互式”身份验证，但提供的是“密码”。

- **建议**： 

    “键盘 - 交互式”是一种特殊的身份验证方法，不同于“密码”。 这意味着登录服务器时，必须手动输入密码，并且无法使用之前保存的密码。 但 Azure 数据工厂 (ADF) 是一项计划的数据传输服务，并且它没有可以让你在运行时提供密码的弹出输入框。 <br/> 
    
    作为一个折衷方案，系统提供了一个选项来模拟后台的输入，而不是实际的手动输入，这等效于将“键盘 - 交互式”更改为“密码”。 如果可以接受此安全问题，请按照以下步骤进行启用：<br/> 
    1. 在 ADF 门户中，将鼠标悬停在 SFTP 链接服务上，并通过选择“代码”按钮打开其有效负载。
    1. 在“typeProperties”部分中添加 `"allowKeyboardInteractiveAuth": true`。

## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>错误代码：HttpFileFailedToRead

- **消息**：`Failed to read data from http server. Check the error from http server：%message;`

- **原因**：当数据工厂或 Synapse 管道与 HTTP 服务器通信时，如果 HTTP 请求操作失败，则会发生此错误。

- **建议**：查看错误消息中的 HTTP 状态代码，并修复远程服务器问题。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
