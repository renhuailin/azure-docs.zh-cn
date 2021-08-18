---
title: 导出用于进行客户端身份验证的受信任的客户端 CA 证书链
titleSuffix: Azure Application Gateway
description: 了解如何导出用于在 Azure 应用程序网关上进行客户端身份验证的受信任的客户端 CA 证书链
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 15655ba313a3a3cba23289c6d829523db6735129
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739907"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>导出受信任的客户端 CA 证书链以用于客户端身份验证
为了配置与客户端的相互身份验证或客户端身份验证，应用程序网关要求将受信任的客户端 CA 证书链上传到网关。 如果有多个证书链，则需要单独创建这些链，并在应用程序网关上将它们作为不同文件上传。 本文介绍如何导出可在网关上的客户端身份验证配置中使用的受信任的客户端 CA 证书链。  

## <a name="prerequisites"></a>必备条件

生成受信任的客户端 CA 证书链需要现有的客户端证书。 

## <a name="export-trusted-client-ca-certificate"></a>导出受信任的客户端 CA 证书

需要受信任的客户端 CA 证书才能在应用程序网关上进行客户端身份验证。 在此示例中，我们将使用 TLS/SSL 证书作为客户端证书，导出其公钥，然后从该公钥导出 CA 证书以获取受信任的客户端 CA 证书。 然后，将所有客户端 CA 证书连接到一个受信任的客户端 CA 证书链。 

以下步骤可帮助你导出证书的 .pem 或 .cer 文件：

### <a name="export-public-certificate"></a>导出公用证书 

1. 若要获取证书 .cer 文件，请打开“管理用户证书”。 找到该证书（通常位于“证书 - 当前用户”>“个人”>“证书”中），并单击右键。 单击“所有任务”，并单击“导出”。 此操作将打开“证书导出向导”  。 如果在 Current User\Personal\Certificates 下找不到证书，可能会意外地打开“Certificates - Local Computer”而不是“Certificates - Current User”）。 如果想要使用 PowerShell 在当前用户范围内打开证书管理程序，请在控制台窗口中键入“certmgr”  。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示了证书管理器，其中已选择“证书”并依次选中了上下文菜单的“所有任务”、“导出”。](./media/certificates-for-backend-authentication/export.png)

2. 在向导中，单击“下一步”  。
    > [!div class="mx-imgBorder"]
    > ![导出证书](./media/certificates-for-backend-authentication/exportwizard.png)

3. 选择“否，不导出私钥”  ，并单击“下一步”  。
    > [!div class="mx-imgBorder"]
    > ![不要导出私钥](./media/certificates-for-backend-authentication/notprivatekey.png)

4. 在“导出文件格式”  页上，选择“Base-64 编码的 X.509 (.CER)”  ，并单击“下一步”  。
    > [!div class="mx-imgBorder"]
    > ![Base-64 编码](./media/certificates-for-backend-authentication/base64.png)

5. 对于“要导出的文件”  ，“浏览”  到要将证书导出的目标位置。 在“文件名”  中，为证书文件命名。 然后单击“下一步”。 

    > [!div class="mx-imgBorder"]
   > ![屏幕截图显示了“证书导出向导”，在其中指定要导出的文件。](./media/certificates-for-backend-authentication/browse.png)

6. 单击“完成”  导出证书。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示了完成文件导出后的证书导出向导。](./media/certificates-for-backend-authentication/finish-screen.png)

7. 证书已成功导出。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示了包含一条成功消息的证书导出向导。](./media/certificates-for-backend-authentication/success.png)

   导出的证书类似于以下内容：

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示了证书符号。](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>从公用证书导出 CA 证书

现在你已导出公用证书，接下来将从公用证书导出 CA 证书。 如果只有根 CA，则只需导出该证书即可。 但如果有多个中间 CA，则还需要导出每个 CA。 

1. 导出公钥后，打开该文件。

    > [!div class="mx-imgBorder"]
    > ![开放授权证书](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![关于证书](./media/mutual-authentication-certificate-management/general.png)

1. 选择“证书路径”选项卡以查看证书颁发机构。

    > [!div class="mx-imgBorder"]
    > ![证书详细信息](./media/mutual-authentication-certificate-management/cert-details.png) 

1. 选择根证书，然后单击“查看证书”。

    > [!div class="mx-imgBorder"]
    > ![证书路径](./media/mutual-authentication-certificate-management/root-cert.png) 

   应该会看到根证书详细信息。

    > [!div class="mx-imgBorder"]
    > ![证书信息](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. 选择“详细信息”选项卡，然后单击“复制到文件...” 

    > [!div class="mx-imgBorder"]
    > ![复制根证书](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. 此时，你已从公用证书中提取了根 CA 证书的详细信息。 此时会看到“证书导出向导”。 按照上一部分（[导出公用证书](./mutual-authentication-certificate-management.md#export-public-certificate)）中的步骤 2-7 完成“证书导出向导”。 

1. 现在，对所有中间 CA 重复当前部分（[从公用证书导出 CA 证书](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)）的步骤 2-6，以导出采用 Base-64 编码的 X.509(.CER) 格式的所有中间 CA 证书。

    > [!div class="mx-imgBorder"]
    > ![中间证书](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    例如，你将对 MSIT CAZ2 中间 CA 重复本部分的步骤 2-6，以将其提取为自己的证书。 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>将所有 CA 证书连接到一个文件

1. 对之前提取的所有 CA 证书运行以下命令。 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux：
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    生成的合并证书应如下所示：
    
    > [!div class="mx-imgBorder"]
    > ![合并证书](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>后续步骤

现在，你已获得受信任的客户端 CA 证书链。 可将此链添加到应用程序网关上的客户端身份验证配置，以允许与网关进行相互身份验证。 请参阅[使用应用程序网关通过门户配置相互身份验证](./mutual-authentication-portal.md)或[使用应用程序网关通过 PowerShell 配置相互身份验证](./mutual-authentication-powershell.md)。

