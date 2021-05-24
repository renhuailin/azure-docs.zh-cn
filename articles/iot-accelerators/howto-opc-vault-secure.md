---
title: 使用 OPC Vault 保护 OPC UA 设备的通信 - Azure | Microsoft Docs
description: 如何注册 OPC UA 应用程序，以及如何使用 OPC Vault 为 OPC UA 设备颁发已签名的应用程序证书。
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a1b7564988c8a4d63a37b53d18ed3a7359e65d72
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645505"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>使用 OPC Vault 证书管理服务

> [!IMPORTANT]
> 当我们更新本文时，请参阅 [Azure 工业 IoT](https://azure.github.io/Industrial-IoT/) 来了解最新内容。

本文介绍了如何注册应用程序，以及如何为 OPC UA 设备颁发已签名的应用程序证书。

## <a name="prerequisites"></a>先决条件

### <a name="deploy-the-certificate-management-service"></a>部署证书管理服务

首先，将证书管理服务部署到 Azure 云中。 有关详细信息，请参阅[部署 OPC Vault 证书管理服务](howto-opc-vault-deploy.md)。

### <a name="create-the-issuer-ca-certificate"></a>创建颁发者 CA 证书

如果还没有这样做，请创建颁发者 CA 证书。 有关详细信息，请参阅[创建和管理 OPC Vault 的颁发者证书](howto-opc-vault-manage.md)。

## <a name="secure-opc-ua-applications"></a>保护 OPC UA 应用程序

### <a name="step-1-register-your-opc-ua-application"></a>第 1 步：注册 OPC UA 应用程序 

> [!IMPORTANT]
> 注册应用程序需要“编写者”角色。

1. 在 `https://myResourceGroup-app.azurewebsites.net` 打开证书服务，然后登录。
2. 转到“注册新应用程序”。 对于应用程序注册，用户至少需要分配有“编写者”角色。
2. 输入表单遵循 OPC UA 中的命名约定。 例如，在下面的屏幕截图中，展示了 OPC UA .NET Standard 堆栈中 [OPC UA 引用服务器](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer)示例的设置：

   ![展示了 UA 引用服务器注册的屏幕截图](media/howto-opc-vault-secure/reference-server-registration.png "UA 引用服务器注册")

5. 选择“注册”，以在证书服务应用程序数据库中注册应用程序。 工作流直接引导用户进入下一步，为应用程序请求已签名的证书。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>第 2 步：使用 CA 签名的应用程序证书保护应用程序

通过颁发基于证书签名请求 (CSR) 的已签名的证书，保护 OPC UA 应用程序。 或者，也可以请求新的密钥对，其中包括新的 PFX 或 PEM 格式私钥。 有关应用程序支持哪种方法的信息，请参阅 OPC UA 设备的文档。 通常情况下，建议使用 CSR 方法，因为它不需要通过线路传输私钥。

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>使用新的密钥对请求新证书

1. 转到“应用程序”。
3. 为列出的应用程序选择“新建请求”。

   ![展示了请求新证书的屏幕截图](media/howto-opc-vault-secure/request-new-certificate.png "请求新证书")

3. 选择“请求新的密钥对和证书”，为应用程序请求私钥和带有公钥的已签名的新证书。

   ![展示了生成新的密钥对和证书的屏幕截图](media/howto-opc-vault-secure/generate-new-key-pair.png "生成新密钥对")

4. 在表单中填写使用者和域名。 对于私钥，选择带密码的 PEM 或 PFX。 选择“生成新的密钥对”，以创建证书请求。

   ![展示了“查看证书请求详细信息”屏幕和“生成新的密钥对”按钮的屏幕截图。](media/howto-opc-vault-secure/approve-reject.png "批准证书")

5. 审批需要用户拥有“审批者”角色，并在 Azure Key Vault 中具有签名权限。 在典型的工作流中，“审批者”角色和“请求者”角色应分配给不同的用户。 选择“批准”或“拒绝”，以启动或取消密钥对的实际创建和签名操作。 新的密钥对会进行创建，并安全地存储在 Azure Key Vault 中，直到被证书请求者下载。 生成的带有公钥的证书由 CA 签名。 这些操作可能需要几秒钟才能完成。

   ![“查看证书请求详细信息”（底部有审批消息）的屏幕截图](media/howto-opc-vault-secure/view-key-pair.png "查看密钥对")

7. 生成的私钥（PFX 或 PEM）和证书 (DER) 可以从此处下载，格式选择为二进制文件下载。 也可以使用 base64 编码版本，以（例如）将证书复制并粘贴到命令行或文本条目中。 
8. 在下载并安全存储私钥后，可以选择“删除私钥”。 带有公钥的证书仍然可用，可供将来使用。
9. 由于使用了 CA 签名的证书，因此也应在此处下载 CA 证书和证书吊销列表 (CRL)。

现在取决于 OPC UA 设备如何应用新的密钥对。 通常，CA 证书和 CRL 被复制到 `trusted` 文件夹，而应用程序证书的公钥和私钥则被应用于证书存储中的 `own` 文件夹。 一些设备可能已经支持证书更新的服务器推送。 请参阅 OPC UA 设备的文档。

#### <a name="request-a-new-certificate-with-a-csr"></a>使用 CSR 请求新证书 

1. 转到“应用程序”。
3. 为列出的应用程序选择“新建请求”。

   ![展示了请求新证书的屏幕截图](media/howto-opc-vault-secure/request-new-certificate.png "请求新证书")

3. 选择“使用签名请求来请求新证书”，以为应用程序请求已签名的新证书。

   ![展示了生成新证书的屏幕截图](media/howto-opc-vault-secure/generate-new-certificate.png "生成新证书")

4. 通过选择本地文件或在表单中粘贴 base64 编码的 CSR 来上传 CSR。 选择“生成新证书”。

   ![“查看证书请求详细信息”的屏幕截图](media/howto-opc-vault-secure/approve-reject-csr.png "审批 CSR")

5. 审批需要用户拥有“审批者”角色，并在 Azure Key Vault 中具有签名权限。 选择“批准”或“拒绝”，以启动或取消实际签名操作。 生成的带有公钥的证书由 CA 签名。 此操作可能需要几秒钟才能完成。

   ![展示了“查看证书请求详细信息”且底部有审批消息的屏幕截图。](media/howto-opc-vault-secure/view-cert-csr.png "查看证书")

6. 生成的证书 (DER) 可以作为二进制文件从此处下载。 也可以使用 base64 编码版本，以（例如）将证书复制并粘贴到命令行或文本条目中。 
10. 在下载并安全存储证书后，可以选择“删除证书”。
11. 由于使用了 CA 签名的证书，因此也应在此处下载 CA 证书和 CRL。

现在取决于 OPC UA 设备如何应用新的证书。 通常，CA 证书和 CRL 被复制到 `trusted` 文件夹，而应用程序证书则被应用于证书存储中的 `own` 文件夹。 一些设备可能已经支持证书更新的服务器推送。 请参阅 OPC UA 设备的文档。

### <a name="step-3-device-secured"></a>第 3 步：受保护的设备

现在，OPC UA 设备已就绪，可以与其他由 CA 签名的证书保护的 OPC UA 设备通信，无需进一步配置。

## <a name="next-steps"></a>后续步骤

至此，你已经学习了如何保护 OPC UA 设备，现在可以：

> [!div class="nextstepaction"]
> [运行安全的证书管理服务](howto-opc-vault-secure-ca.md)
